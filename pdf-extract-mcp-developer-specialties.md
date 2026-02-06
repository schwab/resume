# PDF Extract MCP - Developer Specialties Analysis

## Project Overview

**Project:** pdf_extract_mcp
**Type:** MCP Server for AI-powered document processing
**Purpose:** Extract structured data from business documents (receipts, rate confirmations, bills of lading) using vision and text-based LLMs, with optional FalkorDB graph database persistence
**Version:** 0.1.0
**Architecture:** FastMCP-based server with processor pattern for multi-modal document analysis

---

## 10 Core Developer Specialties

### 1. Document Classification with LLMs

Building AI-driven document type detection systems:

- Using vision models (multimodal) to classify scanned documents
- Using text models for text-based PDFs
- Prompt engineering for classification accuracy
- Handling ambiguous document types
- Working with limited training data through few-shot prompting

**Key Files:**
- `src/pdf_extract/processors/base.py:39` - `get_doc_type()` method
- `src/pdf_extract/processors/image.py:27` - Vision-based classification
- `src/pdf_extract/processors/text.py:22` - Text-based classification
- `src/pdf_extract/__init__.py:245` - `DOC_TYPES` dictionary

**Skills Required:**
- LLM prompt design for classification
- Multi-modal AI understanding
- Domain knowledge of business documents
- Few-shot learning techniques

---

### 2. JSON Schema Design for Structured Extraction

Creating comprehensive JSON schemas that define extraction targets:

- Designing schemas for domain-specific documents (logistics, receipts, etc.)
- Nested object structures for complex relationships
- Validation patterns (regex for phone numbers, dates, etc.)
- Array handling for multi-stop routes
- Required vs. optional field management

**Key Files:**
- `src/pdf_extract/__init__.py:4` - `RATECON_SCHEMA` (145 lines of detailed schema)
- `src/pdf_extract/__init__.py:146` - `BOL_SCHEMA` (Bill of Lading)
- `src/pdf_extract/__init__.py:197` - `RECEIPT_SCHEMA`

**Example Schema Fields:**
```json
{
  "stops": [
    {
      "stop_number": 1,
      "stop_type": "pickup",
      "city": "Los Angeles",
      "state": "CA",
      "mileage_to_next_stop": 280.5
    }
  ],
  "state_mileage": [
    {
      "state": "CA",
      "miles_traveled": 280.5,
      "gallons_used": 40.07
    }
  ]
}
```

**Skills Required:**
- JSON Schema specification (draft 2020-12)
- Domain modeling
- Data validation rules
- Nested structure design

---

### 3. Vision Model Integration (OCR + LLM)

Leveraging vision-capable LLMs for document understanding:

- Using `ollama-ocr` library for multimodal processing
- Extracting text and structure from images/PDFs without text layer
- Custom prompt construction for OCR
- Handling multi-page documents
- Cleaning and parsing OCR output

**Key Files:**
- `src/pdf_extract/processors/image.py:9` - `OCRProcessor` from ollama-ocr
- `src/pdf_extract/processors/image.py:18` - ImageToJson class
- `src/pdf_extract/processors/image.py:29` - `process_image()` workflow

**Skills Required:**
- Vision model understanding (llava, cogvlm, etc.)
- OCR quality assessment
- Image preprocessing
- Prompt engineering for vision models

---

### 4. PDF Processing with PyMuPDF

Working with PDF files programmatically:

- Extracting text content from PDFs
- Determining if PDFs contain text layers
- Page-by-page processing
- Handling various PDF formats
- Text extraction quality validation

**Key Files:**
- `src/pdf_extract/processors/base.py:1` - PyMuPDF import
- `src/pdf_extract/processors/base.py:31` - `contains_text()` method
- `src/pdf_extract/processors/base.py:53` - `get_page_text()` method

**Skills Required:**
- PDF structure understanding
- PyMuPDF (fitz) library usage
- Text encoding handling
- PDF quirks and edge cases

---

### 5. Processor Pattern Architecture

Implementing the strategy/processor pattern for different document types:

