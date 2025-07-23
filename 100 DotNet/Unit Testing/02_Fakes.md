
تمام، اتفضل ملخص للفيديو باللهجة المصرية العامية، جاهز لـ Obsidian.

---

# Unit Testing - الـ Fakes (النسخ المزيفة)

دي نوتس عن إزاي تختبر كود بيعتمد على أجزاء تانية (dependencies) زي داتابيز أو web service من غير ما تشغل الأجزاء دي فعلاً.

## المشكلة: الـ Dependencies

لما تيجي تعمل Unit Test لكلاس معينة، ساعات الكلاس دي بتكون بتعتمد على كلاس تانية (dependency).
-   **مثال:** كلاس `AppointmentScheduler` اللي بتحدد المواعيد، بتعتمد على `IPatientRegistry` عشان تتأكد إن المريض متسجل الأول.

المشكلة إن الـ `PatientRegistry` الحقيقية ممكن تكون بتتصل بداتابيز. ده بيخلي الـ Unit Test:
1.  **بطيء:** الاتصال بالداتابيز بياخد وقت.
2.  **مش معزول (Not Isolated):** لو الداتابيز فيها مشكلة، التست هيفشل مع إن الكود بتاع `AppointmentScheduler` ممكن يكون سليم.
3.  **صعب التحكم فيه:** صعب تجهز الداتا في الداتابيز لكل سيناريو عايز تختبره.

## الحل: الـ Fakes (أو Test Doubles)

الحل إننا نعمل "نسخة مزيفة" (Fake) من الـ dependency دي. الـ Fake ده بيكون object شبه الأصلي بالظبط، لكنه تحت سيطرتنا الكاملة في التست. إحنا اللي بنقوله يرجع إيه وإمتى.

### إزاي نعمل Fake؟ مكتبة `FakeItEasy`

بدل ما نعمل كلاس مزيفة لكل Interface بإيدينا، بنستخدم مكتبة (NuGet Package) اسمها **FakeItEasy**. دي بتعمل لنا الـ Fakes دي في الرن تايم بسهولة.

-   **التركيب:** كليك يمين على مشروع التستات -> **Manage NuGet Packages** -> ابحث عن `FakeItEasy` واعملها Install.

## خطوات كتابة التست باستخدام Fakes (مثال `AppointmentScheduler`)

هنتبع نفس الـ **AAA Pattern** (Arrange, Act, Assert).

### 1. Arrange (التجهيز)
هنا بنجهز الـ Fake Object وبنحدد سلوكه (Behavior).

```csharp
// using FakeItEasy; لازم تعملها فوق

// 1. اعمل نسخة مزيفة من الـ Interface
var patientRegistry = A.Fake<IPatientRegistry>();

// 2. حدد سلوك النسخة المزيفة
// هنقوله لما حد ينادي ميثود FindPatient بأي string، رجّع null
// كأن المريض مش متسجل
A.CallTo(() => patientRegistry.FindPatient(A<string>.Ignored)).Returns(null as Patient);

// 3. جهز الكلاس اللي هتختبرها واديها النسخة المزيفة
var sut = new AppointmentScheduler(patientRegistry);
```
-   `A.Fake<T>()`: بتنشئ object مزيف من النوع `T`.
-   `A.CallTo(() => ...)`: بتحدد الميثود اللي عايز تبرمج سلوكها.
-   `A<string>.Ignored`: معناها "بغض النظر عن قيمة الـ string اللي هتتبعت".
-   `.Returns(value)`: بتحدد القيمة اللي الميثود هترجعها.

### 2. Act (التنفيذ)
هنا بنشغل الميثود اللي بنختبرها. في الحالة دي إحنا بنتوقع إنها هترمي Exception لأن المريض مش موجود.

```csharp
// بما إننا متوقعين exception، الـ Act والـ Assert هيبقوا في سطر واحد
var action = () => sut.Schedule("123", DateTime.Now);
```

### 3. Assert (التأكيد)
هنا بنتأكد إن الـ Exception اللي توقعناه حصل فعلاً.

```csharp
Assert.Throws<ArgumentException>(action);
```
-   `Assert.Throws<T>()`: بتتأكد إن الكود اللي جواها رمى Exception من النوع `T`.

---

### سيناريو تاني: لو المريض متسجل

هنعمل تست تانية، بس المرة دي هنخلي الـ Fake يرجع object حقيقي.

```csharp
[Fact]
public void Schedule_WhenPatientIsRegistered_AppointmentShouldBeCreated()
{
    // Arrange
    var patientRegistry = A.Fake<IPatientRegistry>();

    // المرة دي هنخليه يرجع object مريض حقيقي
    A.CallTo(() => patientRegistry.FindPatient(A<string>.Ignored)).Returns(new Patient());

    var sut = new AppointmentScheduler(patientRegistry);

    // Act
    var appointmentId = sut.Schedule("123", DateTime.Now);

    // Assert
    // بما إن الميعاد اتحجز، المفروض يرجع ID أكبر من صفر
    Assert.True(appointmentId > 0);
}

```

## الخلاصة
-   الـ **Fakes** بتسمح لنا نعزل الكود اللي بنختبره عن أي dependencies خارجية.
-   بنستخدم مكتبات زي **FakeItEasy** عشان ننشئ الـ Fakes دي بسهولة.
-   بنقدر نتحكم في الـ Fakes عشان نختبر كل السيناريوهات الممكنة (زي إن الداتا ترجع صح، أو ترجع `null`, أو ترمي exception).