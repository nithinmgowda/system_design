# Vending Machine System Design - Interview Guide

## 1. Start with the Problem Statement (30 seconds)

**"We need to design a smart vending machine system that sells snacks and drinks."**

**Basic User Flow:**
- User approaches machine
- Selects product 
- Pays money
- Machine dispenses product
- User gets change (if needed)

**Key Requirements:**
- Accept multiple payment methods (cash, card, mobile)
- Track inventory in real-time
- Handle concurrent users
- Provide change accurately
- Monitor machine health remotely

---

## 2. High-Level Architecture (1-2 minutes)

### Basic Components:
```
User Interface → Payment System → Inventory Management → Dispensing System
                      ↓
              Central Management System
```

**Explain in simple terms:**
1. **User Interface** - Touch screen, buttons, display
2. **Payment System** - Handles cash, cards, mobile payments
3. **Inventory System** - Tracks what's available
4. **Dispensing Mechanism** - Physical motors and sensors
5. **Central System** - Monitors all machines remotely

---

## 3. Core Components (2-3 minutes)

### A. User Interface Module
**"How users interact with the machine:"**
- Touch screen display showing products and prices
- Physical buttons as backup
- LED indicators for product availability
- Audio feedback for accessibility

### B. Payment Processing System
**"Handles all types of payments:"**
- **Cash Handler**: Bill validator, coin acceptor, change dispenser
- **Card Reader**: Credit/debit card processing
- **Mobile Payments**: NFC for Apple Pay, Google Pay
- **Payment Validation**: Verify amount before dispensing

### C. Inventory Management
**"Tracks what's inside the machine:"**
```
Inventory Table:
- slot_id (A1, B2, etc.)
- product_name
- current_quantity
- max_capacity
- price
- expiry_date
```

### D. Dispensing System
**"Physical mechanism to deliver products:"**
- Servo motors for each slot
- Sensors to confirm product dropped
- Door lock/unlock mechanism
- Error detection (product stuck)

### E. Local Controller
**"Brain of the machine:"**
- Embedded system (like Raspberry Pi)
- Manages all operations
- Stores transaction data locally
- Communicates with central system

---

## 4. Key Technical Decisions (2-3 minutes)

### A. State Management
**Vending Machine States:**
```
IDLE → SELECTING → PAYMENT → DISPENSING → CHANGE → IDLE
```

**State Transitions:**
- **IDLE**: Showing product catalog
- **SELECTING**: User chooses product
- **PAYMENT**: Processing payment
- **DISPENSING**: Releasing product
- **CHANGE**: Calculating and giving change
- **ERROR**: Handling failures (refund, maintenance alert)

### B. Payment Processing
**Multi-step Validation:**
1. Check payment amount
2. Verify product availability
3. Reserve the product slot
4. Process payment
5. Dispense if successful OR refund if failed

**Payment Flow:**
```
Payment Received → Validate Amount → Reserve Product → 
Attempt Dispensing → Success? → Complete Transaction : Refund
```

### C. Inventory Tracking
**Real-time Updates:**
- Decrement count when product dispensed
- Update central system every 5 minutes
- Alert when stock low (< 5 items)
- Prevent overselling

### D. Error Handling
**Common Failure Scenarios:**
- Product gets stuck → Sensor detects → Auto-retry or refund
- Payment fails → Immediate refund
- Machine full (change) → Stop accepting cash
- Network down → Store transactions locally, sync later

---

## 5. Scale Considerations (1-2 minutes)

### Fleet Management:
- **100s of machines** across city/region
- **Different locations** - offices, malls, airports
- **Different product mixes** - location-specific inventory
- **Remote monitoring** - health status, sales, maintenance needs

### Central Management System:
**Features needed:**
1. **Real-time monitoring** - Machine status dashboard
2. **Inventory management** - Restock alerts and scheduling
3. **Sales analytics** - Popular products, revenue tracking
4. **Maintenance scheduling** - Predictive maintenance
5. **Remote configuration** - Update prices, product catalog

### Data Synchronization:
- **Offline capability** - Machines work without internet
- **Batch sync** - Upload sales data every hour
- **Priority updates** - Price changes push immediately
- **Conflict resolution** - Handle network partition scenarios

---

## 6. Database Design (1 minute)

### Local Storage (on each machine):
```sql
-- Products Table
CREATE TABLE products (
    slot_id VARCHAR(5) PRIMARY KEY,
    product_name VARCHAR(100),
    price DECIMAL(5,2),
    current_stock INTEGER,
    max_capacity INTEGER
);

-- Transactions Table  
CREATE TABLE transactions (
    id UUID PRIMARY KEY,
    product_slot VARCHAR(5),
    amount_paid DECIMAL(5,2),
    payment_method VARCHAR(20),
    timestamp DATETIME,
    status VARCHAR(20) -- SUCCESS/FAILED/REFUNDED
);
```

### Central Database:
- **Machines Table** - Location, status, last sync
- **Sales Analytics** - Aggregated transaction data
- **Inventory Logs** - Stock levels over time
- **Maintenance Records** - Service history

