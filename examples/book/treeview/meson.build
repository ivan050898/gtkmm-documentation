# examples/book/treeview

# input: gnome, gtkmm_dep, build_examples_by_default, compile_schemas_sh,
#        copy_to_subdir_sh, meson_backend, test_timeout, project_build_root
# input and output: examples_targets

exwindow_main = ['examplewindow.cc', 'main.cc']

examples_book_treeview = [
# [[dir-name], exe-name, [sources]]
  [['combo_renderer'], 'example', exwindow_main],
  [['drag_and_drop'], 'example', exwindow_main + ['treemodel_dnd.cc']],
  [['editable_cells'], 'example', exwindow_main],
  [['filter'], 'example', exwindow_main],
  [['filter_modify'], 'example', exwindow_main],
  [['list'], 'example', exwindow_main],
  [['listviewtext'], 'example', exwindow_main],
  [['modelsort'], 'example', exwindow_main],
  [['popup'], 'example', exwindow_main + ['treeview_withpopup.cc']],
  [['tree'], 'example', exwindow_main],
]

foreach ex : examples_book_treeview
  dir = ''
  foreach dir_part : ex[0]
    dir = join_paths(dir, dir_part)
  endforeach
  ex_name = join_paths(dir, ex[1]).underscorify()
  ex_sources = []
  resources = []
  foreach src : ex[2]
    if src.endswith('.gresource.xml')
      resources = gnome.compile_resources(dir.underscorify() + '_resources',
        join_paths(dir, src),
        source_dir: dir
      )
    else
      ex_sources += join_paths(dir, src)
    endif
  endforeach

  exe_file = executable(ex_name, ex_sources, resources,
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

  target_name = join_paths('examples', 'book', 'treeview', stamp_file_name)
  examples_targets += target_name

  test('book_treeview_' + ex_name, meson_backend,
    args: target_name,
    workdir: project_build_root,
    timeout: test_timeout
  )
endforeach
