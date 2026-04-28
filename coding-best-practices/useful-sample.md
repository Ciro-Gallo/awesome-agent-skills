# 🧹 Clean Software Principles (Language-Agnostic) - with examples

---

## 🔐 Security

### Never commit credentials or secrets to Git

❌ Wrong example:

```js
const dbPassword = "supersecret123";
```

✅ Correct:

```js
const dbPassword = process.env.DB_PASSWORD;
```

---

### Always validate inputs

❌

```js
const query = "SELECT * FROM users WHERE id = " + userInput;
```

✅

```js
const query = "SELECT * FROM users WHERE id = ?";
db.execute(query, [userInput]);
```

---

### Principle of least privilege

❌ DB user with admin permissions for everything

✅ DB user with read/write permissions only on the required tables

---

### Secure password handling

❌

```js
savePassword(userPassword);
```

✅

```js
const hash = bcrypt.hashSync(userPassword, 10);
savePassword(hash);
```

---

## ⚙️ Performance & Scalability

### Do not load entire tables into memory

❌

```js
const users = await db.getAllUsers();
```

✅

```js
const users = await db.getUsers({ limit: 100, offset: 0 });
```

---

### Avoid unnecessarily expensive operations

❌ N+1 query:

```js
for (user of users) {
  user.posts = await getPosts(user.id);
}
```

✅

```js
const posts = await getPostsForUsers(userIds);
```

---

### Use cache when it makes sense

❌ Query repeated every time:

```js
const config = await db.getConfig();
```

✅

```js
const config = cache.get("config") || await db.getConfig();
```

---

### Measure before optimizing

❌ Premature optimization

✅ Using a profiler:

```bash
node --prof app.js
```

---

## 🧱 Architecture & Design

### Separate responsibilities (SRP)

❌

```js
class UserService {
  saveUser() {}
  sendEmail() {}
}
```

✅

```js
class UserService {}
class EmailService {}
```

---

### Prefer composition over inheritance

❌

```js
class Admin extends User {}
```

✅

```js
function createAdmin(user) {
  return { ...user, role: "admin" };
}
```

---

### Depend on abstractions, not implementations

❌

```js
const mysql = new MySQLDatabase();
```

✅

```js
const db = new DatabaseInterface();
```

---

### Keep coupling low and cohesion high

❌ Modules that depend on everything

✅ Independent modules with clear responsibilities

---

### Avoid "God objects"

❌ 2000-line class that does everything

✅ Multiple small, focused classes

---

## 📦 Code Management

### Readable code > "clever" code

❌

```js
const x = a ? b : c ? d : e;
```

✅

```js
if (a) return b;
if (c) return d;
return e;
```

---

### Clear and descriptive names

❌

```js
let x;
```

✅

```js
let userAge;
```

---

### Small, focused functions

❌

```js
function processUserDataAndSendEmailAndSave() {}
```

✅

```js
function processUserData() {}
function sendEmail() {}
```

---

### Avoid duplication (DRY)

❌ Duplicated code across multiple files

✅ Shared function:

```js
function calculateTax() {}
```

---

### Comment the "why", not the "what"

❌

```js
// increment i
i++;
```

✅

```js
// workaround per bug API esterna
i++;
```

---

## 🧪 Testing

### Write automated tests

```js
test("sum works", () => {
  expect(sum(1, 2)).toBe(3);
});
```

---

### Deterministic and isolated tests

❌ Test depends on an external API

✅ Mock:

```js
jest.mock("api");
```

---

### Readable tests

❌

```js
test("t1", () => {});
```

✅

```js
test("should return user when id exists", () => {});
```

---

### Do not test implementations

❌ Tests on internal methods

✅ Test behavior:

```js
expect(response.status).toBe(200);
```

---

## 🚨 Error Handling & Logging

### Handle errors explicitly

❌

```js
try { ... } catch(e) {}
```

✅

```js
try { ... } catch(e) {
  logger.error(e);
}
```

---

### Useful error messages

❌

```js
throw new Error("Error");
```

✅

```js
throw new Error("User not found with id: " + id);
```

---

### Structured logging

```js
logger.info({ userId, action: "login" });
```

---

### Do not expose sensitive details

❌ Logging passwords

✅ Logging only the user ID

---

## 🔄 Git & Workflow

### Small and meaningful commits

❌ "fix stuff"

✅ "fix: handle null user in login"

---

### Do not push broken code

❌ Tests fail

✅ Green CI before merge

---

### Use branches

```bash
git checkout -b feature/login
```

---

### Code review

✅ Always at least one reviewer

---

## 🧩 Maintainability

### Reduce complexity

❌ Function with 10 nested ifs

✅ Split into multiple functions

---

### Continuous refactoring

✅ Improve code as you work

---

### Remove dead code

❌ Functions that are never used

✅ Remove them

---

### Avoid unnecessary dependencies

❌ 50 libraries for trivial things

✅ Use the standard library when possible

---

## 🌍 API & Contracts

### Consistent APIs

❌

```
/getUser
/user/get
```

✅

```
GET /users/:id
```

---

### API versioning

```
/api/v1/users
```

---

### Backward compatibility

❌ Breaking existing clients

✅ Add fields, do not remove them

---

### Fail fast

❌ Ambiguous responses

✅

```json
{ "error": "Invalid input" }
```

---

## 🧠 General Principles

### KISS

❌ Over-engineering

✅ Simple and direct solution

---

### YAGNI

❌ Implementing features that are "maybe useful"

✅ Only what is needed now

---

### Boy Scout Rule

✅ Always improve the code you touch

---

### Automate what's repetitive

❌ Manual tasks

✅ Script:

```bash
npm run deploy
```

---

## ✅ Final Note

This document is alive: adapt it to your team, stack, and context.
The goal isn't perfection, but to reduce errors, complexity, and technical debt over time.

---
