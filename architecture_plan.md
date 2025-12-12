# Service Mapping
![service mapping](./service%20mapping.png)

# Data Flow Narrative
Step 1. Ingestion: A hospital or clinic uploads a DICOM image to a secure cloud-based endpoint.

Step 2. Trigger & Pre-processing: The upload triggers a serverless function that extracts metadata, anonymizes the data, and places the raw file into object storage.

Step 3. Analysis: The function triggers the containerized AI Service to perform the computationally intensive image analysis.

Step 4. Results & Storage: The AI service writes the structural analysis results (annotations) to the NoSQL database and stores the annotated/overlaid image back into object storage.

Step 5. Triage/Alert: The analysis results (e.g., a "High Risk" score) can trigger a notification via another serverless function to the radiologist.

Step 6. Viewing: Clinicians access a secure web portal which pulls the metadata from the database and the image from storage for final review.

# Security, identity, and governance basics 
For credentials management, all sensitive keys and database passwords must be stored securely using a Managed Secret Store service (like AWS Secrets Manager, Azure Key Vault, or Google Cloud Secret Manager), not directly in code or environment variables. Compute services (Serverless Functions, Containers) should access these secrets at runtime with their assigned Service Account, utilizing short-lived tokens, enforcing the principle of least privilege. Regular rotation of these stored credentials should be automated to minimize the impact of a compromise.

To avoid putting real PHI into public environments, the pipeline employs de-identification as a critical, mandatory step early in the workflow. The serverless function extracts all identifiers from the DICOM metadata (patient name, ID, date of birth) and replaces them with a pseudonymized token or linkable internal ID before the data is passed to the AI analysis or lower-level environments (like development/testing). The original, identifying data is either stored in a highly restricted, separate database or securely encrypted, ensuring that the primary, analyzed data set used by the AI model and non-clinical personnel is compliant with HIPAA.    

# Cost and operational considerations
The component likely to incur the highest cost is Compute, specifically the AI Analysis service. While developing the custom AI model has a high upfront cost, the ongoing cost of running the analysis is driven by the need for expensive virtual machines to process large medical images quickly. Storage, while containing a large volume, is relatively cheap per gigabyte.

To optimize costs, the architecture strategically uses Serverless/Scheduled Jobs instead of an always-on VM. We use serverless functions for the pre-processing  and triage because these are event-driven tasks that run quickly and only when a new image is uploaded or a result is generated—you only pay for the milliseconds they are active, avoiding idle charges. The compute-intensive containerized AI Service is also designed to be serverless, meaning the powerful, costly compute resources are spun up only when an image is ready for analysis and shut down immediately afterward, maximizing efficiency and minimizing the expensive bill.

The overall strategy to keep this within student budget or within free-tier is to rely almost exclusively on serverless components (Functions, Containers, Databases) configured to scale down to zero when idle. This shifts your architecture from paying for time (VMs) to paying only for actual usage (events/requests). You must also set up billing alerts immediately to notify you if you approach the free-tier limits, especially for the high-risk, compute-intensive AI analysis. 