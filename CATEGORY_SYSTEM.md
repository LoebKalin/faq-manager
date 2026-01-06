# IT Support Categories - Professional Structure

## Overview
The FAQ system now includes a comprehensive, professionally organized category system with 10 main groups and 60+ subcategories specifically designed for IT Support scenarios.

## Main Categories

### 1. 🖥️ Hardware & Devices (7 subcategories)
- Desktop Computers
- Laptops & Notebooks
- Printers & Scanners
- Monitors & Displays
- Network Devices
- Mobile Devices
- Peripherals & Accessories

### 2. 💾 Software & Applications (7 subcategories)
- Office Suite (Word, Excel, Teams)
- Email & Collaboration
- Web Browsers
- VPN & Remote Access
- Security & Antivirus
- Software Updates & Patches
- Software Installation

### 3. 🔐 Account & Access (7 subcategories)
- Login & Authentication
- Password Management
- Multi-Factor Authentication (MFA)
- Single Sign-On (SSO)
- Permissions & Access Rights
- VPN Access
- Account Unlock

### 4. 📡 Network & Connectivity (7 subcategories)
- Wi-Fi Connectivity
- Ethernet Connection
- DNS & Domain Issues
- Network Speed & Performance
- VPN Connection Issues
- Firewall & Security
- Proxy Settings

### 5. ☁️ Cloud Services & Storage (6 subcategories)
- OneDrive & Cloud Storage
- SharePoint
- Azure Services
- File Sync & Backup
- Cloud Access Issues
- Cloud Migration

### 6. 🔧 Performance & Troubleshooting (7 subcategories)
- Slow Performance
- Application Crashes
- Memory & CPU Issues
- Disk Space Issues
- Error Messages
- Boot & Startup Issues
- System Freezing

### 7. 📞 Communication & Collaboration (6 subcategories)
- Microsoft Teams
- Zoom Meetings
- Slack
- VoIP & Phone Systems
- WebEx & Video Conferencing
- Email Issues

### 8. 🛡️ Security & Compliance (7 subcategories)
- Password Security
- Multi-Factor Authentication
- Threats & Malware
- Phishing & Scams
- Compliance & Policies
- Encryption & Privacy
- Security Incidents

### 9. 📋 Data & Backup (6 subcategories)
- Backup & Recovery
- Data Recovery
- Data Export & Import
- Data Migration
- Data Retention Policies
- Archiving

### 10. ❓ General IT Support (5 subcategories)
- Device Setup & Configuration
- Training & How-To
- IT Service Requests
- Feedback & Suggestions
- Other IT Issues

## Usage

### For End Users
1. Click "Add FAQ" button
2. Select from the categorized dropdown
3. Categories are grouped by function for easy navigation
4. Each category shows the group it belongs to in the FAQ table

### For IT Staff
- **Creating FAQs**: Choose the most specific subcategory
- **Viewing FAQs**: See both subcategory name and parent group in table
- **Searching**: Categories are organized logically for better filtering

## Technical Implementation

### Files
- **`client/constants/categories.ts`** - Category definitions and utility functions
- **`client/components/AddQuestion/AddQuestionModal/index.tsx`** - Category selector UI
- **`client/components/FaqTable/index.tsx`** - Category display with grouping

### Available Functions

```typescript
// Get all categories as flat list
getAllCategories(): string[]

// Get category label (subcategory name)
getCategoryLabel(value: string): string
// Example: getCategoryLabel("Hardware-Desktop") → "Desktop Computers"

// Get group label (main category)
getGroupLabel(value: string): string
// Example: getGroupLabel("Hardware-Desktop") → "🖥️ Hardware & Devices"
```

## Category Value Format

All category values follow the pattern: `Group-Subcategory`

Examples:
- `Hardware-Desktop`
- `Software-Office`
- `Access-Password`
- `Cloud-OneDrive`
- `Security-MFA`

This format enables:
- Easy identification of category purpose
- Potential future grouping/filtering by prefix
- Human-readable database values

## Benefits

✅ **Organized**: 10 logical groups instead of random text input  
✅ **Consistent**: Predefined values prevent typos and duplicates  
✅ **Professional**: Emojis and descriptive names improve UX  
✅ **Scalable**: Easy to add new categories without code changes  
✅ **Accessible**: Grouped dropdown makes selection intuitive  
✅ **Searchable**: Category structure aids in FAQ discovery  

## Future Enhancements

Possible extensions:
- Add category-based filtering/search
- Create category statistics dashboard
- Implement category permissions (assign to specific teams)
- Add FAQ templates per category
- Enable category-based SLA tracking
