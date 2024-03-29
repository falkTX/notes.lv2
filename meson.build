project('notes.lv2', 'c', default_options : [
	'buildtype=release',
	'warning_level=3',
	'werror=false',
	'b_lto=false',
	'c_std=gnu11'])

d2tk = subproject('d2tk')

lv2libdir = get_option('lv2libdir')
build_tests = get_option('build-tests')
use_fontconfig = get_option('use-fontconfig')

inst_dir = join_paths(lv2libdir, meson.project_name())

if get_option('use-backend-nanovg').enabled()
	d2tk_dep = d2tk.get_variable('d2tk_nanovg')
elif get_option('use-backend-cairo').enabled()
	d2tk_dep = d2tk.get_variable('d2tk_cairo')
else
	error('no valid UI backend given')
endif

source_root = meson.source_root()
build_root = meson.build_root()

add_project_arguments('-D_GNU_SOURCE', language : 'c')

conf_data = configuration_data()
cc = meson.get_compiler('c')

lv2_validate = find_program('lv2_validate', native : true, required : false)
sord_validate = find_program('sord_validate', native : true, required : false)
lv2lint = find_program('lv2lint', required : false)
xdg_open = find_program('xdg-open', required : false)

m_dep = cc.find_library('m')
lv2_dep = dependency('lv2', version : '>=1.16.0')

if cc.has_member('LV2UI_Request_Value', 'request',
		prefix : '#include <lv2/lv2plug.in/ns/extensions/ui/ui.h>')
	add_project_arguments('-D_LV2_HAS_REQUEST_VALUE', language : 'c')
	message('building with ui:requestValue support')
endif

dsp_deps = [m_dep, lv2_dep]
ui_deps = [lv2_dep, d2tk_dep]

props_inc = include_directories('props.lv2')
ser_inc = include_directories('ser_atom.lv2')
d2tk_inc = include_directories(join_paths('subprojects', 'd2tk'))
inc_dir = [props_inc, ser_inc, d2tk_inc]

dsp_srcs = ['notes.c']

ui_srcs = ['notes_ui.c']

c_args = ['-fvisibility=hidden']

version = run_command('cat', 'VERSION').stdout().strip().split('.')
add_project_arguments('-DNOTES_VERSION="'
	+ version[0] + '.' + version[1] + '.' + version[2] + '"', language : 'c')
conf_data.set('MAJOR_VERSION', version[0])
conf_data.set('MINOR_VERSION', version[1])
conf_data.set('MICRO_VERSION', version[2])

mod = shared_module('notes', dsp_srcs,
	c_args : c_args,
	include_directories : inc_dir,
	name_prefix : '',
	dependencies : dsp_deps,
	install : true,
	install_dir : inst_dir)

ui = shared_module('notes_ui', ui_srcs,
	c_args : c_args,
	include_directories : inc_dir,
	name_prefix : '',
	dependencies : ui_deps,
	install : true,
	install_dir : inst_dir)

bank_filter_through = run_command('cat', 'bank-filter_through.dsp').stdout()
conf_data.set('BANK-FILTER_THROUGH', bank_filter_through)

bank_filter_gain = run_command('cat', 'bank-filter_gain.dsp').stdout()
conf_data.set('BANK-FILTER_GAIN', bank_filter_gain)

bank_time_lfo = run_command('cat', 'bank-time_lfo.dsp').stdout()
conf_data.set('BANK-TIME_LFO', bank_time_lfo)

bank_instrument_osc = run_command('cat', 'bank-instrument_osc.dsp').stdout()
conf_data.set('BANK-INSTRUMENT_OSC', bank_instrument_osc)

bank_analyzer_vu_meter = run_command('cat', 'bank-analyzer_vu-meter.dsp').stdout()
conf_data.set('BANK-ANALYZER_VU-METER', bank_analyzer_vu_meter)

suffix = mod.full_path().strip().split('.')[-1]
conf_data.set('MODULE_SUFFIX', '.' + suffix)

manifest_ttl = configure_file(
	input : 'manifest.ttl.in',
	output : 'manifest.ttl',
	configuration : conf_data,
	install : true,
	install_dir : inst_dir)

dsp_ttl = configure_file(
	input : 'notes.ttl',
	output : 'notes.ttl',
	copy: true,
	install : true,
	install_dir : inst_dir)

ui_ttl = configure_file(
	input : 'notes_ui.ttl',
	output : 'notes_ui.ttl',
	copy: true,
	install : true,
	install_dir : inst_dir)

clipboard_png = configure_file(
	input : join_paths('png', 'clipboard.png'),
	output : 'clipboard.png',
	copy: true,
	install : true,
	install_dir : inst_dir)
copy_png = configure_file(
	input : join_paths('png', 'copy.png'),
	output : 'copy.png',
	copy: true,
	install : true,
	install_dir : inst_dir)
delete_png = configure_file(
	input : join_paths('png', 'delete.png'),
	output : 'delete.png',
	copy: true,
	install : true,
	install_dir : inst_dir)
eye_off_png = configure_file(
	input : join_paths('png', 'eye-off.png'),
	output : 'eye-off.png',
	copy: true,
	install : true,
	install_dir : inst_dir)
eye_png = configure_file(
	input : join_paths('png', 'eye.png'),
	output : 'eye.png',
	copy: true,
	install : true,
	install_dir : inst_dir)
save_png = configure_file(
	input : join_paths('png', 'save.png'),
	output : 'save.png',
	copy: true,
	install : true,
	install_dir : inst_dir)

if not use_fontconfig.enabled()
	fira_sans_bold_ttf = d2tk.get_variable('fira_sans_bold_ttf')
	fira_code_bold_ttf = d2tk.get_variable('fira_code_bold_ttf')
	fira_code_light_ttf = d2tk.get_variable('fira_code_light_ttf')
	fira_code_medium_ttf = d2tk.get_variable('fira_code_medium_ttf')
	fira_code_regular_ttf = d2tk.get_variable('fira_code_regular_ttf')

	configure_file(
		input : fira_sans_bold_ttf,
		output : 'FiraSans:bold.ttf',
		copy : true,
		install : true,
		install_dir : inst_dir)
	configure_file(
		input : fira_code_bold_ttf,
		output : 'FiraCode:bold.ttf',
		copy : true,
		install : true,
		install_dir : inst_dir)
	configure_file(
		input : fira_code_light_ttf,
		output : 'FiraCode:light.ttf',
		copy : true,
		install : true,
		install_dir : inst_dir)
	configure_file(
		input : fira_code_medium_ttf,
		output : 'FiraCode:medium.ttf',
		copy : true,
		install : true,
		install_dir : inst_dir)
	configure_file(
		input : fira_code_regular_ttf,
		output : 'FiraCode:regular.ttf',
		copy : true,
		install : true,
		install_dir : inst_dir)
endif

if build_tests
	if lv2_validate.found() and sord_validate.found()
		test('LV2 validate', lv2_validate,
			args : [manifest_ttl, dsp_ttl, ui_ttl])
	endif

	if lv2lint.found()
		test('LV2 lint', lv2lint,
			args : ['-M', 'pack',
				'-E', 'warn',
				'-I', join_paths(build_root, ''),
				'http://open-music-kontrollers.ch/lv2/notes#notes'
			])
	endif
endif
