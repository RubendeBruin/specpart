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


## Links
https://rgoswami.me/posts/numpy-meson-f2py/

