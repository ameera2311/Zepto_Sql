Zepto Inventory Analysis | SQL | PostgreSQL

An end-to-end SQL analytics project exploring product pricing, discount strategies, inventory weight distribution, revenue estimation, and stock availability using the Zepto Inventory Dataset from Kaggle.


📌 Overview

This project analyzes Zepto's grocery inventory data to extract actionable business insights. Zepto operates as a quick-commerce platform, and understanding its product mix, pricing, discounting patterns, and stock availability is critical for operational and marketing decisions.

Business questions answered:


Which products offer the best value through highest discount percentages?
Which high-MRP products are currently out of stock — representing lost revenue?
What is the estimated revenue potential for each product category?
Which premium-priced products carry minimal discounts?
Which categories offer the highest average discounts to customers?
Which products offer the best price-per-gram value for consumers?
How are products distributed across weight categories (Low / Medium / Bulk)?
What is the total inventory weight burden per category?



📂 Dataset

AttributeDetailsSourceKaggle — Zepto Inventory DatasetFilezepto_v2.csvTotal Records~3,200+ SKUs (after cleaning)Product Categories14

Key Columns:

ColumnDescriptionsku_idUnique product identifier (Primary Key)categoryProduct category (e.g., Fruits & Vegetables, Beverages)nameFull product namemrpMaximum Retail Price (converted from paise to rupees)discountPercentDiscount percentage offereddiscountedSellingPriceFinal selling price after discountweightInGmsProduct weight in gramsavailableQuantityUnits available in inventoryoutOfStockBoolean flag for stock availabilityquantityPack quantity


🛠️ Tools & Technologies

LayerToolDatabasePostgreSQLSQL ClientpgAdminReportPDF


🔄 Project Workflow

Step 1 — Database & Table Creation

Created a SQL table with appropriate data types to store inventory data:

sqlCREATE TABLE zepto (
  sku_id SERIAL PRIMARY KEY,
  category VARCHAR(120),
  name VARCHAR(150) NOT NULL,
  mrp NUMERIC(8,2),
  discountPercent NUMERIC(5,2),
  availableQuantity INTEGER,
  discountedSellingPrice NUMERIC(8,2),
  weightInGms INTEGER,
  outOfStock BOOLEAN,
  quantity INTEGER
);

Step 2 — Data Import

Loaded the CSV file using pgAdmin's import feature. If the import feature is unavailable, use:

sql\copy zepto(category, name, mrp, discountPercent, availableQuantity,
            discountedSellingPrice, weightInGms, outOfStock, quantity)
FROM 'data/zepto_v2.csv' WITH (FORMAT csv, HEADER true, DELIMITER ',', QUOTE '"', ENCODING 'UTF8');


Note: Encoding issues (UTF-8 error) were resolved by saving the CSV file in CSV UTF-8 format before importing.



Step 3 — Data Exploration


Counted total number of records in the dataset
Viewed sample records to understand structure and content
Checked for NULL values across all columns
Identified all 14 distinct product categories
Compared in-stock vs. out-of-stock product counts
Detected duplicate product names representing different SKUs (same product in different pack sizes)


Step 4 — Data Cleaning


Removed rows where MRP or discounted selling price was zero (invalid entries)
Converted mrp and discountedSellingPrice from paise to rupees for consistency and readability


sqlUPDATE zepto
SET mrp = mrp / 100,
    discountedSellingPrice = discountedSellingPrice / 100;

Step 5 — SQL Analysis

Wrote structured queries to answer 8 key business questions:

#QueryKey FindingQ1Top 10 products by discount %Dukes Waffy range leads at 51% discountQ2High-MRP products out of stockPatanjali Ghee (₹565), MamyPoko Diapers (₹399) out of stockQ3Estimated revenue by categoryPersonal Care & Home & Cleaning lead in revenue potentialQ4Products with MRP > ₹500 and discount < 10%Premium oils (Dhara, Saffola, Fortune) carry near-zero discountsQ5Top 5 categories by avg discountFruits & Vegetables: 15.46%, Meats: 11.03%Q6Price per gram (best value sort)Onion and basic staples are most economical per gramQ7Products by weight categoryMajority fall under Low (<1,000g) — typical quick-commerce pack sizesQ8Total inventory weight per categoryChocolates & Ice Cream carry the heaviest inventory load


💡 Key Insights


Dukes Waffy products are discounted at 50–51% — the highest in the entire catalog, which may be eroding margins.
Patanjali Cow's Ghee (₹565) and MamyPoko Diapers (₹399) are out of stock despite being high-MRP items — a direct lost revenue signal.
Fruits & Vegetables has the highest average discount (15.46%), making it a strong candidate for loyalty and subscription campaigns.
Premium edible oils (Dhara, Saffola, Fortune) priced above ₹1,000 carry less than 1% discount — functioning as high-margin anchor products.
Chocolates & Ice Cream carry the heaviest physical inventory weight, indicating significant warehouse space allocation.
Most products fall in the Low weight tier (<1,000g), consistent with Zepto's quick-commerce model focused on small daily-use packs.



📋 Business Recommendations


Restock High-MRP Out-of-Stock Products — Premium items like Patanjali Ghee (₹565) and MamyPoko Diapers (₹399) are out of stock; restoring inventory here recovers high-margin revenue immediately.
Reassess Heavy Discounting on Waffy & Cheese Products — Products discounted at 50–51% may be eroding margins; consider capping at 35–40% or running time-limited offers instead.
Target Fruits & Vegetables for Loyalty Campaigns — This category has the highest average discount (15.46%) and drives daily purchases; bundle or subscription deals here can boost repeat orders.
Promote Premium Oils as Full-Price Anchors — High-MRP oils priced above ₹1,000 sell with near-zero discounts; use these as margin anchors while discounting in adjacent categories.
Optimize Warehouse Space Using Weight Data — Chocolates & Ice Cream carry the heaviest inventory weight; reviewing reorder quantities vs. fast-moving lighter categories can improve storage efficiency

---

## 🗂️ Repository Structure

```
zepto-inventory-analysis/
│
├── data/
│   └── zepto_v2.csv
│
├── sql/
│   └── zepto_analysis.sql
│
├── assets/
│   └── dashboard_preview.png
│
└── README.md
```

---

## ▶️ How to Run

### 1. Clone the repository
```bash
git clone https://github.com/your-username/zepto-inventory-analysis.git
cd zepto-inventory-analysis
```

### 2. Set up PostgreSQL
Create the table using this schema:
```sql
CREATE TABLE zepto (
    sku_id SERIAL PRIMARY KEY,
    category VARCHAR(120),
    name VARCHAR(150) NOT NULL,
    mrp NUMERIC(8,2),
    discountPercent NUMERIC(5,2),
    availableQuantity INTEGER,
    discountedSellingPrice NUMERIC(8,2),
    weightInGms INTEGER,
    outOfStock BOOLEAN,
    quantity INTEGER
);
```

Import the CSV:
```sql
COPY zepto FROM '/path/to/zepto_v2.csv' CSV HEADER;
```

### 3. Run the queries
Open `sql/zepto_analysis.sql` in pgAdmin and run all queries.
