AC_PREREQ(2.60)

dnl initialize autoconf
dnl when going to/from release please set the nano (fourth number) right !
dnl releases only do Wall, cvs and prerelease does Werror too
AC_INIT(GStreamer GObject Introspectin tests for Python , 0.11.92,
    http://bugzilla.gnome.org/enter_bug.cgi?product=GStreamer,
    gst-python)

AG_GST_INIT

dnl initialize automake
AM_INIT_AUTOMAKE([-Wno-portability 1.10])

dnl define PACKAGE_VERSION_* variables
AS_VERSION

dnl check if this is a release version
AS_NANO(GST_CVS="no", GST_CVS="yes")

dnl can autoconf find the source ?
AC_CONFIG_SRCDIR([testsuite/common.py])

dnl define the output header for config
AM_CONFIG_HEADER([config.h])

dnl AM_MAINTAINER_MODE only provides the option to configure to enable it
AM_MAINTAINER_MODE

dnl use pretty build output with automake >= 1.11
m4_ifdef([AM_SILENT_RULES],[AM_SILENT_RULES([yes])],
  [AM_DEFAULT_VERBOSITY=1
   AC_SUBST(AM_DEFAULT_VERBOSITY)])

dnl Add parameters for aclocal
AC_SUBST(ACLOCAL_AMFLAGS, "-I m4 -I common/m4")

dnl required versions of other packages
dnl Note that they are runtime requirements
AC_SUBST(GST_REQ,   0.11.92)
AC_SUBST(PYGOBJECT_REQ, 3.0)

AC_DISABLE_STATIC

AC_LIBTOOL_WIN32_DLL
AC_PROG_LIBTOOL

dnl find a compiler
AC_PROG_CC
AC_PROG_CC_STDC

dnl check if the compiler supports '-c' and '-o' options
AM_PROG_CC_C_O

dnl check for python
dnl AM_PATH_PYTHON(2.5)
AM_PATH_PYTHON
AC_MSG_CHECKING(for python >= 2.5)
prog="
import sys, string
minver = (2,5,0,'final',0)
if sys.version_info < minver:
  sys.exit(1)
sys.exit(0)"


if $PYTHON -c "$prog" 1>&AC_FD_CC 2>&AC_FD_CC
then
  AC_MSG_RESULT(okay)
else
  AC_MSG_ERROR(too old)
fi
AM_CHECK_PYTHON_HEADERS(,[AC_MSG_ERROR(could not find Python headers)])
AS_AC_EXPAND(PYTHONDIR, $pythondir)
AS_AC_EXPAND(PYEXECDIR, $pyexecdir)

AC_ARG_WITH([pygi_overrides_dir],
            AC_HELP_STRING([--with-pygi-overrides-dir], [Path to pygobject overrides directory]))

AC_MSG_CHECKING(for pygobject overrides directory)
if test "x$with_pygi_overrides_dir" = "x" ; then
        overrides_dir="`$PYTHON -c 'import gi; print(gi._overridesdir)' 2>/dev/null`"
    # fallback if the previous failed
    if test "x$overrides_dir" = "x" ; then
        overrides_dir="${pyexecdir}/gi/overrides"
    fi
else
        overrides_dir="$with_pygi_overrides_dir"
fi

PYGI_OVERRIDES_DIR="$overrides_dir"
AC_SUBST(PYGI_OVERRIDES_DIR)
AC_MSG_RESULT($PYGI_OVERRIDES_DIR)

dnl check for GStreamer
GST_API_VERSION=1.0
AC_SUBST(GST_API_VERSION)
PKG_CHECK_MODULES(GST, gstreamer-$GST_API_VERSION >= $GST_REQ)
AC_DEFINE_UNQUOTED(GST_API_VERSION, "$GST_API_VERSION", [Gst API version])
GST_CFLAGS="$GST_CFLAGS $GLIB_EXTRA_CFLAGS"
AC_SUBST(GST_CFLAGS)
AC_SUBST(GST_LIBS)

dnl check for pygobject
PKG_CHECK_MODULES(PYGOBJECT, pygobject-3.0 >= $PYGOBJECT_REQ,
  [
    HAVE_PYGOBJECT="yes"
  ], HAVE_PYGOBJECT="no")

AC_SUBST(PYGOBJECT_CFLAGS)

dnl and set the override directory
AC_ARG_WITH([pygi_overrides_dir],
            AC_HELP_STRING([--with-pygi-overrides-dir], [Path to pygobject overrides directory]))

AG_GST_VALGRIND_CHECK

dnl set release date/time
#AG_GST_SET_PACKAGE_RELEASE_DATETIME_WITH_NANO([$PACKAGE_VERSION_NANO],
  #["${srcdir}/gst-python.doap"],
  #[$PACKAGE_VERSION_MAJOR.$PACKAGE_VERSION_MINOR.$PACKAGE_VERSION_MICRO])

AC_OUTPUT([
  Makefile
  common/Makefile
  common/m4/Makefile
  examples/Makefile
  testsuite/Makefile
  gi/Makefile
  gi/overrides/Makefile
])
