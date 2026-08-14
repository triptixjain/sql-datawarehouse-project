# SQL Data Warehouse Project

A comprehensive data warehouse solution built with SQL Server, featuring ETL processes, dimensional data modeling, and advanced analytics. This project demonstrates enterprise-level data warehouse design patterns and modern analytics implementation using the Medallion Architecture (Bronze, Silver, Gold layers).

## 🎯 Project Overview

This project implements a complete data warehouse architecture using SQL Server with:

- **Medallion Architecture** - Structured data layers (Bronze → Silver → Gold)
- **Dimensional Modeling** - Star and snowflake schema design
- **ETL Pipelines** - Automated Extract, Transform, Load processes
- **Data Quality** - Validation and reconciliation processes
- **Analytics** - OLAP cubes and advanced reporting
- **Performance Optimization** - Indexing and query tuning

## 🏗️ Architecture: Medallion Pattern

```
┌─────────────────┐
│  Source Systems │ (ERP & CRM)
└────────┬────────┘
         │
         ▼
┌──────────────────────────────┐
│  BRONZE LAYER                │ Raw data ingestion
│  (As-is from sources)        │
│  - CSV imports               │
│  - No transformation         │
└────────┬─────────────────────┘
         │
         ▼
┌──────────────────────────────┐
│  SILVER LAYER                │ Data cleaning & transformation
│  (Cleansed & Standardized)   │
│  - Data quality checks       │
│  - Standardization           │
│  - Normalization             │
└────────┬─────────────────────┘
         │
         ▼
┌──────────────────────────────┐
│  GOLD LAYER                  │ Business-ready analytics
│  (Facts & Dimensions)        │
│  - Star schema               │
│  - Fact tables               │
│  - Dimension tables          │
└────────┬─────────────────────┘
         │
         ▼
┌──────────────────────────────┐
│  Analytics & Reporting       │ BI & Dashboards
└──────────────────────────────┘
```

## 📊 Schema Design

### Dimension Tables
- **DimDate** - Time dimension for temporal analysis
- **DimCustomer** - Customer master data
- **DimProduct** - Product catalog
- **DimLocation** - Geographic dimensions

### Fact Tables
- **FactSales** - Transaction-level sales data
- **FactInventory** - Inventory tracking
- **FactMetrics** - KPI measurements

## 🚀 Getting Started

### Prerequisites

- SQL Server 2019 or later
- SQL Server Management Studio (SSMS)
- T-SQL knowledge
- ~500 MB disk space

### Installation

1. **Clone the repository:**
   ```bash
   git clone https://github.com/triptixjain/sql-datawarehouse-project.git
   cd sql-datawarehouse-project
   ```

2. **Create the database:**
   ```sql
   -- Execute in SSMS
   CREATE DATABASE DataWarehouse;
   USE DataWarehouse;
   ```

3. **Run setup scripts in order:**
   ```bash
   # 1. Bronze layer (raw data ingestion)
   # Execute: scripts/bronze/01_create_bronze_tables.sql
   # Execute: scripts/bronze/02_load_raw_data.sql
   
   # 2. Silver layer (data cleaning)
   # Execute: scripts/silver/01_create_silver_tables.sql
   # Execute: scripts/silver/02_cleanse_transform_data.sql
   
   # 3. Gold layer (star schema & analytics)
   # Execute: scripts/gold/01_create_dimensions.sql
   # Execute: scripts/gold/02_create_facts.sql
   # Execute: scripts/gold/03_load_gold_data.sql
   ```

4. **Verify installation:**
   ```sql
   SELECT * FROM INFORMATION_SCHEMA.TABLES 
   WHERE TABLE_SCHEMA = 'dbo'
   ORDER BY TABLE_NAME
   ```

## 🔄 ETL Processes

### Data Layers Overview

| Layer | Purpose | Processing | Input | Output |
|-------|---------|-----------|-------|--------|
| **Bronze** | Raw data ingestion | As-is storage from source systems | CSV files from ERP/CRM | Raw tables |
| **Silver** | Data cleansing | Standardization, validation, transformation | Bronze tables | Cleansed tables |
| **Gold** | Business-ready analytics | Star schema, fact & dimension tables | Silver tables | Facts & Dimensions |

### Key ETL Procedures

| Procedure | Layer | Purpose |
|-----------|-------|---------|
| `uspLoadBronze` | Bronze | Load raw data from CSV sources |
| `uspValidateBronzeData` | Bronze | Data quality checks on raw data |
| `uspCleanSilverData` | Silver | Data quality & cleansing |
| `uspTransformSilverData` | Silver | Standardization & transformation |
| `uspLoadGoldDimensions` | Gold | Create dimension tables |
| `uspLoadGoldFacts` | Gold | Create fact tables |
| `uspReconcileData` | All | Source-to-target reconciliation |

## 📈 Analytics & Queries

### Sample Analytical Queries

**Top 10 Products by Revenue:**
```sql
SELECT TOP 10
    p.ProductName,
    SUM(fs.SalesAmount) AS TotalRevenue,
    COUNT(fs.SalesID) AS TransactionCount,
    AVG(fs.SalesAmount) AS AvgSaleAmount
FROM FactSales fs
INNER JOIN DimProduct p ON fs.ProductKey = p.ProductKey
GROUP BY p.ProductName
ORDER BY TotalRevenue DESC
```

**Sales by Region and Quarter:**
```sql
SELECT
    l.Region,
    d.QuarterName,
    d.Year,
    SUM(fs.SalesAmount) AS QuarterlyRevenue,
    COUNT(DISTINCT fs.CustomerKey) AS UniqueCustomers
FROM FactSales fs
INNER JOIN DimLocation l ON fs.LocationKey = l.LocationKey
INNER JOIN DimDate d ON fs.DateKey = d.DateKey
GROUP BY l.Region, d.QuarterName, d.Year
ORDER BY d.Year, d.QuarterName, l.Region
```

