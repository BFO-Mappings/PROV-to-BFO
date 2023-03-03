# BFO-Mappings

This repo is intended to be a sandbox for mapping W3C rdf standards into Basic Formal Ontology. 

# Imports
* Imported terms are specified in text files in each `OntoFox_inputs` folder
* These text files are generated using https://ontofox.hegroup.org/
* Upload the text files to OntoFox in the "Data input using local text file" section
* OntoFox will generate an OWL file which should be saved to the `OntoFox_outputs` folder (TODO: this will be automated)
* Import the OntoFox output OWL file into the domain ontology
    * Protege will find this file based on its IRI specified in the `owl:Ontology rdf:about` attribute