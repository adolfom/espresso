# Copyright (C) 2006,2007,2008,2009,2010,2011 Olaf Lenz, Axel Arnold
# Copyright (C) 2008,2009,2010 Max-Planck-Institute for Polymer Research, Theory Group, PO Box 3148, 55021 Mainz, Germany
#
# This program is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.

# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.

# You should have received a copy of the GNU General Public License
# along with this program.  If not, see <http://www.gnu.org/licenses/>.

dnl Process this file with autoconf to produce a configure script.
dnl Fetch the version and timestamp from RELEASE_NOTES

# Initialise autoconf
AC_INIT(ESPResSo, [2.9.0], [espressomd-users@nongnu.org])

AC_PREREQ(2.50)
AC_CONFIG_SRCDIR([main.c])
AC_CONFIG_AUX_DIR(config)
AC_CONFIG_MACRO_DIR(config)
AC_PREFIX_DEFAULT($HOME/Espresso)
AC_CANONICAL_HOST
AC_CANONICAL_TARGET

# Initialise automake
AM_INIT_AUTOMAKE([1.9 gnu subdir-objects])
AM_MAINTAINER_MODE

cat <<EOF
****************************************************************
*      Checking compiler characteristics and MPI support       *
****************************************************************
EOF
dnl overwrite _AC_PROC_CC_G to avoid check for -g and -O2 flags
m4_define([_AC_PROG_CC_G],[])

#############
#### MPI ####
#############
# Check for --with-mpi
AC_ARG_WITH(mpi, [AC_HELP_STRING([--with-mpi],
    [compile with MPI (parallelization) support. If none is found, the
     fake implementation for only one processor is used. Default: auto])
],,[with_mpi=auto])

AX_MPI_ONLY([test x"$with_mpi" != xno],,[
  if test xyes = x"$with_mpi"; then
    AC_MSG_FAILURE([MPI compiler requested, but couldn't compile with MPI.])
  else
    AC_MSG_WARN([No MPI compiler found, will use fake implementation!])
    use_mpi_fake="yes"
  fi
])

# if requested, use the fake implementation
AM_CONDITIONAL(MPI_FAKE, [test x"$use_mpi_fake" = xyes])
MPI_FAKE=$use_mpi_fake
AC_SUBST(MPI_FAKE)

# determine ESPRESSO_MPIEXEC
AS_IF([test x"$use_mpi_fake" = xyes], [
  ESPRESSO_MPIEXEC=""
],[
  # mpiexec executable
  AC_ARG_VAR([MPIEXEC], [MPI command mpiexec])
  AS_IF([test x"$MPIEXEC" = x], [
    AC_PATH_PROG([MPIEXEC], [mpiexec], [no])
  ])

  AC_MSG_CHECKING([for the mympiexec user script])
  AC_ARG_WITH([mympiexec],
    AS_HELP_STRING([--with-mympiexec@<:@=SCRIPT@:>@],
      [specify the mpiexec-like program or script that should be
       used to run ESPResSo in parallel. If the script doesn't
       exist, it will try to use mpiexec. 
	 @<:@SCRIPT=./mympiexec.sh@:>@]),
    [if test x"$with_mympiexec" = xno; then
       MYMPIEXEC=""
     else
       MYMPIEXEC="$with_mympiexec"
       dir=`AS_DIRNAME([$MYMPIEXEC])`
       if test x"$dir" = x.; then
         MYMPIEXEC="`pwd`/$MYMPIEXEC"
       fi
     fi],
    [ MYMPIEXEC="`pwd`/mympiexec.sh" ])
  AC_MSG_RESULT($MYMPIEXEC)
  AC_SUBST(MYMPIEXEC)

  ESPRESSO_MPIEXEC="`pwd`/tools/es_mpiexec"
])
AC_SUBST(ESPRESSO_MPIEXEC)

##################################
#### COMPILER CHARACTERISTICS ####
##################################
# test whether to enable debugging
AC_ARG_ENABLE(debug,
	AC_HELP_STRING(--enable-debug,
		[Turn on debugging]),
	,enable_debug=no)
if test .$enable_debug = .yes; then
   AC_DEFINE(DEBUG,,[Turn on debugging?])
fi

##################################
# test whether to enable profiling
AC_ARG_ENABLE(profiling,
	AC_HELP_STRING(--enable-profiling,
		[Turn on profiling]),
	,enable_profiling=no)
if test .$enable_profiling = .yes; then
   AC_DEFINE(PROFILING,,[Turn on profiling?])
fi

##################################
# test for -O5
AC_MSG_CHECKING([whether the compiler accepts -O5])
saved_CFLAGS=$CFLAGS
CFLAGS="-O5 $CFLAGS"
AC_COMPILE_IFELSE([AC_LANG_PROGRAM([],[])],[
  AC_MSG_RESULT(yes);try_add_flag_res=yes
],[
  AC_MSG_RESULT(no); CFLAGS=$saved_CFLAGS; try_add_flag_res=no
])

##################################
# test inlining
AS_IF([test .$enable_debug = .yes || test .$enable_profiling = .yes], [
  mdinline=static
],[
  AC_MSG_CHECKING([how to inline functions])
  for mdinline in "static inline" "inline static" "inline" "static"; do
    AC_COMPILE_IFELSE([
      AC_LANG_PROGRAM([
/**** This HAS to be at the beginning of the line for some compilers ****/
#define MDINLINE $mdinline
MDINLINE void test() {}],[test();])
    ],[works=yes],[works=no])
    if test .$works = .yes; then break; fi
  done
  if test .$works = .no; then
    AC_MSG_RESULT([???])
    AC_MSG_ERROR([your compiler does not even support "static"])
  fi
    AC_MSG_RESULT([$mdinline])
])
AC_DEFINE_UNQUOTED(MDINLINE,$mdinline,[How to inline functions])

##################################
# test linking
case $target_os in
*darwin*) LD="`pwd`/darwinlink.sh $CC" ;;
*) LD=$CC ;;
esac

