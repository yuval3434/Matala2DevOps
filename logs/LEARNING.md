# תיעוד שיחות הלמידה – חלק 2

תיעוד השיחות עם כלי LLM ללמידה עצמית על כתיבת בדיקות יחידה ב-Java.
הקבצים המלאים נמצאים בתיקייה `chats/`.

---

# יובל דרור

**כלי:** Claude (claude.ai)
**משתמש:** יובל דרור
**תאריך:** 25/09/2026
**השיחה המלאה (כולל השאלות):** https://claude.ai/share/08f9a3eb-cd0e-4d1c-b5d9-857c512eb7c2

## השאלות בשיחה

1. הסבר על בדיקות יחידה ב-Java עם JUnit 5 ו-Gradle ועל פונקציות ה-Assert העיקריות, בפורמט של כותרת, הסבר קצר ודוגמת קוד לכל נושא ← חלק א
2. איך יודעים אילו מקרי קצה לבדוק – שיטה מסודרת ודוגמה על פונקציה שבודקת אם מספר ראשוני ← חלק ב
3. איך יודעים שהבדיקות מספיקות – code coverage, line מול branch coverage, והוספת JaCoCo ל-build.gradle.kts ← חלק ג
4. "מה זה תבנית Arrange-Act-Assert, ואיך בוחרים שמות טובים לבדיקות? תן דוגמה לפני ואחרי." ← חלק ד

## תשובות המודל
# בדיקות יחידה ב־Java עם JUnit 5 ו־Gradle

---

# חלק א: מה זה בדיקות יחידה ופונקציות הבדיקה העיקריות

## 1. מה זה בדיקת יחידה

בדיקת יחידה היא קוד קטן שבודק חלק אחד קטן בתוכנית, בדרך כלל פונקציה אחת.
נותנים לפונקציה קלט ידוע, ובודקים שהיא מחזירה את התוצאה שציפינו לה.
אם התוצאה שונה, הבדיקה נכשלת ומראה לנו איפה הבעיה.

```java
// הקוד שאנחנו בודקים
public class Calculator {
    public int add(int a, int b) { return a + b; }
    public int divide(int a, int b) { return a / b; }
}
```

```java
// הבדיקה
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class CalculatorTest {
    @Test
    void addTwoNumbers() {
        Calculator calc = new Calculator();
        assertEquals(5, calc.add(2, 3));
    }
}
```

## 2. למה צריך בדיקות יחידה

הן מוצאות באגים מוקדם, לפני שהקוד מגיע למשתמש.
כשמשנים קוד, מריצים את הבדיקות ורואים מיד אם משהו נשבר.
הן גם מתעדות איך הפונקציה אמורה להתנהג.

```java
// שינית את add בטעות ל: return a - b;
// הבדיקה נכשלת מיד:
// expected: <5> but was: <-1>
```

## 3. הגדרה ב־Gradle והרצה

מוסיפים את הספרייה לקובץ ההגדרות של הפרויקט.
את הבדיקות שמים בתיקייה נפרדת, ומריצים אותן בפקודה אחת.

```groovy
// build.gradle
dependencies {
    testImplementation 'org.junit.jupiter:junit-jupiter:5.10.2'
    testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
}

test {
    useJUnitPlatform()
}
```

```
src/main/java/Calculator.java
src/test/java/CalculatorTest.java
```

```bash
./gradlew test
```

## 4. בדיקת שוויון

בודקת שהתוצאה שווה בדיוק לערך שציפינו.
הערך הצפוי תמיד ראשון, והתוצאה בפועל שנייה.
במספרים עשרוניים מוסיפים טווח סטייה קטן, כי החישוב לא תמיד מדויק.

```java
assertEquals(5, calc.add(2, 3));
assertEquals("hello", "hel" + "lo");
assertEquals(0.3, 0.1 + 0.2, 0.0001);   // עם טווח סטייה
```

## 5. בדיקת אי־שוויון

ההפך מהקודמת.
הבדיקה עוברת רק אם שני הערכים שונים.

```java
assertNotEquals(0, calc.add(2, 3));
```

## 6. בדיקת אמת ושקר

בודקת תנאי שמחזיר אמת או שקר.
מתאימה כשהפונקציה מחזירה תשובה של כן או לא.

```java
assertTrue(calc.add(2, 3) > 0);
assertFalse("abc".isEmpty());
```

## 7. בדיקת ערך ריק

בודקת אם אובייקט קיים או ריק.
שימושי כשפונקציה אמורה להחזיר אובייקט, או להחזיר כלום כשלא נמצא.

