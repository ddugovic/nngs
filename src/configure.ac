dnl Process this file with autoconf to produce a configure script.
dnl this defines VERSION and PACKAGE
AC_INIT([nngs],[1.1.22],[dandydand@gmail.com])

dnl this is to determine if the config script is running in the proper place
dnl just give it one file relative to where it should be
AC_CONFIG_SRCDIR([nngsmain.c])

AM_CONFIG_HEADER(config.h)
AC_PREREQ([2.69])dnl               dnl Minimum Autoconf version required.

AM_INIT_AUTOMAKE
dnl GNU_PACKAGE="GNU $PACKAGE"
dnl AC_DEFINE_UNQUOTED(GNU_PACKAGE, "$GNU_PACKAGE")
AC_DEFINE(GNU_PACKAGE, nngs, [server program for playing the game of go (baduk,weiqi) over the internet])

AM_MAINTAINER_MODE

AC_PROG_CC
AC_PROG_CXX
AC_EXEEXT

dnl add -lm to library list since we use some
dnl math functions such as pow and fabs

dnl AC_SEARCH_LIBS(pow,m)
dnl AC_SEARCH_LIBS(bind,socket)
dnl AC_SEARCH_LIBS(crypt,crypt)
AC_CHECK_LIB(m,pow)
AC_CHECK_LIB(socket,bind)
AC_CHECK_LIB(xcrypt,crypt)

if test "x$ac_cv_mingw32" = "xyes";then
    LIBS="$LIBS -lwsock32"
fi

AC_PROG_CPP
AC_PROG_GCC_TRADITIONAL
AC_CONFIG_MACRO_DIRS([m4])
LT_INIT

dnl required since we use SUBDIRS in Makefile.am
AC_PROG_MAKE_SET

AC_HEADER_MAJOR
AC_HEADER_TIME

AC_C_CONST

AC_CHECK_HEADERS(unistd.h crypt.h term.h fcntl.h dirent.h ctype.h \
stdio.h stdlib.h string.h \
utime.h time.h errno.h strings.h signal.h endian.h \
sys/time.h sys/stat.h sys/socket.h \
arpa/inet.h arpa/telnet.h \
netinet/in.h netdb.h \
gdbm.h ndbm.h)

AC_CHECK_SIZEOF(int,4)
AC_CHECK_SIZEOF(long,4)

AC_CHECK_TYPE(ulong,unsigned long)

dnl results of setlinebuf test used in interface/play_ascii.c
dnl alarm not available in VC
dnl snprintf vsnprintf not universally available
dnl usleep not available in Unicos and mingw32
dnl random,popen,ftruncate should be in stdlib
AC_CHECK_FUNCS(vsnprintf snprintf gettimeofday usleep \
 random popen pclose ftruncate)

dnl  ----------- special-case use of gcc ---------

dnl Not sure if we are supposed to be accessing this variable, but...

AC_SUBST(GCC_ONLY)
AC_SUBST(NNGS_SOME_WARNINGS)
AC_SUBST(NNGS_ALL_WARNINGS)
AC_SUBST(NO_WARNINGS)

dnl Please add -Wp,-lang-c89 to SOME_WARNINGS soon

NO_WARNINGS="CFLAGS=$CFLAGS"
if test $ac_cv_c_compiler_gnu = yes; then
  GCC_ONLY=''
  GGO_SOME_WARNINGS='-Wall -W -Wsign-compare -Wpointer-arith -Wbad-function-cast -Wcast-qual -Wcast-align -Wwrite-strings -Wstrict-prototypes -Wmissing-prototypes -Wmissing-declarations -Wp,-std=c89'
  GGO_ALL_WARNINGS='-Wall -W -Wsign-compare -Wshadow -Wpointer-arith -Wbad-function-cast -Wcast-qual -Wcast-align -Wwrite-strings -Wstrict-prototypes -Wmissing-prototypes -Wmissing-declarations -Wp,-std=c89'
  NNGS_SOME_WARNINGS="CFLAGS=$CFLAGS $GGO_SOME_WARNINGS"
  NNGS_ALL_WARNINGS="CFLAGS=$CFLAGS $GGO_ALL_WARNINGS"
  CFLAGS="$CFLAGS $GGO_SOME_WARNINGS"
else
  GCC_ONLY='#'
  NNGS_SOME_WARNINGS=''
  NNGS_ALL_WARNINGS=''
fi

dnl Now lines in Makefile.am can be prefixed @GCC_ONLY@, and
dnl we can specify either @NNGS_SOME_WARNINGS@ or @NNGS_ALL_WARNINGS@

dnl FIXME POST3.0: please add warnings for other compilers !


dnl #AM_GNU_GETTEXT
dnl #AC_LINK_FILES($nls_cv_header_libgt, $nls_cv_header_intl)

dnl hack to find the mail program(s) - may need some extra checking for
dnl different OS's/distributions. Debian symlinks mailx & Mail to 
dnl /usr/bin/mail. Defaults to the first one found.

AC_PATH_PROGS(MAILPROGRAM, mail mailx Mail)
AC_DEFINE_UNQUOTED(MAILPROGRAM, "$MAILPROGRAM", [what we use to send mail])

dnl do we want UDP-socket?
dnl use this for general ifdef stuff if wanted.
dnl AC_DEFINE_UNQUOTED(WANT_UDP,1, [UDP pipo] )
dnl AC_MSG_CHECKING([whether UDP-socket is requested])
dnl AC_ARG_ENABLE(udp-socket,
	dnl [  --enable-udp-socket     Use UDP-socket [default=no]],
	dnl if test x$enableval = "xyes"; then
	dnl AC_MSG_RESULT([yes])
	dnl AC_DEFINE(WANT_UDP)
	dnl else
	dnl AC_MSG_RESULT([no])
	dnl fi,
	dnl AC_MSG_RESULT([no])
	dnl )

