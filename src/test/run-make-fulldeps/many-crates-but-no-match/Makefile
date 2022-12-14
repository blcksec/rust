include ../tools.mk

# Modelled after ui/changing-crates.rs test, but this one puts
# more than one (mismatching) candidate crate into the search path,
# which did not appear directly expressible in UI testing infrastructure.
#
# Note that we move the built libraries into target direcrtories rather than
# use the `--out-dir` option because the `../tools.mk` file already bakes a
# use of `--out-dir` into the definition of $(RUSTC).

A1=$(TMPDIR)/a1
A2=$(TMPDIR)/a2
A3=$(TMPDIR)/a3

# A hack to match distinct lines of output from a single run.
LOG=$(TMPDIR)/log.txt

all:
	mkdir -p $(A1) $(A2) $(A3)
	$(RUSTC) --crate-type=rlib crateA1.rs
	mv $(TMPDIR)/$(call RLIB_GLOB,crateA) $(A1)
	$(RUSTC) --crate-type=rlib -L $(A1) crateB.rs
	$(RUSTC) --crate-type=rlib crateA2.rs
	mv $(TMPDIR)/$(call RLIB_GLOB,crateA) $(A2)
	$(RUSTC) --crate-type=rlib crateA3.rs
	mv $(TMPDIR)/$(call RLIB_GLOB,crateA) $(A3)
	# Ensure crateC fails to compile since A1 is "missing" and A2/A3 hashes do not match
	$(RUSTC) -L $(A2) -L $(A3) crateC.rs >$(LOG) 2>&1 || true
	$(CGREP) \
		'found possibly newer version of crate `crateA` which `crateB` depends on' \
		'note: perhaps that crate needs to be recompiled?' \
		'crate `crateA`:' \
		'crate `crateB`:' \
		< $(LOG)
	# the 'crate `crateA`' will match two entries.
