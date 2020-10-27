# DevNet Asscociate

Developing Applications and Automating Workflows using Cisco Core Platforms (DEVASC) v1.0 helps prepare you for the DevNet Associate certification and for associate-level network automation engineer roles. 

## Introduction

Things you'll be learning:

* Service Provider architectures, concepts, and transport technologies. 
* IOS software architecture, main IOS and their differences
* Implement OSPF, integrated IS-IS, BGP, route maps, and routing policy language in ISP network
* HA mechanisms in IOS XR
* segment routing and segment routing traffic engineering concepts
* MPLS L2VPN and MPLS L3VPN for ISP
* QoS Architecture and Benefits for SP
* YANG Data Models
* Automation and assurance tools and protocols
* Roles of Cisco NSO in SP enviroments
* Virtualization in SP

## This section

* APIs
    + Data Format
* Serial and Deserialization
* Collaborative Software Dev
    + Version control
    + Git

### Major Dev Platforms

* Web apps
* Mobile Apps
* Integration with Social Media
* Cloud services
* Free software/libraries

## Common API Data Formats

* YAML Ain't Markup Language (YAML)
* JavaScript Object Notation (JSON)
* eXtensible Markup Language (XML)

| Data Format | Most Common Use|
|:-:|:-:|
|XML| Transformation with XSL, Applying XML Schemas|
|JSON| Communication Server/web page, Configuration files|
|YAML| Configuration |

XML is considered no so humanly readable. It's verbose, redundant, and could be complex to use. It is mostly used to interexchange highly structured data between machines. XML is commonly used in conjunction with Java.

JSON is a little easier to read and generally transmit data between a server and web page. JSON syntax is the same as JavaScript programming lang, therefore, you can easily conver JSON to JavaScript objects. JSON is also basically a subset of YAML, therefore parsing JSON files with YAML is simpler.

YAML is best for writing your first API from scratch. YAML also generally resonated well with individuals stronger in Python.

## YAML

* Whitespace is significant
* All data inside an object is at the same indentation level
* Tab indentations are not allowed in YAML as they are treated by different tools
* /- denotes lists

## XML

### XML Namespaces

## JSON

### Example API Data

```xml
<note>
<to>Tove</to>
<from>Jani</from>
<heading>Reminder</heading>
<body>Don't forget me this weekend!</body>
</note>
```

```json
{
    "glossary": {
        "title": "example glossary",
		"GlossDiv": {
            "title": "S",
			"GlossList": {
                "GlossEntry": {
                    "ID": "SGML",
					"SortAs": "SGML",
					"GlossTerm": "Standard Generalized Markup Language",
					"Acronym": "SGML",
					"Abbrev": "ISO 8879:1986",
					"GlossDef": {
                        "para": "A meta-markup language, used to create markup languages such as DocBook.",
						"GlossSeeAlso": ["GML", "XML"]
                    },
					"GlossSee": "markup"
                }
            }
        }
    }
}
```

```yaml
--- !clarkevans.com/^invoice
invoice: 34843
date   : 2001-01-23
bill-to: &id001
    given  : Chris
    family : Dumars
    address:
        lines: |
            458 Walkman Dr.
            Suite #292
        city    : Royal Oak
        state   : MI
        postal  : 48046
ship-to: *id001
product:
    - sku         : BL394D
      quantity    : 4
      description : Basketball
      price       : 450.00
    - sku         : BL4438H
      quantity    : 1
      description : Super Hoop
      price       : 2392.00
tax  : 251.42
total: 4443.52
comments: >
    Late afternoon is best.
    Backup contact is Nancy
    Billsmer @ 338-4338.
```

## Serialization and Deserialization

Transporting over a medium, when you speak into your cell phone, your phone literally serializes into bits for the cell phone to comprehend.