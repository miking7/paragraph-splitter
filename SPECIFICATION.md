## Technical Implementation Details

### LLM Prompting Strategy
- Input: Numbered sentences (e.g., "1. First sentence. 2. Second sentence.")
- Prompt: "Identify which sentences should be grouped together into paragraphs. Return ONLY the paragraph structure in this format: 'P1: 1-4; P2: 5-9; ...' with no additional text."
- Expected output: "P1: 1-4; P2: 5-9; P3: 10-15"
- Section prompting:
  - Input: Numbered paragraphs (e.g., "P1: First paragraph. P2: Second paragraph.")
  - Prompt: "Analyze these paragraphs and identify logical sections with descriptive headings."
  - Expected output: "S1[Heading Title]: P1-P5; S2[Another Heading]: P6-P9; ..." or "S1[Heading]: P1; S2[Heading]: P2;"

### API Integration
- Default endpoint: OpenAI API compatible
- Configurable temperature (default: 0.1 for consistency)
- Model selection in settings (default: gpt-4o-mini)
- Direct fetch API calls from browser

### Error Handling
- API connection failures
- Invalid API key detection
- Response parsing issues
- Text size limitations## File Structure

```
paragraph-splitter.html  <-- The entire application in a single file
```

### HTML File Sections
1. **HTML structure** (Minimalist)
   - Input textarea
   - Process button
   - Output textarea
   - Copy button
   - Settings toggle and panel

2. **CSS section**
   - Tailwind CSS import via CDN
   - Minimal custom styles if needed

3. **Vue Template section**
   - Main UI template with input/output areas
   - Simple settings panel (hidden by default)

4. **JavaScript section**
   - Vue.js import via CDN
   - Vue application initialization
   - Text processing functions
   - OpenAI-compatible API integration
   - localStorage management for settings# Paragraph Segmentation SPA Specification (Minimal UI)

## Overview
A minimal Single Page Application in a single HTML file that efficiently segments unparagraphed transcription text into logical paragraphs using a large language model (LLM) with minimal token usage. Features a simple two-panel UI with input/output areas, utilizes OpenAI-compatible APIs, and stores settings in localStorage.

## Core Functionality

### 1. Text Input & Processing
- Simple text input area for pasting raw transcription
- Basic preprocessing:
  - Split text into sentences using string operations (based on punctuation)
  - Handle consecutive punctuation marks (like ellipses) as a single split point
  - Assign sequential numbers to each sentence
- Display statistics: sentence count, word count, and character count

### 2. Paragraph Segmentation
- Sentence Grouping Approach:
  - Send numbered sentences to LLM (OpenAI compatible API)
  - Request compact paragraph structure (e.g., "P1: 1-4; P2: 5-9") 
  - Reconstruct paragraphs based on this mapping
- Batch Processing for Large Texts:
  - Process text in batches of 50 sentences with 10-sentence overlap
  - Merge paragraph structures from multiple batches
  - Handle edge cases at batch boundaries

### 3. Section Identification
- Paragraph Grouping Approach:
  - Send paragraphs to LLM for section identification
  - Request section structure with headings (e.g., "S1[Introduction]: P1-P5")
  - Reconstruct text with section headings based on this mapping
- Batch Processing for Large Texts:
  - Process text in batches of 20 paragraphs
  - For each batch after the first, include paragraphs from the last section of the previous batch
  - This ensures continuity between sections across batch boundaries
  - Merge section structures from multiple batches
  - Handle overlapping sections intelligently

### 4. LLM Integration
- OpenAI-compatible API integration
- API endpoint and key stored in localStorage
- Simple prompt engineering for paragraph and section detection
- Optimized to minimize token usage

### 5. Output & Export
- Display segmented text with paragraph breaks in output area
- Display sectioned text with headings in a separate output area
- Show statistics: paragraph count, sentence count, section count, and word count
- One-click copy to clipboard functionality for both paragraphed and sectioned text
  - Cross-browser compatible clipboard implementation
  - Works in both secure (HTTPS) and non-secure (HTTP) contexts

## Technical Architecture

