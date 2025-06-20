# Employee Badge Program - Implementation Guide

## Overview

The Employee Badge Program is a gamification system designed for bakery operations to recognize employee achievements, track milestones, and provide point-based rewards. The system integrates between Airtable (data storage) and Glide (mobile interface) to create an engaging employee experience.

## System Architecture

### Database Structure (Airtable)

#### 1. **App_Badges** (Badge Definitions)
**Purpose:** Master list of all available badges
**Primary Key:** Badge_ID (Auto Number)

| Field Name | Type | Description |
|------------|------|-------------|
| Badge_ID | Auto Number | Primary key |
| Badge_Name | Single Line Text | Display name of badge |
| Badge_Icon | Single Line Text | Emoji icon |
| Badge_Description | Long Text | Description of achievement |
| Badge_Type | Single Select | Status/Achievement/Special |
| CSS_Class | Single Line Text | CSS class for styling |
| Points_Value | Number | Points awarded for earning badge |
| Sort_Order | Number | Display order |
| Is_Active | Checkbox | Whether badge is currently available |

#### 2. **Employee_Badges** (Badge Assignments)
**Purpose:** Junction table tracking which employees have earned which badges
**Primary Key:** Record_ID (Auto Number)

| Field Name | Type | Description |
|------------|------|-------------|
| Record_ID | Auto Number | Primary key |
| Employee_ID | Link to Records | Links to Employees table |
| Badge_ID | Link to Records | Links to App_Badges table |
| Date_Earned | Date | When badge was earned |
| Awarded_By | Link to Records | Employee who awarded badge |
| Points_Earned | Lookup | Points from Badge_ID → Points_Value |
| Is_Current | Checkbox | Whether badge is still active |
| Notes | Long Text | Reason for awarding |
| Created_Date | Created Time | Auto-timestamp |

#### 3. **Rewards_Store** (Available Rewards)
**Purpose:** Catalog of rewards employees can purchase with points
**Primary Key:** Reward_ID (Auto Number)

| Field Name | Type | Description |
|------------|------|-------------|
| Reward_ID | Auto Number | Primary key |
| Reward_Name | Single Line Text | Name of reward |
| Reward_Description | Long Text | What the reward includes |
| Points_Cost | Number | Point cost to redeem |
| Reward_Category | Single Select | Time Off/Food & Drinks/Gift Cards/etc. |
| Is_Available | Checkbox | Currently available for redemption |
| Stock_Quantity | Number | Limited quantity items |

#### 4. **Points_Redemptions** (Redemption History)
**Purpose:** Track when employees redeem points for rewards
**Primary Key:** Redemption_ID (Auto Number)

| Field Name | Type | Description |
|------------|------|-------------|
| Redemption_ID | Auto Number | Primary key |
| Employee_ID | Link to Records | Links to Employees table |
| Reward_ID | Link to Records | Links to Rewards_Store table |
| Points_Spent | Lookup | Points from Reward_ID → Points_Cost |
| Redemption_Date | Date | When redemption was requested |
| Status | Single Select | Pending/Approved/Fulfilled/Cancelled |
| Notes | Long Text | Additional details |
| Approved_By | Link to Records | Manager who approved |

#### 5. **Employees** (Enhanced with Badge Data)
**Purpose:** Employee records enhanced with badge/points calculations
**Existing Fields Plus:**

| Field Name | Type | Description |
|------------|------|-------------|
| Total_Points_Earned | Rollup | SUM(Employee_Badges → Points_Earned) |
| Total_Points_Spent | Rollup | SUM(Points_Redemptions → Points_Spent) |
| Current_Points_Balance | Formula | Total_Points_Earned - Total_Points_Spent |
| Badge_Count | Count | COUNT(Employee_Badges) |
| Last_Badge_Earned | Rollup | MAX(Employee_Badges → Date_Earned) |
| Tenure_Months | Formula | DATETIME_DIFF(TODAY(), Hire_Date, 'months') |

## Database Relationships

```
Employees (1) ←→ (Many) Employee_Badges (Many) ←→ (1) App_Badges
Employees (1) ←→ (Many) Points_Redemptions (Many) ←→ (1) Rewards_Store
Employees (1) ←→ (Many) Employee_Badges [Awarded_By relationship]
```

## Badge Types & Point Values

### Status Badges (Automatic/Manual Assignment)
- **New Joiner** (🎉) - 5 points - Auto-assigned on hire
- **Training Complete** (🎓) - 20 points - Manual assignment post-training
- **Tenure Badge** (📅) - 10 points - Auto-updates based on hire date

