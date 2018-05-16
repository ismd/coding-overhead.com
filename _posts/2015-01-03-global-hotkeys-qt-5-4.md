---
layout: post
title: Global hotkeys в Qt 5.4
excerpt: Реализуем отсутствующие в Qt глобальные сочетания клавиш
---

Понадобилось мне реализовать глобальные хоткеи в приложении на Qt. Начал гуглить, и оказалось, что Qt не поддерживает их. Можно ловить события, предназначенные только для текущего приложения, но мне надо было ловить их, когда приложение свёрнуто в трей. Удалось найти библиотеку [libqxt](http://dev.libqxt.org/libqxt/wiki/Home){:target="_blank"}, которая реализует множество фич, которых нет в оригинальном Qt, в том числе и глобальные хоткеи. Вроде бы всё здорово, однако оказалось, что нет.

libqxt на текущий момент не поддерживается и не развивается. Как следствие, очень плохо дружит с Qt5. Чтобы его собрать пришлось ~~много гуглить~~ воспользоваться Arch'евским AUR'ом, в котором оказался пакет [libqxt-qt5-git](https://aur.archlinux.org/packages/libqxt-qt5-git/){:target="_blank"} с нужными патчами. Глядя на название, очевидно, что это сборка для Qt5.

Собственно, чтобы собрать libqxt с Qt5 необходимо применить патч, приложенный к [пакету](https://aur.archlinux.org/packages/libqxt-qt5-git/){:target="_blank"}. Но толку от этого на самом деле мало.

Сборка проходит удачно, но после неё по какой-то причине ни в одном из .so-файлов нет нужного мне класса <a href="http://libqxt.bitbucket.org/doc/tip/qxtglobalshortcut.html" target="_blank">QxtGlobalShortcut</a>. Разбираться дальше я не стал, решив найти более удачное решение для реализации глобальных хоткеев. Это было ошибкой :-)

Убив день на изучение [XCB](http://ru.wikipedia.org/wiki/XCB){:target="_blank"} (замена [Xlib](https://ru.wikipedia.org/wiki/Xlib){:target="_blank"}) и попытки подружить его с Qt, решил, что всё же лучше использовать уже написанный код для решения своей проблемы. Собственно всё же завести libqxt.

Решив, что весь libqxt мне в общем-то ни к чему, скопировал к себе в проект следующие исходники:
* qxtglobalshortcut.cpp
* qxtglobalshortcut.h
* qxtglobalshortcut_p.h
* qxtglobalshortcut_x11.cpp
* qxtglobal.h (/usr/local/Qxt/include/QxtCore/qxtglobal.h)

В .pro-файл дописал:

```
unix:!macx {
    INCLUDEPATH += /usr/include/qt/QtGui/$${QT_VERSION}/QtGui
    LIBS += -lX11
    SOURCES += src/qxtglobalshortcut_x11.cpp
}
```

Также в файле `qxtglobalshortcut_x11.cpp` необходимо поставить `#include <X11/Xlib.h>` последним. После этого можно использовать в своём коде пример из документации:

```
QxtGlobalShortcut *shortcut = new QxtGlobalShortcut(this);
shortcut-&gt;setShortcut(QKeySequence("Ctrl+Shift+F12"));
connect(shortcut, SIGNAL(activated()),
        this, SLOT(run()));
```

Под линуксом заработало. Очевидно, что теперь необходимо перенести к себе в проект `qxtwindowsystem_win.cpp` и `qxtwindowsystem_mac.cpp` и указать их в .pro-файле.

p.s. На самом деле простым копированием файлов `qxtwindowsystem_win.cpp` и `qxtwindowsystem_mac.cpp` проблема не решилась. Посмотреть как я сделал можно в моём [проекте](https://github.com/ismd/screenshotgun){:target="_blank"}.
