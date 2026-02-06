# Notetaker AI - Developer Specialties Analysis

## Project Overview

**Project:** notetaker_ai
**Type:** LLM-powered Zettelkasten note-taking system
**Purpose:** Convert YouTube videos and MP3 files into structured Obsidian notes using the Zettelkasten method with AI-generated literature notes and permanent notes
**Architecture:** Multi-stage pipeline with Redis vector store for RAG (Retrieval-Augmented Generation)

---

## 12 Core Developer Specialties

### 1. Zettelkasten Methodology Implementation

Understanding and implementing the Zettelkasten note-taking system in code:

- **Literature Notes**: First-pass notes capturing main ideas from source material
- **Permanent Notes**: Atomic, evergreen notes in your own words
- **Note Linking**: Creating connections between permanent notes
- **Progressive Summarization**: Multi-stage refinement of ideas
- **Obsidian Integration**: Generating markdown files compatible with Obsidian vault structure

**Key Files:**
- `literature_notes_provider.py:24` - `generate_each_batch()` for literature note creation
- `literature_notes_provider.py:78` - `generate_permanent_note_names()` for note naming
- `cli.py:140` - RAG menu with Zettelkasten workflow
- Prompts in `prompts/lit_notes.txt` and `prompts/permanent_note_names.txt`

**Skills Required:**
- Knowledge management principles
- Zettelkasten methodology
- Progressive summarization techniques
- Markdown formatting

---

### 2. OpenAI Whisper Integration for Speech-to-Text

Using OpenAI's Whisper model for high-quality audio transcription:

- Loading Whisper models (base, small, medium, large)
- GPU acceleration with CUDA
- Timestamp generation for video segments
- Multi-format output (TSV, TXT)
- Handling long-form audio (lectures, podcasts)

**Key Files:**
- `transcribe_provider.py:8` - `transcribe()` function with Whisper
- Environment: `WHISPER_MODEL=base` (configurable model size)

**Code Example:**
```python
model = whisper.load_model(WHISPER_MODEL, device="cuda")
result = model.transcribe(mp3_file)
writer = get_writer("tsv", output_dir=dir_name)
writer(result, tsv_file)
```

**Skills Required:**
- Audio processing fundamentals
- GPU programming (CUDA)
- Whisper API usage
- Timestamp handling
- TSV/TXT file formats

---

### 3. YouTube Video Processing with yt-dlp

Downloading and extracting audio from YouTube videos:

- Using yt-dlp for video downloads
- Audio extraction from video streams
- Metadata extraction (title, description, channel, thumbnail)
- URL parsing and validation
- Error handling for download failures

**Key Files:**
- `yt_dlp_mp3.py:16` - `download_audio()` function
- `video_provider.py:45` - `download_video_mp3()` with queue management
- `cli.py` - Video download menu integration

**Metadata Extracted:**
- Video title
- Channel name and URL
- Description
- Thumbnail URL
- Duration
- Generated key for storage

**Skills Required:**
- yt-dlp library usage
- YouTube API understanding
- Audio format conversion
- Metadata parsing
- File system operations

---

### 4. HDF5 Data Management

Using HDF5 (Hierarchical Data Format) for efficient storage of large tabular data:

- Storing transcripts as time-indexed dataframes
- Hierarchical key organization (/transcripts, /literature_notes, /videos)
- Querying by time ranges and indexes
- Append-only operations for incremental updates
- Column length optimization for strings

**Key Files:**
- `manager_hdf5.py:27` - `ManageHDF5` class
- `manager_hdf5.py:40` - `save_df_to_hd5()` method
- `manager_hdf5.py:110` - `get_dataframe()` method
- `manager_hdf5.py:148` - Time-based and index-based queries

**HDF5 Schema:**
```
/transcripts/{video_key}
  - columns: [start, end, text]
/literature_notes/{video_key}
  - columns: [text, timestamp]
/videos/{video_key}
  - columns: [status, mp3_file, description, etc.]
```

**Skills Required:**
- HDF5 file format
- h5py library
- Pandas HDFStore integration
- Hierarchical data structures
- Performance optimization for large datasets

---

### 5. Redis-Based Document Management

Using Redis for flexible document storage with lists and hashes:

- Redis Lists for document content (line-by-line storage)
- Redis Hashes for metadata/attributes
- Key prefix organization for namespacing
- Document versioning and updates
- Efficient range queries with LRANGE