- Base processor abstraction
- Specialized processors (ImageToJson, TextToJson)
- Dynamic processor selection based on file type
- Shared configuration and utilities
- Template method pattern for common workflows

**Key Files:**
- `src/pdf_extract/processors/base.py:15` - `BaseProcessor` class
- `src/pdf_extract/processors/image.py:12` - `ImageToJson` processor
- `src/pdf_extract/processors/text.py:12` - `TextToJson` processor
- `src/pdf_extract/file_monitor.py:58` - `get_processor()` factory method

**Architecture:**
```
BaseProcessor (abstract)
    ├── ImageToJson (for scanned PDFs/images)
    └── TextToJson (for text-layer PDFs)
```

**Skills Required:**
- Design patterns (Strategy, Template Method, Factory)
- Object-oriented programming
- Abstraction design
- Code reusability principles

---

### 6. Graph Database Integration (FalkorDB)

Persisting extracted document data in a graph database:

- Converting nested JSON to Cypher queries
- Creating nodes and relationships from dictionaries
- Managing graph indexes for performance
- Handling duplicate detection
- Recursive data structure traversal

**Key Files:**
- `src/pdf_extract/data_access/falkordb_persist.py:9` - `DB` class
- `src/pdf_extract/data_access/falkordb_persist.py:98` - `dict_to_cypher()` converter
- `src/pdf_extract/data_access/falkordb_persist.py:53` - Index management

**Cypher Generation Example:**
```cypher
CREATE (n0:receipt {date: '2024-01-15', total: 45.67, company: 'Shell'})
CREATE (n_products_0:products {description: 'Diesel', qty: 15, line_total: 45.00})
CREATE (n0)-[:HAS_ITEM]->(n_products_0)
```

**Skills Required:**
- Graph database concepts
- Cypher query language
- FalkorDB/Redis integration
- Relationship modeling
- Recursive algorithms

---

### 7. FastMCP Server Development

Building MCP servers using the FastMCP framework:

- Defining MCP tools with decorators
- Async tool implementations
- Tool parameter validation
- Error handling and response formatting
- Server initialization and lifecycle

**Key Files:**
- `src/pdf_extract/mcp/server.py:18` - FastMCP initialization
- `src/pdf_extract/mcp/server.py:21` - `@mcp.tool()` decorator usage
- `src/pdf_extract/mcp/server.py:22` - `process_document()` tool
- `src/pdf_extract/mcp/server.py:104` - `batch_process_documents()` tool

**Tool Definition Example:**
```python
@mcp.tool()
async def process_document(
    file_path: str,
    save_to_db: bool = False
) -> dict:
    """Process a single business PDF..."""
    # Implementation
```

**Skills Required:**
- FastMCP framework
- MCP protocol understanding
- Async Python
- API design
- Tool interface design

---

### 8. File System Monitoring and Batch Processing

Implementing file watchers and batch processing workflows:

- Using `watchdog` library for file system events
- Recursive directory traversal
- Missing file detection (comparing filesystem to database)
- Batch processing with error handling
- File filtering by type and date patterns

**Key Files:**
- `src/pdf_extract/file_monitor.py:1` - Watchdog integration
- `src/pdf_extract/file_monitor.py:24` - `get_missing_files()` function
- `src/pdf_extract/file_monitor.py:67` - `process_files()` batch processor
- `src/pdf_extract/file_monitor.py:97` - `MyHandler` event handler

**Skills Required:**
- File system monitoring
- Event-driven programming
- Batch processing patterns
- Error recovery strategies
- Filesystem operations

---

### 9. Environment-Driven Configuration

Managing configuration through environment variables and files:

- `.env` file management with python-dotenv
- Configuration loading patterns
- Default value handling
- Prompt file loading
- Account mapping configuration

**Key Files:**
- `src/pdf_extract/processors/base.py:7` - Environment variable loading
- `src/pdf_extract/processors/base.py:59` - `load_dotenv_file()` method
- `src/pdf_extract/processors/base.py:87` - `load_prompt_file()` method
- `src/pdf_extract/processors/base.py:66` - `load_json_file()` for config

