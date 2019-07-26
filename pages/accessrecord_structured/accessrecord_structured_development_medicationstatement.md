---
title: MedicationStatement resource
keywords: getcarerecord
tags: [design,structured]
sidebar: accessrecord_structured_sidebar
permalink: accessrecord_structured_development_medicationstatement.html
summary: "Guidance for populating and consuming the MedicationStatement resource"
div: resource-page
---

## Introduction ##

The headings below list the elements of the MedicationStatement resource and describe how to populate and consume them.

{% include important.html content="Any element not specifically listed below **MUST NOT** be populated or consumed." %}

{% include tip.html content="You'll find it helpful to read it in conjunction with the underlying [MedicationStatement profile definition](https://fhir.nhs.uk/STU3/StructureDefinition/CareConnect-GPC-MedicationStatement-1/_history/1.2)." %}

{% include note.html content="It is not expected that suppliers will provide resources for data types that reference resources that have not been curated and published by GP Connect. In the case where these fields are required it is not expected they will be populated until the resources have been developed by the suppliers and completed the relevant NHS Digital assurance." %}

## MedicationStatement elements ##

### id ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>Id</code></td>
    <td><b>Optionality:</b> Mandatory</td>
    <td><b>Cardinality:</b> 1..1</td>
  </tr>
</table>

The logical identifier of the MedicationStatement resource.

### meta.profile ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>uri</code></td>
    <td><b>Optionality:</b> Mandatory</td>
    <td><b>Cardinality:</b> 1..1</td>
  </tr>
</table>

The MedicationStatement profile URL.