**Key Files:**
- `document_manager_redis.py:14` - `DocumentManagerRedis` base class
- `video_provider.py:14` - Video queue management with Redis Sets
- `literature_notes_provider.py:14` - Literature notes in Redis
- `transcript_provider.py` - Transcript storage in Redis

**Redis Data Structures:**
```
# Document content (list)
transcripts:{key} = ["line1", "line2", "line3", ...]
transcripts:{key}:timestamps = ["0.0", "5.2", "10.1", ...]

# Document metadata (hash)
transcripts:{key}:attribs = {
  "src_document_key": "...",
  "duration": "...",
  "channel": "..."
}

# Queue sets
videos:waiting = {"url1", "url2", ...}
videos:mp3_downloaded = {"key1", "key2", ...}
videos:transcribed = {"key3", "key4", ...}
```

**Skills Required:**
- Redis data structures (Lists, Sets, Hashes)
- Redis CLI and commands
- Key naming strategies
- Document serialization
- Atomic operations

---

### 6. LangChain RAG Pipeline Implementation

Building Retrieval-Augmented Generation systems with LangChain:

- Vector embeddings with HuggingFace models (all-MiniLM-L6-v2)
- Redis as vector store backend
- Similarity search with distance thresholds
- Prompt templates with context injection
- Chain composition (Retriever + LLM)

**Key Files:**
- `rag_provider.py:37` - `RagProvider` class
- `rag_provider.py:82` - Document loading from URLs/files
- `rag_provider.py:124` - Vector store creation
- `rag_provider.py:185` - RAG pipeline building
- `rag_provider.py:241` - Distance optimization algorithm

**RAG Architecture:**
```
User Query
    ↓
Embeddings (HuggingFace)
    ↓
Similarity Search (Redis Vector Store)
    ↓
Context Documents (top-k with distance threshold)
    ↓
Prompt Template (context + question)
    ↓
LLM (Ollama ChatOllama)
    ↓
Generated Answer
```

**Skills Required:**
- Vector embeddings
- Similarity search algorithms
- LangChain framework
- RAG architecture patterns
- Prompt engineering for QA

---

### 7. Distance Optimization with SciPy

Implementing a unique distance optimization algorithm for RAG tuning:

- Using SciPy optimize for finding optimal distance thresholds
- Target-based optimization (e.g., "return exactly 7 results")
- Balancing precision and recall
- Adaptive distance tuning per query type

**Key Files:**
- `rag_provider.py:14` - SciPy import
- `rag_provider.py:241` - `optimize_distance()` method
- `cli.py:241` - User-facing distance optimization

**Algorithm Concept:**
```python
# Goal: Find distance threshold that returns exactly N results
def objective_function(distance, query, target_count):
    results = vectorstore.similarity_search(query, distance_threshold=distance)
    return abs(len(results) - target_count)

optimized_distance = scipy.optimize.minimize(objective_function, x0=0.71)
```

**Skills Required:**
- SciPy optimization
- Objective function design
- Vector distance metrics
- Hyperparameter tuning
- Performance profiling

---

### 8. LangChain + Ollama Integration

Connecting local LLMs (Ollama) with LangChain workflows:

- ChatOllama for conversational models
- Prompt template chaining
- Streaming responses
- Model configuration (temperature, context window)
- Remote Ollama instances (ngrok tunneling)

**Key Files:**
- `manage_llm.py:12` - `LLMManager` class
- `manage_llm.py:19` - Model and chain initialization
- `rag_provider.py:193` - ChatOllama in RAG pipeline
- `.env:OLLAMA_BASEURL` - Remote/ngrok URL support

**Chain Pattern:**
```python
prompt = ChatPromptTemplate.from_template(prompt_text)
model = ChatOllama(base_url=OLLAMA_URL, model=OLLAMA_MODEL)
chain = prompt | model
result = chain.invoke({"text": user_input})
```

**Skills Required:**
- LangChain LCEL (LangChain Expression Language)
- Ollama API
- Prompt template design
- Chain debugging
- Remote API configuration

---

### 9. Interactive CLI Development with Rich & Questionary

Building user-friendly terminal interfaces:

- Questionary for interactive prompts (select, checkbox, text)
- Rich library for formatted output (tables, markdown, panels)
- Menu-driven navigation with state management
- File browser with directory traversal
- Real-time table updates for queue status

