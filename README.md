# NL2SQL-with-LangChain-LlamaIndex-on-MySQL
This project demonstrates how to build a natural language to SQL query engine using LangChain, LlamaIndex (GPT-Index), and OpenAI LLMs. It connects to a MySQL classic models sample database and allows querying the database using natural language.

---

## Related Videos
- [LangChain, SQL Agents & OpenAI LLMs: Query Database Using Natural Language](https://youtu.be/VG9KYCS0-8E?si=EAqu-9DS6QX9eitR)
- [Mastering LlamaIndex: Create, Save & Load Indexes, Customize LLMs, Prompts & Embeddings](https://youtu.be/XGBQ_f-Yy48?si=ZWs0meSlQpLPcCE0)

---

## Features
- Connects to a remote MySQL `classicmodels` database using SQLAlchemy and PyMySQL.
- Loads database schema and sample rows for context.
- Uses `llama-index` SQLDatabase utility to wrap the SQL database.
- Uses OpenAI GPT-3.5-turbo model as LLM backend for natural language query understanding and SQL generation.
- Converts natural language queries into SQL queries automatically.
- Returns and displays query results and generated SQL queries.

---

## Installation

```bash
pip install llama-index pymysql sqlalchemy openai tiktoken -q
Configuration
Set your OpenAI API key as an environment variable:

python
Copy
Edit
import os
os.environ["OPENAI_API_KEY"] = "your_openai_api_key_here"
Database Connection
python
Copy
Edit
from sqlalchemy import create_engine, text

db_user = "admin"
db_password = "llama_nl2sql"
db_host = "langchainsql.cl0j8hicdoox.us-east-1.rds.amazonaws.com"
db_name = "classicmodels"

connection_string = f"mysql+pymysql://{db_user}:{db_password}@{db_host}/{db_name}"
engine = create_engine(connection_string)

# Test connection
with engine.connect() as connection:
    result = connection.execute(text("SELECT * FROM customers LIMIT 3"))
    for row in result:
        print(row)
Usage
Initialize the SQLDatabase wrapper from llama_index:

python
Copy
Edit
from llama_index import SQLDatabase
sql_database = SQLDatabase(engine, sample_rows_in_table_info=2)
Create an OpenAI LLM service context with token counting callbacks:

python
Copy
Edit
import tiktoken
from llama_index.callbacks import CallbackManager, TokenCountingHandler
from llama_index import ServiceContext
from llama_index.llms import OpenAI

token_counter = TokenCountingHandler(tokenizer=tiktoken.encoding_for_model("gpt-3.5-turbo").encode)
callback_manager = CallbackManager([token_counter])

llm = OpenAI(temperature=0.1, model="gpt-3.5-turbo")
service_context = ServiceContext.from_defaults(llm=llm, callback_manager=callback_manager)
Build the NL2SQL Query Engine and query with natural language:

python
Copy
Edit
from llama_index.indices.struct_store.sql_query import NLSQLTableQueryEngine

query_engine = NLSQLTableQueryEngine(sql_database=sql_database, service_context=service_context)

query_str = "When was order number 10100 shipped?"
response = query_engine.query(query_str)
print(response.response)
print("Generated SQL query:", response.metadata['sql_query'])
Database Tables Overview
customers: Stores customer data.

employees: Employee information and organizational structure.

offices: Sales office data.

orders: Sales orders placed by customers.

orderdetails: Sales order line items.

payments: Payments made by customers.

productlines: Product line categories.

products: List of scale model cars.

Sample Output
plaintext
Copy
Edit
Order number 10100 was shipped on January 10, 2003.
SELECT shippedDate FROM orders WHERE orderNumber = 10100
