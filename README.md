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

## App script
after pasting the app script and deploy as webApp, it will give an url like this:
https://script.google.com/macros/s/AKfycbys15hFMppJnR-PIRLgKZjuZLsI3flaQaQQLmO7Vx9qinDdbccZjPG_PA8R1bwQe_qhqA/exec

this url will return a json with metadata and lessions.
example table: Read only:
https://docs.google.com/spreadsheets/d/e/2PACX-1vQA-UHMxbTmflx0O197KitvuTxeoVao01yT9b6XfAcfV3GU8l20hLmHgp4uae4tdDQr9pxARJQ8KPYz/pubhtml
```js
function doGet() {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName('Sheet1');
  var data = sheet.getDataRange().getValues();

  var meta = {};
  var i = 0;

  // STEP 1: Collect meta (until blank row)
  while (i < data.length && data[i][0]) {
    meta[data[i][0]] = data[i][1];
    i++;
  }

  // Skip blank row
  i++;

  // STEP 2: Lessons headers
  var headers = data[i];
  i++;

  // STEP 3: Parse lessons
  var lessons = [];
  for (; i < data.length; i++) {
    var row = data[i];
    if (!row[0]) continue; // skip empty rows

    var obj = {};
    headers.forEach((header, index) => {
      obj[header] = row[index];
    });

    // Parse links (multi-line text)
    var links = [];
    if (obj.links) {
      var linkLines = obj.links.toString().split('\n');
      linkLines.forEach(line => {
        var parts = line.split(' - ');
        if (parts.length === 2) {
          links.push({ name: parts[0].trim(), url: parts[1].trim() });
        }
      });
    }

    // Parse quizzes (multi-line text)
    var quizzes = [];
    if (obj.quizzes) {
      var quizLines = obj.quizzes.toString().split('\n');
      quizLines.forEach(line => {
        var parts = line.split(' - ');
        if (parts.length === 3) {
          quizzes.push({
            question: parts[0].trim(),
            options: parts[1].split(',').map(opt => opt.trim()),
            correct: parseInt(parts[2].trim())
          });
        }
      });
    }

    lessons.push({
      id: obj.id,
      title: obj.title,
      description: obj.description,
      lesson: obj.lesson,
      links: links,
      quizzes: quizzes
    });
  }

  var result = {
    meta: meta,
    lessons: lessons
  };

  return ContentService.createTextOutput(JSON.stringify(result))
    .setMimeType(ContentService.MimeType.JSON);
}
```
