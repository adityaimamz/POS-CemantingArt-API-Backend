# POS Cemanting Art API Backend

REST API backend for the POS (Point of Sale) system used by the Cemanting Art / E-Coprint business.

This API handles core business workflows such as product management, stock management, selling prices, customers, transactions (`Paid` and `PO`), expenses, financial recap, dashboard statistics, and monthly reports.

## Key Features

- JWT-based login authentication.
- CRUD for master data: products, customers, selling prices, stock, testimonials.
- Transaction management with item details and `Paid` / `PO` statuses.
- Automatic income recap when a transaction is fully paid.
- Main and non-raw-material expense management.
- Dashboard summary (transaction count, customer count, product count, gross income, charts).
- Monthly reporting (income, expenses, net profit calculation).
- Product image upload using `multer`.
- Static file serving for uploads at `/public/uploads`.

## Technology Stack

- Node.js
- Express.js
- Sequelize ORM + Sequelize CLI
- MySQL (`mysql2`)
- JWT (`jsonwebtoken`)
- Bcrypt (`bcrypt`)
- Multer (upload file)
- Morgan (HTTP logger)
- CORS
- Dotenv
- Moment.js
- Puppeteer and PDF-Lib (dependencies are available for document/report generation needs)

## Project Architecture

The project uses a simple modular MVC-style structure:

- `routes/`: API endpoint declarations.
- `controllers/`: business logic per module.
- `models/`: Sequelize model definitions and associations.
- `migrations/`: database schema migrations.
- `seeders/`: initial data seeders.
- `middleware/`: JWT authentication middleware.
- `utils/`: utility modules (for example upload configuration).
- `public/uploads/`: product image storage.

## Directory Structure

```text
.
|-- app.js
|-- config/
|-- controllers/
|-- middleware/
|-- migrations/
|-- models/
|-- public/uploads/
|-- routes/
|-- seeders/
`-- utils/
```

## Environment Configuration

Create a `.env` file in the project root using this example:

```env
PORT=5000

DB_USERNAME=root
DB_PASSWORD=your_password
DB_DATABASE=ecoprint
DB_HOST=127.0.0.1
DB_CONNECTION=mysql

JWT_SECRET=your_jwt_secret
JWT_EXPIRES_IN=1d
```

Notes:

- `DB_CONNECTION` should follow the Sequelize dialect value (example: `mysql`).
- `JWT_SECRET` must be strong and different in production.
- `JWT_EXPIRES_IN` follows JWT time format (example: `1d`, `12h`).

## Installation and Running the Project

1. Install dependencies:

```bash
npm install
```

2. Run database migrations:

```bash
npx sequelize-cli db:migrate
```

3. Run seeders (optional, for initial admin data):

```bash
npx sequelize-cli db:seed:all
```

4. Start the development server:

```bash
npm run dev
```

5. The server runs at:

```text
http://localhost:5000
```

Root endpoint:

- `GET /` -> `Welcome to API E-COPRINT`

## Seeder Account (Development)

The admin seeder is available in `seeders/20240305072320-admin-seeder.js`:

- Email: `admin@gmail.com`
- Password: `admin123`

Note: change default credentials immediately for non-development environments.

## Authentication

Login endpoint:

- `POST /api/auth/login`

On success, the API returns a JWT token. Use it in the header:

```http
Authorization: Bearer <token>
```

Most endpoints require authentication through the `authMiddleware`.

## API Endpoint List

Base URL: `http://localhost:5000`

### Auth

- `POST /api/auth/login`

### Product (`/api/produk`)

- `GET /api/produk` (auth)
- `GET /api/produk/:id` (auth)
- `POST /api/produk` (auth, multipart `gambar`)
- `PUT /api/produk/:id` (auth, multipart `gambar`)
- `DELETE /api/produk/:id` (auth)

### Customer (`/api/customer`)

- `GET /api/customer` (auth)
- `GET /api/customer/:id` (auth)
- `POST /api/customer` (auth)
- `PUT /api/customer/:id` (auth)
- `DELETE /api/customer/:id` (auth)

### Selling Price (`/api/hargajual`)

- `GET /api/hargajual` (auth)
- `GET /api/hargajual/:produk_id` (auth)
- `POST /api/hargajual` (auth)
- `PUT /api/hargajual/:id` (auth)
- `DELETE /api/hargajual/:id` (auth)

### Stock (`/api/stok`)

- `GET /api/stok` (auth)
- `GET /api/stok/:id` (auth)
- `POST /api/stok` (auth)
- `PUT /api/stok/:id` (auth)
- `DELETE /api/stok/:id` (auth)

### Transaction (`/api/transaksi`)

- `GET /api/transaksi`
- `GET /api/transaksi/paid` (auth)
- `GET /api/transaksi/po` (auth)
- `GET /api/transaksi/:id` (auth)
- `POST /api/transaksi` (auth)
- `PUT /api/transaksi/:id` (auth)
- `DELETE /api/transaksi/:id` (auth)

### Testimonial (`/api/testimoni`)

- `GET /api/testimoni` (auth)
- `GET /api/testimoni/:transaksi_id` (auth)
- `POST /api/testimoni` (auth)
- `PUT /api/testimoni/:id` (auth)
- `DELETE /api/testimoni/:id` (auth)

### Expense (`/api/pengeluaran`)

- `GET /api/pengeluaran` (auth)
- `GET /api/pengeluaran/:id` (auth)
- `POST /api/pengeluaran` (auth)
- `PUT /api/pengeluaran/:id` (auth)
- `DELETE /api/pengeluaran/:id` (auth)

### Non-Raw-Material Expense (`/api/nonbaku`)

- `GET /api/nonbaku` (auth)
- `GET /api/nonbaku/:id` (auth)
- `POST /api/nonbaku` (auth)
- `PUT /api/nonbaku/:id` (auth)
- `DELETE /api/nonbaku/:id` (auth)

### Recap (`/api/rekap`)

- `GET /api/rekap` (auth)
- `GET /api/rekap/:id` (auth)

### Report (`/api/laporan`)

- `GET /api/laporan?month=<1-12>` (auth)

### Stock Card (`/api/kartustok`)

- `GET /api/kartustok` (auth)

### Dashboard (`/api/dashboard`)

- `GET /api/dashboard/paidtransaksi` (auth)
- `GET /api/dashboard/potransaksi` (auth)
- `GET /api/dashboard/transaksi` (auth)
- `GET /api/dashboard/customer` (auth)
- `GET /api/dashboard/produk` (auth)
- `GET /api/dashboard/grossincome` (auth)
- `GET /api/dashboard/stokqty` (auth)
- `GET /api/dashboard/incomechart` (auth)
- `GET /api/dashboard/countincomechart` (auth)

## Main Data Entities

Based on models and migrations, the main entities include:

- `User`
- `Produk`
- `HargaJual`
- `Customer`
- `Transaksi`
- `DetailTransaksi`
- `Testimoni`
- `Stok`
- `Pengeluaran`
- `Pengeluaran_Nonbaku`
- `Rekap`

## Implementation Notes

- Image upload only accepts `png`, `jpeg`, and `jpg` formats.
- Image files are stored in `public/uploads` and served as public URLs.
- Several endpoints support `search`, `page`, and `limit` query parameters for pagination/filtering.
- The monthly report query currently uses a hardcoded database name `ecoprint` in raw SQL, so make sure your database name matches.

## Recommended Improvements

- Add OpenAPI/Swagger documentation for a formal API contract.
- Add unit and integration tests.
- Fix the `npm start` script in `package.json` to run the server directly.
- Add stricter request validation (for example Joi/Zod).

## License

ISC
