term_libs = []
if get_option('with-curses')
  term_libs += '-lcurses'
else
  term_libs += '-ltermcap'
endif

readline_sources = [
  'readline.c',
  'funmap.c',
  'keymaps.c',
  'vi_mode.c',
  'parens.c',
  'rltty.c',
  'complete.c',
  'bind.c',
  'isearch.c',
  'display.c',
  'signals.c',
  'util.c',
  'kill.c',
  'undo.c',
  'macro.c',
  'input.c',
  'callback.c',
  'terminal.c',
  'xmalloc.c',
  'xfree.c',
  'history.c',
  'histsearch.c',
  'histexpand.c',
  'histfile.c',
  'nls.c',
  'search.c',
  'shell.c',
  'savestring.c',
  'tilde.c',
  'text.c',
  'misc.c',
  'compat.c',
  'mbutil.c',
  'colors.c',
  'parse-colors.c',
]
readline = library('readline', readline_sources,
  include_directories: [root_incdir, readline_incdir, readline_parent_incdir],
  version: version,
  soversion: version,
  link_args: term_libs,
  install: true,
)
readline_dep = declare_dependency(
  include_directories: [readline_parent_incdir],
  link_with: readline,
)
if libtype == 'static'
  readline_static_dep = declare_dependency(
    include_directories: [readline_parent_incdir],
    link_with: readline,
  )
else
  readline_static = static_library('readline', readline_sources,
    include_directories: [root_incdir, readline_incdir, readline_parent_incdir],
    install: true,
  )
  readline_static_dep = declare_dependency(
    include_directories: [readline_parent_incdir],
    link_with: readline_static,
  )
endif

history_sources = [
  'history.c',
  'histexpand.c',
  'histfile.c',
  'histsearch.c',
  'shell.c',
  'mbutil.c',
  'xmalloc.c',
  'xfree.c',
]
history = library('history', history_sources,
  include_directories: [root_incdir, readline_incdir, readline_parent_incdir],
  version: version,
  soversion: version,
  install: true,
)
history_dep = declare_dependency(
  include_directories: [readline_parent_incdir],
  link_with: history,
)
if libtype == 'static'
  history_static_dep = declare_dependency(
    include_directories: [readline_parent_incdir],
    link_with: history,
  )
else
  history_static = static_library('history', history_sources,
    include_directories: [root_incdir, readline_incdir, readline_parent_incdir],
    install: true,
  )
  history_static_dep = declare_dependency(
    include_directories: [readline_parent_incdir],
    link_with: history_static,
  )
endif

pkg = import('pkgconfig')
pkg.generate(filebase: 'readline',
  name: 'Readline',
  version: version,
  description: 'Gnu Readline library for command line editing',
  subdirs: 'readline',
  libraries: [readline],
  libraries_private: term_libs,
)