### Achievement Badges (Manual Assignment)
- **Star Performer** (⭐) - 25 points - Exceptional work quality
- **Team Player** (🤝) - 15 points - Collaborative spirit
- **Innovation** (💡) - 20 points - Creative problem solving
- **Quality Focus** (🎯) - 15 points - Attention to detail
- **Early Bird** (🌅) - 10 points - Punctuality
- **Customer Hero** (🛡️) - 25 points - Service excellence
- **Safety First** (🦺) - 15 points - Safety commitment
- **Mentor** (👨‍🏫) - 30 points - Guides new employees

### Special Badges (Manual Assignment)
- **OG Member** (👑) - 50 points - Original team members

## Glide Implementation

### Required Glide Tables
1. Import **Employees** table from Airtable
2. Import **App_Badges** table from Airtable
3. Import **Employee_Badges** table from Airtable
4. Import **Rewards_Store** table from Airtable
5. Import **Points_Redemptions** table from Airtable

### Relations Setup in Glide

#### Employee Profile Relations
```
Employees → Employee_Badges (Employee_ID)
Employee_Badges → App_Badges (Badge_ID)
Employees → Points_Redemptions (Employee_ID)
Points_Redemptions → Rewards_Store (Reward_ID)
```

### Template Columns

#### Badge Display Logic
```javascript
// Badge CSS Class Template
if [Badge Earned] = true then "card " + [CSS_Class] else "card inactive"

// Tenure Badge Text
if [Tenure_Months] < 12 then [Tenure_Months] + " Month" + if([Tenure_Months] != 1, "s", "") 
else ROUND([Tenure_Months]/12, 1) + " Year" + if(ROUND([Tenure_Months]/12, 1) != 1, "s", "")

// Points Balance Display
"💎 " + [Current_Points_Balance] + " points"
```

#### Badge Status Checking
```javascript
// New Joiner Badge (Auto-assign logic)
if [Hire_Date] >= TODAY() - 30 then true else false

// Training Complete Badge Check
if [Training_Complete_Date] is not empty then true else false

// Tenure Badge Active
if [Hire_Date] is not empty then true else false
```

### CSS Implementation

#### Badge Card Styles
```css
/* Base card styles */
.card {
  background-color: #0c2340;
  color: white;
  border-radius: 8px;
  padding: 15px 10px;
  text-align: center;
  border: none;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  transition: all 0.2s ease;
  min-height: 90px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.card-icon {
  color: white;
  font-size: 1.8rem;
  margin-bottom: 8px;
}

.card-title {
  font-size: 0.9rem;
  font-weight: 600;
  color: white;
  margin: 0;
  line-height: 1.2;
}

.card:hover {
  background-color: #0f2a4a;
  cursor: pointer;
  transform: translateY(-2px);
}

/* Badge-specific styles */
.card.inactive {
  background-color: #e9ecef;
  color: #6c757d;
  opacity: 0.6;
  transform: scale(0.95);
}

.card.new-joiner { 
  background: linear-gradient(135deg, #17a2b8 0%, #20c997 100%); 
}

.card.training-complete { 
  background: linear-gradient(135deg, #6f42c1 0%, #e83e8c 100%); 
}

.card.tenure { 
  background: linear-gradient(135deg, #fd7e14 0%, #ffc107 100%); 
}

.card.og-member { 
  background: linear-gradient(135deg, #0c2340 0%, #1a365d 100%);
  position: relative;
  overflow: hidden;
}

.card.og-member::before {
  content: '';
  position: absolute;
  top: -2px; left: -2px; right: -2px; bottom: -2px;
  background: linear-gradient(45deg, #ffd700, #ffed4e, #ffd700, #ffed4e);
  background-size: 400% 400%;
  animation: shimmer 2s ease-in-out infinite;
  border-radius: 8px;
  z-index: -1;
}

@keyframes shimmer {
  0%, 100% { background-position: 0% 50%; }
  50% { background-position: 100% 50%; }
}

.card.star-performer { 
  background: linear-gradient(135deg, #f7b801 0%, #ffa726 100%); 
}

.card.team-player { 
  background: linear-gradient(135deg, #28a745 0%, #20c997 100%); 
}
```

### Screen Components

#### Employee Profile Badge Section
1. **Container** - Badge section wrapper
2. **Text** - "Status Badges" section header
3. **Inline List** - Top 3 status badges (New Joiner, Training, Tenure)
   - Source: Employee_Badges relation filtered by Badge_Type = "Status"
   - Layout: Horizontal cards
   - CSS Classes: Applied via template column
4. **Text** - "Achievement Badges" section header  
5. **Collection** - Achievement badges grid
   - Source: Employee_Badges relation filtered by Badge_Type = "Achievement"
   - Layout: 3-column grid
   - CSS Classes: Applied via template column

#### Rewards Store Screen
1. **Collection** - Available rewards
   - Source: Rewards_Store table filtered by Is_Available = true
   - Action: Open reward detail with redemption option

## Automation & Workflows

### Airtable Automations

