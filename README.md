# Friday Announcements Ticker - Infinite Scroll Version

## 🎯 What's New

**Infinite Scroll Architecture:**
- No more restarting/hiccups
- Messages append continuously from the right
- New teacher messages appear within 10-20 seconds
- Weighted probability system for fair message rotation

---

## 📊 New Sheet Structure

Your Google Sheet will now have these tabs:

### **1. Form Responses 1**
- Raw form submissions (will be auto-moved by script)
- Don't manually edit this

### **2. Teacher Messages** (auto-created)
- Column A: Message
- Column B: Name
- Column C: Email
- Column D: Submitted Date
- Column E: Times Shown (auto-tracked)

### **3. Pending Students** (auto-created)
- Awaiting your approval
- Same as before

### **4. Approved Students** (auto-created)
- Column A: Message
- Column B: Name
- Column C: Email
- Column D: Approval Date
- Column E: Displayed (TRUE/FALSE)

### **5. Bank**
- Column A: Your 20 pro-school slogans
- Reusable weekly messages

### **6. Archive** (auto-created)
- Old messages moved here after 7 days

---

## 🚀 Setup Instructions

### Step 1: Update Your Apps Script

1. **Open your Google Sheet** → Extensions → Apps Script

2. **Delete ALL existing code**

3. **Paste the new code** from `code-v2.gs`

4. **Save** (Ctrl+S or Cmd+S)

5. **IMPORTANT: Migrate existing data**
   - If you have teacher submissions in "Form Responses 1", manually move them to "Teacher Messages"
   - Format: Message (A), Name (B), Email (C), Date (D), Times Shown = 0 (E)

6. **Deploy as Web App** (if not already done):
   - Click "Deploy" → "New deployment" (or "Manage deployments" if updating)
   - Type: Web app
   - Execute as: **Me**
   - Who has access: **Anyone**
   - Deploy
   - **Copy the /exec URL** (should be the one you already have)

7. **Set up Form Trigger**:
   - Click the clock icon ⏰ (Triggers)
   - "+ Add Trigger"
   - Function: `onFormSubmit`
   - Event source: "From spreadsheet"
   - Event type: "On form submit"
   - Save

---

### Step 2: Update GitHub Pages

1. **Replace `index.html`** in your GitHub repo with `index-infinite.html`
   - Rename it to `index.html` when uploading
   - The API_URL is already set to your /exec URL

2. **Commit changes**

3. **Wait 1-2 minutes** for GitHub Pages to update

---

## 🎲 How the Algorithm Works

### **Batch Generation (40 messages per batch)**

**Priority Rules:**
1. **All teachers shown at least once** before any students appear
2. After that, weighted probabilities:

**Teacher Probability:**
- Starts at 60%
- Decreases by 10% for each time shown
- Minimum 20% (always have a chance)
- Example: 
  - Never shown = 60% chance
  - Shown 1x = 50% chance
  - Shown 2x = 40% chance
  - Shown 3x = 30% chance
  - Shown 4+ = 20% chance

**Student Probability:**
- 30% chance (only after all teachers shown once)
- Each student shown exactly once per day

**Bank Probability:**
- 40% normally
- 20% if there are 20+ submitted messages (teacher + student)

### **Infinite Scroll:**
1. Page loads → fetches first batch (40 messages)
2. Messages scroll left continuously
3. Every 10 seconds → fetch next batch (40 more)
4. New messages append to the right
5. Never restarts, never stops

### **New Teacher Messages:**
- Submit via form
- Moves to "Teacher Messages" sheet automatically
- Appears in next batch (within 10-20 seconds)
- No visual hiccup - just smoothly appends to the right

---

## ⚙️ Usage

### **Friday Morning Routine:**

1. **Before the show:**
   - Open moderation page: `https://[username].github.io/announcements/moderate.html`
   - Approve student submissions
   - Open ticker: `https://[username].github.io/announcements/`

2. **During the show:**
   - Ticker runs automatically
   - Teacher submits new announcement → appears within 10-20 seconds
   - Students appear once each
   - Bank messages fill gaps

3. **Speed control:**
   - Adjust slider if needed
   - Minimize control panel (click −)

---

## 📈 Tracking & Analytics

**Teacher Messages:**
- "Times Shown" column auto-increments
- See which messages are most frequent
- Probability auto-adjusts based on count

**Student Messages:**
- "Displayed" column marks TRUE after shown
- Each student appears exactly once per day
- Resets at midnight (manually or via script)

---

## 🧹 Weekly Cleanup

**Option 1: Manual**
1. Open Apps Script
2. Run function: `archiveOldSubmissions`
3. Moves teacher messages older than 7 days to Archive

**Option 2: Automatic**
1. Apps Script → Triggers
2. Add Trigger:
   - Function: `archiveOldSubmissions`
   - Event: Time-driven
   - Type: Week timer
   - Day: Every Monday
   - Time: 6am-7am

---

## 🎛️ Customization

### **Batch Size**
In `code-v2.gs`, line 55:
```javascript
const batchSize = 40;
```
Change to 30, 50, 60, etc.

### **Fetch Interval**
In `index-infinite.html`, line 271:
```javascript
setInterval(fetchBatch, 10000); // 10 seconds
```
Change to 5000 (5 sec), 15000 (15 sec), etc.

### **Probabilities**
In `code-v2.gs`, adjust these values:
- Line 76: `const bankProbability = totalSubmitted >= 20 ? 0.20 : 0.40;`
- Line 96: `const teacherProb = Math.max(0.20, 0.60 - ...`
- Line 97: `const studentProb = 0.30;`

### **Teacher Probability Decay**
In `code-v2.gs`, line 96:
```javascript
0.60 - (getAverageTimesShown(teachers) * 0.10)
```
Change `0.10` to `0.05` (slower decay) or `0.15` (faster decay)

---

## 🔧 Troubleshooting

**"Error fetching messages"**
- Check Apps Script is deployed as "Anyone" access
- Verify API_URL is correct in HTML
- Check browser console (F12) for details

**Teacher messages not appearing**
- Check "Teacher Messages" sheet exists
- Verify trigger is set up for onFormSubmit
- Check submitted date is within last 6 days

**Students not going to pending**
- Verify email has 2+ digits before @ (e.g., 25jdoe@school.edu)
- Check onFormSubmit trigger is active

**Ticker seems to repeat same messages**
- This is normal! Teachers are shown multiple times based on probability
- Check "Times Shown" column to see distribution
- Bank messages are random picks, so may repeat

**Speed inconsistent**
- Make sure you set the speed slider
- Speed = pixels per second (consistent visual speed)

---

## 💡 Tips

- **Teacher priority works!** All teachers get shown at least once before any student appears
- **Probability balances rotation** - messages shown less often get higher probability
- **Bank fills gaps** - if you have 20+ submissions, bank drops to 20% to prioritize real announcements
- **Infinite scroll is smooth** - new messages just appear on the right, no restart
- **6-minute show** - with batch every 10 seconds, you'll fetch ~36 batches = ~1,440 messages total (but many duplicates by design)

---

## 🆘 Need Help?

Check these first:
1. Apps Script deployed as "Anyone"?
2. API_URL correct in HTML?
3. Sheets created with correct headers?
4. Form trigger set up?
5. Teacher Messages sheet has data?

Your setup:
- Ticker: `https://mcglynnmorningnews-mms.github.io/ScrollBar/`
- Moderate: `https://mcglynnmorningnews-mms.github.io/ScrollBar/moderate.html`
- Apps Script: In your Google Sheet → Extensions → Apps Script
