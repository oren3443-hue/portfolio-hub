# אורן - אוטומציות ופרויקטי AI

אתר תיק עבודות שמתעדכן אוטומטית. האתר מתארח ב-GitHub Pages ומציג כל ריפו ציבורי שלך עם Pages מופעל. workflow ב-n8n מרענן את `projects.json` בהרצה ידנית, על-ידי שאילתה ל-GitHub API ו-commit חזרה לריפו.

## הקבצים בפרויקט

| קובץ | תפקיד |
|------|-------|
| `index.html` | דף ה-portfolio הסטטי (RTL בעברית, ללא תלויות חיצוניות) |
| `projects.json` | מקור הנתונים — נדחף פעם אחת ונדרס אחר-כך על-ידי n8n |
| `n8n-workflow.json` | ה-workflow ל-n8n, מוכן לייבוא |
| `README.md` | המסמך הזה |

---

## 1. יצירת הריפו והעלאת הקבצים

יוצרים ריפו חדש ב-GitHub בשם `portfolio-hub`. הדרך המהירה:

```bash
# מתוך תיקיית הפרויקט
git init
git add index.html projects.json n8n-workflow.json README.md
git commit -m "Initial portfolio hub"
git branch -M main
git remote add origin https://github.com/oren3443-hue/portfolio-hub.git
git push -u origin main
```

או באמצעות `gh`:

```bash
gh repo create oren3443-hue/portfolio-hub --public --source=. --push
```

---

## 2. הפעלת GitHub Pages

1. נכנסים לעמוד הריפו: `https://github.com/oren3443-hue/portfolio-hub`
2. **Settings** ← **Pages**
3. תחת **Build and deployment**:
   - **Source**: Deploy from a branch
   - **Branch**: `main` / `/ (root)`
4. לוחצים **Save**.

כעבור דקה-שתיים האתר יהיה זמין ב:
```
https://oren3443-hue.github.io/portfolio-hub/
```

---

## 3. יצירת Fine-grained Personal Access Token

ה-PAT צריך שני סוגי הרשאות: לקריאת רשימת הריפוז הציבוריים שלך, ולכתיבה ל-`portfolio-hub`.

1. נכנסים ל: `https://github.com/settings/personal-access-tokens`
2. לוחצים **Generate new token** ← **Fine-grained**
3. ממלאים:
   - **Token name**: `n8n-portfolio-sync`
   - **Expiration**: 90 יום (מומלץ)
   - **Repository access**: **All repositories**
4. תחת **Permissions** → **Repository permissions**:
   - `Metadata` → **Read-only** (חובה)
   - `Pages` → **Read-only** (כדי לדעת איזה ריפוז עם Pages מופעל)
   - `Contents` → **Read and write** (כדי לעדכן את `projects.json`)
5. לוחצים **Generate token** ושומרים את הערך במקום בטוח — לא יוצג שוב.

> **הערה**: ה-UI של fine-grained PAT דורש לבחור או "All repositories" או "Selected repositories" באופן גורף. אם רוצים לצמצם את גישת הכתיבה רק ל-`portfolio-hub`, אפשר ליצור שני tokens נפרדים (אחד לקריאה גורפת, אחד לכתיבה לריפו אחד) ולפצל את הלוגיקה ב-n8n.

---

## 4. ייבוא ה-workflow ל-n8n

1. נכנסים ל-n8n ← **Workflows**
2. כפתור **"+"** ← **Import from File**
3. בוחרים את `n8n-workflow.json` מהתיקייה הזו
4. שומרים (Ctrl/Cmd + S)

ה-workflow אמור להופיע עם 6 nodes:

```
Manual Trigger → List Repos → Filter Pages Only → Build JSON → Get Current File SHA → Update File
```

---

## 5. הגדרת משתני סביבה ב-n8n

ה-workflow משתמש בשני משתני סביבה: `GITHUB_USERNAME` ו-`GITHUB_TOKEN`.

