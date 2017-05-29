project('readline', 'c', version: '7.0')

version = meson.project_version()

add_project_arguments('-DRL_LIBRARY_VERSION="@0@"'.format(version), language: 'c')

cc = meson.get_compiler('c')

cdata = configuration_data()

if not get_option('enable_multibyte')
  cdata.set('NO_MULTIBYTE_SUPPORT', 1)
endif

cdata.set('__EXTENSIONS__', 1)
cdata.set('_ALL_SOURCE', 1)
cdata.set('_GNU_SOURCE', 1)
cdata.set('_POSIX_PTHREAD_SEMANTICS', 1)
cdata.set('_TANDEM_SOURCE', 1)

stdc_headers = '''
#include <stdlib.h>
#include <stdarg.h>
#include <string.h>
#include <float.h>

int
main ()
{
  return 0;
}
'''
if cc.compiles(stdc_headers)
  cdata.set('STDC_HEADERS', 1)
endif

retsigtype_int = '''
#include <sys/types.h>
#include <signal.h>

int
main ()
{
  return *(signal(0, 0))(0) == 1;
}
'''
if cc.compiles(retsigtype_int)
  cdata.set('RETSIGTYPE', 'int')
else
  cdata.set('RETSIGTYPE', 'void')
  cdata.set('VOID_SIGHANDLER', 1)
endif
cdata.set('HAVE_POSIX_SIGNALS', 1)

if cc.has_function('sigsetjmp')
  cdata.set('HAVE_POSIX_SIGSETJMP', 1)
endif

cdata.set('CTYPE_NON_ASCII', 1)

if cc.has_header_symbol('pwd.h', 'getpwuid') or cc.has_header_symbol('pwd.h', 'getpwuid', prefix: '#include <unistd.h>')
  cdata.set('HAVE_GETPW_DECLS', 1)
endif

dirent_prefix = '''
#include <stdio.h>
#include <sys/types.h>
'''
if cc.has_header('unistd.h')
  dirent_prefix += '#include <unistd.h>\n'
endif
if cc.has_header('dirent.h')
  dirent_prefix += '#include <dirent.h>\n'
else
  dirent_prefix += '#define dirent direct\n'
  foreach h : ['sys/ndir.h', 'sys/dir.h', 'ndir.h']
    if cc.has_header(h)
      dirent_prefix += '#include <' + h + '>\n'
    endif
  endforeach
endif
if cc.has_member('struct dirent', 'd_ino', prefix: dirent_prefix)
  cdata.set('HAVE_STRUCT_DIRENT_D_INO', 1)
endif
if cc.has_member('struct dirent', 'd_fileno', prefix: dirent_prefix)
  cdata.set('HAVE_STRUCT_DIRENT_D_FILENO', 1)
endif

cdata.set10('HAVE_DECL_AUDIT_USER_TTY', cc.has_header_symbol('linux/audit.h', 'AUDIT_USER_TTY'))

if cc.has_function('nl_langinfo') and cc.has_header_symbol('langinfo.h', 'CODESET')
  cdata.set('HAVE_LANGINFO_CODESET', 1)
endif

if cc.has_header_symbol('wchar.h', 'wchar_t')
  cdata.set('HAVE_WCHAR_T', 1)
endif
if cc.has_function('mbrtowc') and cc.has_header_symbol('wchar.h', 'mbstate_t')
  cdata.set('HAVE_MBRTOWC', 1)
  cdata.set('HAVE_MBSTATE_T', 1)
endif

if cc.has_header_symbol('sys/ioctl.h', 'TIOCSTAT', prefix: '#include <sys/types.h>')
  cdata.set('TIOCSTAT_IN_SYS_IOCTL', 1)
endif
if cc.has_header_symbol('sys/ioctl.h', 'FIONREAD', prefix: '#include <sys/types.h>')
  cdata.set('FIONREAD_IN_SYS_IOCTL', 1)
endif
if cc.has_header_symbol('sys/types.h', 'speed_t')
  cdata.set('SPEED_T_IN_SYS_TYPES', 1)
endif
if cc.has_header_symbol('sys/ioctl.h', 'struct winsize', prefix: '#include <sys/types.h>')
  cdata.set('STRUCT_WINSIZE_IN_SYS_IOCTL', 1)
elif cc.has_header_symbol('termios.h', 'struct winsize', prefix: '#include <sys/types.h>')
  cdata.set('STRUCT_WINSIZE_IN_TERMIOS', 1)
endif

functions = [
  'mbrlen',
  'mbsrtowcs',
  'iswlower',
  'iswupper',
  'strcoll',
  'towlower',
  'towupper',
  'iswctype',
  'wcwidth',
  'fcntl',
  'kill',
  'lstat',
  'readlink',
  'memmove',
  'pselect',
  'putenv',
  'select',
  'setenv',
  'setlocale',
  'strcasecmp',
  'strpbrk',
  'tcgetattr',
  'vsnprintf',
  'isascii',
  'isxdigit',
  'getpwent',
  'getpwnam',
  'getpwuid',
]
foreach f : functions
  if cc.has_function(f)
    cdata.set('HAVE_' + f.to_upper(), 1)
  endif
endforeach

headers = [
  'wctype.h',
  'wchar.h',
  'dirent.h',
  'fcntl.h',
  'unistd.h',
  'stdlib.h',
  'varargs.h',
  'stdarg.h',
  'stdbool.h',
  'string.h',
  'strings.h',
  'limits.h',
  'locale.h',
  'pwd.h',
  'memory.h',
  'termcap.h',
  'termios.h',
  'termio.h',
  'sys/ioctl.h',
  'sys/pte.h',
  'sys/stream.h',
  'sys/select.h',
  'sys/file.h',
]
foreach h : headers
  if cc.has_header(h)
    cdata.set('HAVE_' + h.underscorify().to_upper(), 1)
  endif
endforeach

configure_file(input: 'config.h.in',
  output: 'config.h',
  configuration: cdata)

add_project_arguments('-DHAVE_CONFIG_H', language: 'c')

root_incdir = include_directories('.')
libtype = get_option('default_library')

if cc.get_id() == 'gcc' or cc.get_id() == 'clang'
  candidate_flags = [
    '-Wno-missing-braces',
    '-Wno-parentheses',
    '-Wno-sometimes-uninitialized',
    '-Wno-unused-function',
    '-Wno-unused-label',
    '-Wno-unused-variable',
  ]
else
  candidate_flags = []
endif
extra_args = []
foreach flag : candidate_flags
  if cc.has_argument(flag)
    extra_args += [flag]
  endif
endforeach
add_project_arguments(extra_args, language: 'c')

subdir('include')
subdir('src')
