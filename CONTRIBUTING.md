# Contributing to Brokerr

Thank you for your interest in contributing to Brokerr! This document provides guidelines and instructions for contributing.

## Development Setup

Follow the "Getting Started" section in README.md to set up your development environment.

## Code Standards

### JavaScript/React Code Style

```javascript
// Use functional components with hooks
export function ComponentName() {
  const [state, setState] = useState(null);

  useEffect(() => {
    // Initialize or fetch data
  }, []);

  return (
    <div className="p-4">
      {/* JSX content */}
    </div>
  );
}
```

### Naming Conventions

- **Files**: PascalCase for components (e.g., `Dashboard.jsx`), camelCase for utilities
- **Functions**: camelCase (e.g., `getUserProfile`)
- **Constants**: UPPER_SNAKE_CASE (e.g., `MAX_TRANSACTION_AMOUNT`)
- **CSS Classes**: Tailwind utilities only, no custom classes without `@apply`

### Database Queries

Always use the database API:

```javascript
// Good
import { dbAPI } from '@/api/supabaseClient';
const { data, error } = await dbAPI.getUserProfile(email);

// Bad - direct Supabase calls with hardcoded logic
const { data } = await supabase.from('users').select('*');
```

### Error Handling

```javascript
try {
  const { data, error } = await dbAPI.getUserProfile(email);
  if (error) throw new Error(error);
  return data;
} catch (error) {
  console.error('Failed to get user profile:', error);
  return null;
}
```

### Component Organization

```
components/
├── layout/           # Layout wrapper components
├── ui/              # Reusable UI components
└── [feature]/       # Feature-specific components
```

## Git Workflow

### Branch Naming

- `feature/user-dashboard` - New features
- `fix/auth-bug` - Bug fixes
- `docs/api-docs` - Documentation
- `refactor/database-queries` - Code refactoring

### Commit Messages

```
feat: Add user authentication
fix: Resolve race condition in portfolio updates
docs: Update API documentation
refactor: Simplify email service
test: Add tests for transaction logic

Use conventional commits format above.
```

### Pull Requests

1. Create a branch from `main`
2. Make your changes
3. Push to your fork
4. Open a PR with a clear description
5. Ensure CI passes (linting, build)
6. Request review
7. Address feedback
8. Merge when approved

## Common Tasks

### Adding a New Page

1. Create component in `src/pages/NewPage.jsx`
2. Import in `src/App.jsx`
3. Add route in Routes component
4. Add to sidebar navigation in `src/components/layout/Sidebar.jsx`
5. Test navigation and authentication

### Adding a Database Table

1. Update `src/schema.sql` with new table definition
2. Add RLS policies for security
3. Run SQL in Supabase editor
4. Add CRUD functions to `src/api/supabaseClient.js`
5. Update `src/api/base44Client.js` if needed
6. Test all operations

### Sending an Email

1. Create template in `src/lib/emailTemplates.js`
2. Add function to `src/api/emailService.js`
3. Call from component: `await sendEmailFunction(email, data)`
4. Verify email was logged in Supabase

### Adding Admin-Only Feature

1. Create component with role check
2. Protect route with `<AdminRoute>` in App.jsx
3. Add RLS policy for admin-only access
4. Test that non-admins cannot access

## Testing

### Manual Testing Checklist

Before submitting a PR, test:

- [ ] Feature works in development
- [ ] No console errors
- [ ] Authentication/authorization works correctly
- [ ] Database queries return expected data
- [ ] Email notifications send (check logs)
- [ ] Responsive design on mobile
- [ ] No broken links or navigation
- [ ] Admin routes are protected
- [ ] Performance is acceptable

### Browser Testing

Test in:
- Chrome (latest)
- Firefox (latest)
- Safari (latest)
- Mobile Safari (iOS)
- Chrome Mobile (Android)

## Performance Guidelines

### Frontend Performance

- Keep components small and focused
- Use proper React key props in lists
- Avoid unnecessary re-renders with useMemo/useCallback
- Lazy load images
- Code-split large pages with React.lazy()

### Database Performance

- Use indexes on frequently queried columns
- Avoid N+1 queries
- Paginate large result sets
- Use RLS for efficient filtering

## Security Checklist

- [ ] No hardcoded credentials or API keys
- [ ] All user input validated
- [ ] Sensitive data not logged to console
- [ ] SQL queries use parameterized queries
- [ ] Admin routes require authentication
- [ ] RLS policies enforced on all tables
- [ ] No exposed secrets in error messages

## Documentation

### Inline Comments

```javascript
// Use comments for WHY, not WHAT
// GOOD
// Cache results for 5 minutes to reduce DB load
const CACHE_DURATION = 5 * 60 * 1000;

// BAD
// Set cache to 5 minutes
const CACHE_DURATION = 300000;
```

### Function Documentation

```javascript
/**
 * Fetches user profile and updates local state
 * @param {string} email - User email address
 * @returns {Promise<Object>} User profile data
 * @throws {Error} If user not found
 */
async function getUserProfile(email) {
  // Implementation
}
```

## Reporting Bugs

When reporting bugs, include:

1. **Description**: What is the issue?
2. **Steps to Reproduce**: How to trigger the bug?
3. **Expected Behavior**: What should happen?
4. **Actual Behavior**: What actually happens?
5. **Screenshots/Video**: If applicable
6. **Environment**: Browser, OS, versions
7. **Logs**: Console errors, server logs

## Feature Requests

Include:

1. **Title**: Clear, concise description
2. **Use Case**: Why is this needed?
3. **Proposed Solution**: How should it work?
4. **Alternatives**: Other approaches?
5. **Impact**: Who benefits? Impact on system?

## Questions?

- Check README.md for general information
- Review existing code for patterns
- Open an Issue to discuss ideas
- Email: dev@brokerr.io

Thank you for contributing!
