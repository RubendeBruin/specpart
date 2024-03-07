# specpart
The specpart fortran code - under construction



steps
## Preparations

Install numpy (pip install numpy)

## Generate wrapper

See https://numpy.org/doc/stable/f2py/f2py.getting-started.html#f2py-getting-started

`python -m numpy.f2py specpart.f90 -m specpart`

Output:

```commandline
Reading fortran codes...
        Reading file 'specpart.f90' (format:free)
Post-processing...
        Block: specpart
                        Block: specpart
{}
In: :specpart:specpart.f90:specpart:partinit
vars2fortran: No typespec for argument "nk".
{}
In: :specpart:specpart.f90:specpart:partinit
vars2fortran: No typespec for argument "nth".
                                Block: partinit
                                Block: partition
                                Block: ptsort
                                Block: ptnghb
                                Block: pt_fld
Applying post-processing hooks...
  character_backward_compatibility_hook
Post-processing (stage 2)...
        Block: specpart
                Block: unknown_interface
                        Block: specpart
                                Block: partinit
                                Block: partition
                                Block: ptsort
                                Block: ptnghb
                                Block: pt_fld
Building modules...
    Building module "specpart"...
                Constructing F90 module support for "specpart"...
                  Variables: ihmax npart
            Constructing wrapper function "specpart.partinit"...
              partinit(nk,nth)
            Constructing wrapper function "specpart.partition"...
              ipart = partition(spec,[nk,nth])
            Constructing wrapper function "specpart.ptsort"...
              ptsort(iihmax,nnspec)
            Constructing wrapper function "specpart.ptnghb"...
              ptnghb()
            Constructing wrapper function "specpart.pt_fld"...
              npart = pt_fld(nnspec)
    Wrote C/API module "specpart" to file ".\specpartmodule.c"
    Fortran 90 wrappers are saved to ".\specpart-f2pywrappers2.f90"
```

## Create the meson build file

Following https://numpy.org/doc/stable/f2py/buildtools/meson.html

```commandline
see meson.build file 
```

## Set meson-python as build backend

This is done in the pyproject.toml file using:

```commandline
[build-system]
requires = ["meson-python","numpy"]
build-backend = "mesonpy"
```

## Use Gitub Actions to perform the actual build

**Should the specpart-f2pywrappers2.f90 file be included in the build?**

Like so:
```commandline
py.extension_module(
    'specpart',
    ['specpart.f90',
    'specpartmodule.c',
    'specpart-f2pywrappers2.f90',
    ],
    incdir_f2py / 'fortranobject.c',
    include_directories: inc_np,
    dependencies : py_dep,
    install : true
)
```

If so then the compilation fails:

```output
FAILED: specpart.cp311-win_amd64.pyd.p/specpart-f2pywrappers2.f90.obj 
"gfortran" "-Ispecpart.cp311-win_amd64.pyd.p" "-I." "-I.." "-IC:\Users\runneradmin\AppData\Local\Temp\build-env-j2hb_ozd\Lib\site-packages\numpy\core\include" "-IC:\Users\runneradmin\AppData\Local\Temp\build-env-j2hb_ozd\Lib\site-packages\numpy\f2py\src" "-IC:\hostedtoolcache\windows\Python\3.11.8\x64\Include" "-fvisibility=hidden" "-fdiagnostics-color=always" "-DNDEBUG" "-D_FILE_OFFSET_BITS=64" "-Wall" "-O3" "-DMS_WIN64=" "-Jspecpart.cp311-win_amd64.pyd.p" -o specpart.cp311-win_amd64.pyd.p/specpart-f2pywrappers2.f90.obj "-c" ../specpart-f2pywrappers2.f90
../specpart-f2pywrappers2.f90:13:26:

   13 |       use specpart, only : pt_fld
      |                          1
Error: Symbol 'pt_fld' referenced at (1) not found in module 'specpart'
../specpart-f2pywrappers2.f90:12:26:

   12 |       use specpart, only : ptnghb
      |                          1
Error: Symbol 'ptnghb' referenced at (1) not found in module 'specpart'
../specpart-f2pywrappers2.f90:11:26:

   11 |       use specpart, only : ptsort
      |                          1
Error: Symbol 'ptsort' referenced at (1) not found in module 'specpart'
```

**If not included**

Fails with undefined reference to `f2pyinitspecpart_'` error:

```commandline
[5/5] Linking target specpart.cp311-win_amd64.pyd
FAILED: specpart.cp311-win_amd64.pyd 
"gcc"  -o specpart.cp311-win_amd64.pyd specpart.cp311-win_amd64.pyd.p/specpart.f90.obj specpart.cp311-win_amd64.pyd.p/specpartmodule.c.obj specpart.cp311-win_amd64.pyd.p/d05849f063a8207084c7a3499469aa652b53c914_site-packages_numpy_f2py_src_fortranobject.c.obj "-LC:/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/12.2.0" "-LC:/mingw64/lib/gcc/x86_64-w64-mingw32/12.2.0" "-LC:/mingw64/bin/../lib/gcc" "-LC:/mingw64/lib/gcc" "-LC:/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/12.2.0/../../../../x86_64-w64-mingw32/lib/../lib" "-LC:/mingw64/x86_64-w64-mingw32/lib" "-LC:/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/12.2.0/../../../../lib" "-LC:/mingw64/lib" "-LC:/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/12.2.0/../../../../x86_64-w64-mingw32/lib" "-LC:/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/12.2.0/../../.." "-Wl,--allow-shlib-undefined" "-Wl,-O1" "-shared" "-Wl,--start-group" "-Wl,--out-implib=specpart.cp311-win_amd64.dll.a" "C:\hostedtoolcache\windows\Python\3.11.8\x64\python311.dll" "-lkernel32" "-luser32" "-lgdi32" "-lwinspool" "-lshell32" "-lole32" "-loleaut32" "-luuid" "-lcomdlg32" "-ladvapi32" "-lgfortran" "-lm" "-Wl,--end-group"
C:/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/12.2.0/../../../../x86_64-w64-mingw32/bin/ld.exe: specpart.cp311-win_amd64.pyd.p/specpartmodule.c.obj:specpartmodule:(.text+0x58): undefined reference to `f2pyinitspecpart_'
```

That makes sense as 
`f2pyinitspecpart` is defined as an external function in the specpartmodule.c file:

```extern void F_FUNC(f2pyinitspecpart,F2PYINITSPECPART)(void (*)(char*,char*,char *,char *,char *,char *,char *));``` 

but is defined in the `specpart-f2pywrappers2.f90` file:

## Use the pre-existing specpart.pyf file

Generate wrapper using the pre-exising .pyf file:

```commandline
python -m numpy.f2py specpart.pyf -m specpart
```

output:

```
Reading fortran codes...
	Reading file 'specpart.pyf' (format:free)
Post-processing...
	Block: specpart
			Block: specpart
					Block: specpart
						Block: partition
Applying post-processing hooks...
  character_backward_compatibility_hook
Post-processing (stage 2)...
	Block: specpart
Building modules...
    Building module "specpart"...
		Constructing F90 module support for "specpart"...
		  Variables: ihmax
            Constructing wrapper function "specpart.partition"...
              ipart = partition(spec)
    Wrote C/API module "specpart" to file ".\specpartmodule.c"
    Fortran 90 wrappers are saved to ".\specpart-f2pywrappers2.f90"
    Building module "specpart"...
    Wrote C/API module "specpart" to file ".\specpartmodule.c"
```

This creates a much shorter .f90 file.

## Links
https://rgoswami.me/posts/numpy-meson-f2py/

