# Train Ticket Price Prediction — Regression

## Description

This project builds a regression model to predict train ticket prices based on route, timing, train type, and ticket class. It serves as the analytical foundation for a **price monitoring and alerting system** — a product that scrapes ticket prices periodically, stores them in a database, and notifies users when prices drop below a user-defined threshold for their chosen route and time window.

The notebook covers the full ML pipeline: data cleaning, exploratory data analysis, feature engineering, and comparison of regression models.

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| **Python 3.x** | Core language |
| **pandas** | Data loading, cleaning, manipulation |
| **NumPy** | Numerical operations |
| **scikit-learn** | Encoding, model training, evaluation |
| **seaborn** | Statistical visualizations |
| **matplotlib** | Plot rendering |
| **Jupyter Notebook** | Interactive development environment |

---

## Dataset

The dataset (`data1.csv`) contains ~215,000 Spanish train ticket price records scraped over time.

| Column | Description |
|--------|-------------|
| `insert_date` | Date and time when the price was collected and written to the database |
| `origin` | City of departure (e.g., MADRID, BARCELONA, SEVILLA, VALENCIA, PONFERRADA) |
| `destination` | City of arrival |
| `start_date` | Scheduled train departure datetime |
| `end_date` | Scheduled train arrival datetime |
| `train_type` | Train service name (e.g., AVE, AVE-TGV, MD-AVE, ALVIA, MD-LD) |
| `price` | Ticket price in EUR |
| `train_class` | Ticket class (Turista, Turista Plus, Preferente, Turista con enlace, etc.) |
| `fare` | Fare type (Promo, Promo+, Flexible, Round trip, etc.) |

> **Note:** The raw dataset is excluded from version control via `.gitignore` due to its size. Place `data1.csv` in the project root before running the notebook.

---

## Setup

### 1. Clone the repository

```bash
git clone https://github.com/<your-username>/train-ticket-price-prediction.git
cd train-ticket-price-prediction
```

### 2. Create and activate a virtual environment

```bash
python -m venv venv

# Windows
venv\Scripts\activate

# macOS / Linux
source venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Add the dataset

Place `data1.csv` in the project root directory.

---

## Running the Notebook

```bash
jupyter notebook
```

Open `Muhammad Hamza Saleem Price Prediction project -regression.ipynb` and run all cells top-to-bottom (`Kernel → Restart & Run All`).

---

## EDA Insights

### Route Analysis
- **MADRID** is both the most frequent origin and destination city, with **PONFERRADA** being the least common.
- Routes from Madrid to Barcelona and Madrid to Sevilla show the most ticket volume.

### Train Type
- **AVE** is the most commonly operated train type by a large margin.
- **AVE-TGV** commands the highest median price across all train types.

### Ticket Class & Price
- **Turista** is the most popular class; **Turista Plus** has the highest median price.
- Price distribution is right-skewed — the majority of tickets cluster around €25, with a long tail up to ~€207.

### Fare Type
- **Promo** is the most purchased fare, reflecting price-sensitive demand.

### Price by Route (Boxplot Analysis)
- Madrid → Ponferrada and Madrid → Valencia show higher price variance compared to Madrid → Sevilla.
- Travel time (engineered from `start_date` − `end_date`) is a meaningful predictor: longer routes generally cost more.

---

## Models & Performance

All categorical features were label-encoded. A `travel_time_in_hrs` feature was engineered from `start_date` and `end_date`. The data was split 30% train / 70% test.

| Model | Train R² | Test R² | Notes |
|-------|----------|---------|-------|
| Linear Regression | 0.725 | 0.728 | Baseline; good generalisation, limited capacity |
| Polynomial Regression (degree=2) | 0.821 | 0.824 | **Best model** — captures non-linear interactions |
| Polynomial Regression (degree=3) | 0.805 | 0.807 | Slight drop vs degree-2 |
| Polynomial Regression (degree=4) | 0.707 | 0.709 | Underfitting trend begins |
| Polynomial Regression (degree=5) | 0.614 | 0.616 | Clear underfitting |

**Best model: Polynomial Regression with degree=2**, achieving ~82% explained variance on both train and test sets without overfitting.

> MAE and RMSE metrics can be added by importing `mean_absolute_error` and `mean_squared_error` from `sklearn.metrics`.

---

## Future Enhancements

- **Automated scraping pipeline** — scheduled scraper (e.g., using `APScheduler` or a cron job) to continuously collect live ticket prices from the RENFE API or website.
- **Historical price database** — store scraped records in a PostgreSQL or SQLite database with proper indexing on route, date, and train type.
- **FastAPI prediction endpoint** — serve the trained regression model via a REST API (`POST /predict`) so any frontend or script can request on-demand price estimates.
- **Email alert system** — allow users to register route + price threshold alerts; send automated emails (via `smtplib` or SendGrid) when the live price drops below their target.

---

## Project Structure

```
.
├── Muhammad Hamza Saleem Price Prediction project -regression.ipynb
├── requirements.txt
├── .gitignore
└── README.md
```

---

## Author

**Muhammad Hamza Saleem**
