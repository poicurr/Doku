# Doku

A header-only umbrella repository for small C++ utilities.
Each upstream API is exposed under the unified `doku::` namespace via a 1-line alias (e.g., `doku::strutil`).

## Features

- Header-only. No linking. Unincluded headers do not affect your binary.
- Single entry point for multiple libs (managed as git submodules under `external/`).
- Unified include prefix `<Doku/...>`.
- Unified namespace `doku::...` with minimal alias shims.

## Components

- **StringUtils** → `#include <Doku/StringUtils.hpp>` → `doku::strutil::trim(...)`, etc.
- **Logger** → `#include <Doku/Logger.hpp>` → `doku::logger::info(...)`, etc.

> Actual implementations live in submodules: `external/StringUtils`, `external/Logger`, etc.

## Getting the sources

```bash
git clone --recursive https://github.com/poicurr/Doku.git
# If already cloned:
git submodule update --init --recursive
```

## CMake Usage

### Option A: add_subdirectory

```cmake
add_subdirectory(path/to/Doku)

# Aggregate target with all included components
target_link_libraries(MyApp PRIVATE Doku)

# Optional: link components explicitly if exported
# target_link_libraries(MyApp PRIVATE Doku::StringUtils Doku::Logger)
```

### Option B: FetchContent

```cmake
include(FetchContent)
FetchContent_Declare(
  Doku
  GIT_REPOSITORY https://github.com/poicurr/Doku.git
  GIT_TAG main
)
FetchContent_MakeAvailable(Doku)

target_link_libraries(MyApp PRIVATE Doku)
```

## Include Example

```cpp
#include <Doku/StringUtils.hpp>
#include <Doku/Logger.hpp>

int main() {
  std::string s = "  hello  ";
  doku::strutil::trim(s);             // from ::strutil, exposed as doku::strutil
  doku::logger::Logger::info("ok");   // from ::logger, exposed as doku::logger
}
```

## Directory Layout

```
Doku/
  CMakeLists.txt
  include/
    Doku/
      StringUtils.hpp   # thin forwarder + namespace alias only
      Logger.hpp
      ...
  external/
    StringUtils/        # git submodule (poicurr/StringUtils)
    Logger/
    ...
```

## License

MIT
