Title: YANG NG, 100% compatibility with OpenAPI 3.1
Slug: yang_ng
Date: 2024-04-01 08:00
Modified: 2024-04-01 22:35
Author: ubaumann
Category: April_Fool
Tags: YANG
Summary: The draft for YANG Next Generation was published on April 1st. The new version is fully compatible with OpenAPI 3.1.
preview_img: yang_ng.png



After diligent effort, we are pleased to announce the publication of the inaugural draft of YANG NG (Next Generation) on April 1st. 


> <big><b>TL;DR</b></big>
>
> Thanks to the simplification of YANG NG it is 100% campatable with OpenAPI 3.1!
>
> - Static types
> - Use IEEE 754 binary64 numbers for decimal64
> - ECMA-262 RegEx dialect
> - No context based data validation
> - No runtime model changes
>

![YANG NG]({static}/images/yang_ng.png)

The goal of this document is to outline the primary adaptations required for achieving compatibility between YANG NG and OpenAPI.


## Data types

While YANG 1.1 defines 19 built-in data types, the types specified in the [OpenAPI Specification 3.1](https://spec.openapis.org/oas/v3.1.0) (OAS) are derived from the data types outlined in the [JSON Schema Specification Draft 2020-12](https://datatracker.ietf.org/doc/html/draft-bhutton-json-schema-00) (refer to tables below). In order to ensure compatibility with OAS 3.1, YANG NG facilitates the mapping of its data types to those defined in JSON Schema.

[YANG 1.1 Built-In Types](https://datatracker.ietf.org/doc/html/rfc7950#section-4.2.4)
> | Name                | Description                         |
> |---------------------|-------------------------------------|
> | binary              | Any binary data                     |
> | bits                | A set of bits or flags              |
> | boolean             | "true" or "false"                   |
> | decimal64           | 64-bit signed decimal number        |
> | empty               | A leaf that does not have any value |
> | enumeration         | One of an enumerated set of strings |
> | identityref         | A reference to an abstract identity |
> | instance-identifier | A reference to a data tree node     |
> | int8                | 8-bit signed integer                |
> | int16               | 16-bit signed integer               |
> | int32               | 32-bit signed integer               |
> | int64               | 64-bit signed integer               |
> | leafref             | A reference to a leaf instance      |
> | string              | A character string                  |
> | uint8               | 8-bit unsigned integer              |
> | uint16              | 16-bit unsigned integer             |
> | uint32              | 32-bit unsigned integer             |
> | uint64              | 64-bit unsigned integer             |
> | union               | Choice of member types              |


[JSON Schema Specification Draft 2020-12 Instance Data Model](https://datatracker.ietf.org/doc/html/draft-bhutton-json-schema-00#section-4.2.1)
> | Name    | Description                                                                                  |
> |---------|----------------------------------------------------------------------------------------------|
> | null    | A JSON "null" value                                                                          |
> | boolean | A "true" or "false" value, from the JSON "true" or "false" value                             |
> | object  | An unordered set of properties mapping a string to an instance, from the JSON "object" value |
> | array   | An ordered list of instances, from the JSON "array" value                                    |
> | number  | An arbitrary-precision, base-10 decimal number value, from the JSON "number" value           |
> | string  | A string of Unicode code points, from the JSON "string" value                                |


The YANG data types ``boolean``, ``empty``, and ``string`` correspond respectively to the JSON Schema types ``boolean``, ``null``, and ``string``. YANG integers without additional restrictions, such as a 'range' statement, can be mapped to the corresponding JSON Schema number type, adhering to the specific restrictions outlined in the provided list.

| Name   | JSON Schema restriction                                              |
|--------|----------------------------------------------------------------------|
| int8   | ``{"minimum": -128, "maximum": 127}``                                |
| int16  | ``{"minimum": -32768, "maximum": 32767}``                            |
| int32  | ``{"minimum": -2147483648, "maximum": 2147483647}``                  |
| int64  | ``{"minimum": 9223372036854775808, "maximum": 9223372036854775807}`` |
| uint8  | ``{"minimum": 0, "maximum": 255}``                                   |
| uint16 | ``{"minimum": 0, "maximum": 65535}``                                 |
| uint32 | ``{"minimum": 0, "maximum": 4294967295}``                            |
| uint64 | ``{"minimum": 0, "maximum": 18446744073709551615}``                  |

### decimal64

The YANG specification defines `decimal64` as follows:

> The value space of decimal64 is the set of numbers that can be obtained by multiplying a 64-bit signed integer by a negative power of ten, i.e., expressible as "i x 10^-n" where i is an integer64 and n is an integer between 1 and 18, inclusively

JSON numbers are represented as IEEE 754 binary64 numbers, which consist of a 64-bit floating point with a sign bit, exponent, and fraction bits. Consequently, **YANG NG will employ IEEE 754 binary64 to represent decimal64**.

### enumeration

To streamline YANG NG, the built-in enumeration type has been eliminated, with its functionality now achievable through string restrictions.


### binary and bits

JSON does not inherently support ``binary`` data. Therefore, in YANG NG, all binary data must be encoded in Base64 format. Additionally, the ``bits`` data type has been removed, and booleans are now recommended for representing flags.


### static types

YANG 1.1's [union type](https://datatracker.ietf.org/doc/html/rfc7950#section-9.12) allows for dynamic typing, where a member can be of any built-in or derived type. However, this flexibility often complicates working with YANG. In **YANG NG, the union type has been removed** without replacement. Instead, a data type remains constant unless explicitly specified as 'null' when unspecified.

### references

With [leafref](https://datatracker.ietf.org/doc/html/rfc7950#section-9.9) in YANG 1.1, it's possible to reference another leaf, which is particularly useful when specifying an interface. However, with the default setting of "instance-identifier", validation fails if the referenced leaf does not exist. Unlike YANG, OAS, and JSON Schema do not support validation of data from other attributes. Although there was a [proposal](https://github.com/json-schema-org/json-schema-spec/issues/51) for a ``$data`` keyword in JSON Schema to introduce this capability, it was rejected due to the complexity it would introduce. Implementing such validation adds significant complexity because sub-schemas cannot be validated independently, and the data context needs to be managed. Even if some implementations support the ``$data`` keyword, it's not standardized and cannot be relied upon. YANG NG aims to simplify the model and does not include such a feature. Validation-related business logic must be handled in the application, and in case of errors, please provide understandable error messages.


## Restrictions

YANG NG simplifies and aligns data type restrictions with JSON Schema. The most important changes are outlined in the following subchapters.

### Range

Range statements define the permissible range of a number. Unlike YANG 1.1, YANG NG does not support specifying multiple ranges using the ``|`` syntax.


### Pattern / Regex

The pattern matching in the previous version of YANG was based on the [W3C XML Schema](https://www.w3.org/TR/2004/REC-xmlschema-2-20041028) dialect and utilized in XDS. However, working with these patterns in modern programming languages requires translation for certain features, such as the [Category Escape](https://www.w3.org/TR/2004/REC-xmlschema-2-20041028/#nt-charProp). For instance, in XSD, ``\p{Lu}`` matches an uppercase letter, whereas ``\P{Lu}`` matches everything except an uppercase letter, which would need to be translated to ``r"[A-Z]"`` and ``r"[^A-Z]"``, respectively. JSON Schema, on the other hand, employs the [ECMA-262](https://262.ecma-international.org/11.0/) dialect, also used in JavaScript, which benefits from higher tooling support. Therefore, YANG NG transitions to using the ECMA-262 dialect.


### Derived Types

YANG NG still permits the creation of derived data types. In YANG 1.1, inherited restrictions are combined using the AND logic. However, after extensive discussions, it was determined that in YANG NG, a newly defined restriction overrides any previous restrictions. If the previous behavior is desired, the AND-linked syntax for the new keyword can be generated with additional tooling to support the YANG model developer.


## Choices

While choices are still supported in YANG NG through its alignment with the functionality of JSON Schema's ``oneOf``, it is advisable to exercise caution in their use due to the potential introduction of complexity. Although choices enable the allowance of different data types for the same properties, it is not considered best practice.


## Conditions

It's worth noting a significant change from YANG 1.1: YANG NG will no longer allow runtime changes to the model. Unlike YANG 1.1, which permitted altering the model using the ``when`` statement, YANG NG maintains consistency and integrity by enforcing static data types throughout the model. This adjustment enhances model predictability and reliability.

## xPath

To achieve full compatibility with OpenAPI and JSON Schema, YANG NG will introduce a new path syntax that combines the features of both [xPath (W3C)](https://www.w3schools.com/xml/xpath_syntax.asp) and [JSON Pointer (RFC 6901)](https://datatracker.ietf.org/doc/html/rfc6901). This change will streamline integration and development processes, facilitating smoother interactions between systems.

## Conclusion

This is an [April Fool](https://en.wikipedia.org/wiki/April_Fools%27_Day)'s prank. However, the complexity of working with YANG can indeed be challenging. If we view SNMP as the first attempt, YANG represents a subsequent approach. Let's hope that future endeavors will yield more practical solutions. 

For any discussions reach out on [Linkedin](https://www.linkedin.com/in/ubaumannch/), [GitHub Discussions](https://github.com/ubaumann/infrastructureascode/discussions) or [X (Twitter)](https://twitter.com/InfraAsCode).
