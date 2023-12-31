dnl 
dnl Copyright (c) 2004, Stefan Walter
dnl All rights reserved.
dnl 
dnl Redistribution and use in source and binary forms, with or without 
dnl modification, are permitted provided that the following conditions 
dnl are met:
dnl 
dnl     * Redistributions of source code must retain the above 
dnl       copyright notice, this list of conditions and the 
dnl       following disclaimer.
dnl     * Redistributions in binary form must reproduce the 
dnl       above copyright notice, this list of conditions and 
dnl       the following disclaimer in the documentation and/or 
dnl       other materials provided with the distribution.
dnl     * The names of contributors to this software may not be 
dnl       used to endorse or promote products derived from this 
dnl       software without specific prior written permission.
dnl 
dnl THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS 
dnl "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT 
dnl LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS 
dnl FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE 
dnl COPYRIGHT OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, 
dnl INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, 
dnl BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS 
dnl OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED 
dnl AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, 
dnl OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF 
dnl THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH 
dnl DAMAGE.
dnl 
dnl
dnl CONTRIBUTORS
dnl  Stef Walter <stef@memberwebs.com>
dnl 
 
dnl Process this file with autoconf to produce a configure script.
AC_INIT(clamsmtp, 1.10, stef@memberwebs.com)
AM_INIT_AUTOMAKE(clamsmtp, 1.10)

LDFLAGS="$LDFLAGS -L/usr/local/lib"
CFLAGS="$CFLAGS -I/usr/local/include"

AC_CONFIG_SRCDIR([src/clamsmtpd.c])
AM_CONFIG_HEADER([config.h])

# Checks for programs.
AC_PROG_CC
AC_PROG_INSTALL
AC_PROG_LN_S
AC_PROG_MAKE_SET

# Debug mode
AC_ARG_ENABLE(debug, 
        AC_HELP_STRING([--enable-debug],
        [Compile binaries in debug mode]))

if test "$enable_debug" = "yes"; then
  CFLAGS="$CFLAGS -g -O0 -Wall"
  AC_DEFINE_UNQUOTED(_DEBUG, 1, [In debug mode])
  echo "enabling debug compile mode"
fi

# TODO: Figure out why we need this wierd hack
ACX_PTHREAD( , [echo "ERROR: Pthread support not found."; exit 1] )

LIBS="$PTHREAD_LIBS $LIBS"
CFLAGS="$CFLAGS $PTHREAD_CFLAGS -D_POSIX_PTHREAD_SEMANTICS"

# Some checks for Solaris
AC_CHECK_LIB(socket, getsockname)
AC_CHECK_LIB(nsl, getaddrinfo)

# Checks for header files.
AC_HEADER_STDC 
AC_CHECK_HEADERS([limits.h err.h paths.h],,)
AC_CHECK_HEADERS([unistd.h stdio.h stddef.h fcntl.h stdlib.h assert.h errno.h stdarg.h string.h netdb.h], ,
    [echo "ERROR: Required C header missing"; exit 1])

# Check for linux type transparent proxy support
AC_CHECK_HEADERS([linux/netfilter_ipv4.h],
   AC_DEFINE(LINUX_TRANSPARENT_PROXY, 1, [Whether the system supports a linux type transparent proxy]),
   , 
   [[
   #ifdef HAVE_LIMITS_H
   #include <limits.h>
   #endif
   ]] )

# Checks for typedefs, structures, and compiler characteristics.
AC_C_CONST
AC_TYPE_SIZE_T

# We use error checking mutexes whenever possible
AC_CHECK_DECL(PTHREAD_MUTEX_ERRORCHECK_NP, [AC_DEFINE(HAVE_ERR_MUTEX, 1, "Error Mutex Type")], 
    [AC_CHECK_DECL(PTHREAD_MUTEX_ERRORCHECK, [AC_DEFINE(HAVE_ERR_MUTEX, 2)], ,
    [ #include <pthread.h> ])], [ #include <pthread.h> ])

# Required Variables
AC_CHECK_MEMBER(struct tm.tm_gmtoff,
    [AC_DEFINE(HAVE_TM_GMTOFF, 1, "Time Zone GMT Offset")],
    ,[ #include <time.h> ])
AC_CHECK_GLOBAL(__argv)

# Required Functions
AC_CHECK_FUNCS([memset strerror malloc realloc getopt strchr tolower getaddrinfo], , 
           [echo "ERROR: Required function missing"; exit 1])
AC_CHECK_FUNCS([strlwr strlcat strlcpy strncat strncpy strcasestr setenv daemon])
AC_CHECK_FUNCS([getline getdelim flockfile])

# DMALLOC memory debugging
AC_ARG_ENABLE(dmalloc, 
        AC_HELP_STRING([--enable-dmalloc],
        [malloc memory debugging (default: no)]))

if test "$enable_dmalloc" = "yes"; then

    AC_CHECK_LIB(dmalloc, malloc, , [echo "DMalloc library not installed"; exit 1])

    AC_DEFINE_UNQUOTED(WITH_DMALLOC, 1, [malloc memory debugging])
    LIBS="${LIBS} -ldmalloc"
    echo "enabling dmalloc memory debugging"

fi

# Have to resolve this for the path below
if test "${prefix}" = "NONE"; then
    prefix=$ac_default_prefix
fi

AC_DEFINE_UNQUOTED(CONF_PREFIX, "`eval echo ${sysconfdir}`", [Installation Prefix] )

AC_CONFIG_FILES([Makefile src/Makefile doc/Makefile])
AC_OUTPUT
