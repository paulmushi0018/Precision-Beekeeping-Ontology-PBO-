# Precision Beekeeping Ontology (PBO) v1.0

This repository contains the OWL developed ontology in Protégé 5.6.5  for precision beekeeping.

The PBO ontology is released under Creative Commons licenses (CC BY-SA) https://creativecommons.org/licenses/by-sa/4.0/.

## Name of the ontology and availability

Precision beekeeping Ontology (PBO) available at https://github.com/paulmushi0018/Precision-Beekeeping-Ontology-PBO-.


## Repository contents
| File | Description |
|------|-------------|
| `precision_beekeeping_ontology_v1.rdf` |The PBO ontology in OWL/RDF format |
| `README.md`| This summary file | 
| `LICENSE.md`| PBO ontology License file | 
| `Documentation_for_PBO_ontology.pdf` | Full technical documentation of the PBO ontology |

## Ontology features

-Hierarchically defined classes and relationships for:
 -bee (bee biodiversity and bee biology)
 -beekeeping practices (beekeeping equipment,beekeeping establishment,beekeeping types, beekeeping materials, beekeeping systems, bee reserves and calendar)
 -bee products and services (bee products processing, bee products quality, bee services and hive products)
 -challenges to bee biodiversity (biological threats, environmental and anthropogenic threats, pathological threats and colony collapse disorder)
 -Beekeeper
