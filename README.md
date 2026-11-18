# Brazier Logging System

Brazier provides a high-performance logging system with colored output, log rotation, and signal handling.
Logging is done both to file and console with color coding.

## Key Features

- **Colored logging** to console
- **Automatic log rotation**
- **Thread safety** using mutexes
- **High performance** with time caching
- **Signal handling** for graceful shutdown
- **File logging** with buffering

| Level     | Color      | Description              |
|-----------|------------|--------------------------|
| ERROR     |  Red       | Critical errors          |
| WARNING   |  Yellow    | Warnings                 |
| INFO      |  Blue      | Informational messages   |
| SUCCESS   |  Green     | Successful operations    |
| HELP      |  Dark blue | Support messages         |
| CMD       |  Purple    | Commands to use          |
| DEBUG     |            | Debug information        |

## Usage examples

```cpp
Logger::log("Database connection established", "SUCCESS");

std::string username = "john_doe";
Logger::log("User " + username + " logged in", "INFO");
Logger::log("Debugging username", "DEBUG");

// It's similar to the one above, but the level doesn't need to be written manually
Logger::success("Database connection established");
Logger::info("User " + username + " logged in");
Logger::debug("Debugging username");

try {
    
} catch (const std::exception& e) {
    Logger::error(std::string("Exception: ") + e.what());
}
```

## Installation

This library is distributed via **vcpkg** as the standalone `brazier-logger` port — you can depend on it without pulling the whole brazier framework.

### vcpkg.json

```json
{
  "name": "your-project",
  "version": "1.0.0",
  "dependencies": [
    "brazier-logger"
  ]
}
```

If you need other framework modules, just add them to `dependencies` (e.g. `brazier-core`, `brazier-orm`).

### CMakeLists.txt

```cmake
project(your_project LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(brazier-logger CONFIG REQUIRED)

add_executable(your_project main.cpp)

target_link_libraries(your_project PRIVATE
    brazier::logger
)
```

Then just include the header:

```cpp
#include <brazier/log/Logger.hpp>
```

### Minimal project example

`vcpkg.json`

```json
{
  "name": "your-project",
  "version": "1.0.0",
  "dependencies": [
    "brazier-logger"
  ]
}
```

`CMakeLists.txt`

```cmake
cmake_minimum_required(VERSION 3.20)

project(your_project LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(brazier-logger CONFIG REQUIRED)

add_executable(your_project main.cpp)

target_link_libraries(your_project PRIVATE
    brazier::logger
)
```

`main.cpp`

```cpp
#include <brazier/log/Logger.hpp>

using namespace brazier::log;

int main() {
    Logger::init("app.log");

    Logger::info("Hello");
    Logger::warn("Careful");
    Logger::error("Oops");

    Logger::shutdown();
    return 0;
}
```

### Building with the vcpkg manifest

```bash
cmake -B build -S . -DCMAKE_TOOLCHAIN_FILE=<path-to-vcpkg>/scripts/buildsystems/vcpkg.cmake
cmake --build build
```

On the first run, vcpkg resolves `brazier-logger` and all of its dependencies from the manifest.

## Custom wrappers

If you don't want to bother writing levels using the `Logger::log()` method, then you have the option to use custom wrappers for logging:

| Method         | Level      | Description              |
|----------------|------------|--------------------------|
| `error(msg)`   |  ERROR     | Critical errors          |
| `warn(msg)`    |  WARNING   | Warnings                 |
| `info(msg)`    |  INFO      | Informational messages   |
| `success(msg)` |  SUCCESS   | Successful operations    |
| `debug(msg)`   |            | Debug information        |

## Automatic log rotation

All logs are saved and can be found at your_project_path/Storage/Logs

```text
your_project_path/
└── Storage/
    └── Logs/
        ├── app.log      # Current log file
        └── app_old.log  # Previous log after rotation

```

The system automatically rotates logs when the line limit is reached:

- Current file: `debug.log`
- Old file: `debug_old.log`
- Default limit: 10,000 lines

## Custom log levels

```cpp
Logger::log("Custom business event", "BUSINESS");
Logger::log("Performance metric", "METRIC");
Logger::log("Security audit", "AUDIT");
```

## Log format

### Console output (with colors)

```text
2024-01-15 14:30:25 [INFO] Application started
2024-01-15 14:30:26 [SUCCESS] Database connected
2024-01-15 14:30:27 [ERROR] Connection timeout
```

The brazier logging system is ready for use in production environments and provides all necessary functions for effective application monitoring and debugging.
