## What is DDL (Data Definition Language)?

**DDL** stands for **Data Definition Language**. Think of it as the set of commands we use to create and manage the structure of a database - like building the blueprint of a house before you can put furniture in it.

> - DDL అంటే **Data Definition Language**. దీన్ని మనం డేటాబేస్ నిర్మాణం క్రియేట్ చేసి, మేనేజ్ చేయడానికి వాడే కమాండ్స్ సెట్‌గా ఊహించుకోవచ్చు — అంటే ఒక ఇల్లు కట్టడానికి ముందు బ్లూప్రింట్ (ప్లాన్) వేసుకున్నట్టు.
> - "డేటాబేస్ నిర్మాణం" అంటే డేటాబేస్‌లో డేటాను ఎలా స్టోర్‌ చేయాలి, ఏ టేబుళ్లు ఉండాలి, వాటిలో ఏ కాలమ్స్ ఉండాలి, వాటి డేటా టైప్ ఏమిటి అన్నది ప్లాన్‌ చేయడం. ఉదాహరణకు, నీకు **స్టూడెంట్ ఇన్ఫర్మేషన్** స్టోర్‌ చేయాలి అనుకుంటే, ఒక **Students అనే టేబుల్** క్రియేట్ చేస్తావు. అందులో `StudentID (number)`, `Name (text)`, `Age (number)`, `Class (text)` లాంటి కాలమ్స్ పెడతావు. ఈ టేబుల్ డిజైన్‌ చేసి రూల్స్ డిఫైన్ చేయడమే "డేటాబేస్ నిర్మాణం".

DDL commands help us:
- Create new tables (like making new folders)
- Modify existing tables (like adding new sections to folders)
- Delete tables (like removing folders)
- Set up rules for our data

**Important**: DDL commands change the structure of the database, not the data inside it. They don't give you back any data - just a message saying "success" or "failed."

> - DDL కమాండ్లు మనకి ఇలా హెల్ప్ చేస్తాయి: కొత్త టేబుళ్లు క్రియేట్ చేయడం (ఫోల్డర్లు కొత్తగా క్రియేట్ చేసినట్టే), ఉన్న టేబుళ్లలో మార్పులు చేయడం (ఫోల్డర్‌కి కొత్త సెక్షన్లు యాడ్ చేసినట్టే), టేబుళ్లు డిలీట్ చేయడం (ఫోల్డర్ తొలగించినట్టే), డేటాకు రూల్స్ సెట్ చేయడం. ముఖ్యంగా గుర్తుపెట్టుకో వాల్సిందేమిటంటే, DDL కమాండ్లు డేటాబేస్‌ **స్ట్రక్చర్‌ని మాత్రమే మార్చుతాయి**, లోపల ఉన్న డేటాని కాదు. ఇవి నీకు డేటా తిరిగి ఇవ్వవు, కేవలం "success" లేదా "failed" అనే మెసేజ్ మాత్రమే ఇస్తాయి.
> - "డేటాకు రూల్స్" అంటే టేబుల్‌లో డేటా ఎలా ఉండాలి అనే నిబంధనలు పెట్టడం. ఉదాహరణకి, ఒక **Students టేబుల్** లో `StudentID` ఎప్పుడూ యూనిక్ (unique) గా ఉండాలి, డూప్లికేట్ కాకూడదు అని రూల్ పెట్టవచ్చు. `Name` ఖాళీగా ఉండకూడదు (NOT NULL) అని కూడా రూల్ పెట్టవచ్చు. అలాగే `Age` కాలమ్ లో 0 కన్నా తక్కువ వాల్యూ రాకూడదు అని చెబితే అది కూడా ఒక రూల్ అవుతుంది. ఇవన్నీ మనం డేటా సరిగ్గా, క్లీన్‌గా ఉండటానికి పెట్టే నియమాలు.

---

## Class Notes: Creating Tables with SQL DDL

### 1. The CREATE TABLE Command

When we want to make a new table, we use the `CREATE TABLE` command. It's like creating a new spreadsheet with specific columns.
> - కొత్త టేబుల్ క్రియేట్ చేయాలనుకుంటే మనం CREATE TABLE కమాండ్ వాడతాం. ఇది ఒక కొత్త స్ప్రెడ్షీట్‌ తీసుకుని అందులో మనకు కావాల్సిన కాలమ్స్ పెట్టినట్టే ఉంటుంది.

**Basic Structure:**
```sql
CREATE TABLE table_name (
    column1_name data_type constraints,
    column2_name data_type constraints,
    ...
)
```

### 2. Example: Creating a "Persons" Table

Let's say we want to create a table to store information about people. We need:
- ID (a unique number for each person)
- Person Name
- Birth Date
- Phone Number

**Step-by-step breakdown:**

