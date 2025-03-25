---

title: "JSON Structure: Symbols, Scientific Units, and Currencies"
abbrev: JSON Structure Units"
category: info

docname: draft-vasters-httpapi-json-structure-units-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date: 2025-03-24
consensus: true
v: 3
area: ""
workgroup: "Building Blocks for HTTP APIs"
keyword:
 - JSON
 - schema
venue:
  group: "Building Blocks for HTTP APIs"
  type: ""
  mail: "httpapi@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/httpapi/"
  github: "json-structure/units"
  latest: "https://json-structure.github.io/units/draft-vasters-httpapi-json-structure-units.html"

author:
 -
    fullname: Clemens Vasters
    organization: Microsoft Corporation
    email: clemensv@microsoft.com

normative:
  RFC2119:
  RFC4646:
  RFC8174:
  IEEE_260.1_2024:
    author:
    - org: Institute of Electrical and Electronics Engineers
    date: May 2024
    seriesinfo:
      DOI: 10.1109/IEEESTD.2024.10530229
      IEEE: 260-1-2024
    target: https://ieeexplore.ieee.org/document/10530229
    title: IEEE Standard for Letter Symbols for Measurement Units (SI and Other Common Units)
  NIST_HB_44_2023:
    author:
    - ins: Butcher
      name: Tina G Butcher
      org: Physical Measurement Laboratory
    - org: National Institute of Standards and Technology
    date: '2023'
    seriesinfo:
      DOI: 10.6028/NIST.HB.44-2023
      NIST Handbooks: 44-2023
    target: https://nvlpubs.nist.gov/nistpubs/hb/2023/NIST.HB.44-2023.pdf
    title: Specifications, Tolerances, and Other Technical Requirements for Weighing and Measuring Devices
  IEC_80000-13_2025:
    author:
    - org: International Electrotechnical Commission
    date: '2025'
    seriesinfo:
      IEC Standards: IEC 80000-13:2025
    target: https://webstore.iec.ch/publication/IEC80000-13-2025
    title: "Quantities and units \u2013 Part 13: Information science"
  IEC_80000-1_2025:
    author:
    - org: International Electrotechnical Commission
    date: '2025'
    seriesinfo:
      IEC Standards: IEC 80000-1:2025
    target: https://webstore.iec.ch/publication/IEC80000-1-2025
    title: "Quantities and units \u2013 Part 1: General"
  IEC_80000-3_2025:
    author:
    - org: International Electrotechnical Commission
    date: '2025'
    seriesinfo:
      IEC Standards: IEC 80000-3:2025
    target: https://webstore.iec.ch/publication/IEC80000-3-2025
    title: "Quantities and units \u2013 Part 3: Space and time"
  IEC_80000-4_2025:
    author:
    - org: International Electrotechnical Commission
    date: '2025'
    seriesinfo:
      IEC Standards: IEC 80000-4:2025
    target: https://webstore.iec.ch/publication/IEC80000-4-2025
    title: "Quantities and units \u2013 Part 4: Electricity and magnetism"
  ISO_4217_2015:
    author:
    - org: International Organization for Standardization
    date: '2015'
    seriesinfo:
      ISO Standards: ISO 4217:2015
    target: https://www.iso.org/standard/42245.html
    title: Codes for the representation of currencies and funds
  JSTRUCT-CORE:
    title: "JSON Structure Core"
    author:
    - fullname: Clemens Vasters
    target: https://json-structure.github.io/core/draft-vasters-httpapi-json-structure-core.html

informative:


--- abstract

This document specifies "JSON Structure Symbols, Scientific Units, and
Currencies", an extension to JSON Structure Core. This specification defines a
set of annotation keywords for associating scientific unit and currency metadata
and constraints, primarily for use with numeric values.

JSON Structure Scientific Units provides a mechanism for schema authors to
explicitly declare the unit associated with numeric data, thereby enabling
precise mapping between schema representations and external data systems.

--- middle

# Introduction {#introduction}

This document is a companion specification to JSON Structure Core
{{JSTRUCT-CORE}}. It defines annotation keywords that allow numeric types to be
enriched with measurement unit or currency information.

The primary purpose of this extension is to help numeric values be interpreted
consistently by specifying their associated scientific units or currencies.

This specification defines the syntax and semantics of the keywords that
annotate numeric types with scientific unit or currency information.
Implementations of JSON Structure Core that support this extension MUST process
these keywords according to the rules defined herein.

