# MafiaNode

Pre-built [libnode](https://nodejs.org/api/embedding.html) (Node.js as a shared library) and standalone V8 distributions for embedding JavaScript in C++ projects.

Part of the [MafiaHub](https://github.com/MafiaHub) ecosystem.

## Downloads

Pre-built binaries are available on the [Releases](https://github.com/MafiaHub/MafiaNode/releases) page.

### libnode

Embeddable Node.js runtime with full npm and ICU support.

| Platform | Architecture | Download |
|----------|--------------|----------|
| Windows | x64 | `libnode-v{version}-win-x64.zip` |
| Windows | x86 | `libnode-v{version}-win-x86.zip` |
| macOS | arm64 | `libnode-v{version}-darwin-arm64.tar.gz` |
| Linux | x64 | `libnode-v{version}-linux-x64.tar.gz` |

### Standalone V8

Lightweight V8 engine as a monolithic static library for projects needing only JavaScript execution without Node.js APIs.

| Platform | Architecture | Download |
|----------|--------------|----------|
| Windows | x64 | `v8-v{version}-win-x64.zip` |
| Windows | x86 | `v8-v{version}-win-x86.zip` |
| macOS | arm64 | `v8-v{version}-darwin-arm64.tar.gz` |
| Linux | x64 | `v8-v{version}-linux-x64.tar.gz` |

## Contents

### libnode Archive

```
lib/
├── libnode.dll/.dylib/.so    # Node.js shared library
├── libnode.lib               # Import library (Windows)
├── libuv.lib/.a              # libuv async I/O
├── v8_libbase.lib/.a         # V8 base library
└── v8_libplatform.lib/.a     # V8 platform library
include/
├── node/                     # Node.js headers
└── v8/                       # V8 headers
bin/
└── node(.exe)                # Node.js executable
```

### V8 Archive

```
lib/
└── libv8_monolith.a/.lib     # V8 static library
include/
└── v8/                       # V8 headers
```

## Usage

### CMake Integration

```cmake
# Find libnode
find_library(LIBNODE_LIBRARY NAMES libnode node PATHS ${LIBNODE_DIR}/lib)
target_include_directories(your_target PRIVATE ${LIBNODE_DIR}/include/node)
target_link_libraries(your_target ${LIBNODE_LIBRARY})
```

### Basic Embedding Example

```cpp
#include <node.h>
#include <uv.h>

int main(int argc, char* argv[]) {
    // Initialize Node.js
    std::vector<std::string> args(argv, argv + argc);
    std::unique_ptr<node::MultiIsolatePlatform> platform =
        node::MultiIsolatePlatform::Create(4);
    v8::V8::InitializePlatform(platform.get());
    v8::V8::Initialize();

    // Create and run Node.js environment
    // See Node.js embedding documentation for complete examples

    return 0;
}
```

For detailed embedding documentation, see the [Node.js Embedding Guide](https://nodejs.org/api/embedding.html).

## Building

Builds are automated via GitHub Actions. The workflow:

1. Resolves the latest Node.js LTS version (v22.x)
2. Builds libnode with `--shared --with-intl=full-icu`
3. Builds standalone V8 as a monolithic static library
4. Publishes releases with all platform artifacts

### Manual Trigger

Builds can be triggered manually via GitHub Actions with an optional version override.

## Version Policy

- **Node.js:** Latest LTS release (currently v22.x)
- **V8:** Version bundled with the corresponding Node.js release
- **Releases:** Created automatically when pushing to `main`, skipped if the version already exists

## Related Projects

- [MafiaHub Framework](https://github.com/MafiaHub/Framework) - C++ game modification framework
- [Node.js](https://nodejs.org/) - JavaScript runtime
- [V8](https://v8.dev/) - JavaScript engine

## License

The build infrastructure in this repository is provided as-is for the MafiaHub project.

Pre-built binaries include components under their respective licenses:
- **Node.js** - [MIT License](https://github.com/nodejs/node/blob/main/LICENSE)
- **V8** - [BSD License](https://chromium.googlesource.com/v8/v8/+/refs/heads/main/LICENSE)
- **libuv** - [MIT License](https://github.com/libuv/libuv/blob/v1.x/LICENSE)
