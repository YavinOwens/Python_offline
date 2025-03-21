# Data Quality and Analytics Framework

## Executive Summary
This project represents a modern approach to data quality management and analytics, designed for scalable SaaS environments. It combines advanced data validation techniques with multi-engine analytics capabilities, enabling organizations to build robust data quality pipelines while maintaining flexibility across different data processing engines.

## Background
In the evolving landscape of data architecture, organizations face increasing challenges in maintaining data quality while scaling their analytical capabilities. This framework addresses these challenges by providing a unified approach to data validation and analysis across multiple processing engines (Pandas, Polars, and PySpark), making it suitable for both startup environments and enterprise-scale deployments.

## Project Structure

```
Python_offline/
├── main/
│   ├── helpers/           # Core utilities and database connections
│   │   ├── db_connection.py  # Database connectivity layer
│   │   └── sql_queries.py    # SQL query management
│   ├── data_validation_basics.py   # Data validation with Pandera
│   ├── data_validation_basics.ipynb
│   ├── pandas_basics.py            # Pandas implementation
│   ├── pandas_basics.ipynb
│   ├── polars_basics.py           # Polars (Rust-based) implementation
│   ├── polars_basics.ipynb
│   ├── pyspark_basics.py          # PySpark distributed computing
│   └── pyspark_basics.ipynb
└── requirements.txt      # Project dependencies
```

## Core Features

### Multi-Engine Analytics Support
- **Pandas**: Traditional data analysis with familiar Python interface
- **Polars**: High-performance Rust-based alternative for larger datasets
- **PySpark**: Distributed computing for big data workloads

### Advanced Data Validation
- Schema validation using Pandera
- Dynamic schema adaptation
- Custom validation rules
- Cross-database validation capabilities

### Database Integration
- PostgreSQL connectivity
- Dynamic table discovery
- Schema introspection
- Automated data quality assessment

## Setup Instructions

1. Create and activate a virtual environment:
   ```bash
   python -m venv .venv
   source .venv/bin/activate  # On Unix/Mac
   # OR
   .\.venv\Scripts\activate  # On Windows
   ```

