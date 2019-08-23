# ParseMultiLevelJSON-NiFiRecordProcessors
How to parse multi level JSON with NiFI and Avro using Record Processors

This guide will describe how to take a nested or multi-level JSON document, and flatten it to a simpler JSON document using Avro, NiFi and Record Processors.

The input JSON looks sometime like: 
```
{
    "transaction": {
        "GEO": {
			"location": "51.5017398,-0.1386134",
			"address": "Westminster, London, SW1A 0AA",
			"landline": "020 7219 4272"
			},
		"amount": "10",
		"currency": "GBP",
		"ts": "2019-08-16 23:54:59.682761"
	}
}
```

And you would like to flatten it out so that it looks like:
```
{
  "location" : "51.5017398,-0.1386134",
  "address" : "Westminster, London, SW1A 0AA",
  "landline" : "020 7219 4272",
  "amount" : "10",
  "currency" : "GBP",
  "ts" : "2019-08-16 23:54:59.682761"
}
```

In NiFi you can use a combination of Record Processors and Avro schemas to define the complex structure, and simplified structure. You can also use the QueryRecord processor using RPATH to access deeper elements of the complex JSON document to to create a simplified JSON output. 

An example flow would look like:
![alt text](https://github.com/willie-engelbrecht/ParseMultiLevelJSON-NiFiRecordProcessors/blob/master/images/FlowDesign.JPG "Sample NiFi Canvas")

The ValidateRecord checks that the input JSON file is valid, using the following Avro schema:
```
{   
  "type" : "record",   
  "name" : "outerschema",   
  "fields" : [ 
    { "name" : "transaction" , "type" : { 
				"type": "record",
				"name" : "middleschema",
				"fields" : [
					{	"name": "GEO", "type" : { 
								"type": "record",
								"name" : "innerschema",
								"fields" : [
									{ "name": "location", "type" : "string" },
									{ "name": "address", "type" : "string" },
									{ "name": "landline", "type" : "string" }
								]
						}
					},
					{"name": "amount", "type": "string"},
					{"name": "currency", "type": "string"},
					{"name": "ts", "type": "string"}							
				]
			}
		}	
	]
}
```