---

## 7. Follow-up Questions & Answers

### Q: "How do you handle machine failures?"
**A:** "Local error detection with sensors, automatic refunds, maintenance alerts to central system, and redundant mechanisms for critical functions."

### Q: "What if payment is successful but product doesn't dispense?"
**A:** "Sensors detect failed dispensing, trigger automatic refund, log incident for maintenance, and mark slot as out-of-order."

### Q: "How do you prevent theft or tampering?"
**A:** "Physical security (locks, alarms), tamper detection sensors, secure payment processing, and surveillance camera integration."

### Q: "How do you optimize inventory?"
**A:** "Analyze sales patterns, predict demand by location/time, automate restock alerts, and adjust product mix based on popularity."

### Q: "What about software updates?"
**A:** "Over-the-air updates during low-traffic hours, rollback capability, staged deployments, and version control."

---

## 8. Interview Tips

### Do:
- **Start with user journey** - "Person wants to buy a Coke..."
- **Think about edge cases** - What goes wrong?
- **Consider physical constraints** - Limited slots, change capacity
- **Mention real-world experience** - "I've seen machines that..."

### Don't:
- Forget about physical hardware limitations
- Ignore payment security requirements
- Over-complicate the initial design
- Forget about maintenance and operations

### Key Phrases to Use:
- "The user experience should be..."
- "For reliability, we need..."
- "To handle edge cases..."
- "From an operations perspective..."
- "The physical constraints are..."

---

## 9. Sample 5-Minute Walkthrough

**Minutes 1-2:** Problem (buying snacks) + basic user flow
**Minutes 3-4:** Core components (UI, payment, inventory, dispensing)
**Minutes 5:** Scale challenges + fleet management

---

## 10. Advanced Topics (If Time Allows)

### Smart Features:
- **Machine Learning** - Predict popular products by time/weather
- **Dynamic Pricing** - Higher prices during peak demand
- **Personalization** - Remember user preferences via app
- **Health Integration** - Calorie tracking, dietary restrictions

### IoT Integration:
- **Temperature monitoring** - Keep drinks cold
- **Predictive maintenance** - Detect worn parts before failure
- **Energy optimization** - Reduce power consumption
- **Remote diagnostics** - Troubleshoot without site visit

### Business Intelligence:
- **Sales forecasting** - Plan inventory purchases
- **Location optimization** - Best spots for new machines
- **Product performance** - Which items sell best where
- **Operational efficiency** - Optimize restocking routes

---

## 11. Technical Architecture Details

### Local Machine Architecture:
```
Hardware Layer:
├── Sensors (inventory, temperature, door)
├── Motors (dispensing mechanisms)
├── Payment Hardware (card reader, bill validator)
└── Display (touchscreen interface)

Software Layer:
├── Embedded OS (Linux/Real-time OS)
├── Application Logic (state machine)
├── Local Database (SQLite)
└── Communication Module (WiFi/4G)
```

### Communication Protocols:
- **Local**: I2C/SPI for hardware communication
- **Remote**: HTTPS/MQTT for central system
- **Payments**: Secure protocols (EMV, PCI DSS)
- **Monitoring**: Real-time telemetry data

---

## 12. Security Considerations

### Physical Security:
- **Tamper-evident seals** on critical components
- **Secure locks** with audit trails
- **Vandalism protection** - Reinforced materials
- **Surveillance integration** - Camera alerts for incidents

### Cyber Security:
- **Encrypted communications** - All data transmission secured
- **Secure payment processing** - PCI DSS compliance
- **Access controls** - Role-based maintenance access
- **Software integrity** - Signed updates, verified boot

---

## 13. Key Points to Remember

### Core Concept:
"A vending machine is like a small, automated store that needs to work reliably 24/7 without human intervention."

### Critical Success Factors:
1. **Reliability** - Must work every time user pays
2. **Accuracy** - Correct change, right product
3. **Security** - Protect money and prevent theft
4. **Maintainability** - Easy to restock and service
5. **Profitability** - Optimize sales and minimize costs

### Common Challenges:
- **Hardware failures** - Moving parts wear out
- **Payment disputes** - Money taken but no product
- **Inventory errors** - Display shows available but slot empty
- **Vandalism** - Physical damage or theft attempts
- **Weather** - Outdoor machines face harsh conditions

---

## 14. Student-Friendly Analogies

**Vending Machine = Mini Robot Store:**
- Has brain (controller) that makes decisions
- Has hands (motors) that grab products
- Has eyes (sensors) that see what's happening
- Has memory (database) that remembers inventory
- Talks to headquarters (central system) for updates

**State Machine = Traffic Light:**
- Clear states: Red, Yellow, Green
- Specific transitions between states
- External triggers cause state changes
- Always knows current state

**Inventory Management = Kitchen Pantry:**
- Know what you have
- Track when you use something
- Reorder before running out
- Check expiration dates

**Remember:** In interviews, relate technical concepts to everyday experiences that everyone understands!