# Specpart

This is used by the wavespectra packages (both wavespectra/wavespectra and metocean/wavespectra).

This repository is used to create pre-build binary wheels for windows.<br>
Linux and macos are open to contributions.


## Creation

Getting this to work was a bit of a trial-and-error. The following problems were encountered:
- not compiling, fixed by removing some of the statements from the generated .f90 file
- not working on the target machine due to missing .dll files (Importing created package fails with ImportError: DLL load failed)

The following steps were taken with help from the community:

- generated wrapper using f2py and specpart.f90 (not using pre-exising .pyf file) file

```commandline
python -m numpy.f2py specpart.f90 -m specpart
```

- manually edited the generated .f90 wrapper file, removing sections:
```
use specpart, only : ptsort
use specpart, only : ptnghb
use specpart, only : pt_fld
```

- created meson build file: [meson.build](https://github.com/RubendeBruin/specpart/blob/main/meson.build):
- set meson-python as build backend: [pyproject.toml](https://github.com/RubendeBruin/specpart/blob/main/pyproject.toml) 
- used Github Actions to perform the actual build on windows: [actions](https://github.com/RubendeBruin/specpart/blob/main/.github/workflows/main.yml)
 
Attention points:
- As the target machine is not the same as the build machine, the dlls need to be bundeled: see [discussion](https://github.com/mesonbuild/meson-python/discussions/595#discussioncomment-8704971)
- use delvewheel to inspect and repair created wheels. This includes all the required .dll files in the wheel
- delvewheel stores the repaired wheel in the /wheelhouse folder, this is uploaded as artifact.
- download the wheel and install it using pip

Checks:
- Importing works,
- Required functions are available:

```
Python 3.11.3 (tags/v3.11.3:f3909b8, Apr  4 2023, 23:49:59) [MSC v.1934 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> import specpart
>>> specpart.specpart.partition
<fortran function partition>
>>> specpart.specpart.partition()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: specpart.specpart.partition() missing required argument 'spec' (pos 1)
>>> specpart.specpart.ihmax
array(200, dtype=int32)
>>> specpart.specpart.npart
array(0, dtype=int32)
>>>
> ``````


## Open questions

- Why is devewheel required when we do static linking?
- Why does devwheel not accept wildcards?