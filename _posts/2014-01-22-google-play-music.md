---
layout: post
title: Google Play Music на Raspberry Pi
excerpt: Обзор легковесных способов слушать музыку
---

Не так давно сервис Google Play Music [стал доступен][1]{:target="_blank"} в России. За 169 рублей в месяц можно получить полный доступ. Проблема в том, что Google предоставляет возможность слушать музыку только либо с мобильных устройств, либо через браузер.

Однако многим это не подходит, к примеру мне. Поэтому в данной статье рассмотрим возможные «неофициальные» способы прослушивания музыки из Google Play Music.

Итак, в наличии имеем Raspberry Pi с установленным сервером MPD. Хотим получить возможность проигрывать удалённо любую музыку из своей фонотеки в Google Play Music на этом Raspberry Pi.

Вариант с запуском vnc-сервера с запущенным браузером не рассматриваем. Пробовал запускать chromium и midori без X-сервера и оконного менеджера, всё равно это дело очень сильно тормозит. Веб-приложение music.google.com всё таки достаточно «тяжёлое».

Официальный API для Google Play Music отсутствует, но на github'е имеется [Unofficial-Google-Music-API][2]{:target="_blank"}, написанный на Python. Автор этого API Simon Weber советует несколько проектов, использующих его API. Рассмотрим заинтересовавшие меня:

* [thunner][3]
* [GMusicFS][4]
* [GMusicProxy][5]

Помимо этих проектов можно также найти:

* [play-pi][6]
* [Mopidy][7]

Стоит отметить, что все проекты написаны на Python. Ниже опишу свой опыт работы с каждым из них.

## [thunner](https://github.com/mstill/thunner){:target="_blank"}
Curses-клиент. Запускает музыку через mplayer. Звучит здорово, однако не работает. Для начала пришлось переключиться на ветку, в которую внесены изменения для работы с последней версией API, версия из master неработоспособна. Но музыка всё равно играет лишь секунду, затем переключается на следующую песню. Собственно можем наблюдать следующую нерешённую проблему [All-Access Songs Not Playing][8]{:target="_blank"}, значит это не только у меня.

![image][9]{:target="_blank"}

## [GMusicFS](https://github.com/EnigmaCurry/GMusicFS){:target="_blank"}
FUSE файловая система. При монтировании получаем иерархию директорий своей фонотеки из Google Play Music в формате

```
artists/<name of artist>/<albums>/<tracks>
```

плюс загружается cover.jpg. Что же, возможность иметь всю фонотеку в формате mp3 в директории -- это просто отлично, но есть ряд печальных ограничений.

Как пишет автор, данное решение подходит только для копирования файлов себе на компьютер либо для воспроизведения простым проигрывателем, таким как mplayer. При попытке воспроизведения в более продвинутых проигрывателях могут возникать проблемы. И действительно, при попытке открыть коллекцию даже в простом mocp он начинает сильно подвисать, музыку слушать невозможно. При попытке указать директорию в качестве библиотеки для MPD получаем возможность слушать музыку, однако тэги у меня не загружались, а слушать песни с одинаковым названием "Unknown" конечно не вариант. Ещё автор отмечает отсутствие возможности воспроизвести песню с определённой позиции.

Установить GMusicFS можно при помощи pip, предварительно установив зависимости:

```
$ pip install https://github.com/terencehonles/fusepy/tarball/master
$ pip install https://github.com/simon-weber/Unofficial-Google-Music-API/tarball/develop
$ pip install https://github.com/EnigmaCurry/GMusicFS/tarball/master
```

Затем необходимо создать конфигурационный файл ~/.gmusicfs со следующим содержимым:

```
[credentials]
username = your_username@gmail.com
password = your_password
```

И можно монтировать:

```
$ mkdir -p $HOME/google_music
$ gmusicfs $HOME/google_music
```

Для размонтирования:

```
$ fusermount -u $HOME/google_music
```

При копировании музыки себе на компьютер все тэги корректно загружаются. Воспроизведение с помощью mplayer также работает отлично. После обновления фонотеки необходимо перемонтировать файловую систему.

## [GMusicProxy](https://github.com/diraimondo/gmusicproxy){:target="_blank"}
В описании проекта говорится следующее: «Let's stream Google Play Music using any media-player». Этот скрипт позволяет получать m3u-плейлисты либо mp3-файлы путём отправки специально сформированных GET-запросов.

