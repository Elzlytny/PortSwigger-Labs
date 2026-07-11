# 🔐 Broken Access Control

تخيل إنك فاتح تطبيق بنك. 🏦

بعد ما سجلت دخولك، قدرت تشوف حسابك، تحول فلوس، وتراجع معاملاتك.

لكن إيه اللي هيحصل لو حاولت:

- 👤 تشوف حساب عميل تاني؟
- 👑 تدخل لوحة تحكم الأدمن؟
- 💰 توافق على قرض مع إنك مستخدم عادي؟
- 🛒 تعدل على طلب كنت دفعت تمنه بالفعل؟

المفروض إن التطبيق يمنع كل المحاولات دي.

وده بالظبط دور **Access Control**.

لما أي مستخدم يحاول يوصل لـ **Resource** أو ينفذ **Action** داخل التطبيق، السيرفر لازم يجاوب على شوية أسئلة قبل ما يسمح بالـ **Request**.

1. 🔑 **Authentication** — مين المستخدم؟
2. 🛡️ **Authorization** — إيه الصلاحيات المسموح بيها؟
3. 🚦 **Access Control** — هل الـ Action دي ضمن صلاحياته؟
4. 🍪 **Session Management** — هل الـ Request تابع لنفس المستخدم اللي سجل دخوله؟

لو التطبيق فشل في تطبيق أي خطوة من الخطوات دي، فهنا بتظهر ثغرات **Broken Access Control**.

---

# 🛡️ Types of Access Control

طيب...

إيه اللي التطبيق بيحاول يحميه أصلاً؟

بشكل عام فيه 3 أنواع رئيسية من الـ **Access Control**.

---

## ⬆️ 1. Vertical Access Control

وده بيمنع المستخدم من الوصول لوظائف أو صلاحيات خاصة بدور أعلى منه.

أمثلة:

- User ➜ Admin
- Employee ➜ Manager

---

## ↔️ 2. Horizontal Access Control

وده بيمنع المستخدم من الوصول لبيانات مستخدم تاني عنده نفس مستوى الصلاحيات.

مثال:

- User A يحاول يشوف بيانات User B.

---

## 🔄 3. Context-dependent Access Control

وده بيعتمد على حالة التطبيق أو ترتيب تنفيذ العمليات.

مثال:

بعد ما تدفع الطلب...

مينفعش ترجع تعدل الكمية أو تضيف منتجات بنفس السعر القديم، لأن التطبيق لازم يفرض ترتيب معين للخطوات.

---

💡 لو قدرت أتجاوز أي نوع من الأنواع دي، فأنا حققت **Unauthorized Access**.

وفي بعض الحالات ده بيؤدي إلى **Privilege Escalation**.

---

# 🚨 Common Broken Access Control Scenarios

دلوقتي عرفنا إيه اللي التطبيق بيحاول يحميه.

السؤال بقى...

**إزاي التطبيقات بتفشل في تطبيق الـ Access Control؟**

تعالى نمشي مع أشهر السيناريوهات، بنفس الترتيب اللي هنقابلها في اللابات.

---

# 🚪 1. Unprotected Administrative Functionality

أبسط سيناريو...

المطور ينسى يحمي صفحة الأدمن أصلاً.

لو عرفت الـ URL...

هتدخل مباشرة.

أحيانًا الرابط بيكون سهل:

```text
/admin
```

وأحيانًا بيكون غير متوقع:

```text
/admin-x83kd92
```

لكن حتى لو كان غير متوقع، ممكن تلاقيه أثناء الـ Recon من أماكن زي:

- 📄 robots.txt
- 🗺️ sitemap.xml
- 🌐 HTML Source Code
- 📜 JavaScript Files
- 🕸️ Burp Suite Sitemap

ولو ملقيناهوش...

نستخدم أدوات **Content Discovery** زي **FFUF**.

### 💡 Example

```http
GET /admin
```

لو الصفحة اتفتحت بدون أي Authorization Check...

فدي تعتبر Broken Access Control.

> ✅ **Key Idea**
>
> معرفة الـ Endpoint لا تعني امتلاك صلاحية استخدامه.
>
> لازم كل Request يتعمله **Server-side Authorization Check**.

---

# 🍪 2. Client-Controlled Authorization

نفترض إن التطبيق بقى بيمنع الوصول للـ Admin Panel.

السؤال هنا...

> 🤔 إزاي السيرفر بيعرف إن المستخدم ده Admin أو User؟

بعض التطبيقات للأسف بتعتمد على بيانات جاية من المستخدم نفسه، زي:

- 🍪 Cookies
- 🔗 URL Parameters
- 📝 Body Parameters
- 🧾 Hidden Form Fields
- 📨 HTTP Headers

مثلاً:

```http
Cookie: Admin=false
```

لو غيرناها إلى:

```http
Cookie: Admin=true
```

والتطبيق سمح بالدخول...

يبقى التطبيق بيثق في بيانات يقدر المستخدم يعدلها.

ونفس الفكرة تنطبق على أي Security-Sensitive Parameter.

مثلاً:

```json
{
    "email":"user@example.com",
    "roleId":1
}
```

نجرب نبعتها في الـ Request:

