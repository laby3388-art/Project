# AI-Powered E-Commerce System

Django + PostgreSQL e-commerce app with a built-in AI product recommendation
engine (content-based "similar products" + popularity-based "trending
products"), matching the project SRS.

## Stack
- Backend: Python / Django
- Database: PostgreSQL
- Frontend: HTML, CSS, Vanilla JavaScript (no frontend framework)
- AI: scikit-learn (TF-IDF + cosine similarity) for content-based recommendations

## Project layout
```
accounts/         # Custom User model, register/login/logout (FR-1)
products/         # Catalog, browsing/search/filter, admin CRUD (FR-2, FR-3)
cart/              # Shopping cart (FR-4)
orders/            # Checkout, order history, admin order management (FR-5)
recommendations/  # AI recommendation engine (FR-6)
templates/         # Shared base template + error pages
static/            # CSS + vanilla JS
```

## Setup

1. Create and activate a virtual environment:
   ```bash
   python -m venv venv
   source venv/bin/activate  # Windows: venv\Scripts\activate
   ```

2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```

3. Create a PostgreSQL database and copy `.env.example` to `.env`,
   filling in your DB credentials and a real `SECRET_KEY`.

4. Run migrations and create an admin account:
   ```bash
   python manage.py makemigrations
   python manage.py migrate
   python manage.py createsuperuser
   ```

5. (Optional) Seed sample catalog data for a quick demo:
   ```bash
   python manage.py seed_demo_data
   ```

6. Run the dev server:
   ```bash
   python manage.py runserver
   ```

Visit `http://127.0.0.1:8000/` for the storefront and `/admin/` for the
Django admin panel. Mark a user as an Administrator actor either via
Django admin (`is_admin_user` / `is_staff`) or `createsuperuser`.

## How the AI recommendation feature works

- **Similar products** (on each product page): TF-IDF vectorizes each
  product's name + description, then ranks other products by cosine
  similarity to the one being viewed. Falls back to same-category products
  if scikit-learn isn't available.
- **Trending products** (homepage): ranks products by view count
  (`ProductView` records) over the last 30 days, falling back to newest
  products when there's no view history yet (cold start).
- Every recommendation includes a short **explanation string**
  (e.g. "Similar to X — same 'Electronics' category") per FR-6.

See `recommendations/services.py` for the full implementation.

## Notes for the ER Diagram deliverable
Core entities: `User`, `Category`, `Product`, `ProductView`, `Cart`,
`CartItem`, `Order`, `OrderItem` — relationships are defined via Django
model fields in each app's `models.py`.
