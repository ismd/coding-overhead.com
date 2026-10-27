---
title: "My CTF Workflow with BlackArch in Docker"
summary: "Running HTB, THM, and CTF tools in a container, keeping task files on the host, and dealing with file ownership"
date: 2026-10-25T11:51:33+02:00
draft: false
tags:
- linux
- security
- docker
- blackarch
- ctf
categories:
- Security
---

Solving CTF challenges and machines on Hack The Box and TryHackMe is one of my hobbies. A typical session involves connecting to the platform's VPN and reaching for whatever tools the task requires. The annoying part was having to stop and install another utility on my laptop before I could continue.

I wanted the tools ready without turning my everyday system into a pentesting distribution. My solution was to put the utilities I use into a BlackArch Docker image and work from a shell inside the container. The image grew out of that workflow: a collection of tools and settings I wanted available when sitting down to solve a box.

## Why I ended up using a container

Installing everything directly on my laptop was the obvious starting point. But different challenges need different tools, and I did not want to keep adding packages and their dependencies to my main system.

There is also the code that comes with a challenge: custom scripts, proof-of-concept exploits, and other snippets downloaded from the internet. Running unfamiliar code directly on the laptop I use every day is another risk I wanted to reduce. Keeping it in a container gives it less direct access to my everyday environment.

