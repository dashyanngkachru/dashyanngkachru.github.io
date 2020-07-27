# Investigating Thyroid Function

## Dataset variables:
     
subject_id : Unique identifier which specifies an individual patient   
hadm_id : Represents a single patient’s admission to the hospital. It is possible to have duplicate subject_id, indicating that a single patient had multiple admissions to the hospital     
gender : Sex of the patient    
dob : Date of birth of the given patient. Patients who are older than 89 years old at any time have had their date of birth shifted to obscure their age and comply with HIPAA   
itemid : Associated with lab measurements   
value : Contains the value measured for the concept identified by the itemid   
valuenum : Contains the same data as "value" variable in a numeric format. If this data is not numeric, valuenum is null   
valueuom : Unit of measurement for the value   
charttime : Records the time at which an observation was made, and is usually the closest proxy to the time the data was actually measured  
label : Describes the concept which is represented by the itemid   
fluid : Describes the substance on which the measurement was made   
icd9_list : Contains the actual code corresponding to the diagnosis assigned to the patient for the given row   
icd9_short_list : Provide a brief definition for the given diagnosis code   
icd9_long_list : Provide a slighly longer definition for the given diagnosis code   
drug_list : Drug prescribed to the patient   
drug_name : Representation of the drug prescribed to the patient   
generic_drug_name : Representation of the drug prescribed to the patient   
drug_type : Provides the type of drug prescribed   
formulary : Provide a representation of the drug in a coding system   
ndc : Provide a representation of the drug as in the National Drug Code   
gsn : Provide a representation of the drug as in the Generic Sequence Number
