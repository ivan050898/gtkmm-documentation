# examples/book/buildapp

# input: gnome, gtkmm_dep, build_examples_by_default, compile_schemas_sh,
#        copy_to_subdir_sh, meson_backend, test_timeout, project_build_root
# input and output: examples_targets

exapp_main = ['exampleapplication.cc', 'exampleappwindow.cc', 'main.cc']
exapp_prefs = exapp_main + \
  ['exampleappprefs.cc', 'exampleapp.gresource.xml', 'org.gtkmm.exampleapp.gschema.xml']

examples_book_buildapp = [
# [[dir-name], exe-name, [sources]]
  [['step1'], 'exampleapp', exapp_main],
  [['step2'], 'exampleapp', exapp_main + ['exampleapp.gresource.xml']],
  [['step3'], 'exampleapp', exapp_main + ['exampleapp.gresource.xml']],
  [['step4'], 'exampleapp', exapp_main + ['exampleapp.gresource.xml']],
  [['step5'], 'exampleapp', exapp_prefs],
  [['step6'], 'exampleapp', exapp_prefs],
  [['step7'], 'exampleapp', exapp_prefs],
  [['step8'], 'exampleapp', exapp_prefs],
  [['step9'], 'exampleapp', exapp_prefs],
]

glib_compile_schemas = find_program('glib-compile-schemas', required: false)

foreach ex : examples_book_buildapp
  dir = ''
  foreach dir_part : ex[0]
    dir = join_paths(dir, dir_part)
  endforeach
  ex_name = join_paths(dir, ex[1]).underscorify()
  ex_sources = []
  resources = []
  schemas = []
  foreach src : ex[2]
    if src.endswith('.gresource.xml')
      resources = gnome.compile_resources(dir.underscorify() + '_resources',
        join_paths(dir, src),
        source_dir: dir
      )
    elif src.endswith('.gschema.xml')
      if glib_compile_schemas.found()
        schemas = custom_target(dir.underscorify() + '_schemas',
          input: join_paths(dir, src),
          output: dir.underscorify() + '_gschemas.compiled',
          command: [
            compile_schemas_sh,
            dir,
            meson.current_source_dir(),
            meson.current_build_dir(),            
            '@OUTPUT@'
          ]
        )
      endif
    else
      ex_sources += join_paths(dir, src)
    endif
  endforeach

  exe_file = executable(ex_name, ex_sources, resources, schemas,
    dependencies: gtkmm_dep,
    gui_app: true,
    build_by_default: build_examples_by_default
  )

  stamp_file_name = ex_name + '_copy.stamp'
  custom_target(stamp_file_name,
    input: exe_file,
    output: stamp_file_name,
    command: [
      copy_to_subdir_sh,
      '@INPUT@',
      dir,
      ex[1],
      '@OUTPUT@',
    ],
    build_by_default: build_examples_by_default
  )

  target_name = join_paths('examples', 'book', 'buildapp', stamp_file_name)
  examples_targets += target_name

  test('book_buildapp_' + ex_name, meson_backend,
    args: target_name,
    workdir: project_build_root,
    timeout: test_timeout
  )
endforeach