**Customer Lifetime Value:**
```sql
SELECT
    c.CustomerName,
    COUNT(DISTINCT fs.SalesID) AS TotalPurchases,
    SUM(fs.SalesAmount) AS TotalSpent,
    AVG(fs.SalesAmount) AS AvgOrderValue,
    DATEDIFF(DAY, MIN(d.Date), MAX(d.Date)) AS DaysActive
FROM FactSales fs
INNER JOIN DimCustomer c ON fs.CustomerKey = c.CustomerKey
INNER JOIN DimDate d ON fs.DateKey = d.DateKey
GROUP BY c.CustomerName
ORDER BY TotalSpent DESC
```

## 🛠️ Tech Stack

- **SQL Server 2019+** - Primary database engine
- **T-SQL** - Data transformation and modeling
- **SSMS** - Development & management
- **Medallion Architecture** - Data organization pattern
- **Star Schema** - Analytical data modeling
- **Stored Procedures** - ETL orchestration
- **Triggers** - Automated validation

## 📁 Project Structure

```
sql-datawarehouse-project/
├── scripts/
│   ├── bronze/
│   │   ├── 01_create_bronze_tables.sql
│   │   └── 02_load_raw_data.sql
│   ├── silver/
│   │   ├── 01_create_silver_tables.sql
│   │   └── 02_cleanse_transform_data.sql
│   ├── gold/
│   │   ├── 01_create_dimensions.sql
│   │   ├── 02_create_facts.sql
│   │   └── 03_load_gold_data.sql
│   └── sample_queries.sql
├── datasets/
│   ├── erp_data.csv
│   ├── crm_data.csv
│   └── README.md
├── docs/
│   ├── data_architecture.md
│   ├── data_catalog.md
│   ├── naming_conventions.md
│   ├── requirements.md
│   └── ER_DIAGRAM.md
├── tests/
│   └── quality_checks.sql
└── README.md
```

## 📚 Key Features

✅ **Medallion Architecture** - Structured data layers (Bronze → Silver → Gold)
✅ **Data Quality Checks** - Validation & reconciliation processes at each layer
✅ **Star Schema** - Optimized for analytical queries
✅ **ETL Automation** - T-SQL stored procedures for automated workflows
✅ **Comprehensive Documentation** - Data catalog & design specifications
✅ **Sample Queries** - Ready-to-use analytics scripts
✅ **Best Practices** - Naming conventions & standards adherence
✅ **Performance Optimized** - Indexed tables & efficient joins

## 🎓 Learning Resources

- [SQL Server Data Warehouse Design](https://docs.microsoft.com/en-us/sql/relational-databases/data-warehouse-design)
- [Medallion Architecture Pattern](https://www.databricks.com/glossary/medallion-architecture)
- [Dimensional Modeling](https://www.kimballgroup.com/data-warehouse-business-intelligence-resources/)
- [T-SQL Performance Tuning](https://docs.microsoft.com/en-us/sql/relational-databases/performance-tuning)
- [Star Schema Best Practices](https://en.wikipedia.org/wiki/Star_schema)

## 🤝 Contributing

We welcome contributions! Areas for enhancement:

- Additional ETL scenarios
- Advanced performance optimizations
- Comprehensive test coverage
- Cloud migration examples (Azure SQL DW / Synapse)
- Power BI / Tableau integration examples
- Additional sample data

Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

### Development Guidelines
- Follow T-SQL naming conventions
- Document procedures with XML comments
- Include data quality checks
- Add sample queries for new features
- Update documentation

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🚀 Roadmap

- [x] Medallion architecture implementation
- [x] Basic ETL pipelines
- [x] Star schema design
- [ ] Incremental load logic (SCD Type 2)
- [ ] Real-time data integration
- [ ] Advanced ML analytics
- [ ] Cloud migration (Azure Synapse)
- [ ] Tableau/Power BI integration
- [ ] Performance monitoring dashboard
- [ ] Automated testing suite
- [ ] CI/CD pipeline

## 📊 Performance Benchmarks

| Operation | Typical Time | Dataset Size |
|-----------|-------------|--------------|
| Load Bronze (CSV) | 30-60s | 100K rows |
| Transform to Silver | 45-90s | 100K rows |
| Create Gold Layer | 60-120s | 100K rows |
| Sample Query (Top Products) | <1s | 100K rows |

## 🐛 Troubleshooting

### Common Issues

**Issue: "Cannot open file" when loading CSVs**
```sql
-- Ensure bulk insert has correct file path
-- Check SQL Server has read permissions
-- Use FORMATFILE if format differs
```

**Issue: Data quality validation failures**
- Check source data for nulls, duplicates
- Review transformation logic
- See quality_checks.sql for diagnostics

**Issue: Slow query performance**
- Run index statistics: `UPDATE STATISTICS table_name`
- Check query execution plan
- Consider columnstore indexes

## 📧 Contact & Support

For questions, issues, or suggestions:
- [Open an Issue](https://github.com/triptixjain/sql-datawarehouse-project/issues)
- [Start a Discussion](https://github.com/triptixjain/sql-datawarehouse-project/discussions)
- Email: triptiprep15@gmail.com

## 🙏 Acknowledgments

- Inspired by Kimball dimensional modeling
- Medallion architecture pattern
- SQL Server community best practices
- Open-source data warehouse projects

---

**Built with ❤️ by [Triptix Jain](https://github.com/triptixjain)**

**Last Updated:** August 2026
