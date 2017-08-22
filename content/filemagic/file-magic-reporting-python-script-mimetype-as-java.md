Title: Correct the file tool from reporting the mime-type of a Python script as text/x-java
Date: 2015-12-23
Status: Published


When using the file tool to report the mime type of a file, your
distributions file-libs package which ships the patterns that detect
the mime type of the script maybe slightly out dated.

For example, take the python script below.

```null
#!/usr/bin/env python

import sys

def main():
    print("hello world")

if __name__ == '__main__':
    sys.exit(main())
```

Running file over this python script will report a mime-type of
text/x-java for older distributions such as CentOS6 (see
bug report [#9999](https://bugs.centos.org/view.php?id=9999)).

```
$ file --mime-type helloworld.py 
helloworld.py: text/x-java
```

file will accept local magic data found in /etc/magic so by adding the following pattern below to /etc/magic, we should be able to have file report the correct mime-type for our hello world script.

```
0       regex    \^(\ |\t)*def\ +[a-zA-Z]+
>&0     regex   \ *\(([a-zA-Z]|,|\ )*\):$ Python script text executable
!:mime text/x-python

```

```
$ file --mime-type helloworld.py 
helloworld.py: text/x-python
```
