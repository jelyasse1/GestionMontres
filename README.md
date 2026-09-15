# ⌚ Gestion de Montres — Étude de cas POO (EPFL) · JavaFX

تطبيق احترافي يطبّق **دراسة الحالة** ديال دورة EPFL
"Introduction à la programmation orientée objet (en Java)" — **Semaine 7 : étude de cas (les Montres)**.

> الفكرة من الدرس: نمذجة **منتجات (Produits)** قابلة للبيع — ساعات (Montres) مكوّنة من
> **ميكانيزم (Mécanisme)** و **إكسسوارات (Accessoires)** — مع تطبيق المفاهيم الكبرى:
> الوراثة، تعدد الأشكال (prix + affichage)، الـ interfaces، و النسخ العميق (copie profonde).

---

## 🧠 النموذج الكائني (مطابق للدرس)

```
Produit (abstract)               ← valeurDeBase, prix(), toString(), copie()
 ├── Accessoire                  ← Boîtier, Bracelet, Fermoir, Vitre (sous-classes)
 ├── Mecanisme (abstract)        ← heure
 │    ├── MecanismeAnalogique   implements Analogique     (aiguilles + date)
 │    ├── MecanismeDigital      implements Digital        (chiffres + réveil)
 │    └── MecanismeDouble extends MecanismeAnalogique
 │                              implements Digital         (une seule heure, 2 affichages)
 └── Montre                      ← 1 Mecanisme + collection d'Accessoires
```

### المفاهيم المطبّقة (من الفيديوهات الـ 5)
| الفيديو | المفهوم | أين في الكود |
|---|---|---|
| 1. Présentation & modélisation | الوراثة (est-un) + التركيب (a-un) | كل التسلسل الهرمي |
| 2. Affichage polymorphique | `toString()` متعدد الأشكال | كل كلاس عندو عرضو الخاص |
| 3. Première version | `prix()` كمعالجة عوض معطى | `Produit.prix()` + override فـ `Montre` |
| 4. Modélisation des mécanismes | الـ **interfaces** (Analogique/Digital) للميكانيزم المزدوج | `MecanismeDouble` |
| 5. Copie profonde | نسخ متعدد الأشكال لمجموعة غير متجانسة | `Produit.copie()` + `Montre.copie()` |

`prix()` ديال الساعة = valeurDeBase + prix(الميكانيزم) + مجموع prix(الإكسسوارات) — كلو **بوليمورفيك**.
`copie()` ديال الساعة = نسخة عميقة مستقلة (الميكانيزم + كل إكسسوار كيتنسخو بنوعهم الحقيقي).

---

## ✨ الواجهة و الميزات الإضافية
- **واجهة احترافية و أنيقة** (نفس نظام التصميم: بطاقات، ألوان فخمة، ظلال).
- **بطاقات إحصائية (KPI)**: عدد الساعات، قيمة الكاطالوگ، الثمن المتوسط.
- **بحث فوري** بالموديل أو نوع الميكانيزم.
- **لوحة تفاصيل** كتعرض `toString()` المتعدد الأشكال للساعة المختارة.
- **⧉ Dupliquer** = زر كيوضّح **النسخ العميق** عملياً (نسخة مستقلة تماماً).
- **🌙 وضع ليلي (Dark Mode)**.
- **⬇ تصدير الكاطالوگ لـ PDF / Excel** (بدون مكتبات خارجية).
- **📊 إحصائيات**: PieChart (حسب نوع الميكانيزم) + BarChart (الثمن لكل ساعة).

---

## 🚀 طريقة التشغيل
المتطلبات: **JDK 17** + **Maven** (و JavaFX كيتجاب أوتوماتيكياً من Maven).

```bash
mvn clean javafx:run
```

> ملاحظة: ما كاينش قاعدة بيانات — دراسة الحالة كلها فالذاكرة (بيانات تجريبية فـ `Catalogue`).

---

## 📁 البنية
```
src/main/java/com/bijouterie/
 ├── MainApp.java
 ├── model/        (Produit, Accessoire+4, Mecanisme+3, interfaces, Montre)
 ├── service/      (Catalogue — Singleton + ObservableList)
 ├── controller/   (Catalogue / MontreEdit / Statistiques)
 └── util/         (Theme, ExcelExporter, PdfExporter, AlertHelper)
src/main/resources/
 ├── css/   (styles.css — mode clair/sombre via .root / .root.dark)
 └── fxml/  (CatalogueView, MontreEditView, StatistiquesView)
```

> تم التحقق فعلياً: المشروع كيكومبايلي بـ Java 17 + JavaFX 17 (22 class، 0 أخطاء)،
> و اختبار النموذج أكّد: `prix()` بوليمورفيك صحيح، و **النسخ العميق** كيخلي الأصل سالم،
> و `MecanismeDouble` كيتصرّف كـ Analogique و Digital فنفس الوقت.


---

## 🗄️ إضافة XAMPP (MySQL) — الحفظ الدائم للبيانات

الكاطالوگ ما بقاش فالذاكرة: ولّى **محفوظ فـ MySQL** ديال XAMPP بنمط **DAO + Factory + HikariCP**
(نفس المعمارية ديال المشاريع الأخرى).

### 1) شغّل قاعدة البيانات
1. حل **XAMPP** و كليكي **Start** على **Apache** و **MySQL**.
2. حل `http://localhost/phpmyadmin`
3. دير **Import** للملف `database_schema.sql` (كيصاوب القاعدة `bijouterie_db` + الجداول + بيانات تجريبية).

> إلا حطّيتي password لـ `root`، نسخي `database.properties.example` إلى
> `src/main/resources/database.properties` (هادا الملف محلي، ما كيتشدّش فـ Git)
> وبدّلو فيه.

### 2) شغّل المشروع
```bash
mvn clean javafx:run
```

### 🧩 النمذجة العلائقية (كيفاش رجّعنا الوراثة لجداول)
| الجدول | الدور |
|---|---|
| `montre` | الساعة + خصائص الميكانيزم، مع عمود **`mec_type`** كـ *discriminateur* (`ANALOGIQUE` / `DIGITAL` / `DOUBLE`) |
| `accessoire` | الإكسسوارات (1-N مع الساعة) + عمود `type` (`Boitier`/`Bracelet`/`Fermoir`/`Vitre`) و `ON DELETE CASCADE` |

الـ DAO (`MontreDAOImpl`) كيعاود يبني الكائن بنوعو الحقيقي من `mec_type` و `type`
=> التسلسل الهرمي و تعدد الأشكال كيبقاو خدّامين مزيان بعد القراءة من قاعدة البيانات.

### 📄 الملفات اللي تزادت
- `database_schema.sql`
- `src/main/resources/database.properties`
- `database/DatabaseConnection.java` (Singleton + HikariCP)
- `dao/MontreDAO.java`, `dao/DAOFactory.java`, `dao/impl/MontreDAOImpl.java`
- تحديث: `Montre` (زدنا `id`)، `Catalogue` (كيخدم بالـ DAO)، `pom.xml`، `module-info.java`، `MainApp.stop()`

> تم التحقق: المشروع كيكومبايلي (26 class، 0 أخطاء)، و **كل استعلامات SQL** (الـ schema + 7 استعلامات DAO)
> تحقّقنا منها بـ parser ديال MySQL و كلها صحيحة.
