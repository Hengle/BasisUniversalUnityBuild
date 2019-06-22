# BasisUniversalUnityBuild

Toolchains to develop and build native libraries for [BasisUniversalUnity](https://github.com/atteneder/BasisUniversalUnity), a Unity package that allows you to load [Binomial LLC](http://www.binomial.info)'s [Basis Universal texture files](https://github.com/BinomialLLC/basis_universal) into a Unity game engine project.

Note: If you just want to use the [BasisUniversalUnity package](https://github.com/atteneder/BasisUniversalUnity), don't waste your time here. This project is just for building the necessary native libraries.

Special thanks to Binomial and everyone involved in making Basis Universal available!

## Building

Check out this repository and make sure its sub-modules are also cloned.

### Prerequisites

You'll need:

- [CMake](https://cmake.org)
- A copy/clone of [BasisUniversalUnity](https://github.com/atteneder/BasisUniversalUnity), since the build installs the libraries into it.

Additional each platform has its own prerequisites (see below).

All platform variants will have an `install` build target, that does the following:

This does the following:

- The final library will be installed to the correct place within `BasisUniversalUnity/Runtime/Plugins`.
- The source code for the transcoder + wrapper is copied to `BasisUniversalUnity/Runtime/Plugins/WebGL`. This way it gets compiled and included when you build the Unity project for WebGL.

### macOS

Needed for macOS Unity Editor and macOS standalone builds.

#### Prerequisites macOS

- Xcode and its command line tools

#### Build for macOS

Open up a terminal and navigate into the repository.

```bash
cd /path/to/BasisUniversalUnityBuild
```

Create a subfolder `build`, enter it and call CMake like this:

```bash
mkdir build
cd build
cmake .. -G Xcode
```

This will generate an Xcode project called `basisu_transcoder.xcodeproj`.

Open it with Xcode and build it (target `ALL_BUILD` or `basisu`).

After this was successful, build the target `install`.

### iOS

#### Prerequisites iOS

- Xcode and its command line tools

#### Build for iOS

Create a subfolder `build_ios`, enter it and call CMake like this:

```bash
mkdir build_ios
cd build_ios
cmake .. -G Xcode \
-DCMAKE_TOOLCHAIN_FILE="../cmake/ios.toolchain.cmake" \
-DBASIS_UNIVERSAL_UNITY_PATH="/your/path/to/BasisUniversalUnity"
```

Replace `/your/path/to/BasisUniversalUnity` with the actual path to your local copy of BasisUniversalUnity.

This will generate an Xcode project called `basisu_transcoder.xcodeproj`.

Open it with Xcode and build target `ALL_BUILD` or `basisu` with scheme `Generic iOS device`. Setting this scheme ensures that a fat-library is built and you don't run into linker errors due to missing symbols/architectures.

After this was successful, build the target `install`.

### Android

#### Prerequisites Android

- Android NDK

#### Build for Android

Create a subfolder `build_android_arm64`, enter it and call CMake like this:

```bash
mkdir build_android_arm64
cd build_android_arm64
cmake .. \
-DANDROID_ABI=arm64-v8a \
-DCMAKE_BUILD_TYPE=RelWithDebInfo \
-DANDROID_NDK=/path/to/your/android/sdk/ndk-bundle \
-DCMAKE_TOOLCHAIN_FILE="/path/to/your/android/sdk/ndk-bundle/build/cmake/android.toolchain.cmake" \
-DANDROID_STL=c++_static \
-DBASIS_UNIVERSAL_UNITY_PATH="/your/path/to/BasisUniversalUnity"
```

Replace `/your/path/to/BasisUniversalUnity` with the actual path to your local copy of BasisUniversalUnity.

Replace `/path/to/your/android/sdk/ndk-bundle` with the actual path to your Android NDK install.

To build and install

```bash
make && make install
```

### Other platforms (Linux,Windows,iOS)

Not tested at the moment. Probably needs some minor tweaks to run.

## Support

Like this project? You can show your appreciation and ...

[![Buy me a coffee](https://az743702.vo.msecnd.net/cdn/kofi1.png?v=0)](https://ko-fi.com/C0C3BW7G)

## License

Copyright (c) 2019 Andreas Atteneder, All Rights Reserved.
Licensed under the Apache License, Version 2.0 (the "License");
you may not use files in this repository except in compliance with the License.
You may obtain a copy of the License at

   <http://www.apache.org/licenses/LICENSE-2.0>

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
