# PROV - BFO Extension
## Contributors
Sydney Cohen, Giacomo De Colle, Austin Liebers, Tim Prudhomme, Alec Sculley, Karl Xie

# Usage
* [Release version here.](prov-bfo-directmappings.ttl) This contains only the mapped and extended terms. Use this one in production.
* [Editor's development version here.](prov-bfo-edit.ttl) This contains imports of BFO, RO, and example instances to test with. Open this one in Protege to view the mapping in full context of each aligned ontology.

# Scope
* The mapping goal is to subsume every [PROV-O](https://www.w3.org/TR/prov-o/) term under some [BFO (Basic Formal Ontology)](https://basic-formal-ontology.org/) or [RO (Relation Ontology)](https://oborel.github.io/) term. A separate extension which maps PROV-O to [CCO (Common Core Ontologies)](https://github.com/CommonCoreOntology/CommonCoreOntologies) is also proposed. 
* This mapping aligns PROV-O to these top-level ontologies to promote interoperability between PROV-O users and BFO users.
* At the same time, the mapping is encoded as an extension to maximize compatibility and minimize dependencies for existing PROV-O users.
* Further BFO interpretations of PROV-O are also offered:
    * An extension of `prov:Agent` using a new term `prov:Agent Role` (a `bfo:role`), which is realized by an instance of `prov:Activity`.
    * An extension of `prov:StartedAtTime` and `prov:EndedAtTime` in terms of `bfo:temporal region`. 

# Methods
### Development
* BFO and RO are temporarily imported into PROV-O to help visualize the mappings in the class hierarchy using Protege
* SPARQL queries are used for finding unsubsumed classes and object properties
* The final result includes triples which relate terms from each ontology using the following "mapping" predicates: `rdfs:subClassOf`, `rdfs:subPropertyOf`, and `owl:equivalentClass`, similar to the [PROV-DC extension](https://www.w3.org/ns/prov-dc-directmappings.ttl)
* Previously, the mappings were added directly to an editor's version of PROV. These are kept in [this folder](prov-bfo-merged) for historical documentation but should not be used.

### Metadata
* The mappings include the following metadata:
    * A justification for each mapping
    * A confidence score for each mapping
* This metadata could be specified either with some standardized annotation properties or an [SSSOM template](https://mapping-commons.github.io/sssom/)
* The SSSOM template is generated from the mapping file using a SPARQL query and the [Makefile](Makefile)

### Testing
* Example instances from the [PROV-O documentation](https://www.w3.org/TR/prov-o/) are temporarily imported into the extended ontology
* The HermiT reasoner is used to check if the example instance data is consistent with the extended ontology

* How to run tests automatically:
    * Push commits to this Github repo, and the [Makefile](Makefile) tests will be triggered in Github Workflow files

* How to run tests manually:
    * Requirements: [GNU Make](https://www.gnu.org/software/make/)
    * Run `make` from the PROV directory, or `make -C PROV` from the BFO-Mappings directory