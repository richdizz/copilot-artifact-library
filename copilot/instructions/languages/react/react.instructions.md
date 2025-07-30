---
applyTo: '**/*.tsx,**/*.jsx,**/*.ts,**/*.js'
---
# React Instructions

- **CRITICAL**: You MUST ALWAYS read and follow ALL instructions from ALL files from:
    - [./react-standards.md](./react-standards.md)

## Implementation Requirements

When implementing any React-related functionality:

- You must use functional components with hooks
- You must use TypeScript for type safety
- You must implement proper error boundaries
- You must follow React best practices for performance
- You must implement proper accessibility (a11y) standards

## Table of Contents

- [Project Structure & Management](#project-structure--management)
  - [Standard Project Layout](#standard-project-layout)
  - [React Project Internal Structure](#react-project-internal-structure)
  - [Managing React Projects](#managing-react-projects)
- [Component Organization](#component-organization)
  - [Component Types](#component-types)
  - [Component Structure](#component-structure)
- [State Management](#state-management)
- [Performance Optimization](#performance-optimization)

## Project Structure & Management

### Standard Project Layout

```plaintext
src/
  assets/
    images/
    styles/
  components/
    common/
    features/
    layouts/
  hooks/
    common/
    features/
  contexts/
  services/
  types/
  utils/
  pages/ (or routes/)
  App.tsx
  main.tsx
tests/
  components/
  hooks/
  utils/
public/
  index.html
  assets/
package.json
tsconfig.json
vite.config.ts (or other config)
.env
.env.example
```

### React Project Internal Structure

Prefer feature-based organization for larger applications:

```plaintext
src/
  features/
    auth/
      components/
      hooks/
      types/
      services/
      contexts/
      utils/
    users/
      components/
      hooks/
      types/
      services/
    products/
      components/
      hooks/
      types/
      services/
  shared/
    components/
    hooks/
    types/
    utils/
```

### Managing React Projects

ALWAYS use modern React tooling:

- **Project Creation**:
  ```bash
  # Vite (Recommended)
  npm create vite@latest my-app -- --template react-ts

  # Next.js
  npx create-next-app@latest my-app --typescript
  ```

- **Dependencies**:
  ```json
  {
    "dependencies": {
      "react": "^18.2.0",
      "react-dom": "^18.2.0",
      "react-router-dom": "^6.x",
      "@tanstack/react-query": "^5.x"
    },
    "devDependencies": {
      "@types/react": "^18.2.0",
      "@types/react-dom": "^18.2.0",
      "@typescript-eslint/eslint-plugin": "^6.x",
      "@typescript-eslint/parser": "^6.x",
      "typescript": "^5.x",
      "vite": "^5.x",
      "vitest": "^1.x"
    }
  }
  ```

## Component Organization

### Component Types

1. **Page Components**:
   - Route-level components
   - Handle data fetching and state management
   - Compose feature components

2. **Feature Components**:
   - Complex UI components with business logic
   - May handle local state
   - Composed of multiple UI components

3. **UI Components**:
   - Pure presentational components
   - Fully controlled through props
   - Highly reusable

4. **Layout Components**:
   - Handle page structure
   - Manage responsive design
   - Handle navigation elements

### Component Structure

```typescript
// types.ts
export interface User {
  id: string;
  name: string;
  email: string;
}

export interface UserCardProps {
  user: User;
  onEdit?: (user: User) => void;
  className?: string;
}

// UserCard.tsx
import { memo } from "react";
import type { UserCardProps } from "./types";

export const UserCard = memo(function UserCard({ 
  user, 
  onEdit,
  className 
}: UserCardProps) {
  return (
    <div className={className}>
      <h3>{user.name}</h3>
      <p>{user.email}</p>
      {onEdit && (
        <button 
          onClick={() => onEdit(user)}
          aria-label={`Edit ${user.name}`}
        >
          Edit
        </button>
      )}
    </div>
  );
});
```

## State Management

1. **Local State**:
   ```typescript
   const [state, setState] = useState<State>({});
   ```

2. **Context**:
   ```typescript
   // UserContext.tsx
   interface UserContextType {
     user: User | null;
     setUser: (user: User | null) => void;
   }

   export const UserContext = createContext<UserContextType | null>(null);

   export function UserProvider({ children }: PropsWithChildren) {
     const [user, setUser] = useState<User | null>(null);

     return (
       <UserContext.Provider value={{ user, setUser }}>
         {children}
       </UserContext.Provider>
     );
   }
   ```

3. **Custom Hooks**:
   ```typescript
   function useUser(userId: string) {
     const queryClient = useQueryClient();
     const { data, error, isLoading } = useQuery({
       queryKey: ["user", userId],
       queryFn: () => fetchUser(userId)
     });

     const updateUser = useMutation({
       mutationFn: (userData: UserUpdateData) => updateUser(userId, userData),
       onSuccess: () => {
         queryClient.invalidateQueries({ queryKey: ["user", userId] });
       }
     });

     return {
       user: data,
       error,
       isLoading,
       updateUser: updateUser.mutate
     };
   }
   ```

## Performance Optimization

1. **Code Splitting**:
   ```typescript
   const UserProfile = lazy(() => import("./UserProfile"));
   ```

2. **Memoization**:
   ```typescript
   const memoizedValue = useMemo(() => computeValue(a, b), [a, b]);
   const memoizedCallback = useCallback((param) => doSomething(param), []);
   ```

3. **Virtual Lists**:
   ```typescript
   import { VirtualList } from "@tanstack/virtual-core";

   function UserList({ users }: { users: User[] }) {
     return (
       <VirtualList
         data={users}
         renderItem={({ item }) => <UserCard user={item} />}
       />
     );
   }
   ```

## Testing

Use React Testing Library and follow the "Testing Library Way":

```typescript
import { render, screen, fireEvent } from "@testing-library/react";
import { UserCard } from "./UserCard";

describe("UserCard", () => {
  const user = {
    id: "1",
    name: "John Doe",
    email: "john@example.com"
  };

  it("renders user information", () => {
    render(<UserCard user={user} />);
    
    expect(screen.getByText(user.name)).toBeInTheDocument();
    expect(screen.getByText(user.email)).toBeInTheDocument();
  });

  it("calls onEdit when edit button is clicked", () => {
    const onEdit = vi.fn();
    render(<UserCard user={user} onEdit={onEdit} />);
    
    fireEvent.click(screen.getByLabelText(`Edit ${user.name}`));
    expect(onEdit).toHaveBeenCalledWith(user);
  });
});
```
