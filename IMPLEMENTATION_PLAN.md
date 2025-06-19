# CRM System Implementation Plan

## 1. Project Structure

```
src/
├── app/                    # Next.js app router
│   ├── api/               # API routes
│   ├── auth/              # Authentication pages
│   ├── dashboard/         # Dashboard pages
│   ├── admin/            # Admin section
│   └── sales/            # Sales section
├── components/
│   ├── auth/             # Auth components
│   ├── dashboard/        # Dashboard components
│   ├── forms/            # Form components
│   ├── sales/            # Sales related components
│   └── ui/               # UI components (existing)
├── lib/
│   ├── auth/             # Auth utilities
│   ├── db/               # Database utilities
│   ├── excel/            # Excel handling
│   └── api/              # API utilities
└── types/                # TypeScript types

## 2. Database Schema (PostgreSQL)

### Users Table
```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  name VARCHAR(255) NOT NULL,
  role ENUM('admin', 'manager', 'sales') NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Custom Fields Table
```sql
CREATE TABLE custom_fields (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  field_type ENUM('text', 'number', 'date', 'dropdown', 'user', 'status') NOT NULL,
  options JSONB, -- For dropdown fields
  created_by INTEGER REFERENCES users(id),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Sales Opportunities Table
```sql
CREATE TABLE opportunities (
  id SERIAL PRIMARY KEY,
  title VARCHAR(255) NOT NULL,
  value DECIMAL(10,2),
  status VARCHAR(50),
  assigned_to INTEGER REFERENCES users(id),
  custom_fields JSONB,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Documents Table
```sql
CREATE TABLE documents (
  id SERIAL PRIMARY KEY,
  opportunity_id INTEGER REFERENCES opportunities(id),
  filename VARCHAR(255) NOT NULL,
  file_path VARCHAR(255) NOT NULL,
  uploaded_by INTEGER REFERENCES users(id),
  uploaded_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Audit Logs Table
```sql
CREATE TABLE audit_logs (
  id SERIAL PRIMARY KEY,
  user_id INTEGER REFERENCES users(id),
  action VARCHAR(50) NOT NULL,
  entity_type VARCHAR(50) NOT NULL,
  entity_id INTEGER NOT NULL,
  changes JSONB,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## 3. API Routes Structure

```
/api
├── auth
│   ├── login
│   ├── register
│   └── reset-password
├── users
│   ├── [id]
│   └── index
├── opportunities
│   ├── [id]
│   ├── import
│   └── index
├── custom-fields
│   ├── [id]
│   └── index
├── documents
│   ├── [id]
│   └── upload
└── dashboard
    ├── metrics
    └── reports
```

## 4. Implementation Phases

### Phase 1: Authentication & User Management
- Setup JWT authentication
- Implement login/register flows
- Create user management for admins
- Setup password reset via email

### Phase 2: Custom Fields & Opportunities
- Implement custom fields CRUD
- Create opportunities management
- Setup field validation
- Implement audit logging

### Phase 3: Excel Import/Export
- Create Excel upload component
- Implement column mapping
- Setup data validation
- Create export functionality

### Phase 4: Dashboards
- Create role-based dashboard layouts
- Implement charts and metrics
- Setup interactive filters
- Create PDF/Excel export

### Phase 5: Document Management
- Setup document upload
- Implement file storage
- Create document viewer
- Setup access controls

## 5. Security Measures

- JWT for authentication
- Role-based access control (RBAC)
- Input validation using Zod
- SQL injection prevention
- XSS protection
- Rate limiting
- CORS configuration
- Secure password hashing
- File upload validation

## 6. Frontend Components

### Authentication
- LoginForm
- RegistrationForm
- PasswordResetForm
- UserProfile

### Dashboard
- DashboardLayout
- MetricsCard
- ChartComponent
- FilterPanel
- ExportButton

### Opportunities
- OpportunityForm
- OpportunityList
- CustomFieldBuilder
- ImportWizard
- DocumentUploader

### Admin
- UserManagement
- RoleEditor
- SystemSettings
- AuditLogViewer

## 7. Testing Strategy

### Unit Tests
- Component testing
- Utility function testing
- Form validation testing

### Integration Tests
- API endpoint testing
- Database operations
- Authentication flows

### E2E Tests
- User flows
- Data import/export
- Dashboard interactions

## 8. Performance Considerations

- Implement data pagination
- Use React Query for data fetching
- Optimize database queries
- Setup proper indexing
- Implement caching where appropriate
- Optimize bundle size
- Use image optimization
- Implement lazy loading

## 9. Monitoring & Maintenance

- Setup error logging
- Implement performance monitoring
- Create backup strategy
- Plan update procedures
- Document API endpoints
- Create user documentation
