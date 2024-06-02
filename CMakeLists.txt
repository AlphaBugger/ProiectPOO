cmake_minimum_required(VERSION 3.16)
project(CMakeSFMLProject LANGUAGES CXX)

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
option(BUILD_SHARED_LIBS "Build shared libraries" OFF)

# Ensure FreeType is installed
find_package(Freetype REQUIRED)

# Set FreeType include directories
include_directories(${FREETYPE_INCLUDE_DIRS})
include_directories(/usr/local/include/freetype2)

# Fetch SFML from GitHub
include(FetchContent)
FetchContent_Declare(
    SFML
    GIT_REPOSITORY https://github.com/SFML/SFML.git
    GIT_TAG 2.6.x
)
FetchContent_MakeAvailable(SFML)

# Add executable
add_executable(main src/main.cpp)

# Link SFML and FreeType libraries
target_link_libraries(main PRIVATE sfml-graphics ${FREETYPE_LIBRARIES})

# Set C++ standard
target_compile_features(main PRIVATE cxx_std_17)

# Windows specific: copy OpenAL DLL
if(WIN32)
    add_custom_command(
        TARGET main
        COMMENT "Copy OpenAL DLL"
        PRE_BUILD COMMAND ${CMAKE_COMMAND} -E copy ${SFML_SOURCE_DIR}/extlibs/bin/$<IF:$<EQUAL:${CMAKE_SIZEOF_VOID_P},8>,x64,x86>/openal32.dll $<TARGET_FILE_DIR:main>
        VERBATIM)
endif()