# Conventions {#conventions}

{::boilerplate bcp14}

# Symbol Annotations {#symbol-annotations}

This section defines the keywords used to annotate schema elements with symbols
that accompany values when presented to users.

## The `symbol` Keyword {#symbol-keyword}

The `symbol` keyword provides a mechanism for annotating a schema element with a
symbol that annotates the value of the element when presented to users.

The keyword MAY appear alongside the `type` keyword in object properties or
array items or map values.

- The value of `symbol` MUST be a JSON string.
- The string value of `symbol` SHOULD contain a Unicode character or a
  multi-character symbol that represents the value of the annotated element.
- The `symbol` keyword MAY be used as an annotation on any schema element.
- `symbol` MAY be used in conjunction with `unit` or `currency` annotations or
  independently.

Example:

~~~json
{
  "type": "number",
  "currency": "EUR",
  "symbol": "€"
}
~~~

or

~~~json
{
  "type": "number",
  "unit": "m/s^2",
  "symbol": "m/s²"
}
~~~

or

~~~json
{
  "type": "number",
  "description": "Number of bunnies",
  "symbol": "bunnies"
}
~~~

### The `symbols` Keyword {#symbols-keyword}

The `symbols` keyword provides a mechanism for annotating a schema element with
a set of symbols that annotate the value of the element when presented to users.

The keyword MAY appear alongside the `type` keyword in object properties or
array items or map values.

- The value of `symbols` MUST be a `map`.
- The keys of the `symbols` map MUST be strings that represent a purpose
  indicator. The `lang:` prefix is reserved for language-specific symbols. The
  suffix after the colon specifies the language code. The language code MUST
  conform to the {{RFC4646}} standard.
- The values of the `symbols` map MUST be strings that represent the symbol.
- The `symbols` keyword MAY be used as an annotation on any schema element.

Example:

~~~json
{
  "type": "number",
  "description": "Number of bunnies",
  "symbols": {
    "lang:en": "Bunnies",
    "lang:de": "Kaninchen",
    "lang:fr": "Lapins"
  }
}
~~~

# Scientific Unit Annotations {#scientific-unit-annotations}

This section defines the keywords used to annotate numeric types with scientific
unit information.

## The `unit` Keyword {#unit-keyword}

The `unit` keyword provides a mechanism for annotating a numeric schema (or a
schema based on a numeric extended type such as `number`, `int32`, `uint32`,
`int64`, `uint64`, `int128`, `uint128`, `float`, `double`, or `decimal`) with
its measurement unit.

The keyword MAY appear alongside the `type` keyword in object properties or
array items or map values.

- The value of `unit` MUST be a JSON string.
- The string value of `unit` SHOULD contain:
  - An SI unit symbol or derived unit symbol conforming to the Bureau
    International des Poids et Mesures (BIPM) International System of Units (SI)
    {{IEEE_260.1_2024}}
  - A unit symbol defined in ISO/IEC 80000 series {{IEC_80000-1_2025}}
  - A non-SI unit symbol defined in NIST Handbook 44 Appendix C
    {{NIST_HB_44_2023}}

For "derived" SI units that reflect a multiplication, the unit symbols MUST be
separated by the asterisk character (`*`). For derived units that reflect a
division, the unit symbols MUST be separated by the forward slash (`/`). The
notation for exponentiation MUST be indicated using the caret (`^`). For
example, acceleration SHALL be denoted as `"m/s^2"`.

Units that use Greek-language symbols (including supplementary or derived units)
such as Ohm (`"Ω"`) MUST be denoted with those Greek symbols (using the
corresponding Unicode code points).

The `unit` keyword MAY be used as an annotation on any schema element whose
underlying type is numeric. Schema processors that support JSON Structure
Scientific Units MUST use the value of the `unit` keyword to interpret, convert,
or display numeric values appropriately.

Example:

~~~json
{
  "type": "number",
  "unit": "m/s^2"
}
~~~

## Unit Annotations {#unit-annotations}

This is a list of common scientific units that MAY be used with the `unit`.
Units are defined according to ISO/IEC 80000, BIPM SI, and NIST HB44:

