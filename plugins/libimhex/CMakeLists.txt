cmake_minimum_required(VERSION 3.16)
project(libimhex)

set(CMAKE_CXX_STANDARD 20)

add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../../external/ImGui ${CMAKE_CURRENT_BINARY_DIR}/external/ImGui)
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../../external/nlohmann_json ${CMAKE_CURRENT_BINARY_DIR}/external/nlohmann_json)
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../../external/nativefiledialog ${CMAKE_CURRENT_BINARY_DIR}/external/nativefiledialog)


if (WIN32)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -static-libstdc++ -static-libgcc -Wl,-Bstatic,--whole-archive -lwinpthread -Wl,--no-whole-archive")
endif()

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC")

set(CMAKE_SHARED_LIBRARY_PREFIX "")

add_library(libimhex SHARED
        source/api/event.cpp
        source/api/imhex_api.cpp
        source/api/content_registry.cpp

        source/helpers/utils.cpp
        source/helpers/shared_data.cpp
        source/helpers/crypto.cpp
        source/helpers/lang.cpp

        source/lang/pattern_language.cpp
        source/lang/preprocessor.cpp
        source/lang/lexer.cpp
        source/lang/parser.cpp
        source/lang/validator.cpp
        source/lang/evaluator.cpp
        source/lang/builtin_functions.cpp

        source/providers/provider.cpp

        source/views/view.cpp
        )

target_include_directories(libimhex PUBLIC include ${MBEDTLS_INCLUDE_DIR})
target_link_directories(libimhex PUBLIC ${MBEDTLS_LIBRARY_DIR})

if (WIN32)
    target_link_libraries(libimhex PUBLIC imgui nlohmann_json libmbedcrypto.a nfd)
else ()
    target_link_libraries(libimhex PUBLIC imgui nlohmann_json mbedcrypto nfd)
endif ()