Build guide
Thanks to [DC的博文](http://www.cnblogs.com/dongc/p/6876362.html) for your batch files.

1  Environment 
   VC build tools 2015.3
   win7 or  win10

2  Install essential tools  

    1) Install Visual C++ Build Tools2015.3(typical install, add "C:\Program Files (x86)\Windows Kits\10\bin\x64\ucrt"to PATH)
    2) Install Cmake 3.8.2
    3) Install ActivePerl-5.26.0.2600
    4) Install golang:go1.9.1 windows/amd64
    5) Install nasm:2.13.01
    6) Download Ninja v1.8.2, unzip it, put "ninja.exe" in "C:\Program Files\CMake\bin"
    7) Verify all of them are proper installed with following commands:
           cmake -version
           perl -version
           go version
           nasm -v
	   ninja --version
       Assume there no errors, if there are , pls add relative PATH item. 

3 Download Source files   
	A:   
		1. grpc-1.6.6.zip unzip to D:/grpc-1.6.6

		2. benchmark-1.2.0.zip unzip to D:/grpc-1.6.6/third_party/benchmark

		3. boringssl-chromium-stable.zip unzip to D:/grpc-1.6.6/third_party/boringssl 

		4. c-ares-cares-1_12_0.zip unzip to D:/grpc-1.6.6/third_party/cares/cares

		5. gflags-2.2.1.zip unzip to D:/grpc-1.6.6/third_party/gflags

		6. protobuf-3.4.1.zip unzip to D:/grpc-1.6.6/third_party/protobuf

		7. zlib-1.2.11.zip unzip to D:/grpc-1.6.6/third_party/zlib
        Note: c-ares-cares-1_12_0.zip(grpc1.6.x is not compatible with c-ares-cares-1_13_0)must be unzipped to D:/grpc-   1.6.6/third_party/cares/cares, or it will fail。
     B:   
		1. grpc-1.7.x.zip unzip to D:/grpc-1.7.x

		2. benchmark-1.2.0.zip unzip to D:/grpc-1.7.x/third_party/benchmark

		3. boringssl-chromium-stable.zip unzip to D:/grpc-1.7.x/third_party/boringssl 

		4. c-ares-cares-1_13_0.zip unzip to D:/grpc-1.7.x/third_party/cares/cares

		5. gflags-2.2.1.zip unzip to D:/grpc-1.7.x/third_party/gflags

		6. protobuf-3.4.1.zip unzip to D:/grpc-1.7.x/third_party/protobuf

		7. zlib-1.2.11.zip unzip to D:/grpc-1.7.x/third_party/zlib      
4  Build 

   a) Option 1 : CMAKE+NMAKE

			1) Copy build_x64.bat adn do_build.bat script to D:/grpc-1.6.6/cmake 

            2) Run build_x64.bat

   b) Option 2 : CMAKE+NINJA
            1)  modify do_build.bat

				@echo off

				::Release,Debug,RelWithDebInfo

				set CMAKE_BUILD_TYPE=%1%

				::x86,x64

				set PLATFORM=%2%

				set OUTPUT_PATH=%3%

				set CMAKE_PATH=%4%

				if not exist %PLATFORM% (

				mkdir %PLATFORM%

				)

				cd %PLATFORM%

				if %PLATFORM% == x86 (

				call "%VS140COMNTOOLS%..\..\VC\vcvarsall.bat" x86

				)

				if %PLATFORM% == x64 (

				call "%VS140COMNTOOLS%..\..\VC\vcvarsall.bat" amd64

				)

				echo cmake -G "Ninja" -D CMAKE_BUILD_TYPE=%CMAKE_BUILD_TYPE% -D EXECUTABLE_OUTPUT_PATH=%OUTPUT_PATH% -D LIBRARY_OUTPUT_PATH=%OUTPUT_PATH% -D CMAKE_C_FLAGS_RELEASE="/MT /WX-" -D CMAKE_CXX_FLAGS_RELEASE="/MT /WX-" -D BUILD_SHARED_LIBS=0 %CMAKE_PATH%

				cmake -G Ninja -D CMAKE_BUILD_TYPE=%CMAKE_BUILD_TYPE% -D EXECUTABLE_OUTPUT_PATH=%OUTPUT_PATH% -D LIBRARY_OUTPUT_PATH=%OUTPUT_PATH% -D CMAKE_C_FLAGS_RELEASE="/MT /WX-" -D CMAKE_CXX_FLAGS_RELEASE="/MT /WX-" -D BUILD_SHARED_LIBS=0 %CMAKE_PATH%

			2) Modify build_x64.bat

				@echo off

				set CURRENT_DIR=%~dp0

				set CMAKE_BUILD_TYPE=Release

				set PLATFORM=x64

				set OUTPUT_PATH=%CURRENT_DIR%/x64/bin

				set CMAKE_PATH=%CURRENT_DIR%/../

				call do_build.bat %CMAKE_BUILD_TYPE% %PLATFORM% %OUTPUT_PATH% %CMAKE_PATH%

				@pause

			3) Build：
				Copy build_x64.bat和do_build.bat to D:/grpc-1.6.6/cmake, run build_x64.bat， then run cmake --build .



==========================
Alternative way to download sources(For Chinese) :
		1) fork official grpc

		2) change .gitmodules：

			[submodule "third_party/boringssl-with-bazel"]

			path = third_party/boringssl-with-bazel

			url =https://boringssl.googlesource.com/boringssl

			===>

			[submodule "third_party/boringssl-with-bazel"]

			path = third_party/boringssl-with-bazel

			url =https://github.com/google/boringssl.git

		3) git clone --recursive -b v1.7.x https://github.com/topillar/grpc.git grpc-v1.7.x