```java
String name = null;
assertNull(name);

Calculator calc = new Calculator();
assertNotNull(calc);
```

## 8. בדיקת חריגה

בודקת שהפונקציה זורקת שגיאה במקרה לא תקין.
כותבים את סוג השגיאה, ואת הקוד שאמור לזרוק אותה.
אפשר גם לשמור את השגיאה ולבדוק את ההודעה שלה.

```java
assertThrows(ArithmeticException.class, () -> calc.divide(10, 0));

ArithmeticException ex =
    assertThrows(ArithmeticException.class, () -> calc.divide(10, 0));
assertEquals("/ by zero", ex.getMessage());
```

## 9. בדיקה שאין חריגה

ההפך מהקודמת.
הבדיקה עוברת אם הקוד רץ בלי שגיאה.

```java
assertDoesNotThrow(() -> calc.divide(10, 2));
```

## 10. השוואת מערכים

השוואה רגילה של מערכים בודקת אם זה אותו מערך בזיכרון, ולא את התוכן.
לכן יש פונקציה מיוחדת שמשווה איבר אחרי איבר.

```java
int[] expected = {1, 2, 3};
int[] actual   = {1, 2, 3};
assertArrayEquals(expected, actual);
```

## 11. אותו אובייקט בדיוק

בודקת ששני משתנים מצביעים על אותו אובייקט בזיכרון.
זה שונה מבדיקת שוויון, שבודקת רק שהתוכן זהה.

```java
Calculator a = new Calculator();
Calculator b = a;
assertSame(a, b);
assertNotSame(a, new Calculator());
```

## 12. כמה בדיקות ביחד

בדרך כלל הבדיקה עוצרת בכישלון הראשון.
כאן כל הבדיקות רצות, ובסוף רואים את כל הכישלונות ביחד.

```java
assertAll(
    () -> assertEquals(5, calc.add(2, 3)),
    () -> assertEquals(0, calc.add(-1, 1)),
    () -> assertEquals(-4, calc.add(-2, -2))
);
```

## 13. הודעת שגיאה משלך

לכל פונקציה אפשר להוסיף הודעה בסוף.
ההודעה מופיעה רק אם הבדיקה נכשלת, ועוזרת להבין מה השתבש.

```java
assertEquals(5, calc.add(2, 3), "add should return sum of two numbers");
```

---

# חלק ב: איך יודעים אילו מקרי קצה לבדוק

## השיטה בחמישה שלבים

1. **מחלקים את הקלט לקבוצות.** כל קבוצה היא ערכים שהפונקציה אמורה להתנהג עליהם אותו דבר. בודקים ערך אחד מכל קבוצה.
2. **בודקים את הגבולות.** הבאגים מתחבאים בדיוק בנקודה שבה קבוצה אחת נגמרת ושנייה מתחילה.
3. **בודקים ערכים מיוחדים.** אפס, מספרים שליליים, ערך ריק.
4. **בודקים ערכים קיצוניים.** הערך הכי גדול והכי קטן שהטיפוס מסוגל להחזיק.
5. **מסתכלים על הקוד ושואלים איפה הוא יכול לטעות.** לולאות, תנאי עצירה, חישובים שיכולים לחרוג.

## הפונקציה שנבדוק

```java
public class PrimeChecker {
    public static boolean isPrime(int n) {
        if (n < 2) return false;
        if (n == 2) return true;
        if (n % 2 == 0) return false;
        for (int i = 3; i <= n / i; i += 2) {
            if (n % i == 0) return false;
        }
        return true;
    }
}
```

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class PrimeCheckerTest {
    // הבדיקות למטה נכנסות לכאן
}
```

## סוגי מקרי הקצה

### 1. מקרה רגיל

קודם מוודאים שהפונקציה עובדת על מספרים רגילים.
ראשוני אחד ולא ראשוני אחד.

```java
@Test
void typicalPrime() {
    assertTrue(PrimeChecker.isPrime(7));
}

@Test
void typicalNotPrime() {
    assertFalse(PrimeChecker.isPrime(8));
}
```

### 2. הגבול של ההגדרה

מספר ראשוני מתחיל מ־2.
לכן בודקים בדיוק מסביב לנקודה הזאת: מה שלפני, הנקודה עצמה, ומה שאחרי.

```java
@Test
void oneIsNotPrime() {
    assertFalse(PrimeChecker.isPrime(1));
}

@Test
void twoIsPrime() {
    assertTrue(PrimeChecker.isPrime(2));
}