##################################
# miscellaneous
AC_PROG_CC_STDC
AM_PROG_CC_C_O
AC_C_CONST
AC_HEADER_TIME

cat <<EOF
****************************************************************
*                  Checking for programs                       *
****************************************************************
EOF

AC_PROG_AWK
AC_PROG_EGREP
AC_PROG_RANLIB
AC_PROG_INSTALL
AC_PROG_LN_S
AC_PATH_PROG(DOXYGEN,doxygen,no)
AM_CONDITIONAL(HAVE_DOXYGEN,[test .$DOXYGEN != .no])
AC_PATH_PROG(PDFLATEX,pdflatex,no)
AC_PATH_PROG(MAKEINDEX,makeindex,no)
AC_PATH_PROG(BIBTEX,bibtex,no)
AM_CONDITIONAL(HAVE_LATEX,
	[test .$PDFLATEX != .no && 
	test .$MAKEINDEX != .no && 
	test .$BIBTEX != .no])


cat <<EOF
****************************************************************
*               Checking for standard headers                  *
****************************************************************
EOF

##################################
# Checks for header files.
AC_HEADER_STDC
AC_CHECK_HEADERS([arpa/inet.h fcntl.h float.h inttypes.h limits.h netdb.h\
 netinet/in.h stddef.h stdlib.h string.h strings.h sys/file.h sys/socket.h\
 sys/time.h unistd.h])


cat <<EOF
****************************************************************
*               Checking for standard functions                *
****************************************************************
EOF
##################################
# Checks for library functions.
AC_FUNC_MALLOC
AC_FUNC_MEMCMP
AC_FUNC_REALLOC
AC_FUNC_SELECT_ARGTYPES
AC_TYPE_SIGNAL
AC_TYPE_SIZE_T
AC_FUNC_STRTOD
AC_CHECK_FUNCS([atexit floor getcwd gethostbyname memset pow rint select socket sqrt])

cat <<EOF
****************************************************************
*         Checking for required tools and libraries            *
****************************************************************
EOF

LIBS="$LIBS -lm"

##################################
# check for tcl
AC_DEFINE([USE_NON_CONST],1,[prevent TCL from defining const ptrs])

AC_ARG_WITH(tcl,AC_HELP_STRING([--with-tcl=VERSION],[specify the tcl library to use (e.g. tcl8.4)]),
            [], [with_tcl=yes])
