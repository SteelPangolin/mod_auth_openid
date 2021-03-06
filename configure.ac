AC_INIT([mod_auth_openid], [0.9], [bamuller@gmail.com])
AC_DEFINE([PACKAGE_URL],["http://findingscience.com/mod_auth_openid"],[project url])
AM_CONFIG_HEADER(src/config.h)
AM_INIT_AUTOMAKE()

AC_CONFIG_MACRO_DIR([acinclude.d])

AC_PROG_CXX
AC_PROG_CXXCPP
AC_LANG_CPLUSPLUS
AC_CANONICAL_HOST
AC_PROG_INSTALL
AM_PROG_LIBTOOL

AC_HEADER_STDC

# provide flag --enable-debug
AC_ARG_ENABLE([debug],
    [  --enable-debug    Enable debugging output to Apache error log],
    [case "${enableval}" in
        yes) debug=true ;;
        no)  debug=false ;;
        *) AC_MSG_ERROR([bad value ${enableval} for --enable-debug]) ;;
     esac],[debug=false])
AM_CONDITIONAL([DEBUG], [test x$debug = xtrue])

# this will look for apxs command - put it in $APXS, fail on failure
AX_WITH_APXS()


# begin APR config

# find apr-config binary
AC_ARG_WITH(apr_config, AC_HELP_STRING([[--with-apr-config=FILE]], [Path to apr-config program]),
			[ apr_config="$withval" ],
			[AC_PATH_PROGS(apr_config,
				[apr-config apr-0-config apr-1-config], 
				[no], 
				[$PATH:/usr/sbin/:/usr/local/apache2/bin]
			)]
)

if test "$apr_config" = "no"; then
   AC_MSG_ERROR(Could not find the apr-config program.  You can specify a location with the --with-apr-config=FILE option.  It may be named apr-0-config or apr-1-config and can be found in your apache2 bin directory.)
fi

$apr_config --cppflags &> /dev/null
if test "$?" != "0"; then
  AC_MSG_ERROR($apr_config is not a valid apr-config program)
fi

APR_LDFLAGS="`${apr_config} --link-ld --libs`"
AC_SUBST(APR_LDFLAGS)

# end APR config


# begin APR util config

# find apu-config binary
AC_ARG_WITH(apu_config, AC_HELP_STRING([[--with-apu-config=FILE]], [Path to apu-config program]),
      [ apu_config="$withval" ],
      [AC_PATH_PROGS(apu_config,
        [apu-config apu-0-config apu-1-config], 
        [no], 
        [$PATH:/usr/sbin/:/usr/local/apache2/bin]
      )]
)

if test "$apu_config" = "no"; then
   AC_MSG_ERROR(Could not find the apu-config program.  You can specify a location with the --with-apu-config=FILE option.  It may be named apu-0-config or apu-1-config and can be found in your apache2 bin directory.)
fi

$apu_config --link-ld --libs &> /dev/null
if test "$?" != "0"; then
  AC_MSG_ERROR($apu_config is not a valid apu-config program)
fi

APU_LDFLAGS="`${apu_config} --link-ld --libs`"
AC_SUBST(APU_LDFLAGS)

# end APR util config


APACHE_CFLAGS="-I`${APXS} -q INCLUDEDIR` -I`${apr_config} --includedir`"
AC_SUBST(APACHE_CFLAGS)

PKG_CHECK_MODULES([OPKELE],[libopkele >= 2.0],,[
 AC_MSG_ERROR([no libopkele library found (version 2.0 or higher). get one from http://kin.klever.net/libopkele/])
])

# These next few are prerequisites for libopekele, but it's possible they've been removed since that install....
# Check for pcre
AX_PATH_LIB_PCRE([], [ AC_MSG_ERROR([No pcre library found.  You can get it at http://www.pcre.org]) ])
AC_SUBST(PCRE_LIBS)
AC_SUBST(PCRE_CFLAGS)

# Check for curl
AC_CHECK_CURL([7], [], [ AC_MSG_ERROR([No curl library found.  You can get it at http://curl.haxx.se]) ]) 
AC_SUBST(CURL_CFLAGS)
AC_SUBST(CURL_LIBS)

# Check for CppUnit
AM_PATH_CPPUNIT([], [], [ AC_MSG_WARN([No CppUnit library found: tests will not be buildable. You can get it at http://cppunit.sourceforge.net/]) ]) 
AC_SUBST(CPPUNIT_CFLAGS)
AC_SUBST(CPPUNIT_LIBS)

# Idea taken from libopekele
nitpick=false
AC_ARG_ENABLE([nitpicking],
 AC_HELP_STRING([--enable-nitpicking],[make compiler warn about possible problems]),
 [ test "$enableval" = "no" || nitpick=true ]
)
AM_CONDITIONAL(NITPICK, test x$nitpick = xtrue)


# Enable code coverage instrumentation
coverage=false
AC_ARG_ENABLE([coverage],
 AC_HELP_STRING([--enable-coverage],[instrument files for gcov code coverage tool]),
 [ test "$enableval" = "no" || coverage=true ]
)
AM_CONDITIONAL(COVERAGE, test x$coverage = xtrue)


AC_CONFIG_FILES([
 Makefile
 src/Makefile
])
AC_OUTPUT

echo " ***"
echo " *** You are now ready to build mod_auth_openid:"
echo " *** Enter the following commands:"
echo " ***"
echo " *** $> make"
echo " *** $> sudo make install"
echo " ***"
echo " *** Report bugs at http://github.com/bmuller/mod_auth_openid/issues"
echo " *** Thanks for using free (as in speech and beer) software."
echo " ***"
echo