```sql
CREATE TABLE persons (
    ID int NOT NULL,
    person_name varchar(50) NOT NULL,
    birth_date date,
    phone varchar(15) NOT NULL,
    CONSTRAINT PK_persons PRIMARY KEY (ID)
)
```

> - 2. ఉదాహరణ: **"Persons"** అనే టేబుల్‌ని మనం వ్యక్తుల సమాచారాన్ని పెట్టడానికి క్రియేట్ చేయాలనుకున్నప్పుడు కావాల్సిన ఫీల్డ్‌లు ఇవే — `ID` (ప్రతి వ్యక్తికి ప్రత్యేక సంఖ్య), `person_name` (పేరు), `birth_date` (పుట్టిన తేది), `phone` (ఫోన్ నంబర్). దీన్ని ఈ విధంగా క్రియేట్ చేయొచ్చు: `CREATE TABLE persons ( ID int NOT NULL, person_name varchar(50) NOT NULL, birth_date date, phone varchar(15) NOT NULL, CONSTRAINT PK_persons PRIMARY KEY (ID) );`
> - "CONSTRAINT PK_persons PRIMARY KEY (ID)" అంటే `ID` కాలమ్‌ని **ప్రైమరీ కీ**గా సెట్‌ చేయడం. దీని అర్థం ఏమిటంటే, ప్రతి రోలో `ID` విలువ యూనిక్‌గా ఉండాలి, ఖాళీగా (NULL) ఉండకూడదు. ఉదాహరణకి, ఒక `Persons` టేబుల్‌లో `ID = 1` ఉన్న రో ఉంటే, మరోసారి `ID = 1` తో కొత్త రోని ఇన్సర్ట్ చేయలేం. అలాగే `ID`ని ఖాళీగా వదిలేయలేం. అంటే, ప్రతి వ్యక్తికి ఒక ప్రత్యేక గుర్తింపు నంబర్ ఉండేలా చేసే రూల్‌ని మనం ఇక్కడ పెట్టుతున్నాం.


### 3. Understanding Data Types

**Data Types** tell the database what kind of information each column can store:

- **int**: Numbers only (like 1, 2, 100, 500)
- **varchar(number)**: Text with a maximum length
  - varchar(50) means up to 50 characters
- **date**: Dates (like 2023-05-15)

```
3. **Data Types అర్థం చేసుకోవడం**
   డేటా టైప్స్ అంటే ప్రతి కాలమ్‌లో ఎలాంటి ఇన్ఫర్మేషన్ స్టోర్ అవుతుందో డేటాబేస్‌కి చెప్పే విధానం. ఉదాహరణకి:

* **int**: నంబర్లు మాత్రమే (ఉదా: 1, 2, 100, 500)
* **varchar(number)**: టెక్స్ట్, గరిష్ట లెంగ్త్‌ సెట్ చేయవచ్చు

  * ఉదా: **varchar(50)** అంటే గరిష్టంగా 50 అక్షరాలు వరకు స్టోర్ అవుతాయి
* **date**: తేదీలు (ఉదా: 2023-05-15)
```

### 4. Understanding Constraints

**Constraints** are rules that control what data can go into each column:

- **NOT NULL**: This column must have a value (cannot be empty)
- **PRIMARY KEY**: A special column that uniquely identifies each row (like a student ID number)

```
4. **Constraints అర్థం చేసుకోవడం**
   Constraints అంటే ప్రతి కాలమ్‌లో ఎలాంటి డేటా ఉండాలి అనేది కంట్రోల్ చేసే రూల్స్. ఉదాహరణకి:

* **NOT NULL**: ఈ కాలమ్ ఖాళీగా ఉండకూడదు, తప్పనిసరిగా విలువ ఉండాలి
* **PRIMARY KEY**: ప్రతి రోను యూనిక్‌గా గుర్తించే ప్రత్యేక కాలమ్‌ (ఉదా: విద్యార్థి ID నంబర్)
```

### 5. Column-by-Column Explanation

1. **ID int NOT NULL**: 
   - Stores numbers only
   - Cannot be empty
   - Each person gets a unique number

2. **person_name varchar(50) NOT NULL**:
   - Stores names (text)
   - Maximum 50 characters
   - Cannot be empty (everyone must have a name)

3. **birth_date date**:
   - Stores birth dates
   - Can be empty (some people might not want to share)

4. **phone varchar(15) NOT NULL**:
   - Stores phone numbers (as text because of special characters like +, -, spaces)
   - Maximum 15 characters
   - Cannot be empty

5. **PRIMARY KEY (ID)**:
   - Makes ID the unique identifier
   - No two people can have the same ID

