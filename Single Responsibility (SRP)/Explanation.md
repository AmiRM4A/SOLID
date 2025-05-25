## 🧠 **Single Responsibility Principle (SRP)**

> **A class should only have one reason to change.**
> In other words, it should do **only one job** — and do it well.

---

### ✅ Why SRP Matters

When a class has **just one responsibility**, it becomes:

* Easier to **read**
* Easier to **test**
* Easier to **maintain**
* Safer to **change** (because changes affect only one concern)

---

### ❌ Violations of SRP:

1. **Too many instance variables**
   <br>👉 Suggests the class is managing too many things.

2. **Too many public methods**
   <br>👉 Implies the class offers too many unrelated operations.

3. **Each method uses different instance variables**
   <br>👉 The methods are working on different concerns, not one unified purpose.

4. **Specific tasks are hidden in private methods**
   <br>👉 Breaking responsibilities down privately doesn’t fix the overall problem — they still exist in one class.