import pandas as pd
import numpy as np
from faker import Faker
import random

fake = Faker()

# -----------------------------
# Configuration
# -----------------------------
num_records = 500_000  # Fact table records
num_dim_records = 500  # Dimension table size

# -----------------------------
# Generate Dimension Tables (6 attributes each)
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

def generate_dim_visit_type(n):
    return pd.DataFrame({
        'VisitTypeID': range(1, n+1),
        'VisitType': [random.choice(['Emergency','Routine','Follow-up','Check-up']) for _ in range(n)],
        'AvgWaitTime': [random.randint(5,180) for _ in range(n)],
        'Notes': [fake.sentence() for _ in range(n)],
        'TypicalDuration': [random.randint(15,120) for _ in range(n)],
        'CostEstimate': [random.randint(500,5000) for _ in range(n)]
    })

def generate_dim_date(n):
    return pd.DataFrame({
        'DateID': range(1, n+1),
        'FullDate': [fake.date_between(start_date='-5y', end_date='today') for _ in range(n)],
        'Day': [random.randint(1,31) for _ in range(n)],
        'Month': [random.randint(1,12) for _ in range(n)],
        'Year': [random.randint(2018,2025) for _ in range(n)],
        'Weekday': [random.choice(['Mon','Tue','Wed','Thu','Fri','Sat','Sun']) for _ in range(n)]
    })

def generate_dim_insurance(n):
    return pd.DataFrame({
        'InsuranceID': range(1, n+1),
        'ProviderName': [fake.company() for _ in range(n)],
        'PolicyType': [random.choice(['Basic','Premium','Gold','Silver']) for _ in range(n)],
        'CoveragePercent': [random.randint(50,100) for _ in range(n)],
        'ContactNumber': [fake.phone_number() for _ in range(n)],
        'Notes': [fake.sentence() for _ in range(n)]
    })

def generate_dim_payment_method(n):
    return pd.DataFrame({
        'PaymentMethodID': range(1, n+1),
        'Method': [random.choice(['Cash','Credit Card','Insurance','UPI']) for _ in range(n)],
        'Bank': [fake.company() for _ in range(n)],
        'AccountNumber': [fake.bothify(text='##########') for _ in range(n)],
        'Notes': [fake.sentence() for _ in range(n)],
        'TransactionFee': [random.randint(0,100) for _ in range(n)]
    })

# -----------------------------
# Generate Fact_Visits Table
# -----------------------------
def generate_fact_visits(fact_records, dim_records):
    # Create dimensions
    Dim_Patient = generate_dim_patient(dim_records)
    Dim_Doctor = generate_dim_doctor(dim_records)
    Dim_Hospital = generate_dim_hospital(dim_records)
    Dim_Department = generate_dim_department(dim_records)
    Dim_VisitType = generate_dim_visit_type(dim_records)
    Dim_Date = generate_dim_date(dim_records)
    Dim_Insurance = generate_dim_insurance(dim_records)
    Dim_PaymentMethod = generate_dim_payment_method(dim_records)

    # Generate random foreign keys
    patient_ids = np.random.randint(1, dim_records+1, fact_records)
    doctor_ids = np.random.randint(1, dim_records+1, fact_records)
    hospital_ids = np.random.randint(1, dim_records+1, fact_records)
    department_ids = np.random.randint(1, dim_records+1, fact_records)
    visittype_ids = np.random.randint(1, dim_records+1, fact_records)
    date_ids = np.random.randint(1, dim_records+1, fact_records)
    insurance_ids = np.random.randint(1, dim_records+1, fact_records)
    paymentmethod_ids = np.random.randint(1, dim_records+1, fact_records)

    # Create base fact table
    fact = pd.DataFrame({
        'VisitID': range(1, fact_records+1),
        'PatientID': patient_ids,
        'DoctorID': doctor_ids,
        'HospitalID': hospital_ids,
        'DepartmentID': department_ids,
        'VisitTypeID': visittype_ids,
        'DateID': date_ids,
        'InsuranceID': insurance_ids,
        'PaymentMethodID': paymentmethod_ids,
        'VisitDurationMinutes': np.random.randint(10,480,fact_records),
        'VisitCost': np.random.randint(500,50000,fact_records)
    })

    # Merge dimension attributes
    fact = fact.merge(Dim_Patient, on='PatientID', how='left', suffixes=('','_Patient'))
    fact = fact.merge(Dim_Doctor, on='DoctorID', how='left', suffixes=('','_Doctor'))
    fact = fact.merge(Dim_Hospital, on='HospitalID', how='left', suffixes=('','_Hospital'))
    fact = fact.merge(Dim_Department, on='DepartmentID', how='left', suffixes=('','_Department'))
    fact = fact.merge(Dim_VisitType, on='VisitTypeID', how='left', suffixes=('','_VisitType'))
    fact = fact.merge(Dim_Date, on='DateID', how='left', suffixes=('','_Date'))
    fact = fact.merge(Dim_Insurance, on='InsuranceID', how='left', suffixes=('','_Insurance'))
    fact = fact.merge(Dim_PaymentMethod, on='PaymentMethodID', how='left', suffixes=('','_PaymentMethod'))

    return fact

# -----------------------------
# Generate data and save
# -----------------------------
Fact_Visits = generate_fact_visits(num_records, num_dim_records)
Fact_Visits.to_csv('Fact_Visits_Denormalized.csv', index=False)

print("Single denormalized Fact_Visits CSV generated with 500,000 records and 8 dimensions!")
