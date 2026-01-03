# 🧪 Testing Guide

<div align="center">

**Comprehensive Testing Strategy & Best Practices**

[Strategy](#-testing-strategy) • [Setup](#-setup) • [Examples](#-examples) • [Coverage](#-coverage)

</div>

---

## 📋 Table of Contents

- [Testing Philosophy](#-testing-philosophy)
- [Testing Strategy](#-testing-strategy)
- [Test Types](#-test-types)
- [Setup & Configuration](#-setup--configuration)
- [Writing Tests](#-writing-tests)
- [Test Examples](#-test-examples)
- [Coverage Goals](#-coverage-goals)
- [Best Practices](#-best-practices)
- [CI/CD Integration](#-cicd-integration)

---

## 🎯 Testing Philosophy

### Core Principles

1. **Test Behavior, Not Implementation** - Focus on what the code does, not how
2. **Write Tests First** - TDD when possible, tests before refactoring
3. **Keep Tests Simple** - One assertion per test when possible
4. **Fast Feedback** - Tests should run quickly
5. **Maintainable Tests** - Tests are code too, keep them clean

### Testing Pyramid

```
        /\
       /  \      E2E Tests (Few, Critical Paths)
      /────\
     /      \    Integration Tests (Some, Key Flows)
    /────────\
   /          \  Unit Tests (Many, All Components)
  /────────────\
```

---

## 🏗️ Testing Strategy

### Test Levels

#### **1. Unit Tests** (70% of tests)

Test individual components, functions, and utilities in isolation.

**What to Test:**
- Component rendering
- Function logic
- Utility functions
- State management
- Hooks

**Tools:**
- Jest
- React Testing Library
- @testing-library/react

#### **2. Integration Tests** (20% of tests)

Test component interactions and data flow.

**What to Test:**
- Component interactions
- API integration
- State store operations
- Form submissions
- User workflows

**Tools:**
- Jest
- React Testing Library
- MSW (Mock Service Worker)

#### **3. E2E Tests** (10% of tests)

Test complete user workflows from start to finish.

**What to Test:**
- Critical user paths
- Complete customization flow
- Export functionality
- Error scenarios

**Tools:**
- Playwright
- Cypress (alternative)

---

## 📦 Test Types

### Component Tests

Test React components in isolation:

```typescript
import { render, screen } from '@testing-library/react';
import { Button } from '@/components/ui/Button';

describe('Button', () => {
  it('renders with text', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByRole('button')).toHaveTextContent('Click me');
  });

  it('calls onClick when clicked', () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click</Button>);
    fireEvent.click(screen.getByRole('button'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });
});
```

### Hook Tests

Test custom React hooks:

```typescript
import { renderHook, act } from '@testing-library/react';
import { useMaterialPreview } from '@/hooks/useMaterialPreview';

describe('useMaterialPreview', () => {
  it('loads preview for valid material', async () => {
    const { result } = renderHook(() => useMaterialPreview('material-1'));
    
    await act(async () => {
      await waitFor(() => {
        expect(result.current.preview).toBeTruthy();
      });
    });
  });
});
```

### Utility Tests

Test pure functions:

```typescript
import { clampPositionToZone } from '@/types/zones';

describe('clampPositionToZone', () => {
  it('clamps position within zone bounds', () => {
    const zone = { minU: 0.2, maxU: 0.8, minV: 0.2, maxV: 0.8 };
    const position = { u: 0.1, v: 0.9 };
    const clamped = clampPositionToZone(position, zone);
    
    expect(clamped.u).toBe(0.2);
    expect(clamped.v).toBe(0.8);
  });
});
```

### API Tests

Test API endpoints:

```typescript
import request from 'supertest';
import app from '../src/server';

describe('POST /api/prints/upload', () => {
  it('uploads file successfully', async () => {
    const response = await request(app)
      .post('/api/prints/upload')
      .attach('image', testImageBuffer, 'test.png');
    
    expect(response.status).toBe(200);
    expect(response.body.success).toBe(true);
    expect(response.body.url).toContain('r2.dev');
  });

  it('rejects invalid file types', async () => {
    const response = await request(app)
      .post('/api/prints/upload')
      .attach('image', testPdfBuffer, 'test.pdf');
    
    expect(response.status).toBe(400);
    expect(response.body.error).toContain('Invalid file type');
  });
});
```

### E2E Tests

Test complete workflows:

```typescript
import { test, expect } from '@playwright/test';

test('user can customize shirt', async ({ page }) => {
  // Navigate to app
  await page.goto('http://localhost:3000');
  
  // Select component
  await page.click('[data-testid="component-body"]');
  
  // Select material
  await page.click('[data-testid="material-cotton"]');
  await expect(page.locator('[data-testid="material-preview"]')).toBeVisible();
  
  // Change color
  await page.click('[data-testid="color-picker"]');
  await page.fill('[data-testid="color-input"]', '#FF0000');
  
  // Verify changes
  await expect(page.locator('[data-testid="color-preview"]')).toHaveCSS('background-color', 'rgb(255, 0, 0)');
});
```

---

## ⚙️ Setup & Configuration

### Client Testing Setup

**Install Dependencies:**
```bash
cd client
pnpm add -D @testing-library/react @testing-library/jest-dom @testing-library/user-event jest jest-environment-jsdom @types/jest
```

**Jest Configuration** (`jest.config.js`):
```javascript
module.exports = {
  testEnvironment: 'jsdom',
  setupFilesAfterEnv: ['<rootDir>/jest.setup.js'],
  moduleNameMapper: {
    '^@/(.*)$': '<rootDir>/$1',
  },
  testMatch: ['**/__tests__/**/*.test.[jt]s?(x)', '**/?(*.)+(spec|test).[jt]s?(x)'],
  collectCoverageFrom: [
    'components/**/*.{ts,tsx}',
    'lib/**/*.{ts,tsx}',
    'hooks/**/*.{ts,tsx}',
    '!**/*.d.ts',
    '!**/node_modules/**',
  ],
};
```

**Jest Setup** (`jest.setup.js`):
```javascript
import '@testing-library/jest-dom';
import { cleanup } from '@testing-library/react';

afterEach(() => {
  cleanup();
});
```

### Server Testing Setup

**Install Dependencies:**
```bash
cd server
pnpm add -D jest @types/jest ts-jest supertest @types/supertest
```

**Jest Configuration** (`jest.config.js`):
```javascript
module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'node',
  testMatch: ['**/__tests__/**/*.test.ts'],
  collectCoverageFrom: [
    'src/**/*.ts',
    '!src/**/*.d.ts',
  ],
};
```

---

## ✍️ Writing Tests

### Test Structure

```typescript
describe('ComponentName', () => {
  // Setup (beforeEach, afterEach)
  
  describe('rendering', () => {
    it('renders correctly', () => {
      // Test rendering
    });
  });

  describe('interactions', () => {
    it('handles user input', () => {
      // Test interactions
    });
  });

  describe('edge cases', () => {
    it('handles errors gracefully', () => {
      // Test error cases
    });
  });
});
```

### Testing Patterns

#### **Arrange-Act-Assert (AAA)**

```typescript
it('updates material when selected', () => {
  // Arrange
  const material = { id: 'mat-1', name: 'Cotton' };
  const { result } = renderHook(() => useConfiguratorStore());
  
  // Act
  act(() => {
    result.current.setMaterial('body', material.id);
  });
  
  // Assert
  expect(result.current.materialMap.body).toBe(material.id);
});
```

#### **Mocking**

```typescript
// Mock external dependencies
jest.mock('@/lib/logger', () => ({
  logger: {
    info: jest.fn(),
    error: jest.fn(),
  },
}));

// Mock API calls
global.fetch = jest.fn(() =>
  Promise.resolve({
    json: () => Promise.resolve({ data: 'test' }),
  })
);
```

#### **Async Testing**

```typescript
it('loads material data', async () => {
  const { result } = renderHook(() => useMaterialLoader('mat-1'));
  
  expect(result.current.loading).toBe(true);
  
  await waitFor(() => {
    expect(result.current.loading).toBe(false);
    expect(result.current.material).toBeTruthy();
  });
});
```

---

## 📝 Test Examples

### Component Test Example

```typescript
import { render, screen, fireEvent } from '@testing-library/react';
import { MaterialSwatch } from '@/components/configurator/MaterialSwatch';

const mockMaterial = {
  id: 'mat-1',
  properties: {
    name: 'Cotton',
    category: 'fabric',
    baseColor: '#FFFFFF',
  },
};

describe('MaterialSwatch', () => {
  it('renders material thumbnail', () => {
    render(
      <MaterialSwatch
        material={mockMaterial}
        isSelected={false}
        onSelect={jest.fn()}
      />
    );
    
    expect(screen.getByRole('button')).toBeInTheDocument();
  });

  it('shows selected state', () => {
    render(
      <MaterialSwatch
        material={mockMaterial}
        isSelected={true}
        onSelect={jest.fn()}
      />
    );
    
    const button = screen.getByRole('button');
    expect(button).toHaveClass('border-accent-cyan');
  });

  it('calls onSelect when clicked', () => {
    const handleSelect = jest.fn();
    render(
      <MaterialSwatch
        material={mockMaterial}
        isSelected={false}
        onSelect={handleSelect}
      />
    );
    
    fireEvent.click(screen.getByRole('button'));
    expect(handleSelect).toHaveBeenCalledTimes(1);
  });
});
```

### Hook Test Example

```typescript
import { renderHook, act } from '@testing-library/react';
import { useConfiguratorStore } from '@/stores/configuratorStore';

describe('useConfiguratorStore', () => {
  it('initializes with default state', () => {
    const { result } = renderHook(() => useConfiguratorStore());
    
    expect(result.current.currentMode).toBe('blank');
    expect(result.current.selectedComponent).toBeNull();
  });

  it('updates mode', () => {
    const { result } = renderHook(() => useConfiguratorStore());
    
    act(() => {
      result.current.setMode('branded');
    });
    
    expect(result.current.currentMode).toBe('branded');
  });

  it('manages undo/redo history', () => {
    const { result } = renderHook(() => useConfiguratorStore());
    
    act(() => {
      result.current.setMode('branded');
      result.current.saveToHistory();
      result.current.setMode('blank');
    });
    
    expect(result.current.canUndo()).toBe(true);
    
    act(() => {
      result.current.undo();
    });
    
    expect(result.current.currentMode).toBe('branded');
  });
});
```

### API Test Example

```typescript
import request from 'supertest';
import app from '../src/server';

describe('Print Upload API', () => {
  it('uploads valid image file', async () => {
    const response = await request(app)
      .post('/api/prints/upload')
      .attach('image', Buffer.from('fake-image'), 'test.png')
      .set('Content-Type', 'multipart/form-data');
    
    expect(response.status).toBe(200);
    expect(response.body).toHaveProperty('success', true);
    expect(response.body).toHaveProperty('url');
  });

  it('rejects files over size limit', async () => {
    const largeFile = Buffer.alloc(6 * 1024 * 1024); // 6MB
    
    const response = await request(app)
      .post('/api/prints/upload')
      .attach('image', largeFile, 'large.png');
    
    expect(response.status).toBe(400);
    expect(response.body.error).toContain('too large');
  });

  it('rejects invalid file types', async () => {
    const response = await request(app)
      .post('/api/prints/upload')
      .attach('image', Buffer.from('pdf-content'), 'test.pdf');
    
    expect(response.status).toBe(400);
    expect(response.body.error).toContain('Invalid file type');
  });
});
```

---

## 📊 Coverage Goals

### Coverage Targets

| Category | Target | Current |
|----------|--------|---------|
| **Overall** | 80%+ | TBD |
| **Components** | 85%+ | TBD |
| **Utilities** | 90%+ | TBD |
| **Hooks** | 85%+ | TBD |
| **Stores** | 80%+ | TBD |
| **API Routes** | 90%+ | TBD |

### Critical Paths (100% Coverage)

- User authentication flows
- File upload handling
- State management operations
- Error handling
- Export functionality

### Coverage Reports

```bash
# Generate coverage report
pnpm test:coverage

# View HTML report
open coverage/lcov-report/index.html
```

---

## ✅ Best Practices

### Do's ✅

- **Test user behavior** - What users see and do
- **Use semantic queries** - `getByRole`, `getByLabelText`
- **Keep tests isolated** - No shared state between tests
- **Mock external dependencies** - APIs, services, libraries
- **Test edge cases** - Empty states, errors, boundaries
- **Use descriptive names** - Clear test descriptions
- **Clean up** - Remove test data, reset mocks

### Don'ts ❌

- **Don't test implementation** - Test behavior, not internals
- **Don't test third-party code** - Mock it instead
- **Don't write flaky tests** - Avoid timeouts, random data
- **Don't test everything** - Focus on critical paths
- **Don't ignore failing tests** - Fix or remove them
- **Don't test multiple things** - One assertion per test when possible

### Test Organization

```
component/
├── ComponentName.tsx
├── ComponentName.test.tsx    # Unit tests
└── ComponentName.integration.test.tsx  # Integration tests

__tests__/
├── e2e/
│   └── user-flows.test.ts   # E2E tests
└── utils/
    └── helpers.test.ts      # Utility tests
```

---

## 🔄 CI/CD Integration

### GitHub Actions

```yaml
name: Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: pnpm/action-setup@v2
      - uses: actions/setup-node@v3
        with:
          node-version: 18
          cache: 'pnpm'
      
      - name: Install dependencies
        run: pnpm install
      
      - name: Run tests
        run: pnpm test
      
      - name: Generate coverage
        run: pnpm test:coverage
      
      - name: Upload coverage
        uses: codecov/codecov-action@v3
```

### Pre-commit Hooks

```bash
# Install husky
pnpm add -D husky lint-staged

# Setup pre-commit
npx husky install
npx husky add .husky/pre-commit "pnpm lint-staged"
```

**lint-staged.config.js:**
```javascript
module.exports = {
  '*.{ts,tsx}': ['eslint --fix', 'jest --findRelatedTests'],
  '*.{json,md}': ['prettier --write'],
};
```

---

## 🛠️ Testing Tools

### Recommended Tools

- **Jest** - Test runner and assertion library
- **React Testing Library** - Component testing
- **Playwright** - E2E testing
- **MSW** - API mocking
- **@testing-library/user-event** - User interaction simulation

### Debugging Tests

```typescript
// Debug test
it('debug test', () => {
  const { debug } = render(<Component />);
  debug(); // Prints component HTML
});

// Screen debugging
screen.debug(); // Prints current screen
screen.logTestingPlaygroundURL(); // Get testing playground URL
```

---

## 📚 Additional Resources

- **[Jest Documentation](https://jestjs.io/)**
- **[React Testing Library](https://testing-library.com/react)**
- **[Playwright](https://playwright.dev/)**
- **[Testing Best Practices](https://kentcdodds.com/blog/common-mistakes-with-react-testing-library)**

---

<div align="center">

**Quality through comprehensive testing**

[Contributing](./CONTRIBUTING.md) • [Main README](./README.md)

</div>

