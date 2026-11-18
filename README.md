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

## Usage

You can use the built-in methods ``log()``, ``info()``, ``debug()``, ``error()``, ``warn()``, and ``success()`` for logging like this:

```cpp
Logger::info("Database connection established");
Logger::debug("Connected to " + db.host + " in " + t + " ms!");
```

Which will give ``[INFO] ... `` and ``[DEBUG] ... `` respectively.

Alternatively, you may want to specify your own type by passing a second argument into ``log()`` like this:

```cpp
Logger::log("Hello world!", "TEST");
```

## Installation

This library is distributed via **vcpkg** as the standalone `brazier-logger` port — you can depend on it without pulling the whole brazier framework.

Add `brazier-logger` to the list of dependencies in your `vcpkg.json` file:

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

Add the following entries to your `CMakeLists.txt`, specifying to find the package, and link it to the target:

`CMakeLists.txt`

```cmake
# ...

find_package(brazier-logger CONFIG REQUIRED)

# ...

target_link_libraries(your_project PRIVATE
    brazier::logger
)
```

You may then use `brazier-logger` in your project by including it in the relevant files like this:

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
- Lines limit: 10,000

## Log format

### Console output (with colors)

$\texttt{2024-01-15 14:30:25 }\textcolor{#00FFFF}{\texttt{[INFO]}}\texttt{ Application started}$  
$\texttt{2024-01-15 14:30:26 }\textcolor{#00FF00}{\texttt{[SUCCESS]}}\texttt{ Database connected}$  
$\texttt{2024-01-15 14:30:27 }\textcolor{#FF0000}{\texttt{[ERROR]}}\texttt{ Connection timeout}$  

The brazier logging system is ready for use in production environments and provides all necessary functions for effective application monitoring and debugging.
