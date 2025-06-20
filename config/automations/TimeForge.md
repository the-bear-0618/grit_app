Time Forge Integration

# TimeForge API Integration Summary

## Project Overview

We are building an automated employee onboarding integration that creates TimeForge employee records when offer letters are digitally signed, eliminating manual data entry and ensuring immediate system access for new hires.

## Integration Flow

```mermaid
graph LR
    A[PandaDoc<br/>Offer Letter Signed] --> B[n8n Workflow<br/>Automation Platform]
    B --> C[Airtable<br/>GRIT.Recruiting Database]
    C --> D[TimeForge API<br/>Employee Creation]
    D --> E[Airtable<br/>Status Update]
    
    style A fill:#e1f5fe
    style B fill:#f3e5f5
    style C fill:#e8f5e8
    style D fill:#fff3e0
    style E fill:#e8f5e8
```

## Technical Architecture

### Data Flow Process:

1. **Trigger**: PandaDoc sends webhook when offer letter receives final signature
1. **Data Retrieval**: n8n retrieves applicant details from Airtable using document ID
1. **Employee Creation**: n8n calls TimeForge API to create new employee record
1. **Status Update**: n8n updates Airtable with TimeForge employee ID and creation status

### Systems Involved:

- **PandaDoc**: Digital signature platform (webhook trigger)
- **Airtable**: Applicant database (GRIT.Recruiting base)
- **n8n**: Workflow automation platform (data transformation & API orchestration)
- **TimeForge**: Workforce management system (employee record creation)

## TimeForge API Requirements

### API Endpoint Usage:

- **Primary**: `EmployeeWebService/addNewEmployee`
- **Authentication**: Basic HTTP authentication with location-specific credentials
- **Format**: JSON/REST requests

### Data Mapping:

|Source (Airtable)|TimeForge API Field|Required|Notes                   |
|-----------------|-------------------|--------|------------------------|
|First Name       |firstName          |Yes     |Direct mapping          |
|Last Name        |lastName           |Yes     |Direct mapping          |
|Email            |email              |No      |Used for TimeForge login|
|Phone            |cellnumber         |No      |Contact information     |
|Start Date       |hireDate           |No      |Format: MM/dd/yyyy      |
|Position         |employeeType       |Yes     |Mapped to E/S/M values  |
|Record ID        |internalNumber     |No      |Unique identifier       |

### Position Type Mapping:

- **Manager roles** → `employeeType: "M"`
- **Supervisor roles** → `employeeType: "S"`
- **All other positions** → `employeeType: "E"`

## Expected API Behavior

### Success Response:

```json
{
  "tf_id": 12345,
  "status": "success"
}
```

### Error Handling:

- Duplicate employee scenarios (same name combination)
- Invalid email formats
- Missing required fields
- API authentication failures

### Integration Features:

- **Error Recovery**: Failed attempts logged in Airtable for manual review
- **Status Tracking**: Real-time integration status updates
- **Audit Trail**: Complete record of all API interactions
- **Duplicate Prevention**: Checks before creation attempts

## Setup Requirements

### TimeForge Configuration Needed:

1. **Web Service Credentials**: Username/password for API access
- Location: TimeForge → Set Up → Settings
- Scope: Location-specific credentials required
1. **API Permissions**: Confirm employee creation permissions enabled
1. **Position IDs**: Document available position codes for mapping

### Security Considerations:

- Credentials stored securely in n8n environment
- HTTPS-only communication
- Webhook signature validation
- Audit logging of all transactions

## Business Benefits

### Immediate Value:

- **Elimination of Manual Entry**: No more duplicate data entry between systems
- **Faster Onboarding**: Employees have TimeForge access from day one
- **Reduced Errors**: Automated data transfer prevents transcription mistakes
- **Audit Trail**: Complete record of when employees were created and by whom

### Operational Efficiency:

- **Real-time Processing**: Employee records created within minutes of signature
- **Exception Handling**: Failed creations flagged for immediate attention
- **Scalability**: Process handles multiple simultaneous new hires
- **Consistency**: Standardized data format across all employee records

## Implementation Timeline

- **Phase 1**: TimeForge API setup and testing (1-2 days)
- **Phase 2**: n8n workflow development (2-3 days)
- **Phase 3**: End-to-end testing (1-2 days)
- **Phase 4**: Production deployment and monitoring (1 day)

## Support Requirements

### From TimeForge:

- Web service credential configuration assistance
- API endpoint testing and validation
- Documentation clarification if needed
- Troubleshooting support during initial testing

-----

This integration will streamline our employee onboarding process and ensure data consistency between our recruiting pipeline and workforce management system. We’re looking forward to working with TimeForge support to ensure a smooth implementation.