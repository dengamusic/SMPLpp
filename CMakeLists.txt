cmake_minimum_required(VERSION 3.15)

project(SMPLXX LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# ─── xtensor ───────────────────────────────────────────────────────────
if(TARGET xtensor)
    message(STATUS "xtensor target already provided by parent – skipping find_package")
    get_target_property(_XTENSOR_INC xtensor INTERFACE_INCLUDE_DIRECTORIES)
else()
    find_package(xtensor REQUIRED)
    get_target_property(_XTENSOR_INC xtensor INTERFACE_INCLUDE_DIRECTORIES)
endif()

# ─── other deps ─────────────────────────────────────────────────────────
find_package(nlohmann_json REQUIRED)
find_package(Torch REQUIRED)

# ─── sources ────────────────────────────────────────────────────────────
file(GLOB_RECURSE HEADER "SMPL++/include/*.h"  "SMPL++/include/*.hpp")
file(GLOB_RECURSE SOURCE "SMPL++/src/*.c"      "SMPL++/src/*.cpp")

# ─── library target ─────────────────────────────────────────────────────
add_library(smplpp STATIC ${HEADER} ${SOURCE})

target_include_directories(smplpp PUBLIC
    SMPL++/include
    ${_XTENSOR_INC}
    ${TORCH_INCLUDE_DIRS}                     # add Torch headers
)

target_link_libraries(smplpp PUBLIC
    xtensor
    nlohmann_json::nlohmann_json
    ${TORCH_LIBRARIES}                        # link Torch libs
)
