## 1️⃣ Stored Procedure Definition చూడడం (MOST IMPORTANT)

### Option 1: `sp_helptext` (quick view)

```sql
EXEC sp_helptext 'ETL.uspAddEventTrigger';
```

👉 ఇది ప్రొసీజర్‌లో ఉన్న పూర్తి T-SQL కోడ్‌ని చూపిస్తుంది (line by line).

> ⚠️ Note: If the procedure is encrypted, ఇది పనిచేయదు.

---

### Option 2: SSMS UI (Best for reading)

1. SQL Server Management Studio (SSMS) ఓపెన్ చేయండి
2. Database → **Programmability**
3. **Stored Procedures** → `ETL.uspAddEventTrigger`
4. Right-click → **Modify** (or Script as → ALTER To → New Query Editor Window)

👉 ఇక్కడ complete logic clean‌గా చదవొచ్చు.

---