**Key Files:**
- `cli.py:1` - CLI imports (click, questionary, Rich)
- `cli.py:60` - `create_df_table()` for dataframe display
- `cli.py:74` - `show_video_queue_state()` with Rich tables
- `cli.py:98` - `get_file_list()` interactive file browser
- `cli.py:140` - `rag_menu()` with dynamic option management

**UI Features:**
- Select menus with emoji labels
- Checkbox multi-select for files
- Text input with defaults
- Dataframe rendering as Rich tables
- Markdown rendering in terminal
- Dynamic menu options based on state

**Skills Required:**
- Click framework
- Questionary library
- Rich terminal formatting
- ANSI escape codes
- Interactive prompt design
- State-driven UI updates

---

### 10. Batch Processing with LLM Workflows

Processing long transcripts in batches with LLM summarization:

- Chunking transcripts into manageable batches (15-line default)
- Timestamp preservation for batch boundaries
- Sequential LLM processing with progress tracking
- Prompt management with variable substitution
- Result aggregation and storage

**Key Files:**
- `literature_notes_provider.py:24` - `generate_each_batch()` method
- Batch size: 15 lines per LLM call
- Progress: `range(0, size, batch_size)` iteration
- Timestamp tracking: `df["start"].min()` for batch start time

**Workflow:**
```
Transcript (1000 lines)
    ↓
Split into batches (15 lines each)
    ↓
For each batch:
  - Extract text
  - Get timestamp range
  - Call LLM with prompt
  - Parse response
  - Store result with timestamp
    ↓
Combine batches → Literature Note
```

**Skills Required:**
- Batch processing patterns
- LLM token management
- Progress tracking
- Error recovery mid-batch
- Result aggregation

---

### 11. Prompt Management and Templating

Managing reusable prompts for different LLM tasks:

- Redis-based prompt storage with versioning
- Prompt categorization by type (rag_prompt, literature_note, permanent_note)
- Variable substitution in templates
- Prompt retrieval by key
- CRUD operations for prompts

**Key Files:**
- `prompt_manager_redis.py` - Prompt storage in Redis
- `manage_llm.py:8` - Prompt file paths configuration
- Prompt types: `rag_prompt`, `literature_note`, `permanent_note`, `summarize`

**Prompt Template Example:**
```
Given the following transcript:

{text}

Create a literature note following the Zettelkasten method. Include:
1. Main ideas (3-5 bullet points)
2. Key quotes
3. Questions raised
4. Connections to existing knowledge
```

**Skills Required:**
- Template systems
- Variable interpolation
- Prompt engineering
- Versioning strategies
- Key-value store design

---

### 12. Multi-Stage Pipeline Orchestration

Coordinating complex workflows across multiple stages:

- **Stage 1**: Video download (URL → MP3)
- **Stage 2**: Transcription (MP3 → TSV with timestamps)
- **Stage 3**: Literature note generation (Transcript → Lit notes)
- **Stage 4**: Permanent note naming (Lit notes → Note titles)
- **Stage 5**: Permanent note generation (Lit notes + Title → Final note)
- **Stage 6**: RAG indexing (Documents → Vector store)

**Queue Management:**
```
WAITING → MP3_DOWNLOADED → TRANSCRIBED → PROCESSED
```

**Key Files:**
- `cli.py` - Main orchestration in menu system
- `video_provider.py:27` - Queue state transitions
- `literature_notes_provider.py` - Processing stages
- `rag_provider.py` - Final indexing stage

**Skills Required:**
- Workflow orchestration
- State machines
- Error handling across stages
- Idempotent operations
- Progress checkpointing

---

## Supporting Specialties

### 13. Text Chunking and Splitting

Using LangChain text splitters for document preparation:

- RecursiveCharacterTextSplitter for semantic chunking
- Chunk size: 500 chars with 50 char overlap
- Start index tracking for reference
- Metadata preservation across chunks

**Key Files:**
- `rag_provider.py:87` - Text splitting configuration
- `rag_provider.py:154` - Document generation from text

---

### 14. Web Scraping (Bonus)

Scrapy spider for OpenBible.info data extraction:

**Key Files:**
- `openbible_info/openbible_info/spiders/openbible.py` - Scrapy spider
- Religious text cross-referencing and topic extraction

---

### 15. Flask Web Interface (Bonus)

Optional web UI for remote access:

**Key Files:**
- `app.py` - Flask application

---

## Technology Stack Summary

**Core Technologies:**
- Python 3.x
- OpenAI Whisper (speech-to-text)
- yt-dlp (YouTube downloads)
- Redis (document + vector store)
- HDF5/h5py (structured data storage)
- LangChain (RAG orchestration)
- Ollama (local LLM inference)

