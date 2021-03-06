# docs/tutorial

# input: gnome, gtkmm_datadir, gtkmm_pcname, tutorial_custom_cmd_sh, project_source_root
# output: can_parse_and_validate, build_translations_by_default, can_build_translations,
#         build_pdf_by_default, can_build_pdf

# xsltproc is required by tutorial_custom_cmd_sh html.
xsltproc = find_program('xsltproc', required: true)
# perl is required by tutorial_custom_cmd_sh insert_example_code.
perl = find_program('perl', required: true)
xmllint = find_program('xmllint', required: false)

can_parse_and_validate = xmllint.found()

can_build_translations = find_program('msgfmt', required: false).found() and \
                         find_program('itstool', required: false).found()
build_translations_by_default = get_option('build-translations')
validate = get_option('validation') ? 'true' : 'false'

dblatex = find_program('dblatex', required: false)
can_build_pdf = dblatex.found() or (xmllint.found() and \
                find_program('docbook2pdf', required: false).found())
build_pdf_by_default = get_option('build-pdf')

# Installation directories
tutorialdir = join_paths(gtkmm_datadir, 'doc', gtkmm_pcname, 'tutorial')
tutorial_htmldir = join_paths(tutorialdir, 'html')
tutorial_figuresdir = join_paths(tutorial_htmldir, 'figures')
tutorial_iconsdir = join_paths(tutorial_htmldir, 'icons')

tutorial_figurefiles = [
  'aspectframe.png',
  'assistant.png',
  'box_packing1.png',
  'box_packing2.png',
  'box_packing3.png',
  'buildapp_app_menu.png',
  'buildapp_header_bar.png',
  'buildapp_opening_files.png',
  'buildapp_populating_window.png',
  'buildapp_pref_dialog.png',
  'buildapp_properties.png',
  'buildapp_search_bar.png',
  'buildapp_side_bar.png',
  'buildapp_trivial_app.png',
  'buttons.png',
  'cairo_clock.png',
  'cairo_joins.png',
  'checkbutton.png',
  'clipboard_ideal.png',
  'clipboard_simple.png',
  'combo.png',
  'combobox_complex.png',
  'combobox_text.png',
  'comboboxentry_complex.png',
  'comboboxentry_text.png',
  'custom_container.png',
  'custom_widget.png',
  'dialogs_about.png',
  'dialogs_colorchooserdialog.png',
  'dialogs_filechooser.png',
  'dialogs_fontchooserdialog.png',
  'dialogs_messagedialog.png',
  'dialogs_simple.png',
  'drag_and_drop.png',
  'drawingarea_arcs.png',
  'drawingarea_curve.png',
  'drawingarea_image.png',
  'drawingarea_lines.png',
  'drawingarea_pango_text.png',
  'drawingarea_text.png',
  'drawingarea_thin_lines.png',
  'entry.png',
  'entry_completion.png',
  'entry_icon.png',
  'entry_progress.png',
  'expander.png',
  'filechooserbutton.png',
  'frame.png',
  'grid.png',
  'helloworld.png',
  'helloworld2.png',
  'idle.png',
  'infobar.png',
  'keyboardevents_simple.png',
  'keyboardevents_propagation.png',
  'label.png',
  'main_menu.png',
  'menu_popup.png',
  'menus_and_toolbars.png',
  'menus_menu.png',
  'menus_menubar.png',
  'multithread.png',
  'notebook.png',
  'paned.png',
  'printing.png',
  'progressbar.png',
  'radiobuttons.png',
  'range_widgets.png',
  'recentfiles.png',
  'scrolledwindow.png',
  'spinbutton.png',
  'textview.png',
  'toolbar.png',
  'tooltip.png',
  'treeview_combo_renderer.png',
  'treeview_draganddrop.png',
  'treeview_editablecells.png',
  'treeview_list.png',
  'treeview_popup.png',
  'treeview_tree.png',
  'update_ui.png',
]
tutorial_figures = []
tutorial_C_figures = []
foreach f : tutorial_figurefiles
  tutorial_figures += join_paths('figures', f)
  tutorial_C_figures += join_paths('C', 'figures', f)
endforeach

tutorial_iconfiles = [
  'caution.png',
  'home.png',
  'important.png',
  'next.png',
  'note.png',
  'prev.png',
  'tip.png',
  'up.png',
  'warning.png',
]
tutorial_icons = []
foreach i : tutorial_iconfiles
  tutorial_icons += join_paths('icons', i)
endforeach

# Create a DocBook C locale XML file with the examples' source code included.
# Copy it to the source directory, where gnome.yelp() expects to find it.
index_docbook = custom_target('index.docbook',
  input: join_paths('C', 'index-in.docbook'),
  output: 'index.docbook',
  command: [
    tutorial_custom_cmd_sh, 'insert_example_code',
    join_paths(meson.current_source_dir(), 'insert_example_code.pl'),
    join_paths(project_source_root, 'examples', 'book'),
    '@INPUT@',
    '@OUTPUT@',
  ],
  build_by_default: true
)

# Create an html version of the C locale's version of the DocBook.
custom_target('html_index.html',
  input: index_docbook,
  output: 'html',
  command: [
    tutorial_custom_cmd_sh, 'html',
    'html',
    meson.current_build_dir(),
    '@INPUT@',
    '@OUTPUT@',    
  ],
  build_by_default: true,
  install: true,
  install_dir: tutorialdir
)

if can_parse_and_validate
  # Parse and possibly validate the C locale's version of the DocBook.
  custom_target('C-xmllint',
    input: index_docbook,
    output: 'C_xmllint.stamp',
    command: [
      tutorial_custom_cmd_sh, 'xmllint',
      validate,
      '@INPUT@',
      '@OUTPUT@'
    ],
    build_by_default: true,
  )
endif

if can_build_pdf
  # Create a PDF file of the C locale's version of the DocBook.
  # Prefer dblatex, if both dblatex and docbook2pdf are available.
  custom_target('C-pdf',
    input: index_docbook,
    output: 'programming-with-gtkmm.pdf',
    command: [
      tutorial_custom_cmd_sh,
      dblatex.found() ? 'dblatex' : 'docbook2pdf',
      '@INPUT@',
      join_paths(meson.current_source_dir(), 'C', 'figures'),
      '@OUTPUT@'
    ],
    build_by_default: build_pdf_by_default,
  )
endif

if can_build_translations and build_translations_by_default
  # Build translations. Install help documentation using Yelp.
  # C/index.docbook is copied from the source directory.
  # <lang>/index.docbook are written to the build directory, and copied from there.
  # It's bad that gnome.yelp() builds only at install time.
  # See https://github.com/mesonbuild/meson/issues/2775
  gnome.yelp('gtkmm-tutorial',
    sources: 'index.docbook',
    media: tutorial_figures,
    symlink_media: true
)
endif

# Install extra files in the html directory.
install_data('style.css', install_dir: tutorial_htmldir)
install_data(tutorial_C_figures, install_dir: tutorial_figuresdir)
install_data(tutorial_icons, install_dir: tutorial_iconsdir)

