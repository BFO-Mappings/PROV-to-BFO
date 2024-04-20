#### Basic Ontology Pipeline
# https://github.com/tmprd/ontology-pipeline/
# Heavily adapted from https://github.com/obi-ontology/obi/blob/master/Makefile by James A. Overton <james@overton.ca>
# Tim Prudhomme <tmprdh@gmail.com>

# WARN: This file contains significant whitespace, i.e. tabs!
# Ensure that your text editor shows you those characters.

# --------------------------------------------------------------------------------
#### Configuration / details about our project
# Project essentials
config.ONTOLOGY_FILE	:= prov-mappings-edit.ttl
config.ONTOLOGY_PREFIX	:= prov-bfo-directmappings
config.ONTOLOGY-IRI		:= http://www.w3.org/ns/prov-bfo-directmappings
config.BASE_IRI			:= $(config.ONTOLOGY-IRI)_
config.DEV_IRI			:= $(config.ONTOLOGY-IRI)/dev
config.MODULES_IRI		:= $(config.DEV_IRI)/modules

# Local project directories
config.SOURCE_DIR		:= /
config.TEMP_DIR			:= build/artifacts
config.RELEASE_DIR		:= /
config.REPORTS_DIR		:= $(config.TEMP_DIR)
config.QUERIES_DIR		:= sparql
config.IMPORTS_DIR		:= imports
config.LIBRARY_DIR		:= ../build/lib

# Settings
# These will cause targets run by a Github Action to fail, if desired
config.FAIL_ON_TEST_FAILURES := false 
config.REPORT_FAIL_ON := none

# Other constants
TODAY  		:= $(shell date +%Y-%m-%d)
TIMESTAMP	:= $(shell date +'%Y-%m-%d %H:%M')

# Default name for release. Ideally this should incude some kind of semantic versioning
config.RELEASE_NAME := $(config.ONTOLOGY_PREFIX) $(TIMESTAMP)

# Generic files
EDITOR_BUILD_FILE = $(config.ONTOLOGY_FILE) # "editors ontology module" http://purl.obolibrary.org/obo/IAO_8000002
RELEASE_BUILD_FILE = $(config.ONTOLOGY_PREFIX).ttl # "main release ontology module" http://purl.obolibrary.org/obo/IAO_8000003

EDITOR_REPORT_FILE = $(config.REPORTS_DIR)/$(config.ONTOLOGY_PREFIX)-edit-report.tsv
RELEASE_REPORT_FILE = $(config.REPORTS_DIR)/$(config.ONTOLOGY_PREFIX)-release-report.tsv

# Generic directories to create if needed
REQUIRED_DIRS = $(config.TEMP_DIR) $(config.LIBRARY_DIR) $(config.SOURCE_DIR) $(config.QUERIES_DIR) $(config.REPORTS_DIR)


#--- Custom targets
MAPPINGS_FILE			:= prov-bfo-mappings.sssom.csv # csv for importing into google sheets
mappings: 
	$(ROBOT) query --input $(EDITOR_BUILD_FILE) -q $(config.QUERIES_DIR)/get-mappings.rq $(MAPPINGS_FILE) --use-graphs true

candidates:
	$(ROBOT) query --input $(EDITOR_BUILD_FILE) -q $(config.QUERIES_DIR)/candidate-superproperties.rq $(config.TEMP_DIR)/candidate-superproperties.tsv --use-graphs true ;

candidates-complex: 
	$(ROBOT) query --input $(EDITOR_BUILD_FILE) -q $(config.QUERIES_DIR)/candidate-superproperties-complex.rq $(config.TEMP_DIR)/candidate-superproperties-complex.tsv --use-graphs true ;


# Materialize inferred mappings (after removing individuals) then check for unmapped terms
unmapped:
	$(ROBOT) remove --input $(EDITOR_BUILD_FILE) --select individuals \
	reason --reasoner HermiT --axiom-generators "SubClass SubObjectProperty InverseObjectProperties" --include-indirect true \
	query --output-dir $(config.TEMP_DIR) --queries $(config.QUERIES_DIR)/unmapped-terms.rq --use-graph true

