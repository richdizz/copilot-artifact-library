# TypeScript Coding Standards

## Code Conventions and Styles

CRITICAL: ALWAYS follow these conventions unless specified otherwise or if existing code differs:

- **SOLID Principles**: Prefer SOLID principles
- **Naming**:
  - Use short, descriptive names
  - Interface names: `PascalCase` with `I` prefix (e.g., `IUserService`)
  - Type names: `PascalCase` (e.g., `UserResponse`)
  - Class names: `PascalCase` (e.g., `UserService`)
  - Method names: `camelCase` (e.g., `getUserById`)
  - Variable names: `camelCase`
  - Constants: `UPPER_SNAKE_CASE` for true constants, `camelCase` for const variables
  - File names: `kebab-case.ts` or match the primary export name
  - React Components: `PascalCase.tsx`
- **Types & Interfaces**:
  - Prefer interfaces for object shapes that will be implemented
  - Prefer type aliases for unions, intersections, and simple object shapes
  - ALWAYS explicitly define return types on public functions/methods
  - Use generics for reusable components and functions
- **String Literals**:
  - ALWAYS use double quotes (`"`) for string literals
  - Use template literals for string interpolation
- **Arrays**:
  - Prefer `Array<T>` for complex types
  - Use `T[]` for simple types
  - ALWAYS specify array types explicitly
- **Object & Array Destructuring**:
  - Prefer destructuring for props and state
  - Use rest parameters when appropriate
- **Exports & Imports**:
  - Prefer named exports over default exports
  - Group imports by type (external, internal, types)
  - Use absolute imports with path aliases when configured
- **Null & Undefined**:
  - Use `undefined` for optional values
  - Use `null` for intentionally empty values
  - ALWAYS use optional chaining (`?.`) and nullish coalescing (`??`)
- **Async Code**:
  - ALWAYS use `async/await` over `.then()`
  - Handle errors with try/catch
  - Use Promise.all for parallel operations
- **Error Handling**:
  - Create custom error classes when needed
  - ALWAYS include error context in catches
  - Use discriminated unions for error states

### TypeScript Example

```typescript
// Types and interfaces at the top of the file
interface IUserService {
  getUserById(id: string): Promise<UserResponse>;
  updateUser(user: UpdateUserRequest): Promise<void>;
}

type UserResponse = {
  id: string;
  name: string;
  email: string;
  preferences?: UserPreferences;
};

type UpdateUserRequest = Omit<UserResponse, "id">;

interface UserPreferences {
  theme: "light" | "dark";
  notifications: boolean;
}

// Error class definition
class UserServiceError extends Error {
  constructor(
    message: string,
    public readonly code: number,
    public readonly context?: Record<string, unknown>
  ) {
    super(message);
    this.name = "UserServiceError";
  }
}

// Service implementation
export class UserService implements IUserService {
  private readonly apiClient: ApiClient;
  private static readonly CACHE_TTL = 300_000; // 5 minutes in milliseconds

  constructor(apiClient: ApiClient) {
    this.apiClient = apiClient;
  }

  public async getUserById(id: string): Promise<UserResponse> {
    try {
      const cachedUser = await this.getCachedUser(id);
      if (cachedUser) {
        return cachedUser;
      }

      const response = await this.apiClient.get<UserResponse>(`/users/${id}`);
      await this.cacheUser(id, response);

      return response;
    } catch (error) {
      throw new UserServiceError(
        "Failed to fetch user",
        500,
        { userId: id, originalError: error }
      );
    }
  }

  public async updateUser(user: UpdateUserRequest): Promise<void> {
    const { name, email, preferences } = user;

    try {
      await this.apiClient.put("/users", {
        name,
        email,
        preferences: preferences ?? { theme: "light", notifications: true }
      });
    } catch (error) {
      throw new UserServiceError(
        "Failed to update user",
        500,
        { user, originalError: error }
      );
    }
  }

  private async getCachedUser(id: string): Promise<UserResponse | undefined> {
    // Implementation
    return undefined;
  }

  private async cacheUser(id: string, user: UserResponse): Promise<void> {
    // Implementation
  }
}

// React Component Example
interface UserProfileProps {
  userId: string;
  onUpdate?: (user: UserResponse) => void;
}

type UserProfileState = {
  status: "loading" | "error" | "success";
  error?: Error;
  user?: UserResponse;
};

export const UserProfile: React.FC<UserProfileProps> = ({ userId, onUpdate }) => {
  const [state, setState] = useState<UserProfileState>({ status: "loading" });
  const userService = useUserService();

  useEffect(() => {
    const loadUser = async (): Promise<void> => {
      try {
        const user = await userService.getUserById(userId);
        setState({ status: "success", user });
      } catch (error) {
        setState({
          status: "error",
          error: error instanceof Error ? error : new Error("Unknown error")
        });
      }
    };

    void loadUser();
  }, [userId, userService]);

  if (state.status === "loading") {
    return <LoadingSpinner />;
  }

  if (state.status === "error") {
    return <ErrorMessage error={state.error} />;
  }

  const { user } = state;

  return (
    <div className="user-profile">
      <h1>{user.name}</h1>
      <p>{user.email}</p>
      {user.preferences && (
        <PreferencesPanel
          preferences={user.preferences}
          onChange={async (newPreferences) => {
            await userService.updateUser({
              ...user,
              preferences: newPreferences
            });
            onUpdate?.(user);
          }}
        />
      )}
    </div>
  );
};
```

