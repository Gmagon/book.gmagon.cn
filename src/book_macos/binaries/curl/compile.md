---
index: true
type: api
---

## 安装curl

```

$ export CFLAGS=" -mmacosx-version-min=10.6"
$ export CXXFLAGS=" -mmacosx-version-min=10.6"
$ export CPPFLAGS=" -mmacosx-version-min=10.6"
$ brew install curl

```

## 查看curl的版本信息

> 

//1. /usr/local/Cellar/curl/7.54.1/bin/curl
//2. ~/common_plugins/exec_plugins/curl/7.54.1/bin

```
$ otool -L /usr/local/Cellar/curl/7.54.1/bin/curl
```

> curl:
	/usr/local/Cellar/curl/7.54.1/lib/libcurl.4.dylib (compatibility version 9.0.0, current version 9.0.0)
	/System/Library/Frameworks/LDAP.framework/Versions/A/LDAP (compatibility version 1.0.0, current version 2.4.0)
	/usr/lib/libz.1.dylib (compatibility version 1.0.0, current version 1.2.8)
	/System/Library/Frameworks/Security.framework/Versions/A/Security (compatibility version 1.0.0, current version 57740.51.2)
	/System/Library/Frameworks/CoreFoundation.framework/Versions/A/CoreFoundation (compatibility version 150.0.0, current version 1349.64.0)
	/usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1238.50.2)


```
$ sudo install_name_tool -change /usr/local/Cellar/curl/7.54.1/lib/libcurl.4.dylib @loader_path/../lib/libcurl.4.dylib ./curl

```
