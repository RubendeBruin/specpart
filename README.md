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




### Signature file

In this case we have the "signature file" (specpart.pyf) already.

Created a new one using `python -m numpy.f2py specpart.f90 -m specpart -h specpart.pyf`

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
Saving signatures to file ".\specpart.pyf"```

The generated one is different from the one we already had.

https://numpy.org/doc/stable/f2py/signature-file.html




### Build the extension module

`python -m numpy.f2py specpart.pyf`

```commandline
Reading fortran codes...
        Reading file 'specpart.pyf' (format:free)
Post-processing...
        Block: specpart
                        Block: specpart
                                Block: partition
Applying post-processing hooks...
  character_backward_compatibility_hook
Post-processing (stage 2)...
        Block: specpart
                Block: unknown_interface
                        Block: specpart
                                Block: partition
Building modules...
    Building module "specpart"...
                Constructing F90 module support for "specpart"...
                  Variables: ihmax
            Constructing wrapper function "specpart.partition"...
              ipart = partition(spec)
    Wrote C/API module "specpart" to file ".\specpartmodule.c"
    Fortran 90 wrappers are saved to ".\specpart-f2pywrappers2.f90"
```

## Create the meson build file

Following https://numpy.org/doc/stable/f2py/buildtools/meson.html

```commandline


## Links
https://rgoswami.me/posts/numpy-meson-f2py/

