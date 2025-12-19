# Precision Beekeeping Ontology (PBO) v1.0

This repository contains the OWL developed ontology in Protégé 5.6.5  for precision beekeeping.

The PBO ontology is released under Creative Commons licenses (CC BY-SA) https://creativecommons.org/licenses/by-sa/4.0/.

## Name of the ontology and availability

Precision beekeeping Ontology (PBO) available at https://github.com/paulmushi0018/Precision-Beekeeping-Ontology-PBO-.


## Repository contents
| File | Description |
|------|-------------|
| `Precision_Beekeeping_Ontology_PBO.rdf` |The PBO ontology in OWL/RDF format |
| `README.md`| This summary file | 
| `LICENSE.md`| PBO ontology License file | 
| `Documentation_for_PBO_ontology.pdf` | Full technical documentation of the PBO ontology |

## Ontology features

-Hierarchically defined classes and relationships for:
 -Abstract concepts such as monitoring system status, beehive status, and bee growth stage.
 -Physical object; a Device that includes a smart device, a device part, a power supply system, a network router, protective   gear, harvesting equipment and solar panel.
 -Social object such as the Beekeeper role, bee role, alert, monitoring system, service provider role and Bee colony.
 -Observation and Observation property for temperature, humidity, weight, acceleration, acoustic sound, CO2 concentration,     varroa drop and geo position.

