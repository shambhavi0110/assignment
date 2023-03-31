# assignment
import pandas as pd
import psycopg2

# Define the database connection details
conn = psycopg2.connect(
    host="devtradingsagedb-do-user-12481132-0.b.db.ondigitalocean.com",
    port=25060,
    database="defaultdb",
    user="doadmin",
    password="AVNS_AZ-3Q1oUpp9WnsReBBX",
    sslmode="require"
)

# Define the input parameters
date1 = '2023-01-02'
date2 = '2023-01-05'

# Define the SQL query to fetch the data
query = f'''
SELECT strike, instrument_type, SUM(CASE WHEN date = '{date1}' THEN OI ELSE 0 END) AS OI1,
                                       SUM(CASE WHEN date = '{date2}' THEN OI ELSE 0 END) AS OI2
FROM test_assignment
WHERE date IN ('{date1}', '{date2}')
GROUP BY strike, instrument_type;
'''

# Fetch the data from PostgreSQL into a Pandas dataframe
df = pd.read_sql_query(query, conn)

# Calculate the difference in OI for each strike and instrument type
df['OI Difference'] = df['OI2'] - df['OI1']

# Reshape the dataframe to have CE and PE OI Differences as columns
df = df.pivot(index='strike', columns='instrument_type', values='OI Difference')

# Print the resulting dataframe
print(df)
