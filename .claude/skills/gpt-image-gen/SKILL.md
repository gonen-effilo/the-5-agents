# Skill: gpt-image-gen

מעטפת לקריאת OpenAI Images API ליצירת תמונות.

## שימוש

קבל prompt ו-output path, שלח לـ API, שמור את התמונה.

## דרישות מוקדמות

- `OPENAI_API_KEY` מוגדר ב-`.env`
- `jq` מותקן (או Python כ-fallback)

## קריאת ה-API

### עם jq (מועדף)

```bash
source .env 2>/dev/null || true
curl -s -X POST "https://api.openai.com/v1/images/generations" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gpt-image-2",
    "prompt": "<the prompt>",
    "size": "1024x1024",
    "quality": "medium",
    "output_format": "png"
  }' | jq -r '.data[0].b64_json' | base64 --decode > <output-path>.png
```

### Python fallback (כשאין jq)

```bash
source .env 2>/dev/null || true
RESPONSE=$(curl -s -X POST "https://api.openai.com/v1/images/generations" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d "{
    \"model\": \"gpt-image-2\",
    \"prompt\": \"<the prompt>\",
    \"size\": \"1024x1024\",
    \"quality\": \"medium\",
    \"output_format\": \"png\"
  }")

echo "$RESPONSE" | python3 -c "
import sys, json, base64
data = json.load(sys.stdin)
b64 = data['data'][0]['b64_json']
with open('<output-path>.png', 'wb') as f:
    f.write(base64.b64decode(b64))
print('Image saved.')
"
```

## הערות

- המודל `gpt-image-2` יצא ב-21 באפריל 2026 — הוא קיים ואמיתי.
- אל תחליף את שם המודל. אם יש שגיאה — בדוק API key ו-parameters.
- הפלט תמיד PNG.
- אם `source .env` לא עובד (Windows cmd), טען את המשתנה ידנית לפני הקריאה.
