

# Unit Testing - كتابة أول تست

دي نوتس عن إزاي تكتب أول Unit Test ليك باستخدام C# و xUnit framework.

## الفكرة الأساسية
الفكرة إننا بنعمل مشروع تاني مخصوص للتستات، وظيفته إنه يختبر الكود بتاعنا في المشروع الأصلي ويتأكد إن كل جزء (كل Unit) شغال صح لوحده.

## الخطوات العملية

### ١. تجهيز مشروع التستات

-   في الـ Solution بتاعك، كليك يمين -> **Add** -> **New Project**.
-   اختار **"xUnit Test Project"**.
-   **تسمية المشروع (Convention):** سميه نفس اسم المشروع الأصلي لكن زوّد عليه `.Tests`.
    -   مثال: لو مشروعك اسمه `EMR`، مشروع التستات هيبقى `EMR.Tests`.

### ٢. إضافة مرجع (Reference) للمشروع الأصلي

دي أهم خطوة عشان مشروع التستات يقدر "يشوف" الكود اللي هيختبره.
-   في مشروع التستات، كليك يمين على **Dependencies**.
-   اختار **Add Project Reference**.
-   علّم على اسم مشروعك الأصلي.

### ٣. كتابة كلاس التست

-   هيتم إنشاء ملف افتراضي اسمه `UnitTest1.cs`.
-   **تسمية كلاس التست (Convention):** سمّي الكلاس بنفس اسم الكلاس اللي بتختبره + كلمة `Test`.
    -   مثال: لو بتختبر كلاس `BMICalculator`، يبقى كلاس التست اسمه `BMICalculatorTest`.

### ٤. كتابة ميثود التست (أهم جزء)

#### الـ `[Fact]` Attribute
أي ميثود هتكون عبارة عن تست، لازم تحط فوقها `[Fact]`. دي اللي بتعرّف الـ Test Runner إن الميثود دي المفروض تشتغل كتستة.

#### تسمية ميثود التست (Convention مهم للـ Documentation)
اسم الميثود لازم يكون وصفي جدًا، كأنه بيشرح التست دي بتعمل إيه بالظبط. بنستخدم الـ Pattern ده:

```csharp
[Public | void] MethodName_WhenCondition_ShouldReturnExpectedResult()
```

-   **MethodName:** اسم الميثود اللي بتختبرها.
-   **WhenCondition:** الشرط أو الحالة اللي بتختبرها فيها.
-   **ShouldReturnExpectedResult:** النتيجة المتوقعة من الاختبار ده.

**مثال:**
`CalculateBMI_WhenHeightIsZero_ShouldReturnZero()`
-   **معناها:** "اختبر ميثود `CalculateBMI` لما يكون الطول بصفر، المفروض النتيجة ترجع صفر".

### ٥. هيكل التست من جوه (AAA Pattern)
أي Unit Test بتتكون من 3 أجزاء عشان الكود يبقى منظم وسهل للقراءة:

```csharp
[Fact]
public void CalculateBMI_WhenHeightIsZeroAndWeightIsNotZero_ShouldReturnZero()
{
    // 1. Arrange (التجهيز)
    // هنا بتجهز كل حاجة محتاجها للتست
    var sut = new BMICalculator(); // sut = Subject Under Test

    // 2. Act (التنفيذ)
    // هنا بتشغل الميثود اللي بتختبرها
    var result = sut.CalculateBMI(0, 90);

    // 3. Assert (التأكيد)
    // هنا بتتأكد إن النتيجة اللي طلعت هي اللي أنت متوقعها
    Assert.Equal(0, result);
}
```

- ا   **Arrange (التجهيز):** جهّز الـ objects والمتغيرات اللي محتاجها. المصطلح المشهور هنا هو `sut` اختصار لـ **Subject Under Test** وده بيشير للـ object اللي بنختبره.
-  ا  **Act (التنفيذ):** نفّذ الميثود اللي عايز تختبرها وخزّن النتيجة.
-  ا  **Assert (التأكيد):** استخدم كلاس `Assert` عشان تقارن النتيجة الفعلية (`actual`) بالنتيجة المتوقعة (`expected`). لو الاتنين زي بعض يبقى التست نجحت (Pass ✅)، لو مختلفين يبقى فشلت (Fail ❌).


