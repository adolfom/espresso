# Copyright (C) 2007,2008,2009,2010 Olaf Lenz, Axel Arnold
#
# This file is part of ESPResSo.
#
# ESPResSo is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.
#
# ESPResSo is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program.  If not, see <http://www.gnu.org/licenses/>.
#
SUBDIRS = . scripts tools testsuite doc

# List files that should go into the distribution but are not required
# by any other means
EXTRA_DIST = darwinlink.sh \
	autogen.sh samples cmake CMakeLists.txt \
        acconfig.h.cmakein packages \
	$(extra)

# List headers that are not used by the program here
noinst_HEADERS = myconfig-sample.h \
	p3m-charges.c p3m-dipoles.h \
	p3m-dipoles.c p3m-charges.h \
	p3m-assignment.c

# List the sources of the Espresso binary here
libEspresso_a_SOURCES = \
	main.c \
	config.c config.h \
	initialize.c initialize.h \
	global.c global.h \
	communication.c communication.h \
	binary_file.c binary_file.h \
	interaction_data.c interaction_data.h\
	verlet.c verlet.h \
	grid.c grid.h \
	integrate.c integrate.h \
	cells.c cells.h \
	ghosts.c ghosts.h \
	forces.c forces.h \
	rotation.c rotation.h \
	debug.c debug.h \
	particle_data.c	particle_data.h \
	thermostat.c thermostat.h \
	dpd.c dpd.h \
	statistics.c statistics.h \
	statistics_chain.c statistics_chain.h \
	energy.c energy.h \
	pressure.c pressure.h \
	vmdsock.c vmdsock.h \
	imd.c imd.h \
	iccp3m.c iccp3m.h \
	p3m.c p3m.h \
	magnetic_non_p3m_methods.c magnetic_non_p3m_methods.h \
	ewald.c ewald.h \
	fft.c fft.h \
	random.c random.h \
	blockfile.c blockfile.h \
	blockfile_tcl.c blockfile_tcl.h \
	polymer.c polymer.h \
	specfunc.c specfunc.h \
	tuning.c tuning.h \
	uwerr.c	uwerr.h \
	parser.c parser.h \
	domain_decomposition.c domain_decomposition.h \
	nsquare.c nsquare.h \
	layered.c layered.h \
	mmm-common.c mmm-common.h \
	mmm1d.c mmm1d.h \
	mmm2d.c	mmm2d.h \
	modes.c	modes.h \
	topology.c topology.h \
	nemd.c nemd.h \
	statistics_cluster.c statistics_cluster.h \
	elc.c elc.h \
	mdlc_correction.c  mdlc_correction.h \
	statistics_molecule.c statistics_molecule.h \
	errorhandling.c	errorhandling.h \
	constraint.c constraint.h \
	maggs.c maggs.h \
	mol_cut.h \
	rattle.c rattle.h \
	molforces.c molforces.h \
	virtual_sites.c virtual_sites.h \
	virtual_sites_com.c virtual_sites_com.h \
	virtual_sites_relative.c virtual_sites_relative.h \
	metadynamics.c metadynamics.h \
        npt.h \
	lb.c lb.h \
	lb-d3q18.h lb-d3q19.h \
	bin.c bin.h \
	lattice.c lattice.h \
	halo.c halo.h \
	statistics_fluid.c statistics_fluid.h \
	lb-boundaries.c lb-boundaries.h \
	utils.c utils.h \
	angle.h \
        pwdist.h \
	angledist.h \
	endangledist.h \
	buckingham.h \
	comfixed.h \
	comforce.h \
	debye_hueckel.h \
	reaction_field.h \
	dihedral.h \
	fene.h \
	gb.h \
	harmonic.h \
        hertzian.h \
	imd.h \
	ljcos2.h \
	ljcos.h \
	lj.h \
	ljgen.c ljgen.h \
	steppot.h \
	bmhtf-nacl.h \
	morse.h \
	polynom.h \
	soft_sphere.h \
	subt_lj.h \
	tab.h \
	overlap.h \
	ljangle.h \
	adresso.c adresso.h \
        tunable_slip.h

#################################################################
# Installation of the main program
#################################################################
# All objects are compiled into the library
# Two binaries are generated: Espresso for the build dir,
# Espresso.install for the installation dir. 
# The ".install" suffix is removed upon installation
noinst_LIBRARIES = libEspresso.a

noinst_PROGRAMS = Espresso
Espresso_CPPFLAGS = -D 'ESPRESSO_SCRIPTS_DEFAULT="$(buildscriptsdir)"'
Espresso_SOURCES = scriptsdir.c
Espresso_LDADD = libEspresso.a

bin_PROGRAMS = Espresso.install
Espresso_install_CPPFLAGS = -D 'ESPRESSO_SCRIPTS_DEFAULT="$(scriptsdir)"'
Espresso_install_SOURCES = scriptsdir.c
Espresso_install_LDADD = libEspresso.a

# rename Espresso after installation
install-exec-hook:
	$(am__mv) \
		$(DESTDIR)$(bindir)/Espresso.install \
		$(DESTDIR)$(bindir)/Espresso

uninstall-local:
	-rm -f $(DESTDIR)$(bindir)/Espresso


#################################################################
# Documentation
#################################################################
.PHONY: FORCE doc ug dg tutorials
doc ug dg tutorials: FORCE
	cd doc; $(MAKE) --print-directory $@
FORCE:

#################################################################
# Handling of local configure headers
#################################################################

# add config/ to the INCLUDES so that the default config/$(myconfig) is
# found at compile time
INCLUDES = -Iconfig
DEFS += -D 'MYCONFIG_H="$(myconfig)"'

# add config/ to the VPATH so that $(myconfig) can be found by make
vpath $(myconfig) $(top_builddir):$(top_srcdir):$(top_builddir)/config

# add $(myconfig) to the dependencies of all object files so that
# they get rebuilt when $(myconfig) is changed
$(Espresso_OBJECTS): $(myconfig)

DISTCLEANFILES = $(top_builddir)/config/$(myconfig)

#################################################################
# Handling of the MPI fake implementation
#################################################################
if MPI_FAKE
libEspresso_a_SOURCES += mpifake/mpi.h mpifake/mpi.c
# mpifake should come before any system includes
INCLUDES += -I$(srcdir)/mpifake -Iconfig
endif

ACLOCAL_AMFLAGS = -Iconfig

##################################################
# CUDA rules
##################################################
if CUDA
SUFFIXES=.cu
.cu.o:
	$(NVCC) -c $(NVCCFLAGS) $(CPPFLAGS) $(INCLUDES) $(DEFS) -o $@ $<

CUDA_SOURCES= \
	cuda_init.cu \
	cuda_init.h

libEspresso_a_SOURCES += $(CUDA_SOURCES)
endif

##################################################
# Targets for backwards compatibility
##################################################
test: check
mostclean: mostlyclean
docu: doc

