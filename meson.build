project('specpart', 'fortran')

py = import('python').find_installation(pure: false)

py.extension_module(
    'specpart',
    'specpart.pyf',
    'specpart.f90',
    install : true,
  )
