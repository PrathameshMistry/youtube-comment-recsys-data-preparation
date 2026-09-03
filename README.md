

<img width="936" height="1681" alt="Data preparation architecture" src="https://github.com/user-attachments/assets/16ccf29c-8b78-46ad-9a66-16ac0499eeb6" />

# YouTube Comment Recommendation System — Data Preparation & Comment Detection

A complete data preparation and feature-generation pipeline for YouTube videos and comments.

The purpose of this project is to transform raw YouTube video and comment data into clean, structured and meaningful information that can be used for:

- Comment recommendation
- Audience understanding
- Comment prioritization
- Spam detection
- Toxicity and hate detection
- Viewer intent analysis
- Content and topic understanding
- Engagement analysis
- Trend and virality analysis
- Intelligent response systems

The pipeline is designed to prepare the data once and store the generated features so that downstream systems can use them without repeatedly performing the complete data-preparation process.

---

## Project Architecture

The complete pipeline follows this flow:

**Raw YouTube Data**
→ **Data Validation**
→ **Video & Comment Preparation**
→ **Cleaning & Normalization**
→ **Language Processing**
→ **Deduplication**
→ **Temporal & Context Features**
→ **Silver Feature Store**
→ **NLP Feature Generation**
→ **Business Features**
→ **Validation & Monitoring**
→ **Gold Feature Store**
→ **Model Ready Data**

---

## 1. Dependency Check

The pipeline first checks the required Python and machine-learning libraries.

Main libraries include:

- Pandas
- NumPy
- Scikit-learn
- PyArrow
- Lingua language detection
- Optional Transformer models
- Sentence Transformers

The pipeline can also operate with rule-based and lexicon-based fallbacks when large NLP models are not available.

---

# 2. Configuration

The pipeline supports configurable processing.

Important configuration options include:

- Run ID
- Backfill processing
- Incremental processing
- Velocity windows
- Model settings
- Feature version
- Schema version
- Pipeline version
- Data location
- Feature-store location

The current pipeline version is:

**Pipeline Version:** 1.1.0  
**Schema Version:** 1.1.0  
**Feature Version:** 1.1.0  
**Model Version:** 1.1.0

---

# 3. Input Data

The pipeline works with YouTube video and comment datasets.

### Video Data

Supports:

- US videos
- GB videos
- Video ID
- Title
- Channel
- Category
- Tags
- Views
- Likes
- Dislikes
- Comment count
- Trending date
- Publication information when available

### Comment Data

Supports:

- Comment text
- Comment ID
- Video ID
- Author ID when available
- Likes
- Replies
- Published time
- Updated time
- Parent comment ID
- Country

---

# 4. Video Data Preparation

Video data is prepared before being connected with comments.

The pipeline performs:

### Schema Unification
Supports different YouTube CSV formats and converts them into a common structure.

### Date Processing
Converts different date formats into a consistent timestamp.

### Category Mapping
Converts category IDs into readable category names.

### Daily Video Panel
Creates daily video-level information.

### Video Trend Features
Calculates changes in:

- Views
- Likes
- Dislikes
- Comment count

### Engagement Features

The pipeline calculates:

- Engagement rate
- Like ratio
- View changes
- Like changes
- Comment changes

### Multi-window Changes

Video activity can be examined over:

- 24 hours
- 3 days
- 7 days

---

# 5. Comment Data Ingestion

Raw comments are loaded and validated.

The pipeline:

- Reads comment CSV files
- Checks the expected structure
- Standardizes column names
- Converts numeric values
- Handles missing values
- Records the source file
- Stores country information
- Captures malformed rows

Malformed rows are placed into a **quarantine area** instead of being silently deleted.

This makes the data-preparation process auditable.

---

# 6. Comment Normalization

Every comment goes through a cleaning process.

The pipeline performs:

- Unicode normalization
- Control-character removal
- Whitespace normalization
- Line-break cleaning
- Text length control
- Missing-text handling

The original text is preserved as:

`text_raw`

The cleaned version is stored as:

`text_clean`

---

# 7. Structural Comment Features

The pipeline extracts useful information directly from the comment text.

Features include:

- Character length
- Word count
- Number of URLs
- Number of mentions
- Number of hashtags
- Number of timecodes
- Number of emojis
- Number of question marks
- Whether the comment ends with a question
- Repeated punctuation
- Elongated words
- Uppercase ratio
- Digit ratio
- Emoji ratio
- Script ratios

Supported script detection includes:

- Latin
- Devanagari
- Bengali
- Arabic
- CJK
- Cyrillic

---

# 8. Language Processing

The pipeline detects the language used in comments.

It generates:

- Language
- Language confidence
- Code-mixed language detection

It also contains special handling for romanized Indian-language comments such as:

- Hinglish
- Banglish

This helps the system understand comments that mix English with Indian-language words.

---

# 9. Comment Deduplication

Duplicate comments can create misleading results.

The pipeline identifies:

### Exact/Repeated Comments

Creates a unique comment identifier.

### Near-Duplicate Comments

Identifies comments that are highly similar after ignoring:

- Case
- Punctuation
- Emoji differences

### Copypasta Detection

Identifies repeated comment patterns appearing across multiple videos.

This can be useful for detecting repeated promotional or spam-like behavior.

---

# 10. Comment Quality Gate

Before advanced feature generation, comments pass through a quality gate.

The pipeline identifies:

- Empty/degenerate comments
- URL-only comments
- Low-quality comments

Comments are not simply deleted.

Instead, they receive quality flags so that downstream systems can decide how to use them.

---

# 11. Temporal Features

The pipeline tracks how comment engagement changes over time.

It generates:

### Like Velocity

- Like velocity — overall
- Like velocity over 1 hour
- Like velocity over 6 hours
- Like velocity over 24 hours

### Reply Velocity

- Reply velocity — overall
- Reply velocity over 1 hour
- Reply velocity over 6 hours
- Reply velocity over 24 hours

### Additional Features

- First likes
- Last likes
- Maximum likes
- Like delta
- Reply delta
- Growing comment detection
- Number of observations
- Observation time span

These features help identify comments that are gaining attention.

---

# 12. Context Features

The pipeline understands how a comment relates to other comments.

It generates:

- Is reply
- Is root comment
- Thread size
- Thread reply count
- Sibling count
- Comments per hour
- Video-hour burst ratio
- Burst detection
- Near-duplicate count
- Near-duplicate detection
- Copypasta video count
- Copypasta detection
- Likes percentile
- Reply percentile
- Comment-length percentile

These features provide context around the comment instead of looking at the comment text alone.

---

# 13. Silver Feature Store

After cleaning and validation, the pipeline creates a **Silver Feature Store**.

The Silver layer contains:

- Clean comments
- Validated comments
- Comment metadata
- Structural features
- Language information
- Deduplication information
- Temporal information
- Context information
- Video information

This becomes the foundation for advanced feature generation.

---

# NLP FEATURE GENERATION

The cleaned comments are then processed to generate semantic and business-level information.

---

# 14. Sentiment Detection

### Feature #1 — Sentiment

Determines whether the viewer's reaction is:

- Positive
- Negative
- Neutral

Also provides a sentiment score.

**Purpose:**

Understand whether viewers are happy, unhappy, or neutral about the content.

---

# 15. Emotion Detection

### Feature #2 — Emotion

Identifies the emotional reaction expressed in a comment.

The system also produces an emotion confidence score.

**Purpose:**

Understand the emotional response of viewers beyond simple positive/negative sentiment.

---

# 16. Topic Detection

### Feature #3 — Topic

Groups comments according to the subject they discuss.

The pipeline uses topic modelling with:

**K-Means**

The configured number of topics is:

**24 topics**

It also generates topic distance information.

**Purpose:**

Understand what viewers are talking about.

---

# 17. Intent Detection

### Feature #4 — Intent

Identifies what the viewer is trying to communicate.

Possible intent categories include:

- Question
- Complaint
- Opinion
- Praise
- Spam
- Feature request
- Other relevant intents

An intent confidence score is also generated.

**Purpose:**

Understand the reason behind a comment instead of only analyzing its words.

---

# 18. Feature Request Detection

### Feature #5 — Feature Request Probability

Detects comments where viewers request:

- New features
- New content
- Improvements
- Additional functionality

**Purpose:**

Find what viewers want the creator or product to add.

---

# 19. Pain Point Detection

### Feature #6 — Pain Point Probability

Identifies comments describing:

- Problems
- Bugs
- Frustration
- Difficulties
- Negative experiences

**Purpose:**

Find problems that need attention.

---

# 20. Question Detection

### Feature #7 — Question Detection

Determines whether a viewer is asking a question.

It considers:

- Question marks
- Question words
- Question-like sentence structure
- Whether the comment ends with a question

The system produces:

- `is_question`
- `question_prob`

