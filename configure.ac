AC_PREREQ(2.60)
m4_include([m4/version.m4])
AC_INIT(memcachetest, VERSION_NUMBER, ingenthr@cep.net)
AC_CONFIG_SRCDIR([main.c])
AC_CONFIG_AUX_DIR(config)

# Preserve what the user specified as CFLAGS/CXXFLAGS/CPPFLAGS/LDFLAGS
SAVE_CFLAGS="$CFLAGS"
SAVE_CXXFLAGS="$CXXFLAGS"
SAVE_CPPFLAGS="$CPPFLAGS"
SAVE_LDFLAGS="$LDFLAGS"

# The stupid automake system thinks it is a good idea to inject
# -g -O2 to C(XX)FLAGS if it isn't set..
# Why I don't like this? Well -g is in conflict with -g3 that I'm
# adding for Sun Studio
AS_IF([test "x$CFLAGS" = "x" ],
      [CFLAGS="-I."])
AS_IF([test "x$CXXFLAGS" = "x" ],
      [CXXFLAGS="-I."])
AS_IF([test "x$CPPFLAGS" = "x" ],
      [CPPFLAGS="-I."])

AC_USE_SYSTEM_EXTENSIONS
AM_INIT_AUTOMAKE(subdir-objects foreign)
AC_PROG_CXX
AC_PROG_CC
AM_PROG_CC_C_O
AC_PROG_LN_S
AM_CONFIG_HEADER([config.h])
AC_CONFIG_MACRO_DIR([m4])

# Restore CFLAGS/CXXFLAGS now that I've prevented automake
# to inject the extra options ;)
AS_IF([test "x$CFLAGS" = "x-I." ],
      [CFLAGS=""])
AS_IF([test "x$CXXFLAGS" = "x-I." ],
      [CXXFLAGS=""])
AS_IF([test "x$CPPFLAGS" = "x-I." ],
      [CPPFLAGS=""])

# the malloc tests seems to be broken for cross compilation.. ignore them
ac_cv_func_malloc_0_nonnull=yes
ac_cv_func_realloc_0_nonnull=yes

COUCHBASE_GENERIC_COMPILER(c99)

AC_ARG_ENABLE([m32],
    [AS_HELP_STRING([--enable-m32],
                    [Pass -m32 to the compiler and linker @<:@default=off@:>@])],
    [ac_cv_m32="$enableval"])
AC_ARG_ENABLE([m64],
    [AS_HELP_STRING([--enable-m64],
                    [Pass -m64 to the compiler and linker @<:@default=off@:>@])],
    [ac_cv_m64="$enableval"])

AS_IF([test "x$ac_cv_m32" = "xyes"],
      [ AM_CPPFLAGS="$AM_CPPFLAGS -m32"
        AM_LDFLAGS="$AM_LDFLAGS -m32"
        CPPFLAGS="$CPPFLAGS -m32"
        LDFLAGS="$LDFLAGS -m32"
        AS_IF([test "x$ac_cv_m64" = "xyes"],
              [ AC_MSG_ERROR([You can't specify -m32 and -m64. Pick one of them]) ]) ],
      [ AS_IF([test "x$ac_cv_m64" = "xyes"],
              [ AM_CPPFLAGS="$AM_CPPFLAGS -m64"
                AM_LDFLAGS="$AM_LDFLAGS -m64"
                CPPFLAGS="$CPPFLAGS -m64"
                LDFLAGS="$LDFLAGS -m64"
              ]) ])

AC_SEARCH_LIBS(socket, socket)
AC_SEARCH_LIBS(gethostbyname, nsl)
AC_SEARCH_LIBS(log, m)
AC_SEARCH_LIBS(sqrt, m)
AC_SEARCH_LIBS(pthread_create, pthread)
AC_SEARCH_LIBS(clock_gettime, rt)

AC_CHECK_HEADERS_ONCE([mach/mach_time.h memcached/protocol_binary.h
                       libvbucket/vbucket.h libmemcached/memcached.h
                       libcouchbase/couchbase.h])
AC_CHECK_FUNCS_ONCE(gethrtime clock_gettime gettimeofday)


saved_LIBS="$LIBS"
LIBS="$LIBS -lmemcached"
AC_CACHE_CHECK([for libmemcached],[ac_cv_have_libmemcached],[
   AC_TRY_LINK([
      #include <libmemcached/memcached.h>
       ],[
      memcached_lib_version();
       ], [
          ac_cv_have_libmemcached=yes
       ], [
          ac_cv_have_libmemcached=no
       ])
   ])

AS_IF([test "x$ac_cv_have_libmemcached" = "xyes"],
      [ AC_DEFINE([HAVE_LIBMEMCACHED], [1], [Have libmemcached]) ],
      [ LIBS="$saved_LIBS" ])

saved_LIBS="$LIBS"
LIBS="$LIBS -lvbucket"
AC_CACHE_CHECK([for libvbucket],[ac_cv_have_libvbucket],[
   AC_TRY_LINK([
      #include <libvbucket/vbucket.h>
       ],[
      VBUCKET_CONFIG_HANDLE config = vbucket_config_parse_file(NULL);
       ], [
          ac_cv_have_libvbucket=yes
       ], [
          ac_cv_have_libvbucket=no
       ])
   ])

AS_IF([test "x$ac_cv_have_libvbucket" = "xyes"],
      [ AC_DEFINE([HAVE_LIBVBUCKET], [1], [Have libvbucket]) ],
      [ LIBS="$saved_LIBS" ])


saved_LIBS="$LIBS"
LIBS="$LIBS -lcouchbase"
AC_CACHE_CHECK([for libcouchbase],[ac_cv_have_libcouchbase],[
   AC_TRY_LINK([
      #include <sys/types.h>
      #include <libcouchbase/couchbase.h>
       ],[
      libcouchbase_destroy(NULL);
       ], [
          ac_cv_have_libcouchbase=yes
       ], [
          ac_cv_have_libcouchbase=no
       ])
   ])

AS_IF([test "x$ac_cv_have_libcouchbase" = "xyes"],
      [ AC_DEFINE([HAVE_LIBCOUCHBASE], [1], [Have libcouchbase]) ],
      [ LIBS="$saved_LIBS" ])

AH_TOP([
#ifndef CONFIG_H
#define CONFIG_H

#ifdef _SYS_FEATURE_TESTS_H
#error "You should include config.h as your first include file"
#endif
])

AH_BOTTOM(
#ifndef HAVE_GETHRTIME
#include <stdint.h>
typedef uint64_t hrtime_t;
extern hrtime_t gethrtime(void);
#endif
#endif
)

dnl restore CFLAGS, CPPFLAGS, CXXFLAGS and LDFLAGS
CFLAGS="$SAVE_CFLAGS"
CXXFLAGS="$SAVE_CXXFLAGS"
CPPFLAGS="$SAVE_CPPFLAGS"
LDFLAGS="$SAVE_LDFLAGS"

dnl ----------------------------------------------------------------------------
AC_CONFIG_FILES(Makefile)
AC_OUTPUT
