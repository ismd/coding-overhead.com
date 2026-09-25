---
title: "[RU] Тема оформления Hyprland"
summary: Первая статья из серии статей про конфигурацию Hyprland. В ней я расскажу как настроил тему оформления, а именно внешний вид окон и виджетов, курсор и иконки
categories:
  - Настройка окружения Hyprland от начала до конца
tags:
  - linux
  - hyprland
date: 2024-01-14T21:18:00
draft: false
---

Это первая статья из&nbsp;серии статей про конфигурацию Hyprland. В&nbsp;ней я&nbsp;расскажу, как настроил тему оформления, а&nbsp;именно внешний вид окон, виджетов, а&nbsp;также курсор и&nbsp;иконки.

Я&nbsp;предпочитаю светлые темы, поэтому выбирал только среди таких. Курсор выбирал, чтобы контрастировал с&nbsp;остальным интерфейсом, соответственно был тёмным. Иконки&nbsp;же выбирал на&nbsp;основе популярности и&nbsp;просто чтобы понравилось.

Далее распишу подробнее про каждый элемент.

# Внешний вид окон и&nbsp;виджетов

Как следует из&nbsp;официальной вики, понадобилось отдельно настроить темы для GTK и&nbsp;Qt:

> Since this is&nbsp;not a&nbsp;fully-fledged Desktop Environment, you will need to&nbsp;use tools such as&nbsp;lxappearance and nwg-look (recommended) for GTK, and qt5ct&nbsp;/ qt6ct for their respective&nbsp;Qt versions. Some older applications may also require qt4ct.

## GTK

