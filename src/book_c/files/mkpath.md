
---
index: true
type: api
---

[
http://www.niallohiggins.com/2009/01/08/mkpath-mkdir-p-alike-in-c-for-unix/](http://www.niallohiggins.com/2009/01/08/mkpath-mkdir-p-alike-in-c-for-unix/)

## [mkpath\(\) - \`mkdir -p' alike in C for UNIX](http://www.niallohiggins.com/2009/01/08/mkpath-mkdir-p-alike-in-c-for-unix/)



```

```

Most people are probably familiar with the UNIX utility,

[mkdir\(1\)](http://www.openbsd.org/cgi-bin/man.cgi?query=mkdir&apropos=0&sektion=0&manpath=OpenBSD+Current&arch=i386&format=html)

. The mkdir utility makes directories \(surprise surprise\). There is a matching

[mkdir\(2\) system call](http://www.openbsd.org/cgi-bin/man.cgi?query=mkdir&sektion=2&arch=i386&apropos=0&manpath=OpenBSD+Current)

available in the POSIX standard C library. The usage is pretty straightforward - how ever, the command-line executable, mkdir\(1\), supports a useful option

**-p**

to "create intermediate directories as required". Its very convenient to run \`mkdir -p' on a long path before copying things or whatever, since you don't have to worry about the directory structure not existing. However, the mkdir\(2\) library function doesn't support an analogous mode. If you want to recursively create all the intermediate directories in a path in your program, you must implement this yourself. I've used this same function in at least three distinct projects now and so I decided to post the code:

```

```

/* Function with behaviour like `mkdir -p' */

```
int
mkpath(const char *s, mode_t mode){
char *q, *r = NULL, *path = NULL, *up = NULL;
int rv;

rv = -1;
if (strcmp(s, ".") == 0 || strcmp(s, "/") == 0)
return (0);

if ((path = strdup(s)) == NULL)
exit(1);

if ((q = strdup(s)) == NULL)
exit(1);

if ((r = dirname(q)) == NULL)
goto out;

if ((up = strdup(r)) == NULL)
exit(1);

if ((mkpath(up, mode) == -1)
&
&
(errno != EEXIST))
goto out;

if ((mkdir(path, mode) == -1)
&
&
(errno != EEXIST))
rv = -1;
else
rv = 0;

out:
if (up != NULL)
free(up);
free(q);
free(path);
return (rv);
}
```

**UPDATE 2010-05-19**

These are the includes you need:

```
#include

#include

#include

#include

#include
```