-Alignment with external controlled vocabularies such as:
  - AGROVOC (https://agrovoc.fao.org/browse/agrovoc/en/) (e.g `Honey`, `Pollen`)
  - GEMET (https://www.eionet.europa.eu/gemet/en/themes/) (e.g `Bee`)

## SPARQL Query support

10 competence questions were evaluated using SPARQL queries in an Apache Jena Fuseki Server 5.4.0. 

The link to video on how to install Apache Jena Fuseki: https://www.youtube.com/watch?v=kwB_53ju4_c

Example query 1: What are the products produced by the honey bees?

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT ?instance ?label
WHERE {
  ?instance rdf:type <https://agrovoc.fao.org/browse/agrovoc/en/page/c_3655> .
  FILTER NOT EXISTS {
    ?instance rdf:type ?subclass .
    ?subclass rdfs:subClassOf <https://agrovoc.fao.org/browse/agrovoc/en/page/c_3655> .
    FILTER (?subclass != <https://agrovoc.fao.org/browse/agrovoc/en/page/c_3655>)
  }
 OPTIONAL {?instance rdfs:label ?label}
}
```
Example query 2: What are the subspecies of the stinging bees, Genus Apis (True honey bees) and others?

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT ?instance ?label
WHERE {
  ?instance rdf:type <https://agrovoc.fao.org/browse/agrovoc/en/page/c_533> .
  FILTER NOT EXISTS {
    ?instance rdf:type ?subclass .
    ?subclass rdfs:subClassOf <https://agrovoc.fao.org/browse/agrovoc/en/page/c_533> .
    FILTER (?subclass != <https://agrovoc.fao.org/browse/agrovoc/en/page/c_533>)
  }
 OPTIONAL {?instance rdfs:label ?label}
}
```

Example query 3: What are the types of equipment and tools used in precision beekeeping?

```sparql
PREFIX pbo: <http://www.semanticweb.org/paulk/ontologies/2025/4/untitled-ontology-43#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT ?instance ?label
WHERE {
  ?instance rdf:type pbo:BeekeepingEquipment .
  FILTER NOT EXISTS {
    ?instance rdf:type ?subclass .
    ?subclass rdfs:subClassOf pbo:BeekeepingEquipment .
    FILTER (?subclass != pbo:BeekeepingEquipment)
  }
  OPTIONAL {?instance rdfs:label ?label}
}
```

Example query 4: What are the different types of sensors needed in precision beekeeping?

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT ?instance ?label
WHERE {
  ?instance rdf:type <https://agrovoc.fao.org/browse/agrovoc/en/page/c_28279> .
  FILTER NOT EXISTS {
    ?instance rdf:type ?subclass .
    ?subclass rdfs:subClassOf <https://agrovoc.fao.org/browse/agrovoc/en/page/c_28279> .
    FILTER (?subclass != <https://agrovoc.fao.org/browse/agrovoc/en/page/c_28279>)
  }
  OPTIONAL {?instance rdfs:label ?label}
}
```

Example query 5: What are the beekeeping types?

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX pbo: <http://www.semanticweb.org/paulk/ontologies/2025/4/untitled-ontology-43#>

SELECT ?instance ?label
WHERE {
  ?instance rdf:type pbo:Beekeeping .
  FILTER NOT EXISTS {
    ?instance rdf:type ?subclass .
    ?subclass rdfs:subClassOf pbo:Beekeeping .
    FILTER (?subclass != pbo:Beekeeping)
  }
  OPTIONAL {?instance rdfs:label ?label}
}
```

Example query 6: What are the types of modern beehives?

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX pbo: <http://www.semanticweb.org/paulk/ontologies/2025/4/untitled-ontology-43#>

SELECT ?instance ?label
WHERE {
  ?instance rdf:type pbo:ModernBeehive .
  FILTER NOT EXISTS {
    ?instance rdf:type ?subclass .
    ?subclass rdfs:subClassOf pbo:ModernBeehive .
    FILTER (?subclass != pbo:ModernBeehive)
  }
  OPTIONAL {?instance rdfs:label ?label}
}
```

Example query 7: What are the types of beekeeping systems?

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX pbo: <http://www.semanticweb.org/paulk/ontologies/2025/4/untitled-ontology-43#>

SELECT ?instance ?label
WHERE {
  ?instance rdf:type pbo:BeekeepingSystems .
  FILTER NOT EXISTS {
    ?instance rdf:type ?subclass .
    ?subclass rdfs:subClassOf pbo:BeekeepingSystems .
    FILTER (?subclass != pbo:BeekeepingSystems)
  }
  OPTIONAL {?instance rdfs:label ?label}
}
```

Example query 8: What are the challenges/threats to Bee Biodiversity?

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX pbo: <http://www.semanticweb.org/paulk/ontologies/2025/4/untitled-ontology-43#>

SELECT ?instance ?label
WHERE {
  ?instance rdf:type pbo:ChallengesToBeeBiodiversity .
  FILTER NOT EXISTS {
    ?instance rdf:type ?subclass .
    ?subclass rdfs:subClassOf pbo:ChallengesToBeeBiodiversity .
    FILTER (?subclass != pbo:ChallengesToBeeBiodiversity)
  }
  OPTIONAL {?instance rdfs:label ?label}
}
```

Example query 9: What are the bee human-centric services?

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX pbo: <http://www.semanticweb.org/paulk/ontologies/2025/4/untitled-ontology-43#>

SELECT ?instance ?label
WHERE {
  ?instance rdf:type pbo:BeeHumanCentricServices .
  FILTER NOT EXISTS {
    ?instance rdf:type ?subclass .
    ?subclass rdfs:subClassOf pbo:BeeHumanCentricServices .
    FILTER (?subclass != pbo:BeeHumanCentricServices)
  }
  OPTIONAL {?instance rdfs:label ?label}
}
```

Example query 10: What are the bee ecosystem services?

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX pbo: <http://www.semanticweb.org/paulk/ontologies/2025/4/untitled-ontology-43#>

SELECT ?instance ?label
WHERE {
  ?instance rdf:type pbo:BeeEcosystemServices .
  FILTER NOT EXISTS {
    ?instance rdf:type ?subclass .
    ?subclass rdfs:subClassOf pbo:BeeEcosystemServices .
    FILTER (?subclass != pbo:BeeEcosystemServices)
  }
  OPTIONAL {?instance rdfs:label ?label}
}
```