That isolation has limits. Containers share the host's kernel, and [Docker's security documentation](https://docs.docker.com/engine/security/) describes the risks of incomplete isolation. Code inside the container can still access the directories I mount into it, including modifying files in writable mounts. A container is an extra boundary, not a guarantee that an unknown script is safe.

A separate installation of Kali Linux or Parrot OS would mean leaving my usual environment and rebooting. A virtual machine avoids that, but it is still another full system to update and configure. For these sessions, I wanted less setup and no guest OS to boot before opening a terminal.

A container fits the way I work. Once the image is available locally, I can start a shell with the tools already installed and keep using my laptop around it. This does not eliminate maintenance: packages still need updates, and the image needs rebuilding. It moves that setup into a Dockerfile I can reuse.

BlackArch is not the only way to do this. [Kali also provides official Docker images](https://www.kali.org/docs/containers/official-kalilinux-docker-images/), so it does not require a desktop or a VM. Starting with one of those and adding a personal selection of tools is a valid alternative. I used the [official BlackArch image](https://github.com/BlackArch/blackarch-docker) as my base.

## What I keep in the image

The [Dockerfile](https://github.com/ismd/blackarch-docker) installs the utilities I use ahead of time. It is a personal selection, rather than an attempt to install the whole BlackArch repository. Some examples are:

- Nmap, ffuf, Gobuster, and sqlmap for network and web tasks.
- John, Hashcat, and Hydra for password-related challenges.
- ExifTool, Binwalk, and Stegseek for inspecting files and steganography tasks.
- GDB, pwndbg, and strace for working with binaries.
- Python with pwntools, Requests, and PyCryptodome for scripts.

The everyday tools matter too. The image includes Zsh with Starship, fzf, ripgrep, jq, and Neovim with LazyVim. Having the security tools installed would only solve part of the problem if I then had to set up the shell and editor every time.

The base is `blackarchlinux/blackarch:novnc`, which also makes a browser-accessible desktop available. My normal way of using it is still the terminal: the default command starts Zsh in `/app`.

## Starting a task

I keep a named container that I can return to between sessions. For example, to start working on a box:

```bash
mkdir -p ~/ctf/example-box
cd ~/ctf/example-box

docker run -it \
  --name blackarch-ctf \
  -v "$PWD":/app \
  -v "$HOME/wordlists:/mnt/wordlists" \
  -v /etc/hosts:/etc/hosts:ro \
  ismd/blackarch
```

Files saved under `/app` remain in `~/ctf/example-box` on the host. That includes downloaded challenge files, scripts, and command output. They are available from either side of the mount, without copying them out of the container after the session.

I also mount my wordlists at `/mnt/wordlists`, so I can use the same collection across tasks without copying it into each container. The example uses `~/wordlists`; replace it with an existing directory containing your wordlists. If the tools only need to read the lists, adding `:ro` to that mount prevents them from changing the originals. As written, both the task directory and wordlists are writable from inside the container.

Mounting `/etc/hosts` gives the container the same custom hostname entries I use on the host, which is useful for lab machines with specific domain names. The `:ro` makes this mount read-only. Docker normally manages a separate hosts file for each container; this mount replaces it with mine. If an editor replaces the host file when saving, the running container may still see the old file until it is restarted.

This is a persistent working environment: exiting the main shell stops the container but keeps its filesystem, including tools installed during the session. The name makes it easy to find again. To [resume the stopped container](https://docs.docker.com/reference/cli/docker/container/start/):

```bash
docker start -ai blackarch-ctf
```

`docker run` creates a new container, so I only use it for the initial setup. Subsequent sessions use `docker start`. Pulling a newer image does not update this existing container; using the new image means creating another container. Keeping task files in the host directory makes that transition easier.

For another terminal in the same running container:

```bash
docker exec -it blackarch-ctf /bin/zsh
```

That makes it possible to leave a command running in one terminal and investigate its output in another, using the same environment and files.

## Reaching the lab through the VPN

My usual setup is simple: I connect to the HTB or THM VPN on the host, then start the container. I leave Docker's network settings at their defaults. On my Linux setup, the container uses the [default bridge network](https://docs.docker.com/engine/network/drivers/bridge/), and its outbound connections reach the lab through the host's VPN connection. This relies on the host's routing and firewall allowing that traffic.

When I need a reverse-shell listener, I usually run it directly on the host:

```bash
# On the host, outside the container
nc -lvnp 4444
```

The target connects to my host's VPN IP on port `4444`. Docker is not involved in that incoming connection, so there is no container port to publish.

If I want the listener inside the container instead, there are two alternatives to the default launch command:

- Add `-p <host-vpn-ip>:4444:4444` when creating the container, replacing `<host-vpn-ip>` with the actual address. Docker then forwards that host port to port `4444` in the container, where the listener must bind to `0.0.0.0:4444`.
- Add [`--network host`](https://docs.docker.com/engine/network/drivers/host/) instead. With a regular rootful Docker Engine on Linux, the container shares the host's network, including its VPN interface. A listener uses a host port directly, without `-p`, but the container no longer has a separate network namespace.

Both are options for a newly created container, not flags to add to `docker start`. For my usual workflow, a listener on the host is enough. Whichever option is used, the host firewall must allow the incoming connection, and two listeners cannot use the same host address and port at once.

## The file ownership problem I ran into

While solving an HTB machine, I hit a less obvious consequence of sharing a directory with the container: a file I created there belonged to root on the host.

The image starts as root. With a regular rootful Docker setup and no user namespace remapping, container UID `0` is also UID `0` on the host. Running the `docker` command from my normal user account does not change that. The process writing the file is still root.

For ordinary file processing, one option is to add `--user "$(id -u):$(id -g)"` when creating the container. On that rootful setup, files written by the process belong to my host user. But this is not a complete replacement for the usual interactive session: my shell and editor configuration live under `/root`, and changing the UID does not create a configured home directory for another user. Package installation and tools that require additional privileges also need separate handling.

This experience made me look into two other options:

- **Rootless Docker** runs the daemon and containers without host root privileges. Its [UID/GID mapping](https://docs.docker.com/engine/security/rootless/uid-gid-mapping/) maps root inside the container to the user running rootless Docker on the host. That directly addresses this particular ownership problem when the container writes files as root.
- **Rootless Podman** also maps container root to the invoking host user by default. Its [`--userns=keep-id` option](https://docs.podman.io/en/latest/markdown/podman-run.1.html#userns-mode) is a different arrangement: it preserves the user's numeric UID inside the container, which brings back the need for a suitable home directory and configuration.

These are options I want to explore further, rather than a migration I have already completed. File ownership is one part of the decision; the VPN and the tools I use need to work with the chosen runtime too.

## When a graphical application is needed

The image also supports a desktop through [noVNC](https://github.com/novnc/novnc). I mainly use CLI tools, but this mode provides a way to open graphical applications such as Burp Suite in a browser:

```bash
docker run -d \
  --name blackarch-gui \
  --security-opt seccomp=unconfined \
  -p 127.0.0.1:8080:8080 \
  -v "$PWD":/app \
  -v "$HOME/wordlists:/mnt/wordlists" \
  -v /etc/hosts:/etc/hosts:ro \
  ismd/blackarch \
  /usr/bin/supervisord -c /etc/supervisor/conf.d/supervisord.conf
```

The desktop is then available at `http://localhost:8080`. This command binds the browser endpoint to localhost and uses the seccomp setting required by the upstream noVNC image, which disables that layer of syscall filtering. The VPN setup is the same as for the CLI container.

To stop the desktop session:

```bash
docker stop blackarch-gui
```

To start it again later, use `docker start blackarch-gui` and reopen the browser page.

## The cost of having everything ready

The image is large: approximately **15.6 GB locally** and **5.6 GB (5.2 GiB) to download** from [Docker Hub](https://hub.docker.com/r/ismd/blackarch/tags) for `linux/amd64`, as checked in October 2026. Sizes vary between builds, and cached layers reduce the download.

The desktop base, development tools, and security utilities all contribute to that. I accept the disk usage in exchange for having my environment ready, but a smaller selection built directly on a base image can make more sense for someone else.

The same applies to the package list. If a tool is missing for one task, it can be installed in the running container. If it becomes part of the regular workflow, it belongs in the Dockerfile. Another option is to extend this image with a small Dockerfile of your own, or use mine as a reference and start from the official BlackArch base.

For my sessions, the useful change has been spending less time preparing the environment before working on a challenge. I open a terminal, have my tools available, and keep the task files in a directory on my laptop. The ownership issue is a reminder that there are still details to improve as I use it.

The [source is on GitHub](https://github.com/ismd/blackarch-docker), and the image is published as [`ismd/blackarch` on Docker Hub](https://hub.docker.com/r/ismd/blackarch). If you use a similar setup, I would be interested to hear how you handle the rough edges; feedback, issues, and pull requests are welcome.