@Test
void threeIsPrime() {
    assertTrue(PrimeChecker.isPrime(3));
}
```

### 3. אפס ושליליים

אלה ערכים שקל לשכוח.
לפי ההגדרה אף אחד מהם לא ראשוני.

```java
@Test
void zeroIsNotPrime() {
    assertFalse(PrimeChecker.isPrime(0));
}

@Test
void negativesAreNotPrime() {
    assertFalse(PrimeChecker.isPrime(-1));
    assertFalse(PrimeChecker.isPrime(-7));
}
```

### 4. מקרה מיוחד בתוך הקוד

הקוד מטפל במספרים זוגיים בנפרד.
שתיים הוא הזוגי הראשוני היחיד, וכל זוגי אחר לא ראשוני.

```java
@Test
void evenNumbersAreNotPrime() {
    assertFalse(PrimeChecker.isPrime(4));
    assertFalse(PrimeChecker.isPrime(100));
}
```

### 5. גבול הלולאה

הלולאה רצה עד השורש של המספר.
אם תנאי העצירה שגוי בצעד אחד, ריבוע של ראשוני ייחשב בטעות לראשוני.

```java
@Test
void squaresOfPrimesAreNotPrime() {
    assertFalse(PrimeChecker.isPrime(9));
    assertFalse(PrimeChecker.isPrime(25));
    assertFalse(PrimeChecker.isPrime(49));
}
```

### 6. ערכים קיצוניים

הערך הכי גדול של מספר שלם הוא במקרה ראשוני.
אם הלולאה הייתה מכפילה את המונה בעצמו, המכפלה הייתה חורגת והתוצאה הייתה שגויה.

```java
@Test
void maxIntIsPrime() {
    assertTrue(PrimeChecker.isPrime(Integer.MAX_VALUE));   // 2147483647
}

@Test
void minIntIsNotPrime() {
    assertFalse(PrimeChecker.isPrime(Integer.MIN_VALUE));
}
```

### 7. מספר גדול שאינו ראשוני

מוודאים שגם במספרים גדולים הפונקציה מוצאת מחלק.
כדאי לבחור מכפלה של שני ראשוניים גדולים, כי שם המחלק נמצא רק בסוף הלולאה.

```java
@Test
void productOfTwoLargePrimesIsNotPrime() {
    assertFalse(PrimeChecker.isPrime(46337 * 46327));   // 2146654799
}
```

## קיצור: הרבה ערכים בבדיקה אחת

כשיש הרבה ערכים מאותו סוג, אפשר לכתוב בדיקה אחת שרצה על כולם.

```java
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.ValueSource;

@ParameterizedTest
@ValueSource(ints = {2, 3, 5, 7, 11, 13, 97, 7919})
void primes(int n) {
    assertTrue(PrimeChecker.isPrime(n));
}

@ParameterizedTest
@ValueSource(ints = {-7, 0, 1, 4, 9, 15, 25, 100})
void notPrimes(int n) {
    assertFalse(PrimeChecker.isPrime(n));
}
```

---

# חלק ג: כיסוי קוד

## מה זה כיסוי קוד

כיסוי קוד מודד איזה חלק מהקוד רץ בזמן הבדיקות.
אם שורה לא רצה אף פעם, אף בדיקה לא בודקת אותה.
כיסוי גבוה לא מוכיח שהבדיקות טובות, אבל כיסוי נמוך מראה בוודאות מה לא נבדק.

## כיסוי שורות מול כיסוי ענפים

**כיסוי שורות** בודק אם כל שורה רצה לפחות פעם אחת.
**כיסוי ענפים** בודק אם כל תנאי קיבל גם אמת וגם שקר.
כיסוי ענפים מחמיר יותר, כי שורה אחת יכולה להכיל כמה מסלולים.

```java
public static String sign(int n) {
    return n >= 0 ? "positive" : "negative";
}
```

```java
@Test
void positive() {
    assertEquals("positive", sign(5));
}
```

```
Line coverage:   100%   (השורה היחידה רצה)
Branch coverage:  50%   (המסלול של negative לא נבדק אף פעם)
```

## הוספת JaCoCo

```kotlin
// build.gradle.kts
plugins {
    java
    jacoco
}

repositories {
    mavenCentral()
}

dependencies {
    testImplementation("org.junit.jupiter:junit-jupiter:5.10.2")
    testRuntimeOnly("org.junit.platform:junit-platform-launcher")
}

jacoco {
    toolVersion = "0.8.12"
}

tasks.test {
    useJUnitPlatform()
    finalizedBy(tasks.jacocoTestReport)   // דוח נוצר אוטומטית אחרי הבדיקות
}

