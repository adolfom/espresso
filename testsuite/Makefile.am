# Copyright (C) 2007,2008,2009,2010,2011 Olaf Lenz, Axel Arnold
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
# all the test scripts
tests= \
	nve_pe.tcl npt.tcl \
	madelung.tcl p3m.tcl el2d.tcl \
	p3m-magnetostatics.tcl \
	el2d_nonneutral.tcl el2d_die.tcl mmm1d.tcl dh.tcl \
	lj.tcl lj-cos.tcl lj-generic.tcl tabulated.tcl gb.tcl \
	harm.tcl fene.tcl \
	kinetic.tcl thermostat.tcl \
	intpbc.tcl intppbc.tcl \
	layered.tcl nsquare.tcl \
	comforce.tcl comfixed.tcl \
	analysis.tcl \
	rotation.tcl \
	constraints.tcl \
	mass.tcl \
	lb.tcl \
        tunable_slip.tcl \
	virtual-sites.tcl

# add data files for the tests here
EXTRA_DIST = $(tests) \
	tests_common.tcl \
	analysis_system.data.00.gz analysis_system.data.01.gz \
	analysis_system.data.02.gz analysis_system.data.03.gz \
	analysis_system.data.04.gz analysis_system.data.05.gz \
	analysis_system.data.06.gz analysis_system.data.07.gz \
	analysis_system.data.08.gz analysis_system.data.09.gz \
	analysis_system.data.10.gz analysis_system.data.chk \
	comforce_system.data comfixed_system.data \
	constraints_system.data \
        p3m-magnetostatics-t1.data \
	p3m_system.data el2d_system.data el2d_system_die.data\
	mmm1d_system.data dh_system.data \
	lj_system.data lj-cos_system.data \
	gb_system.data \
	tabulated_system.data lj1.tab lj2.tab lj3.tab \
	fene_system.data.gz harm_system.data.gz \
	intpbc_system.data.gz intppbc.tcl intppbc_system.data.gz \
	npt_lj_system.data \
	thermostat.data thermostat_rot.data \
	mass_system.data \
	lb_system.data \
	\
	pe_micelle.tcl pe_micelle.data \
	uwerr_test.tcl uwerr_test.data \
	\
	gen_fene.tcl gen_harm.tcl

# run the testsuite
check-local: runtest.sh
	if test -n "$(processors)"; then \
	  procopt=-p "$(processors)"; \
	fi; \
	@builddir@/runtest.sh $$procopt $(tests)

DISTCLEANFILES=runtest.sh