-Alignment with external controlled vocabularies and ontologies:
  - AGROVOC (https://agrovoc.fao.org/browse/agrovoc/en/)
  - GEMET (https://www.eionet.europa.eu/gemet/en/themes/) 
  - DUL (http://www.ontologydesignpatterns.org/ont/dul/DUL.owl)
  - SSN/SOSA (https://w3c.github.io/sdw-sosa-ssn/ssn/)

## SPARQL Query support

6 competence questions were evaluated using SPARQL queries in an Apache Jena Fuseki Server 5.4.0, based on the 74 instances as indicated in the documentation, using data collected from the Kaggle dataset (https://www.kaggle.com/datasets/se18m502/bee-hive-metrics/data). 

The link to video on how to install Apache Jena Fuseki: https://www.youtube.com/watch?v=kwB_53ju4_c

Example query 1: For each hive, what is its most recent recorded weight?

```sparql
PREFIX rdf:  <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX xsd:  <http://www.w3.org/2001/XMLSchema#>
PREFIX pbo:   <http://www.semanticweb.org/paulk/ontologies/2025/10/revisedPBO#>
PREFIX sosa: <http://www.w3.org/ns/sosa/>
SELECT ?hive ?time ?weight
WHERE {
  {
    SELECT ?hive (MAX(?t) AS ?time)
    WHERE {
      ?obs rdf:type pbo:WeightObservation ;
           sosa:hasFeatureOfInterest ?hive ;
           pbo:recordedAt ?t .
    }
    GROUP BY ?hive
  }
  ?obs rdf:type pbo:WeightObservation ;
       sosa:hasFeatureOfInterest ?hive ;
       pbo:recordedAt ?time ;
       pbo:hasWeight ?weight .
}
ORDER BY ?hive
```
Example query 2: On which days did each hive experience strong daily nectar intake, defined as a daily weight increase above a threshold?

```sparql
PREFIX rdf:  <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX xsd:  <http://www.w3.org/2001/XMLSchema#>
PREFIX pbo:   <http://www.semanticweb.org/paulk/ontologies/2025/10/revisedPBO#>
PREFIX sosa: <http://www.w3.org/ns/sosa/>
SELECT ?hive ?day ?startWeight ?endWeight (?endWeight - ?startWeight AS ?dailyChange)
WHERE {
  # 1) For each hive + day, get first and last timestamps
  {
    SELECT ?hive ?day (MIN(?t) AS ?startTime) (MAX(?t) AS ?endTime)
    WHERE {
      ?obs rdf:type pbo:WeightObservation ;
           sosa:hasFeatureOfInterest ?hive ;
           pbo:recordedAt ?t .
      # Optional: limit to a specific period
      FILTER(
        ?t >= "2017-01-01T00:00:00"^^xsd:dateTime &&
        ?t <  "2017-01-31T00:00:00"^^xsd:dateTime
      )
      # Derive calendar day from timestamp
      BIND( xsd:date(?t) AS ?day )
    }
    GROUP BY ?hive ?day
  }
  # 2) Get weight at startTime
  ?startObs rdf:type pbo:WeightObservation ;
            sosa:hasFeatureOfInterest ?hive ;
            pbo:recordedAt ?startTime ;
            pbo:hasWeight ?startWeight .
  # 3) Get weight at endTime
  ?endObs   rdf:type pbo:WeightObservation ;
            sosa:hasFeatureOfInterest ?hive ;
            pbo:recordedAt ?endTime ;
            pbo:hasWeight ?endWeight .

  # 4) Keep only significant gains (e.g. greater than 0 kg)
  FILTER( (?endWeight - ?startWeight) > 0 )
}
ORDER BY ?hive ?day
```

Example query 3: On which days did each hive experience a significant daily weight loss below a given threshold?

```sparql
PREFIX rdf:  <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX xsd:  <http://www.w3.org/2001/XMLSchema#>
PREFIX pbo:   <http://www.semanticweb.org/paulk/ontologies/2025/10/revisedPBO#>
PREFIX sosa: <http://www.w3.org/ns/sosa/>
SELECT ?hive ?day ?startWeight ?endWeight (?endWeight - ?startWeight AS ?dailyChange)
WHERE {
  # 1) For each hive + day, get first and last timestamps
  {
    SELECT ?hive ?day (MIN(?t) AS ?startTime) (MAX(?t) AS ?endTime)
    WHERE {
      ?obs rdf:type pbo:WeightObservation ;
           sosa:hasFeatureOfInterest ?hive ;
           pbo:recordedAt ?t .
      # Optional: limit to a specific period
      FILTER(
        ?t >= "2017-01-01T00:00:00"^^xsd:dateTime &&
        ?t <  "2017-01-31T00:00:00"^^xsd:dateTime
      )
      # Derive calendar day from timestamp
      BIND( xsd:date(?t) AS ?day )
    }
    GROUP BY ?hive ?day
  }
  # 2) Get weight at startTime
  ?startObs rdf:type pbo:WeightObservation ;
            sosa:hasFeatureOfInterest ?hive ;
            pbo:recordedAt ?startTime ;
            pbo:hasWeight ?startWeight .
  # 3) Get weight at endTime
  ?endObs   rdf:type pbo:WeightObservation ;
            sosa:hasFeatureOfInterest ?hive ;
            pbo:recordedAt ?endTime ;
            pbo:hasWeight ?endWeight .

  # 4) Keep only significant loss (e.g. less than 0 kg)
  FILTER( (?endWeight - ?startWeight) < 0 )
}
ORDER BY ?hive ?day
```

Example query 4: Is the Schwartau hive maintaining a stable internal hive temperature?

```sparql
PREFIX sosa: <http://www.w3.org/ns/sosa/>
PREFIX rdf:  <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX xsd:  <http://www.w3.org/2001/XMLSchema#>
PREFIX pbo:   <http://www.semanticweb.org/paulk/ontologies/2025/10/revisedPBO#>
SELECT ?time1 ?temp1 ?time2 ?temp2 ?time3 ?temp3
WHERE {
  # First hour
  ?obs1 a pbo:InternalTemperatureObservation ;
        sosa:hasFeatureOfInterest pbo:Schwartau ;
        pbo:recordedAt ?time1 ;
        pbo:hasTemperature ?temp1 .
  # Second hour = first + 1 hour
  ?obs2 a pbo:InternalTemperatureObservation ;
        sosa:hasFeatureOfInterest pbo:Schwartau ;
        pbo:recordedAt ?time2 ;
        pbo:hasTemperature ?temp2 .
  # Third hour = second + 1 hour
  ?obs3 a pbo:InternalTemperatureObservation ;
        sosa:hasFeatureOfInterest pbo:Schwartau ;
        pbo:recordedAt ?time3 ;
        pbo:hasTemperature ?temp3 .
  FILTER(?time2 = ?time1 + "PT1H"^^xsd:duration)
  FILTER(?time3 = ?time2 + "PT1H"^^xsd:duration)
  # either of three hours is too cold
  FILTER(?temp1 > 20.0 || ?temp2 > 20.0 || ?temp3 > 20.0)
}
ORDER BY ?time1
```

Example query 5: Did the Schwartau hive experience rapid temperature drops (> 2°C per hour), indicating weakening thermoregulation or disturbance?

```sparql
PREFIX sosa:  <http://www.w3.org/ns/sosa/>
PREFIX rdf:   <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX xsd:   <http://www.w3.org/2001/XMLSchema#>
PREFIX pbo:   <http://www.semanticweb.org/paulk/ontologies/2025/10/revisedPBO#>
SELECT ?time1 ?temp1 ?time2 ?temp2 ?deltaTemp
WHERE {
  ?obs1 a pbo:InternalTemperatureObservation ;
        sosa:hasFeatureOfInterest pbo:Schwartau ;
        pbo:recordedAt ?time1 ;
        pbo:hasTemperature ?temp1 .
  ?obs2 a pbo:InternalTemperatureObservation ;
        sosa:hasFeatureOfInterest pbo:Schwartau ;
        pbo:recordedAt ?time2 ;
        pbo:hasTemperature ?temp2 .
  # ensure obs2 is exactly 1 hour after obs1
  FILTER(?time2 = ?time1 + "PT1H"^^xsd:duration)
  # compute temperature change
  BIND((?temp2 - ?temp1) AS ?deltaTemp)
  # keep only strong drops
  FILTER(?deltaTemp < -2.0)
}
ORDER BY ?time1
```

Example query 6: Is the internal temperature showing a consistent upward or downward trend, indicating cluster migration?

```sparql
PREFIX sosa:  <http://www.w3.org/ns/sosa/>
PREFIX rdf:  <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX xsd:  <http://www.w3.org/2001/XMLSchema#>
PREFIX pbo:   <http://www.semanticweb.org/paulk/ontologies/2025/10/revisedPBO#>
SELECT ?avgDay1 ?avgDay2 ?avgDay3
(?avgDay2 - ?avgDay1 AS ?trend1_Day2_Day1)
	   (?avgDay3 - ?avgDay2 AS ?trend2_Day3_Day2)
WHERE {
  # Day 1: e.g. 2017-01-01
  {
    SELECT (AVG(?temp) AS ?avgDay1)
    WHERE {
      ?obs rdf:type pbo:InternalTemperatureObservation ;
           sosa:hasFeatureOfInterest pbo:Schwartau ;
           pbo:recordedAt ?time ;
           pbo:hasTemperature ?temp .
      FILTER( xsd:date(?time) = "2017-01-01"^^xsd:date )
    }
  }

  # Day 2: e.g. 2017-01-02
  {
    SELECT (AVG(?temp) AS ?avgDay2)
    WHERE {
      ?obs rdf:type pbo:InternalTemperatureObservation ;
           sosa:hasFeatureOfInterest pbo:Schwartau ;
           pbo:recordedAt ?time ;
           pbo:hasTemperature ?temp .
      FILTER( xsd:date(?time) = "2017-01-02"^^xsd:date )
    }
  }
  # Day 3: e.g. 2017-01-03
  {
    SELECT (AVG(?temp) AS ?avgDay3)
    WHERE {
      ?obs rdf:type pbo:InternalTemperatureObservation ;
           sosa:hasFeatureOfInterest pbo:Schwartau ;
           pbo:recordedAt ?time ;
           pbo:hasTemperature ?temp .
      FILTER( xsd:date(?time) = "2017-01-03"^^xsd:date )
    }
  }
}

```

