import boto3
import pandas as pd
import cx_Oracle
import os

# --- Configuration ---
S3_BUCKET_NAME = 'your-bucket-name'
S3_FILE_KEY = 'path/to/yourfile.csv'  # like 'folder/yourfile.csv' or just 'yourfile.csv'
LOCAL_FILE_PATH = '/path/to/save/yourfile.csv'  # eg. '/home/ec2-user/files/yourfile.csv'

ORACLE_CONNECTION_STRING = 'your_full_connection_string'  # Example: 'user/password@host:port/service_name'

TABLE_NAME = 'your_table_name'  # the table you want to create

# --- Step 1: Download CSV from S3 ---
def download_csv_from_s3():
    s3 = boto3.client('s3')
    os.makedirs(os.path.dirname(LOCAL_FILE_PATH), exist_ok=True)
    s3.download_file(S3_BUCKET_NAME, S3_FILE_KEY, LOCAL_FILE_PATH)
    print(f"Downloaded file from S3 to {LOCAL_FILE_PATH}")

# --- Step 2: Read CSV ---
def read_csv():
    df = pd.read_csv(LOCAL_FILE_PATH)
    print(f"Read CSV file with {len(df)} rows and {len(df.columns)} columns")
    return df

# --- Step 3: Create Table in Oracle DB ---
def create_table_from_dataframe(cursor, df):
    columns_with_types = []
    for col in df.columns:
        # Assuming all columns as VARCHAR2(255) for simplicity
        # You can improve by checking data types of df[col]
        safe_col = col.replace(' ', '_')  # Replace spaces with underscores for SQL
        columns_with_types.append(f'"{safe_col}" VARCHAR2(255)')
    
    columns_sql = ', '.join(columns_with_types)
    create_table_sql = f'CREATE TABLE {TABLE_NAME} ({columns_sql})'
    try:
        cursor.execute(create_table_sql)
        print(f"Table {TABLE_NAME} created successfully.")
    except cx_Oracle.DatabaseError as e:
        error_obj, = e.args
        if 'ORA-00955' in error_obj.message:
            print(f"Table {TABLE_NAME} already exists. Skipping creation.")
        else:
            raise

# --- Step 4: Insert Data into Table ---
def insert_data(cursor, connection, df):
    cols = [col.replace(' ', '_') for col in df.columns]
    cols_str = ', '.join(f'"{col}"' for col in cols)
    placeholders = ', '.join(f':{i+1}' for i in range(len(cols)))

    insert_sql = f'INSERT INTO {TABLE_NAME} ({cols_str}) VALUES ({placeholders})'
    data = [tuple(str(x) if pd.notna(x) else None for x in row) for row in df.values]

    cursor.executemany(insert_sql, data)
    connection.commit()
    print(f"Inserted {len(data)} rows into {TABLE_NAME}")

# --- Main Function ---
def main():
    download_csv_from_s3()
    df = read_csv()

    # Connect to Oracle
    connection = cx_Oracle.connect(ORACLE_CONNECTION_STRING)
    cursor = connection.cursor()

    create_table_from_dataframe(cursor, df)
    insert_data(cursor, connection, df)

    cursor.close()
    connection.close()
    print("All done successfully!")

if __name__ == "__main__":
    main()