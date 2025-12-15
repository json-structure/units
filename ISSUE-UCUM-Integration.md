# Issue: Integration of UCUM (Unified Code for Units of Measure)

**Status:** Proposal  
**Date:** 2025-12-15  
**Category:** Enhancement  
**Priority:** Medium

## Summary

This issue proposes integrating support for UCUM (Unified Code for Units of Measure) into the JSON Structure Units specification. UCUM is a widely-adopted standard for representing units of measure in a formal, machine-parseable format, particularly in healthcare (HL7, FHIR) and scientific computing domains.

## Background

### Current Approach

The current JSON Structure Units specification defines the `unit` keyword to annotate numeric values with measurement units. The specification references:
- SI units (BIPM International System of Units, IEEE 260.1-2024)
- ISO/IEC 80000 series
- NIST Handbook 44 for non-SI units

**Current Syntax:**
- Multiplication: `*` (asterisk)
- Division: `/` (forward slash)
- Exponentiation: `^` (caret)
- Example: `"m/s^2"` for acceleration

### UCUM (Unified Code for Units of Measure)

UCUM is a code system maintained at https://ucum.org/ that provides:

1. **Formal Grammar:** A precise, unambiguous syntax for expressing units
2. **Comprehensive Coverage:** Includes all contemporary units used in science, engineering, and business
3. **Case Sensitivity:** Distinguishes units precisely (e.g., `m` for meter, `M` for mega-prefix)
4. **Machine Parseability:** Designed for computational processing with formal grammar
5. **Wide Adoption:** Used extensively in healthcare (HL7 FHIR), scientific computing, and data exchange
6. **Conversion Support:** Includes formal conversion factors between units

**UCUM Syntax:**
- Multiplication: `.` (period) or juxtaposition
- Division: `/` (forward slash)
- Exponentiation: digit directly after unit
- Example: `"m/s2"` or `"m.s-2"` for acceleration

### Key Differences

| Aspect | Current Spec | UCUM |
|--------|-------------|------|
| Acceleration | `"m/s^2"` | `"m/s2"` |
| Area | `"m^2"` | `"m2"` |
| Multiplication | `"N*m"` | `"N.m"` |
| Micro prefix | `"μm"` | `"um"` (ASCII-safe) |
| Case sensitivity | Mixed | Strict (case-sensitive) |
| Formal grammar | No | Yes (BNF grammar) |
| Conversion factors | Not included | Included |

## Motivation

### Benefits of UCUM Integration

1. **Interoperability:** Enable seamless data exchange with healthcare systems (HL7 FHIR), scientific databases, and other systems that use UCUM
2. **Standardization:** Leverage an existing, well-maintained standard with formal grammar and validation tools
3. **Precision:** Case-sensitive notation eliminates ambiguity (e.g., `m` meter vs `M` mega-prefix)
4. **Tooling:** Existing UCUM parsers, validators, and conversion libraries could be used
5. **Healthcare Adoption:** Critical for medical device data, clinical observations, and laboratory results
6. **ASCII Compatibility:** UCUM provides ASCII representations for Greek symbols (e.g., `"Ohm"` vs `"Ω"`)

### Use Cases

1. **Medical Data Exchange:** Laboratory results, vital signs, medication dosages
2. **Scientific Computing:** Research data with standardized unit notation
3. **IoT Sensors:** Device telemetry with standardized units
4. **International Data Exchange:** Systems requiring unambiguous unit representation
5. **Legacy System Integration:** Many existing systems already use UCUM

## Proposed Solutions

### Option 1: Extend Existing `unit` Keyword

Allow the `unit` keyword to accept both current syntax and UCUM syntax, with optional validation mode.

**Pros:**
- Minimal schema changes
- Backward compatible
- Simple for schema authors

**Cons:**
- Ambiguity in which syntax is being used
- Difficult to validate without additional metadata
- May confuse implementers

**Example:**
```json
{
  "type": "number",
  "unit": "m/s2",
  "description": "Could be current syntax with typo or UCUM"
}
```

### Option 2: Add New `ucumUnit` Keyword

Introduce a dedicated keyword for UCUM units, allowing both to coexist.

**Pros:**
- Explicit and unambiguous
- Allows both systems to be used in same schema
- Clear migration path
- Easy to validate

**Cons:**
- Two keywords with similar purpose
- Potential confusion about which to use
- More complex specification

**Example:**
```json
{
  "type": "number",
  "ucumUnit": "m/s2",
  "symbol": "m/s²"
}
```

Or with both:
```json
{
  "type": "number",
  "unit": "m/s^2",
  "ucumUnit": "m/s2",
  "description": "Supports both representations"
}
```

### Option 3: Unit System Indicator

Add a `unitSystem` keyword to specify which notation is being used.

**Pros:**
- Single `unit` keyword
- Explicit about which system is in use
- Extensible to other unit systems (e.g., imperial notation variations)
- Clear validation rules

