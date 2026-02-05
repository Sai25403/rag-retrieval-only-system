# rag-retrieval-only-system
<img width="921" height="411" alt="architecture_diagram drawio" src="https://github.com/user-attachments/assets/77365e6a-b18e-4034-b73a-b0f02a608e35" />
## Document Loading
The system begins by loading the input PDF document from the local directory using LangChain’s PyPDFLoader.
Each page of the PDF is converted into a structured Document object that contains:

-Page content (text)

-Metadata such as page number and source file name

This structured format allows downstream processing while preserving document traceability.