Установить можно следующей командой:

```
$ pip install https://github.com/diraimondo/gmusicproxy/tarball/master
```

Для работы необходим device-id одного из зарегистрированных устройств. Получить список этих устройств можно следующим образом:

```
$ GMusicProxy --email <адрес> --password <пароль> --list-devices
```

Создаём конфиг ~/.config/gmusicproxy.cfg:

```
email = my.email@gmail.com
password = my-secret-password
device-id = your_device_id
```

Запускаем:

```
$ GMusicProxy
```

<details>
    <summary>Примеры использования с помощью консольного клиента mpc</summary>
    curl -s 'http://localhost:9999/get_by_search?type=album&artist=Queen&title=Greatest%20Hits' > /var/lib/mpd/playlists/queen.m3u
    mpc load queen
    mpc play

    mpc clear
    curl -s 'http://localhost:9999/get_new_station_by_search?type=artist&artist=Queen&num_tracks=100' | grep -v ^# | while read url; do mpc add "$url"; done
    mpc play
</details>

<details>
    <summary>Примеры использования с помощью VLC</summary>
    vlc 'http://localhost:9999/get_by_search?type=album&artist=Rolling%20Stones&title=tattoo&exact=no'
    curl -s 'http://localhost:9999/get_all_stations?format=text&only_url=yes' | sort -R | head -n1 | vlc -
</details>

Поддерживаются самые различные запросы: получение песен, радиостанций, плейлистов, ...

Из минусов стоит отметить, что все эти запросы необходимо составлять самому, а также, что тэги загружаются только при воспроизведении конкретной песни.
## [play-pi](https://github.com/fredley/play-pi){:target="_blank"}
Web-фронтенд на Django для доступа к фонотеке с интеграцией в MPD. Скажу лишь, что у меня возникла такая же проблема, как и с thunner'ом -- воспроизводится лишь секунда.

## [Mopidy](http://www.mopidy.com/){:target="_blank"}
Mopidy представляет из себя музыкальный сервер, который умеет «притворяться» MPD. Но самое интересное -- для него есть расширение [Mopidy-GMusic][10]{:target="_blank"}.

Установить Mopidy можно с помощью пакетного менеджера, а расширение следующей командой:

```
$ pip install mopidy-gmusic
```

Для работы понадобится опять же device-id, который можно получить либо набрав \*#\*#8255#\*#\*, либо установив [приложение][11]{:target="_blank"}.

В конфиг Mopidy необходимо дописать:

```
[gmusic]
username = alice
password = secret
deviceid = your_device_id
```

После дальнейшего конфигурирования (документация [здесь][12]{:target="_blank"}) получим MPD-совместимый сервер с музыкой из Google Play Music, а также с локальной коллекцией. Из MPD-клиентов без проблем заработали [GMPC][13]{:target="_blank"}, [Ario][14]{:target="_blank"} и [pympd][15]{:target="_blank"}. Мой любимый [Cantata][16]{:target="_blank"} к сожалению не подключается.

* * *

## Выводы
Для копирования музыки из Google Play Music в формате mp3 отлично подойдёт [GMusicFS][4]. Для воспроизведения можно использовать [Mopidy][7] с плагином.

Я остановился на том, что запускаю одновременно MPD и Mopidy на разных портах и подключаюсь разными клиентами (Cantata и GMPC). MPD для локальной коллекции, Mopidy для Google Play Music.

Не стоит забывать, что при использовании двухфакторной аутентификации необходимо создавать пароли приложений в настройках аккаунта Google.

[1]: http://habrahabr.ru/post/195872/
[2]: https://github.com/simon-weber/Unofficial-Google-Music-API
[3]: #thunner
[4]: #gmusicfs
[5]: #gmusicproxy
[6]: #play-pi
[7]: #mopidy
[8]: https://github.com/mstill/thunner/issues/14
[9]: /images/google-play-music/thunner.png
[10]: https://github.com/hechtus/mopidy-gmusic
[11]: https://play.google.com/store/apps/details?id=com.evozi.deviceid
[12]: http://docs.mopidy.com/en/latest/
[13]: http://gmpclient.org/
[14]: http://ario-player.sourceforge.net/
[15]: http://pympd.sourceforge.net/
[16]: https://code.google.com/p/cantata/