# Remove CCO individuals to speed up reasoner
config.CCO_FILENAME = ../MergedAllCoreOntology-v1.4-2023-04-07
config.CCO_FILE = $(config.CCO_FILENAME).ttl
config.CCO_EDITED_FILE = $(config.CCO_FILENAME)-no-individuals.ttl
prep-cco:
	$(ROBOT) remove --input $(config.CCO_FILE) --select individuals --output $(config.CCO_EDITED_FILE)


# --------------------------------------------------------------------------------
#### Targets / main "goals" of this Makefile
.PHONY: all
#all: reason-edit test-edit build-release reason-release test-release mappings
# NOTE: We aren't building automatically building a release version, but instead editing & testing the release version directly
all: reason-edit test-edit mappings

build-release: $(RELEASE_BUILD_FILE)

# These are parameterized targets, which assign "target-specific variables" to be used in a resuable target
.PHONY: reason-edit reason-release test-edit test-release report-edit report-release
reason-edit test-edit report-edit explain-edit: 				TEST_INPUT = $(EDITOR_BUILD_FILE)
reason-release test-release report-release explain-release:		TEST_INPUT = $(RELEASE_BUILD_FILE)
report-edit:													REPORT_FILE_INPUT = $(EDITOR_REPORT_FILE)
report-release:													REPORT_FILE_INPUT = $(RELEASE_REPORT_FILE)

# Map to reusable targets (this is a disjunction mapped to a conjunction: either target maps to all of these targets)

reason-edit reason-release: reason 
explain-edit: explain
test-edit test-release: verify
report-edit report-release: report

# Output variables that are useful to a Github Action
.PHONY: output-release-filepath
output-release-filepath:
	@echo $(RELEASE_BUILD_FILE)
	
.PHONY: output-release-version
output-release-name:
	@echo $(config.RELEASE_NAME)


# --------------------------------------------------------------------------------
#### Setup / configure Make to use with our project
# Make configuration --- Ignore this section unless curious
# <http://clarkgrubb.com/makefile-style-guide#toc2>
MAKEFLAGS += --warn-undefined-variables
SHELL := bash # need bash to use `pipefail`
# `-e` causes exit if a command fails. `-u` causes exit for undefined variables. `-c` passes Make script to bash
# `pipefail` causes a chain of piped commands to fail if any of its commands fail
.SHELLFLAGS := -eu -o pipefail -c
.DEFAULT_GOAL := all # default target running Make without arguments
.DELETE_ON_ERROR: # delete target if recipe fails
.SUFFIXES: # ignore default suffix rules
.SECONDARY: # keep all files created by pattern rules

# Create any of these directories if they don't exist
$(REQUIRED_DIRS):
	mkdir -p $@

# ROBOT
ROBOT_FILE := $(config.LIBRARY_DIR)/robot.jar
$(ROBOT_FILE): | $(config.LIBRARY_DIR)
	curl -L -o $@ https://github.com/ontodev/robot/releases/download/v1.9.5/robot.jar

ROBOT := java -jar $(ROBOT_FILE)

# Cleanup
.PHONY: clean
clean:
	@[ "${config.TEMP_DIR}" ] || ( echo ">> config.TEMP_DIR is not set"; exit 1 )
	rm -rf $(config.TEMP_DIR)
	rm -rf $(RELEASE_BUILD_FILE)


# --------------------------------------------------------------------------------
#### Build / different versions of the ontology

# Release build has import declarations removed
$(RELEASE_BUILD_FILE): $(EDITOR_BUILD_FILE)
	$(ROBOT) remove --input $< --select "imports individuals" \
	reason --reasoner ELK \
	annotate ${call annotation-inputs,$@,$@}

# Function (aka "canned recipe") using parameters to build arguments for `annnotate`
define annotation-inputs
	--ontology-iri "$(config.ONTOLOGY-IRI)/$1" \
	--version-iri "$(config.ONTOLOGY-IRI)/$(TODAY)/$1" \
	--annotation owl:versionInfo "$(TODAY)" \
	--output $2
