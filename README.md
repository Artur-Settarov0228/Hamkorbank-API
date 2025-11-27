 🏦 HamkorBank API Integration Backend (Django REST Framework)

Ushbu loyiha Hamkorbank to‘lov tizimlari bilan integratsiya qilish uchun mo‘ljallangan professional backend skeletonidir.
Loyiha to‘lovlar, balanslar, invoice (payment link) yaratish, va bankdan keladigan webhook callback’larni boshqarish imkonini beradi.

Barcha ma’lumotlar JSON API orqali qaytariladi.

📦 Apps Strukturası
payments/       — Payment yaratish, status boshqarish
accounts/       — Foydalanuvchi accountlari & balanslar
invoices/       — Invoice/payment link yaratish
webhook/        — Hamkorbank callbacklarini qabul qilish

🧩 Models (to‘liq aniqlangan)
payments.models.Payment
id              AutoField(pk)
from_account    FK → Account
to_account      FK → Account
amount          DecimalField(10,2)
currency        CharField(3)   choices=['UZS','USD']
description     CharField(255)
status          CharField(choices=['pending','success','failed'])
created_at      DateTimeField(auto_now_add=True)
updated_at      DateTimeField(auto_now=True)


Business constraints:

amount > 0

from_account.balance >= amount

currency: only UZS or USD

accounts.models.Account
id              AutoField(pk)
account_number  CharField(20, unique)
balance         DecimalField(12,2)
created_at      DateTimeField(auto_now_add=True)
updated_at      DateTimeField(auto_now=True)

invoices.models.Invoice
id              AutoField
account         FK → Account
amount          DecimalField(10,2)
currency        CharField(3)
description     CharField(255)
status          CharField(choices=['pending','paid','expired'], default='pending')
created_at      DateTimeField(auto_now_add=True)

webhook.models.WebhookLog
id              AutoField
payload         JSONField
processed       BooleanField(default=False)
received_at     DateTimeField(auto_now_add=True)

⚙️ Business Rules
1. To‘lov statuslari

pending — yaratilgan

success — bank tasdiqlagan

failed — rad etilgan

2. Validatsiya

amount 0 dan katta bo‘lishi shart

currency faqat UZS / USD

from_account.balance >= amount

3. Webhook

Bankdan kelgan transaction_id bo‘yicha:

Payment status update qilinadi

Account balanslari o‘zgartiriladi

WebhookLog saqlanadi

4. API Security

HTTPS majburiy

Autentifikatsiya: API-Key yoki Bearer Token

5. Xatoliklar formati
{
  "error": "Invalid currency",
  "details": { ... }
}

🌐 Full API Endpoints (base: /api/)
💳 Payments
POST    /api/payments/                  → yangi to‘lov yaratish
GET     /api/payments/{id}/             → to‘lov detali

🧾 Invoices
POST    /api/invoices/                  → invoice yaratish
GET     /api/invoices/{id}/             → invoice detali

🧮 Accounts
GET     /api/accounts/{id}/balance/     → balans
GET     /api/accounts/{id}/transactions/ → o‘tkazmalar listi

📩 Webhook
POST    /api/webhook/hamkorbank/        → bank callback qabul qilish

📘 JSON Misollar
1) Payment yaratish

POST /api/payments/

{
  "from_account": 2,
  "to_account": 5,
  "amount": 125000,
  "currency": "UZS",
  "description": "Premium to'lov"
}


Response:

{
  "id": 91,
  "status": "pending",
  "amount": 125000,
  "currency": "UZS"
}

2) Account balans

GET /api/accounts/2/balance/

{
  "account_id": 2,
  "balance": 4300000,
  "currency": "UZS"
}

3) Invoice yaratish

POST /api/invoices/

{
  "account": 5,
  "amount": 99000,
  "currency": "UZS",
  "description": "Monthly subscription"
}


Response:

{
  "id": 301,
  "status": "pending",
  "payment_link": "https://pay.hamkorbank.uz/invoice/301"
}

4) Webhook callback (bankdan keladi)

POST /api/webhook/hamkorbank/

{
  "transaction_id": 91,
  "status": "success",
  "amount": 125000,
  "currency": "UZS"
}


Response:

{
  "ok": true,
  "updated_payment_id": 91
}

🎯 Yakuniy eslatma

Bu README kompaniyalarda beriladigan haqiqiy backend test-vazifa formatiga 100% mos.

Agar xohlasang, shu README asosida:

✅ Django project skeleton
✅ Models
✅ Serializers
✅ Views
✅ URLs
✅ Swagger konfiguratsiya

hammasini yozib beraman.
