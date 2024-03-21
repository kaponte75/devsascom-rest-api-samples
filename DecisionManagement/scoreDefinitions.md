# Score Definitions API
Score definitions are used by the Score Execution API to generate mapped code, which is then used to generate the score for the input data.

A score definition contains the following details:

1. Input data that needs to be scored.
2. Details about the score object whose logic is used to produce a score.
3. Mappings between the input data columns and the variables of the mapped code of the score object.

#### Why Use this API?
* Users can test the score objects by generating and executing the code.
* Users can use the definition in the score execution to score the input data.
* Can be used in a consistent way for generating a score for the input data using different types of score objects like Decisions and Models.
* Can be used in a consistent way for generating mapped code using different types of score objects such as decisions and models.

## API Request Examples Grouped by Object Type

<details>
<summary>Create a Score Definition</summary>

* [Specifying the type of input data as a CAS table](#specifying-input-data-type-cas-table)
* [Specifying the type of input data as inline](#specifying-input-data-inline)
* [Specifying the type of input data as scenario](#specifying-input-data-scenario)
</details>

<details>
<summary>Update Score Definitions</summary>

* [Replacing all parts of the score definition with new data](#replacing-score-definition-new-data)
</details>

<details>
<summary>Searching a Score Definition</summary>

* [Search by name](#search-by-name)
* [Search by the score object URI](#search-by-uri)
* [Search by the score object type](#search-by-type)
</details>

<details>
<summary>Getting the Mapped Code for Score Objects</summary>

* [Get the mapped code for score objects](#get-mapped-code-score-objects)
</details>

<details>
<summary>Delete a Score Definition</summary>

* [Delete a score definition](#delete-score-definition)
</details>

### Creating a Score Definition
Here are some examples of creating a score definition.
</br>

#### Example 1: <a name='specifying-input-data-type-cas-table'>Specifying the Type of Input Data as a CAS Table</a>
```json
{
  "POST": "/definitions",
  "headers": {
    "Content-Type": "application/vnd.sas.score.definition+json",
    "Accept": "application/vnd.sas.score.definition+json"
  },
  "body": {
    "name": "My Score Definition",
    "objectDescriptor": {
      "uri": "/businessRules/ruleSets/0d25d749-24e0-4e5d-a773-ea7a1eb0fdc5"
    },
    "inputData": {
      "type": "CASTable",
      "serverName": "edmcas",
      "tableName": "BADCAR_DEMO",
      "libraryName": "HPS"
    },
    "mappings": [
      {
        "variableName": "Make",
        "mappingType": "datasource",
        "mappingValue": "make"
      },
      {
        "variableName": "Model",
        "mappingType": "datasource",
        "mappingValue": "model"
      },
      {
        "variableName": "Odometer",
        "mappingType": "datasource",
        "mappingValue": "vehodo"
      },
      {
        "variableName": "Maximum_Mileage",
        "mappingType": "static",
        "mappingValue": 100000
      }
    ]
  }
}
```

#### Example 2: <a name='specifying-input-data-inline'>Specifying the Type of Input Data as Inline</a>
```json
{
  "POST": "/definitions",
  "headers": {
    "Content-Type": "application/vnd.sas.score.definition+json",
    "Accept": "application/vnd.sas.score.definition+json"
  },
  "body": {
    "name": "My Inline Score Definition",
    "objectDescriptor": {
      "uri": "/decisions/flows/17ee66ee-63d6-11e6-8b77-86f30ca893d3"
    },
    "inputData": {
      "type": "Inline",
      "code": "data BADCAR_DEMO; dcl varchar(100) make; dcl varchar(100) model; dcl double vehodo;\nmethod init(); \"make\" = 'HYUNDAI'; \"model\" = 'ELANTRA'; \"vehodo\" = 108811; output;\n\"make\" = 'FORD'; \"model\" = 'FOCUS'; \"vehodo\" = 98038; output; end; enddata;",
      "outputTableName": "CARS"
    },
    "mappings": [
      {
        "variableName": "Make",
        "mappingType": "datasource",
        "mappingValue": "make"
      },
      {
        "variableName": "Model",
        "mappingType": "datasource",
        "mappingValue": "model"
      },
      {
        "variableName": "Odometer",
        "mappingType": "datasource",
        "mappingValue": "vehodo"
      },
      {
        "variableName": "Maximum_Mileage",
        "mappingType": "static",
        "mappingValue": 100000
      }
    ]
  }
}
```

NOTE: For clarity, the example above is shown as YAML below.

```yaml
POST: "/definitions"
headers:
  Content-Type: application/vnd.sas.score.definition+json
  Accept: application/vnd.sas.score.definition+json
body:
  name: My Inline Score Definition
  objectDescriptor:
    uri: "/decisions/flows/17ee66ee-63d6-11e6-8b77-86f30ca893d3"
  inputData:
    type: Inline
    code: 
          data BADCAR_DEMO; 
            dcl varchar(100) make;
            dcl varchar(100) model;
            dcl double vehodo;

            method init();
              "make" = 'HYUNDAI'; 
              "model" = 'ELANTRA'; 
              "vehodo" = 108811; 
              output; 
    
              "make" = 'FORD'; 
              "model" = 'FOCUS'; 
              "vehodo" = 98038; 
              output; 
           end; 
         enddata;
    outputTableName: CARS
  mappings:
  - variableName: Make
    mappingType: datasource
    mappingValue: make
  - variableName: Model
    mappingType: datasource
    mappingValue: model
  - variableName: Odometer
    mappingType: datasource
    mappingValue: vehodo
  - variableName: Maximum_Mileage
    mappingType: static
    mappingValue: 100000
```
#### Example 3: <a name='specifying-input-data-scenario'>Specifying the Type of Input Data as Scenario</a>
```json
{
  "POST": "/definitions",
  "headers": {
    "Content-Type": "application/vnd.sas.score.definition+json",
    "Accept": "application/vnd.sas.score.definition+json"
  },
  "body": {
    "name": "My Scenario Score Definition",
    "objectDescriptor": {
      "uri": "/decisions/flows/17ee66ee-63d6-11e6-8b77-86f30ca893d3"
    },
    "inputData": {
      "type": "Scenario"
    },
    "mappings": [
      {
        "variableName": "Make",
        "mappingType": "static",
        "mappingValue": "HYUNDAI"
      },
      {
        "variableName": "Model",
        "mappingType": "static",
        "mappingValue": "ELANTRA"
      },
      {
        "variableName": "Odometer",
        "mappingType": "static",
        "mappingValue": 108811
      },
      {
        "variableName": "Maximum_Mileage",
        "mappingType": "static",
        "mappingValue": 100000
      },
      {
        "variableName": "RejectCar",
        "mappingType": "expected",
        "mappingValue": true
      }
    ]
  }
}
```

### Updating a Score Definition
Here is an example of <a name='replacing-score-definition-new-data'>replacing all parts of the score definition with new data</a>.

```json
{
  "PUT": "/definitions/{definitionId}",
  "headers": {
    "Content-Type": "application/vnd.sas.score.definition+json",
    "Accept": "application/vnd.sas.score.definition+json",
    "If-Match": "<ETag value that you captured before using either GET or HEAD>"
  },
  "body": {
    "name": "My Score Definition",
    "objectDescriptor": {
      "uri": "/businessRules/ruleSets/0d25d749-24e0-4e5d-a773-ea7a1eb0fdc5"
    },
    "inputData": {
      "type": "CASTable",
      "serverName": "edmcas",
      "tableName": "BADCAR_DEMO",
      "libraryName": "HPS"
    },
    "mappings": [
      {
        "variableName": "Make",
        "mappingType": "datasource",
        "mappingValue": "make"
      },
      {
        "variableName": "Model",
        "mappingType": "datasource",
        "mappingValue": "model"
      },
      {
        "variableName": "Odometer",
        "mappingType": "datasource",
        "mappingValue": "vehodo"
      },
      {
        "variableName": "Maximum_Mileage",
        "mappingType": "static",
        "mappingValue": 50000
      }
    ]
  }
}
```

### Searching a Score Definition
Here are some examples of searching a score definition.
Note: Accept-Item can be application/vnd.sas.summary+json, application/vnd.sas.score.definition+json or application/vnd.sas.score.definition.summary+json 
<br/>
#### Example 1: <a name='search-by-name'>Search by Name</a>
```json
{
  "GET": "/definitions?filter=eq(name,'<name to match>')",
  "headers": {
    "Accept": "application/vnd.sas.collection+json",
    "Accept-Item": "application/vnd.sas.score.definition+json"
  }
}
```

#### Example 2: <a name='search-by-uri'>Search by the Score Object URI</a>
```json
{
  "GET": "/definitions?filter=eq(objectDescriptor.uri,'<uri of the Score Object>')",
  "headers": {
    "Accept": "application/vnd.sas.collection+json",
    "Accept-Item": "application/vnd.sas.score.definition+json"
  }
}
```

#### Example 3: <a name='search-by-type'>Search by the Score Object Type</a>
```json
{
  "GET": "/definitions?filter=eq(objectDescriptor.type,'<type of the Score Object>')",
  "headers": {
    "Accept": "application/vnd.sas.collection+json",
    "Accept-Item": "application/vnd.sas.score.definition+json"
  }
}
```

### Getting the Mapped Code for Score Objects
Here is an example of <a name='get-mapped-code-score-objects'>getting the mapped code for score objects</a>.
```json
{
  "POST": "/definitions/{definitionId}/mappedCode",
  "headers": {
    "Content-Type": "application/vnd.sas.score.code.generation.request+json",
    "Accept": "application/vnd.sas.score.mapped.code+json"
  },
  "body": {
    "hints": {
      "outputLibraryName": "PUBLIC"
    }
  }
}
```

### Deleting a Score Definition
Here is an example of <a name='delete-score-definition'>deleting a score definition</a>/
```json
{
  "DELETE": "/definitions/{definitionId}"
}
```


version 3, last updated 21 March 2024