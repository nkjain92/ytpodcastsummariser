Below is an updated, refined Product Requirements Document (PRD) incorporating latest details.

Podcast Summarizer Tool – Product Requirements Document (PRD)

1. Overview

Purpose:
Develop a locally running tool for personal use that automatically downloads podcast episodes (via RSS feeds or YouTube video links), transcribes the audio using Groq's speech-to-text API, and generates a summary using Groq's Llama 3.3 70B Versatile 128k model. The summaries (with episode metadata) will be displayed on a web-based dashboard.

Primary Use Case:
For users (initially yourself) who want to keep up with podcasts without listening to full episodes. The tool will let you add either an RSS feed URL or a one-off YouTube link. For YouTube links, the system will automatically extract the channel ID from the provided URL, locate the associated RSS feed (via page source lookup), and display the latest three summaries from that channel as well as the summary for the added video.

2. Key Features & Scope

A. Subscription & One-Off Content Addition
• Input Options:
• Home Tab:
• A prominent input field to add either an RSS feed link or a YouTube video link.
• When a YouTube video link is added, the tool should automatically:
• Extract the channel ID.
• Retrieve the channel's RSS feed by parsing the page source.
• Display Summaries of 3 latest videos:
• The Home page will display the latest three summaries from any newly added RSS feed or channel, along with the summary of the one-off video added.

B. Audio Processing and Summarization
• Audio Downloading:
• Download the audio file from each new podcast episode (youtube video or rss link)
• Use yt-dlp for YouTube downloads
• Direct download from enclosure URL for podcast RSS feeds
• Keep track of failed downloads in a dedicated "Failed Downloads" tab
• Log errors and provide retry options for failed downloads

• Handling Transcription:
• Use Groq's distil-whisper-large-v3-en model (optimized for English content)
• For files larger than 25 MB, implement audio chunking:
• Convert to FLAC format (16KHz mono)
• Break audio into chunks with overlap handling
• Process chunks sequentially (no parallel processing)
• Combine results using longest common sequence algorithm
• Show progress indicator during transcription process

• Summarization:
• Use Groq's Llama 3.3 70B Versatile 128k model
• Allow user to select summary length:
• 200 words
• 300 words
• 400 words
• 500 words
• Display summary on card with expandable view

C. Dashboard & Data Display
• Navigation (Left-hand Pane):

1. Home Tab:
   • Input field for RSS or YouTube link addition
   • Summary length selector (200/300/400/500 words)
   • Progress indicator for ongoing transcriptions
   • Display latest episodes' summaries in card format:

   - Show selected length summary in preview
   - Click to expand for full metadata view
   - Metadata includes: title, publication date, description, original link

2. RSS Feeds Tab:
   • List view of all subscribed feeds with:

   - Feed name
   - Status (active, error)
   - Last successful update
     • Add/delete feed options
     • Feed error details when applicable

3. Past Summaries Tab:
   • Chronological list of all processed summaries
   • Filter options by feed/channel
   • Search functionality
   • Export options (JSON, TXT)

4. Failed Downloads Tab:
   • List of failed download attempts with:
   - Source (RSS/YouTube)
   - Error details
   - Timestamp
   - Retry button
     • Detailed error logs viewer
     • Bulk retry option

• Data Persistence:
• Use Supabase (PostgreSQL) for storing: - Subscription details - Episode metadata - Transcripts and summaries - Error logs and failed download records

D. Processing and Reliability
• Audio Processing Pipeline:
• Preprocessing: - Convert to FLAC format (16KHz mono) - Implement chunking for files >25MB using provided sample code - Handle chunk overlap and reassembly
• Progress Tracking: - Show current chunk being processed - Display overall progress percentage - Estimated time remaining

• RSS Polling:
• Daily polling of RSS feeds
• Smart retry mechanism: - Exponential backoff for failed attempts - Maximum of 3 retries per feed - Error notification after final retry

• Error Handling:
• Comprehensive error logging: - Download failures - Transcription errors - Summarization issues
• User-friendly error messages
• Automatic retry for transient failures
• Manual retry option for persistent failures

E. Technical Implementation Details
• Audio Processing:
• Use yt-dlp for YouTube downloads: - Extract audio only - Handle age restrictions and region blocks - Support various video qualities
• Direct download for podcast RSS: - Handle various audio formats - Support authentication if needed - Validate file integrity

