

# مقدمة عن الـ Unit Testing

الـ Unit Testing طريقة من طرق اختبار الـ Software اللي بنعمله، وبتساعدنا نتأكد إن الـ code اللي كتبناه شغال صح.

فيه نوعين أساسيين من الـ Testing:

## 1. الـ White Box Testing (أو الـ Clear Box Testing)
*   الـ Tester هنا بيكون عنده كل التفاصيل الفنية عن الـ System، زي الـ Source Code والـ Architecture Documents والـ Design Documents والـ Database.
*   اللي بيعمل الـ Test ده غالبًا بيكون الـ Developer نفسه، عشان هو اللي فاهم الـ Internal Workings بتاعة الـ code كويس.

## 2. الـ Black Box Testing
*   الـ Tester بيتعامل مع الـ System كـ "صندوق أسود"، يعني مش عارف إيه اللي بيحصل جواه بالظبط، لكن عارف إيه الـ Functions والـ Features اللي المفروض الـ System يعملها.
*   الـ Test ده بيكون من وجهة نظر الـ User.
*   تحت الـ Black Box Testing فيه أنواع كتير زي:
    *   الـ Integration Test.
    *   الـ Functional Test.
    *   الـ Smoke Test.
    *   الـ Sanity Test.
    *   الـ Penetration Test.
    *   الـ Performance Test.

> [!NOTE]
> *   **مهم جداً**: الأنواع دي كلها، بما فيها الـ Unit Testing، بتكمل بعضها، يعني مفيش واحدة فيهم بتغني عن التانية. كل واحدة ليها دورها.

---

# مميزات الـ Unit Testing (ليه بنحتاجها؟)

بشكل عام، الـ Unit Testing معناه إننا بنكتب **Code يختبر الـ Code**. يعني الـ code بتاعك اللي بيشتغل، بتكتب له code تاني مخصوص عشان يتأكد إنه سليم.

1.  **بتقلل الأخطاء (Bugs)**:
    *   الـ Unit Testing بتقلل نسبة الـ Errors في الـ Code بتاعك بشكل كبير.
    *   لما بنعمل Unit Test، بنعمل **Isolation** للـ code اللي بنختبره. يعني بنعزله عن أي حاجة خارجية ممكن تأثر عليه، زي الـ Database أو الـ Web Services اللي بيكلمها.
    *   ليه بنعمل Isolation؟ عشان الـ Dependencies دي ممكن تدخلنا **Non-Predictable Inputs** (بيانات مش متوقعينها)، وده يخلي الـ Test بتاعنا مش دقيق. لما بنعزل الـ code، بنقدر نتوقع الـ Output بدقة ونقارنه بالنتيجة اللي متوقعينها.

2.  **بتسهل الـ Refactoring**:
    *   الـ Refactoring معناه إنك بتعيد هيكلة الـ code بتاعك عشان يكون أحسن وأوضح وأسهل في الصيانة، من غير ما تغير الـ Functionality بتاعته.
    *   لو شغال على **Legacy System** (نظام قديم والـ code بتاعه مش أحسن حاجة)، الـ Unit Tests بتحميك وانت بتعمل Refactoring.
    *   الـ Steps بتكون كالتالي:
        1.  تكتب الـ Unit Tests للـ code القديم الأول، وطبعاً كلها هتكون Fail (فاشلة) في البداية لأنك لسه ما ظبطتش حاجة.
        2.  تبدأ تعمل Refactoring، وكل ما تخلص جزء صغير، ترن الـ Tests تاني.
        3.  لو أي Test فشل، تعرف إن التعديل ده بوظ حاجة فتروح تصلحها لحد ما الـ Test ينجح تاني.
    *   كده الـ Unit Tests بتوفر لك أمان إنك تغير وتعدل في الـ code وانت مطمن إنك مابوظتش حاجة كانت شغالة.

3.  **الـ Automation (أتمتة عملية الـ Testing)**:
    *   ممكن ندخل الـ Unit Tests في عملية الـ Build بتاعة الـ Project بتاعك.
    *   لو أي Unit Test فشل، الـ Build بيقف وما بيخليش الـ System يشتغل.
    *   ده زي الـ Compiler اللي بيوقف الـ Build لو فيه Syntax Errors، لكن الـ Unit Tests بتصطاد الـ Run-Time Errors اللي الـ Compiler ما بيشوفهاش.

4.  **تعتبر Documentation للـ System**:
    *   الـ Unit Tests بتوضح إزاي الـ Code المفروض يشتغل.
    *   لو جه Developer جديد، بدل ما تشرحله الـ Business بالتفصيل، ممكن تديله الـ Test Projects، ومنها هيفهم الـ Features والـ Functionalities اللي الـ System بيقدمها.

5.  **تدعم الـ Test-Driven Development (TDD)**:
    *   الـ TDD هي Methodology بتعتمد على إنك بتكتب الـ Tests الأول كلها تكون Fail، وبعدين تكتب أقل كمية من الـ Code تخلي الـ Tests دي تنجح.
    *   الـ Unit Tests هي أساس الـ TDD، وبتساعدك تكتب code نظيف وقابل للاختبار من البداية.

---

# عيوب الـ Unit Testing (أو مفاهيم خاطئة عنها)

1.  **مش بتمنع كل الأخطاء**:
    *   صحيح بتقلل الأخطاء، لكن بما إنها بتعمل Isolation للـ code، فممكن تظهر مشاكل في الـ Integration بين المكونات المختلفة بعدين، ودي بتغطيها أنواع تانية من الـ Testing.

2.  **مفهوم خاطئ إنها بتزود الـ Development Time**:
    *   ناس كتير بتعتقد إن كتابة الـ Unit Tests بتاخد وقت زيادة.
    *   الرأي ده مش دقيق: لو الـ Task هتاخد منك ساعتين، ممكن كتابة الـ Unit Test تزودها ساعة أو أكتر.
    *   لكن في المقابل، الـ 10 ساعات اللي كنت هتقضيهم في حل مشاكل الـ code بعدين، هيقلوا لساعة أو نص ساعة، وممكن تختفي تماماً لو الـ Test Coverage بتاعك عالي.
    *   فـ فعلياً، الـ Unit Tests بتقلل الوقت الإجمالي للـ Development لو كتبتها صح.

---

# إيه هو الـ "Unit"؟

كلمة "Unit" هنا معناها **مسار تنفيذي واحد (Single Execution Path)**.
*   يعني مثلاً لو عندك Function فيها `If-Else Statement`:
    *   الـ `If Statement` دي لوحدها تعتبر **Unit**.
    *   والـ `Else Statement` دي لوحدها تعتبر **Unit** تانية.
*   في الـ `Switch-Case`، كل `Case` تعتبر **Unit** لوحدها.
*   الـ Unit هو جزء من الـ Code، لو اديته نفس الـ Input، لازم يديك نفس الـ Output.
*   كل Unit بتحتاج على الأقل **Test Case واحدة**، وممكن بعض الـ Units تحتاج أكتر من Test Case.

---