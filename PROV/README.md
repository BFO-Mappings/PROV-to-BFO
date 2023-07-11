# PROV - BFO Extension
## Contributors
Sydney Cohen, Tim Prudhomme, Alec Sculley, Karl Xie, Giacomo De Colle, Austin Liebers

# Scope
* The mapping goal is to subsume every [PROV-O](https://www.w3.org/TR/prov-o/) term under some [BFO (Basic Formal Ontology)](https://basic-formal-ontology.org/) or [RO (Relation Ontology)](https://oborel.github.io/) term. A separate extension which maps PROV-O to [CCO (Common Core Ontologies)](https://github.com/CommonCoreOntology/CommonCoreOntologies) is also proposed. 
* This mapping aligns PROV-O to these top-level ontologies to promote interoperability between PROV-O users and BFO users.
* At the same time, the mapping is encoded as an extension to maximize compatibility and minimize dependencies for existing PROV-O users.
* Further enhancements to PROV-O are also offered, such as a re-interpretation of `prov:Agent` in terms of an `Agent Role` using `bfo:role` and a re-interpretation of `prov:StartedAtTime` and `prov:EndedAtTime` in terms of `bfo:temporal region`. 

# Methods
### Development
* BFO and RO are temporarily imported into PROV-O to help visualize the mappings in the class hierarchy using Protege
* SPARQL queries are used for finding unsubsumed classes and object properties
* The final result should include only the triples which relate terms from each ontology using the following "mapping" predicates: `rdfs:subClassOf`, `rdfs:subPropertyOf`, and `owl:equivalentClass`, similar to the [PROV-DC extension](https://www.w3.org/ns/prov-dc-directmappings.ttl)

### Metadata
* The mapping should include the following metadata:
    * A justification for each mapping
    * A confidence score for each mapping
* This metadata could be specified either with some standardized annotation properties or an [SSSOM template](https://mapping-commons.github.io/sssom/)

### Testing
1. Example instances from the [PROV-O documentation](https://www.w3.org/TR/prov-o/) are temporarily imported into the extended ontology
2. The HermiT reasoner is used to check if the example instance data is consistent with the extended ontology