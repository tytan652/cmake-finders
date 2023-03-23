![cmake-finders repository logo](https://repository-images.githubusercontent.com/617985106/dde2a1a0-15b6-4a91-b065-a905e6e6a86c)

# cmake-finders
Collection of modern CMake find modules.

# Features

* Find modules are based on CMake 3.x targets (no CMake 2.x global variables)
* `find_package_handle_standard_args` used to check for all required variables (including version variable)
* Uses `pkg-config` by default to discover system-wide libraries
* Sets `VERSION` target property according to meta data provided by `pkg-config` or a known header file
* Sets `INTERFACE_COMPILE_OPTIONS` target property according to `CFLAGS` provided by `pkg-config`
* Attempts to find a corresponding `dll` dynamic library for a discovered `lib` import library (Windows only)
    * The import library will be retained via the `Libx264_IMPLIB` target property
    * The DLL will be retained via the `IMPORTED_LOCATION` target property
    * Target `TYPE` will be `STATIC` if just an import library exists, or `SHARED` otherwise
* Attempts to set the `IMPORTED_SONAME` target property (macOS and Linux only)
* Sets description text and URL for use with the `FeatureSummary` module

# Installation

Copy desired find modules into a directory in a CMake project (e.g. `cmake`), then add this directory to existing `CMAKE_MODULE_PATH`:

    list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")

Appending ensures that any pre-existing module paths are retained, also path variables should be contained in double quotes to avoid unintended list tokenisation due to space characters.

**Note:** CMake will iterate through all existing module paths in order, with the earliest discovered find module "winning". If you need to ensure your own find modules take precedence, create a new list _prepending_ your own module path to the existing one, then overwrite the variable with this new list.

# Contributions

This repository is open for contributions: To add CMake find modules, use one of the existing modules as a template and adjust appropriately.

## Acceptance Criteria

* Every finder must be formatted using `cmake-format` (the included cmake-format file will ensure compliance)
* Every finder must be linted using `cmake-lint`, `C0103` and `C0301` are acceptable to be disabled
* Every finder must have a full doc header including a license note
* File names for find modules are case-sensitive: To use `find_package(MyLib)` the find module must be named  `FindMyLib.cmake`
* Variables used within the finder are case-sensitive as well, i.e. use `MyLib_FOUND`, `MyLib_VERSION`, etc.
* Target name should match package name, i.e. `MyLib::MyLib`. For modules with multiple components use the package name as the namespace, i.e. `MyLib::MyComponent`
* Unset helper variables at the end of the find modules
* A version variable must be set (if no proper semver can be found, set it to `0.0.0` with an accompanying warning)
* If a package is only available for a specific operating system, OS-specific features can be omitted (e.g. searching for a DLL as part of a finder for a Linux-only library)
* Try to use the shortest possible description text for `FeatureSummary`, usually the first sentence of the official distribution's description or subtitle will suffice

Reasonable exceptions to these critera are possible - as an example: If a library is part of a wider known software package, it makes sense to use that package as the namespace (e.g. `VLC::LibVLC`).