endef

# --------------------------------------------------------------------------------
#### Manage imports

config.EXTRACTED_IMPORTS_NAME := RO-imports-extracted
EXTRACTED_IMPORTS_FILENAME := $(config.IMPORTS_DIR)/$(config.EXTRACTED_IMPORTS_NAME)
EXTRACTED_IMPORTS_IRI := https://raw.githubusercontent.com/BFO-Mappings/BFO-Mappings/main/PROV/$(EXTRACTED_IMPORTS_FILENAME).ttl
IMPORTED_SOURCE_ONTOLOGY := $(config.IMPORTS_DIR)/ro.owl

ontology-temp-file := ontology_no_imports.tmp.ttl

# Build list of imported terms that are referenced in the mappings
.PHONY: $(EXTRACTED_IMPORTS_FILENAME).txt
# TODO this could be less verbose if `ROBOT query` could pipe its inputs and outputs
$(EXTRACTED_IMPORTS_FILENAME).txt: $(config.ONTOLOGY_FILE) $(ROBOT_FILE)
	echo "Removing extracted imported terms file from mapping file before querying..."; \
	sed '\#$(EXTRACTED_IMPORTS_IRI)#d' $(config.ONTOLOGY_FILE) > $(ontology-temp-file); \
	echo "Querying for referenced terms within mappings and saving to $@"; \
	$(ROBOT) query --input $(ontology-temp-file) -q $(config.QUERIES_DIR)/get-imported-terms.rq $@ --use-graphs true; \
	tail -n +2 $@ > "imported-terms.tmp" && mv "imported-terms.tmp" $@; rm -f $(ontology-temp-file);

# Extract imported terms from their source ontology to avoid testing with unnecessary axioms
.PHONY: extract-imports
extract-imports: $(EXTRACTED_IMPORTS_FILENAME).txt | $(ROBOT_FILE)
	$(ROBOT) extract --input $(IMPORTED_SOURCE_ONTOLOGY) --method subset --term-file $< --output $(EXTRACTED_IMPORTS_FILENAME).ttl;


# --------------------------------------------------------------------------------
#### Test / test ontology with reasoners and queries
QUERIES = $(wildcard $(config.QUERIES_DIR)/*.rq)

# Check for inconsistency
# Note: $(TEST_INPUT) is a "target-specific variable" that isn't a evaluated as a prerequisite here
.PHONY: reason
reason: $(TEST_INPUT) | $(ROBOT_FILE)
	$(ROBOT) reason --input $(TEST_INPUT) --reasoner HermiT

explain: $(TEST_INPUT) | $(ROBOT_FILE)
	$(ROBOT) explain --input $(TEST_INPUT) --reasoner HermiT -M inconsistency --explanation inconsistency-explanation.md

# Test using specific queries
.PHONY: verify
verify: $(TEST_INPUT) $(QUERIES) | $(config.QUERIES_DIR) $(config.TEMP_DIR) $(ROBOT_FILE)
ifeq ($(QUERIES),)
	$(warning No query files found in $(config.QUERIES_DIR))
else
	$(ROBOT) verify --input $(TEST_INPUT) --output-dir $(config.TEMP_DIR) --queries $(QUERIES) --fail-on-violation $(config.FAIL_ON_TEST_FAILURES)
endif

# Report using built-in ROBOT queries
.PHONY: report
report: $(TEST_INPUT) | $(config.REPORTS_DIR) $(ROBOT_FILE)
	$(ROBOT) report --input $(TEST_INPUT) \
	--labels true \
	--fail-on $(config.REPORT_FAIL_ON) \
	--print 10 \
	--output $(REPORT_FILE_INPUT)

# --------------------------------------------------------------------------------
#### Make syntax cheatsheet
# targets
# .PHONY means a target that isn't a file to be created or checked

# automatic variables
# $@ means target
# $< means first prerequisite
# $^ means all prerequisites

# := means simply expand
# = means recursively/lazily expand