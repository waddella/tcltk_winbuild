# Build Tcl and Tk v8.6 on Windows for R

We follow the
[CRAN Tcl/Tk build instructions](https://svn.r-project.org/R-packages/trunk/R-Tcl-win/README)
used for the current `Tcl` distribution shipped with `R` on Windows.


# Using Microsoft VC

All the following commands assume that the project folder root is at

~~~
C:\buildVC
~~~

## Download

Download the following files into `C:\buildVC` (see
[Tcl and Tk sources](https://www.tcl.tk/software/tcltk/download.html))

* [Tcl Sources](http://prdownloads.sourceforge.net/tcl/tcl864-src.zip)
* [Tk Sources](http://prdownloads.sourceforge.net/tcl/tk864-src.zip)

Instead of downloading all the sources one-by-one you may also
download the [buildVC.zip](buildVC.zip) file and extract it into the
`C:\buildVC` folder.

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

First, copy these modified [makefile.vc 32bit](makefile_tktable32.vc)
and [makefile.vc 64bit](makefile_tktable64.vc) files into the folder
`C:\buildVC\` and run

~~~
cp C:\buildVC\makefile_tktable32.vc C:\buildVC\build32\tktable\win\makefile.vc
cp C:\buildVC\makefile_tktable64.vc C:\buildVC\build64\tktable\win\makefile.vc
~~~

Then, for the 32bit build enter

~~~
cd C:\buildVC\build32\tktable\win

"c:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\bin\vcvars32.bat"

nmake -f makefile.vc
nmake -f makefile.vc install
~~~

and to test run

~~~
nmake -f makefile.vc test 
~~~

and for the 64bit build run (**This does currently not work**)


~~~
cd C:\buildVC\build64\tktable\win

"c:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\vcvarsall.bat" amd64

nmake -f makefile.vc
nmake -f makefile.vc install
~~~


# Using msys and mingw from the R toolchain

For a later time point.