• Transcription Pipeline:
• Use distil-whisper-large-v3-en model
• Sequential chunk processing: - No parallel processing - Optimal chunk size for 50-100 MB files - Progress tracking per chunk
• Result combination: - Use longest common sequence algorithm - Handle overlapping segments - Maintain timing information

• Summary Generation:
• Use Llama 3.3 70B Versatile 128k model
• Configurable summary lengths: - Support 200/300/400/500 word options - Maintain key points regardless of length - Preserve important metadata

F. Future Enhancements (Planned for Later)
• Notification System:
• Email notifications
• WhatsApp integration
• Mobile push notifications
• Multi-user Support:
• Simple email registration
• User preferences storage
• Individual feed management
• Advanced Features:
• Custom summary templates
• Batch processing
• Advanced search and filtering

3. User Stories
   • Subscription & Content Addition:
   • As a user, I want to add an RSS feed or YouTube video link directly from the Home page, so I can subscribe to a podcast or get a one-off transcription/summarization without needing to log in.
   • As a user, when I add a YouTube video link, I want the system to automatically find the corresponding RSS feed by extracting the channel ID and parsing the page source.
   • Viewing Summaries:
   • As a user, I want to see the latest three summaries on the Home page in 50-word preview cards so that I can quickly decide which content interests me.
   • As a user, clicking on a summary card should reveal the complete summary along with episode metadata and direct links.
   • Managing Subscriptions:
   • As a user, I want to view and manage my subscribed RSS feeds (add or delete) on a dedicated "RSS Feeds" page so that I can control my subscriptions.
   • As a user, I want a "Past Summaries" section that lists all previously processed summaries for reference.
   • Processing Reliability:
   • As a user, I want the tool to reliably process every episode—handling large files by chunking when needed—so that no episode is skipped or left unprocessed.

4. Technical Architecture & Tech Stack

A. Frontend
• Framework: Next.js
• Design Libraries: Tailwind CSS with shadcn components for a clean, modern look
• Pages & Components:
• Home Page:
• Input field for RSS/YouTube link.
• Display area for latest summary cards.
• RSS Feeds Page:
• List view with add/delete functionality.
• Past Summaries Page:
• Chronological list of all summaries.
• Detail View Component:
• Expands a summary card to show full details when clicked.

B. Backend (Local)
• Language & Framework:
• Likely Python (with FastAPI or Flask) or Node.js (if preferred) running locally.
• RSS Parsing:
• Use a library such as Python's feedparser or a Node.js equivalent.
• Audio Processing:
• Use yt-dlp for YouTube audio extraction
• Direct download for podcast RSS enclosures
• FFmpeg for audio preprocessing
• Implementation of audio chunking based on provided sample code
• Transcription:
• Use distil-whisper-large-v3-en model
• Sequential chunk processing
• Chunk size optimization for 50-100 MB files
• Error Handling:
• Detailed logging of failed downloads
• Retry mechanism with error tracking
• Error notification system
• Integration with Groq API:
• Speech-to-Text:
• Use Groq API endpoints for transcription (handling file chunking for files larger than 25 MB).
• Summarization:
• Use Groq's Llama 3.3 70B Versatile 128k API for generating summaries.
• Data Storage:
• Supabase (PostgreSQL) for subscriptions, episode metadata, transcripts, and summaries.
• Local Environment:
• The entire system will run on a local machine, eliminating the need for cloud hosting for now.

C. Handling Larger Files (Groq API)
• File Size Limit:
• Groq API accepts a maximum of 25 MB per file.
• For Files >25 MB:
• Implement audio chunking:
• Break the audio into smaller overlapping segments.
• Process each segment individually via the Groq transcription endpoint.
• Combine the transcriptions while managing overlapping text.
• Preprocessing Recommendation:
• Use FFmpeg to downsample and convert to FLAC:

ffmpeg -i <input_file> -ar 16000 -ac 1 -map 0:a -c:a flac <output_file>.flac

5. User Flows

A. Onboarding & Content Addition Flow 1. Home Page:
• Step 1: User enters an RSS feed URL or YouTube video link in the input field.
• Step 2:
• If a YouTube link is provided, the system extracts the channel ID, accesses the page source, and retrieves the RSS feed.
• The system validates and adds the feed/video.
• Step 3: Display a confirmation and show the latest three summaries (for the feed/channel and the one-off video). 2. RSS Feeds Page:
• Step 1: List all currently subscribed feeds.
• Step 2:
• Provide an interface to add new feeds or delete existing ones.
• Display status (active or error) for each feed. 3. Past Summaries Page:
• Step 1: Show all previously processed summaries in reverse chronological order.
• Step 2: Allow the user to click on any summary for detailed view.