tasks.jacocoTestReport {
    dependsOn(tasks.test)
    reports {
        html.required.set(true)
        xml.required.set(true)
    }
}
```

הרצה:

```bash
./gradlew test
```

הדוח נמצא כאן. פותחים בדפדפן:

```
build/reports/jacoco/test/html/index.html
```

בדוח, שורות ירוקות רצו, אדומות לא רצו, וצהובות רצו רק בחלק מהענפים.

## להכשיל את הבנייה כשהכיסוי נמוך

אפשר לקבוע סף מינימלי.
אם הכיסוי יורד מתחתיו, הבנייה נכשלת.

```kotlin
tasks.jacocoTestCoverageVerification {
    violationRules {
        rule {
            limit {
                counter = "BRANCH"
                minimum = "0.80".toBigDecimal()
            }
        }
    }
}

tasks.check {
    dependsOn(tasks.jacocoTestCoverageVerification)
}
```

```bash
./gradlew check
```

## אז מתי הבדיקות מספיקות

- כיסוי הענפים גבוה, בדרך כלל שמונים אחוז ומעלה.
- כל סוגי מקרי הקצה מהשיטה הקודמת נבדקו.
- כל בדיקה באמת בודקת תוצאה. בדיקה שמריצה קוד בלי לבדוק כלום מעלה את הכיסוי, אבל לא תופסת שום באג.

---

# חלק ד: תבנית הכנה־פעולה־בדיקה ושמות טובים

## מה זה Arrange-Act-Assert

זו דרך לסדר כל בדיקה בשלושה חלקים קבועים:

1. **הכנה** — יוצרים את האובייקטים ואת הנתונים.
2. **פעולה** — קוראים לפונקציה שבודקים. פעם אחת בלבד.
3. **בדיקה** — מוודאים שהתוצאה נכונה.

כך כל מי שקורא את הבדיקה מבין מיד מה קורה בה.

```java
@Test
void withdraw_enoughBalance_reducesBalance() {
    // Arrange
    BankAccount account = new BankAccount(100);

    // Act
    account.withdraw(30);

    // Assert
    assertEquals(70, account.getBalance());
}
```

## איך בוחרים שם טוב

שם טוב אומר שלושה דברים: מה בודקים, באיזה מצב, ומה אמור לקרות.
כשבדיקה נכשלת, השם לבד צריך להסביר מה נשבר.

```
methodName_condition_expectedResult
```

```
withdraw_enoughBalance_reducesBalance
withdraw_amountTooLarge_throwsException
withdraw_negativeAmount_throwsException
```

אפשר גם להוסיף שם קריא שמופיע בדוח:

```java
@Test
@DisplayName("משיכה של יותר מהיתרה זורקת שגיאה")
void withdraw_amountTooLarge_throwsException() { ... }
```

## לפני

```java
@Test
void test1() {
    BankAccount a = new BankAccount(100);
    a.withdraw(30);
    assertEquals(70, a.getBalance());
    a.deposit(50);
    assertEquals(120, a.getBalance());
    assertThrows(IllegalArgumentException.class, () -> a.withdraw(500));
}
```

הבעיות:
- השם לא אומר כלום.
- בודקים שלושה דברים שונים בבדיקה אחת.
- אם הבדיקה נכשלת, לא ברור איזה חלק נשבר.
- אין הפרדה בין הכנה, פעולה ובדיקה.

## אחרי

```java
class BankAccountTest {

    @Test
    void withdraw_enoughBalance_reducesBalance() {
        // Arrange
        BankAccount account = new BankAccount(100);

        // Act
        account.withdraw(30);

        // Assert
        assertEquals(70, account.getBalance());
    }

    @Test
    void deposit_positiveAmount_increasesBalance() {
        // Arrange
        BankAccount account = new BankAccount(70);

        // Act
        account.deposit(50);

        // Assert
        assertEquals(120, account.getBalance());
    }

    @Test
    void withdraw_amountTooLarge_throwsException() {
        // Arrange
        BankAccount account = new BankAccount(100);

        // Act + Assert
        assertThrows(IllegalArgumentException.class,
                     () -> account.withdraw(500));
    }
}
```

מה השתפר:
- כל בדיקה בודקת דבר אחד.
- השם מסביר בדיוק מה נבדק.
- כל בדיקה עומדת בפני עצמה ולא תלויה בבדיקה אחרת.
- בבדיקת שגיאה, הפעולה והבדיקה נכתבות ביחד, כי הפונקציה עוטפת את הקריאה.