Темы для GTK можно найти&nbsp;на [gnome-look.org](https://www.gnome-look.org/) в&nbsp;разделе &laquo;GTK3/4&nbsp;Themes&raquo;. Я&nbsp;остановился на&nbsp;теме [Fluent](https://github.com/vinceliuice/Fluent-gtk-theme). У&nbsp;неё есть большое количество стилей, включая светлый. Установить эту тему из&nbsp;AUR можно командой `yay -S fluent-gtk-theme`.

Для того, чтобы указать в&nbsp;Hyprland тему GTK, я&nbsp;использовал переменную окружения `GTK_THEME` со&nbsp;значением `Fluent-Light`. Для этого необходимо добавить в&nbsp;файл `~/.config/hypr/hyprland.conf` следующую строку:

```
env = GTK_THEME,Fluent-Light
```

В&nbsp;целом на&nbsp;этом настройка GTK закончена. Для применения изменений нужно перезапустить окружение.

## Qt

Внешний вид Qt-приложений можно настроить через переменную окружения `QT_QPA_PLATFORMTHEME`. В&nbsp;качестве значения можно передать `gtk3`, чтобы использовать тему GTK. Я&nbsp;решил так и&nbsp;поступить и&nbsp;добавил в&nbsp;файл `~/.config/hypr/hyprland.conf`:

```
env = QT_QPA_PLATFORMTHEME,gtk3
```

### Дополнительно

Следуя рекомендациям&nbsp;из [официальной документации Hyprland](https://wiki.hyprland.org/Configuring/Environment-variables/) также добавил переменные окружения для Qt:

```
env = QT_AUTO_SCREEN_SCALE_FACTOR,1
env = QT_QPA_PLATFORM,wayland;xcb
env = QT_WAYLAND_DISABLE_WINDOWDECORATION,1
```

# Курсор

Hyprland поддерживает свой формат курсоров, который называется [hyprcursor](https://wiki.hyprland.org/Hypr-Ecosystem/hyprcursor/). Этот формат был создан вместо XCursor, чтобы решить недостатки последнего. Однако стоит учесть, что в&nbsp;Xwayland курсоры будут отображаться в&nbsp;формате XCursor. Поэтому нужно выбрать такую тему, для которой есть и&nbsp;XCursor-версия, и&nbsp;hyprcursor-версия.

Курсоры в&nbsp;XCursor-формате можно найти без проблем, так как это формат по&nbsp;умолчанию. Например все темы&nbsp;на [gnome-look.org](https://www.gnome-look.org/) скачиваются именно в&nbsp;этом формате. Для hyprcursor&nbsp;же искал тему в&nbsp;официальном Discord-канале `#hyprcursor-themes`. Изучив канал, остановился&nbsp;на [BreezeX_Cursor](https://github.com/ful1e5/BreezeX_Cursor).

Установить эту тему в&nbsp;XCursor-формате можно следующей командой `yay -S breezex-cursor-theme`. Для hyprcursor скачать архив можно либо&nbsp;из [Discord-канала](https://discord.com/channels/961691461554950145/1216066899729977435/1255300607523422249), либо [с&nbsp;моего сайта](BreezeX-Dark-hyprcursor.zip). Распаковав архив в&nbsp;директорию `~/.local/share/icons`, получил установленную тему курсоров.

В&nbsp;файле `~/.config/hypr/hyprland.conf` добавил следующие переменные окружения:

```
env = HYPRCURSOR_SIZE,28
env = HYPRCURSOR_THEME,BreezeX-Dark-hyprcursor
env = XCURSOR_SIZE,28
env = XCURSOR_THEME,BreezeX-Dark
```

А&nbsp;также выполнил команду из&nbsp;документации:

```console
$ gsettings set org.gnome.desktop.interface cursor-theme "BreezeX-Dark"
```

# Иконки

Наборы иконок так&nbsp;же, как и&nbsp;остальное, искал&nbsp;на [gnome-look.org](https://www.gnome-look.org/). Выбирал между, пожалуй, самой популярной темой [Papirus](https://github.com/PapirusDevelopmentTeam/papirus-icon-theme) и&nbsp;[Qogir](https://github.com/vinceliuice/Qogir-icon-theme). Остановился на&nbsp;второй. На&nbsp;мой взгляд, она больше подходит под выбранное оформление окон и&nbsp;виджетов.

Я&nbsp;использую EndeavourOS, поэтому иконки Qogir уже были установлены вместе с&nbsp;пакетом `endeavouros/eos-qogir-icons`. В&nbsp;Arch Linux&nbsp;же пакет называется `qogir-icon-theme`.

Указал иконки через `gsettings`:

```console
$ gsettings set org.gnome.desktop.interface icon-theme "Qogir"
```

Благодаря `QT_QPA_PLATFORMTHEME=gtk3`, которую я&nbsp;добавил в&nbsp;разделе&nbsp;Qt, для Qt-приложений также используется тема иконок Qogir.

# Шрифты

В&nbsp;качестве шрифта выбрал Noto Sans Regular. Указать можно через `gsettings`:

```console
$ gsettings set org.gnome.desktop.interface font-name "NotoSans Nerd Font 12"
```

# nwg-look

[nwg-look](https://github.com/nwg-piotr/nwg-look)&nbsp;&mdash; это утилита для настройки GTK. С&nbsp;помощью неё можно задать внешний вид, цвета, курсор, шрифты, а&nbsp;также темы иконок. Напомню, что&nbsp;Qt настроил так, чтобы использовалась тема GTK.

`nwg-look` при открытии подставляет свои сохранённые значения, а&nbsp;не&nbsp;берёт их&nbsp;из&nbsp;конфига GTK. Это означает, что при сохранении настроек в `nwg-look` они перетрут всё, что мы&nbsp;настраивали до&nbsp;этого вручную. По&nbsp;этой причине не&nbsp;рекомендую использовать эту утилиту, достаточно только конфигов и `gsettings`.

# Заключение

Следуя шагам из&nbsp;статьи можно получить красивое окружение в&nbsp;Hyprland. Конечно, не&nbsp;обязательно использовать те&nbsp;же темы, но&nbsp;общий подход остаётся таким&nbsp;же. Возможно в&nbsp;будущем я&nbsp;что-то поменяю, поэтому даю ссылку на&nbsp;свои конфиги&nbsp;в [репозитории](https://github.com/ismd/dotfiles). В&nbsp;следующих статьях расскажу как настроил остальные элементы окружения.