dnl <Joy> CQ: do stuff like AC_DEFINE_UNQUOTED(MY_VARIABLE,"$something")
dnl <Joy> CQ: oh yeah i think you need to #undef it in acconfig.h or something
dnl <luca> joy cq: AC_DEFINE(CQ_VARIABLE) in configure.in and one of: #undef
dnl    CQ_VARIABLE in acconfig.h or use autoheader

AC_DEFINE_UNQUOTED(AHELP_DIR,"$prefix/${PACKAGE}/ahelp", [admin help file dir])
AC_DEFINE_UNQUOTED(HELP_DIR,"$prefix/${PACKAGE}/help", [help files directory])
AC_DEFINE_UNQUOTED(INFO_DIR,"$prefix/${PACKAGE}/info", [info directory])
AC_DEFINE_UNQUOTED(MESSAGE_DIR,"$prefix/${PACKAGE}/messages", [message directory])
AC_DEFINE_UNQUOTED(PLAYER_DIR,"$prefix/${PACKAGE}/players", [player dirs])
AC_DEFINE_UNQUOTED(STATS_DIR,"$prefix/${PACKAGE}/stats", [message dir])
AC_DEFINE_UNQUOTED(SPOOL_DIR,"$prefix/${PACKAGE}/spool", [spool dir])

AC_DEFINE_UNQUOTED(GAME_DIR,"$prefix/${PACKAGE}/games", [games dir])
AC_DEFINE_UNQUOTED(CGAME_DIR,"$prefix/${PACKAGE}/cgames", [cgames dir])
AC_DEFINE_UNQUOTED(LIST_DIR,"$prefix/${PACKAGE}/lists", [list definition dir])
AC_DEFINE_UNQUOTED(NEWS_DIR,"$prefix/${PACKAGE}/news", [news files dir])
AC_DEFINE_UNQUOTED(PROBLEM_DIR,"$prefix/${PACKAGE}/problems", [go progblem dir])

AC_DEFINE_UNQUOTED(LADDER9_FILE,"$prefix/${PACKAGE}/ladder/ladder9", [9x9 ladder file])
AC_DEFINE_UNQUOTED(LADDER19_FILE,"$prefix/${PACKAGE}/ladder/ladder19", [19x19 ladder file])

AC_DEFINE_UNQUOTED(EMOTES_FILE,"$prefix/${PACKAGE}/emotestr", [emoticons file])
AC_DEFINE_UNQUOTED(INTERGO_FILE,"$prefix/${PACKAGE}/intergo/intergo",
                    [intergo executable from http://www.cwi.nl/~jansteen/intergo/])

dnl
dnl Files below this line are automatically generated by the server
dnl and do not need to be created.
dnl
AC_DEFINE_UNQUOTED(RATINGS_FILE,"$prefix/${PACKAGE}/results-rated", [file])
AC_DEFINE_UNQUOTED(NRATINGS_FILE,"$prefix/${PACKAGE}/ratdb", [file])
AC_DEFINE_UNQUOTED(RESULTS_FILE,"$prefix/${PACKAGE}/results", [file])
AC_DEFINE_UNQUOTED(NRESULTS_FILE,"$prefix/${PACKAGE}/nresults", [file])
AC_DEFINE_UNQUOTED(FIND_FILE,"$prefix/${PACKAGE}/find.out", [find file])
AC_DEFINE_UNQUOTED(NOTE_FILE,"$prefix/${PACKAGE}/note", [notes file])

AC_DEFINE_UNQUOTED(LOG_FILE,"$prefix/${PACKAGE}/stats/logfile", [logfile])
AC_DEFINE_UNQUOTED(LOGONS_FILE,"$prefix/${PACKAGE}/stats/logons", [logonsfile])

dnl
dnl files found in some of the above directories
dnl
AC_DEFINE_UNQUOTED(MESS_WELCOME,"welcome", [welcome message file])
AC_DEFINE_UNQUOTED(MESS_LOGIN,"login", [login message file])
AC_DEFINE_UNQUOTED(MESS_LOGOUT,"logout", [logout message file])
AC_DEFINE_UNQUOTED(MESS_MOTD,"motd", [motd message file])
AC_DEFINE_UNQUOTED(MESS_AMOTD,"admotd", [admin motd message file])
AC_DEFINE_UNQUOTED(MESS_UNREGISTERED,"unregistered", [unreg message file])

dnl
dnl define this to use PEM's malloc and smalloc routines
dnl
dnl AC_DEFINE_UNQUOTED(USE_PEM_MALLOC,, [define this to use pem's malloc and salloc routines])

dnl dmalloc debugging

dnl AC_MSG_CHECKING([whether dmalloc debugging is requested])
dnl AC_ARG_ENABLE(dmalloc,
dnl   [  --enable-dmalloc         Enable dmalloc debugging [default=no]],
dnl   if test x$enableval = "xyes"; then
dnl       AC_MSG_RESULT([yes])
dnl       AC_DEFINE(USING_DMALLOC)
dnl 	  DMALLOCLIB="-ldmalloc"
dnl   else
dnl       AC_MSG_RESULT([no])
dnl 	  DMALLOCLIB=""
dnl   fi,
dnl   AC_MSG_RESULT([no])
dnl )
dnl AC_SUBST(DMALLOCLIB)
dnl LIBS="$LIBS $DMALLOCLIB"

AC_SUBST(LIBS)

dnl
dnl this must be the last line of configure.in
dnl
AC_CONFIG_FILES([Makefile])
AC_OUTPUT

