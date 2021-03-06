# -*- Autoconf -*-
# Process this file with autoconf to produce a configure script.

AC_PREREQ(2.61)

m4_include([version.m4])

AC_INIT([subconvert],[VERSION_NUMBER],[johnw@boostpro.com])

AC_CONFIG_AUX_DIR([.])
AM_INIT_AUTOMAKE([dist-bzip2 foreign])
AC_CONFIG_MACRO_DIR([m4])

AC_CONFIG_SRCDIR([src/main.cpp])
AC_CONFIG_HEADER([config.h])

# Use AM_SILENT_RULES if present
m4_ifdef([AM_SILENT_RULES], [AM_SILENT_RULES([yes])])

# Checks for programs.
AC_USE_SYSTEM_EXTENSIONS
AC_PROG_CXX
AC_PROG_MAKE_SET
AC_PROG_LIBTOOL
#AM_GNU_GETTEXT
#AM_GNU_GETTEXT_VERSION([0.17])

# Check for options
AC_ARG_ENABLE(debug,
  [  --enable-debug          Turn on debugging],
  [case "${enableval}" in
    yes) debug=true ;;
    no)  debug=false ;;
    *) AC_MSG_ERROR(bad value ${enableval} for --enable-debug) ;;
  esac],[debug=false])

if [ test x$debug = xtrue ]; then
  AC_DEFINE([DEBUG_MODE], [1], [Whether debugging is enabled])
fi
AM_CONDITIONAL(DEBUG, test x$debug = xtrue)

AC_ARG_ENABLE(pch,
  [  --enable-pch            Use GCC 4.x pre-compiled headers],
  [case "${enableval}" in
    yes) pch=true ;;
    no)  pch=false ;;
    *) AC_MSG_ERROR(bad value ${enableval} for --enable-pch) ;;
  esac],[pch=false])

if [ test x$pch = xtrue ]; then
  AC_DEFINE([USE_PCH], [1], [Whether pre-compiled headers are being used])
fi
AM_CONDITIONAL(USE_PCH, test x$pch = xtrue)

AC_ARG_ENABLE(threads,
  [  --enable-threads            Use GCC 4.x pre-compiled headers],
  [case "${enableval}" in
    yes) threads=true ;;
    no)  threads=false ;;
    *) AC_MSG_ERROR(bad value ${enableval} for --enable-threads) ;;
  esac],[threads=false])

if [ test x$threads = xtrue ]; then
  AC_DEFINE([USE_THREADS], [1], [Whether pre-compiled headers are being used])

  AC_ARG_WITH(boost-suffix,
    [  --with-boost-suffix=X   Append X to the Boost library names],
    [BOOST_SUFFIX="${withval}"],
    [BOOST_SUFFIX="-mt"])
else
  AC_ARG_WITH(boost-suffix,
    [  --with-boost-suffix=X   Append X to the Boost library names],
    [BOOST_SUFFIX="${withval}"],
    [BOOST_SUFFIX=""])
fi
AM_CONDITIONAL(USE_THREADS, test x$threads = xtrue)

AC_SUBST([BOOST_SUFFIX], $BOOST_SUFFIX)

AC_ARG_ENABLE(cache,
  [  --enable-cache          Enable use of the Boost.Serialization cache],
  [case "${enableval}" in
    yes) cache=true ;;
    no)  cache=false ;;
    *) AC_MSG_ERROR(bad value ${enableval} for --enable-cache) ;;
  esac],[cache=false])

AC_ARG_ENABLE(doxygen,
  [  --enable-doxygen        Turns on generation of code documentation],
  [case "${enableval}" in
    yes) doxygen=true ;;
    no)  doxygen=false ;;
    *) AC_MSG_ERROR(bad value ${enableval} for --enable-doxygen) ;;
  esac],[doxygen=false])

AM_CONDITIONAL(USE_DOXYGEN, test x$doxygen = xtrue)