Fixed value [https://fhir.nhs.uk/STU3/StructureDefinition/CareConnect-GPC-MedicationStatement-1](https://fhir.nhs.uk/STU3/StructureDefinition/CareConnect-GPC-MedicationStatement-1)

### extension[lastIssueDate] ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>dateTime</code></td>
    <td><b>Optionality:</b> Required</td>
    <td><b>Cardinality:</b> 0..1</td>
  </tr>
</table>

The date when the latest prescription under this plan was issued. This will not be populated where the Medication/Medical Device is Repeat Dispensed or Prescribed Elsewhere as these do not have issue information recorded in the GP system.

### extension[prescribingAgency] ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>CodeableConcept</code></td>
    <td><b>Optionality:</b> Mandatory</td>
    <td><b>Cardinality:</b> 1..1</td>
  </tr>
</table>

This details the care setting in which the medication or medical device was prescribed.

Currently this field will only support two coded entries. If the medication/medical device was prescribed by the GP practice or by another organisation. If the providing organisation has more details about the type of prescribing organisation (for example that is was a dental practice or hospital) this **MUST** be included in the CodeableConcept.Text field.

In the future the coded valueset will be built on to be more specific about where a medication/medical device was prescribed. For instance, if the patient was prescribed a medication by a hospital or bought a medication over the counter then this would be coded as well as in the text.

For repeat and repeat dispensed medications/medical devices, the value identifies the care setting where the medication plan (rather than any specific issue in the plan) was authorised.

### identifier ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>Identifier</code></td>
    <td><b>Optionality:</b> Mandatory</td>
    <td><b>Cardinality:</b> 1..*</td>
  </tr>
</table>

This is for business identifiers.

This is sliced to include a cross-care setting identifier which **MUST** be populated. The codeSystem for this identifier is `https://fhir.nhs.uk/Id/cross-care-setting-identifier`.

This **MUST** be a GUID.

*Providing* systems **MUST** ensure this GUID is globally unique and a persistent identifier (that is, it doesn't change between requests and therefore stored with the source data).

Where *consuming* systems are integrating data from this resource to their local system, they **MUST** also persist this GUID at the same time.

### basedOn ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>Reference</code></td>
    <td><b>Optionality:</b> Mandatory</td>
    <td><b>Cardinality:</b> 1..1</td>
  </tr>
</table>

Link to the MedicationRequest that this `MedicationStatement` is based on.

Every MedicationStatement **MUST** be based on a `MedicationRequest` with `intent` set to `plan`.

### context ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>Reference(Encounter)</code></td>
    <td><b>Optionality:</b> Required</td>
    <td><b>Cardinality:</b> 0..1</td>
  </tr>
</table>

The `Encounter` within which the medication/medical device was authorised.

As per base profile guidance.

### status ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>code</code></td>
    <td><b>Optionality:</b> Mandatory</td>
    <td><b>Cardinality:</b> 1..1</td>
  </tr>
</table>

The status of the authorisation.

Use one of `active`, `completed` or `stopped`:

- `active` represents an active authorisation - used for active repeat medications/medical devices
- `stopped` represents an authorisation which has been discontinued, cancelled or stopped
- `complete` represents an authorisation which has run its course

For repeat and repeat dispensed the status refers to the status of the plan (the entire cycle of prescriptions).

For acute, the status refers to the status of the prescription issue.

### medicationReference ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>Reference(Medication)</code></td>
    <td><b>Optionality:</b> Mandatory</td>
    <td><b>Cardinality:</b> 1..1</td>
  </tr>
</table>

The medication/medical device the authorisation is for.

The `Medication` resource provides the coded representation of the medication/medical device.

### effective ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>Period</code></td>
    <td><b>Optionality:</b> Required</td>
    <td><b>Cardinality:</b> 0..1</td>
  </tr>
</table>

`Period.start` is **MANDATORY**. Where there is no defined start date this is populated with the recorded date.

`Period.end` is **REQUIRED**. Where there is a defined expiry or end date or where the end date can be derived (for example - from the duration) the end date **MUST** be supplied.

For repeats and repeat dispensed this refers to the period of the plan (the entire cycle of prescriptions).

For acutes, this refers to the period of the prescription issue.

### dateAsserted ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>dateTime</code></td>
    <td><b>Optionality:</b> Mandatory</td>
    <td><b>Cardinality:</b> 1..1</td>
  </tr>
</table>

When this medication statement was believed true.

Unless there is a distinct user-modifiable availability date/time for the authorisation, this is the audit trail date/time for when the authorisation was entered.

### subject ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>Reference(Patient)</code></td>
    <td><b>Optionality:</b> Mandatory</td>
    <td><b>Cardinality:</b> 1..1</td>
  </tr>
</table>

Who the medication/medical device is for- that is, to whom it will be administered.

Reference to patient.

### taken ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>code</code></td>
    <td><b>Optionality:</b> Mandatory</td>
    <td><b>Cardinality:</b> 1..1</td>
  </tr>
</table>

Whether a medication/medical device was taken.

Providers **MUST** use a default value of `unk` – unknown.

This item is mandatory in the base FHIR profile, but GP systems do not record this detail. Therefore, we have been forced to pick a default value and this information should not be used.

This element has been included in this section as providers **MUST** populate it. However, as the data should not be used it has also been included in the ‘Do not use’ section below.

### note ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>Annotation</code></td>
    <td><b>Optionality:</b> Required</td>
    <td><b>Cardinality:</b> 0..*</td>
  </tr>
</table>

All notes that are associated with this medication/medical device record.

All patient notes and prescriber notes at authorisation(plan) and issue(order) level **MUST** be included in this field. They **MUST** be concatenated and indicate the level the notes come from (for example, 1st Issue) and be prefixed with either ‘Patient Notes:’ or ‘Prescriber Notes:’ as appropriate.

### dosage.text ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>String</code></td>
    <td><b>Optionality:</b> Mandatory</td>
    <td><b>Cardinality:</b> 1..1</td>
  </tr>
</table>

Complete dosage instructions as text.

In exceptional cases where for legacy data there is no dosage recorded in the system then this MUST be populated with the text 'No information available'.

### dosage.patientInstruction ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>String</code></td>
    <td><b>Optionality:</b> Required</td>
    <td><b>Cardinality:</b> 0..1</td>
  </tr>
</table>

Additional instructions for patient - that is, RHS of prescription label.

<br><br>

## MedicationStatement elements not in use ##

The following elements **SHALL NOT** be populated:

### meta.versionId ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>Id</code></td>
  </tr>
</table>

### meta.lastUpdated ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>Instant</code></td>
  </tr>
</table>

### extension[ChangeSummary] ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>Complex Extension</code></td>
  </tr>
</table>

This is not in scope for this version of GP Connect.

### partOf ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>Reference(MedicationAdministration, MedicationDispense, MedicationStatement, Procedure, Observation)</code></td>
  </tr>
</table>

This is not in scope for this version of Care Connect and therefore not available for use in GP Connect.

### category ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>CodeableConcept</code></td>
  </tr>
</table>

This is not in scope for this version of Care Connect and therefore not available for use in GP Connect.

### informationSource ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>Reference(Patient, Practitioner, RelatedPerson, Organization)  </code></td>
  </tr>
</table>

This is not in scope for this version of Care Connect and therefore not available for use in GP Connect.

### derivedFrom ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>Reference(Any)</code></td>
  </tr>
</table>

This is not in scope for this version of Care Connect and therefore not available for use in GP Connect.

### taken ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>code</code></td>
  </tr>
</table>

This is not in scope for this version of Care Connect and therefore not available for use in GP Connect.

### reasonNotTaken ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>CodeableConcept</code></td>
  </tr>
</table>

This is not in scope for this version of Care Connect and therefore not available for use in GP Connect.

### reasonCode ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>CodeableConcept</code></td>
  </tr>
</table>

This information is available via linking to a Problem record.

### reasonReference ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>Reference(Condition), Reference(Observation)</code></td>
  </tr>
</table>

This information is available via linking to a Problem record.