B. Processing Flow (Scheduled Daily) 1. RSS Polling:
• The system polls all subscribed RSS feeds once a day. 2. Episode Processing:
• For each new episode:
• Download Audio: Retrieve the audio file.
• Transcription:
• If the file is ≤25 MB: send it directly to Groq API.
• If >25 MB: chunk the file into segments and transcribe each.
• Summarization: Process the transcript using Groq's Llama 3.3 70B Versatile 128k API.
• Storage: Save the metadata, transcript, and summary in Supabase. 3. Dashboard Update:
• Newly processed summaries are updated on the Home and Past Summaries pages.

C. Error Handling Flow
• Error Detection:
• If an RSS feed fails to load or processing fails (audio download, transcription, or summarization), mark the feed status as "error."
• Retries & Logging:
• Automatically retry failed tasks with exponential back-off.
• Log errors locally for debugging.

6. UI/UX Design Considerations

Layout and Navigation
• Left-hand Navigation Pane: 1. Home Tab:
• Contains an input field for adding an RSS feed or YouTube link.
• Below the input, displays the latest episode summaries in 50-word cards.
• Clicking a card navigates to a full-detail view. 2. RSS Feeds Tab:
• Displays a list of all subscribed feeds with status indicators.
• Options to add new feeds or delete existing ones. 3. Past Summaries Tab:
• Lists all previously processed summaries in reverse chronological order.

Design & Styling
• Frontend Framework: Next.js
• Styling Libraries: Tailwind CSS with shadcn for components
• Summary Cards:
• Each card shows a 50-word preview, episode metadata (title, date, description), and a direct link.
• A click expands the card to show the complete summary.
• Responsive & Minimalistic:
• Prioritize readability, clean lines, and an intuitive navigation experience.

Prototype Prompt for UI/UX

You can share the following prompt with a UI prototyping platform (e.g., Figma, Sketch, or Adobe XD):

    "Design a modern, minimalistic dashboard for a local podcast summarizer tool. The layout should feature a left-hand navigation pane with three tabs: 'Home', 'RSS Feeds', and 'Past Summaries'.

- The Home tab should include a prominent input area for users to add an RSS feed or YouTube video link, and display the latest episode summaries in cards (each card showing a 50-word preview). Clicking a card should expand it to reveal the complete summary and episode metadata.
- The RSS Feeds tab should list all subscribed feeds with status indicators (active or error), and provide options to add or delete feeds.
- The Past Summaries tab should chronologically list all processed summaries for easy reference.
- Use Next.js for the frontend, styled with Tailwind CSS and shadcn components, ensuring a responsive and user-friendly design. Suggest a clean color scheme and modern typography that emphasize clarity and ease of navigation."

7. Open Questions & Clarifications

1. Backend Language Preference:
   • Python with FastAPI is recommended for:

   - Strong audio processing libraries
   - Excellent FFmpeg integration
   - Native support for the provided audio chunking code
   - Better async handling for long-running tasks

1. YouTube RSS Feed Extraction:
   • Implementation approach:

   - Extract channel ID from video URL using regex
   - Generate RSS feed URL using format:
     `https://www.youtube.com/feeds/videos.xml?channel_id=<CHANNEL_ID>`
   - Fallback to page source parsing if direct RSS URL fails

1. Audio Processing Details:
   • Using provided audio chunking implementation with:

   - FFmpeg for preprocessing
   - PyDub for chunk management
   - Sequential processing to maintain reliability
     • Chunk size optimization:
   - Default 10-minute chunks (600 seconds)
   - 10-second overlap between chunks
   - Adjustable based on file size

1. Progress Tracking Requirements:
   • Detailed progress indicators for:

   - Download progress
   - Transcription progress (per chunk)
   - Summarization progress
     • Estimated time remaining calculations
     • Status updates in UI

1. Error Handling Strategy:
   • Implement comprehensive error tracking:

   - Network errors during downloads
   - API failures (Groq endpoints)
   - File processing errors
     • Maintain detailed error logs with:
   - Timestamp
   - Error type
   - Context information
   - Stack trace when applicable

1. Implementation Phases

