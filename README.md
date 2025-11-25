# 📘 **User Property and Data Manager – GTM Custom Tag Template**

## Overview
The **User Property and Data Manager** is a Google Tag Manager custom tag template designed to collect, normalize, hash, merge, store, and optionally push user data into the dataLayer.  
It supports **manual user inputs**, **user property objects**, **secure hashing**, **cookie and localStorage persistence**, and **merging logic** that preserves previously stored attributes.

This template is built for advanced user-data handling while maintaining compliance, flexibility, and durable user-data persistence across sessions.

---

# 🚀 Features

### **✔ Collect User Data From Multiple Sources**
- Manual input fields (first name, last name, email, phone, country, region, DOB, user ID)
- User property object mapping (dynamic key/value extraction)
- Combined mode: manual + object input

### **✔ Secure Hashing Support**
- SHA-256 hashing for selected manual fields (first name, last name, email)
- SHA-256 hashing for selected object fields
- Choose **Hexadecimal** or **Base64** output format

### **✔ Persistent Storage Layer**
- Stores user data in cookie  
- Optionally stores in localStorage  
- Custom cookie names, domain, path  
- Custom localStorage keys supported

### **✔ Advanced Merging Logic**
- Cookie = **primary**, localStorage = **fallback**
- If cookie exists → merge cookie + LS (cookie overrides conflicts)  
- If cookie missing → restore from LS  
- Empty, undefined, or null values never overwrite existing stored values

### **✔ Optional DataLayer Push**
Pushes:
- `event`: `"gtm_dd_updm_user_data"` (default) or custom name
- `datalayer_source`: `"dd gtm custom tag template"`
- Full user property data object

### **✔ Fully Async-Safe**
- Only calls `data.gtmOnSuccess()` when hashing + writing completes  
- Calls `data.gtmOnFailure()` on real hashing failures  
- Never fires callbacks prematurely

### **✔ Developer Friendly**
- Clean mapping flow  
- Predictable outputs  
- Manual + Object harmonized  
- Apache 2 licensed

---

# 📥 Installation (GTM)

1. Go to **Google Tag Manager → Templates → Tag Templates → New**  
2. Click **Import**  
3. Upload the `.tpl` file for this template  
4. Save the template  
5. Create a new tag using **User Property and Data Manager**  
6. Configure using the instructions below

---

# ⚙️ Configuration Guide  

All references below use **Display Names** exactly as they appear in the GTM Template UI.

---

## **1. Select Data Source Method**
Field: **“Select the User Property Data Source Method”**

Options:
- Both User Property Object and Manual Input  
- Manual Input Only  
- User Property Object Only  

This determines which configuration groups will become visible.

---

## **2. Configure Manual User Data**
Group: **“Configure Your User Data/Property Fields”**

### User Property Table
Columns:
- **User Data** → Choose field type  
- **Enter Value** → Macro or static input (e.g., `{{dlv - userFirstName}}`)

### Optional: Hashing  
Toggle: **“Enable hashing for first name, last name, and email address if collected.”**

Then choose:  
- **Hexadecimal**  
- **BASE64**

---

## **3. Configure User Object Mapping**
Group: **“Configure the User Object”** *(Shown only in Object or Hybrid mode)*

### Fields:
- **User Property Object Variable**  
  Provide a GTM variable returning an object.

### Mapping Table:
- **Key Name** → The storage key (spaces become `_`)  
- **Corresponding Attribute Key** → The object property to read

### Object Hashing  
- Enable: **“Enable hashing for selected user property attributes…”**  
- Choose **Hexadecimal** or **Base64**  
- Add one or more keys inside **“Enter Hashing User Attribute Key”**

---

## **4. Cookie Configuration**
Group: **“Cookie Configuration”**

- **Use a Custom Cookie Name** (optional)  
- **Cookie Duration**  
- **Cookie Lifespan**  
- **Advanced Configuration (Optional)**  
  - Custom domain  
  - Custom path

Default cookie name:  
`dd_cookie_gtm_user_data`

---

## **5. LocalStorage Configuration**
Group: **“LocalStorage Config”**

- **Also store the data in Local Storage**  
- **Use a custom localstorage key** (optional)
- **Input LocalStorage Name**

Default LS key:  
`dd_cookie_ls_user_data`

---

## **6. DataLayer Configuration**
Group: **“DataLayer Configuration”**

Options:
- **Push user property data to the dataLayer**  
- **Use a custom event name**  
- **Custom Data Layer Event Name**

### Default dataLayer push:
```js
{
  event: "gtm_dd_updm_user_data",
  datalayer_source: "dd gtm custom tag template",
  user_properties: { ...stored properties... }
}
```

---

# 📊 Example Setups & Outputs

## **Example 1 — Manual + Object (Hybrid Mode)**

### Manual Inputs:

| User Data  | Enter Value         |
| ---------- | ------------------- |
| First Name | {{dlv - firstName}} |
| Email      | {{dlv - email}}     |

Hashing enabled → **Hexadecimal**

### Object Input:

Variable: `{{dlv - userProfile}}`

Mapping:

| Key Name     | Corresponding Attribute Key |
| ------------ | --------------------------- |
| user_plan    | plan                        |
| subscription | level                       |

Object hashing enabled for:

* `plan`

---

### **Resulting Stored Cookie:**

```json
{
  "firstName": "John",
  "firstName_sha": "3249bfe7...",
  "email": "john@example.com",
  "email_sha": "7af02ce...",
  "user_plan": "premium",
  "user_plan_sha": "91f7ab3...",
  "subscription": "gold"
}
```

### **If Previous Cookie Exists**

* Missing keys are added
* Existing keys remain
* No empty or null values overwrite previous values

### **If Cookie Missing, but LS Exists**

* LS restores previously stored values
* New fields are merged in
* Cookie rewritten with refreshed expiration

---

# 🧠 Logic Summary

1. Load cookie → acts as **primary** data source
2. Load localStorage → fills missing fields
3. Merge manual inputs
4. Merge object mappings
5. Hash fields (if enabled), handle async
6. Refresh cookie & update LS (if enabled)
7. Push to dataLayer (if enabled)
8. Call `data.gtmOnSuccess()`
9. On hashing failure → `data.gtmOnFailure()`

---

# 🔗 Useful Links

### **DumbData Custom Template Collection:**

[https://dumbdata.co/gtm-custom-templates/](https://dumbdata.co/gtm-custom-templates/)

---

# 📄 License

**Apache License 2.0**
Free for commercial and personal use.
