# Copyright (C) 2001-2016 Quantum ESPRESSO group
# 
# This program is free software; you can redistribute it and/or
# modify it under the terms of the GNU General Public License
# as published by the Free Software Foundation; either version 2
# of the License. See the file `License' in the root directory
# of the present distribution.
#
# Original: Filippo Spiga (filippo.spiga@quantum-espresso.org)
# Maintainer: Samuel Ponce
#
# Makefile to run regression numerical tests. Simple is good

include ENVIRONMENT

default :
	@echo 'To run Wannier90 test-suite, type at the shell prompt:'
	@echo ' '
	@echo '  make target'
	@echo ' '
	@echo 'where <target> identifies an action'
	@echo ' run-tests           : run wannier tests (default)'
	@echo ' run-tests-interface : run tests that requires other codes (e.g. QE)'
	@echo ' run-tests-all       : run all the tests in SERIAL '
	@echo ' run-tests-parallel  : run all tests in PARALLEL (4 MPI)'
	@echo ' compare             : compare last output generated with reference'
	@echo ' clean               : clean output (our/err files) of all tests'
	@echo ' '
	@echo 'For additional advanced commands and settings please manually inspect'
	@echo 'ENVIRONMENT and Makefile files'

prolog :
#	@if [ ! -f userconfig ]; then \
	sed "s|XXXXXX|$(WANNIER_ROOT)|g" < userconfig.tmp > userconfig; fi
	@sed "s|XXXXXX|$(WANNIER_ROOT)|g" < userconfig.tmp > userconfig

run-tests : run-tests-wannier-serial

run-tests-interface : prolog
	env QE_USE_MPI=0 ${TESTCODE_DIR}/bin/testcode.py --verbose --category=interface_all

run-tests-all : prolog
	env QE_USE_MPI=0 ${TESTCODE_DIR}/bin/testcode.py --verbose --category=test_all

run-tests-parallel : run-tests-wannier-parallel

run-custom-test : run-custom-test-serial

run-tests-wannier-serial : prolog 
	        env QE_USE_MPI=0 ${TESTCODE_DIR}/bin/testcode.py --verbose --category=wannier_all

run-tests-wannier-parallel : prolog 
	        env QE_USE_MPI=1 ${TESTCODE_DIR}/bin/testcode.py --verbose --category=wannier_all

run-custom-test-serial : prolog 
	@if test -d $(testdir); then \
	env QE_USE_MPI=0 ${TESTCODE_DIR}/bin/testcode.py --verbose --category=$(testdir) ; fi

run-custom-test-parallel : prolog 
	@if test -d $(testdir); then \
	env QE_USE_MPI=1 ${TESTCODE_DIR}/bin/testcode.py --verbose --category=$(testdir) ; fi


compare : compare-cp compare-pw compare-epw

# All comparison outputs are, so far, generated only in SERIAL
create-reference : prolog
	env QE_USE_MPI=0 ${TESTCODE_DIR}/bin/testcode.py --category=_default_ make-benchmarks

compare-wannier : prolog
	${TESTCODE_DIR}/bin/testcode.py --category=wannier_all  --verbose compare 2>&1 | tee out.WANNIER90.`date +%Y%m%d_%H%M%S`

clean:
	@for x in `find example* -name "test.*"`; do rm -rf $$x; done
	@for x in `find example* -name "*.wout"`; do rm -rf $$x; done
	@for x in `find pw_example* -name "test.*"`; do rm -rf $$x; done
	@for x in `find pw_example* -name "*.wout"`; do rm -rf $$x; done
	@for x in `find test_* -name "test.*"`; do rm -rf $$x; done
	@for x in `find test_* -name "*.wout"`; do rm -rf $$x; done

# Calling 'purge' means remove all reference outputs... be careful!
purge: clean
	@for x in `find pw_* -name "benchmark*"`; do rm -rf $$x; done
	@for x in `find cp_* -name "benchmark*"`; do rm -rf $$x; done