Phase 1: Core Infrastructure
• Set up local development environment
• Implement basic audio processing pipeline
• Establish database schema and connections
• Create basic UI framework

Phase 2: Processing Pipeline
• Implement audio download functionality
• Set up transcription pipeline with chunking
• Develop summarization workflow
• Add progress tracking

Phase 3: User Interface
• Build dashboard components
• Implement summary cards and views
• Add error handling and retry mechanisms
• Create settings and configuration options

Phase 4: Testing & Refinement
• Comprehensive testing of all components
• Performance optimization
• Error handling improvements
• User feedback incorporation

Phase 5: Documentation & Deployment
• Create comprehensive API documentation
• Write deployment guide
• Document database schema
• Create troubleshooting guide
• Write user manual

Phase 6: Monitoring & Maintenance
• Set up error monitoring
• Implement usage analytics
• Create backup strategy
• Establish update procedure

10. Security Considerations

A. API Security
• Store API keys securely in environment variables
• Implement rate limiting for API endpoints
• Validate all input data
• Sanitize file uploads

B. Data Security
• Encrypt sensitive data at rest
• Implement secure file handling
• Regular database backups
• Secure local storage management

C. Error Handling Security
• Sanitize error messages
• Log sensitive information securely
• Implement proper access controls
• Handle failed authentications

11. Performance Optimization

A. Resource Management
• Implement cleanup for temporary files
• Optimize database queries
• Cache frequently accessed data
• Monitor memory usage

B. Processing Optimization
• Optimize chunk size based on file type
• Implement smart polling intervals
• Use efficient file formats
• Optimize database indexes

Looking forward to your feedback so we can refine and move forward with the implementation!

D. File Storage Structure

```
project_root/
├── data/
│   ├── downloads/         # Temporary audio downloads
│   ├── transcriptions/    # Processed transcriptions
│   └── logs/             # Application logs
├── config/
│   └── .env              # Environment variables
└── src/
    ├── frontend/         # Next.js frontend
    ├── backend/          # FastAPI backend
    └── scripts/          # Utility scripts
```

E. API Rate Limiting & Quotas
• Groq API:

- Track daily API usage
- Implement request throttling
- Monitor costs and usage
  • Local API:
- Rate limit: 100 requests/minute
- Concurrent downloads: 2
- Max file size: 500MB

F. Error Recovery Procedures
• Download Failures:

- Retry with exponential backoff
- Max 3 retries per file
- Alternative download methods as fallback

• Transcription Failures:

- Retry individual chunks
- Adjust chunk size if needed
- Save partial results

• Database Failures:

- Local file backup
- Transaction rollback
- Automatic reconnection

G. Monitoring & Logging
• Application Metrics:

- CPU/Memory usage
- Processing times
- Success/failure rates
- API call statistics

• Log Levels:

- ERROR: Processing failures
- WARN: Retries and degraded operations
- INFO: Normal operations
- DEBUG: Detailed processing info

12. Testing Strategy

A. Unit Tests
• Components:

- Audio processing functions
- Transcription pipeline
- Summary generation
- Database operations

B. Integration Tests
• End-to-end workflows:

- RSS feed processing
- YouTube video processing
- Error handling scenarios
- Database interactions

C. Performance Tests
• Benchmarks:

- Processing times
- Memory usage
- Database performance
- API response times

## 2. CrewAI Orchestration

### Agent Structure

1. **Download Agent**

- Role: Audio Content Retriever
- Tools: yt-dlp, RSS parser
- Tasks:
  - Download audio from YouTube/RSS
  - Extract metadata
  - Handle file management

2. **Transcription Agent**

- Role: Audio Processor
- Tools: FFmpeg, Groq STT API
- Tasks:
  - Preprocess audio (16kHz mono FLAC)
  - Chunk files > 25MB
  - Manage transcription pipeline
  - Merge transcription chunks

3. **Summarization Agent**

- Role: Content Analyzer
- Tools: Groq Chat API (Llama 3.3 70B)
- Tasks:
  - Generate summaries of transcripts
  - Extract key points
  - Format output for display

4. **Manager Agent**

- Role: Workflow Coordinator
- Tasks:
  - Orchestrate agent interactions
  - Handle error recovery
  - Track progress
  - Manage state

### Process Flow

