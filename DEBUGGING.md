# Debugging Log

This document records real issues and failed assumptions discovered during development.

---

## Issue 1: False positive classification caused by substring matching

### Symptom

The facet:

`Desperation`

was incorrectly classified as:

`biographical_or_external_fact`

### Diagnosis

The preprocessing logic used unrestricted substring matching for keywords.

One of the external-fact keywords was:

`ratio`

Because:

`desperation`

contains the character sequence:

`ratio`

the classifier incorrectly matched the facet to an unrelated category.

### Root Cause

The implementation used logic equivalent to:

```python
keyword in normalized_facet