# check for boost_filesystem
AC_CACHE_CHECK(
  [if boost_filesystem is available],
  [boost_filesystem_cpplib_avail_cv_],
  [boost_filesystem_save_libs=$LIBS
   LIBS="-lboost_filesystem$BOOST_SUFFIX -lboost_system$BOOST_SUFFIX $LIBS"
   AC_LANG_PUSH(C++)
   AC_LINK_IFELSE(
     [AC_LANG_PROGRAM(
        [[#include <boost/filesystem/path.hpp>]],
        [[boost::filesystem::path this_path("Hello");]])],
     [boost_filesystem_cpplib_avail_cv_=true],
     [boost_filesystem_cpplib_avail_cv_=false])
   AC_LANG_POP
   LIBS=$boost_filesystem_save_libs])

if [test x$boost_filesystem_cpplib_avail_cv_ = xtrue ]; then
  LIBS="-lboost_filesystem$BOOST_SUFFIX -lboost_system$BOOST_SUFFIX $LIBS"
else
  AC_MSG_FAILURE("Could not find boost_filesystem library (set CPPFLAGS and LDFLAGS?)")
fi

# check for boost_thread
if [ test x$threads = xtrue ]; then
  AC_CACHE_CHECK(
    [if boost_thread is available],
    [boost_thread_cpplib_avail_cv_],
    [boost_thread_save_libs=$LIBS
     LIBS="-lboost_thread$BOOST_SUFFIX $LIBS"
     AC_LANG_PUSH(C++)
     AC_LINK_IFELSE(
       [AC_LANG_PROGRAM(
          [[#include <boost/thread.hpp>
            void f(void) {}]],
          [[boost::thread this_thread(f);]])],
       [boost_thread_cpplib_avail_cv_=true],
       [boost_thread_cpplib_avail_cv_=false])
     AC_LANG_POP
     LIBS=$boost_thread_save_libs])

  if [test x$boost_thread_cpplib_avail_cv_ = xtrue ]; then
    LIBS="-lboost_thread$BOOST_SUFFIX $LIBS"
  else
    AC_MSG_FAILURE("Could not find boost_thread library (set CPPFLAGS and LDFLAGS?)")
  fi
fi

# check for boost_serialization
if [ test x$cache = xtrue ]; then
  AC_CACHE_CHECK(
    [if boost_serialization is available],
    [boost_serialization_cpplib_avail_cv_],
    [boost_serialization_save_libs=$LIBS
     LIBS="-lboost_serialization$BOOST_SUFFIX -lboost_system$BOOST_SUFFIX $LIBS"
     AC_LANG_PUSH(C++)
     AC_LINK_IFELSE(
       [AC_LANG_PROGRAM(
          [[#include <boost/archive/binary_oarchive.hpp>
            #include <iostream>
            struct foo {
              int a;
              template<class Archive>
              void serialize(Archive & ar, const unsigned int) {
                ar & a;
              }
            };]],
          [[boost::archive::binary_oarchive oa(std::cout);
            foo x;
            oa << x;]])],
       [boost_serialization_cpplib_avail_cv_=true],
       [boost_serialization_cpplib_avail_cv_=false])
     AC_LANG_POP
     LIBS=$boost_serialization_save_libs])

  if [test x$boost_serialization_cpplib_avail_cv_ = xtrue -a x$cache = xtrue]; then
    AC_DEFINE([HAVE_BOOST_SERIALIZATION], [1], [Whether Boost.Serialization is available])
    LIBS="-lboost_serialization$BOOST_SUFFIX $LIBS"
  fi
fi

AM_CONDITIONAL(HAVE_BOOST_SERIALIZATION, test x$boost_serialization_cpplib_avail_cv_ = xtrue -a x$cache = xtrue)

# Checks for header files.
AC_HEADER_STDC
AC_HEADER_STAT
AC_CHECK_HEADERS([openssl/md5.h openssl/sha.h])

# Checks for libraries.
AC_CHECK_LIB(crypto, MD5)
AC_CHECK_LIB(crypto, SHA1)
AC_CHECK_LIB(git2, git_oid_fmt)

# Checks for typedefs, structures, and compiler characteristics.
AC_TYPE_SIZE_T
#AC_STRUCT_TM

# Checks for library functions.
#AC_FUNC_MKTIME
#AC_FUNC_STAT
#AC_FUNC_STRFTIME
#AC_CHECK_FUNCS([access isatty])

# Pepare the Makefiles
AC_CONFIG_FILES([Makefile])
AC_OUTPUT