**Key Libraries:**
- `openai-whisper` - Speech recognition
- `yt_dlp` - Video downloading
- `h5py` + `pandas` + `tables` - HDF5 data management
- `redis` - Redis client
- `langchain` - LLM orchestration framework
  - `langchain-ollama` - Ollama integration
  - `langchain-huggingface` - Embeddings
  - `langchain-community` - Vector stores
  - `langchain-classic` - Deprecated RetrievalQA (needs migration)
- `sentence-transformers` - Embedding models
- `click` - CLI framework
- `questionary` - Interactive prompts
- `rich` - Terminal formatting
- `scipy` - Optimization algorithms
- `torch` - Deep learning backend (CPU/CUDA)
- `feedparser` - RSS feed parsing
- `flask` - Web framework
- `falkordb` - Graph database (optional integration)
- `ollama-ocr` - OCR processing

**Development Tools:**
- python-dotenv for configuration
- pytest for testing (implied)
- CUDA for GPU acceleration

---

## Architecture Patterns

1. **Pipeline Pattern**: Multi-stage processing (download → transcribe → generate → index)
2. **Provider Pattern**: Specialized providers for each document type (Video, Transcript, LiteratureNote)
3. **Repository Pattern**: DocumentManagerRedis as base class for all storage
4. **Queue Pattern**: Redis Sets for workflow state management
5. **Chain of Responsibility**: LangChain LCEL for LLM workflows
6. **Template Method**: Base prompts with variable substitution
7. **Lazy Loading**: Models loaded on-demand (Whisper, LLM, embeddings)

---

## Key Workflows

### Complete Note Generation Workflow

```
1. User provides YouTube URL
   ↓
2. Download MP3 (yt-dlp)
   - Extract metadata
   - Queue state: WAITING → MP3_DOWNLOADED
   ↓
3. Transcribe audio (Whisper)
   - Generate TSV with timestamps
   - Store in HDF5/Redis
   - Queue state: MP3_DOWNLOADED → TRANSCRIBED
   ↓
4. Generate Literature Note (LLM + batching)
   - Process 15-line batches
   - Apply literature note prompt
   - Store with timestamps
   ↓
5. Generate Permanent Note Names (LLM)
   - Read full literature note
   - Apply naming prompt
   - Return list of suggested titles
   ↓
6. Generate Permanent Note (LLM)
   - User selects note name
   - Apply permanent note prompt
   - Generate atomic note in Zettelkasten format
   ↓
7. Save to Obsidian
   - Write .md file
   - Copy to vault directory
```

### RAG Query Workflow

```
1. Create/Select Vector Index
   - Load documents from files or transcripts
   - Chunk into 500-char segments
   - Generate embeddings (all-MiniLM-L6-v2)
   - Store in Redis vector store
   ↓
2. Optimize Distance Threshold
   - User provides sample query
   - SciPy optimization finds threshold
   - Returns target number of results
   ↓
3. Build RAG Pipeline
   - Load prompt template
   - Configure retriever with distance
   - Chain: Retriever → Prompt → LLM
   ↓
4. Query Loop
   - User enters question
   - Re-optimize distance per query (optional)
   - Retrieve context documents
   - Generate answer with LLM
   - Display with Rich markdown
```

---

## Domain Knowledge Requirements

### Zettelkasten Principles

- **Atomicity**: One idea per note
- **Autonomy**: Notes standalone without context
- **Connectivity**: Links create knowledge graph
- **Progressive Summarization**: Multi-stage refinement

### Knowledge Management

- Literature notes (fleeting → permanent)
- Evergreen notes
- Note linking strategies
- Obsidian vault structure

### Audio/Video Processing

- Audio codecs and formats
- Timestamp synchronization
- GPU vs CPU transcription trade-offs
- Whisper model size selection (base vs large)

---

## Configuration Management

### Environment Variables (.env)

```bash
# Storage
HDF5_PATH="data/processed/transcripts.h5"
DATA_PATH="data/transcripts"
MP3_PATH="data/mp3"

# Redis
REDIS_PORT="6389"
REDIS_SERVER="localhost"
REDIS_PASSWORD=""

# Ollama
OLLAMA_BASEURL="https://vicunaapi.ngrok.io"
OLLAMA_MODEL="mistral-nemo:12b"

# Whisper
WHISPER_MODEL="base"

# Embeddings
EMBEDDINGS_MODEL="all-MiniLM-L6-v2"

# Prompts
SUMMARIZE_PROMPT="prompts/summarize.txt"
```

