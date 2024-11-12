# NestJS Study Notes

## Project Structure

A typical NestJS project structure:

```
/dist             - Contains compiled JavaScript code
/node_modules     - Contains project dependencies
/src              - Contains your source code
  /tasks          - Feature module folder
    /dto          - Data Transfer Objects
    task.entity.ts
    tasks.controller.ts
    tasks.service.ts
    tasks.module.ts
  app.module.ts   - Root module
  main.ts         - Application entry point
/test            - Contains test files
```

## Core Components

### 1. Modules

- Used to organize related features together
- Each feature module encapsulates a specific functionality
- The `AppModule` is the root module that ties everything together

Example:

```typescript
// app.module.ts
@Module({
  imports: [TasksModule],
})
export class AppModule {}

// tasks.module.ts
@Module({
  controllers: [TasksController],
  providers: [TasksService],
})
export class TasksModule {}
```

### 2. Controllers

- Handle HTTP requests
- Define API endpoints
- Route requests to appropriate services
- Use decorators to define routes and HTTP methods

Example:

```typescript
@Controller('tasks')
export class TasksController {
  constructor(private tasksService: TasksService) {}

  @Get()
  getAllTasks() {
    return this.tasksService.getAllTasks();
  }

  @Post()
  createTask(@Body() createTaskDto: CreateTaskDto) {
    return this.tasksService.createTask(createTaskDto);
  }
}
```

### 3. Services

- Contain business logic
- Handle data operations
- Are injectable providers
- Follow Single Responsibility Principle

Example:

```typescript
@Injectable()
export class TasksService {
  private tasks = [];

  getAllTasks() {
    return this.tasks;
  }

  createTask(createTaskDto: CreateTaskDto) {
    const task = {
      id: Date.now(),
      ...createTaskDto,
      status: 'OPEN',
    };
    this.tasks.push(task);
    return task;
  }
}
```

### 4. DTOs (Data Transfer Objects)

- Define the shape of data for requests
- Help with validation and type checking
- Improve code readability and maintainability

Example:

```typescript
export class CreateTaskDto {
  title: string;
  description: string;
}
```

## Important Concepts

### When to Use Controllers in AppModule vs Feature Modules

1. **Feature Module Controllers (Most Common)**

- Handle specific feature-related routes
- Grouped under a specific path prefix
- Encapsulated within their feature module

```typescript
// tasks.controller.ts in TasksModule
@Controller('tasks')
export class TasksController {
  @Get()    // Route: /tasks
  @Post()   // Route: /tasks
  @Get('/:id') // Route: /tasks/:id
}
```

2. **AppModule Controllers (App-wide Features)**

- Handle application-wide routes
- Manage global endpoints
- Deal with general application concerns

```typescript
// app.controller.ts in AppModule
@Controller()
export class AppController {
  @Get() // Route: /
  getAppInfo() {
    return {
      name: 'Task Management API',
      version: '1.0.0',
      status: 'running',
    };
  }

  @Get('health') // Route: /health
  healthCheck() {
    return { status: 'ok' };
  }
}
```

### Module Organization Examples

1. **Simple Application**

```typescript
@Module({
  imports: [TasksModule],
})
export class AppModule {}
```

2. **Medium Application**

```typescript
@Module({
  imports: [
    TasksModule,
    UsersModule,
    DatabaseModule.forRoot({
      host: 'localhost',
      port: 5432,
    }),
  ],
})
export class AppModule {}
```

3. **Complex Application**

```typescript
@Module({
  imports: [
    TasksModule,
    UsersModule,
    AuthModule,
    DatabaseModule.forRoot({
      host: 'localhost',
      port: 5432,
    }),
    ConfigModule.forRoot({
      isGlobal: true,
      envFilePath: '.env',
    }),
    LoggerModule,
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

## Testing the API

### Using cURL

```bash
# Create Task
curl -X POST http://localhost:3000/tasks \
-H "Content-Type: application/json" \
-d '{"title": "Learn NestJS", "description": "Complete the tutorial project"}'

# Get All Tasks
curl http://localhost:3000/tasks

# Get Task by ID
curl http://localhost:3000/tasks/1

# Update Task Status
curl -X PATCH http://localhost:3000/tasks/1/status \
-H "Content-Type: application/json" \
-d '{"status": "IN_PROGRESS"}'

# Delete Task
curl -X DELETE http://localhost:3000/tasks/1
```

## Next Steps for Enhancement

1. **Add Data Validation**

```typescript
npm install class-validator class-transformer
```

2. **Database Integration**

```typescript
npm install @nestjs/typeorm typeorm pg
```

3. **Authentication**

```typescript
npm install @nestjs/jwt @nestjs/passport passport passport-jwt bcrypt
```

4. **API Documentation**

```typescript
npm install @nestjs/swagger swagger-ui-express
```

5. **Logging**

```typescript
npm install nest-winston winston
```

These notes cover the basic structure and concepts of NestJS, focusing on practical implementation and common use cases. For more detailed information, refer to the [official NestJS documentation](https://docs.nestjs.com/).
