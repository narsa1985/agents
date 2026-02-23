# Design Document: Transcript Documentation Generator

## Overview

The Transcript Documentation Generator is a Python-based command-line tool that transforms raw LangGraph course transcripts into structured, interview-ready Markdown documentation. The system employs a pipeline architecture with four main stages: file parsing, topic grouping, healthcare classification, and markdown generation. The design emphasizes content fidelity, intelligent consolidation, and consistent output formatting.

## Architecture

The system follows a linear pipeline architecture:

```
Input Transcripts → Parser → Topic Grouper → Healthcare Classifier → Markdown Generator → Output Files
```

### Pipeline Stages

1. **File Parser**: Reads transcript files and extracts title and content
2. **Topic Grouper**: Analyzes titles and content to identify related topics for consolidation
3. **Healthcare Classifier**: Determines healthcare applicability and adapts content accordingly
4. **Markdown Generator**: Formats content according to the structured template and writes output files

### Data Flow

```mermaid
graph LR
    A[Transcript Files] --> B[File Parser]
    B --> C[Parsed Transcripts]
    C --> D[Topic Grouper]
    D --> E[Grouped Topics]
    E --> F[Healthcare Classifier]
    F --> G[Classified Content]
    G --> H[Markdown Generator]
    H --> I[Output MD Files]
```

## Components and Interfaces

### 1. TranscriptParser

**Responsibility**: Read and parse transcript files from the input directory.

**Interface**:
```python
class TranscriptParser:
    def __init__(self, input_directory: str)
    def parse_all_transcripts(self) -> List[Transcript]
    def parse_single_file(self, filepath: str) -> Optional[Transcript]
```

**Transcript Data Structure**:
```python
@dataclass
class Transcript:
    filename: str
    title: str
    content: str
    raw_path: str
```

### 2. TopicGrouper

**Responsibility**: Identify related transcripts and group them for consolidation.

**Interface**:
```python
class TopicGrouper:
    def group_transcripts(self, transcripts: List[Transcript]) -> List[TopicGroup]
    def detect_continuation_pattern(self, title: str) -> Optional[str]
    def calculate_similarity(self, title1: str, title2: str) -> float
```

**TopicGroup Data Structure**:
```python
@dataclass
class TopicGroup:
    group_name: str
    transcripts: List[Transcript]
    should_consolidate: bool
```

**Grouping Logic**:
- Detect patterns: "Part 1/Part 2", "Basics/Implementation", "Introduction/Advanced"
- Calculate title similarity using string matching
- Group transcripts with similarity > 0.7 or explicit continuation patterns
- Keep clearly different topics separate

### 3. HealthcareClassifier

**Responsibility**: Classify content by healthcare applicability and adapt examples.

**Interface**:
```python
class HealthcareClassifier:
    def classify(self, topic_group: TopicGroup) -> HealthcareClassification
    def adapt_examples(self, content: str) -> str
    def determine_architecture_fit(self, content: str) -> List[str]
```

**HealthcareClassification Data Structure**:
```python
@dataclass
class HealthcareClassification:
    category: str  # "Healthcare-Applicable", "General Technical", "Domain-Neutral"
    adapted_content: str
    architecture_layers: List[str]  # e.g., ["RAG", "AI Agent", "Security"]
    use_hc_prefix: bool
```

**Classification Rules**:
- Healthcare-Applicable: Content directly relevant to patient data, medical workflows, healthcare AI
- General Technical: Broadly applicable technical concepts (e.g., graph structures, state management)
- Domain-Neutral: Generic programming concepts

### 4. MarkdownGenerator

**Responsibility**: Format content into structured Markdown documents and write to disk.

**Interface**:
```python
class MarkdownGenerator:
    def __init__(self, output_directory: str)
    def generate_document(self, topic_group: TopicGroup, classification: HealthcareClassification) -> str
    def create_filename(self, topic_group: TopicGroup, classification: HealthcareClassification) -> str
    def write_to_file(self, filename: str, content: str) -> None
```

**Document Structure**:
```markdown
# [Topic Name]

## Simple Explanation
[Beginner-friendly explanation]

## Why It Matters
[Interview relevance + real-world application]

## Very Simple Example
[Healthcare example if applicable, otherwise neutral]

## Step-by-Step Workflow
[Numbered steps with Mermaid diagram]

## Where It Fits
[Only for healthcare-applicable topics]
```

### 5. DocumentationPipeline (Orchestrator)

**Responsibility**: Coordinate the entire processing pipeline.

**Interface**:
```python
class DocumentationPipeline:
    def __init__(self, input_dir: str, output_dir: str)
    def run(self) -> ProcessingReport
    def log_progress(self, message: str) -> None
```

**ProcessingReport Data Structure**:
```python
@dataclass
class ProcessingReport:
    total_transcripts_processed: int
    total_files_generated: int
    errors: List[str]
    warnings: List[str]
    file_mappings: Dict[str, List[str]]  # output_file -> source_transcripts
```