---

## Performance Considerations

1. **GPU Acceleration**: Whisper transcription 10-50x faster on CUDA vs CPU
2. **HDF5 Indexing**: Time-based queries use pandas indexing for speed
3. **Redis Vector Search**: Sub-second similarity search with proper distance tuning
4. **Batch Size**: 15-line batches balance LLM context vs. granularity
5. **Chunk Overlap**: 50-char overlap prevents semantic breaks in RAG
6. **Model Selection**: Base Whisper (fast) vs Large (accurate) trade-off

---

## Testing Considerations

**Test Data Requirements:**
- Sample YouTube videos (short, 2-5 min)
- Pre-transcribed audio for validation
- Sample literature notes
- Reference permanent notes
- RAG test queries with known answers

**Test Infrastructure:**
- Redis instance for document storage
- Ollama with test model
- GPU for Whisper testing
- HDF5 test database

---

## Deployment Requirements

### System Setup

```bash
# Install dependencies
pip install -r requirements.txt

# Install FFMPEG for audio conversion
sudo apt-get install ffmpeg

# Start Redis
docker run -d -p 6389:6379 redis:latest

# Start Ollama
ollama serve

# Pull Ollama model
ollama pull mistral-nemo:12b

# Download Whisper model (happens automatically on first use)
# Models stored in ~/.cache/whisper/

# Create data directories
mkdir -p data/mp3 data/transcripts data/processed

# Configure environment
cp .env.example .env
# Edit .env with your settings

# Run application
python cli.py
```

### Optional: GPU Setup

```bash
# Install CUDA toolkit
# Follow NVIDIA CUDA installation guide

# Install PyTorch with CUDA support
pip install torch==2.7.1 torchvision==0.22.1 torchaudio==2.7.1
```

---

## Integration Points

### 1. Obsidian Vault Integration

- Generated .md files compatible with Obsidian
- Wikilink format for note connections
- Frontmatter YAML for metadata
- Tag support (#topic, #source/video)

### 2. YouTube Data

- Video metadata extraction
- Thumbnail URLs
- Channel information
- Duration and timestamps

### 3. Ollama API

- Remote instance support via ngrok
- Model switching without restart
- Streaming responses (not yet implemented)

### 4. Redis Vector Store

- Langchain Redis integration
- Embedding storage
- Similarity search
- Index management

---

## Future Enhancement Opportunities

1. **Streaming Responses**: Real-time LLM output for better UX
2. **Parallel Processing**: Multi-GPU transcription for batches
3. **Auto-Linking**: Detect connections between permanent notes
4. **Web UI**: Full-featured web interface beyond CLI
5. **Mobile App**: Record and transcribe on mobile devices
6. **Multi-Language**: Support for non-English transcription
7. **Custom Embeddings**: Fine-tune embeddings for domain-specific RAG
8. **Graph Visualization**: Obsidian-style graph view
9. **Collaborative Filtering**: Recommend notes based on usage
10. **Export Formats**: Notion, Roam Research, etc.

---

## Known Issues / Technical Debt

1. **Deprecated LangChain API**: Still using `RetrievalQA` which is deprecated
   - Need migration to `create_retrieval_chain`
   - File: `rag_provider.py:198`

2. **HDF5 + Redis Dual Storage**: Same data stored in both systems
   - HDF5 for dataframes with time queries
   - Redis for line-by-line access
   - Could consolidate to Redis only

3. **Error Handling**: Limited error recovery in multi-stage pipeline
   - Manual queue cleanup required on failures

4. **Prompt Versioning**: No version control for prompts
   - Changes to prompts can break existing workflows

---

## Unique Innovations

### Distance Optimization Algorithm

**Problem:** Standard RAG retrieves top-k documents, but optimal k varies by query.

**Solution:** Optimize distance threshold to return target number of results:
```python
def optimize_distance(query, target_count=7):
    def objective(distance):
        results = similarity_search(query, distance_threshold=distance)
        return abs(len(results) - target_count)

    result = scipy.optimize.minimize(objective, x0=0.71)
    return result.x[0]
```

**Benefits:**
- Adaptive retrieval based on query type
- Better precision/recall balance
- Reduces hallucination from irrelevant context

---

*Last Updated: 2026-01-09*
*Project Location: /home/mcstar/notetaker_ai*
