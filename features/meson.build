cpu = host_machine.cpu()
system = host_machine.system()
bash_machtype = cpu + '-unknown-'+ system
feature_data.set_quoted('BASH_MACHTYPE', bash_machtype)

# voidptr is larger than int
if ptr_size > int_size
    feature_data.set('_more_void_int', 1)
else
    feature_data.set('_more_void_int', 0)
endif

# long is larger than int
if long_size > int_size
    feature_data.set('_more_long_int', 1)
else
    feature_data.set('_more_long_int', 0)
endif

# On some platforms the math functions (e.g., `sin()`) that we need are found in
# libc, on others they are found in libm.
libm_dep = cc.find_library('m', required: false)

# On some platforms `dladdr()` and related functions are found in libc,
# on others they are found in libdl.
libdl_dep = cc.find_library('dl', required: false)

# On some systems (e.g., FreeBSD) `libutil` contains functions we may need
# such as `openpty()`.
libutil_dep = cc.find_library('util', required: false)

# On some platforms `backtrace()` and related functions are found in libc,
# on others they are found in libexecinfo.
libexecinfo_dep = cc.find_library('execinfo', required: false)

# On OpenBSD libiconv is in /usr/local/lib rather than /usr/lib.
libiconv_dep = cc.find_library('iconv', required: false, dirs: ['/usr/lib', '/usr/local/lib'])

# On Cygwin the message catalog functions (e.g., `catopen()`) are in this
# library.
libcatgets_dep = cc.find_library('catgets', required: false)


# It was turned off on Linux but the iffe test was botched, keeping it on here.
feature_data.set('_map_malloc', 1)

feature_data.set10('_hdr_execinfo', cc.has_header('execinfo.h', args: feature_test_args))

openpty_hdrs = ['#include <stdlib.h>']
if cc.has_header('pty.h', args: feature_test_args)
    feature_data.set('_hdr_pty', 1)
    openpty_hdrs += ['#include <pty.h>']
else
    feature_data.set('_hdr_pty', 0)
endif
if cc.has_header('util.h', args: feature_test_args)
    feature_data.set('_hdr_util', 1)
    openpty_hdrs += ['#include <util.h>']
else
    feature_data.set('_hdr_util', 0)
endif

feature_data.set10('_hdr_malloc', cc.has_header('malloc.h', args: feature_test_args))
feature_data.set10('_hdr_paths', cc.has_header('paths.h', args: feature_test_args))
feature_data.set10('_hdr_termios', cc.has_header('termios.h', args: feature_test_args))
feature_data.set10('_hdr_sys_sysmacros', cc.has_header('sys/sysmacros.h', args: feature_test_args))
feature_data.set10('_hdr_utmp', cc.has_header('utmp.h', args: feature_test_args))
feature_data.set10('_hdr_utmpx', cc.has_header('utmpx.h', args: feature_test_args))

feature_data.set10('_lib_sigqueue',
    cc.has_function('sigqueue', prefix: '#include <signal.h>', args: feature_test_args))
feature_data.set10('_lib_tcgetattr',
    cc.has_function('tcgetattr', prefix: '#include <termios.h>', args: feature_test_args))

if not cc.has_function('isnanl', prefix: '#include <math.h>', args: feature_test_args)
    feature_data.set('isnanl', 'isnan')
endif

feature_data.set10('_lib_utimensat',
    cc.has_function('utimensat', prefix: '#include <sys/stat.h>', args: feature_test_args))
feature_data.set10('_lib_utimets',
    cc.has_function('utimets', prefix: '#include <sys/stat.h>', args: feature_test_args))
feature_data.set10('_lib_sysinfo',
    cc.has_function('sysinfo', prefix: '#include <sys/systeminfo.h>', args: feature_test_args))
feature_data.set10('_lib_pipe2',
    cc.has_function('pipe2', prefix: '#include <unistd.h>', args: feature_test_args))

socketpair_shutdown_feature_file = files('socketpair_shutdown.c')
socketpair_shutdown_feature_result = cc.run(
    socketpair_shutdown_feature_file,
    name: 'fchmod() after socketpair() shutdown()',
    args: feature_test_args)
feature_data.set10('_socketpair_shutdown_mode',
    socketpair_shutdown_feature_result.returncode() == 0)

if cc.has_member('struct stat', 'st_mtim', prefix: '#include <sys/stat.h>',
                 args: feature_test_args)
    feature_data.set('STAT_ST_MTIM', 1)
elif cc.has_member('struct stat', 'st_mtimespec', prefix: '#include <sys/stat.h>',
                   args: feature_test_args)
    # Under macOS modification time field is defined as mtimespec
    feature_data.set('STAT_ST_MTIMESPEC', 1)
else
    error('Cannot determine modification time field in struct stat.')
endif

feature_data.set10('_mem_ut_host_utmp',
    cc.has_member('struct utmp', 'ut_host', prefix: '#include <utmp.h>', args: feature_test_args))
feature_data.set10('_mem_ut_tv_utmp',
    cc.has_member('struct utmp', 'ut_tv', prefix: '#include <utmp.h>', args: feature_test_args))
feature_data.set10('_mem_ut_type_utmp',
    cc.has_member('struct utmp', 'ut_type', prefix: '#include <utmp.h>', args: feature_test_args))
feature_data.set10('_mem_ut_user_utmp',
    cc.has_member('struct utmp', 'ut_user', prefix: '#include <utmp.h>', args: feature_test_args))
feature_data.set10('_mem_ut_host_utmpx',
    cc.has_member('struct utmpx', 'ut_host', prefix: '#include <utmpx.h>', args: feature_test_args))