## Data Models

### Core Data Structures

```python
@dataclass
class Transcript:
    filename: str
    title: str
    content: str
    raw_path: str

@dataclass
class TopicGroup:
    group_name: str
    transcripts: List[Transcript]
    should_consolidate: bool

@dataclass
class HealthcareClassification:
    category: str
    adapted_content: str
    architecture_layers: List[str]
    use_hc_prefix: bool

@dataclass
class ProcessingReport:
    total_transcripts_processed: int
    total_files_generated: int
    errors: List[str]
    warnings: List[str]
    file_mappings: Dict[str, List[str]]
```

### Filename Generation Rules

```python
def create_filename(topic_name: str, use_hc_prefix: bool) -> str:
    # 1. Convert to lowercase
    # 2. Replace spaces with underscores
    # 3. Remove special characters except underscores and hyphens
    # 4. Add HC_ prefix if healthcare-applicable
    # 5. Add .md extension
    pass
```

## Correctness Properties

*A property is a characteristic or behavior that should hold true across all valid executions of a system—essentially, a formal statement about what the system should do. Properties serve as the bridge between human-readable specifications and machine-verifiable correctness guarantees.*


### Property 1: Complete File Discovery
*For any* directory containing transcript files, the parser should discover and return all readable text files in that directory.
**Validates: Requirements 1.1**

### Property 2: Title and Content Extraction
*For any* valid transcript file, parsing should extract both a title and content, where the content is non-empty.
**Validates: Requirements 1.2**

### Property 3: Error Resilience
*For any* set of files containing at least one unreadable file, the parser should log the error and successfully process all remaining readable files.
**Validates: Requirements 1.3**

### Property 4: Accurate File Count
*For any* set of input files, the reported count of successfully parsed transcripts should equal the actual number of files that were successfully parsed.
**Validates: Requirements 1.4**

### Property 5: Continuation Pattern Detection
*For any* transcript title containing continuation patterns (e.g., "Part 1", "Part 2", "Basics", "Implementation"), the Topic Grouper should correctly identify and extract the pattern.
**Validates: Requirements 2.1**

### Property 6: Related Topic Consolidation
*For any* set of transcripts where at least two share related topics (high similarity or continuation patterns), the output should contain fewer documents than the input, indicating consolidation occurred.
**Validates: Requirements 2.2**

### Property 7: Unrelated Topic Separation
*For any* set of transcripts with clearly different topics (low similarity, no continuation patterns), each transcript should produce a separate output document.
**Validates: Requirements 2.3**

### Property 8: Similarity-Based Grouping
*For any* pair of transcripts with title similarity above the threshold (0.7), they should be grouped together in the same TopicGroup.
**Validates: Requirements 2.4**

### Property 9: Valid Classification Category
*For any* processed transcript, the Healthcare Classifier should return exactly one of the three valid categories: "Healthcare-Applicable", "General Technical", or "Domain-Neutral".
**Validates: Requirements 3.1**

### Property 10: Healthcare Example Adaptation
*For any* content classified as Healthcare-Applicable, the adapted content should contain healthcare-specific terminology or examples not present in the original.
**Validates: Requirements 3.2**

### Property 11: Healthcare Filename Prefix
*For any* content classified as Healthcare-Applicable, the generated filename should start with "HC_".
**Validates: Requirements 3.3**

### Property 12: No Forced Healthcare Mapping
*For any* content classified as General Technical or Domain-Neutral, the adapted content should be identical or nearly identical to the original content.
**Validates: Requirements 3.4**

### Property 13: Filename Sanitization
*For any* topic name, the generated filename should have spaces converted to underscores and all special characters removed except underscores and hyphens.
**Validates: Requirements 4.1, 4.2**

### Property 14: Consolidated Filename Descriptiveness
*For any* consolidated topic group containing multiple transcripts, the generated filename should contain elements from at least two of the source transcript titles.
**Validates: Requirements 4.4**

### Property 15: Output Directory Consistency
*For any* generated file, its full path should be within the specified output directory.
**Validates: Requirements 4.5**

### Property 16: Required Markdown Sections
*For any* generated document, it should contain all required sections: Topic Name, Simple Explanation, Why It Matters, Very Simple Example, and Step-by-Step Workflow.
**Validates: Requirements 5.1, 5.2, 5.3, 5.4, 5.5**