**Purpose:**

Find questions that may require a response.

---

# 21. Spam Detection

### Feature #8 — Spam Probability

Identifies comments that may look like spam.

Signals include:

- URLs
- Spam phrases
- Excessive uppercase text
- Many mentions
- Repeated punctuation
- Excessive word elongation
- URL-only comments
- Copypasta patterns

The result is:

`spam_prob`

**Purpose:**

Help identify unwanted or promotional comments.

---

# 22. Toxicity Detection

### Feature #9 — Toxicity Probability

Detects potentially toxic or abusive language.

The pipeline can use a toxicity classification model or a rule/lexicon fallback.

The result is:

`toxicity_prob`

**Purpose:**

Support safer comment moderation and prioritization.

---

# 23. Hate / Offensive Detection

### Feature #10 — Hate Probability

Detects potentially hateful or offensive content.

The result is:

`hate_prob`

**Purpose:**

Help identify comments that may require moderation or review.

---

# BUSINESS FEATURES

After NLP analysis, the pipeline creates higher-level features that can help prioritize and understand comments.

---

# 24. Engagement Score

### Feature #11 — Engagement Score

Measures how much attention a comment receives.

It uses information such as:

- Likes
- Replies
- Like percentile
- Reply percentile
- Like velocity

**Purpose:**

Identify comments receiving significant viewer attention.

---

# 25. Quality Score

### Feature #12 — Quality Score

Estimates the overall usefulness and quality of a comment.

It considers:

- Comment length
- Whether the comment is meaningful
- Spam probability
- Toxicity probability
- URL presence

**Purpose:**

Separate potentially useful comments from low-quality comments.

---

# 26. Virality Score

### Feature #13 — Virality Score

Estimates whether a comment is gaining attention quickly.

It uses:

- Like growth
- Like velocity
- Reply velocity
- Growth status

**Purpose:**

Identify comments that are becoming popular.

---

# 27. Trend Score

### Feature #14 — Trend Score

Combines:

- Video trend information
- Comment virality

**Purpose:**

Identify comments connected with content that is gaining attention.

---

# 28. Response Priority

### Feature #15 — Response Priority

Determines which comments may deserve attention first.

The priority considers:

- Questions
- Pain points
- Feature requests
- Engagement
- Commercial intent
- Toxicity

The system creates response tiers:

### P0
Highest moderation priority.

### P1
High-priority response.

### P2
Medium-priority response.

### P3
Lower-priority response.

**Purpose:**

Help creators or systems decide which comments should be handled first.

---

# 29. Audience Need Score

### Feature #16 — Audience Need Score

Measures how strongly a comment represents an audience need.

It considers signals such as:

- Questions
- Pain points
- Feature requests
- Topic-level audience needs

**Purpose:**

Understand what viewers need from the creator or content.

---

# 30. Commercial Intent

### Feature #17 — Commercial Intent Probability

Detects possible commercial interest.

Examples include comments related to:

- Product pricing
- Buying
- Products
- Commercial questions

**Purpose:**

Identify viewers who may have buying or pricing-related interest.

---

# 31. Recommendation Intent

### Feature #18 — Recommendation Intent Probability

Detects comments that:

- Recommend something
- Ask for recommendations
- Suggest content

**Purpose:**

Understand recommendation-related conversations.

---

# 32. Language & Code Mixing

### Feature #19 — Language

The pipeline stores:

- Detected language
- Language confidence
- Code-mixed status

This helps the system work with multilingual and mixed-language comments.

---

# 33. Comment Identifier

### Feature #20 — Comment UID

Every processed comment receives a unique identifier:

`comment_uid`

This identifier is used for:

- Tracking comments
- Deduplication
- Incremental processing
- Feature-store updates
- Preventing duplicate records

---

# 34. Embedding Representation

The architecture also generates a **128-dimensional text embedding representation**.

The embedding provides a numerical representation of the meaning of a comment.

The pipeline can use:

- Sentence-transformer embeddings when models are enabled
- TF-IDF + SVD fallback when models are unavailable

The current documented run used:

**TF-IDF + SVD — 128 dimensions**

**Purpose:**

Allow downstream recommendation and similarity systems to compare comments based on their textual meaning.

---

# 35. Author Behaviour Features

When reliable `author_id` information is available, the pipeline can generate:

- Author comment count
- Author average likes
- Author toxicity rate
- Author spam rate
- Author duplicate rate
- Author prolific-user detection
- Author feature reliability