feature_data.set10('_mem_ut_tv_utmpx',
    cc.has_member('struct utmpx', 'ut_tv', prefix: '#include <utmpx.h>', args: feature_test_args))
feature_data.set10('_mem_ut_type_utmxp',
    cc.has_member('struct utmp', 'ut_type', prefix: '#include <utmpx.h>', args: feature_test_args))
feature_data.set10('_mem_ut_user_utmpx',
    cc.has_member('struct utmpx', 'ut_user', prefix: '#include <utmpx.h>', args: feature_test_args))

feature_data.set10('_lib_posix_openpt',
    cc.has_function('posix_openpt', prefix: '#include <stdlib.h>',
                    args: feature_test_args,
                    dependencies: [libutil_dep]))

feature_data.set10('_lib_openpty',
    cc.has_function('openpty', prefix: '\n'.join(openpty_hdrs),
                    args: feature_test_args,
                    dependencies: [libutil_dep]))

feature_data.set10('_lib_ptsname',
    cc.has_function('ptsname', prefix: '#include <stdlib.h>',
                    args: feature_test_args,
                    dependencies: [libutil_dep]))

feature_data.set10('_lib_grantpt',
    cc.has_function('grantpt', prefix: '#include <stdlib.h>',
                    args: feature_test_args,
                    dependencies: [libutil_dep]))

feature_data.set10('_lib_unlockpt',
    cc.has_function('unlockpt', prefix: '#include <stdlib.h>',
                    args: feature_test_args,
                    dependencies: [libutil_dep]))

pty_feature_file = files('pty.c')
pty_feature_result = cc.run(pty_feature_file, name: 'pty dev name check', args: feature_test_args)
if not pty_feature_result.compiled()
    error('Unable to compile the pty_feature.c module to determine pty device names')
endif
foreach line : pty_feature_result.stdout().strip().split('\n')
    feature_data.set(line.split())
endforeach

feature_data.set10('_lib_mallinfo',
    cc.has_function('mallinfo', prefix: '#include <malloc.h>', args: feature_test_args))

feature_data.set10('_lib_mstats',
    cc.has_member('struct mstats', 'bytes_total', prefix : '#include<malloc/malloc.h>',
                  args: feature_test_args))

feature_data.set10('_lib_mallopt',
    cc.has_function('mallopt', prefix: '#include <malloc.h>', args: feature_test_args))
feature_data.set10('_lib_memalign',
    cc.has_function('memalign', prefix: '#include <stdlib.h>', args: feature_test_args))
feature_data.set10('_lib_pvalloc',
    cc.has_function('pvalloc', prefix: '#include <malloc.h>', args: feature_test_args))
feature_data.set10('_lib___libc_malloc',
    cc.has_function('__libc_malloc', prefix: '#include <malloc.h>', args: feature_test_args))

feature_data.set10('_mem_arena_mallinfo',
    cc.has_member('struct mallinfo', 'arena', prefix : '#include<malloc.h>',
                  args: feature_test_args))

sbrk_feature_file = files('sbrk.c')
sbrk_feature_result = cc.run(sbrk_feature_file, name: 'sbrk() works as expected',
                             args: feature_test_args)
feature_data.set10('_mem_sbrk', sbrk_feature_result.returncode() == 0)

# This was defined in iffe tests when '_mmap_anon' is true
# '_mmap_anon' is always true so keeping this macro on here
feature_data.set('_mem_mmap_anon', 1)

stack_direction_feature_file = files('stack_direction.c')
stack_direction_feature_result = cc.run(stack_direction_feature_file, name: 'stack grows downward',
                                        args: feature_test_args)
feature_data.set10('_stk_down', stack_direction_feature_result.returncode() == 0)

syslocale_result = run_command('syslocale.sh')
if syslocale_result.returncode() == 0
    syslocale_output = syslocale_result.stdout().strip()
    feature_data.set('_locale_utf8_str', syslocale_output)
endif

libpath_result = run_command('libpath.sh')
if libpath_result.returncode() == 0
    libpath_output = libpath_result.stdout().strip()
    feature_data.set_quoted('CONF_LIBPATH', libpath_output)
endif

prog_result = run_command('prog.sh')
if prog_result.returncode() == 0
    prog_output = prog_result.stdout().strip()
    feature_data.set('_PROC_PROG', prog_output)
endif

align_feature_file = files('alignment.c')
align_feature_result = cc.run(align_feature_file, name: 'align bound check',
                              args: feature_test_args)
if not align_feature_result.compiled()
    error('Unable to compile the align_feature.c module to determine compiler alignment')
endif
foreach line : align_feature_result.stdout().strip().split('\n')
    feature_data.set(line.split())
endforeach

posix_spawn_feature_file = files('posix_spawn.c')
posix_spawn_feature_result = cc.run(posix_spawn_feature_file,
                                    name: 'posix_spawn exists and it works and its worth using',
                                    args: feature_test_args)
feature_data.set10('_lib_posix_spawn', posix_spawn_feature_result.returncode() == 0)

newgrp = find_program('newgrp', required: false)
feature_data.set10('_cmd_newgrp', newgrp.found())

tput = find_program('tput', required: false)
feature_data.set10('_cmd_tput', tput.found())

ed = find_program('ed', required: false)
if ed.found()
    feature_data.set_quoted('_pth_ed', ed.path())
else
    feature_data.set_quoted('_pth_ed', 'ed')
endif

subdir('dll')
subdir('ids')
subdir('locales')
subdir('sockets')
subdir('time')
subdir('utsname')
