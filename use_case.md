This page contains the use case description for the final assignment.

# Concrete Healthcare Scenario
Medical Image Annotation and Analysis:
Accelerating the diagnosis of conditions (e.g., tumors on X-rays or CT scans) by providing a pipeline for securely uploading medical images, automatically running an AI analysis, and storing the results

# 🤔 Problem Statement
Radiologists and specialists face an overwhelming volume of medical images (X-rays, CT scans, MRIs) and increasing pressure to provide timely, accurate diagnoses. The manual process of reviewing, measuring, and annotating these images is time-consuming, prone to human error, and can lead to significant delays in treatment for critical conditions like tumors, fractures, or pulmonary embolisms. This delay negatively impacts patient outcomes and increases hospital operational costs. Often times in the inpatient setting, attendings and providers are often waiting on results of the medical images to be read by a radiologist before coming up with a plan of action. 

Users involved:
1. Radiologists / Specialists:
    Provides them with pre-annotated images (e.g., tumor margins, fracture lines) and a confidence score, allowing them to focus on complex cases and confirm automated findings faster. They would then be reviewing AI-generated annotations, accessing the analysis report, and doing a final sign-off.

2. Patients:
    Leads to faster, more accurate diagnoses, reducing anxiety and allowing for earlier initiation of necessary treatment.
    
3. Medical Students / Residents:
    Provides a tool for structured learning by comparing their manual interpretations against the AI's automated and consistent output.

# 📥 Data sources
The primary data source is the Digital Imaging and Communications in Medicine (DICOM) file format. DICOM is the international standard for medical images and related information, and it is the native format produced by virtually all imaging equipment. It will come from various medical devices such as Computed Tomography (CT) Scanners, Magnetic Resonance Imaging (MRI) Machines, X-ray/Radiography Equipment, and Ultrasound Machines. This pipeline will have two distinct data types that will be stored: Raw & Processed DICOM Files and Analysis Results & Metadata.

# 🛠️ Basic Workflow
1. Ingestion: A hospital or clinic uploads a DICOM image to a secure cloud-based endpoint.

2. Trigger & Pre-processing: The upload triggers a Serverless Function that extracts metadata, anonymizes the data, and places the raw file into Object Storage. The pipeline must include this critical step. It involves extracting patient health information and technical metadata from the DICOM files and de-identifies them before running the AI model, ensuring compliance with standards like HIPAA.

3. Analysis: The function triggers the Containerized AI Service to perform the image analysis.

4. Results & Storage: The AI service writes the structural analysis results (annotations) to the NoSQL Database and stores the annotated/overlaid image back into object storage.

5. Triage/Alert: The analysis results (e.g., a "High Risk" score) can trigger a notification via another serverless function to the radiologist.

6. Viewing: Clinicians access a secure web portal which pulls the metadata from the database and the image from storage for final review.
