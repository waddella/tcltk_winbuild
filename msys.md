
# Compile with R tools

Download the newest R tools from
[here](https://cran.r-project.org/bin/windows/Rtools/) and read more
about gcc 4.9.3 [here](https://github.com/rwinlib/r-base#readme).

* Install R tools under `c:\Rtools`
* Clone `https://github.com/rwinlib/r-base.git` into `c:\rbuild`
* Download R sources to ...


# Initialize

Download the [sources archive](buildVC.zip) and extract them to the
folder `C:\buildMsys`. With `cmd.exe` run

~~~
unzip buildVC.zip -d C:\buildMsys

cd C:\buildMsys
mkdir build32
mkdir build64

unzip tcl864-src.zip -d build32
unzip tcl864-src.zip -d build64
unzip tk864-src.zip -d build32
unzip tk864-src.zip -d build64
~~~


# Compile 32bit Tcl and Tk with mingw

Run 'msys.bat' and enter

~~~

export PATH=/usr/local/bin:/c/Rtools/mingw_32/bin/:/bin/

cd /c/buildMsys/build32/tcl8.6.4/win

./configure --prefix=/c/buildMsys/build32/Tcl
make
make install

cd /c/buildMsys/build32/tk8.6.4/win
./configure --prefix=/c/buildMsys/build32/Tcl --with-tcl=/c/buildMsys/build32/Tcl/lib
make
make install


cd /c/buildMsys

/c/Rtools/bin/unzip bwidget-1.9.9.zip -d build32
tar -zxvf tktable-tktable.tar.gz --directory build32

mv build32/bwidget-1.9.9 build32/Tcl/lib/BWidget

cd build32/tktable
./configure --prefix=/c/buildMsys/build32/Tcl/lib --with-tcl=/c/buildMsys/build32/Tcl/lib --with-tk=/c/buildMsys/build32/Tcl/lib
make
make install
~~~

Now the 32bit Tcl build is under `C:\buildMsys\build32\Tcl` and
available as a download [here as Tcl32rtools.zip](Tcl32rtools.zip).


# Compile 64bit tcl and tk

Run 'msys.bat' and enter **This does currently not work!**

~~~

export PATH=/usr/local/bin:/c/Rtools/mingw_64/bin/:/bin/

cd /c/buildMsys/build64/tcl8.6.4/win

./configure --prefix=/c/buildMsys/build64/Tcl --enable-64bit
make
make install

cd /c/buildMsys/build64/tk8.6.4/win
./configure --prefix=/c/buildMsys/build64/Tcl --with-tcl=/c/buildMsys/build64/Tcl/lib --enable-64bit
make
make install


cd /c/buildMsys

/c/Rtools/bin/unzip bwidget-1.9.9.zip -d build64
tar -zxvf tktable-tktable.tar.gz --directory build64

mv build64/bwidget-1.9.9 build64/Tcl/lib/BWidget

cd build64/tktable
./configure --prefix=/c/buildMsys/build64/Tcl/lib --with-tcl=/c/buildMsys/build64/Tcl/lib --with-tk=/c/buildMsys/build64/Tcl/lib
make
make install

~~~