```json
{
    "email":"user@example.com",
    "roleId":2
}
```

لو بقينا Admin...

يبقى التطبيق بيعتمد على **Client-Controlled Data** في اتخاذ قرار الـ Authorization.

> ✅ **Key Idea**
>
> أي بيانات جاية من الـ Client تعتبر User-Controlled Data.
>
> وبالتالي لا يجوز الاعتماد عليها في اتخاذ قرارات الـ Authorization.

---

# 🔀 3. URL-based Access Control

طيب...

لو غيرنا الـ Cookies والـ Parameters ولسه مفيش حاجة اشتغلت...

ممكن أسأل نفسي سؤال مختلف.

> 🤔 هل التطبيق بيطبق الـ Authorization على الـ URL نفسه؟

بعض التطبيقات بتشتغل خلف **Reverse Proxy** أو Web Server زي:

- Apache
- Nginx

والسيرفرات دي ممكن تستخدم آلية اسمها **URL Rewriting**.

يعني قبل ما الـ Request يوصل للتطبيق...

الـ Path بيتغير.

وفي بعض البيئات بيتبعت الـ Path الجديد داخل Headers زي:

```http
X-Original-URL: /admin
```

أو

```http
X-Rewrite-URL: /admin
```

فلو التطبيق وثق في الـ Headers دي وهي جاية من المستخدم...

هيعتبر إن الـ Request رايح للـ Path الجديد.

مثلاً:

```http
GET /

X-Original-URL: /admin
```

أو

```http
POST /

X-Original-URL: /admin/delete
```

ولو التطبيق اعتمد على الـ Header أثناء تطبيق الـ Authorization...

ممكن نتجاوز الـ URL-based Access Control بالكامل.

---

### 🔎 فيه سيناريو تاني قريب من ده

أحيانًا المشكلة مبتكونش في الـ Headers...

لكن في طريقة السيرفر لمطابقة الـ URL.

بعض السيرفرات أو الـ Frameworks بتعتبر إن المسارات دي كلها واحدة:

```text
/admin
```

```text
/admin/
```

```text
/ADMIN
```

```text
/admin.anything
```

لكن طبقة الـ Access Control ممكن تعتبرهم مختلفين.

في الحالة دي، مجرد تغيير بسيط في شكل الـ URL ممكن يخلي الـ Request يعدي بدون تطبيق الصلاحيات.

علشان كده، أثناء الاختبار جرّب دائمًا:

- إضافة `/` في آخر الـ Path.
- تغيير حالة الأحرف (Uppercase / Lowercase).
- إضافة File Extension.
- تجربة أشكال مختلفة لنفس الـ Endpoint.

> ✅ **Key Idea**
>
> متفترضش إن كل أجزاء التطبيق بتفسر الـ URL بنفس الطريقة.
>
> اختلاف بسيط في مطابقة المسار ممكن يؤدي إلى تجاوز الـ Access Control.

---

# 🔄 4. Method-based Access Control

طيب...

لو جربنا كل اللي فات ولسه مفيش حاجة اشتغلت...

ممكن يكون التطبيق بيربط الـ Authorization بالـ HTTP Method نفسها.

يعني مثلاً...

الـ Request دي:

```http
POST /admin-roles
```

محمية.

لكن نفس الـ Endpoint لو بعتناه بـ:

```http
GET /admin-roles
```

يتنفذ عادي.

في الحالة دي المشكلة مش في:

- ❌ الـ Endpoint
- ❌ الـ Session
- ❌ الـ Cookies
- ❌ الـ Parameters

المشكلة إن التطبيق بيطبق الـ Authorization على POST فقط.

فمجرد تغيير الـ HTTP Method يسمح بتنفيذ نفس العملية.

> ✅ **Key Idea**
>
> الـ Access Control لازم يحمي الـ Functionality نفسها، مش طريقة إرسال الـ Request.

---

# 🚀 What's Next?

السيناريوهات اللي فاتت كلها كانت أمثلة على **Vertical Privilege Escalation**.

لكن **Broken Access Control** أوسع من كده بكتير.

في اللابات الجاية هنغطي:

- ↔️ Horizontal Privilege Escalation
- 🆔 Insecure Direct Object References (IDOR)
- 🔄 Context-dependent Access Control
- 🧩 Multi-step Workflow Bypass
- 🎯 وغيرها من سيناريوهات Broken Access Control.

---

> 🧠 **Think Like an Attacker**
>
> لما تلاقي Endpoint محمي، متفترضش إن مفيش ثغرة.
>
> اسأل نفسك دايمًا:
>
> - هل الـ URL نفسه محمي؟
> - هل التطبيق بيعتمد على Cookies أو Parameters؟
> - هل فيه Headers ممكن تغير قرار الـ Authorization؟
> - هل فيه اختلاف في طريقة تفسير الـ URL؟
> - هل تغيير الـ HTTP Method هيغير سلوك التطبيق؟
> - هل فيه أي جزء من الـ Request يقدر يأثر على قرار الـ Authorization؟
>
> التفكير بالطريقة دي هيخليك تكتشف نسبة كبيرة جدًا من ثغرات **Broken Access Control**.