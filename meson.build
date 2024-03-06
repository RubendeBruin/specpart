project('specpart', 'fortran')
py3_mod = import('python').find_installation('python3')
py3_extension_module('specpart', 'specpart.f90',
  install : true,
  subdir : py3_mod.get_purelib())