```
5. **కాలమ్ వారీగా వివరణ**

* **ID int NOT NULL**:
  నంబర్లు మాత్రమే స్టోర్ అవుతాయి, ఖాళీగా ఉండకూడదు, ప్రతి వ్యక్తికి ప్రత్యేక నంబర్ ఇస్తుంది.

* **person_name varchar(50) NOT NULL**:
  పేర్లు (టెక్స్ట్) స్టోర్ అవుతాయి, గరిష్టంగా 50 అక్షరాలు వరకు, ఖాళీగా ఉండకూడదు (ప్రతి ఒక్కరికీ పేరు తప్పనిసరి).

* **birth_date date**:
  పుట్టిన తేదీలు స్టోర్ అవుతాయి, ఖాళీగా ఉంచవచ్చు (ఎవరైనా షేర్ చేయకపోతే).

* **phone varchar(15) NOT NULL**:
  ఫోన్ నంబర్లు స్టోర్ అవుతాయి (టెక్స్ట్‌గా, ఎందుకంటే +, -, స్పేస్‌లాంటి ప్రత్యేక సింబల్స్ వాడవచ్చు), గరిష్టంగా 15 అక్షరాలు వరకు, ఖాళీగా ఉండకూడదు.

* **PRIMARY KEY (ID)**:
  `ID`ని యూనిక్ ఐడెంటిఫైయర్‌గా చేస్తుంది, అంటే ఇద్దరికి ఒకే `ID` ఉండకూడదు.
```

### 6. What Happens After Creating a Table?

- The database creates an empty table with your specified columns
- You get a success message, not data
- The table has the structure but no information yet
- You can check if it worked by running: `SELECT * FROM persons`

> - 6. **టేబుల్ క్రియేట్ చేసిన తర్వాత ఏమవుతుంది?**
> - డేటాబేస్ నీ చెప్పిన కాలమ్స్‌తో ఒక ఖాళీ టేబుల్ క్రియేట్ చేస్తుంది. నీకు డేటా రాదు, కేవలం "success" మెసేజ్ మాత్రమే వస్తుంది. టేబుల్‌లో స్ట్రక్చర్ ఉన్నప్పటికీ ఇంకా ఎలాంటి ఇన్ఫర్మేషన్ లేదు. ఇది సరిగ్గా వర్క్ అయ్యిందా అని చెక్ చేయాలంటే, `SELECT * FROM persons` రన్ చేయవచ్చు.


### 7. Important Tips

1. **Save Your Work**: Always save your CREATE TABLE commands in a file so you can use them again later

2. **Getting Your Code Back**: If you lose your original code, you can right-click on the table name and select "Script Table As > CREATE To > New Query" to see the CREATE statement again

3. **Remember**: DDL commands change the database structure, not the data inside it

```
7. **ముఖ్యమైన సూచనలు**

* **మీ పని సేవ్ చేసుకోండి**: CREATE TABLE కమాండ్లను ఎల్లప్పుడూ ఫైల్‌లో సేవ్ చేయండి, తర్వాత మళ్ళీ ఉపయోగించుకోవచ్చు.
* **కోడ్ తిరిగి పొందడం**: ఒరిజినల్ కోడ్ కోల్పోతే, టేబుల్ పేరు పై రైట్-క్లిక్ చేసి `"Script Table As > CREATE To > New Query"` సిలెక్ట్ చేయడం ద్వారా CREATE స్టేట్‌మెంట్ మళ్ళీ చూడవచ్చు.
* **గుర్తుంచుకోండి**: DDL కమాండ్లు డేటాబేస్ స్ట్రక్చర్‌ని మాత్రమే మార్చతాయి, లోపల ఉన్న డేటా కాదు.
```

### 8. Key Vocabulary

- **DDL**: Data Definition Language - commands that change database structure
- **Table**: Like a spreadsheet with rows and columns
- **Column**: A category of information (like "Name" or "Age")
- **Data Type**: What kind of information can go in a column
- **Constraint**: Rules about what data is allowed
- **Primary Key**: A special column that uniquely identifies each row

```
8. **ప్రధాన పదాలు**

* **DDL**: Data Definition Language – డేటాబేస్ స్ట్రక్చర్ మార్చే కమాండ్లు
* **Table**: రోలు, కాలమ్స్ ఉన్న స్ప్రెడ్షీట్ లాంటిది
* **Column**: ఒక రకమైన ఇన్ఫర్మేషన్ కేటగరి (ఉదా: "Name" లేదా "Age")
* **Data Type**: కాలమ్‌లో ఎలాంటి డేటా పెట్టవచ్చో తెలిపేది
* **Constraint**: ఏ డేటా వాడాలో చెప్పే నియమాలు
* **Primary Key**: ప్రతి రోను ప్రత్యేకంగా గుర్తించే ప్రత్యేక కాలమ్
```

This is the foundation of creating database tables - once you understand this, you can create tables to store any kind of organized information!