```python
from crewai import Agent, Task, Crew, Process
from crewai.tasks import Task

# Define agents with specific roles and tools
download_agent = Agent(
    role="Audio Content Retriever",
    goal="Download and prepare audio content",
    backstory="Expert at retrieving audio from various sources",
    tools=[YoutubeDLTool(), RSSParserTool()]
)

transcription_agent = Agent(
    role="Audio Processor",
    goal="Convert audio to accurate transcripts",
    backstory="Specialist in audio processing and transcription",
    tools=[FFmpegTool(), GroqTranscriptionTool()]
)

summarization_agent = Agent(
    role="Content Analyzer",
    goal="Create concise, informative summaries",
    backstory="Expert at distilling key information",
    tools=[GroqChatTool()]
)

# Create tasks with dependencies
download_task = Task(
    description="Download and prepare audio file",
    agent=download_agent
)

transcribe_task = Task(
    description="Transcribe audio content",
    agent=transcription_agent,
    context=[download_task]
)

summarize_task = Task(
    description="Generate summary from transcript",
    agent=summarization_agent,
    context=[transcribe_task]
)

# Create crew with hierarchical process
crew = Crew(
    agents=[download_agent, transcription_agent, summarization_agent],
    tasks=[download_task, transcribe_task, summarize_task],
    process=Process.hierarchical,
    manager_llm=ChatOpenAI(temperature=0, model="gpt-4")
)
```

## 3. Technical Implementation

### Audio Processing Pipeline

1. **Download Phase**

- Use yt-dlp for YouTube
- Direct download for RSS enclosures
- Extract metadata
- Validate file integrity

2. **Preprocessing**

- Convert to 16kHz mono FLAC using FFmpeg
- Implementation from sample code:

```python
def preprocess_audio(input_path: Path) -> Path:
    """Preprocess audio file to 16kHz mono FLAC"""
    subprocess.run([
        'ffmpeg',
        '-i', input_path,
        '-ar', '16000',
        '-ac', '1',
        '-c:a', 'flac',
        output_path
    ])
```

3. **Chunking (for files >25MB)**

- Use provided chunking implementation
- 600-second chunks with 10-second overlap
- Sequential processing
- Merge using longest common sequence algorithm

4. **Transcription**

- Use Groq's distil-whisper-large-v3-en model
- Handle rate limits and retries
- Merge transcription chunks
- Validate output

5. **Summarization**

- Use Llama 3.3 70B Versatile
- Support multiple summary lengths
- Extract key points
- Format for display

### Frontend Dashboard

1. **Home Tab**

- Input field for RSS/YouTube links
- Summary length selector
- Progress indicators
- Latest episode cards

2. **RSS Feeds Tab**

- Feed management
- Status monitoring
- Error handling

3. **Past Summaries Tab**

- Chronological list
- Search/filter options
- Export functionality

4. **Failed Downloads Tab**

- Error tracking
- Retry options
- Detailed logs

### Data Storage

- Use Supabase (PostgreSQL) for:
  - Subscription details
  - Episode metadata
  - Transcripts/summaries
  - Error logs

## 4. Error Handling & Reliability

### Retry Mechanism

- Exponential backoff
- Maximum 3 retries
- Error notification
- State persistence

### Progress Tracking

- Download status
- Transcription progress
- Chunk processing
- Summary generation

### Resource Management

- Cleanup temporary files
- Monitor API usage
- Handle rate limits
- Database optimization

## 5. Implementation Phases

1. **Core Infrastructure** (Week 1-2)

- Set up CrewAI agents
- Implement basic pipeline
- Database schema

2. **Processing Pipeline** (Week 2-3)

- Audio download
- Transcription
- Summarization
- Progress tracking

3. **User Interface** (Week 3-4)

- Dashboard components
- Error handling
- Settings/configuration

4. **Testing & Refinement** (Week 4)

- Integration testing
- Performance optimization
- User feedback

## 6. Technical Requirements

### API Integration

- Groq API key management
- Rate limit handling
- Error recovery

### File Storage

```
project_root/
├── data/
│   ├── downloads/     # Temporary audio
│   ├── transcripts/   # Processed text
│   └── logs/         # System logs
├── config/
│   └── .env         # Environment vars
└── src/
    ├── frontend/    # Next.js
    ├── backend/     # FastAPI
    └── agents/      # CrewAI agents
```

### Performance Targets

- Max file size: 500MB
- Concurrent downloads: 2
- Processing time: <10min for 1hr audio
- API rate limits: Respect Groq quotas

This refined PRD removes redundancies while maintaining critical information and incorporating CrewAI orchestration for better task management and reliability.
