---
title: MacX下 dx 报错
date: 2016-06-29 14:48:46
tags: google
---
- Mac操作系统下dx报错

<!-- more -->

```
$:/dx --dex --output=Hello.dex Hello.class
UNEXPECTED TOP-LEVEL EXCEPTION:
java.lang.RuntimeException: Exception parsing classes
	at com.android.dx.command.dexer.Main.processClass(Main.java:752)
	at com.android.dx.command.dexer.Main.processFileBytes(Main.java:718)
	at com.android.dx.command.dexer.Main.access$1200(Main.java:85)
	at com.android.dx.command.dexer.Main$FileBytesConsumer.processFileBytes(Main.java:1645)
	at com.android.dx.cf.direct.ClassPathOpener.processOne(ClassPathOpener.java:170)
	at com.android.dx.cf.direct.ClassPathOpener.process(ClassPathOpener.java:144)
	at com.android.dx.command.dexer.Main.processOne(Main.java:672)
	at com.android.dx.command.dexer.Main.processAllFiles(Main.java:574)
	at com.android.dx.command.dexer.Main.runMonoDex(Main.java:311)
	at com.android.dx.command.dexer.Main.run(Main.java:277)
	at com.android.dx.command.dexer.Main.main(Main.java:245)
	at com.android.dx.command.Main.main(Main.java:106)
Caused by: com.android.dx.cf.iface.ParseException: bad class file magic (cafebabe) or version (0034.0000)
	at com.android.dx.cf.direct.DirectClassFile.parse0(DirectClassFile.java:472)
	at com.android.dx.cf.direct.DirectClassFile.parse(DirectClassFile.java:406)
	at com.android.dx.cf.direct.DirectClassFile.parseToInterfacesIfNecessary(DirectClassFile.java:388)
	at com.android.dx.cf.direct.DirectClassFile.getMagic(DirectClassFile.java:251)
	at com.android.dx.command.dexer.Main.parseClass(Main.java:764)
	at com.android.dx.command.dexer.Main.access$1500(Main.java:85)
	at com.android.dx.command.dexer.Main$ClassParserTask.call(Main.java:1684)
	at com.android.dx.command.dexer.Main.processClass(Main.java:749)
```
	
修复方案：采用1.7版本jdk