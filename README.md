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
# Generate Dimension Tables (6 columns each)
# -----------------------------

# 1. Dim_Patient
Dim_Patient = pd.DataFrame({
    'PatientID': range(1, num_records_dim+1),
    'PatientName': [fake.name() for _ in range(num_records_dim)],
    'Gender': [random.choice(['Male','Female']) for _ in range(num_records_dim)],
    'DOB': [fake.date_of_birth(minimum_age=0, maximum_age=90) for _ in range(num_records_dim)],
    'City': [fake.city() for _ in range(num_records_dim)],
    'State': [fake.state() for _ in range(num_records_dim)]
})

# 2. Dim_Doctor
Dim_Doctor = pd.DataFrame({
    'DoctorID': range(1, num_records_dim+1),
    'DoctorName': [fake.name() for _ in range(num_records_dim)],
    'Specialty': [random.choice(['Cardiology','Orthopedics','Neurology','General']) for _ in range(num_records_dim)],
    'ExperienceYears': [random.randint(1,40) for _ in range(num_records_dim)],
    'Department': [random.choice(['Surgery','Cardiology','Neurology','Orthopedics']) for _ in range(num_records_dim)],
    'ContactNumber': [fake.phone_number() for _ in range(num_records_dim)]
})

# 3. Dim_Hospital
Dim_Hospital = pd.DataFrame({
    'HospitalID': range(1, num_records_dim+1),
    'HospitalName': [fake.company() for _ in range(num_records_dim)],
    'City': [fake.city() for _ in range(num_records_dim)],
    'State': [fake.state() for _ in range(num_records_dim)],
    'Capacity': [random.randint(50,500) for _ in range(num_records_dim)],
    'AccreditationLevel': [random.choice(['A','B','C']) for _ in range(num_records_dim)]
})

# 4. Dim_Department
Dim_Department = pd.DataFrame({
    'DepartmentID': range(1, num_records_dim+1),
    'DepartmentName': [random.choice(['Surgery','Cardiology','Neurology','Orthopedics']) for _ in range(num_records_dim)],
    'Floor': [random.randint(1,10) for _ in range(num_records_dim)],
    'HeadDoctorID': [random.randint(1,num_records_dim) for _ in range(num_records_dim)],
    'RoomCount': [random.randint(5,50) for _ in range(num_records_dim)],
    'Budget': [random.randint(10000,500000) for _ in range(num_records_dim)]
})

# 5. Dim_Procedure
Dim_Procedure = pd.DataFrame({
    'ProcedureID': range(1, num_records_dim+1),
    'ProcedureName': [fake.bs().title() for _ in range(num_records_dim)],
    'Category': [random.choice(['Minor','Major','Elective','Emergency']) for _ in range(num_records_dim)],
    'CPT_Code': [fake.bothify(text='???-###') for _ in range(num_records_dim)],
    'EstimatedDuration': [random.randint(30,240) for _ in range(num_records_dim)],
    'CostEstimate': [random.randint(5000,50000) for _ in range(num_records_dim)]
})

# 6. Dim_Date
Dim_Date = pd.DataFrame({
    'DateID': range(1, num_records_dim+1),
    'FullDate': [fake.date_between(start_date='-5y', end_date='today') for _ in range(num_records_dim)],
    'Day': [random.randint(1,31) for _ in range(num_records_dim)],
    'Month': [random.randint(1,12) for _ in range(num_records_dim)],
    'Year': [random.randint(2018,2025) for _ in range(num_records_dim)],
    'Weekday': [random.choice(['Mon','Tue','Wed','Thu','Fri','Sat','Sun']) for _ in range(num_records_dim)]
})

# 7. Dim_Anesthesia
Dim_Anesthesia = pd.DataFrame({
    'AnesthesiaID': range(1, num_records_dim+1),
    'Type': [random.choice(['General','Local','Regional','Sedation']) for _ in range(num_records_dim)],
    'RiskLevel': [random.choice(['Low','Medium','High']) for _ in range(num_records_dim)],
    'Description': [fake.sentence() for _ in range(num_records_dim)],
    'DurationEstimate': [random.randint(30,240) for _ in range(num_records_dim)],
    'Cost': [random.randint(1000,10000) for _ in range(num_records_dim)]
})

