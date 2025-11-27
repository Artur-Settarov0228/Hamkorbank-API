# Hamkorbank-API
🏦 Hamkorbank API Integration — Django/DRF Backend

Ushbu loyiha Hamkorbank API integratsiyasi uchun mo‘ljallangan backend skeleton.
Tizim foydalanuvchilarga to‘lov yaratish, balans tekshirish, o‘tkazmalar ko‘rish, invoice yaratish kabi xizmatlarni taqdim etadi.

📦 Apps Strukturası
payments/        — To‘lovlar yaratish va boshqarish
accounts/        — Foydalanuvchi hisoblari va balans
invoices/        — Invoice / payment link yaratish
webhook/         — Bankdan keladigan callbacklarni qabul qilish

⚙️ Models
Payment
Field	Type	Tavsif
id	AutoField	Primary Key
from_account	CharField	Jo‘natuvchi account raqami
to_account	CharField	Qabul qiluvchi account raqami
amount	DecimalField	To‘lov summasi
currency	CharField	“UZS” yoki “USD”
description	TextField	To‘lov izohi
status	CharField	pending / success / failed
created_at	DateTimeField	Auto-created
updated_at	DateTimeField	Auto-updated
Account
Field	Type	Tavsif
id	AutoField	Primary Key
account_number	CharField	Account raqami
balance	DecimalField	Hisob balansi
created_at	DateTimeField	Auto-created
updated_at	DateTimeField	Auto-updated
Invoice
Field	Type	Tavsif
id	AutoField	Primary Key
account	FK → Account	Invoice tegishli account
amount	DecimalField	Summasi
currency	CharField	“UZS” yoki “USD”
description	TextField	Izoh
status	CharField	pending / success / failed
created_at	DateTimeField	Auto-created
WebhookLog
Field	Type	Tavsif
id	AutoField	Primary Key
payload	JSONField	Bankdan kelgan ma’lumot
received_at	DateTimeField	Auto-created
processed	BooleanField	Callback qayta ishlanganligi
🌐 API Endpoints
Payments

POST /api/payments/ — yangi to‘lov yaratish

{
  "from_account": "86001234567890",
  "to_account": "86009876543210",
  "amount": 50000,
  "currency": "UZS",
  "description": "Invoice #123"
}


Response

{
  "id": "PAY-20251127-001",
  "status": "pending",
  "amount": 50000,
  "currency": "UZS",
  "created_at": "2025-11-27T20:00:00Z"
}


GET /api/payments/{id}/ — to‘lov tafsiloti

Accounts

GET /api/accounts/{account_id}/balance/ — balans

GET /api/accounts/{account_id}/transactions/ — o‘tkazmalar tarixini olish

Invoices

POST /api/invoices/ — invoice yaratish

{
  "account_id": "86001234567890",
  "amount": 75000,
  "currency": "UZS",
  "description": "Invoice #456"
}

Webhook

POST /api/webhook/hamkorbank/ — bank callback qabul qilish

{
  "payment_id": "PAY-20251127-001",
  "status": "success",
  "processed_at": "2025-11-27T20:05:00Z"
}

🔒 Business Rules

status: pending / success / failed

amount > 0

currency: “UZS” yoki “USD”

Webhook orqali status update qilinadi

HTTPS majburiy, API key / token bilan autentifikatsiya

Xatoliklar JSON formatda qaytariladi

Error misol

{
  "error": "Invalid account number",
  "code": 400
}

🛠 Tech Stack & Features

Django + Django REST Framework

Serializerlar bilan validation

Views: APIView yoki ViewSet

URL routing DRF Routers bilan

Swagger / OpenAPI dokumentatsiya

📘 Yakuniy Izoh

Ushbu README Hamkorbank API backend skeleton sifatida xizmat qiladi.
Keyingi bosqichda DRF kodlari bilan models, serializers, views, urls va Swagger/OpenAPI tayyorlanadi, to‘lovlar va webhooklar test qilinadi.