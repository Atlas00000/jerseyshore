# Enhanced Logging System

## Overview

Comprehensive logging system for client, server, and Docker services with multiple transports, performance tracking, and error aggregation.

## Client Logging

### Features
- **Multiple Transports**: Console, LocalStorage, and Remote (configurable)
- **Performance Tracking**: Built-in performance monitoring utilities
- **Error Tracking**: Error aggregation and statistics
- **Structured Logging**: JSON and text formats
- **Log Levels**: debug, info, warn, error (environment-based)

### Usage

```typescript
import { logger } from '@/lib/logger';

// Basic logging
logger.info('User logged in', { context: 'Auth', metadata: { userId: '123' } });
logger.error('Failed to load data', { context: 'DataLoader', error: new Error('Network error') });

// Performance tracking
logger.performance.start('loadUserData');
// ... do work ...
logger.performance.end('loadUserData');

// Async performance tracking
await logger.performance.measureAsync('fetchData', async () => {
  return await fetch('/api/data');
});
```

### Configuration

Environment variables:
- `NEXT_PUBLIC_LOG_LEVEL`: Log level (debug, info, warn, error)
- `NEXT_PUBLIC_LOG_ENDPOINT`: Remote logging endpoint (optional)

## Server Logging

### Features
- **Request Logging**: Automatic HTTP request/response logging with timing
- **File Transport**: Log rotation with configurable size and file count
- **Structured Output**: JSON or text format
- **Request IDs**: Unique request tracking
- **Error Middleware**: Enhanced error logging with full context

### Usage

```typescript
import { logger } from './utils/logger';

logger.info('Server started', { context: 'Server' });
logger.error('Database connection failed', { 
  context: 'Database',
  error: new Error('Connection timeout'),
  requestId: 'req-123'
});
```

### Configuration

Environment variables:
- `LOG_LEVEL`: Log level (debug, info, warn, error)
- `LOG_DIR`: Directory for log files (default: `./logs`)
- `LOG_FORMAT`: Format (json or text)
- `ENABLE_FILE_LOGGING`: Enable file transport (true/false)

## Docker Logging

### Configuration

Docker services are configured with:
- **JSON File Driver**: Structured log output
- **Log Rotation**: Max size 10MB, keep 3 files
- **Labels**: Service identification for log aggregation

### Viewing Logs

```bash
# View all container logs
docker-compose logs

# View specific service logs
docker-compose logs postgres
docker-compose logs redis

# Follow logs in real-time
docker-compose logs -f

# View last 100 lines
docker-compose logs --tail=100
```

## Error Tracking

### Features
- Automatic error aggregation
- Error statistics by component
- Error frequency tracking
- Recent error history

### Usage

```typescript
import { errorTracker } from '@/lib/logging/errorTracker';

// Track error
const errorId = errorTracker.trackError(error, {
  component: 'UserProfile',
  action: 'loadData',
});

// Get statistics
const stats = errorTracker.getErrorStats();
```

## Performance Monitoring

### Features
- Function execution timing
- Async operation tracking
- Performance summaries
- Built-in performance marks

### Usage

```typescript
import { performanceTracker } from '@/lib/logging/performance';

// Track sync function
const result = performanceTracker.measureSync('processData', () => {
  return processData();
});

// Track async function
const result = await performanceTracker.measureAsync('fetchData', async () => {
  return await fetch('/api/data');
});

// Get summary
const summary = performanceTracker.getSummary();
```

## Log Files

### Server Logs
- Location: `server/logs/app.log`
- Rotation: Automatic when file exceeds 10MB
- Format: JSON or text (configurable)
- Retention: 5 rotated files

### Client Logs
- Location: Browser localStorage (development)
- Key: `app_logs`
- Retention: Last 50 entries
- Export: `logger.exportLogs()`

## Best Practices

1. **Use Context**: Always provide context for better log filtering
2. **Include Metadata**: Add relevant metadata for debugging
3. **Error Objects**: Pass Error objects, not just messages
4. **Performance Tracking**: Use for critical operations
5. **Log Levels**: Use appropriate levels (debug for dev, warn/error for prod)

## Integration

### React Error Boundaries
Error boundaries automatically log errors with full context:
- Component stack traces
- Error metadata
- Session tracking

### Express Middleware
Request logging middleware automatically logs:
- Request method and URL
- Response status and timing
- Request IDs for tracing
- Error context