### Single HTML File Implementation
- Vue.js framework (CDN version) with all code in one HTML file
- Tailwind CSS (CDN version) for styling
- No build process or bundling required
- All components defined within the same HTML file
- All processing done directly in browser

### Vue Implementation
- Single Vue application defined in the HTML file
- Template section containing all UI elements
- Script section with Vue component logic
- No separate .vue files or build step

### Tailwind Implementation
- CDN-loaded Tailwind for zero-config styling
- Custom utility classes for text processing interface
- Responsive design for various screen sizes
- Consistent styling without separate CSS files

### LLM Integration
- Direct API calls to LLM service (OpenAI, Anthropic, etc.) from browser
- Client-side API key management (stored in localStorage)
- Rate limiting handled on client side

### Data Flow
1. User inputs raw text
2. Browser-side JavaScript splits text into sentences using string operations
3. Sentences are numbered and prepared for LLM
4. Direct API call to LLM service, requesting paragraph structure (not full text)
5. Browser reconstructs paragraphed text from LLM response
6. User reviews and can manually adjust
7. Final text is available for export

### Key Functions
- `splitSentences()`: Divide text using regular expressions, handling consecutive punctuation
- `preparePrompt()`: Format numbered sentences for LLM
- `callLLMService()`: Make API request to language model
- `parseParagraphStructure()`: Extract paragraph structure from LLM response
- `reconstructParagraphs()`: Apply LLM structure to original text
- `createBatches()`: Create batches of sentences or paragraphs for processing
- `adjustParagraphStructure()`: Adjust paragraph indices based on batch position
- `prepareSectionPrompt()`: Format paragraphs for section identification
- `parseSectionStructure()`: Extract section structure from LLM response, handling various formats including multi-paragraph ranges (P1-P5) and single-paragraph sections (P1)
- `reconstructSections()`: Apply section structure to paragraphed text
- `copyToClipboard()`: Copy formatted output to clipboard

## User Interface

### Main UI
- Input text area (for pasting raw transcription)
- "Split paragraphs" button
- Paragraphed text area (showing paragraphed result)
- "Add sections" button
- Sectioned text area (showing text with section headings)
- Copy to clipboard buttons for both output areas
- Settings toggle (to access API configuration)

### Settings Panel (Toggleable Overlay)
- Modal overlay with semi-transparent backdrop
- API endpoint input (OpenAI-compatible by default)
- API key input 
- Save settings button
- Settings automatically saved to localStorage
- Multiple dismissal methods:
  - Close button in top-right corner
  - Clicking outside the panel
  - Pressing the Escape key

### User Flow
1. User pastes transcription text into input area
2. User clicks "Split paragraphs" button
3. Application processes text and displays paragraphed result
4. User can copy paragraphed result or proceed to add sections
5. If desired, user clicks "Add sections" button
6. Application processes paragraphed text and displays sectioned result with headings
7. User copies final result with one click

## Implementation Roadmap

### Phase 1: Minimal Viable Product
- Single HTML file with Vue.js and Tailwind CSS via CDN
- Two text areas (input/output) and action buttons
- Basic settings panel for API configuration
- String-based sentence segmentation
- OpenAI API integration with minimal prompt
- localStorage for saving API settings
- Copy to clipboard functionality

### Phase 2: Refinements (Implemented)
- Error handling for API failures
- Statistics display (sentences, words, characters, paragraphs)
- Loading indicator during processing
- Basic mobile responsiveness
- Batch processing for large texts

### Phase 3: Section Identification (Implemented)
- Add section identification capability
- Three-panel UI (input, paragraphed output, sectioned output)
- Batch processing for section identification
- Intelligent handling of section boundaries between batches
- Section statistics display

### Phase 4: Optional Enhancements (If Needed)
- Dark/light mode toggle
- Processing history (last few conversions)
- Simple statistics on paragraph and section density
- Custom section heading formatting options

## Technical Requirements
- Modern web browser support
- Responsive design for desktop and tablet
- Local storage for text recovery and settings
- User-provided API key for LLM service
- No server requirements or installation needed

## Storage & Persistence

### localStorage Implementation
- API endpoint URL
- API key (securely stored)
- Model settings

### No Server-Side Requirements
- All persistence handled via browser localStorage
- No cookies required
- No backend storage needed