### אם n8n רץ ב-Docker

מוסיפים ל-`docker-compose.yml` תחת `environment:`:

```yaml
environment:
  - GITHUB_USERNAME=oren3443-hue
  - GITHUB_TOKEN=ghp_xxxxxxxxxxxxxxxxxxxx
```

ואז:

```bash
docker compose down
docker compose up -d
```

### אם n8n רץ עם npm

מוסיפים ל-`~/.n8n/.env`:

```
GITHUB_USERNAME=oren3443-hue
GITHUB_TOKEN=ghp_xxxxxxxxxxxxxxxxxxxx
```

ואז מפעילים את n8n מחדש.

### אם n8n Cloud

ב-n8n Cloud אין גישה ישירה למשתני סביבה. במקום זאת:
- פותחים את ה-Code node ומחליפים `$env.GITHUB_USERNAME` ב-`"oren3443-hue"` ישירות.
- בשני ה-HTTP Request nodes מחליפים את ה-`{{$env.GITHUB_TOKEN}}` בשימוש ב-**Credentials** מסוג **HTTP Header Auth** עם השם `Authorization` והערך `Bearer ghp_...`.

---

## 6. הרצה ראשונה ובדיקה

1. פותחים את ה-workflow ב-n8n
2. לוחצים **Execute Workflow**
3. בהרצה הראשונה:
   - `List Repos` יחזיר את כל הריפוז הציבוריים שלך (ירוק)
   - `Filter Pages Only` יסנן רק את אלה עם Pages פעיל (ירוק)
   - `Build JSON` יבנה את ה-JSON (ירוק)
   - `Get Current File SHA` ישתמש ב-"Continue on Fail" כי הקובץ עוד לא קיים (כתום — צפוי)
   - `Update File` יצור את הקובץ מאפס (ירוק, status 201)
4. מהרצה שנייה והלאה — כל ה-nodes ירוקים (status 200).

לאחר הרצה מוצלחת, מרעננים את `https://oren3443-hue.github.io/portfolio-hub/` ומוודאים שהפרויקטים האמיתיים מופיעים.

---

## בדיקה מקומית של ה-HTML

אפשר לבחון את `index.html` בלי לדחוף לריפו:

```bash
open index.html
```

אמור להופיע כרטיס לדוגמה אחד מ-`projects.json` ההתחלתי, יחד עם החיפוש, הכותרת וה-footer. החלפת מצב כהה/בהיר במערכת מחליפה את הצבעים אוטומטית.

---

## פתרון בעיות נפוצות

| תופעה | סיבה אפשרית | פתרון |
|--------|-------------|-------|
| `List Repos` מחזיר 401 | Token לא תקף | יוצרים PAT חדש ומעדכנים `GITHUB_TOKEN` |
| `List Repos` מחזיר 403 | Rate limit | ממתינים שעה או משתמשים ב-Token עם הרשאות מורחבות |
| `Update File` מחזיר 422 | חוסר התאמה ב-`sha` | מריצים שוב — `Get Current File SHA` ימשוך את הערך העדכני |
| ה-Pages מציג 404 | האתר עוד לא פורסם | ממתינים 1-2 דקות אחרי לחיצה על Save |
| הכרטיסים לא נטענים | `projects.json` לא נמצא | בודקים שהקובץ באמת בריפו ובשם הנכון |
| התאריך מוצג באנגלית | הדפדפן לא תומך ב-`he-IL` | מרעננים בכרום/ספארי עדכניים |

---

## אוטומציה תקופתית (אופציונלי)

אם רוצים שה-workflow ירוץ אוטומטית בלי לחיצה ידנית, אפשר להחליף את **Manual Trigger** ב-**Schedule Trigger**:

1. מוחקים את ה-`Manual Trigger`
2. מוסיפים `Schedule Trigger`, למשל "Every Day at 03:00"
3. מחברים אותו ל-`List Repos`

זה ירוץ פעם ביום ויעדכן את האתר אוטומטית.
