# Enterprise Retail Analytics Engine: Market Basket Matrix Optimization
### Scalable Data Architecture, Star Schema Transformation & Disconnected Matrix Modeling

## 📊 Business Problem & Analytics Objective
In retail business intelligence, tracking simple aggregate sales metrics fails to reveal transactional cross-selling opportunities. To drive shelf-space optimization and algorithmic product bundles, organizations require data systems that calculate product co-occurrence dynamically. 

This repository demonstrates an end-to-end Power BI solution engineered using the Kaggle Groceries transactional dataset (~38,000 records). 

**The core architectural challenge solved:** Designing a dynamic cross-selling matrix that evaluates multi-item checkouts natively within the data model, bypassing heavy Python/R processing pipelines and eliminating self-filtering schema conflicts.

---

## 🛠️ Data Engineering & Star Schema Architecture
To optimize DAX calculation speed and ensure absolute query performance, the flat transactional log was normalized into a production-grade **Star Schema**:

*   **Fact_Groceries:** Holds core transactional rows. A calculated column `Receipt_ID` was engineered using `[Member_number] & "-" & [Date]` to create unique transaction scopes.
*   **Dim_Date (M-Code Calendar):** A continuous calendar built natively using Power Query M-code, replacing standard automatic time intelligence tables to optimize storage footprint.
*   **Dim_Product:** Primary dimension handling the main inventory master list.
*   **Dim_Product_Basket (Disconnected Table Pattern):** A duplicate, standalone product dimension table left completely out of the active schema relationships. This prevents natural filter propagation from wiping out the cross-product comparison matrix columns.

---

## 🧠 Advanced Analytical Logic (DAX Framework)

### 1. Dynamic Co-occurrence Calculation
To calculate the true cross-selling intersection of Product A (Rows) and Product B (Columns) dynamically, a localized virtual relationship was established inside the measure using `TREATAS`:

```dax
Orders with Both A & B = 
VAR SelectedProductA = SELECTEDVALUE(Dim_Product[Product_Name])
VAR SelectedProductB = SELECTEDVALUE(Dim_Product_Basket[Basket_Product_Name])

RETURN
IF(
    SelectedProductA = SelectedProductB, 
    BLANK(), 
    CALCULATE(
        DISTINCTCOUNT(Groceries_dataset[Receipt_ID]),
        FILTER(
            VALUES(Groceries_dataset[Receipt_ID]),
            CALCULATE(
                DISTINCTCOUNT(Groceries_dataset[itemDescription]),
                TREATAS({SelectedProductB}, Dim_Product[Product_Name])
            ) > 0
        )
    )
)
```

### 2. Product Attachment / Confidence Rate (%)
Evaluates the conditional probability that a customer purchasing an anchor item will simultaneously attach a secondary category to the basket.
```dax
Attachment % = DIVIDE([Orders with Both A & B], [Orders with Product A], 0)
```

---

## 🎨 Executive UI/UX Architecture
The user interface is designed around corporate dashboard best practices:
1.  **Summary Analytics Layer:** Real-time KPI summaries detailing transaction volumes, unique customer counts, and average item density per checkout.
2.  **Affinity Engine Layer:** A dense matrix visualization featuring inline conditional formatting data bars. This allows corporate stakeholders to instantly isolate high-lift cross-selling paths visually.

---

## 🚀 Corporate Consulting & Inquiries
I specialize in engineering high-performance business intelligence architecture, optimizing complex data models, and upskilling data teams on advanced enterprise design patterns.

*   **Connect on LinkedIn:** [Insert Your LinkedIn Link]
*   **Corporate Training & Consulting Inquiries:** [Insert your corporate email address]