dnl with_tcl=yes  try to find a working TCL version, bail out if none is found
dnl with_tcl=no   bail out
dnl otherwise     use the specified version

if test .$with_tcl = .no; then
   AC_MSG_ERROR([Tcl is required by ESPResSo!])
elif test .$with_tcl = .yes; then
	for version in $TCL_VERSION tcl8.6 tcl8.5 tcl8.4 tcl8.3 tcl8.2 tcl; do
		ES_ADDPATH_CHECK_LIB($version, Tcl_Init, [use_tcl=$version])
		if test .$use_tcl != .; then break; fi
	done
else
	ES_ADDPATH_CHECK_LIB($with_tcl, Tcl_Init, [use_tcl=$with_tcl])
fi

# check the results
if test .$use_tcl = .; then
   # Tcl was not found
   case $target_os in
	*darwin*) AC_MSG_NOTICE(
[If you have Tcl installed, make sure that in one of the library paths, e.g. /usr/local/lib,
there is a link from lib<tclversion>.dylib to the Tcl library, which usually is
/Library/Frameworks/Tcl.framework/Tcl.]) ;;
	*) ES_NOTE_64BIT
	   AC_MSG_FAILURE([
********************************************************************************
* Could not link against the (static) Tcl library (libtcl*.a).                 *
* Please add the library path to LDFLAGS (e.g. configure LDFLAGS=-L/usr/lib)!  *
********************************************************************************
]) ;;
   esac
fi

case $target_os in
  *darwin*) extrapaths=/Library/Frameworks/Tcl.framework/Headers ;;
  *linux*)  # path used by *buntu
	  extrapaths=/usr/include/$version ;;
esac

ES_ADDPATH_CHECK_HEADER(tcl.h, [], 
		[AC_MSG_FAILURE([
********************************************************************************
* Could not find the Tcl header files (tcl.h).                                 *
* Please add the include path to CPPFLAGS                                      *
* (e.g. configure CPPFLAGS=-I/usr/include)!                                    *
********************************************************************************
])], $extrapaths)

if test .$use_tcl = .; then
   use_tcl=none
fi

##################################
# check for tk
AC_ARG_WITH(tk,
	AC_HELP_STRING([--with-tk=VERSION],
		[whether to use Tk, and which version to use]),
	[], [with_tk=no])
dnl with_tk=no   don't use Tk
dnl with_tk=yes  try to find a working Tk version, bail out if none is found
dnl otherwise    use the specified version
if test .$with_tk != .no; then
	# test for X11
	AC_PATH_XTRA
	saved_CPPFLAGS=$CPPFLAGS
	saved_LDFLAGS=$LDFLAGS
	saved_LIBS=$LIBS
	CPPFLAGS="$CPPFLAGS $X_CFLAGS"
	LDFLAGS="$LDFLAGS $X_LIBS"
	LIBS="$LIBS $X_PRE_LIBS -lX11 $X_EXTRA_LIBS"
	AC_LINK_IFELSE([AC_LANG_CALL([],[XOpenDisplay])],[x11_works=yes],[x11_works=no])
	if test $x11_works = no; then
		AC_MSG_WARN([could not link against X11, hoping Tk works without])
		CPPFLAGS=$saved_CPPFLAGS
		LDFLAGS=$saved_LDFLAGS
		LIBS=$saved_LIBS
	fi
	# now test whether Tk can be found
	if test .$with_tk = .yes; then
		for version in $TK_VERSION tk8.5 tk8.4 tk8.3 tk8.2 tk; do
			ES_ADDPATH_CHECK_LIB($version, Tk_Init, [use_tk=$version], [])
			if test .$use_tk != .; then break; fi
		done
	else
		ES_ADDPATH_CHECK_LIB($with_tk, Tk_Init, [use_tk=$with_tk], [])
	fi
	if test .$use_tk = .; then
		case $target_os in
		(*darwin*) AC_MSG_ERROR([If you have Tk installed, make sure that in one of the library paths, e.g. /usr/local/lib,
there is a link from lib<tkversion>.dylib to the Tk library, which usually is
/Library/Frameworks/Tk.framework/Tk.]) ;;
		(*) ES_NOTE_64BIT
		    AC_MSG_FAILURE([Tk library $with_tk not found]) ;;
		esac
	fi
	if test .$use_tk = .tk; then
		if test .$use_tcl != .tcl; then
			AC_MSG_WARN([You are using a generic Tk version, but a defined Tcl version. This may cause problems.
Try --with-tcl=tcl to also use a generic Tcl version, which may fit better.])
		fi
	fi
	case $target_os in
	*darwin*) extrapaths=/Library/Frameworks/Tk.framework/Headers ;;
	*linux*)  # path used by *buntu
		  extrapaths="/usr/include/$version /usr/include/$use_tcl" ;;
	(*) ;;
	esac
	ES_ADDPATH_CHECK_HEADER(tk.h, [], 
		[AC_MSG_ERROR([Tk headers not found. Please add the include path to CPPFLAGS (e.g. configure CPPFLAGS=-I/usr/include/tcl8.4).])
		]
		,$extrapaths)
	AC_DEFINE_UNQUOTED(TK,$use_tk,[Whether to use Tk])
