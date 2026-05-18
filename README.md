# אורן - אוטומציות ופרויקטי AI

תיק עבודות שמתעדכן אוטומטית. סטטי, מתארח ב-GitHub Pages, ומציג כל ריפו ציבורי עם Pages פעיל.

🌐 **האתר החי:** https://oren3443-hue.github.io/portfolio-hub/

## קבצים בריפו

| קובץ | תפקיד |
|------|-------|
| `index.html` | דף ה-portfolio (RTL בעברית, vanilla HTML/CSS/JS) |
| `projects.json` | רשימת ריפוז GitHub עם Pages — נוצר ומעודכן אוטומטית |
| `overrides.json` | כותרות, תיאורים ואייקונים בעברית פר ריפו |
| `links.json` | אתרים חיצוניים שנוספו ידנית (לא חייבים להיות GitHub Pages) |
| `favicon.svg` | סמליל האתר (גרדיאנט + ספארקל) |
| `.github/workflows/sync-projects.yml` | סנכרון אוטומטי יומי מ-GitHub API |

## איך הסנכרון עובד

ה-workflow `sync-projects.yml` רץ:
- **אוטומטית כל יום** ב-03:00 UTC (≈06:00 ישראל)
- **ידנית** דרך לשונית Actions → "Sync projects.json" → "Run workflow"
- **משורת הפקודה**: `gh workflow run sync-projects.yml --repo oren3443-hue/portfolio-hub`

מה הוא עושה: שולף את כל הריפוז הציבוריים, מסנן רק את אלו עם Pages פעיל (לא fork, לא archived, לא ב-`EXCLUDE_REPOS`), בונה את `projects.json`, ומבצע commit רק אם משהו השתנה.

## עריכת כותרות בעברית

הכותרות בעברית גרות בקובץ `overrides.json` נפרד. כשנוסף ריפו חדש, הוא יופיע עם שם הריפו באנגלית עד שמוסיפים לו entry.

**הדרך הקלה לערוך:** לחיצה על כפתור **"✏️ עריכת כותרות בעברית"** ב-footer של האתר → פותח את `overrides.json` בעורך של GitHub. שמירה דורשת הרשאת כתיבה (יש רק לי).

מבנה entry:
```json
"<repo-name>": {
  "title": "כותרת בעברית",
  "description": "תיאור קצר",
  "icon": "🎯"
}
```

ה-Pages יבנה מחדש תוך ~30 שניות והשינוי יופיע באתר.

## הוספת אתרים חיצוניים (לא GitHub Pages)

הקובץ `links.json` מאפשר להוסיף קישורים לאתרים חיצוניים — מאמרים, דמואים, פרופיל LinkedIn, וכל אתר אחר. הם יופיעו ראשונים ברשת הכרטיסים, לפני הריפוז שמסונכרנים אוטומטית.

**הדרך הקלה לערוך:** כפתור **"➕ הוספת אתרים חיצוניים"** ב-footer של האתר.

מבנה entry (מערך של אובייקטים):
```json
[
  {
    "title": "הבלוג שלי",
    "description": "מאמרים על אוטומציה ו-AI",
    "url": "https://example.com",
    "icon": "📝"
  }
]
```

ריק (`[]`) = אין אתרים חיצוניים.

## הסתרת ריפוז מהאתר

לערוך את `EXCLUDE_REPOS` ב-`.github/workflows/sync-projects.yml`:
```yaml
EXCLUDE_REPOS: '["portfolio-hub","payslip-tool"]'
```

הסנכרון הבא ידלג עליהם.
