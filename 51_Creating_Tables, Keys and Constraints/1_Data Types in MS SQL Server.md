Got it 👍 I’ve cleaned this up and rewritten the transcript into clear, well-structured **paragraphs**, while keeping the original meaning and flow intact. This will read like proper study notes or documentation.

---

Before we jump into the creation of tables, it is important to understand the data types available in SQL Server. In this lesson, we will first focus on **numeric data types**, followed by **string**, **date and time**, and **other commonly used data types**.

### Numeric Data Types

The first numeric data type is **BIT**. The BIT data type can store a whole number value of either **0 or 1**. If it does not contain 0 or 1, it can also store a **NULL** value. It occupies **one byte** of memory and is **highly used**, especially for Boolean-type values.

Next is **TINYINT**, which is used to store whole numbers ranging from **0 to 2⁸ − 1**, that is **0 to 255**. It occupies **one byte** in memory and is **less commonly used**.

The next data type is **SMALLINT**, which stores whole numbers between **−2¹⁵ and 2¹⁵ − 1**. It occupies **two bytes** of memory and is also **frequently used**.

After that, we have **INT**, which stands for integer. This is one of the most commonly used numeric data types. It stores whole numbers between **−2³¹ and 2³¹ − 1** and occupies **four bytes** in memory.

Similarly, **BIGINT** is used to store very large whole numbers between **−2⁶³ and 2⁶³ − 1**. It occupies **eight bytes** in memory and is **less commonly used** compared to INT.

If you want to store numbers with decimal values, you can use the **DECIMAL** data type. Its syntax is `DECIMAL(t, r)`. This allows numbers in the range of approximately **−10³⁸ + 1 to 10³⁸ − 1**. Here, **t** represents the total number of digits (both before and after the decimal point), and it can range from **1 to 38**, with a default value of **18**. **r** represents the number of digits after the decimal point, with a default value of **0**, and it can range from **0 to t**. For example, `DECIMAL(5,2)` means a total of five digits, with two digits after the decimal point.

The **SMALLMONEY** data type is used to store small currency values ranging from **−214,748.3648 to 214,748.3647**. It occupies **four bytes** in memory and is **less commonly used**.

The **MONEY** data type is used to store larger currency values. Developers generally prefer MONEY over SMALLMONEY because it supports larger values. It occupies **eight bytes** in memory and is **highly used**.

Next is **FLOAT**, which is used to store floating-point numbers in the approximate range of **−1.79E+308 to 1.79E+308**. It occupies **either four or eight bytes**, depending on precision, and is **commonly used**.

The last numeric data type is **REAL**, which is also used to store floating-point numbers, with a range of approximately **−3.40E+38 to 3.40E+38**. It occupies **four bytes**. Between FLOAT and REAL, **FLOAT is used most of the time**.

### String Data Types

The first string data type is **CHAR(n)**. This is used to store fixed-length character strings. The memory allocation is fixed based on the value of **n**. For example, `CHAR(10)` will always occupy **10 bytes**, regardless of the actual length of the data stored.

Next is **VARCHAR(n)**, which is used to store variable-length character strings. This is useful when the length of the data is not fixed. The maximum length supported is **8000 characters**. It occupies **2 bytes plus the number of characters stored**, and it is **highly used**.

The **TEXT** data type is used to store very large variable-length text data, up to approximately **2 GB**. In practical scenarios, this data type is **rarely used**.

The next data type is **NCHAR(n)**, which is used to store fixed-length **Unicode** character strings, with a maximum length of **4000 characters**. Unicode is a standard encoding system used to represent characters from multiple international languages.

Unicode assigns a unique numeric value to each character. One of the most popular Unicode encodings is **UTF-8**, which uses an 8-bit encoding scheme. In Windows, you can find the Unicode value of a character by selecting it in Microsoft Word and pressing **Alt + X**.

Next is **NVARCHAR(n)**, which is similar to NCHAR but is used for **variable-length Unicode strings**.

The **IMAGE** data type is used to store variable-length binary data such as images, with a storage capacity of **0 to 2 GB**.

### Date and Time Data Types

The **DATETIME** data type is used to store both date and time values. It supports dates ranging from **January 1, 1753 to December 31, 9999**. It occupies **eight bytes** in memory and has an accuracy of **3.33 milliseconds**.

The **DATE** data type stores only date values in the format `YYYY-MM-DD`. The valid range is from **0001-01-01 to 9999-12-31**, and it occupies **three bytes** in memory. Both DATE and DATETIME are **commonly used**.

The **TIME** data type is used to store time values in the format `HH:MM:SS` with fractional seconds. It occupies **3 to 5 bytes** in memory. However, this data type is used only in **rare scenarios**.

The **TIMESTAMP** data type is used to store a unique value that automatically changes whenever a row is inserted or updated. It reads the internal clock of the SQL Server system and is **not frequently used**.

### Other Data Types

The **UNIQUEIDENTIFIER** data type stores a **16-byte globally unique identifier (GUID)**. It is often used when a unique row identifier is required.

The **XML** data type is used to store XML data. XML (Extensible Markup Language) is similar to HTML and is mainly used for data storage and data transfer. This data type is used only when there is a requirement to handle XML data.

---
