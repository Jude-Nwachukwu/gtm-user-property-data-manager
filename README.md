
# 📘 **User Property and Data Manager – GTM Custom Tag Template**

## Overview
The **User Property and Data Manager** is a Google Tag Manager custom tag template designed to collect, normalize, hash, merge, store, and optionally push user data into the dataLayer.

It supports **manual user inputs**, **user property object mapping**, **secure hashing**, **cookie and localStorage persistence**, and a robust **merge-and-restore logic** that guarantees durable user data across sessions.

This template is built for advanced, production-grade user data handling while maintaining flexibility, correctness, and compliance-friendly behavior.

---

# 🚀 Features

### ✔ Collect User Data From Multiple Sources
- Manual user attribute inputs
- User property object mapping (dynamic key/value extraction)
- Hybrid mode: manual + object input together

### ✔ Secure Hashing Support
- SHA-256 hashing
- Manual hashing for selected attributes
- Object hashing for selected object keys
- Output formats:
  - Hexadecimal
  - Base64
- Hashes only regenerate when values change

### ✔ Persistent Storage Layer
- Stores data in cookies
- Optional localStorage persistence
- Custom cookie name, domain, and path
- Custom localStorage key support
- Cookie expiration always refreshed on update

### ✔ Advanced Merge & Restore Logic
- Cookie is **primary**
- localStorage is **secondary**
- If cookie exists → merge cookie + LS (cookie wins conflicts)
- If cookie is missing → restore fully from LS
- Empty, null, or undefined values **never overwrite** existing data

### ✔ Optional DataLayer Push
Pushes a clean user object into the dataLayer with:
- Default or custom event name
- Stable data structure
- Clear source attribution

### ✔ Async-Safe Execution
- Waits for all hashing to complete
- Calls `data.gtmOnSuccess()` only after completion
- Calls `data.gtmOnFailure()` on real hashing failures
- No premature callbacks


---

# 📥 Installation (GTM)

1. Open **Google Tag Manager**
2. Go to **Templates → Tag Templates**
3. Click **New → Import**
4. Upload the template `.tpl` file
5. Save the template
6. Create a new tag using **User Property and Data Manager**
7. Configure the tag as described below

---

# ⚙️ Configuration Guide

All configuration references below use **Display Names exactly as shown in the GTM UI**.

---

## 1️⃣ Select Data Source Method
**Field:** *Select the User Property Data Source Method*

Options:
- **Both User Property Object and Manual Input**
- **Manual Input Only**
- **User Property Object Only**

This controls which configuration sections are displayed.

---

## 2️⃣ Configure Manual User Data
**Group:** *Configure Your User Data/Property Fields*

### Manual User Property Table

Columns:
- **User Data** → Select a predefined user attribute
- **Enter Value** → GTM variable or static value

### ✅ Supported Manual User Attributes

| Display Name      | Stored Key     |
|------------------|---------------|
| First Name       | `firstName`   |
| Last Name        | `lastName`    |
| Email Address    | `email`       |
| Phone Number     | `phone`       |
| Date of Birth    | `dateOfBirth` |
| User ID          | `userId`      |
| Country          | `country`     |
| Region / State   | `region`      |
| City             | `city`        |
| Postal Code      | `postalCode`  |
| Address          | `address`     |
| Gender           | `gender`      |
| Full Name        | `fullName`    |

> All values are validated before storage. Empty or invalid values are ignored.

---

### 🔐 Manual Hashing
**Toggle:** *Enable hashing for first name, last name, and email address if collected*

Hashing applies to:
- First Name
- Last Name
- Email Address

Hash formats:
- Hexadecimal
- Base64

Hashes are only regenerated when values change.

---

## 3️⃣ Configure User Property Object
**Group:** *Configure the User Object*  
*(Visible in Object-only or Hybrid mode)*

### Fields:
- **User Property Object Variable**  
  A GTM variable returning an object.

### Mapping Table

| Column | Description |
|------|-------------|
| Key Name | Output storage key (spaces auto-converted to `_`) |
| Corresponding Attribute Key | Object property to read |

### 🔐 Object Hashing
- Enable hashing for selected object attributes
- Choose Hex or Base64
- Specify one or more attribute keys to hash

---

## 4️⃣ Cookie Configuration
**Group:** *Cookie Configuration*

Options:
- Use a custom cookie name
- Cookie duration unit
- Cookie lifespan value
- Advanced options:
  - Custom domain
  - Custom path

**Default cookie name:**  
`dd_cookie_gtm_user_data`

---

## 5️⃣ LocalStorage Configuration
**Group:** *LocalStorage Config*

Options:
- Also store the data in Local Storage
- Use a custom localStorage key

**Default localStorage key:**  
`dd_cookie_ls_user_data`

---

## 6️⃣ DataLayer Configuration
**Group:** *DataLayer Configuration*

Options:
- Push user property data to the dataLayer
- Use a custom event name

### Default dataLayer Push
```js
{
  event: "gtm_dd_updm_user_data",
  datalayer_source: "dd gtm custom tag template",
  user_data: { ...merged user properties... }
}
````

---

# 📊 Example Setups & Outputs

## Example 1 — Hybrid Mode (Manual + Object)

### Manual Inputs

| User Data  | Enter Value         |
| ---------- | ------------------- |
| First Name | {{dlv - firstName}} |
| Email      | {{dlv - email}}     |
| City       | {{dlv - city}}      |

Hashing enabled → **Hexadecimal**

---

### Object Input

**Variable:** `{{dlv - userProfile}}`

**Mapping Table:**

| Key Name     | Corresponding Attribute Key |
| ------------ | --------------------------- |
| user_plan    | plan                        |
| subscription | level                       |

Hash enabled for:

* `plan`

---

### Resulting Stored Cookie

```json
{
  "firstName": "John",
  "firstName_sha": "3249bfe7...",
  "email": "john@example.com",
  "email_sha": "7af02ce...",
  "city": "Lagos",
  "user_plan": "premium",
  "user_plan_sha": "91f7ab3...",
  "subscription": "gold"
}
```

---

### Merge Behavior

* Existing cookie values are preserved
* Missing fields are filled from localStorage
* localStorage restores cookie if cookie is deleted
* No invalid values overwrite stored data

---

# 🧠 Execution Logic Summary

1. Load cookie (primary)
2. Load localStorage (secondary)
3. Merge cookie + LS
4. Merge manual inputs
5. Merge object mappings
6. Hash updated fields (async-safe)
7. Write cookie + localStorage
8. Push to dataLayer (if enabled)
9. Call `data.gtmOnSuccess()`
10. On hashing error → `data.gtmOnFailure()`

---

# 🔗 Useful Links

### DumbData GTM Custom Templates

[https://dumbdata.co/gtm-custom-templates/](https://dumbdata.co/gtm-custom-templates/)

---

# 📄 License

**Apache License 2.0**
Free for commercial and personal use.

