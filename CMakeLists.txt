cmake_minimum_required(VERSION 3.24)

project(Solstice)

file(GLOB_RECURSE SOLSTICE_SOURCES "${CMAKE_CURRENT_LIST_DIR}/src/*.cpp")
file(
    GLOB_RECURSE SOLSTICE_PRIVATE_HEADERS
    "${CMAKE_CURRENT_LIST_DIR}/src/*.hpp"
)
file(
    GLOB_RECURSE SOLSTICE_PUBLIC_HEADERS
    "${CMAKE_CURRENT_LIST_DIR}/include/*.hpp"
)

add_library(Solstice SHARED ${SOLSTICE_SOURCES})

target_sources(
    Solstice
    PUBLIC
        FILE_SET solstice_public_headers
            TYPE HEADERS
            BASE_DIRS "${CMAKE_CURRENT_LIST_DIR}/include"
            FILES ${SOLSTICE_PUBLIC_HEADERS}
)

target_sources(
    Solstice
    PRIVATE
        FILE_SET solstice_private_headers
            TYPE HEADERS
            BASE_DIRS "${CMAKE_CURRENT_LIST_DIR}/src"
            FILES ${SOLSTICE_PRIVATE_HEADERS}
)

include(FetchContent)

function(target_add_dependency target)
    set(options SHALLOW)
    set(one_value_keywords NAME GH_REPO TAG TARGET)
    cmake_parse_arguments(
        PARSE_ARGV 1
        ARG
        "${options}"
        "${one_value_keywords}"
        ""
    )
    FetchContent_Declare(
        ${ARG_NAME}
        GIT_REPOSITORY "https://github.com/${ARG_GH_REPO}"
        GIT_TAG ${ARG_TAG}
        GIT_SHALLOW ${ARG_SHALLOW}
    )
    FetchContent_MakeAvailable(${ARG_NAME})
    target_link_libraries(${target} PRIVATE ${ARG_TARGET})
endfunction()

target_add_dependency(Solstice NAME spdlog GH_REPO gabime/spdlog TAG v1.17.0 TARGET spdlog::spdlog SHALLOW)
target_add_dependency(Solstice NAME magic_enum GH_REPO Neargye/magic_enum TAG v0.9.8 TARGET magic_enum::magic_enum SHALLOW)
target_add_dependency(Solstice NAME glm GH_REPO g-truc/glm TAG 1.0.1 TARGET glm::glm SHALLOW)
target_add_dependency(Solstice NAME nlohmann_json GH_REPO nlohmann/json TAG v3.12.0 TARGET nlohmann_json::nlohmann_json SHALLOW)
target_add_dependency(Solstice NAME DirectXTK GH_REPO microsoft/DirectXTK TAG may2026 TARGET DirectXTK SHALLOW)
target_add_dependency(Solstice NAME cryptocpp GH_REPO abdes/cryptopp-cmake TAG CRYPTOPP_8_9_0 TARGET cryptopp::cryptopp SHALLOW)
