# Content Delivery Network (CDN) - System Design Interview Guide

## 1. Start with the Problem Statement (30 seconds)

**"We need to build a CDN like CloudFlare that delivers content faster to users worldwide."**

**The Problem:**
- Website in New York, user in Tokyo → slow loading (200ms+ latency)
- Popular websites get millions of requests → servers overloaded
- Large files (images, videos) take forever to download

**What CDN Does:**
- Caches content closer to users
- Reduces server load on origin
- Makes websites load 5-10x faster

**Key Requirements:**
- Store content at multiple locations globally
- Route users to nearest server
- Keep content fresh and updated
- Handle massive traffic spikes

---

## 2. High-Level Architecture (1-2 minutes)

### Basic Flow:
```
User Request → DNS → Nearest Edge Server → Origin Server (if needed)
                ↓
            Cached Content
```

**Explain in simple terms:**
1. **User requests file** - wants image/video from website
2. **DNS routing** - finds closest CDN server to user
3. **Edge server** - checks if it has the file cached
4. **Serve from cache** OR **fetch from origin** if not cached
5. **Cache for future** - store copy for next users

---

## 3. Core Components (2-3 minutes)

### A. Edge Servers (PoPs - Points of Presence)
**"Our servers placed around the world:"**
- Located in major cities globally
- Store cached copies of popular content
- Handle user requests directly
- **Example:** Servers in NYC, London, Tokyo, Mumbai

### B. Origin Server
**"The main website's server:"**
- Contains the original/master files
- CDN fetches content from here when needed
- Usually customer's existing web server

### C. DNS Management
**"Smart routing system:"**
- Determines which edge server user should connect to
- Based on: geographic location, server load, network conditions
- Returns IP address of best edge server

### D. Cache Management
**"Decides what to store and for how long:"**
```
Cache Rules:
- Images/CSS: Cache for 24 hours
- Videos: Cache for 7 days  
- HTML: Cache for 1 hour
- API responses: No cache
```

---

## 4. Key Technical Decisions (2-3 minutes)

### A. How do we route users to nearest server?
**GeoDNS (Geographic DNS):**
- User in India → Mumbai edge server
- User in USA → New York edge server
- **Why?** Lower latency = faster loading

**Anycast Routing:**
- All edge servers use same IP address
- Internet routes user to closest one automatically
- More advanced but very efficient

### B. Cache Strategy
**Cache Levels:**
1. **Browser Cache** - User's computer (fastest)
2. **Edge Cache** - CDN servers (fast)
3. **Origin Cache** - Website's server (slower)

**Cache Invalidation:**
- **TTL (Time To Live)** - Files expire after X hours
- **Manual purge** - Website owner can force refresh
- **Version-based** - Change filename when content updates

### C. Storage at Edge
**What to store:**
- **Hot storage (SSD)** - Popular files for instant access
- **Warm storage (HDD)** - Less popular files
- **Cold storage** - Rarely accessed, fetch from origin

---

## 5. Scale Considerations (1-2 minutes)

### Global Distribution:
- **100+ edge servers** across continents
- **Tier-1 cities first** - NYC, London, Tokyo
- **Tier-2 expansion** - Regional cities as traffic grows

### Traffic Patterns:
- **Peak hours vary by region** - Asia morning ≠ US morning
- **Viral content** - Sudden traffic spikes to specific files
- **Seasonal traffic** - Holiday shopping, sports events

### Scaling Solutions:
1. **More edge servers** - Add capacity in high-traffic areas
2. **Larger cache storage** - Store more content locally
3. **Intelligent caching** - Predict what content will be popular
4. **Load balancing** - Distribute traffic across multiple servers in same city

---

## 6. Follow-up Questions & Answers

### Q: "How do you keep content updated across all servers?"
**A:** "Use TTL expiration and cache invalidation APIs. When origin updates, we can purge specific files from all edge servers."

### Q: "What if an edge server goes down?"
**A:** "Route traffic to next nearest server. Have redundancy in each region."

### Q: "How do you handle dynamic content?"
**A:** "Don't cache it! Route directly to origin server. Or use very short TTL (seconds)."

### Q: "What about security?"
**A:** "HTTPS everywhere, DDoS protection at edge, access controls, and content validation."

### Q: "How do you measure performance?"
**A:** "Track latency, cache hit rates, bandwidth usage, and user experience metrics."

---

## 7. Interview Tips

### Do:
- **Start with user experience** - "User in Tokyo wants to download a file from US server"
- **Think geographically** - Draw a world map if helpful
- **Explain cache benefits** - Speed + reduced origin load
- **Consider different content types** - Images vs videos vs API calls

### Don't:
- Get lost in networking protocols initially
- Forget about cache invalidation
- Ignore global distribution challenges
- Over-complicate the initial design

### Key Phrases to Use:
- "The user experience improves because..."
- "We place servers closer to users..."
- "Caching reduces latency from X to Y..."
- "For global scale, we need..."
- "The trade-off between freshness and speed is..."

---

## 8. Sample 5-Minute Walkthrough

**Minutes 1-2:** Problem (slow global access) + basic CDN concept
**Minutes 3-4:** Edge servers + caching strategy + DNS routing
**Minutes 5:** Scaling globally + cache invalidation challenges

---

## 9. Advanced Topics (If Time Allows)

### Content Optimization:
- **Image compression** - Serve WebP to modern browsers
- **Minification** - Compress CSS/JS files
- **Adaptive streaming** - Different video quality based on connection

### Edge Computing:
- **Edge functions** - Run code at edge servers
- **Dynamic content generation** - Personalization at edge
- **Real-time processing** - Process requests without going to origin

### Business Considerations:
- **Bandwidth costs** - Expensive to serve from many locations
- **Popular content** - Cache longer, unpopular content purge faster
- **Customer tiers** - Free vs premium caching policies

---

## 10. Key Points to Remember

### Core Concept:
"CDN is like having multiple copies of a store in different cities, so customers don't have to travel far to shop."

### Technical Benefits:
1. **Reduced Latency** - Content served from nearby servers
2. **Improved Reliability** - Multiple servers provide redundancy
3. **Reduced Origin Load** - Less traffic to main server
4. **Better User Experience** - Faster loading times

### Business Benefits:
1. **Cost Savings** - Reduced bandwidth costs at origin
2. **Global Reach** - Serve users worldwide effectively
3. **Scalability** - Handle traffic spikes automatically
4. **SEO Benefits** - Faster sites rank better

---

## 11. Student-Friendly Analogies

**CDN = Library System:**
- Main library (origin server) has all books
- Branch libraries (edge servers) have popular books
- You go to nearest branch first
- If book not there, branch gets it from main library

**Caching = Grocery Store:**
- Popular items (milk, bread) always in stock
- Specialty items ordered when needed
- Store predicts what customers want
- Expires old products regularly

**Remember:** In interviews, use analogies to show you understand the real-world impact, not just the technical details!