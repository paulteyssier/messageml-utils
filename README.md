[![Build Status](https://travis-ci.org/symphonyoss/messageml-utils.svg)](https://travis-ci.org/symphonyoss/messageml-utils)
[![Dependencies](https://www.versioneye.com/user/projects/59260c2c25feb9004d428b63/badge.svg?style=flat-square)](https://www.versioneye.com/user/projects/59260c2c25feb9004d428b63)
[![Validation Status](https://scan.coverity.com/projects/12785/badge.svg?flat=1)](https://scan.coverity.com/projects/symphonyoss-messageml-utils)
[![Test Coverage](https://codeclimate.com/github/symphonyoss/messageml-utils/badges/coverage.svg)](https://codeclimate.com/github/symphonyoss/messageml-utils/coverage)

# Introduction

MessageML is a markup language used by the Symphony Agent API for representing messages, including formatting (bold, italic, numbered and unnumbered lists etc.) 
and entity data representing _structured objects_. 

The format is intended to allow third parties to create and render rich content messages representing complex objects, 
enabling deep workflow integrations across multiple systems, with Symphony being the central hub.

# Description

Support of Symphony structured objects comprises the ability to parse and render the following formats:

* [PresentationML](https://symphonyoss.atlassian.net/wiki/display/WGFOS/PresentationML), 
a strict subset of HTML5 describing the markup which controls the formatting (presentation) of a message. 
PresenttionML is the format used internally by Symphony and returned from message ingestion and retrieval by the Agent API.
* [MessageML](https://symphonyoss.atlassian.net/wiki/display/WGFOS/MessageML), 
a superset of PresentationML, adding a number of convenience tags for more complex or frequently used constructs.
It is translated to PresentationML internally by the Agent API before ingestion in Symphony.
* [EntityJSON](https://symphonyoss.atlassian.net/wiki/display/WGFOS/EntityJSON), 
JSON data representing complex financial objects and other structured data. 
EntityJSON elements are referenced in PresentationML and are provided by the message originator 
or generated automatically during the expansion of MessageML convenience tags.
* [Freemarker](http://freemarker.org/),
a templating language providing macros (conditional blocks, iterations, assignments etc.) 
to expand message templates into MessageML messages.
* [Markdown](http://daringfireball.net/projects/markdown/syntax),
the legacy text format used by Symphony. 
Markdown parsing and rendering capabilities are provided for compatibility with older versions of Symphony.

More specifically, the MessageML parser can be used to:

* validate the input message as correct MessageML or PresentationML and the input data as correct EntityJSON; 
if the input message contains references to entity data, the validation process verifies if the provided data matches those references 
* expand Freemarker templates into MessageML messages; this includes processing Freemarker macros 
and injecting EntityJSON data referenced by template variables
* construct a document tree representing the message
* serialize the MessageML tree as PresentationML; this step includes generation of EntityJSON data from convenience MessageML tags
* serialize the MessageML tree as Markdown
* parse Markdown input into a MessageML document tree

# Usage

```java
/* Instantiate the parsing context. The "dataProvider" object is used to resolve user mentions and check supplied URLs against a whitelist of supported URI schemes. */
MessageMLContext context = new MessageMLContext(/*IDataProvider*/ dataProvider);

/* Parse the message and entity data */
context.parseMessageML(/*String*/ message, /*String*/ entityJSON, /*String*/ version);

/* Parse a Markdown message into a MessageML document tree */
context.prseMarkdown(/*String*/ markdown, /*JsonNode*/ entities);

/* Get the MessageML document tree */
Element messageML = context.getMessageML();

/* Get the PresenttionML representation of the message */
String presentationML = context.getPresentationML();

/* Get the entity data of the message, including data auto-generated from MessageML convenience tags*/
String entityJSON = context.getEntityJson();

/* Get the Markdown representation of the message */
String markdown = context.getMarkdown();
```