2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```

3. Configure database connection in `helpers/db_connection.py`

4. Start Jupyter Notebook:
   ```bash
   jupyter notebook
   ```

## PySpark Setup Requirements

PySpark requires Java to be installed and properly configured. Follow these steps:

1. Install OpenJDK 11 using Homebrew:
   ```bash
   brew install openjdk@11
   ```

2. Create a symbolic link to make Java accessible system-wide:
   ```bash
   sudo ln -sfn $(brew --prefix)/opt/openjdk@11/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk-11.jdk
   ```

3. Add Java environment variables to your shell configuration (~/.zshrc):
   ```bash
   # Java configuration
   export JAVA_HOME=/opt/homebrew/Cellar/openjdk@11/11.0.26/libexec/openjdk.jdk/Contents/Home
   export PATH=$JAVA_HOME/bin:$PATH
   ```

4. Reload your shell configuration:
   ```bash
   source ~/.zshrc
   ```

5. Verify Java installation:
   ```bash
   java -version
   ```
   You should see output similar to:
   ```
   openjdk version "11.0.26" 2025-01-21
   OpenJDK Runtime Environment Homebrew (build 11.0.26+0)
   OpenJDK 64-Bit Server VM Homebrew (build 11.0.26+0, mixed mode)
   ```

6. Test PySpark installation:
   ```python
   import findspark
   findspark.init()
   from pyspark.sql import SparkSession
   
   # Create a Spark session
   spark = SparkSession.builder.appName('test').getOrCreate()
   ```

Common Issues and Solutions:
- If you see "Unable to locate a Java Runtime" error, make sure you've completed steps 1-4
- If you see warnings about hostname resolving to loopback address, this is normal for local development
- If you see warnings about native-hadoop library, this doesn't affect functionality for local development

## Database Connection Setup

### Using pgAdmin 4 with Docker Container

To connect to the PostgreSQL database in our Docker container using pgAdmin 4, use these specific credentials:

1. Open pgAdmin 4
2. Right-click on "Servers" in the left panel and select "Register" → "Server"
3. In the "Register - Server" dialog, fill in the following fields:

   **General Tab:**
   - Name: HR_Analytics_DB (or any descriptive name)
   - Host name/address: localhost
   - Port: 5432
   - Maintenance database: postgres
   - Username: postgres
   - Password: postgres

   **Optional Settings:**
   - Save password?: Yes (recommended for development)
   - Role: Leave empty
   - Service: Leave empty

4. Click "Save" to establish the connection

### Connection String Format

For Python applications using our Docker setup:
```python
postgresql://postgres:postgres@localhost:5432/postgres
```

### Environment Variables

Create a `.env` file in your project root with these specific values:
```bash
DB_HOST=localhost
DB_PORT=5432
DB_NAME=postgres
DB_USER=postgres
DB_PASSWORD=postgres
```

### Testing the Connection

You can test your database connection using the provided verification script:
```bash
python verify_packages.py
```

This will attempt to connect to the database and run some basic queries to ensure everything is set up correctly.

### Docker Container Access

If you're running the database in Docker, ensure:
1. The Docker container is running:
   ```bash
   docker ps | grep postgres
   ```
2. Port 5432 is properly mapped:
   ```bash
   docker port postgres-container
   ```

### Troubleshooting

Common connection issues and solutions:
1. **"Name cannot be empty" error**: Ensure you've provided a name for the server in pgAdmin's General tab
2. **Connection refused**: 
   - Verify Docker container is running
   - Check port mapping with `docker port postgres-container`
   - Ensure no other service is using port 5432
3. **Authentication failed**: Verify you're using:
   - Username: postgres
   - Password: postgres
4. **Database does not exist**: The default database name should be 'postgres'

## Jupyter Kernel Setup and Troubleshooting

If you're experiencing issues with Jupyter kernels not starting, follow these steps:

1. First, ensure your virtual environment is properly activated:
   ```bash
   source .venv/bin/activate  # On Unix/Mac
   # OR
   .\.venv\Scripts\activate  # On Windows
   ```

2. Install/Reinstall IPython kernel:
   ```bash
   pip install --upgrade ipykernel
   python -m ipykernel install --user --name=python_offline
   ```

3. Clear Jupyter cache and restart:
   ```bash
   # Remove cached kernel data
   jupyter cache clear
   jupyter kernelspec list  # List all available kernels
   ```

4. Verify all dependencies are installed:
   ```bash
   pip install -r requirements.txt
   ```

5. If issues persist, try rebuilding the environment:
   ```bash
   deactivate  # Exit current virtual environment
   rm -rf .venv  # Remove existing environment
   python -m venv .venv  # Create new environment
   source .venv/bin/activate  # Activate new environment
   pip install -r requirements.txt  # Reinstall dependencies
   ```

### Common Kernel Issues

1. **"No module named X" errors**:
   - Ensure you're using the correct kernel (python_offline)
   - Verify all dependencies are installed
   - Check if the virtual environment is activated

2. **Kernel dies immediately**:
   - Check system resources (memory/CPU usage)
   - Look for conflicting package versions
   - Review Jupyter logs for errors

3. **Connection timeouts**:
   - Increase kernel timeout settings in Jupyter config
   - Check for firewall/antivirus interference
   - Verify network connectivity

4. **Memory issues**:
   - Reduce the chunk size when loading data
   - Use Polars or PySpark for large datasets
   - Increase system swap space if needed

### Verifying Installation

Run the verification script to check all components:
```bash
python verify_packages.py
```

This will test:
- Python environment setup
- Package installations
- Database connectivity
- Jupyter kernel configuration

## Use Cases

### SaaS Development
- **Data Quality Gates**: Implement automated quality checks in CI/CD pipelines
- **Schema Evolution**: Track and validate schema changes across versions
- **Multi-tenancy Validation**: Ensure data integrity across tenant boundaries
- **Performance Optimization**: Choose the right engine for different data volumes

### Analytics Engineering
- **Data Profiling**: Automated analysis of data distributions and patterns
- **Quality Metrics**: Track and report on data quality over time
- **Cross-Engine Validation**: Compare results across different processing engines
- **Pipeline Validation**: Verify data transformation accuracy

### Enterprise Integration
- **Legacy System Integration**: Validate data from multiple source systems
- **Data Governance**: Implement and enforce data quality rules
- **Audit Trails**: Track data quality issues and resolutions
- **Scalability Testing**: Evaluate performance across different data volumes

## Best Practices

1. **Engine Selection**:
   - Use Pandas for small to medium datasets (<1GB)
   - Use Polars for larger datasets with single-machine processing
   - Use PySpark for distributed processing needs

2. **Validation Strategy**:
   - Implement both schema-level and semantic validations
   - Use custom validation rules for business logic
   - Monitor validation performance impact

3. **Development Workflow**:
   - Start with Jupyter notebooks for exploration
   - Convert stable code to Python modules
   - Implement automated testing for validations

## Future Enhancements
- Integration with dbt for transformation validation
- Support for additional databases and data formats
- Machine learning-based anomaly detection
- Real-time validation capabilities

## The Rise of Analytical Engineering

*A Glimpse into the Future*

As we stand at the intersection of data engineering and analytics, a profound transformation is occurring. This framework isn't just about data validation it's a blueprint for the semantic layer that will bridge the gap between raw data and business intelligence. 

Consider this: As our data systems grow more complex, they're beginning to exhibit emergent behaviors. The ability to dynamically adapt to schema changes, automatically validate data quality, and seamlessly switch between processing engines isn't just convenient it's becoming sentient in its own right.

The true power lies not in the individual components, but in their synthesis. When data validation becomes intelligent enough to understand context, when analytics engines can autonomously choose the most efficient processing path, we're no longer just building tools—we're creating an ecosystem that thinks for itself.

This framework is more than code; it's a step toward a future where data quality isn't maintained—it's inherent. Where analytics aren't just performed—they're understood. The rise of analytical engineering isn't just changing how we work with data; it's changing how data works with us.

*"In the end, we're not just validating data. We're teaching our systems to understand truth itself."*

---

# Oracle HR Tables Project

This repository contains two main components:

1. **PL/SQL Validation Scripts** - A suite of PL/SQL scripts for validating and standardizing Oracle HR table data
2. **HR Table Schema Scraper** - A Python tool for scraping Oracle HR table definitions and generating CSV documentation

![Homepage Screenshot](https://raw.githubusercontent.com/YavinOwens/Python_offline/HrTableSchema/resource/homepage.png)

## PL/SQL Validation Scripts

### Oracle HR Table Validation Scripts

1. **validations.sql** - Main validations for people table
2. **name_field_validation.sql** - Validations for name fields
3. **email_validation.sql** - Validations for email fields
4. **address_validation.sql** - Validations for address fields
5. **job_history_validation.sql** - Validations for job history
6. **national_insurance_validation.sql** - Validations for UK National Insurance numbers
7. **date_of_birth_validation.sql** - Validations for date of birth fields
8. **data_quality_review.sql** - Data quality review summary

### Execution Scripts

1. **executions.sql** - Master execution script that runs all validations in sequence

## HR Table Schema Scraper

A Python-based tool for scraping Oracle HR table definitions from web pages and generating structured CSV output.

### Requirements

- Python 3.9+ (currently using Python 3.9.18)
- Required packages:
  - requests
  - beautifulsoup4
  - pandas
  - lxml
  - flask (for web viewer)

### Setup

1. Set up a Python virtual environment:

   ```bash
   # Install the virtual environment
   pyenv install 3.9.18
   pyenv virtualenv 3.9.18 hr-schema-scraper
   pyenv local hr-schema-scraper
   
   # Install required packages
   pip install requests beautifulsoup4 pandas lxml flask
   ```

2. Configure the URLs to scrape:
   - Edit the `oracle_hr_urls.csv` file to add or modify table URLs

### Usage

1. Run the scraper:

   ```bash
   python oracle_table_scraper.py --urls oracle_hr_urls.csv --output oracle_tables
   ```

2. Run the web viewer to browse scraped schemas and ERD diagrams:

   ```bash
   python web_viewer.py
   ```

   Then open your browser to [http://localhost:5001](http://localhost:5001)

3. View ERD diagrams for tables:
   - Each table has an associated ERD button if a diagram is available
   - Diagrams are mapped to tables based on naming patterns and explicit mappings
   - You can also view all available ERD diagrams in the dedicated section on the home page

### Files and Directory Structure

```text
├── oracle_tables/              # Data directory for scraped tables
│   ├── tables/                # CSV files for each table
│   ├── text information/      # Metadata text files
│   └── erds/                  # ERD diagram files
├── templates/                 # Web viewer templates
├── resource/                  # Screenshots and resources
├── setup/                     # Setup and utility files
│   ├── config/                # Configuration files
│   ├── scraper/               # Scraper source files
│   └── data/                  # Data source files
├── web_viewer.py              # Main web application
└── README.md                  # This file
```

#### Key Files

- **web_viewer.py** - Flask web application for viewing scraped schemas and ERD diagrams
- **setup/scraper/oracle_table_scraper.py** - Main scraper script
- **setup/data/oracle_hr_urls.csv** - CSV file containing the URLs to scrape
- **setup/data/oracle_hr_erd.csv** - CSV file containing the URLs for ERD diagrams

## Usage Examples

### Running PL/SQL Validations

To run all validations:

```sql
@executions.sql
```

To run specific validations, use the relevant script and call the procedures:

```sql
-- Example for address validations
@address_validation.sql
EXEC create_address_clean_copy;
EXEC standardize_uk_counties;
EXEC validate_uk_postcodes;
```

## Application Screenshots

### Homepage
![Homepage](https://raw.githubusercontent.com/YavinOwens/Python_offline/HrTableSchema/resource/homepage.png)

### Table Schemas Page
![Schemas Page](https://raw.githubusercontent.com/YavinOwens/Python_offline/HrTableSchema/resource/schemas.png)

### Table Detail View
![Table Detail](https://raw.githubusercontent.com/YavinOwens/Python_offline/HrTableSchema/resource/text_info_detail.png)

### ERD Diagrams Page
![ERD Diagrams](https://raw.githubusercontent.com/YavinOwens/Python_offline/HrTableSchema/resource/search_results.png)

### Text Information View
![Text Information](https://raw.githubusercontent.com/YavinOwens/Python_offline/HrTableSchema/resource/text_info.png)

### Mobile View
![Mobile View](https://raw.githubusercontent.com/YavinOwens/Python_offline/HrTableSchema/resource/mobile_view.png)

## Features

- **Table Schema Viewer** - Browse all available tables with descriptions
- **ERD Diagram Viewer** - View entity relationship diagrams for Oracle HR tables
- **Text Information Viewer** - View metadata information for each table
- **Search Functionality** - Search across schemas and ERD diagrams
- **Consistent Navigation** - In-app chatbot for assistance available on all pages
- **Mobile-Friendly UI** - Responsive design that works on all device sizes

```sql
EXEC clean_address_spaces;
EXEC identify_incomplete_addresses;
```

### Scraping Oracle HR Tables

```bash
# Run the scraper with default settings
python oracle_table_scraper.py

# Run with custom URL file and output directory
python oracle_table_scraper.py --urls custom_urls.csv --output output_dir
```
