# How to retrieve documents from ArangoDB without knowing the structure? 

## Problem
If you use a NoSQL database it's common to retrieve documents with an unknown attribute structure. Furthermore, the amount and types of attributes may differ in documents resulting from a single query. Another problem is that you want to add one ore more attributes to a document.
 
In Java you are used to work with objects. Regarding the upper requirements it is possible to directly retrieve objects with the same attribute structure as the document out of the database. Adding attributes to an object at runtime could be very messy.

**Note**: Arango 2.4 and the corresponding javaDriver is needed.

## Solution
With the latest version of the Java driver of ArangoDB an object called `BaseDocument` is provided.

The structure is very simple: It only has four attributes:

``` java
public class BaseDocument {

    long documentRevision;
    String documentHandle;
    String documentKey;
    Map<String, Object> properties;
}
```

The first three attributes are the system attributes `_rev`, `_id` and `_key`. The fourth attribute is a `HashMap`. The key always is a String, the value an object. These properties contain all non system attributes of the document. 

The map can contain values of the following types:

* HashMap `<String, Object>`
* ArrayList `<Object>`
* Boolean
* Double
* String
* null

**Note**: `HashMap` and ArrayList contain objects, which are of the same types as listed above.

To retrieve a document is similar to the known procedure, except that you use `_BaseDocument_` as type.
 
``` java
DocumentEntity<BaseDocument> myObject = driver.getDocument(myDocument.getDocumentHandle(), BaseDocument.class);
```

**Author**: [gschwab](https://github.com/gschwab)

**Tags**: #java #driver