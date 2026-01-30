# staxless test

> Run the test suite.

Executes Jest tests across the project.

---

## Usage

```bash
staxless test [options]
```

## Options

| Option | Description |
|--------|-------------|
| `--watch` | Watch mode — rerun on changes |
| `--coverage` | Generate coverage report |
| `--path <pattern>` | Run tests matching pattern |

---

## Examples

### Run all tests

```bash
staxless test
```

### Watch mode

```bash
staxless test --watch
```

### With coverage

```bash
staxless test --coverage
```

### Run specific tests

```bash
# By file path
staxless test --path auth-service

# By test name
staxless test --path "user login"
```

---

## Test structure

Tests live alongside source files or in `__tests__` directories:

```
src/
├── services/
│   ├── user-service.ts
│   └── user-service.test.ts
└── __tests__/
    └── integration/
        └── auth.test.ts
```

---

## Writing tests

```typescript
// user-service.test.ts
import { UserService } from './user-service';

describe('UserService', () => {
  it('creates a user', async () => {
    const user = await UserService.create({
      email: 'test@example.com',
      name: 'Test User',
    });

    expect(user.email).toBe('test@example.com');
  });
});
```

---

## CI Integration

```yaml
- name: Test
  run: staxless test --coverage

- name: Upload coverage
  uses: codecov/codecov-action@v3
```

---

<p align="center">
  <a href="./README.md">← CLI Reference</a>
</p>
