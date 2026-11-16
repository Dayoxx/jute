# Jute

A multi-designer fashion marketplace that brings independent Nigerian and West African designers together in one app. Shoppers can browse, compare, and buy from many labels in one checkout, and designers get a storefront without building their own.

![Status](https://img.shields.io/badge/status-MVP%20in%20development-orange)
![Python](https://img.shields.io/badge/python-3.12+-blue)
![Django](https://img.shields.io/badge/django-5.x-green)
![License](https://img.shields.io/badge/license-MIT-lightgrey)

## Features

**For shoppers**
- Browse products by designer, category, size, colour, and price
- Product pages with size and colour variants and stock status
- Guest and registered checkout
- Secure payments through Paystack
- Order tracking and email notifications

**For designers**
- Storefront with a designer profile and product listings
- Inventory management per size and colour
- Order management and earnings statements
- Automatic commission deduction and split payouts

**For the platform**
- Admin panel for onboarding designers, moderating listings, and handling disputes
- Returns and refund workflow

## Tech stack

| Layer | Technology |
|---|---|
| Backend | Python, Django, Django REST Framework |
| Frontend | Next.js, React, TypeScript |
| Database | PostgreSQL |
| Search | Meilisearch (Phase 2) |
| Media storage | Cloudinary |
| Payments | Paystack |
| Background jobs | Celery with Redis |

## Project structure

```
Jute/
├── backend/
│   ├── config/          # Django settings, URLs, WSGI/ASGI
│   ├── accounts/        # Users, addresses, designer profiles
│   ├── catalog/         # Categories, products, variants, images
│   ├── cart/            # Cart and cart items
│   ├── orders/          # Orders, order items, returns
│   ├── payments/        # Paystack integration and webhooks
│   └── manage.py
├── frontend/            # Next.js storefront and designer pages
└── docs/                # Architecture notes, API docs, decisions
```

## Getting started

### Prerequisites

- Python 3.12 or newer
- Node.js 20 or newer
- PostgreSQL 15 or newer
- A Paystack test account (free to create)

### 1. Clone the repository

```bash
git clone https://github.com/Dayoxx/jute.git
cd Jute
```

### 2. Set up the backend

```bash
cd backend
python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install -r requirements.txt

cp .env.example .env               # then fill in the values
python manage.py migrate
python manage.py createsuperuser
python manage.py runserver
```

The API will be available at `http://localhost:8000`.

### 3. Set up the frontend

```bash
cd frontend
npm install
cp .env.example .env.local         # set NEXT_PUBLIC_API_URL
npm run dev
```

The storefront will be available at `http://localhost:3000`.

## Environment variables

Backend (`backend/.env`):

| Variable | Description |
|---|---|
| `DJANGO_SECRET_KEY` | Secret key for Django. Never commit this. |
| `DJANGO_DEBUG` | `True` for local development, `False` in production |
| `DATABASE_URL` | PostgreSQL connection string |
| `PAYSTACK_SECRET_KEY` | Paystack secret key (test or live) |
| `PAYSTACK_WEBHOOK_SECRET` | Used to verify incoming Paystack webhooks |
| `CLOUDINARY_URL` | Cloudinary credentials for product images |
| `EMAIL_HOST_*` | SMTP settings for order notifications |

Frontend (`frontend/.env.local`):

| Variable | Description |
|---|---|
| `NEXT_PUBLIC_API_URL` | Base URL of the Django API |
| `NEXT_PUBLIC_PAYSTACK_PUBLIC_KEY` | Paystack public key |

## Running tests

```bash
cd backend
python manage.py test
```

Frontend tests:

```bash
cd frontend
npm run test
```

## API overview

| Endpoint | Method | Description |
|---|---|---|
| `/api/products/` | GET | List published products, with filters |
| `/api/products/{slug}/` | GET | Product detail with variants |
| `/api/designers/` | GET | List active designers |
| `/api/designers/{slug}/` | GET | Designer profile and products |
| `/api/cart/` | GET, POST, DELETE | Manage the current cart |
| `/api/orders/` | GET, POST | List orders or start checkout |
| `/api/orders/{reference}/` | GET | Order detail and status |
| `/api/payments/webhook/` | POST | Paystack webhook (signature verified) |

Full API documentation is available at `/api/docs/` when running locally.

## How checkout works

1. The shopper's cart is validated against current stock and prices.
2. An order is created in `pending_payment` status, and the shopper is sent to Paystack.
3. Paystack sends a webhook. The signature is verified, and the payment is confirmed with Paystack's API.
4. Stock is decremented inside a database transaction, and the order moves to `paid`.
5. Each designer's earnings are recorded after commission for payout.

Prices, names, and addresses are snapshotted on each order, so later edits never change past receipts.

## Roadmap

- [x] Project setup and core data models
- [ ] MVP: catalogue, cart, checkout, and admin panel (Phase 1)
- [ ] Designer dashboard, split payouts, search, and reviews (Phase 2)
- [ ] Personalised recommendations and lookbooks (Phase 3)
- [ ] Android and iOS apps (Phase 4)
- [ ] Expansion to other West African markets (Phase 5)

## Contributing

1. Fork the repository and create a branch: `git checkout -b feature/your-feature`
2. Make your changes and add tests where relevant.
3. Run the test suite and confirm it passes.
4. Open a pull request with a clear description of what changed and why.

Please keep commits small and focused, and follow the existing code style. Run `black` and `ruff` on Python code and `npm run lint` on frontend code before committing.

## Security

Please do not open public issues for security vulnerabilities. Email security@mydomain.com instead, and we will respond as quickly as we can.

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.

## Contact

Built by [Enoch Adesiji](https://github.com/Dayoxx). Questions and partnership enquiries: hello@mydomain.com
