



# Unit Testing - الاختبار باستخدام In-Memory DbContext

إزاي تختبر الكود اللي بيتعامل مباشرة مع الداتابيز (بيستخدم `DbContext`) من غير ما تحتاج داتابيز حقيقية شغالة.

## المشكلة: الـ Tight Coupling مع الـ DbContext

في بعض الأحيان، الكلاس اللي بنختبرها (زي `PatientRegistry`) بتكون بتتعامل مباشرة مع `ApplicationDbContext` بتاع الـ Entity Framework.

-   الكلاس دي مش بتعتمد على Interface نقدر نعمله Fake بسهولة.
-   الـ `DbContext` الأصلي متبرمج إنه يتصل بداتابيز SQL Server حقيقية.
-   لو شغلنا التست كده، هيحاول يتصل بالداتابيز الحقيقية، وده بيبوظ مبدأ الـ Unit Testing اللي بيفرض **العزل التام (Isolation)**.

## الحل: In-Memory Database

الـ Entity Framework Core بيقدم لنا حل ممتاز للمشكلة دي: **In-Memory Database Provider**.

-   دي عبارة عن داتابيز وهمية بتتعمل في الذاكرة (RAM) وقت تشغيل التست، وبمجرد ما التست تخلص، بتتمسح.
-   **ميزتها:**
    1.  **سريعة جدًا:** لأنها في الميموري، مش على الهارد ديسك.
    2.  **معزولة تمامًا:** كل تست تقدر تعمل داتابيز خاصة بيها، فالتستات مش بتأثر على بعض.
    3.  **مش محتاجة setup:** مش محتاج تشغل SQL Server أو أي حاجة.

### الخطوات العملية

#### 1. تركيب الـ Package المطلوبة
-   في مشروع التستات، ادخل على **Manage NuGet Packages**.
-   ابحث عن `Microsoft.EntityFrameworkCore.InMemory` واعملها Install.

#### 2. عمل كلاس DbContext خاصة بالتستات
-   هنعمل كلاس جديدة في مشروع التستات، وليكن اسمها `InMemoryDbContext`.
-   الكلاس دي هتورث (inherit) من الـ `ApplicationDbContext` الأصلية بتاعتنا.

#### 3. تعديل الـ Configuration (أهم خطوة)
-   جوه الكلاس الجديدة دي (`InMemoryDbContext`)، هنعمل `override` لميثود اسمها `OnConfiguring`.
-   بدل ما نستخدم `UseSqlServer` زي الكلاس الأصلية، هنستخدم `UseInMemoryDatabase`.

```csharp
// الكلاس دي جوه مشروع التستات
internal class InMemoryDbContext : ApplicationDbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        // هنا السر كله
        // بنقوله استخدم داتابيز في الميموري بدل SQL Server
        optionsBuilder.UseInMemoryDatabase("EMR");
    }
}
```

#### 4. نقطة مهمة: عزل التستات عن بعضها
-   لو سبنا اسم الداتابيز ثابت ("EMR")، كل التستات اللي هتستخدم الـ `InMemoryDbContext` ده هيستخدموا **نفس الداتابيز** في الميموري. ده معناه إنهم ممكن يأثروا على بعض.
-   **الحل:** ندي لكل تست داتابيز باسم فريد ومختلف. أسهل طريقة هي استخدام `Guid`.

```csharp
// التعديل عشان كل تست يبقى ليها داتابيز لوحدها
optionsBuilder.UseInMemoryDatabase(Guid.NewGuid().ToString());
```

#### 5. كتابة التست
دلوقتي نقدر نكتب التست بتاعتنا عادي جدًا، بس بدل ما نستخدم `ApplicationDbContext`، هنستخدم `InMemoryDbContext` اللي عملناها.

```csharp
[Fact]
public void AddPatient_WhenIdentityNumberIsUnique_PatientShouldBeAdded()
{
    // Arrange
    // 1. جهز الـ In-Memory DbContext
    var dbContext = new InMemoryDbContext();
    var sut = new PatientRegistry(dbContext);

    var patient = new Patient
    {
        Name = "Ibrahim Khalid Elnaggar",
        IdentityNumber = "1234567890",
        Gender = Gender.Male,
        DateOfBirth = new DateTime(1980, 10, 15)
    };

    // Act
    // 2. شغل الميثود اللي بتضيف في الداتابيز (الوهمية)
    sut.AddPatient(patient);

    // Assert
    // 3. اتأكد إن الـ ID بتاع المريض بقى له قيمة (أكبر من صفر)
    // ده معناه إنه اتضاف فعلاً والـ EF Core اداله ID
    Assert.True(patient.Id > 0);
}```

## الخلاصة
-   لما تلاقي الكود بتاعك بيعتمد مباشرة على `DbContext`، استخدم `InMemoryDatabase`.
-   اعمل كلاس جديدة تورث من الـ `DbContext` الأصلي، وغير الـ `Provider` لـ `UseInMemoryDatabase`.
-   ادي لكل تست داتابيز باسم فريد (استخدم `Guid.NewGuid()`) عشان تضمن العزل التام.
-   كده تقدر تختبر اللوجيك بتاعك اللي بيتعامل مع الداتابيز بسرعة ومن غير أي dependencies خارجية.