else
	use_tk=none
fi

##################################
# check for FFTW
AC_DEFUN([ES_CHECK_FFTW3],[
 	ES_ADDPATH_CHECK_LIB(fftw3, fftw_plan_many_dft, [fftw3_found=yes], [fftw3_found=no])
	if test x$fftw3_found = xyes; then
		ES_ADDPATH_CHECK_HEADER(fftw3.h, [], [fftw3_found=no])
	fi
])

AC_DEFUN([ES_CHECK_FFTW2],[
	dnl we just assume that fftw and rfftw are in the same directory
	dnl if this is not the case for you, consider cleaning up your system...
	dnl first we check for the d* (SuSE) versions, then the normal ones
	dnl At the end we have to include rfftw before fftw on some systems, but testing
	dnl is only possible for fftw
	saved_LIBS=$LIBS
 	ES_ADDPATH_CHECK_LIB(dfftw, fftw_create_plan_specific, [fftw2_found=yes], [fftw2_found=no])
	if test x$fftw2_found = xyes; then
		LIBS="$saved_LIBS -ldrfftw -ldfftw"
	else
	 	ES_ADDPATH_CHECK_LIB(fftw, fftw_create_plan_specific, [fftw2_found=yes], [fftw2_found=no])
		if test x$fftw2_found = xyes; then
			LIBS="$LIBS -lrfftw -lfftw"
		fi
	fi
	if test x$fftw2_found = xyes; then
		ES_ADDPATH_CHECK_HEADER(fftw.h, [], [fftw2_found=no])
	fi
])

AC_MSG_CHECKING([whether to use FFTW])
AC_ARG_WITH([fftw],
	AC_HELP_STRING([--with-fftw=VERSION],
		[specify the version of FFTW to use (2 or 3)]),
	, with_fftw=guess)
AC_MSG_RESULT($with_fftw)
dnl with_fftw=no    don't use FFTW
dnl with_fftw=yes   try to find a working FFTW, bail out if none is found
dnl with_fftw=  (not set) try to find a working FFTW, continue if none is found
dnl otherwise       use the specified version

if test x"$with_fftw" = xguess || test x"$with_fftw" = xyes; then
	# determine fftw automatically
	ES_CHECK_FFTW3
	if test x$fftw3_found = xyes; then
	   use_fftw=3
	else
	  ES_CHECK_FFTW2
	  if test x$fftw2_found = xyes; then
	     use_fftw=2
	  elif test x$with_fftw = xguess; then
	      # no fftw was found, and none was requested
	      use_fftw=none
	  else
              # no fftw was found, but the user wanted one (--with-fftw)
	      ES_NOTE_64BIT
	      AC_MSG_FAILURE([
********************************************************************************
* Could not find FFTW!                                                         *
********************************************************************************
])
	  fi
	fi
elif test x$with_fftw = x3; then
        use_fftw=3
	ES_CHECK_FFTW3
	if test x$fftw3_found != xyes; then
	   ES_NOTE_64BIT
	   AC_MSG_FAILURE([
********************************************************************************
* Could not find FFTW3!                                                        *
* Please add the library path to LDFLAGS (e.g. configure LDFLAGS=-L/usr/lib)   *
* Please add the include path to CPPFLAGS                                      *
* (e.g. configure CPPFLAGS=-I/usr/include).                                    *
********************************************************************************
])
	fi
