# React Coding Standards

## Code Conventions and Styles

CRITICAL: ALWAYS follow these conventions unless specified otherwise or if existing code differs:

- Use TypeScript for all new components
- Use functional components exclusively
- Implement proper prop types and interfaces
- Follow React hooks rules strictly
- Implement proper error boundaries
- Follow accessibility (a11y) best practices

## Component Standards

### Functional Components

```typescript
import { type PropsWithChildren } from "react";
import type { User } from "./types";

interface Props {
  user: User;
  onUpdate?: (user: User) => void;
  className?: string;
}

export function UserProfile({ 
  user, 
  onUpdate,
  className,
  children 
}: PropsWithChildren<Props>) {
  return (
    <div className={className}>
      <h1>{user.name}</h1>
      {children}
    </div>
  );
}
```

### Custom Hooks

```typescript
function useDebounce<T>(value: T, delay: number): T {
  const [debouncedValue, setDebouncedValue] = useState<T>(value);

  useEffect(() => {
    const timer = setTimeout(() => setDebouncedValue(value), delay);
    return () => clearTimeout(timer);
  }, [value, delay]);

  return debouncedValue;
}
```

### Error Boundaries

```typescript
interface ErrorBoundaryProps {
  fallback: React.ReactNode;
  children: React.ReactNode;
}

interface ErrorBoundaryState {
  hasError: boolean;
  error?: Error;
}

class ErrorBoundary extends React.Component<
  ErrorBoundaryProps,
  ErrorBoundaryState
> {
  constructor(props: ErrorBoundaryProps) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error: Error): ErrorBoundaryState {
    return { hasError: true, error };
  }

  componentDidCatch(error: Error, errorInfo: React.ErrorInfo): void {
    console.error("ErrorBoundary caught an error:", error, errorInfo);
  }

  render(): React.ReactNode {
    if (this.state.hasError) {
      return this.props.fallback;
    }

    return this.props.children;
  }
}
```

## Type Definitions

### Props and State

```typescript
// types.ts
export interface User {
  id: string;
  name: string;
  email: string;
  preferences?: UserPreferences;
}

export interface UserPreferences {
  theme: "light" | "dark";
  notifications: boolean;
}

export interface UserProfileProps {
  user: User;
  onUpdate?: (user: User) => Promise<void>;
  className?: string;
}

export interface UserState {
  isEditing: boolean;
  isDirty: boolean;
  errors: Record<string, string>;
}
```

### API Models

```typescript
// api-types.ts
export interface ApiResponse<T> {
  data: T;
  meta: {
    timestamp: string;
    requestId: string;
  };
}

export interface ApiError {
  code: string;
  message: string;
  details?: unknown;
}

export interface UserUpdateRequest {
  name?: string;
  email?: string;
  preferences?: Partial<UserPreferences>;
}

export interface UserUpdateResponse {
  user: User;
  updated: string[];
}
```

## State Management

### Context Creation

```typescript
// UserContext.tsx
interface UserContextValue {
  user: User | null;
  setUser: (user: User | null) => void;
  isLoading: boolean;
  error: Error | null;
}

const UserContext = createContext<UserContextValue | null>(null);

export function UserProvider({ children }: PropsWithChildren) {
  const [user, setUser] = useState<User | null>(null);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    fetchUser()
      .then(setUser)
      .catch(setError)
      .finally(() => setIsLoading(false));
  }, []);

  const value = useMemo(
    () => ({ user, setUser, isLoading, error }),
    [user, isLoading, error]
  );

  return (
    <UserContext.Provider value={value}>
      {children}
    </UserContext.Provider>
  );
}

export function useUser() {
  const context = useContext(UserContext);
  if (!context) {
    throw new Error("useUser must be used within UserProvider");
  }
  return context;
}
```

## Form Handling