### Property 17: Mermaid Diagram Presence
*For any* generated document, the Step-by-Step Workflow section should contain at least one Mermaid code block (```mermaid).
**Validates: Requirements 5.5**

### Property 18: Conditional Where It Fits Section
*For any* generated document, the "Where It Fits" section should be present if and only if the content is classified as Healthcare-Applicable.
**Validates: Requirements 5.6**

### Property 19: Content Fidelity
*For any* generated document, all substantive content (explanations, examples, workflows) should be derivable from the source transcript content, with no fabricated information.
**Validates: Requirements 7.1**

### Property 20: Graceful Missing Content Handling
*For any* transcript lacking sufficient information for a required section, the generated document should either use a placeholder or omit the section rather than fabricate content.
**Validates: Requirements 7.2**

### Property 21: Consolidation Logging
*For any* consolidation decision made by the Topic Grouper, there should be a corresponding log entry documenting which transcripts were grouped and why.
**Validates: Requirements 8.4**

### Property 22: File Generation Display
*For any* generated file, the system should display the folder path, filename, and full Markdown content during processing.
**Validates: Requirements 9.1, 9.2, 9.3**

### Property 23: Accurate Generation Count
*For any* processing run, the reported number of files generated should equal the actual number of files written to the output directory.
**Validates: Requirements 9.4**

### Property 24: Error Reporting Completeness
*For any* processing run where errors occurred, all errors should be included in the final processing report.
**Validates: Requirements 9.5**

### Property 25: Windows Path Handling
*For any* file operation on Windows, the system should correctly use backslash separators and handle Windows-specific path conventions.
**Validates: Requirements 10.1**

### Property 26: Output Directory Creation
*For any* specified output directory that does not exist, the system should create it before attempting to write files.
**Validates: Requirements 10.4**

### Property 27: UTF-8 Encoding
*For any* written file, it should be encoded in UTF-8 to support all characters including special characters and non-ASCII text.
**Validates: Requirements 10.5**

## Error Handling

### File System Errors

**Unreadable Input Files**:
- Log error with filename and reason
- Continue processing remaining files
- Include in error report

**Missing Output Directory**:
- Attempt to create directory
- If creation fails, log error and abort
- Report error to user

**Write Permission Errors**:
- Log error with filename and reason
- Continue processing remaining files
- Include in error report

### Content Processing Errors

**Malformed Transcript Structure**:
- Log warning with filename
- Attempt best-effort parsing
- Mark in processing report

**Empty or Invalid Content**:
- Log warning with filename
- Skip file or use placeholder
- Include in warnings report

**Classification Failures**:
- Default to "Domain-Neutral" category
- Log warning
- Continue processing

### Grouping Errors

**Ambiguous Grouping**:
- Log decision rationale
- Prefer separation over incorrect consolidation
- Include in processing report

**Title Parsing Failures**:
- Use filename as fallback title
- Log warning
- Continue processing

## Testing Strategy

### Dual Testing Approach

The testing strategy employs both unit tests and property-based tests to ensure comprehensive coverage:

- **Unit tests**: Verify specific examples, edge cases, and error conditions
- **Property tests**: Verify universal properties across all inputs

Together, these approaches provide comprehensive coverage where unit tests catch concrete bugs and property tests verify general correctness.

### Property-Based Testing

**Framework**: Use `hypothesis` library for Python property-based testing

**Configuration**:
- Minimum 100 iterations per property test
- Each test tagged with: `# Feature: transcript-documentation-generator, Property N: [property text]`
- Each correctness property implemented by a SINGLE property-based test

**Test Generators**:
- Random transcript content with varying structures
- Random filenames with various special characters
- Random title patterns (with and without continuation markers)
- Random directory structures
- Edge cases: empty files, very long content, special characters

### Unit Testing

**Focus Areas**:
- Specific examples of continuation patterns ("Part 1", "Basics")
- Edge cases: empty directories, single file, all files unreadable
- Integration between components
- Specific healthcare classification examples
- Markdown formatting with known inputs

**Test Organization**:
```
tests/
  test_parser.py          # TranscriptParser unit and property tests
  test_grouper.py         # TopicGrouper unit and property tests
  test_classifier.py      # HealthcareClassifier unit and property tests
  test_markdown.py        # MarkdownGenerator unit and property tests
  test_pipeline.py        # End-to-end integration tests
  test_properties.py      # All property-based tests
```

### Test Data

**Fixtures**:
- Sample transcript files with known structure
- Test directories with various file configurations
- Expected output templates
- Healthcare and non-healthcare content samples

**Mocking**:
- File system operations for error simulation
- LLM calls for healthcare classification (if using AI)
- Path operations for cross-platform testing

## Implementation Notes

### Technology Stack

- **Language**: Python 3.9+
- **File I/O**: `pathlib` for cross-platform path handling
- **String Matching**: `difflib` for title similarity
- **Markdown Generation**: String templates (no external library needed)
- **Testing**: `pytest` + `hypothesis`

### Performance Considerations

- Process files sequentially (22 files is small enough)
- Cache parsed transcripts in memory
- Lazy evaluation not necessary for this scale
- Expected runtime: < 10 seconds for all 22 files

### Extensibility

- Easy to add new classification categories
- Template-based markdown generation allows format changes
- Pluggable grouping strategies
- Configurable similarity thresholds

### Configuration

Expose these as command-line arguments or config file:
- Input directory path
- Output directory path
- Similarity threshold for grouping (default: 0.7)
- Healthcare classification mode (manual, keyword-based, or AI-assisted)
- Verbosity level for logging