elif test x$with_fftw = x2; then
	use_fftw=2
	ES_CHECK_FFTW2
	if test x$fftw2_found != xyes; then
	   ES_NOTE_64BIT
	   AC_MSG_FAILURE([
************************************************************************
* Could not find FFTW2!                                                        *
* Please add the library path to LDFLAGS (e.g. configure LDFLAGS=-L/usr/lib)   *
* Please add the include path to CPPFLAGS                                      *
* (e.g. configure CPPFLAGS=-I/usr/include).                                    *
********************************************************************************
])
	fi
elif test x$with_fftw = xno; then
     use_fftw=none
else
  AC_MSG_ERROR([specified bad FFTW version ($with_fftw)])
fi

# after this, use_fftw should be set correctly
if test x$use_fftw != xnone; then
	# save the result
	AC_DEFINE_UNQUOTED(FFTW, $use_fftw, [Whether to use the FFTW library, and which version to use])
fi

##################################
# check for CUDA
AC_MSG_CHECKING([whether to use CUDA])
AC_ARG_WITH([cuda],
	AC_HELP_STRING([--with-cuda=CUDAINSTALLDIR],
		[specify where CUDA is installed.
		The cuda compiler can also be specified by setting the NVCC
		environment variable. The CUDA library and header can be
		manually specified by using CPPFLAGS, LDFLAGS and LIBS.]),
	, with_cuda=no)
AC_MSG_RESULT($with_cuda)

if test x$with_cuda != xno; then
   # if installation dir is given, set the paths
   if test x$with_cuda != xyes; then
       if test x$NVCC = x; then
	   NVCC=$with_cuda/bin/nvcc
       fi
       if test -d $with_cuda/lib64; then
           LDFLAGS="$LDFLAGS -L$with_cuda/lib64"
       else
	   LDFLAGS="$LDFLAGS -L$with_cuda/lib"
       fi
       NVCCFLAGS="$NVCCFLAGS -I$with_cuda/include"
   fi

   # NVCC
   AC_ARG_VAR(NVCC,[NVIDIA CUDA compiler command])
   AC_ARG_VAR(NVCCFLAGS,[special compiler flags for the NVIDIA CUDA compiler])
   AC_PATH_PROG(NVCC, nvcc, no)
   if test x$NVCC = xno; then
       AC_MSG_FAILURE([CUDA compiler nvcc was not found, specify location using the NVCC variable])
   fi

   # runtime library
   AC_CHECK_LIB(cudart, cudaGetDevice, [LIBS="$LIBS -lcudart"], [AC_MSG_FAILURE([could not find cuda runtime library (cudart), specify location using LDFLAGS])])

   # NVCC compile check
   AC_MSG_CHECKING([whether CUDA works])
   # if no other compute capability is defined by the user, we need at least 1.1
   case "$NVCCFLAGS" in
       *-arch=*) ;;
       *) NVCCFLAGS="$NVCCFLAGS -arch=compute_11"
   esac
   # use nvcc
   save_CC=$CC
   save_CFLAGS=$CFLAGS
   CC=$NVCC
   CFLAGS="$NVCCFLAGS -x cu"
   AC_COMPILE_IFELSE([AC_LANG_PROGRAM([
	#include <cuda.h>
	__global__ void test()
	{}
   ],[
	dim3 block(1), grid(1);
	test<<<block,grid>>>();
   ])], [AC_MSG_RESULT(yes)], [AC_MSG_FAILURE([cannot compile CUDA code. Look at config.log for more details.])])
   CC=$save_CC
   CFLAGS=$save_CFLAGS
   AC_DEFINE(CUDA,[],[Whether CUDA is available])
fi
AM_CONDITIONAL(CUDA, [test x$with_cuda != xno])

cat <<EOF
****************************************************************
*                   Other settings                             *
****************************************************************
EOF

##################################
# check for efence
AC_MSG_CHECKING([whether to use efence])
AC_ARG_WITH(efence,
  AC_HELP_STRING([--with-efence],[use ElectricFence memory debugging for the debug binary]),
    ,with_efence=no)