**Cons:**
- Requires an additional keyword
- More verbose schemas
- Need to define multiple systems

**Example:**
```json
{
  "type": "number",
  "unitSystem": "ucum",
  "unit": "m/s2"
}
```

Or:
```json
{
  "type": "number",
  "unitSystem": "si-extended",
  "unit": "m/s^2"
}
```

### Option 4: Format Attribute on Unit

Add a format attribute similar to how date-time formats are handled in JSON Schema.

**Pros:**
- Follows JSON Schema patterns
- Single unit value with format specification
- Clear and familiar to JSON Schema users

**Cons:**
- More complex syntax
- Requires parsing the format attribute

**Example:**
```json
{
  "type": "number",
  "unit": {
    "value": "m/s2",
    "format": "ucum"
  }
}
```

Or simpler:
```json
{
  "type": "number",
  "unit": "m/s2",
  "unitFormat": "ucum"
}
```

## Recommendations

### Recommended Approach: Option 2 with Migration Path

1. **Add `ucumUnit` keyword** for explicit UCUM support
2. **Keep existing `unit` keyword** unchanged for backward compatibility
3. **Define precedence:** If both are present, `ucumUnit` takes precedence for systems that understand UCUM
4. **Document mapping:** Provide guidance on converting between formats
5. **Validation:** Schema validators can check UCUM syntax using existing UCUM grammar

### Implementation Considerations

1. **Add UCUM Reference:** Include UCUM specification in normative references
2. **Define Syntax:** Clearly specify UCUM syntax rules in the specification
3. **Provide Examples:** Show side-by-side comparisons of current vs UCUM notation
4. **Validation Tools:** Reference or provide UCUM validation tools/libraries
5. **Migration Guide:** Help existing users understand when and how to adopt UCUM
6. **ASCII Requirement:** Decide whether to require ASCII-only UCUM codes or allow Unicode variants

### Example Specification Text

```markdown
## The `ucumUnit` Keyword

The `ucumUnit` keyword provides a mechanism for annotating a numeric schema 
with its measurement unit using UCUM (Unified Code for Units of Measure) notation.

The keyword MAY appear alongside the `type` keyword in object properties or
array items or map values.

- The value of `ucumUnit` MUST be a JSON string.
- The string value of `ucumUnit` MUST conform to the UCUM syntax as defined in
  the Unified Code for Units of Measure specification.
- The `ucumUnit` keyword MAY be used in conjunction with the `unit` keyword.
  When both are present, systems that support UCUM SHOULD use the `ucumUnit` value.
- UCUM codes SHOULD use the case-sensitive syntax defined in the UCUM specification.

Example:

```json
{
  "type": "number",
  "ucumUnit": "m/s2",
  "symbol": "m/s²"
}
```

## Compatibility and Migration

### Backward Compatibility

- Existing schemas using `unit` keyword remain valid
- New schemas can use `ucumUnit` exclusively or alongside `unit`
- Validators that don't support UCUM will ignore `ucumUnit` (annotation keyword)

### Migration Strategies

1. **Dual Annotation:** Include both `unit` and `ucumUnit` during transition
2. **Healthcare Focus:** Use `ucumUnit` for medical/healthcare schemas from the start
3. **Gradual Adoption:** Add UCUM support incrementally as tooling becomes available

## Open Questions

1. **ASCII vs Unicode:** Should UCUM codes be restricted to ASCII-only representations?
2. **Validation:** Should the specification mandate UCUM validation, or leave it as optional?
3. **Default System:** For new schemas, should there be a recommended default (current vs UCUM)?
4. **Conversion:** Should the specification include guidance on converting between formats?
5. **Prefixes:** How to handle differences in prefix notation (μ vs u)?
6. **Nested Units:** How to handle complex UCUM expressions with annotations?

## References

- UCUM Specification: https://ucum.org/
- UCUM Version 2.1: https://ucum.org/ucum.html
- HL7 FHIR Use of UCUM: http://hl7.org/fhir/datatypes.html#Quantity
- Current JSON Structure Units Spec: https://json-structure.github.io/units/draft-vasters-json-structure-units.html

## Related Work

- HL7 FHIR Quantity datatype uses UCUM
- SNOMED CT uses UCUM for units
- LOINC uses UCUM for laboratory units
- OpenEHR uses UCUM for clinical measurements

## Next Steps

1. **Community Feedback:** Gather input from JSON Structure working group
2. **Healthcare Input:** Consult with HL7/FHIR community on requirements
3. **Prototype:** Create example schemas using proposed syntax
4. **Tooling Survey:** Identify existing UCUM validation and conversion tools
5. **Specification Update:** Draft specific text for inclusion in specification
6. **Examples:** Create comprehensive examples showing UCUM usage

## Contact

For discussion, please use the JSON Structure mailing list or GitHub issues.