```typescript
interface FormData {
  email: string;
  password: string;
}

interface FormErrors {
  email?: string;
  password?: string;
}

function LoginForm() {
  const [data, setData] = useState<FormData>({
    email: "",
    password: ""
  });
  const [errors, setErrors] = useState<FormErrors>({});
  const [isSubmitting, setIsSubmitting] = useState(false);

  const validate = useCallback((data: FormData): FormErrors => {
    const errors: FormErrors = {};
    
    if (!data.email) {
      errors.email = "Email is required";
    } else if (!/^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i.test(data.email)) {
      errors.email = "Invalid email address";
    }

    if (!data.password) {
      errors.password = "Password is required";
    } else if (data.password.length < 8) {
      errors.password = "Password must be at least 8 characters";
    }

    return errors;
  }, []);

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    const validationErrors = validate(data);
    
    if (Object.keys(validationErrors).length > 0) {
      setErrors(validationErrors);
      return;
    }

    setIsSubmitting(true);
    try {
      await submitForm(data);
      // Handle success
    } catch (error) {
      // Handle error
    } finally {
      setIsSubmitting(false);
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="email"
        value={data.email}
        onChange={e => setData(prev => ({ 
          ...prev, 
          email: e.target.value 
        }))}
        aria-invalid={!!errors.email}
        aria-describedby={errors.email ? "email-error" : undefined}
      />
      {errors.email && (
        <span id="email-error" role="alert">
          {errors.email}
        </span>
      )}
      {/* Similar pattern for password */}
      <button 
        type="submit" 
        disabled={isSubmitting}
      >
        {isSubmitting ? "Submitting..." : "Submit"}
      </button>
    </form>
  );
}
```

## Testing Standards

### Component Testing

```typescript
import { render, screen, fireEvent, waitFor } from "@testing-library/react";
import { vi } from "vitest";
import { UserProfile } from "./UserProfile";

describe("UserProfile", () => {
  const user = {
    id: "1",
    name: "John Doe",
    email: "john@example.com"
  };

  it("renders user information", () => {
    render(<UserProfile user={user} />);
    
    expect(screen.getByText(user.name)).toBeInTheDocument();
    expect(screen.getByText(user.email)).toBeInTheDocument();
  });

  it("handles update correctly", async () => {
    const onUpdate = vi.fn();
    render(<UserProfile user={user} onUpdate={onUpdate} />);
    
    fireEvent.click(screen.getByRole("button", { name: /edit/i }));
    
    const nameInput = screen.getByLabelText(/name/i);
    fireEvent.change(nameInput, { target: { value: "Jane Doe" } });
    
    fireEvent.click(screen.getByRole("button", { name: /save/i }));
    
    await waitFor(() => {
      expect(onUpdate).toHaveBeenCalledWith({
        ...user,
        name: "Jane Doe"
      });
    });
  });
});
```

### Hook Testing

```typescript
import { renderHook, act } from "@testing-library/react-hooks";
import { useCounter } from "./useCounter";

describe("useCounter", () => {
  it("increments counter", () => {
    const { result } = renderHook(() => useCounter());
    
    act(() => {
      result.current.increment();
    });
    
    expect(result.current.count).toBe(1);
  });
});
```

## Performance Guidelines

1. **Memoization**:
   - Use `useMemo` for expensive computations
   - Use `useCallback` for function props
   - Use `memo` for pure components that re-render frequently

2. **Code Splitting**:
   - Use dynamic imports for routes
   - Lazy load heavy components
   - Prefetch critical routes

3. **State Management**:
   - Keep state as local as possible
   - Use context sparingly
   - Consider using state machines for complex state

4. **Rendering Optimization**:
   - Avoid unnecessary re-renders
   - Use virtual lists for long lists
   - Implement proper key props
   - Use CSS transforms for animations

## Accessibility Standards

1. **Semantic HTML**:
   - Use proper heading hierarchy
   - Use semantic elements (`nav`, `main`, `article`, etc.)
   - Use ARIA attributes when needed

2. **Keyboard Navigation**:
   - Ensure all interactive elements are focusable
   - Implement proper tab order
   - Provide keyboard shortcuts for power users

3. **Screen Readers**:
   - Add aria-labels where needed
   - Use aria-live for dynamic content
   - Test with screen readers

4. **Color Contrast**:
   - Ensure sufficient color contrast
   - Don't rely on color alone
   - Support high contrast modes