if test .$with_efence = .yes; then
  AC_CHECK_LIB(efence,malloc,,AC_MSG_FAILURE([could not link against the efence library]))
  CPPFLAGS="$CPPFLAGS -DEFENCE"
  LDFLAGS="$LDFLAGS -lefence"
fi
AC_MSG_RESULT($with_efence)

##################################
# Handling the myconfig-header
AC_ARG_WITH(myconfig, 
  AC_HELP_STRING(--with-myconfig=FILE,
    [default name of the local config file [[myconfig.h]]]),
[ test .$with_myconfig = .no && with_myconfig=myconfig.h],
[ with_myconfig=myconfig.h ])
AC_MSG_CHECKING([the name of the configuration header])
myconfig=$with_myconfig
AC_MSG_RESULT($myconfig)
AC_SUBST(myconfig)

##################################
# Number of CPUs
AX_COUNT_CPUS
AC_ARG_VAR(CPU_COUNT,[number of CPUS available on this machine])
AC_SUBST(CPU_COUNT)

AC_ARG_VAR(LIBS,[use this to specify additional libraries to link against, e.g. -lmx])

# where to install the init scripts and tools
buildscriptsdir='${abs_top_builddir}/scripts'
AC_SUBST(buildscriptsdir)
scriptsdir='${pkgdatadir}/scripts'
AC_SUBST(scriptsdir)
toolsdir='${pkgdatadir}/tools'
AC_SUBST(toolsdir)

cat <<EOF
****************************************************************
*                   Creating output files                      *
****************************************************************
EOF
# Creating the output
AC_CONFIG_HEADERS([acconfig.h])
AC_CONFIG_FILES([
	Makefile
	tools/Makefile
	scripts/Makefile 
	testsuite/Makefile
	doc/Makefile
	doc/latexit.sh
	doc/dg/Makefile
	doc/ug/Makefile
	doc/maggs_doc/Makefile
	doc/tutorials/Makefile
	doc/tutorials/tut2/Makefile 
	config/$myconfig:config/myconfig-default.h.in
	])
AC_CONFIG_FILES([testsuite/runtest.sh],
	[chmod 755 testsuite/runtest.sh])
AC_CONFIG_FILES([tools/es_mpiexec],
	[chmod 755 tools/es_mpiexec])

AC_OUTPUT

cat <<EOF
****************************************************************
*                   Configuration summary                      *
****************************************************************
platform detected	= $target

Compiler settings:
------------------
compiler		= $CC
linker			= $LD
c compiler flags	= $CFLAGS
c preprocessor flags	= $CPPFLAGS
linker flags		= $LDFLAGS
libraries		= $LIBS
using debug flags	= $enable_debug
using profiling flags	= $enable_profiling
inline			= $MDINLINE

Libraries:
----------
Tcl version		= $use_tcl
Tk version		= $use_tk
FFTW version		= $use_fftw
efence			= $with_efence

Other settings:
---------------
local config header	= $myconfig
installation prefix	= $prefix
number of CPUs		= $CPU_COUNT
mympiexec               = $MYMPIEXEC
mpiexec	     		= $MPIEXEC
pdflatex		= $PDFLATEX
makeindex		= $MAKEINDEX
bibtex			= $BIBTEX
doxygen			= $DOXYGEN

EOF

if test x"$use_mpi_fake" = x"yes"; then
   echo "WARNING: MPI disabled or not supported! Parallelization turned off."
fi

if test x$with_cuda != xno; then
   echo "Using NVIDA CUDA compiler nvcc = $NVCC, flags = $NVCCFLAGS."
else
   echo "CUDA disabled or not supported."
fi

if test .$pdflatex = .no; then
   echo "To be able to rebuild the user's guide, add pdflatex to your PATH."
fi
if test .$makeindex = .no; then
   echo "To be able to rebuild the user's guide, add makeindex to your PATH."
fi
if test .$bibtex = .no; then
   echo "To be able to rebuild the user's guide, add bibtex to your PATH."
fi
if test .$doxygen = .no; then
   echo "To be able to build the developer's guide, add doxygen to your PATH."
fi
