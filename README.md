import pandas as pd
import numpy as np
from faker import Faker
import random

fake = Faker()

# -----------------------------
# Configuration
# -----------------------------
num_records_fact = 500_000   # Fact table records
num_records_dim = 500        # Dimension table records

# -----------------------------
# Generate Dimension Tables
# -----------------------------
def create_dim_patient(n):
    return pd.DataFrame({
        'PatientID': range(1, n+1),
        'PatientName': [fake.name() for _ in range(n)],
        'Gender': [random.choice(['Male','Female']) for _ in range(n)],
        'DOB': [fake.date_of_birth(minimum_age=0, maximum_age=90) for _ in range(n)]
    })

def create_dim_doctor(n):
    return pd.DataFrame({
        'DoctorID': range(1, n+1),
        'DoctorName': [fake.name() for _ in range(n)],
        'Specialty': [random.choice(['Cardiology','Orthopedics','Neurology','General']) for _ in range(n)],
        'ExperienceYears': [random.randint(1,40) for _ in range(n)]
    })

def create_dim_hospital(n):
    return pd.DataFrame({
        'HospitalID': range(1, n+1),
        'HospitalName': [fake.company() for _ in range(n)],
        'City': [fake.city() for _ in range(n)],
        'AccreditationLevel': [random.choice(['A','B','C']) for _ in range(n)]
    })

def create_dim_department(n):
    return pd.DataFrame({
        'DepartmentID': range(1, n+1),
        'DepartmentName': [random.choice(['Surgery','Cardiology','Neurology','Orthopedics']) for _ in range(n)],
        'Floor': [random.randint(1,10) for _ in range(n)],
        'HeadDoctorID': [random.randint(1,n) for _ in range(n)]
    })

def create_dim_procedure(n):
    return pd.DataFrame({
        'ProcedureID': range(1, n+1),
        'ProcedureName': [fake.bs().title() for _ in range(n)],
        'Category': [random.choice(['Minor','Major','Elective','Emergency']) for _ in range(n)],
        'CPT_Code': [fake.bothify(text='???-###') for _ in range(n)]
    })

def create_dim_date(n):
    return pd.DataFrame({
        'DateID': range(1, n+1),
        'FullDate': [fake.date_between(start_date='-5y', end_date='today') for _ in range(n)],
        'Month': [random.randint(1,12) for _ in range(n)],
        'Year': [random.randint(2018,2025) for _ in range(n)]
    })

def create_dim_anesthesia(n):
    return pd.DataFrame({
        'AnesthesiaID': range(1, n+1),
        'Type': [random.choice(['General','Local','Regional','Sedation']) for _ in range(n)],
        'RiskLevel': [random.choice(['Low','Medium','High']) for _ in range(n)],
        'Description': [fake.sentence() for _ in range(n)]
    })

def create_dim_equipment(n):
    return pd.DataFrame({
        'EquipmentID': range(1, n+1),
        'EquipmentName': [fake.word().title() for _ in range(n)],
        'Category': [random.choice(['Surgical','Monitoring','Imaging','Support']) for _ in range(n)],
        'Cost': [random.randint(1000,50000) for _ in range(n)]
    })

def create_dim_nurse(n):
    return pd.DataFrame({
        'NurseID': range(1, n+1),
        'NurseName': [fake.name() for _ in range(n)],
        'ExperienceYears': [random.randint(1,30) for _ in range(n)],
        'Shift': [random.choice(['Morning','Evening','Night']) for _ in range(n)]
    })

def create_dim_room(n):
    return pd.DataFrame({
        'RoomID': range(1, n+1),
        'RoomNumber': [random.randint(100,500) for _ in range(n)],
        'RoomType': [random.choice(['ICU','General','Private']) for _ in range(n)],
        'Floor': [random.randint(1,10) for _ in range(n)]
    })

# -----------------------------
# Generate Fact Table
# -----------------------------
def create_fact_operations(fact_records, dim_size):
    return pd.DataFrame({
        'OperationID': range(1, fact_records+1),
        'PatientID': np.random.randint(1, dim_size+1, fact_records),
        'DoctorID': np.random.randint(1, dim_size+1, fact_records),
        'HospitalID': np.random.randint(1, dim_size+1, fact_records),
        'DepartmentID': np.random.randint(1, dim_size+1, fact_records),
        'ProcedureID': np.random.randint(1, dim_size+1, fact_records),
        'DateID': np.random.randint(1, dim_size+1, fact_records),
        'AnesthesiaID': np.random.randint(1, dim_size+1, fact_records),
        'EquipmentID': np.random.randint(1, dim_size+1, fact_records),
        'NurseID': np.random.randint(1, dim_size+1, fact_records),
        'RoomID': np.random.randint(1, dim_size+1, fact_records),
        'DurationMinutes': np.random.randint(30, 480, fact_records),
        'OperationCost': np.random.randint(5000, 100000, fact_records)
    })

# -----------------------------
# Create tables
# -----------------------------
tables = {
    'Dim_Patient': create_dim_patient(num_records_dim),
    'Dim_Doctor': create_dim_doctor(num_records_dim),
    'Dim_Hospital': create_dim_hospital(num_records_dim),
    'Dim_Department': create_dim_department(num_records_dim),
    'Dim_Procedure': create_dim_procedure(num_records_dim),
    'Dim_Date': create_dim_date(num_records_dim),
    'Dim_Anesthesia': create_dim_anesthesia(num_records_dim),
    'Dim_Equipment': create_dim_equipment(num_records_dim),
    'Dim_Nurse': create_dim_nurse(num_records_dim),
    'Dim_Room': create_dim_room(num_records_dim),
    'Fact_Operations': create_fact_operations(num_records_fact, num_records_dim)
}

# -----------------------------
# Save as CSVs
# -----------------------------
for name, df in tables.items():
    df.to_csv(f'{name}.csv', index=False)

print("All CSV files generated successfully!")
