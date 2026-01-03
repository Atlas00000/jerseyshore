# 🤝 Contributing Guide

<div align="center">

**Guidelines for contributing to the Shirt Configurator project**

[Code Style](#-code-style) • [Git Workflow](#-git-workflow) • [Testing](#-testing) • [Pull Requests](#-pull-requests)

</div>

---

## 📋 Table of Contents

- [Getting Started](#-getting-started)
- [Development Workflow](#-development-workflow)
- [Code Style](#-code-style)
- [Component Guidelines](#-component-guidelines)
- [Testing Standards](#-testing-standards)
- [Git Workflow](#-git-workflow)
- [Pull Request Process](#-pull-request-process)
- [Best Practices](#-best-practices)

---

## 🚀 Getting Started

### Prerequisites

- **Node.js** 18+
- **pnpm** 8+
- **Git** 2.30+
- **Code Editor** (VS Code recommended)

### Initial Setup

```bash
# Fork and clone the repository
git clone <your-fork-url>
cd shirt_config

# Install dependencies
cd client && pnpm install
cd ../server && pnpm install

# Start development servers
cd client && pnpm dev
cd ../server && pnpm dev
```

---

## 🔄 Development Workflow

### 1. Create a Branch

```bash
# Create feature branch
git checkout -b feature/your-feature-name

# Or bugfix branch
git checkout -b fix/your-bug-description
```

### 2. Make Changes

- Follow code style guidelines
- Write tests for new features
- Update documentation as needed
- Ensure builds pass

### 3. Commit Changes

```bash
# Stage changes
git add .

# Commit with descriptive message
git commit -m "feat: add material preview tooltip"
```

### 4. Push and Create PR

```bash
# Push to your fork
git push origin feature/your-feature-name

# Create pull request on GitHub
```

---

## 💻 Code Style

### TypeScript

**General Rules:**
- Use TypeScript for all new code
- Avoid `any` type - use proper types
- Use interfaces for object shapes
- Use enums for constants
- Prefer `const` over `let`

**Example:**
```typescript
// ✅ Good
interface UserProps {
  name: string;
  age: number;
}

const user: UserProps = {
  name: 'John',
  age: 30,
};

// ❌ Bad
const user: any = {
  name: 'John',
  age: 30,
};
```

### React Components

**Component Structure:**
```typescript
'use client';

/**
 * Component Description
 * Brief explanation of what the component does
 */

import { useState } from 'react';
import { Button } from '@/components/ui/Button';

interface ComponentProps {
  title: string;
  onAction: () => void;
}

export function Component({ title, onAction }: ComponentProps) {
  const [state, setState] = useState<string>('');

  return (
    <div>
      <h1>{title}</h1>
      <Button onClick={onAction}>Action</Button>
    </div>
  );
}
```

**Naming Conventions:**
- **Components**: PascalCase (`MaterialLibrary.tsx`)
- **Hooks**: camelCase with `use` prefix (`useMaterialPreview.ts`)
- **Utilities**: camelCase (`materialManager.ts`)
- **Types**: PascalCase (`MaterialProperties.ts`)
- **Constants**: UPPER_SNAKE_CASE (`MAX_FILE_SIZE`)

### File Organization

```
component/
├── ComponentName.tsx      # Main component
├── ComponentName.test.tsx # Tests
├── ComponentName.stories.tsx # Storybook (if used)
└── index.ts               # Exports
```

### Import Order

```typescript
// 1. React and Next.js
import { useState, useEffect } from 'react';
import { useRouter } from 'next/navigation';

// 2. Third-party libraries
import { motion } from 'framer-motion';
import * as THREE from 'three';

// 3. Internal components
import { Button } from '@/components/ui/Button';
import { Card } from '@/components/ui/Card';

// 4. Internal utilities
import { logger } from '@/lib/logger';
import { useConfiguratorStore } from '@/stores/configuratorStore';

// 5. Types
import { Material } from '@/types/materials';

// 6. Styles (if needed)
import './ComponentName.css';
```

---

## 🧩 Component Guidelines

### Component Principles

1. **Single Responsibility** - One component, one purpose
2. **Composition** - Build complex components from simple ones
3. **Reusability** - Design for reuse across the app
4. **Accessibility** - Include ARIA labels and keyboard support
5. **Performance** - Optimize renders and use memoization

### Component Patterns

**Presentational Component:**
```typescript
// Simple, reusable UI component
export function Button({ children, onClick }: ButtonProps) {
  return (
    <button onClick={onClick} className="btn">
      {children}
    </button>
  );
}
```

**Container Component:**
```typescript
// Manages state and business logic
export function MaterialLibrary() {
  const { materials, setMaterial } = useConfiguratorStore();
  // ... logic
  return <MaterialList materials={materials} onSelect={setMaterial} />;
}
```

**Custom Hook:**
```typescript
// Extract reusable logic
export function useMaterialPreview(materialId: string) {
  const [preview, setPreview] = useState<string | null>(null);
  // ... logic
  return { preview, loading };
}
```

### Props Guidelines

- **Use TypeScript interfaces** for props
- **Keep props minimal** - avoid prop drilling
- **Use default values** for optional props
- **Document complex props** with JSDoc

```typescript
interface MaterialSwatchProps {
  material: Material;
  isSelected: boolean;
  onSelect: () => void;
  onDoubleClick?: () => void; // Optional
  size?: 'sm' | 'md' | 'lg'; // With default
}

export function MaterialSwatch({
  material,
  isSelected,
  onSelect,
  onDoubleClick,
  size = 'md', // Default value
}: MaterialSwatchProps) {
  // ...
}
```

---

## 🧪 Testing Standards

### Testing Requirements

**All new features must include:**

1. **Unit Tests** - Test individual functions/components
2. **Integration Tests** - Test component interactions
3. **Type Tests** - Ensure TypeScript types are correct

### Test Structure

```typescript
describe('ComponentName', () => {
  describe('rendering', () => {
    it('renders with required props', () => {
      render(<ComponentName title="Test" />);
      expect(screen.getByText('Test')).toBeInTheDocument();
    });
  });

  describe('interactions', () => {
    it('calls onAction when clicked', () => {
      const handleAction = jest.fn();
      render(<ComponentName onAction={handleAction} />);
      fireEvent.click(screen.getByRole('button'));
      expect(handleAction).toHaveBeenCalledTimes(1);
    });
  });

  describe('edge cases', () => {
    it('handles missing data gracefully', () => {
      render(<ComponentName data={null} />);
      expect(screen.getByText('No data')).toBeInTheDocument();
    });
  });
});
```

### Test Coverage

- **Minimum**: 80% code coverage
- **Critical paths**: 100% coverage
- **New code**: Must have tests before merge

### Running Tests

```bash
# Run all tests
pnpm test

# Run with coverage
pnpm test:coverage

# Run specific test
pnpm test ComponentName.test.tsx

# Watch mode
pnpm test:watch
```

---

## 📝 Git Workflow

### Branch Naming

```
feature/description    # New features
fix/description        # Bug fixes
refactor/description   # Code refactoring
docs/description      # Documentation
test/description       # Test additions
chore/description     # Maintenance tasks
```

### Commit Messages

Follow [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation
- `style`: Code style (formatting)
- `refactor`: Code refactoring
- `test`: Adding tests
- `chore`: Maintenance

**Examples:**
```bash
feat(materials): add material preview tooltip
fix(prints): resolve print placement offset issue
docs(readme): update setup instructions
refactor(store): optimize state updates
test(components): add MaterialLibrary tests
```

### Commit Guidelines

- **One logical change per commit**
- **Write clear, descriptive messages**
- **Reference issues** when applicable: `fix: resolve #123`
- **Keep commits focused** - avoid "fix everything" commits

---

## 🔀 Pull Request Process

### Before Submitting

- [ ] Code follows style guidelines
- [ ] All tests pass
- [ ] New tests added for new features
- [ ] Documentation updated
- [ ] No console errors or warnings
- [ ] Build succeeds (`pnpm build`)

### PR Template

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Feature
- [ ] Bug fix
- [ ] Documentation
- [ ] Refactoring

## Testing
- [ ] Unit tests added/updated
- [ ] Integration tests added/updated
- [ ] Manual testing completed

## Screenshots (if applicable)
Add screenshots for UI changes

## Checklist
- [ ] Code follows style guidelines
- [ ] Self-review completed
- [ ] Comments added for complex code
- [ ] Documentation updated
```

### Review Process

1. **Automated Checks** - CI/CD runs tests and linting
2. **Code Review** - At least one approval required
3. **Testing** - Reviewer tests the changes
4. **Merge** - Squash and merge preferred

---

## ✅ Best Practices

### Code Quality

1. **Keep functions small** - Single responsibility
2. **Avoid deep nesting** - Max 3-4 levels
3. **Use meaningful names** - Self-documenting code
4. **Remove dead code** - Delete unused code
5. **Comment complex logic** - Explain "why", not "what"

### Performance

1. **Lazy load heavy components**
2. **Memoize expensive computations**
3. **Optimize re-renders** - Use React.memo when needed
4. **Bundle size** - Keep dependencies minimal
5. **Image optimization** - Use Next.js Image component

### Accessibility

1. **Semantic HTML** - Use proper elements
2. **ARIA labels** - For interactive elements
3. **Keyboard navigation** - All features keyboard accessible
4. **Color contrast** - WCAG AA compliance
5. **Focus management** - Visible focus indicators

### Security

1. **Input validation** - Validate all user inputs
2. **Sanitize data** - Prevent XSS attacks
3. **Environment variables** - Never commit secrets
4. **Dependencies** - Keep updated, audit regularly
5. **Error messages** - Don't expose sensitive info

### Documentation

1. **JSDoc comments** - Document public APIs
2. **README updates** - Keep documentation current
3. **Code comments** - Explain complex logic
4. **Type definitions** - Clear, descriptive types
5. **Examples** - Provide usage examples

---

## 🐛 Debugging

### Development Tools

- **React DevTools** - Component inspection
- **Redux DevTools** - State inspection (Zustand)
- **Three.js Inspector** - 3D scene debugging
- **Browser DevTools** - Network, console, performance

### Common Issues

**Build Fails:**
- Check TypeScript errors: `pnpm build`
- Verify imports are correct
- Check for missing dependencies

**Tests Fail:**
- Run tests individually to isolate issue
- Check test environment setup
- Verify mocks are correct

**Runtime Errors:**
- Check browser console
- Review error boundaries
- Check network requests

---

## 📚 Resources

- **[Testing Guide](./TESTING.md)** - Detailed testing documentation
- **[UI Philosophy](./client/docs/UI_PHILOSOPHY.md)** - Design system
- **[Main README](./README.md)** - Project overview
- **[Client README](./client/README.md)** - Frontend docs
- **[Server README](./server/README.md)** - Backend docs

---

## ❓ Questions?

- **Open an issue** for bugs or feature requests
- **Start a discussion** for questions
- **Check documentation** first

---

<div align="center">

**Thank you for contributing! 🎉**

[Code of Conduct](./CODE_OF_CONDUCT.md) • [License](./LICENSE)

</div>

