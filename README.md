# Build Tcl and Tk v8.6 on Windows for R

We follow the
[CRAN Tcl/Tk build instructions](https://svn.r-project.org/R-packages/trunk/R-Tcl-win/README)
used for the current `Tcl` distribution shipped with `R` on Windows.

* Currently the *"Building the CHM help"* part is missing in this guide

# Using Microsoft VC

All the following commands assume that the project folder root is at

~~~
C:\buildVC
~~~

## Download

This page provides the url's to all the sources needed to compile
`Tcl`, `Tk` and `R`. You can also download the
[buildVC.zip](buildVC.zip) file and extract it into the `C:\buildVC`
folder and then just follow the command line parts.


Download the following files into `C:\buildVC` (see
[Tcl and Tk sources](https://www.tcl.tk/software/tcltk/download.html))

* [Tcl Sources](http://prdownloads.sourceforge.net/tcl/tcl864-src.zip)
* [Tk Sources](http://prdownloads.sourceforge.net/tcl/tk864-src.zip)


## Unzip

In `cmd.exe` execute

~~~
cd C:\buildVC
mkdir build32
mkdir build64

unzip tcl864-src.zip -d build32
unzip tcl864-src.zip -d build64
unzip tk864-src.zip -d build32
unzip tk864-src.zip -d build64
~~~

## Compile 32bit Tcl and Tk

Setting the Path and Environment Variables for Command-Line Builds
(adjust according to Visual Studio version installed). In `cmd.exe`
enter

~~~
"c:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\bin\vcvars32.bat"
~~~

Now build `Tcl`

~~~
cd C:\buildVC\build32\tcl8.6.4\win
nmake -nologo -f makefile.vc TCLDIR=C:\buildVC\build32\tcl8.6.4 release
nmake -f makefile.vc install INSTALLDIR=C:\buildVC\build32\Tcl
~~~

and then `Tk`

~~~
cd C:\buildVC\build32\tk8.6.4\win
nmake -nologo -f makefile.vc TCLDIR=C:\buildVC\build32\tcl8.6.4 release
nmake -f makefile.vc install INSTALLDIR=C:\buildVC\build32\Tcl
~~~

Now check if this is indeed a 32bit build. Execute
`C:\buildVC\build32\Tcl\bin\wish86t.exe` and enter

~~~
set tcl_platform(pointerSize)
~~~

if the returned number is `4` then the 32bit build was successful.

Run the tests. These may take a long time.

~~~
cd C:\buildVC\build32\tcl8.6.4\win
nmake -nologo -f makefile.vc TCLDIR=C:\buildVC\build32\tcl8.6.4 test

cd C:\buildVC\build32\tk8.6.4\win
nmake -nologo -f makefile.vc TCLDIR=C:\buildVC\build32\tcl8.6.4 test
~~~

## Compile 64bit Tcl and Tk

Now we need to setup the 64bit build environment variables (read more
[here](https://msdn.microsoft.com/en-us/library/x4d2c09s.aspx))

Start a new `cmd.exe` and enter

~~~
"c:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\vcvarsall.bat" amd64
~~~

Then, as before

~~~
cd C:\buildVC\build64\tcl8.6.4\win
nmake -nologo -f makefile.vc TCLDIR=C:\buildVC\build64\tcl8.6.4 release
nmake -f makefile.vc install INSTALLDIR=C:\buildVC\build64\Tcl

cd C:\buildVC\build64\tk8.6.4\win
nmake -nologo -f makefile.vc TCLDIR=C:\buildVC\build64\tcl8.6.4 release
nmake -f makefile.vc install INSTALLDIR=C:\buildVC\build64\Tcl
~~~

Check if this is indeed a 64bit build. Execute
`C:\buildVC\build64\Tcl\bin\wish86t.exe` and enter

~~~
set tcl_platform(pointerSize)
~~~

if the returned number is `8` then the 64bit build was successful.


Run the tests

~~~
cd C:\buildVC\build64\tcl8.6.4\win
nmake -nologo -f makefile.vc TCLDIR=C:\buildVC\build64\tcl8.6.4 test

cd C:\buildVC\build64\tk8.6.4\win
nmake -nologo -f makefile.vc TCLDIR=C:\buildVC\build64\tcl8.6.4 test
~~~

## Additional Extensions

Currently, the `Tcl` build that ships with `R` has also the `Tcl`
libraries `BWidget` and `Tktable` installed. The following
instructions add those two libraries to the 32bit and 64bit builds.

Download the following two files into the `C:\buildVC` folder

* `bwidget-1.9.9.zip` from [Bwidget sourceforge](http://sourceforge.net/projects/tcllib/files/BWidget/)
* Tktable 2.10 did not work for me, so I downloaded `tktable-tktable.tar.gz`, the last CVS commit (ActiveTcl also uses version 2.11, though I am not sure whether it is the same commit), from [Tktable sourceforge](http://tktable.sourceforge.net/) form the [CVS repository here](http://tktable.cvs.sourceforge.net/viewvc/tktable/tktable/?view=tar) 


in `cmd.exe` run

~~~
cd C:\buildVC

unzip bwidget-1.9.9.zip -d build32
unzip bwidget-1.9.9.zip -d build64

tar -zxvf tktable-tktable.tar.gz --directory build32
tar -zxvf tktable-tktable.tar.gz --directory build64
~~~

### BWidget

The bwidget extension needs no compilation

~~~
cd C:\buildVC
mv build32\bwidget-1.9.9 build32\Tcl\lib\BWidget
mv build64\bwidget-1.9.9 build64\Tcl\lib\BWidget
~~~

### Tktable

Thanks to [Ashok P. Nadkarni](http://www.magicsplat.com/) who provided new makefiles to compile `Tktable` for 32bit and 64bit Tcl builds on Windows. Theses makefiles must be [downloaded here](tktable_makefiles.zip) and copied into the `C:\buildVC\` folder. Then run

~~~
cd C:\buildVC
unzip -o tktable_makefiles.zip -d C:\buildVC\build32\tktable\win
unzip -o tktable_makefiles.zip -d C:\buildVC\build64\tktable\win
~~~

Then, for the 32bit build start a new `cmd.exe` and enter

~~~
cd C:\buildVC\build32\tktable\win

"c:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\bin\vcvars32.bat"

nmake -f makefile.vc TCLDIR=c:/buildVC/build32/Tcl INSTALLDIR=c:/buildVC/build32/Tcl
nmake -f makefile.vc TCLDIR=c:/buildVC/build32/Tcl INSTALLDIR=c:/buildVC/build32/Tcl install
~~~

and to test run

~~~
nmake -f makefile.vc TCLDIR=c:/buildVC/build32/Tcl INSTALLDIR=c:/buildVC/build32/Tcl test
~~~

and for the 64bit build start a new `cmd.exe` and enter

~~~
cd C:\buildVC\build64\tktable\win

"c:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\vcvarsall.bat" amd64

nmake -f makefile.vc TCLDIR=c:/buildVC/build64/Tcl INSTALLDIR=c:/buildVC/build64/Tcl
nmake -f makefile.vc TCLDIR=c:/buildVC/build64/Tcl INSTALLDIR=c:/buildVC/build64/Tcl install
~~~

and to test run

~~~
nmake -f makefile.vc TCLDIR=c:/buildVC/build64/Tcl INSTALLDIR=c:/buildVC/build64/Tcl test
~~~


## Final Tcl builds

Finally, the 32bit and 66bit Tcl builds are in

* `C:\buildVC\build32\Tcl`
* `C:\buildVC\build64\Tcl`

## Compile 32bit R with new Tcl build


You can find the detailed `R` build instructions
[here](https://cran.r-project.org/doc/manuals/r-release/R-admin.html#Building-from-source).

Note that building `R` requires `Rtools` which can be downloaded
[here](https://cran.r-project.org/bin/windows/Rtools/). Also, download
the recommended software
[from here](https://www.stats.ox.ac.uk/pub/Rtools/goodies/multilib/local320.zip) and save it into `C:\buildVC`


Either download the current release or the 

* [current R release](https://cran.r-project.org/src/base/R-3/R-3.2.3.tar.gz)
* [pre-release for R-patched](https://cran.r-project.org/src/base-prerelease/)
* [R-devel](https://cran.r-project.org/src/base-prerelease/)

We use `R-patched` and `gcc-4.6.3` here. Download the `R-patched` file
into `C:\buildVC`. Also download the recommended external software and
save it into `C:\buildVC`
[from here](https://www.stats.ox.ac.uk/pub/Rtools/goodies/multilib/local323.zip).

Unpack the files

~~~
cd C:\buildVC
tar -zxvf R-patched_2016-01-29_r70052.tar.gz
mv R-patched R32
unzip local323.zip -d c:\buildVC\R32\extsoft
~~~

Modify the `MkRules`

~~~
cd C:\buildVC\R32\src\gnuwin32
cp MkRules.dist MkRules.local
sed -i 's!# EXT_LIBS = $(LOCAL_SOFT)!EXT_LIBS = C:/buildVC/R32/extsoft!' MkRules.local
~~~

Create the `C:\tmp` folder if it does not exist and set the `TMPDIR` environment variable to `C:\tmp`

~~~
set TMPDIR=C:\tmp
~~~


Now copy the `Tcl` builds

~~~
cp -R C:\buildVC\build32\Tcl C:\buildVC\R32

## otherwise linker can not find -ltk85 and -ltcl85
cd C:\buildVC\R32\Tcl\bin
cp tcl86t.dll tcl85.dll
cp tk86t.dll tk85.dll
~~~

Now compile `R`

~~~
cd C:\buildVC\R32\src\gnuwin32
make all recommended 
~~~

Then test if `R` has been built with `Tcl` version

~~~
library(tcltk)
.Tcl('set tcl_version')
~~~

which should return `8.6`

## Build 64bit R

~~~
cd C:\buildVC
tar -zxvf R-patched_2016-01-29_r70052.tar.gz
mv R-patched R64
unzip local323.zip -d c:\buildVC\R64\extsoft
~~~

Modify the `MkRules`

~~~
cd C:\buildVC\R64\src\gnuwin32
cp MkRules.dist MkRules.local
sed -i 's!# EXT_LIBS = $(LOCAL_SOFT)!EXT_LIBS = C:/buildVC/R64/extsoft!' MkRules.local
sed -i 's!# MULTI =!MULTI = 64!' MkRules.local
~~~

Now copy the `Tcl` builds

~~~
cp -R C:\buildVC\build64\Tcl C:\buildVC\R64\Tcl

## otherwise linker can not find -ltk85 and -ltcl85
cd C:\buildVC\R64\Tcl\bin
cp tcl86t.dll tcl85.dll
cp tk86t.dll tk85.dll
~~~

Now compile `R` 

~~~
cd C:\buildVC\R64\src\gnuwin32
make all recommended 
~~~

**This does not work** with the error message

~~~
making tcltk_win.d from tcltk_win.c
gcc -m64 -I"../../../../include" -DNDEBUG -I "../../../../Tcl"/include -DWin32 -
I../../../../src/include -DHAVE_CONFIG_H    -I"C:/buildVC/R64/extsoft/include"
   -O2 -Wall  -std=gnu99 -mtune=core2 -c init.c -o init.o
In file included from init.c:22:0:
tcltk.h:25:17: fatal error: tcl.h: No such file or directory
compilation terminated.
make[4]: *** [init.o] Error 1
make[3]: *** [mksrc-win2] Error 1
make[2]: *** [all] Error 2
make[1]: *** [R] Error 1
make: *** [all] Error 2
~~~