**Environment Variables:**
```bash
DOCTYPE_PROMPT_FILE=/path/to/prompts/doctype.txt
EXTRACT_DETAILS_PROMPT_FILE=/path/to/prompts/extract.txt
ACCOUNT_MAP_FILE=/path/to/config/accounts.json
MM_MODEL_NAME=llava:13b
TXT_MODEL_NAME=qwen2.5:7b
OLLAMA_URL=http://localhost:11434/api/generate
FALKORDB_HOST=localhost
FALKORDB_PORT=6379
FALKORDB_GRAPH=VTCLLC
```

**Skills Required:**
- Environment variable management
- Configuration best practices
- Secret management
- Config file formats

---

### 10. Prompt Engineering for Data Extraction

Crafting prompts that guide LLMs to extract structured data:

- Schema-aware prompt construction
- Template-based prompting with variable substitution
- Account mapping integration
- Output format enforcement (JSON)
- Multi-page document handling

**Key Files:**
- `src/pdf_extract/processors/base.py:17` - Prompt loading
- `src/pdf_extract/processors/image.py:80` - Extraction prompt formatting
- `src/pdf_extract/processors/text.py:25` - Text-based classification prompt
- `src/pdf_extract/processors/text.py:85` - Extraction prompt for text

**Prompt Template Example:**
```python
final_prompt = extract_details_prompt.format(
    doc_type=doc_type,
    accounts=account_map[doc_type],
    file_name=full_path,
    schemas=DOC_TYPES[doc_type]
)
```

**Skills Required:**
- Prompt engineering techniques
- Template systems
- JSON output enforcement
- Domain-specific instruction writing
- Few-shot example construction

---

## Supporting Specialties

### 11. Error Handling and Data Validation

Robust error handling throughout the extraction pipeline:

- Try/except blocks at critical points
- JSON parsing error recovery
- Missing field handling
- Type coercion and validation
- Graceful degradation

**Key Patterns:**
```python
try:
    json_data = json.loads(result)
    if "doc_type" not in json_data:
        return "No doc type found."
    # Process...
except Exception as err:
    print(f"Error getting doc_type {str(err)}")
    return None
```

---

### 12. Multi-Model Strategy

Using different models for different tasks:

- Vision models (MM_MODEL_NAME) for images and scanned PDFs
- Text models (TXT_MODEL_NAME) for text-layer PDFs
- Model selection based on document characteristics
- Ollama URL configuration for model hosting

**Strategy:**
- **Has text layer?** → Use text model (faster, cheaper)
- **Image/scan only?** → Use vision model (slower, more capable)

---

## Technology Stack Summary

**Core Technologies:**
- Python 3.10+
- PyMuPDF for PDF processing
- Ollama for LLM inference
- FastMCP for MCP server implementation
- FalkorDB (Redis-based graph database)

**Key Libraries:**
- `pymupdf` - PDF manipulation
- `python-dotenv` - Environment configuration
- `ollama-ocr` - Vision model OCR
- `falkordb` - Graph database client
- `watchdog` - File system monitoring
- `requests` - HTTP requests to Ollama
- `fastmcp` - MCP server framework

**Development Tools:**
- pytest for testing
- setuptools for packaging

---

## Domain Knowledge Requirements

### Logistics Industry Terminology

**Rate Confirmations:**
- MC/DOT numbers
- Carrier information
- Broker details
- Load types (FTL, LTL, partial)
- Trailer types (dry van, reefer, flatbed)
- IFTA reporting (state-by-state mileage)
- Quick pay options
- Hazmat classifications

**Bills of Lading:**
- BOL numbers
- PRO numbers
- Shipper/consignee addresses
- Initial loading locations
- Commodity descriptions

**Fuel Receipts:**
- Gallons purchased
- Price per gallon
- Payment types
- Credit card information
- Line item parsing

---

## Architecture Patterns

1. **Processor Pattern**: Different processors for different document types
2. **Factory Pattern**: `get_processor()` selects appropriate processor
3. **Template Method**: Base class defines workflow, subclasses implement specifics
4. **Strategy Pattern**: Text vs. image processing strategies
5. **Repository Pattern**: DB class abstracts FalkorDB operations

---

