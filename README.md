
# Policy Insights

Policy Insights is a full-stack application designed to provide semantic search capabilities for text-based documents. Users can upload .txt or .pdf files, and the system extracts, chunks, and embeds the text content. This allows for natural language querying to retrieve the most contextually relevant snippets from the document library.

## Features

- **File Upload**: Supports both .txt and .pdf document uploads.
- **Text Extraction**: Automatically extracts readable text from PDF and plain text files.
- **Text Chunking**: Splits large documents into smaller, manageable, and overlapping text chunks to prepare for embedding.
- **Semantic Embedding**: Converts text chunks into vector embeddings using an external AI service (with a mock fallback for offline development).
- **Semantic Search**: Allows users to ask natural language questions and retrieves the most relevant text chunks based on cosine similarity scores.

## Tech Stack

### Backend

- **Framework**: .NET 8
- **Language**: C#
- **API Type**: RESTful API
- **Database**: SQLite (for local development)
- **ORM**: Entity Framework Core
- **PDF Parsing**: PdfPig

### Frontend

- **Library**: React
- **Build Tool**: Vite
- **Language**: JavaScript (JSX)

## Setup and Installation

### Prerequisites

- .NET 8 SDK
- Node.js and npm

### Backend Setup

1. Navigate to the backend project directory:
   ```bash
   cd backend/PI.Api
   ```

2. Restore the .NET dependencies:
   ```bash
   dotnet restore
   ```

3. Create and apply the database migrations. This will create the PIDb.db file.
   ```bash
   dotnet ef database update
   ```

4. Run the backend server:
   ```bash
   dotnet run
   ```

The API will be running on the port specified in the terminal output (e.g., `http://localhost:5169`).

### Frontend Setup

1. In a separate terminal, navigate to the frontend project directory:
   ```bash
   cd frontend
   ```

2. Install the Node.js dependencies:
   ```bash
   npm install
   ```

3. Run the frontend development server:
   ```bash
   npm run dev
   ```

The application will be accessible at the URL shown in the terminal (e.g., `http://localhost:5173`).

## Configuration

### Backend

- **Database**: The database connection string is configured in `backend/PI.Api/appsettings.json`. By default, it is set up to use a local SQLite file named `PIDb.db`.
- **CORS**: The Cross-Origin Resource Sharing policy is defined in `backend/PI.Api/Program.cs`. It is configured to allow requests from the default Vite development server URL.
- **AI Service**: The application can connect to the Google Gemini API for embeddings. To enable this, set an environment variable named `GEMINI_API_KEY` with your API key before running the backend.

```powershell
# Example for PowerShell
$env:GEMINI_API_KEY = "YOUR_API_KEY_HERE"
dotnet run
```

If the `GEMINI_API_KEY` is not provided, the system will use a mock service that generates random vectors, allowing the application to be fully functional for offline development.

### Frontend

- **API URL**: The URL for the backend API is configured in `frontend/src/api.js`. Ensure the `API_BASE_URL` constant matches the address and port where your backend server is running.

## API Endpoints

### 1. Upload Document

- **URL**: `/api/documents/upload`
- **Method**: `POST`
- **Body**: `multipart/form-data`
- **Form Field**: `file` (the uploaded .txt or .pdf file)
- **Success Response**: `200 OK`

```json
{
  "documentId": 1,
  "title": "your-document-name.pdf"
}
```

### 2. Search Documents

- **URL**: `/api/search`
- **Method**: `POST`
- **Body**: `application/json`

```json
{
  "query": "your natural language question",
  "topK": 5
}
```

- **Success Response**: `200 OK`

```json
[
  {
    "documentId": 1,
    "title": "your-document-name.pdf",
    "chunkId": 12,
    "chunkText": "The relevant text snippet from the document...",
    "score": 0.895
  }
]
```

## Architectural Concepts

- **Semantic Search**: The core functionality relies on converting text into numerical vectors (embeddings). The relevance of a document chunk to a search query is determined by calculating the cosine similarity between their respective vectors, allowing for searches based on meaning rather than keywords.
- **Chunking Strategy**: Documents are split into small, overlapping chunks. The overlap helps preserve the context of sentences that might otherwise be split across two chunks, improving the quality of search results.
- **In-Memory Search**: For this proof-of-concept, the search operation loads all document chunk embeddings into memory and calculates similarity scores on the fly. This is suitable for a small number of documents. For a production system with a large dataset, a specialized vector database (e.g., Pinecone, Milvus, or a PostgreSQL extension like pgvector) would be used for efficient, indexed similarity searches.
```