## Code Documentation

- All public interfaces, types, and functions MUST have JSDoc comments
- Use TypeScript-specific JSDoc tags when appropriate (`@template`, `@typedef`, etc.)
- Include examples in documentation for complex functions
- Document thrown errors and side effects
- Use markdown in JSDoc comments for better readability

### JSDoc Example

```typescript
/**
 * Represents a service for managing user data.
 * @template T - The type of the user data
 */
export interface IGenericUserService<T extends BaseUser> {
  /**
   * Retrieves a user by their unique identifier.
   * 
   * @param id - The unique identifier of the user
   * @returns A promise that resolves with the user data
   * @throws {UserServiceError} When the user cannot be found or the request fails
   * 
   * @example
   * ```typescript
   * const userService = new UserService(apiClient);
   * const user = await userService.getUserById("123");
   * console.log(user.name);
   * ```
   */
  getUserById(id: string): Promise<T>;

  /**
   * Updates a user's information.
   * 
   * @param user - The updated user data
   * @throws {UserServiceError} When the update operation fails
   */
  updateUser(user: Omit<T, "id">): Promise<void>;
}
```

## Testing Standards

- Use Jest or Vitest as the testing framework
- Write tests for all public functions and components
- Follow the Arrange-Act-Assert pattern
- Use meaningful test descriptions
- Mock external dependencies
- Test error cases

### Test Example

```typescript
describe("UserService", () => {
  let userService: UserService;
  let mockApiClient: jest.Mocked<ApiClient>;

  beforeEach(() => {
    mockApiClient = {
      get: jest.fn(),
      put: jest.fn()
    } as any;

    userService = new UserService(mockApiClient);
  });

  describe("getUserById", () => {
    it("should return user when found", async () => {
      // Arrange
      const mockUser: UserResponse = {
        id: "123",
        name: "John Doe",
        email: "john@example.com"
      };
      mockApiClient.get.mockResolvedValueOnce(mockUser);

      // Act
      const result = await userService.getUserById("123");

      // Assert
      expect(result).toEqual(mockUser);
      expect(mockApiClient.get).toHaveBeenCalledWith("/users/123");
    });

    it("should throw UserServiceError when API call fails", async () => {
      // Arrange
      mockApiClient.get.mockRejectedValueOnce(new Error("Network error"));

      // Act & Assert
      await expect(userService.getUserById("123")).rejects.toThrow(
        UserServiceError
      );
    });
  });
});
```