Author features are only trusted when author ID coverage reaches the configured threshold.

If coverage is insufficient, the author features are nulled instead of producing unreliable results.

---

# 36. Feature Assembly

The pipeline separates information into different groups.

### Raw Features

Original and cleaned comment/video information.

### ML Features

Features used for machine-learning and NLP systems.

### Business Features

Features designed for recommendation, prioritization, audience analysis and moderation.

### Embeddings

Numerical representations of comment meaning.

### Version Information

The system stores:

- Pipeline version
- Schema version
- Feature version
- Model version
- Model signature
- Gold run ID
- Gold ingestion timestamp

---

# 37. Validation & Monitoring

The final feature data is checked before being stored.

The pipeline monitors:

- Missing values
- Language confidence
- Sentiment confidence
- Emotion confidence
- Intent confidence
- Toxicity uncertainty
- Question uncertainty
- Topic distance
- Multilingual coverage
- Feature availability

This helps identify situations where the generated features may be unreliable.

---

# 38. Gold Feature Store

The final output is stored in the **Gold Feature Store**.

The Gold layer contains:

- NLP features
- Business features
- Embeddings
- Raw features
- ML features
- Recommendation-related features
- Moderation-related features
- Version information

The resulting data is ready for downstream machine-learning and recommendation systems.

---

# 39. Incremental Processing

The pipeline supports both:

### Backfill Mode

Processes the complete historical dataset.

### Incremental Mode

Processes new comments without unnecessarily processing the entire dataset again.

A timestamp + comment identifier watermark is used to determine which comments are new.

The feature store also uses idempotent writes to avoid creating duplicate records during repeated runs.

---

# 40. Data Quality & Auditability

The pipeline does not silently discard malformed data.

Malformed rows are captured in a quarantine area with information such as:

- Reason
- Source line number
- Source file
- Country
- Data type
- Ingestion run ID
- Raw row information

This makes the data pipeline easier to audit and debug.

---

# 41. Example Processing Result

The documented pipeline run processed:

- **1,409,852 raw comments**
- **834,772 deduplicated comments**
- **828,212 comments passed the quality gate**
- **20 malformed rows quarantined**

The pipeline also generated:

- **24 topic clusters**
- **128-dimensional text representations**
- Language detection
- Code-mixing detection
- Near-duplicate detection
- Copypasta detection
- Spam detection
- Toxicity detection
- Hate detection
- Sentiment
- Emotion
- Intent
- Engagement
- Quality
- Virality
- Trend
- Response priority
- Audience need
- Commercial intent
- Recommendation intent

The documented validation confirmed coverage for the 20 listed core feature outputs. :contentReference[oaicite:0]{index=0}

---

# 42. Important Note About the Architecture

The architecture diagram represents **embedding as #20**, while the validation feature map identifies **comment_uid as #20**.

For clarity:

- `comment_uid` = unique identifier used to track each comment
- `embedding` = 128-dimensional semantic representation used as an ML input

The two serve different purposes and are both retained in the feature store.

---

# 43. Final Goal

The final goal of this project is to convert raw YouTube comments into structured information that a recommendation, moderation, analytics or response system can understand.

Instead of treating a comment simply as:

> "This video is amazing!"

the system can understand multiple dimensions of that comment:

- What is the sentiment?
- What emotion is expressed?
- What topic is being discussed?
- What does the viewer want?
- Is the viewer asking a question?
- Is it a feature request?
- Is there a pain point?
- Is it spam?
- Is it toxic or hateful?
- How much engagement is it receiving?
- Is it becoming viral?
- Is it connected to a trending video?
- Does it represent an audience need?
- Does it have commercial intent?
- Is it a recommendation?
- What language is being used?
- Does it require a response?
- How should it be prioritized?

This transforms raw comments into **structured, actionable audience intelligence**.

---

## Technology

- Python
- Pandas
- NumPy
- Scikit-learn
- PyArrow
- Lingua
- TF-IDF
- SVD
- K-Means
- NLP classification
- Transformer models
- Sentence Transformers
- Parquet / CSV feature storage

---

## Project Structure

```text
youtube-comment-recsys-data-preparation/
│
├── prathamesh-mistry-comment-recsys-data-prep.ipynb
├── README.md
│
└── feature_store/
    ├── bronze/
    ├── silver/
    ├── gold/
    ├── embeddings/
    ├── business_scores/
    ├── models/
    ├── quarantine/
    ├── meta/
    └── _state/
