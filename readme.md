# Great Harvest Bakery Operations App

## Project Overview
A comprehensive mobile-first operations management system for Great Harvest Crown Point bakery, built with Airtable backend and Glide frontend.

## Repository Structure

```
bakery-operations-app/
├── README.md                          # This file
├── docs/
│   ├── architecture/
│   │   ├── overview.md                # High-level system design
│   │   ├── data-flow.md               # Operational-first data flow
│   │   └── table-relationships.md     # Entity relationship diagram
│   ├── airtable/
│   │   ├── schema.md                  # Complete table structures
│   │   ├── automations.md             # All automation configurations
│   │   ├── formulas.md                # Calculated field formulas
│   │   └── views.md                   # Table views and filters
│   ├── glide/
│   │   ├── app-structure.md           # Navigation and screens
│   │   ├── components.md              # UI component configurations
│   │   ├── actions.md                 # Form actions and workflows
│   │   └── styling.md                 # Brand colors and design
│   └── deployment/
│       ├── setup-guide.md             # Step-by-step setup instructions
│       ├── testing.md                 # Test scenarios and data
│       └── troubleshooting.md         # Common issues and solutions
├── config/
│   ├── tables/
│   │   ├── events.yml                 # Events table structure
│   │   ├── catering-details.yml       # Catering Details structure
│   │   ├── market-details.yml         # Market Details structure
│   │   ├── production-details.yml     # Production Details structure
│   │   ├── ordering-details.yml       # Ordering Details structure
│   │   └── interview-details.yml      # Interview Details structure
│   ├── automations/
│   │   ├── catering-to-events.yml     # Catering automation config
│   │   ├── market-to-events.yml       # Market automation config
│   │   └── update-automations.yml     # Update automation configs
│   ├── brand/
│   │   ├── colors.yml                 # Brand color scheme
│   │   └── styling-guide.md           # UI/UX guidelines
│   └── glide-config.yml               # Glide app configuration
├── backups/
│   ├── airtable-exports/
│   │   ├── events-YYYY-MM-DD.csv      # Periodic data exports
│   │   └── catering-details-YYYY-MM-DD.csv
│   ├── screenshots/
│   │   ├── airtable/
│   │   │   ├── table-structures/
│   │   │   └── automations/
│   │   └── glide/
│   │       ├── home-screen.png
│   │       └── forms/
│   └── configs/
│       └── automation-screenshots/
├── scripts/
│   ├── airtable-backup.js             # Data export scripts
│   ├── validation.js                  # Data validation scripts
│   └── migration-helpers.js           # Future migration tools
└── testing/
    ├── test-data.md                   # Sample data for testing
    ├── user-scenarios.md              # Test scenarios
    └── validation-checklist.md        # QA checklist
```

## Quick Start

1. **Clone this repository**
   ```bash
   git clone [repository-url]
   cd bakery-operations-app
   ```

2. **Set up Airtable** (Follow docs/deployment/setup-guide.md)
   - Create base from config/tables/*.yml
   - Configure automations from config/automations/*.yml
   - Import test data from testing/test-data.md

3. **Set up Glide** (Follow docs/glide/app-structure.md)
   - Connect to Airtable base
   - Configure navigation and components
   - Apply brand styling from config/brand/

## Current Status

- [x] Architecture designed
- [x] Airtable base created with AI builder
- [x] Basic catering automation configured
- [ ] Data integrity automations
- [ ] Glide app setup
- [ ] Testing and validation
- [ ] Production deployment

## Key Features

### Operational Data Flow
- **Operational-first approach**: Staff enters rich data in detail tables
- **Auto-generated events**: Calendar events created automatically
- **Single source of truth**: Detail tables remain authoritative
- **Real-time updates**: Changes propagate automatically

### Event Types
- 🍽️ Catering (EzCater orders, custom orders)
- 🧺 Market (Farmers markets, events)
- 🍞 Production (Baking schedules, batches)
- 🚚 Ordering & Deliveries (Supplier orders)
- 👥 Interviews (Staff hiring)
- 🏪 Store Events (Promotions, training)
- 📈 Administrative (Reports, compliance)
- 🧩 Miscellaneous (Quick entries)

### Tech Stack
- **Backend**: Airtable (database, automations, business logic)
- **Frontend**: Glide (mobile-first interface)
- **Integration**: Airtable API + Glide native connection
- **Version Control**: Git (documentation and configuration)

## Contributing

### Documentation Standards
- Keep all documentation in markdown format
- Update relevant docs with every change
- Include screenshots for UI changes
- Version configuration files with changes

### Backup Strategy
- Export Airtable data weekly to backups/airtable-exports/
- Screenshot major configuration changes
- Document all automation changes in config/automations/

### Testing
- Test all changes with sample data from testing/test-data.md
- Validate user scenarios from testing/user-scenarios.md
- Update validation checklist for new features

## Contact & Support

- **Project Owner**: [Your Name]
- **Bakery**: Great Harvest Crown Point
- **Architecture**: Operational-first, mobile-focused
- **Status**: In Development

---

*This documentation is version controlled and updated as the application evolves.*