#### 1. New Employee Badge Assignment
```javascript
// Trigger: New record in Employees table
// Action: Create Employee_Badges record

let newEmployee = input.config();
let badgeId = "rec_new_joiner_badge_id"; // New Joiner badge ID

await base("Employee_Badges").createAsync({
  "Employee_ID": [newEmployee.employeeId],
  "Badge_ID": [badgeId],
  "Date_Earned": new Date().toISOString().split('T')[0],
  "Awarded_By": [newEmployee.hiredBy],
  "Is_Current": true,
  "Notes": "Auto-assigned on hire date"
});
```

#### 2. Tenure Badge Updates
```javascript
// Trigger: Scheduled daily
// Action: Update tenure badges for all employees

let employees = await base("Employees").select().all();
let today = new Date();

for (let employee of employees) {
  let hireDate = new Date(employee.getCellValue("Hire_Date"));
  let monthsWorked = Math.floor((today - hireDate) / (1000 * 60 * 60 * 24 * 30.44));
  
  // Update tenure badge name
  let tenureBadge = await base("Employee_Badges").select({
    filterByFormula: `AND({Employee_ID} = "${employee.id}", {Badge_ID} = "tenure_badge_id")`
  }).firstPage();
  
  if (tenureBadge.length > 0) {
    let badgeName = monthsWorked < 12 ? 
      `${monthsWorked} Month${monthsWorked !== 1 ? 's' : ''}` :
      `${Math.round(monthsWorked/12 * 10)/10} Year${Math.round(monthsWorked/12) !== 1 ? 's' : ''}`;
    
    await base("App_Badges").updateAsync(tenureBadge[0].getCellValue("Badge_ID")[0].id, {
      "Badge_Name": badgeName
    });
  }
}
```

### Glide Actions

#### Award Badge Action
1. **Add Row** to Employee_Badges table
   - Employee_ID: Current user
   - Badge_ID: Selected badge
   - Date_Earned: Current date
   - Awarded_By: Signed-in user
   - Is_Current: True

#### Redeem Reward Action
1. **Show Confirmation** - "Redeem [Reward Name] for [Points] points?"
2. **Add Row** to Points_Redemptions table
   - Employee_ID: Current user
   - Reward_ID: Selected reward
   - Redemption_Date: Current date
   - Status: "Pending"
3. **Show Notification** - "Redemption request submitted!"

## Implementation Steps

### Phase 1: Database Setup (Airtable)
1. Create App_Badges table with badge definitions
2. Create Employee_Badges junction table
3. Create Rewards_Store table
4. Create Points_Redemptions table
5. Add rollup/formula fields to Employees table
6. Set up table relationships
7. Import initial badge and reward data

### Phase 2: Glide Configuration
1. Connect Airtable as data source
2. Import all tables to Glide
3. Set up relations between tables
4. Create template columns for badge logic
5. Add custom CSS for badge styling
6. Configure user profiles and authentication

### Phase 3: UI Development
1. Build employee profile screen with badge sections
2. Create badge management admin screen
3. Build rewards store interface
4. Create redemption history screens
5. Add badge awarding workflows for managers

### Phase 4: Automation
1. Set up new hire badge automation
2. Create tenure badge update automation
3. Implement point balance calculations
4. Add approval workflows for redemptions

### Phase 5: Testing & Launch
1. Test all badge assignment workflows
2. Verify point calculations
3. Test redemption process
4. Train managers on badge awarding
5. Launch to employees with onboarding

## Maintenance & Administration

### Regular Tasks
- **Weekly:** Review pending redemptions
- **Monthly:** Award achievement badges based on performance
- **Quarterly:** Add new seasonal rewards
- **Annually:** Review point values and badge criteria

### Analytics & Reporting
- Most earned badges
- Employee engagement scores
- Points economy balance
- Redemption patterns
- Badge distribution across departments

## Future Enhancements

### Potential Features
1. **Leaderboards** - Monthly point leaders
2. **Team Badges** - Department-wide achievements  
3. **Seasonal Challenges** - Limited-time badge opportunities
4. **Social Features** - Badge sharing and congratulations
5. **Advanced Analytics** - Performance correlation with badges
6. **Mobile Notifications** - Badge earned alerts
7. **Custom Rewards** - Employee-suggested rewards
8. **Badge Streaks** - Consecutive month achievements

## Technical Notes

### Performance Considerations
- Use Glide's caching for badge status checks
- Limit badge history to last 12 months for performance
- Use computed columns for complex calculations
- Optimize relations to avoid circular references

### Security
- Restrict badge awarding to managers only
- Audit trail for all badge assignments
- Point balance verification to prevent manipulation
- Approval workflow for high-value redemptions

### Backup & Recovery
- Daily Airtable backups
- Document all custom formulas
- Version control for CSS and templates
- Employee data export capabilities

---

*This document serves as the complete technical specification for the Employee Badge Program implementation between Airtable and Glide.*
