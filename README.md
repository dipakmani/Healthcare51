import pandas as pd
import numpy as np
from faker import Faker
import random

fake = Faker()

# -----------------------------
# Configuration
# -----------------------------
num_records = 500_000  # Total records for fact table
num_dim_records = 500  # Dimension table size

# -----------------------------
# Generate Dimension Tables (6 columns each)
# -----------------------------
def generate_dim_patient(n):
    return pd.DataFrame({
        'PatientID': range(1, n+1),
        'PatientName': [fake.name() for _ in range(n)],
        'Gender': [random.choice(['Male','Female']) for _ in range(n)],
        'DOB': [fake.date_of_birth(minimum_age=0, maximum_age=90) for _ in range(n)],
        'City': [fake.city() for _ in range(n)],
        'State': [fake.state() for _ in range(n)]
    })

def generate_dim_doctor(n):
    return pd.DataFrame({
        'DoctorID': range(1, n+1),
        'DoctorName': [fake.name() for _ in range(n)],
        'Specialty': [random.choice(['Cardiology','Orthopedics','Neurology','General']) for _ in range(n)],
        'ExperienceYears': [random.randint(1,40) for _ in range(n)],
        'Department': [random.choice(['Surgery','Cardiology','Neurology','Orthopedics']) for _ in range(n)],
        'ContactNumber': [fake.phone_number() for _ in range(n)]
    })

def generate_dim_hospital(n):
    return pd.DataFrame({
        'HospitalID': range(1, n+1),
        'HospitalName': [fake.company() for _ in range(n)],
        'City': [fake.city() for _ in range(n)],
        'State': [fake.state() for _ in range(n)],
        'Capacity': [random.randint(50,500) for _ in range(n)],
        'AccreditationLevel': [random.choice(['A','B','C']) for _ in range(n)]
    })

def generate_dim_department(n):
    return pd.DataFrame({
        'DepartmentID': range(1, n+1),
        'DepartmentName': [random.choice(['Surgery','Cardiology','Neurology','Orthopedics']) for _ in range(n)],
        'Floor': [random.randint(1,10) for _ in range(n)],
        'HeadDoctorID': [random.randint(1,n) for _ in range(n)],
        'RoomCount': [random.randint(5,50) for _ in range(n)],
        'Budget': [random.randint(10000,500000) for _ in range(n)]
    })

def generate_dim_procedure(n):
    return pd.DataFrame({
        'ProcedureID': range(1, n+1),
        'ProcedureName': [fake.bs().title() for _ in range(n)],
        'Category': [random.choice(['Minor','Major','Elective','Emergency']) for _ in range(n)],
        'CPT_Code': [fake.bothify(text='???-###') for _ in range(n)],
        'EstimatedDuration': [random.randint(30,240) for _ in range(n)],
        'CostEstimate': [random.randint(5000,50000) for _ in range(n)]
    })

# -----------------------------
# Generate Fact Table with Dimension Columns
# -----------------------------
def generate_fact_operations(fact_records, dim_records):
    # Create dimension tables
    Dim_Patient = generate_dim_patient(dim_records)
    Dim_Doctor = generate_dim_doctor(dim_records)
    Dim_Hospital = generate_dim_hospital(dim_records)
    Dim_Department = generate_dim_department(dim_records)
    Dim_Procedure = generate_dim_procedure(dim_records)

    # Generate random foreign keys
    patient_ids = np.random.randint(1, dim_records+1, fact_records)
    doctor_ids = np.random.randint(1, dim_records+1, fact_records)
    hospital_ids = np.random.randint(1, dim_records+1, fact_records)
    department_ids = np.random.randint(1, dim_records+1, fact_records)
    procedure_ids = np.random.randint(1, dim_records+1, fact_records)

    # Map dimension columns to fact table
    fact = pd.DataFrame({
        'OperationID': range(1, fact_records+1),
        'PatientID': patient_ids,
        'DoctorID': doctor_ids,
        'HospitalID': hospital_ids,
        'DepartmentID': department_ids,
        'ProcedureID': procedure_ids,
        'DurationMinutes': np.random.randint(30,480,fact_records),
        'OperationCost': np.random.randint(5000,100000,fact_records)
    })

    # Merge dimension attributes
    fact = fact.merge(Dim_Patient, on='PatientID', how='left', suffixes=('','_Patient'))
    fact = fact.merge(Dim_Doctor, on='DoctorID', how='left', suffixes=('','_Doctor'))
    fact = fact.merge(Dim_Hospital, on='HospitalID', how='left', suffixes=('','_Hospital'))
    fact = fact.merge(Dim_Department, on='DepartmentID', how='left', suffixes=('','_Department'))
    fact = fact.merge(Dim_Procedure, on='ProcedureID', how='left', suffixes=('','_Procedure'))

    return fact

# -----------------------------
# Generate data
# -----------------------------
Fact_Operations = generate_fact_operations(num_records, num_dim_records)

# -----------------------------
# Save as single CSV
# -----------------------------
Fact_Operations.to_csv('Fact_Operations_Denormalized.csv', index=False)

print("Single denormalized Fact_Operations CSV generated with 500,000 records!")
