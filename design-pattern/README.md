<div dir="rtl" align="right">

الگوهای طراحی (Design Patterns) راه‌حل‌های استاندارد و تکرارپذیر برای مسائل رایج در طراحی نرم‌افزار هستند. هدف آن‌ها این است که کد **قابل نگهداری‌تر، قابل توسعه‌تر و خواناتر** شود.  
در پایتون هم بسیاری از این الگوها استفاده می‌شوند، البته به دلیل انعطاف بالای پایتون بعضی از آن‌ها ساده‌تر پیاده‌سازی می‌شوند.

معمولاً Design Pattern ها به سه دسته اصلی تقسیم می‌شوند:

<div dir="ltr" align="left">

- Creational (ساخت اشیا)
- Structural (ساختار کلاس‌ها)
- Behavioral (رفتار بین اشیا)

</div>

در ادامه مهم‌ترین آن‌ها را با مثال پایتونی توضیح می‌دهم.

---
<div dir="ltr" align="left">

# 1. [Singleton Pattern](files/singleton.md)
# 2. [Factory Pattern](files/factory.md)
# 3. [Observer Pattern](files/observer.md)
# 4. [Strategy Pattern](files/strategy.md)
# 5. [Decorator Pattern](files/decorator.md)
# 6. [Adapter Pattern](files/adapter.md)
# 7. [Command Pattern](files/command.md)
# 8. [Facad Pattern](files/facad.md)
# 9. [Dependecy Injection Pattern](files/dependency-injection.md)


الگو | کاربرد
--- | ---
Singleton | یک instance در کل برنامه
Factory | ساخت object بدون وابستگی به کلاس
Observer | سیستم notification
Strategy | تغییر الگوریتم در runtime
Decorator | اضافه کردن قابلیت به تابع
Adapter | سازگار کردن interface ها
Command | تبدیل request به object

---
</div>


---

# ✅ 1️⃣ Low Coupling (کوپلینگ پایین)

## ‫Coupling یعنی چه؟



**Coupling = میزان وابستگی یک کلاس یا ماژول به کلاس یا ماژول دیگر**

هرچه یک بخش سیستم بیشتر به جزئیات داخلی بخش دیگر وابسته باشد → coupling بالاتر است.

---

## ❌ مثال High Coupling (بد)

<div dir="ltr" align="left">

```python
class MySQLDatabase:
    def connect(self):
        print("Connected to MySQL")


class UserService:
    def __init__(self):
        self.db = MySQLDatabase()  # وابستگی مستقیم و سخت
```

</div>

مشکل‌ها:

- اگر بخواهیم دیتابیس را PostgreSQL کنیم باید UserService را تغییر دهیم ❌  
- تست کردن سخت می‌شود ❌  
- Mock کردن سخت می‌شود ❌  

اینجا **UserService به پیاده‌سازی خاص وابسته است.**

---



## ✅ مثال Low Coupling (خوب)

<div dir="ltr" align="left">

```python
class DatabaseInterface:
    def connect(self):
        pass


class MySQLDatabase(DatabaseInterface):
    def connect(self):
        print("Connected to MySQL")


class UserService:
    def __init__(self, db: DatabaseInterface):
        self.db = db
```
</div>

استفاده:

<div dir="ltr" align="left">

```python
db = MySQLDatabase()
service = UserService(db)
```
</div>

حالا اگر دیتابیس عوض شود:

<div dir="ltr" align="left">

```python
class PostgresDatabase(DatabaseInterface):
    def connect(self):
        print("Connected to Postgres")
```
</div>

هیچ تغییری در `UserService` لازم نیست ✅

---

## 🎯 چرا Low Coupling مهم است؟

- تغییر یک بخش باعث خراب شدن بقیه نمی‌شود  
- تست‌پذیری بالا  
- توسعه آسان  
- قابلیت تعویض implementation  
- مناسب برای پروژه‌های بزرگ  

---

# ✅ 2️⃣ High Cohesion (انسجام بالا)

## 🔹 Cohesion یعنی چه؟

**Cohesion = میزان تمرکز یک کلاس یا ماژول روی یک مسئولیت مشخص**

هرچه یک کلاس فقط یک کار مشخص انجام دهد → cohesion بالاتر است.

---

## ❌ مثال Low Cohesion (بد)

<div dir="ltr" align="left">

```python
class UserManager:

    def create_user(self):
        pass

    def connect_to_database(self):
        pass

    def send_email(self):
        pass

    def generate_pdf_report(self):
        pass
```
</div>

این کلاس همه‌کاره است 😅  
چندین مسئولیت دارد → cohesion پایین.

---

## ✅ مثال High Cohesion (خوب)

<div dir="ltr" align="left">

```python
class UserService:
    def create_user(self):
        pass


class EmailService:
    def send_email(self):
        pass


class ReportService:
    def generate_pdf(self):
        pass
```
</div>


هر کلاس فقط یک وظیفه دارد ✅

این دقیقاً با اصل **Single Responsibility Principle (SRP)** مرتبط است.

---

# 🔥 رابطه Low Coupling و High Cohesion

این دو معمولاً با هم می‌آیند:

| مفهوم | تمرکز روی چیست؟ |
|--------|----------------|
| Low Coupling | وابستگی بین کلاس‌ها کم باشد |
| High Cohesion | هر کلاس فقط یک کار انجام دهد |

در معماری حرفه‌ای هدف این است:

> ✅ کلاس‌ها داخلی منسجم باشند (High Cohesion)  
> ✅ اما بین هم وابستگی کم داشته باشند (Low Coupling)

---

# 🔎 مثال از FastAPI که قبلاً گفتیم

```
API → Service → Repository → DB
```

### چرا Low Coupling است؟

- ‫API به دیتابیس وابسته نیست  
- ‫Service دیتابیس را مستقیم نمی‌سازد  
- ‫Repository فقط با DB کار می‌کند  
- همه چیز با DI وصل شده  

اگر دیتابیس عوض شود → فقط Repository تغییر می‌کند ✅

---

### چرا High Cohesion است؟

- ‫API فقط request/response مدیریت می‌کند  
- ‫Service فقط business logic دارد  
- ‫Repository فقط data access دارد  
- ‫DB فقط اتصال را مدیریت می‌کند  


هر بخش یک مسئولیت مشخص دارد ✅

---

# 🧠 یک تشبیه ساده

فرض کن شرکت داری:

- حسابداری کار حسابداری می‌کند (cohesion بالا)
- فروش کار فروش می‌کند (cohesion بالا)
- این دو واحد مستقل‌اند ولی با هم تعامل دارند (coupling پایین)

اگر حسابدار استعفا دهد، فروش نابود نمی‌شود 😄

---

# 💎 خلاصه حرفه‌ای

فرمول سیستم‌های سالم:

Good Architecture = Low Coupling + High Cohesion
---

</div>
