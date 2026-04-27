# Compound Interest Debt Calculator

A Flask web application that estimates how long it will take to pay off debt with compound interest, shows a month‑by‑month breakdown (interest, principal payment, remaining balance), and compares the outcome to a fixed 10% simple‑interest loan. Results include summary metrics, an amortization‑style table, and a small bar chart.

## Features

- Debt payoff timeline with daily‑compounded interest (365 periods/year).
- Month‑by‑month breakdown: month, interest, principal payment, accrued interest, remaining principal.
- 20‑year guardrail: rejects scenarios that would take longer than 20 years to repay.
- Comparison to a 10% simple‑interest loan (cost and duration).
- Currency selection for display (USD, EUR, GBP, CNY, JPY, INR, BRL).
- Basic chart (Chart.js) and client‑side “Save as PDF” (html2canvas + jsPDF).

## How It Works

- Route: `GET/POST /` renders a form and, on submit, computes results and renders a results page.
- Core calculation uses daily compounding and monthly payments to iterate principal until paid or the 20‑year cap is hit.
- Simple‑interest scenario at 10% APR is computed for comparison.
- Outputs are passed to Jinja templates and rendered as tables, cards, and a chart.

## Key Files

- `main2.py:418` — Flask app setup.
- `main2.py:422` — `index()` view handling form and results.
- `main2.py:54` — `compound_interest(debt_apr, compound_frequency)` helper.
- `main2.py:69` — `debt_validate(...)` 20‑year viability check.
- `main2.py:211` — `debt_calculate_monthly(...)` month‑by‑month compound interest.
- `main2.py:283` — `debt_accrued_int()` accrued interest rollup.
- `main2.py:307` — `calc_simple_interest(...)` simple‑interest scenario.
- `myfunctions.py:1` — comparison utilities (percent differences, month/year helpers).
- `templates/base.html:1` — base layout.
- `templates/C_results.html:1` — instructions + input form.
- `templates/index2.html:1` — results view (table, summaries, chart, PDF).

## Data Flow

1. User submits APR, Monthly Payment, and Debt Balance (+ currency).
2. Backend converts APR to decimal and validates feasibility within 20 years.
3. If feasible, compounding loop builds lists of per‑month values and rolls up totals; simple‑interest comparison is computed.
4. Results are rendered in `templates/index2.html`.
5. If not feasible (exceeds 20 years), a guidance panel is shown instead.

## Math Overview

- Compounding factor per month:
  - `base = 1 + (apr / 365)`; `factor_month = base^(365 * (1/12))`
  - Interest for the month: `interest = principal * factor_month - principal`
  - New principal: `principal = principal + interest - min_payment`
- 20‑year cap: iterate up to `20 * 12 + 1` months and fail if unpaid.
- Simple interest (for comparison): monthly interest uses `si_apr / 12` on remaining principal with fixed payment.

## Validation

- Custom numeric validation for APR, Monthly Payment, and Debt Balance.
- Business rules:
  - APR must be > 0 and ≤ 80.
  - Monthly payment must be less than the debt balance.
  - All inputs are required.

References:
- `main2.py:353` — `validate_only_numbers` helper.
- `main2.py:362` — `DebtDataForm` with validators.

## Running Locally

Prerequisites: Python 3.10+ recommended.

1. Create a virtual environment and install dependencies:
   - `python -m venv .venv`
   - On macOS/Linux: `source .venv/bin/activate`  •  On Windows: `.venv\\Scripts\\activate`
   - `pip install flask flask-wtf wtforms`

2. Run the app:
   - `python main2.py`
   - Open `http://127.0.0.1:5000` in a browser.

Note: If you use a production server, configure a proper secret key and WSGI server.

## Project Structure

- `main2.py` — Flask app, form, views, and core calculations.
- `myfunctions.py` — comparison/math helpers for simple vs compound results.
- `templates/` — Jinja templates for layout and results UI.
- `__pycache__/` — Python bytecode cache (can be ignored).

## UI and Assets

- Bootstrap is used for layout and components.
- Chart.js renders the small bar chart comparing principal vs total debt.
- html2canvas + jsPDF provide client‑side PDF export buttons on results.

## Limitations and Notes

- The compounding model assumes daily compounding rolling into a monthly step, and a fixed monthly payment.
- Edge cases (very small payments vs high APR) are capped by the 20‑year rule; otherwise, repayment may not converge.
- No server‑side PDF generation; export is client‑side only.
- No authentication; do not deploy as‑is to the public internet without hardening.

## Possible Improvements

- Add a `requirements.txt` and a proper config for environment variables.
- Add unit tests for the math helpers and repayment loop.
- Allow users to set compounding frequency and simple‑interest comparison rate.
- Improve input validation (e.g., better handling of decimals and localization).
- Add CSV export of the amortization table.

