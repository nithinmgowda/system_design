# Text Storage Service (Pastebin) - System Design Interview Guide

## 1. Start with the Problem Statement (30 seconds)

**"We need to build a service like Pastebin where users can paste text and share it via a link."**

**Example:** 
- User pastes code/text
- We give them: `pastebin.com/xyz123`
- Anyone with that link can view the text

**Key Requirements:**
- Store text content
- Generate unique shareable links
- Retrieve and display text quickly
- Handle millions of pastes

---

## 2. High-Level Architecture (1-2 minutes)

### Basic Flow:
```
User → Load Balancer → Web Servers → Database
                           ↓
                       Cache Layer
```

**Explain in simple terms:**
1. **User comes to our website** - wants to save some text
2. **Load balancer** - distributes traffic (like a traffic cop)
3. **Web servers** - handle the actual requests
4. **Database** - stores the text content with unique IDs
5. **Cache** - keeps popular pastes in memory for speed

---

## 3. Core Components (2-3 minutes)

### A. Text Storage Service
**"When someone wants to save text:"**
1. Generate a unique paste ID (like "xyz123")
2. Save the mapping: `xyz123 → text_content`
3. Return the shareable link

### B. Text Retrieval Service
**"When someone visits a paste link:"**
1. Extract the paste ID ("xyz123")
2. Look it up in our database/cache
3. Display the original text content

### C. Database Design
**Simple table structure:**
```
Pastes Table:
- paste_id (Primary Key)
- content (the actual text)
- created_date
- expires_at (optional)
- language (for syntax highlighting)
```

---

## 4. Key Technical Decisions (2-3 minutes)

### A. How to Generate Paste IDs?
**Option 1: Random Generation**
- Generate random 8-character strings
- Use Base62 encoding (a-z, A-Z, 0-9)
- Check if it already exists, retry if collision

**Option 2: Counter-based**
- Use auto-incrementing counter
- Convert number to Base62
- More predictable, no collisions

**Recommendation:** Random for privacy, counter for simplicity

### B. Database Choice
**SQL Database (PostgreSQL/MySQL):**
- Good for: Text storage, simple queries
- Easy query: "SELECT content FROM pastes WHERE paste_id = 'xyz123'"
- Handles text encoding (UTF-8) well

### C. Caching Strategy
**Redis for popular pastes:**
- Keep frequently accessed pastes in memory
- Check cache first, then database
- Much faster than database lookup

---

## 5. Scale Considerations (1-2 minutes)

### Traffic Patterns:
- **Read-heavy system** (more views than new pastes)
- **90:1 ratio** - 90 views for every 1 new paste created
- **Popular pastes** get viewed thousands of times

### Scaling Solutions:
1. **Cache popular pastes** - 80% of traffic hits 20% of content
2. **Database replicas** - Multiple read-only copies for viewing
3. **Load balancers** - Handle traffic spikes
4. **CDN** - Serve static assets (CSS, JS) from edge locations
5. **Text compression** - Gzip compress large text blocks

---

## 6. Follow-up Questions & Answers

### Q: "How do you handle large text files?"
**A:** "Set size limits (like 10MB), use file storage (S3) for very large pastes, keep metadata in database."

### Q: "What if two users get the same paste ID?"
**A:** "Use atomic database operations or check-and-retry logic. Very rare with 62^8 combinations."

### Q: "How do you handle syntax highlighting?"
**A:** "Detect language on frontend, apply highlighting in browser. Store plain text in database."

### Q: "What about private pastes?"
**A:** "Add privacy flag, require authentication/password, or use unguessable longer IDs."

---

## 7. Interview Tips

### Do:
- **Start simple** - basic text storage first, then add features
- **Think about user experience** - how do people use Pastebin?
- **Ask clarifying questions** - "Should pastes expire automatically?"
- **Consider trade-offs** - "We could add features but it increases complexity"

### Don't:
- Jump into complex details immediately
- Forget about the data model
- Ignore text size limitations
- Over-engineer with microservices initially

### Key Phrases to Use:
- "Let's start with the basic flow..."
- "For scale, we would need..."
- "The trade-off here is..."
- "A simple approach would be..."
- "To handle millions of requests..."

---

## 8. Sample 5-Minute Walkthrough

**Minutes 1-2:** Problem statement + basic architecture
**Minutes 3-4:** Core services (shortening + resolution)
**Minutes 5:** Scaling considerations + follow-up questions

**Remember:** Keep it conversational, draw diagrams if possible, and always relate technical decisions back to user needs!