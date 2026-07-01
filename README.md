# אורן - אוטומציות ופרויקטי AI

תיק עבודות שמתעדכן אוטומטית. סטטי, מתארח ב-GitHub Pages, ומציג כל ריפו ציבורי עם Pages פעיל.

🌐 **האתר החי:** https://madebyoren.com (הכתובת הישנה oren3443-hue.github.io/portfolio-hub מפנה לשם אוטומטית)

## קבצים בריפו

| קובץ | תפקיד |
|------|-------|
| `index.html` | דף ה-portfolio (RTL בעברית, vanilla HTML/CSS/JS) |
| `projects.json` | snapshot של הריפוז — משמש כגיבוי כשה-API של GitHub לא זמין |
| `overrides.json` | כותרות ואייקונים בעברית פר ריפו |
| `favicon.svg` | סמליל האתר (גרדיאנט + ספארקל) |
| `.github/workflows/sync-projects.yml` | סנכרון אוטומטי יומי של ה-snapshot מ-GitHub API |

## מאיפה האתר לוקח את הנתונים

האתר מושך את רשימת הריפוז **ישירות מה-API של GitHub בכל טעינת דף**, כך שכל שינוי (ריפו חדש עם Pages, עדכון תיאור) מופיע מיד אחרי רענון. רק אם ה-API לא זמין (למשל rate limit), האתר נופל חזרה ל-`projects.json` שמתעדכן פעם ביום.

- **תיאור** — מגיע מתיאור הריפו ב-GitHub (About → Description בעמוד הריפו). עורכים שם, מרעננים את האתר, וזה מתעדכן.
- **כותרת ואייקון** — מגיעים מ-`overrides.json`.
- ריפוז להסתרה מוגדרים גם ב-`EXCLUDE_REPOS` ב-workflow וגם ב-`EXCLUDE_REPOS` ב-`index.html` — לעדכן בשניהם.

## איך הסנכרון עובד

ה-workflow `sync-projects.yml` רץ:
- **אוטומטית כל יום** ב-03:00 UTC (≈06:00 ישראל)
- **ידנית** דרך לשונית Actions → "Sync projects.json" → "Run workflow"
- **משורת הפקודה**: `gh workflow run sync-projects.yml --repo oren3443-hue/portfolio-hub`

מה הוא עושה: שולף את כל הריפוז הציבוריים, מסנן רק את אלו עם Pages פעיל (לא fork, לא archived, לא ב-`EXCLUDE_REPOS`), בונה את `projects.json`, ומבצע commit רק אם משהו השתנה.

## עריכת כותרות ואייקונים בעברית

הכותרות והאייקונים גרים בקובץ `overrides.json`. כשנוסף ריפו חדש, הוא יופיע עם שם הריפו באנגלית עד שמוסיפים לו entry. **תיאורים לא עורכים כאן** — אותם עורכים בתיאור הריפו עצמו ב-GitHub.

**הדרך הקלה לערוך:** לחיצה על כפתור **"✏️ עריכת כותרות בעברית"** ב-footer של האתר → פותח את `overrides.json` בעורך של GitHub. שמירה דורשת הרשאת כתיבה (יש רק לי).

מבנה entry:
```json
"<repo-name>": {
  "title": "כותרת בעברית",
  "icon": "🎯"
}
```

ה-Pages יבנה מחדש תוך ~30 שניות והשינוי יופיע באתר.

## הפעלת סנכרון ידנית

כדי לחפש ריפוז חדשים עם Pages בלי לחכות לסנכרון היומי:

**הדרך הקלה:** כפתור **"🔄 סנכרון אתרים חדשים"** ב-footer של האתר → פותח את עמוד ה-Actions → לוחצים `Run workflow` → `Run workflow`.

תוך 15-30 שניות הריצה תסתיים. אם נמצאו ריפוז חדשים, ה-workflow יבצע commit ל-`projects.json` והאתר יתעדכן.

## הסתרת ריפוז מהאתר

לערוך את `EXCLUDE_REPOS` ב-`.github/workflows/sync-projects.yml`:
```yaml
EXCLUDE_REPOS: '["portfolio-hub","payslip-tool"]'
```

הסנכרון הבא ידלג עליהם.
