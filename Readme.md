# מטלה 2 – Gradle ובדיקות יחידה בג'אווה

קורס כלי פיתוח (10142), סמסטר קיץ תשפ"ו.

## חברי הצוות

| שם | ת"ז |
|---|---|
| יובל דרור (ראש צוות) | 213144058 |
| לי-ים צימרמן | 322633082 |

## על הפרויקט

הפרויקט מכיל את המחלקה `App` עם פונקציות עזר (חישובים מתמטיים, עיבוד מחרוזות ורשימות),
ואת סט בדיקות היחידה שלהן ב-JUnit 5.

```
app/src/main/java/org/example/App.java      – הפונקציות הנבדקות
app/src/test/java/org/example/AppTest.java  – בדיקות היחידה
chats/                                      – תיעוד השיחות עם כלי ה-LLM
logs/LEARNING.md                            – תיעוד שיחות הלמידה (חלק 2)
logs/COPILOT.md                             – תיעוד השיחות בכתיבת הבדיקות (חלק 3)
```

## דרישות מקדימות

- **Git**
- **JDK 21** – לדוגמה, ב-Windows:
  ```powershell
  winget install EclipseAdoptium.Temurin.21.JDK
  ```
  לאחר ההתקנה יש לפתוח טרמינל חדש ולוודא: `java -version`

אין צורך להתקין Gradle – הפרויקט כולל Gradle Wrapper (`gradlew`) שמוריד את הגרסה המתאימה אוטומטית.

## הרצת הבדיקות

### 1. שכפול הפרויקט

```bash
git clone https://github.com/yuval3434/Matala2DevOps.git
cd Matala2DevOps
```

### 2. הרצת כל הבדיקות

Windows (PowerShell / CMD):

```powershell
.\gradlew test
```

macOS / Linux:

```bash
chmod +x gradlew
./gradlew test
```

בהרצה הראשונה Gradle יוריד את התלויות, וזה עשוי לקחת כמה דקות.
בסיום אמורה להופיע ההודעה `BUILD SUCCESSFUL`.

### 3. צפייה בתוצאות

- **דוח הבדיקות:** `app/build/reports/tests/test/index.html`
- **דוח כיסוי קוד (JaCoCo):** `app/build/reports/jacoco/test/html/index.html`

את שני הקבצים פותחים בדפדפן. דוח הכיסוי נוצר אוטומטית בכל הרצה של `gradlew test`,
ומראה אילו שורות וענפים (branches) ב-`App.java` הורצו על ידי הבדיקות.

### הרצה מחדש של כל הבדיקות

Gradle מדלג על בדיקות שלא השתנו. כדי להריץ הכול מאפס:

```powershell
.\gradlew clean test
```
