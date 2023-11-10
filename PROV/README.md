# PROV - BFO Extension
## Contributors
Sydney Cohen, Giacomo De Colle, Austin Liebers, Tim Prudhomme, Alec Sculley, Karl Xie

# Usage
### Development
* [Editor's development version here.](prov-mappings-edit.ttl) This contains imports of BFO, CCO, RO, and 91 example instances from the PROV-O docs to test with. Open this file in Protege to view the mapping in full context of each aligned ontology. Test this file to ensure consistency among the extensions.

### PROV-BFO
* [PROV-BFO Release version here.](prov-bfo-directmappings.ttl) This contains only the mapped and extended terms from BFO. Use this file in production.

### PROV-RO
* [PROV-RO Release version here.](prov-ro-directmappings.ttl) This contains only the mapped and extended terms from RO. Use this file in production.

### PROV-CCO
* [PROV-CCO Release version here.](prov-cco-directmappings.ttl) This contains only the mapped and extended terms from CCO. Use this file in production.

# Scope
* The mapping goal is to subsume every [PROV-O](https://www.w3.org/TR/prov-o/) term under some [BFO (Basic Formal Ontology)](https://basic-formal-ontology.org/) or [RO (Relation Ontology)](https://oborel.github.io/) term. A separate extension which maps PROV-O to [CCO (Common Core Ontologies)](https://github.com/CommonCoreOntology/CommonCoreOntologies) is also proposed. 
* This mapping aligns PROV-O to these top-level ontologies to promote interoperability between PROV-O users and BFO users.
* At the same time, the mapping is encoded as an extension to maximize compatibility and minimize dependencies for existing PROV-O users.
* The mapping follows conventions from the PROV-DC (Dublin Core) mapping by serializing as an RDF Turtle extension of PROV.
* Further BFO interpretations of PROV-O are also offered:
    * An extension of `prov:Agent` using a new anonymous instance of BFO "role", which is realized by an instance of `prov:Activity`.
    * An extension of `prov:StartedAtTime` and `prov:EndedAtTime` in terms of BFO "temporal region".

* The following PROV namespaces have been (1) mapped in the mapping files, (2) imported in the editors file, and (3) all Turtle-serialized examples from each have been loaded as instances in the editors file for testing:
    * PROV-O [Ontology](http://www.w3.org/ns/prov-o)
    * PROV Access and Query [Ontology](http://www.w3.org/ns/prov-aq) & [Examples](examples/prov-aq-examples.ttl)
    * PROV Data Dictionary [Ontology](http://www.w3.org/ns/prov-dictionary) & [Examples](examples/prov-dictionary-examples.ttl)
    * PROV Linking Across Provenance Bundles [Ontology](http://www.w3.org/ns/prov-links) & [Examples](examples/prov-links-examples.ttl)
    * PROV Inverses [Ontology](http://www.w3.org/ns/prov-o-inverses)
        * Automated entailment of mappings for inverse object properties is supported by OWL-DL and OWL-QL. For example, `prov:wasMemberOf` may be automatically inferred as a subproperty of BFO "part of" because `prov:hadMember` is a subproperty of BFO "has part" and these PROV terms are inverses of each other.
    * PROV Dublin Core <http://www.w3.org/ns/prov-dc#> [Ontology](http://www.w3.org/ns/prov-dc) & [Examples](examples/prov-dc-examples.ttl)

# Methods
### Development
1. A new PROV-BFO extension ontology is created.
2. BFO and RO are temporarily imported into PROV-BFO to help visualize the mappings in the class hierarchy using Protege
    * For testing and reasoning, only terms we use are extracted from RO -- plus their logical dependencies using the [MIREOT](https://www.nature.com/articles/npre.2009.3576.1.pdf) strategy with [OntoFox](https://ontofox.hegroup.org/). 
    * The list of RO terms to import is specified in the [OntoFox_inputs](OntoFox_inputs) folder.
    * The subset of RO extracted from OntoFox is stored locally in the [OntoFox_outputs](OntoFox_outputs) folder. This subset is imported into the editor's version of the mapping.
3. [SPARQL queries](sparql) are used for finding unsubsumed classes and object properties, and also "candidate" superproperties.
4. PROV-BFO adds triples which relate terms from each ontology using the following "mapping" predicates: `rdfs:subClassOf`, `rdfs:subPropertyOf`, and `owl:equivalentClass`, similar to the [PROV-DC extension](https://www.w3.org/ns/prov-dc-directmappings.ttl).

* (Previously, the alignment mappings were added directly to a new version of PROV. These are kept in [this folder](prov-bfo-merged) for historical documentation but should not be used.)

### Metadata
* The mappings should include the following metadata:
    * A justification for each mapping
    * A confidence score for each mapping
* This metadata could be specified either with some standardized annotation properties or an [SSSOM template](https://mapping-commons.github.io/sssom/)
* The SSSOM template is generated from the mapping file using a [SPARQL](sparql) query and the [Makefile](Makefile)

### Testing
* 91 example instances from the [PROV-O documentation](https://www.w3.org/TR/prov-o/) are temporarily imported into the extended ontology
* The HermiT reasoner is used to check if the example instance data is consistent with the extended ontology

* How to run tests automatically:
    * Push commits to this Github repo, and the [Makefile](Makefile) tests will be triggered in Github Workflow files

* How to run tests manually:
    * Requirements: [GNU Make](https://www.gnu.org/software/make/)
    * Run `make` from the PROV directory, or `make -C PROV` from the BFO-Mappings directory

### Release
* Release files are indicated with `-directmappings` prefix in the file names. These are edited and annotated directly, and imported in the Editor's file for development and testing.
* Turtle (.ttl) versions of the release are not currently automatically built in order to ensure precise formatting, but RDF/XML versions could be automatically built from the Turtle versions.