| Measure               | `unit`  | Description                             | Reference    |
| --------------------- | ------- | --------------------------------------- | ------------ |
| Length                | `m`     | Meters, SI unit of length               | {{IEC_80000-3_2025}} |
| Velocity              | `m/s`   | Meters per second                       | {{IEC_80000-3_2025}} |
| Acceleration          | `m/s^2` | Meters per second squared               | {{IEC_80000-3_2025}} |
| Weight                | `kg`    | Kilograms, SI unit of mass              | {{IEC_80000-4_2025}} |
| Time                  | `s`     | Seconds, SI unit of time                | {{IEEE_260.1_2024}} |
| Temperature           | `K`     | Kelvin, SI unit of temperature          | {{IEEE_260.1_2024}} |
| Volume                | `L`     | Liters, non-SI unit accepted in SI      | {{IEEE_260.1_2024}} |
| Pressure              | `psi`   | Pounds per square inch, non-SI unit     | {{NIST_HB_44_2023}} |
| Energy                | `J`     | Joules, SI unit of energy               | {{IEEE_260.1_2024}} |
| Power                 | `W`     | Watts, SI unit of power                 | {{IEEE_260.1_2024}} |
| Electrical Resistance | `Ω`     | Ohms, SI unit of electrical resistance  | {{IEEE_260.1_2024}} |
| Electrical Current    | `A`     | Amperes, SI unit of electric current    | {{IEEE_260.1_2024}} |
| Light Intensity       | `cd`    | Candelas, SI unit of luminous intensity | {{IEEE_260.1_2024}} |
| Area                  | `m^2`   | Square meters, SI unit of area          | {{IEEE_260.1_2024}} |
| Volume                | `m^3`   | Cubic meters, SI unit of volume         | {{IEEE_260.1_2024}} |
| Length                | `ft`    | Feet, non-SI unit                       | {{NIST_HB_44_2023}} |
| Volume                | `gal`   | Gallon, non-SI unit                     | {{NIST_HB_44_2023}} |
| Pressure              | `bar`   | Bar, non-SI unit                        | {{NIST_HB_44_2023}} |
| Digital Storage       | `B`     | Bytes, non-SI unit                      | {{IEC_80000-13_2025}} |
| Data Rate             | `bit/s` | Bits per second                         | {{IEC_80000-13_2025}} |

## Unit Prefixes {#unit-prefixes}

The following SI prefixes MAY be used with base units:

| Prefix | Symbol | Factor |
| ------ | ------ | ------ |
| yotta  | Y      | 10²⁴   |
| zetta  | Z      | 10²¹   |
| exa    | E      | 10¹⁸   |
| peta   | P      | 10¹⁵   |
| tera   | T      | 10¹²   |
| giga   | G      | 10⁹    |
| mega   | M      | 10⁶    |
| kilo   | k      | 10³    |
| milli  | m      | 10⁻³   |
| micro  | μ      | 10⁻⁶   |
| nano   | n      | 10⁻⁹   |
| pico   | p      | 10⁻¹²  |
| hecto  | h      | 10²    |

Examples:

- `"km"`: Kilometers
- `"mm"`: Millimeters
- `"μm"`: Micrometers
- `"nm"`: Nanometers
- `"ps"`: Picoseconds
- `"mΩ"`: Milliohms
- `"kΩ"`: Kilohms
- `"MW"`: Megawatts

# Currency Annotations {#currency-annotations}

This section defines the keywords used to annotate numeric types with currency
information.

## The `currency` Keyword {#currency-keyword}

The `currency` keyword provides a mechanism for annotating a numeric schema (or
a schema based on a numeric extended type such as `number`, `int32`, `uint32`,
`int64`, `uint64`, `int128`, `uint128`, `float`, `double`, or `decimal`) with a
currency annotation.

The keyword MAY appear alongside the `type` keyword in object properties or
array items or map values.

- The value of `currency` MUST be a JSON string.
- The string value of `currency` SHOULD contain a three-letter currency code
  conforming to the {{ISO_4217_2015}} standard.
- The `currency` keyword MAY be used as an annotation on any schema element.

Example:

~~~json
{
  "type": "number",
  "currency": "EUR"
}
~~~

# Security and Interoperability Considerations {#security-and-interoperability-considerations}

Alternate unit annotations do not affect the fundamental validation of instance
data. They are purely metadata and MUST be ignored by validators that do not
support this extension. Applications that rely on unit annotations for
conversion or display MUST implement appropriate validation against recognized
standards (BIPM SI and NIST HB44) to ensure consistency.

# IANA Considerations {#iana-considerations}

This document has no IANA actions.

--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
