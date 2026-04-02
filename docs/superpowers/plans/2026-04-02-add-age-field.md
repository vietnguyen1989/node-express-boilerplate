# Add Age Field to User Model Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add a required age field to the User model, ensuring all new users must provide their age.

**Architecture:** Add the age field directly to the Mongoose userSchema definition. The field will be required, numeric, with no validation constraints. Tests will verify the field is required and is persisted/retrieved correctly.

**Tech Stack:** Mongoose, Jest, Faker (for test data), Supertest (for integration tests)

---

## Task 1: Update User Fixtures

**Files:**
- Modify: `tests/fixtures/user.fixture.js`

- [ ] **Step 1: Add age property to userOne fixture**

In `tests/fixtures/user.fixture.js`, add `age: 25` to the userOne object:

```javascript
const userOne = {
  _id: mongoose.Types.ObjectId(),
  name: faker.name.findName(),
  email: faker.internet.email().toLowerCase(),
  password,
  role: 'user',
  isEmailVerified: false,
  age: 25,
};
```

- [ ] **Step 2: Add age property to userTwo fixture**

Add `age: 30` to the userTwo object:

```javascript
const userTwo = {
  _id: mongoose.Types.ObjectId(),
  name: faker.name.findName(),
  email: faker.internet.email().toLowerCase(),
  password,
  role: 'user',
  isEmailVerified: false,
  age: 30,
};
```

- [ ] **Step 3: Add age property to admin fixture**

Add `age: 35` to the admin object:

```javascript
const admin = {
  _id: mongoose.Types.ObjectId(),
  name: faker.name.findName(),
  email: faker.internet.email().toLowerCase(),
  password,
  role: 'admin',
  isEmailVerified: false,
  age: 35,
};
```

- [ ] **Step 4: Commit**

```bash
git add tests/fixtures/user.fixture.js
git commit -m "test: add age to user fixtures"
```

---

## Task 2: Add Unit Test for Age Field Requirement

**Files:**
- Modify: `tests/unit/models/user.model.test.js`

- [ ] **Step 1: Update beforeEach to include age in newUser**

In the `User validation` describe block, update the beforeEach to add age:

```javascript
beforeEach(() => {
  newUser = {
    name: faker.name.findName(),
    email: faker.internet.email().toLowerCase(),
    password: 'password1',
    role: 'user',
    age: 25,
  };
});
```

- [ ] **Step 2: Add test for missing age validation**

After the test `should throw a validation error if role is unknown`, add this test:

```javascript
test('should throw a validation error if age is missing', async () => {
  delete newUser.age;
  await expect(new User(newUser).validate()).rejects.toThrow();
});
```

- [ ] **Step 3: Run tests to verify they pass**

```bash
npm test -- tests/unit/models/user.model.test.js
```

Expected output: All tests should pass, including the new age validation test.

- [ ] **Step 4: Commit**

```bash
git add tests/unit/models/user.model.test.js
git commit -m "test: add validation test for age field requirement"
```

---

## Task 3: Update Integration Tests

**Files:**
- Modify: `tests/integration/user.test.js`

- [ ] **Step 1: Update newUser object in POST /v1/users tests**

In the `POST /v1/users` describe block, update the beforeEach to include age:

```javascript
beforeEach(() => {
  newUser = {
    name: faker.name.findName(),
    email: faker.internet.email().toLowerCase(),
    password: 'password1',
    role: 'user',
    age: 25,
  };
});
```

- [ ] **Step 2: Update response expectations to include age**

In the test `should return 201 and successfully create new user if data is ok`, update the response expectation:

Find this section:
```javascript
expect(res.body).toEqual({
  id: expect.anything(),
  name: newUser.name,
  email: newUser.email,
  role: newUser.role,
  isEmailVerified: false,
});
```

Replace with:
```javascript
expect(res.body).toEqual({
  id: expect.anything(),
  name: newUser.name,
  email: newUser.email,
  role: newUser.role,
  isEmailVerified: false,
  age: newUser.age,
});
```

- [ ] **Step 3: Update database user expectation**

In the same test, find:
```javascript
expect(dbUser).toMatchObject({ name: newUser.name, email: newUser.email, role: newUser.role, isEmailVerified: false });
```

Replace with:
```javascript
expect(dbUser).toMatchObject({ name: newUser.name, email: newUser.email, role: newUser.role, isEmailVerified: false, age: newUser.age });
```

- [ ] **Step 4: Update other POST tests**

Search for the `POST /v1/users` test that checks `should be able to create an admin as well` and any other similar tests. Update them to include `age: 25` (or appropriate age) in the newUser objects and update response expectations to include age.

- [ ] **Step 5: Run integration tests**

```bash
npm test -- tests/integration/user.test.js --testNamePattern="POST /v1/users"
```

Expected output: Tests should pass with age included in responses.

- [ ] **Step 6: Commit**

```bash
git add tests/integration/user.test.js
git commit -m "test: update user integration tests to include age field"
```

---

## Task 4: Add Age Field to User Model

**Files:**
- Modify: `src/models/user.model.js`

- [ ] **Step 1: Add age field to userSchema**

In `src/models/user.model.js`, add the age field after the `name` field in the schema definition:

```javascript
const userSchema = mongoose.Schema(
  {
    name: {
      type: String,
      required: true,
      trim: true,
    },
    age: {
      type: Number,
      required: true,
    },
    email: {
```

- [ ] **Step 2: Verify the complete schema looks correct**

The schema should now have this structure:
```javascript
const userSchema = mongoose.Schema(
  {
    name: {
      type: String,
      required: true,
      trim: true,
    },
    age: {
      type: Number,
      required: true,
    },
    email: {
      type: String,
      required: true,
      unique: true,
      trim: true,
      lowercase: true,
      validate(value) {
        if (!validator.isEmail(value)) {
          throw new Error('Invalid email');
        }
      },
    },
    // ... rest of schema
  },
  {
    timestamps: true,
  }
);
```

- [ ] **Step 3: Run all tests**

```bash
npm test -- tests/unit/models/user.model.test.js tests/integration/user.test.js
```

Expected output: All tests pass, including new age validation tests.

- [ ] **Step 4: Commit**

```bash
git add src/models/user.model.js
git commit -m "feat: add required age field to User model"
```

---

## Task 5: Verification and Cleanup

**Files:**
- None

- [ ] **Step 1: Run full test suite**

```bash
npm test
```

Expected output: All tests pass, no failures.

- [ ] **Step 2: Verify model structure**

Run a quick check in the Node REPL or create a small test file to confirm age is required:
```bash
node -e "const User = require('./src/models/user.model'); const u = new User({name: 'test', email: 't@t.com', password: 'pass1'}); u.validate().catch(e => console.log('Validation error (expected):', e.message))"
```

Expected output: Should show a validation error about age being required.

- [ ] **Step 3: Commit any remaining changes**

```bash
git status
```

If there are any uncommitted changes, commit them with appropriate messages.

---

## Summary

This plan modifies 4 files across tests and model layer:
1. **User fixtures** - Add age to test data
2. **Unit tests** - Add age validation test
3. **Integration tests** - Update to expect age in requests/responses
4. **User model** - Add required age field to schema

Result: Users cannot be created without an age value, and age persists correctly in the database.
