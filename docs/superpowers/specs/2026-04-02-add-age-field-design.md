# Design: Add Age Field to User Model

**Date:** 2026-04-02
**Feature:** Add required age field to User model
**Status:** Design approved

## Overview

Add a required `age` numeric field to the User Mongoose schema. This field will be mandatory for all new user records with no additional validation constraints.

## Schema Changes

Add the following field to the User schema after the `name` field:

```javascript
age: {
  type: Number,
  required: true,
}
```

**Field specifications:**
- Type: `Number` (numeric integer)
- Required: `true` (mandatory on user creation)
- No min/max validation
- No default value
- No special serialization (uses default toJSON behavior)

## Implementation Details

**Location:** `/src/models/user.model.js`

**Change scope:**
- Single field addition to userSchema definition
- No changes to schema plugins, methods, or statics
- No changes to password hashing, email validation, or role logic
- No changes to API endpoints or controllers (Mongoose handles automatic validation)

**Database:**
- New field applies to all records going forward
- Existing records will not have age populated (handled by MongoDB as `undefined`)
- New user creation will fail validation if age is not provided

## Testing

The change should be tested in:
- User creation endpoints (must include age)
- User validation (rejects missing age)
- User retrieval (age is returned in JSON responses)

## Success Criteria

✅ Age field appears in User schema definition
✅ Age is required on user creation
✅ Age is persisted and retrieved correctly
✅ Omitting age on creation fails validation
