[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.11338700.svg)](https://doi.org/10.5281/zenodo.11338700) [![Test PROV-BFO Mapping Changes](https://github.com/BFO-Mappings/PROV-to-BFO/actions/workflows/test-mappings.yml/badge.svg)](https://github.com/BFO-Mappings/PROV-to-BFO/actions/workflows/test-mappings.yml)
# PROV - BFO, RO, CCO Mappings
The [Provenance Ontology (PROV-O)](https://www.w3.org/TR/prov-o/) is a World Wide Web Consortium (W3C) recommended ontology used to structure data about provenance across a wide variety of domains. [Basic Formal Ontology (BFO)](https://basic-formal-ontology.org/) is a top-level ontology ISO/IEC standard used to structure a wide variety of ontologies, such as [Relation Ontology (RO)](https://oborel.github.io/) and the [Common Core Ontologies (CCO)](https://github.com/CommonCoreOntology/CommonCoreOntologies). To enhance interoperability between these two ontologies, their extensions, and data organized by them, this repo includes a set of alignments that were created according to specific criteria which prioritize semantic and logical principles.

For more information, read: ["A semantic approach to mapping the Provenance Ontology to Basic Formal Ontology"](https://doi.org/10.1038/s41597-025-04580-1).

# Usage
### PROV-BFO
* [PROV-BFO Release version here.](prov-bfo-directmappings.ttl) This contains only the mapped and extended terms from BFO. Use this file in production.

### PROV-RO
* [PROV-RO Release version here.](prov-ro-directmappings.ttl) This contains only the mapped and extended terms from RO. Use this file in production.

### PROV-CCO
* [PROV-CCO Release version here.](prov-cco-directmappings.ttl) This contains only the mapped and extended terms from CCO. Use this file in production.

### Example Usage
1. Import terms from the ontologies that you want to use in your ontology.
2. Import one or more of the mapping files.
3. Use a semantic reasoner or SPARQL queries on your ontology to generate new inferences about data encoded in your ontology.
* See the [example ontology](/example-usage/example-ontology-full-imports.ttl) demonstrating the first two steps.

### Development
* [Editor's development version here.](src/prov-mappings-edit.ttl) This file is used only for development, not intended for production. It contains imports of BFO, CCO, RO, each and all canonical example instances from the PROV-O docs to test with. Open this file in Protege to view the mapping in full context of each aligned ontology. Test this file to ensure consistency among the extensions.


# Scope
* Every class and object property from PROV-O and its extensions is mapped to some class or object property from BFO, RO, or CCO.
* The mappings use subsumption and equivalence relations either directly with RDFS and OWL, or indirectly with SWRL rules and property chain axioms. Additional SKOS mappings are provided for extra commentary.

* The following PROV namespaces have been (1) mapped in the mapping files, (2) imported in the editors file, and (3) all Turtle-serialized canonical examples from each have been loaded as instances in the editors file for testing:
    * [PROV-O](https://www.w3.org/TR/prov-o/) - [Ontology](https://www.w3.org/ns/prov-o)
    * [PROV Access and Query](https://www.w3.org/TR/prov-aq/) - [Ontology](http://www.w3.org/ns/prov-aq) & [Examples](src/examples/prov-aq-examples.ttl)
    * [PROV Data Dictionary](https://www.w3.org/TR/prov-dictionary) - [Ontology](http://www.w3.org/ns/prov-dictionary) & [Examples](src/examples/prov-dictionary-examples.ttl)
    * [PROV Linking Across Provenance Bundles](https://www.w3.org/TR/prov-links/) - [Ontology](http://www.w3.org/ns/prov-links) & [Examples](src/examples/prov-links-examples.ttl)
    * PROV Inverses - [Ontology](http://www.w3.org/ns/prov-o-inverses)
        * Automated entailment of mappings for inverse object properties is supported by OWL-DL and OWL-QL. For example, `prov:wasMemberOf` may be automatically inferred as a subproperty of BFO "part of" because `prov:hadMember` is a subproperty of BFO "has part" and these PROV terms are inverses of each other.
    * [PROV Dublin Core](https://www.w3.org/TR/prov-dc) - [Ontology](http://www.w3.org/ns/prov-dc) & [Examples](src/examples/prov-dc-examples.ttl)

* Bonus mappings between BFO and the [SOSA (Sensor, Observation, Sample, and Actuator)](https://www.w3.org/TR/vocab-ssn/) ontology are possible, due to the [SOSA-PROV Mapping](https://www.w3.org/TR/vocab-ssn/#PROV_Alignment). 


# Methods
### Development Dependencies
* The version of each PROV-O, BFO, RO, and CCO file is specified in the  `owl:imports` of the editor's file `prov-mappings-edit.ttl`.

* The following W3C semantic web technologies are used to represent the mappings and the mapping criteria:
    * [RDF](https://www.w3.org/RDF/) version 1.1, 25 February 2014
    * [RDF Schema](https://www.w3.org/TR/2014/REC-rdf-schema-20140225/) version 1.1, 25 February 2014
    * [OWL](https://www.w3.org/TR/2012/REC-owl2-overview-20121211/) version 2, 11 December 2012
    * [RDF Turtle](https://www.w3.org/TR/2014/REC-turtle-20140225/) version 1.1, 25 February 2014
    * [SPARQL](https://www.w3.org/TR/2013/REC-sparql11-query-20130321/) version 1.1, 21 March 2013
    * [SWRL](https://www.w3.org/submissions/2004/SUBM-SWRL-20040521/) version, 21 May 2004

* The following tools are used to develop and evaluate the mappings:
    * [Protégé](https://protege.stanford.edu/) version 5.6.3
    * [ROBOT](https://robot.obolibrary.org/) version 1.9.5
    * [HermiT Reasoner](https://mvnrepository.com/artifact/net.sourceforge.owlapi/org.semanticweb.hermit) version 1.4.5.456
    * [GNU Make](https://www.gnu.org/software/make/) version 3.81

### Development Process
1. A separate RDF Turtle file is created to extend each ontology with a set of mappings.

2. All mappings and ontologies are imported into the editor's file `prov-mappings-edit.ttl` to help visualize the mappings using Protege. To ensure tractability of reasoning, RO terms that are referenced, along with their logical dependencies, are extracted and imported separately.

3. A mapping file adds triples which relate terms from each ontology using the following "mapping" predicates: `rdfs:subClassOf`, `rdfs:subPropertyOf`, `owl:equivalentClass`, and `owl:equivalentProperty`.
    * SWRL rules and property chain axioms are used to represent more complex, indirect relations.
    * SKOS relations are used for extra commentary. 
    * OWL annotation axioms encode each mapping with justifications as `rdfs:comment` and other SSSOM metadata.

4. SPARQL queries are used for finding "unmapped" classes and object properties, and also "candidate" superproperties. The HermiT reasoner is used to check consistency and materialize inferences for various development tasks.

6. Mappings for each ontology are encoded as separate extensions to maximize compatibility and minimize dependencies for users, following conventions from the PROV-DC (Dublin Core) mapping.

### Testing
* All example instances from the [PROV-O documentation](https://www.w3.org/TR/prov-o/) and its extensions are temporarily imported into the extended ontology. The HermiT reasoner is used to check if the example instance data is consistent with the extended ontology.
* ROBOT runs HermiT and also SPARQL queries for evaluating the mappings. A SPARQL query that formalizes the mapping criteria is executed to check whether any mappings are missing.

* How to run tests automatically:
    * Push commits to this Github repo, and the Makefile tests will be triggered in Github Workflow files

* How to run tests manually:
    * Requirements: [GNU Make](https://www.gnu.org/software/make/)
    * Run `make` from the src directory, or `make -C src` from this directory
    * Alternatively, run the commands defined within the Makefile.

### Release
* Release files are indicated with `-directmappings` prefix in the file names. These are edited and annotated directly, and imported in the Editor's file for development and testing.
* Turtle (.ttl) versions of the release are currently *not* built automatically in order to ensure precise formatting, but RDF/XML versions could be automatically built from the Turtle versions.
* Release files do *not* include imports of the ontologies they map. Why? First, because many terms in the BFO ontologies are not used or relevant to the mappings. Second, because users should not need to import irrelevant terms from any ontology (including PROV ontologies) for their use cases.

# How to cite
Please cite with the following reference:
```
Prudhomme, T., De Colle, G., Liebers, A., Sculley, A., Xie, P., Cohen, S., & Beverley, J. 
A semantic approach to mapping the Provenance Ontology to Basic Formal Ontology.
Sci Data 12, 282 (2025). https://doi.org/10.1038/s41597-025-04580-1
```

An archive of this repository can be referenced with: https://doi.org/10.5281/zenodo.11338700