# 8. Dim_Equipment
Dim_Equipment = pd.DataFrame({
    'EquipmentID': range(1, num_records_dim+1),
    'EquipmentName': [fake.word().title() for _ in range(num_records_dim)],
    'Category': [random.choice(['Surgical','Monitoring','Imaging','Support']) for _ in range(num_records_dim)],
    'PurchaseDate': [fake.date_between(start_date='-10y', end_date='today') for _ in range(num_records_dim)],
    'Cost': [random.randint(1000,50000) for _ in range(num_records_dim)],
    'MaintenanceSchedule': [random.choice(['Monthly','Quarterly','Yearly']) for _ in range(num_records_dim)]
})

# 9. Dim_Nurse
Dim_Nurse = pd.DataFrame({
    'NurseID': range(1, num_records_dim+1),
    'NurseName': [fake.name() for _ in range(num_records_dim)],
    'ExperienceYears': [random.randint(1,30) for _ in range(num_records_dim)],
    'Shift': [random.choice(['Morning','Evening','Night']) for _ in range(num_records_dim)],
    'Certification': [random.choice(['Basic','Advanced','Critical Care']) for _ in range(num_records_dim)],
    'DepartmentID': [random.randint(1,num_records_dim) for _ in range(num_records_dim)]
})

# 10. Dim_Room
Dim_Room = pd.DataFrame({
    'RoomID': range(1, num_records_dim+1),
    'RoomNumber': [random.randint(100,500) for _ in range(num_records_dim)],
    'RoomType': [random.choice(['ICU','General','Private']) for _ in range(num_records_dim)],
    'Floor': [random.randint(1,10) for _ in range(num_records_dim)],
    'Capacity': [random.randint(1,5) for _ in range(num_records_dim)],
    'AvailabilityStatus': [random.choice(['Available','Occupied','Maintenance']) for _ in range(num_records_dim)]
})

# -----------------------------
# Generate Fact Table
# -----------------------------
Fact_Operations = pd.DataFrame({
    'OperationID': range(1, num_records_fact+1),
    'PatientID': np.random.randint(1, num_records_dim+1, num_records_fact),
    'DoctorID': np.random.randint(1, num_records_dim+1, num_records_fact),
    'HospitalID': np.random.randint(1, num_records_dim+1, num_records_fact),
    'DepartmentID': np.random.randint(1, num_records_dim+1, num_records_fact),
    'ProcedureID': np.random.randint(1, num_records_dim+1, num_records_fact),
    'DateID': np.random.randint(1, num_records_dim+1, num_records_fact),
    'AnesthesiaID': np.random.randint(1, num_records_dim+1, num_records_fact),
    'EquipmentID': np.random.randint(1, num_records_dim+1, num_records_fact),
    'NurseID': np.random.randint(1, num_records_dim+1, num_records_fact),
    'RoomID': np.random.randint(1, num_records_dim+1, num_records_fact),
    'DurationMinutes': np.random.randint(30, 480, num_records_fact),
    'OperationCost': np.random.randint(5000,100000, num_records_fact)
})

# -----------------------------
# Save as CSV
# -----------------------------
dimension_tables = [Dim_Patient, Dim_Doctor, Dim_Hospital, Dim_Department, Dim_Procedure,
                    Dim_Date, Dim_Anesthesia, Dim_Equipment, Dim_Nurse, Dim_Room]

dimension_names = ['Dim_Patient','Dim_Doctor','Dim_Hospital','Dim_Department','Dim_Procedure',
                   'Dim_Date','Dim_Anesthesia','Dim_Equipment','Dim_Nurse','Dim_Room']

for name, df in zip(dimension_names, dimension_tables):
    df.to_csv(f'{name}.csv', index=False)

Fact_Operations.to_csv('Fact_Operations.csv', index=False)

print("Fact_Operations and 10 dimension tables generated successfully with 500,000 records!")