## Key Business Logic

### Document Processing Workflow

```
1. File Detection
   ↓
2. Processor Selection (image vs. text)
   ↓
3. Document Classification (receipt, rate_con, bol)
   ↓
4. Schema Loading
   ↓
5. Data Extraction (LLM with schema prompt)
   ↓
6. JSON Validation
   ↓
7. Optional: Database Persistence (Cypher conversion)
   ↓
8. Result Return
```

### Date Handling

Special date extraction from filenames:
- Pattern: `YYYYMMDD_` or `YYYY-MM-DD_`
- Conversion to FalkorDB date format: `YYYY-MM-DD`
- Fallback to document content if filename doesn't match

---

## MCP Tools Exposed

### 1. process_document

**Purpose:** Process a single PDF or image file
**Parameters:**
- `file_path: str` - Absolute path to file
- `save_to_db: bool = False` - Whether to persist to FalkorDB

**Returns:**
```json
{
  "success": true,
  "file": "/path/to/file.pdf",
  "doc_type": "receipt",
  "data": { /* extracted fields */ },
  "saved_to_db": false
}
```

### 2. batch_process_documents

**Purpose:** Process multiple documents from a directory
**Parameters:**
- `directory: str` - Directory path
- `filter_doc_types: list[str] = None` - Optional filter
- `recursive: bool = True` - Search subdirectories
- `save_to_db: bool = False` - Persist to database

**Returns:**
```json
{
  "success": true,
  "directory": "/path/to/dir",
  "total_files": 50,
  "processed": 45,
  "failed": 3,
  "skipped": 2,
  "files": [ /* per-file results */ ]
}
```

---

## Integration Points

### 1. Ollama Integration

Direct HTTP requests to Ollama API:
```python
payload = {
    "model": self.txt_model_name,
    "prompt": prompt,
    "stream": False
}
response = requests.post(self.ollama_url, json=payload)
```

### 2. FalkorDB Integration

Graph database for relationship-rich data:
```python
db = DB()
db.connect()
cypher = db.dict_to_cypher(data, root_key=doc_type)
db.execute_cypher(cypher[0])
```

### 3. MCP Client Integration

Designed to work with mcp-client-for-ollama:
```python
# MCP client calls
result = await pdf_extract.process_document(
    file_path="/path/to/receipt.pdf",
    save_to_db=True
)
```

---

## Testing Considerations

**Test Data Requirements:**
- Sample PDFs of each document type
- Text-layer PDFs vs. scanned images
- Multi-page documents
- Edge cases (missing fields, malformed data)

**Test Environment:**
- Ollama with appropriate models installed
- FalkorDB instance running
- Test .env configuration
- Sample prompt files

---

## Performance Considerations

1. **Model Selection Impact:**
   - Vision models: 5-15 seconds per page
   - Text models: 1-3 seconds per document

2. **Batch Processing:**
   - Processes files sequentially (no parallel processing)
   - Could be optimized with async processing

3. **Database Operations:**
   - Index creation on first run
   - Duplicate detection via file existence check

---

## Deployment Requirements

**Environment Setup:**
```bash
# Install dependencies
pip install pymupdf python-dotenv ollama-ocr falkordb watchdog requests fastmcp

# Configure environment
cp .env.example .env
# Edit .env with your settings

# Start FalkorDB
docker run -p 6379:6379 falkordb/falkordb

# Start Ollama
ollama serve

# Pull required models
ollama pull llava:13b
ollama pull qwen2.5:7b

# Run MCP server
pdf-extract-mcp
```

---

## Future Enhancement Opportunities

1. **Parallel Processing**: Async batch processing for speed
2. **Model Fine-tuning**: Custom models for specific document types
3. **Confidence Scores**: Return extraction confidence levels
4. **Auto-correction**: Validate extracted data against business rules
5. **Template Learning**: Learn document templates from examples
6. **Multi-language Support**: Handle documents in various languages
7. **Real-time Monitoring**: Dashboard for batch processing status

---

*Last Updated: 2026-01-09*
*Project Location: /home/mcstar/Nextcloud/DEV/pdf_extract_mcp*
