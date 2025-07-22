## ✅ **Google Sheet Structure for Lessons & Quizzes**

### **1. Overview**

The sheet contains:

* **Metadata**: General info about the course version.
* **Lessons**: Each row represents one lesson, including its links and quizzes.

---

### **2. Layout**

#### **Metadata Section (Top)**

* Key-value pairs starting from the first row.
* Ends with a blank row.

**Example:**

```
version       | v1
language      | en
created_at    | 2025-07-21
last_modified | 2025-07-22
provided_by   | Nasir
```

---

#### **Lessons Section**

* Starts after the blank row.
* **Columns**:

  * `id` → Unique lesson ID
  * `title` → Lesson title
  * `description` → Short description
  * `lesson` → Main lesson text/content
  * `links` → Multi-line text, each line: `Name - URL`
  * `quizzes` → Multi-line text, each line:
    `Question - option1, option2, option3, option4 - correctOptionNumber`

---

**Example Lesson Row:**

| id | title       | description  | lesson          | links                                                                                                      | quizzes                                                                               |
| -- | ----------- | ------------ | --------------- | ---------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| 1  | Intro to JS | Basics of JS | Learn JS basics | Documentation - [https://example.com](https://example.com)<br>W3Schools - [https://w3.com](https://w3.com) | What is JS? - Lang, Library, Framework, None - 1<br>JS is? - Yes, No, Maybe, None - 2 |

---

### **3. Data Entry Rules**

✅ **Links Column**:
Each line → `Name - URL`
Example:

```
Documentation - https://example.com
W3Schools - https://w3.com
```

✅ **Quizzes Column**:
Each line → `Question - option1, option2, option3, option4 - correctIndex`
Example:

```
What is JS? - Lang, Library, Framework, None - 1
JS is? - Yes, No, Maybe, None - 2
```

*(Correct index starts from 1 for the first option.)*

---

### **4. How It’s Used**

* A Google Apps Script converts the sheet into **JSON API**.
* Output JSON structure:

```json
{
  "meta": {...},
  "lessons": [
    {
      "id": "1",
      "title": "...",
      "links": [...],
      "quizzes": [...]
    }
  ]
}
```
