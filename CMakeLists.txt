cmake_minimum_required(VERSION 3.15)
project(PupilEXT)

set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)
if(CMAKE_VERSION VERSION_LESS "3.7.0")
    set(CMAKE_INCLUDE_CURRENT_DIR ON)
endif()

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${PROJECT_SOURCE_DIR}/cmake)
set(CMAKE_PREFIX_PATH ${CMAKE_PREFIX_PATH} "C:/Qt/5.15.16/msvc2019_64")
set(CMAKE_PREFIX_PATH ${CMAKE_PREFIX_PATH} "C:/vcpkg/installed/x64-windows")
set(GLOG_INCLUDE_DIR "C:/vcpkg/installed/x64-windows/include")
if(CMAKE_BUILD_TYPE STREQUAL Debug)
    set(CMAKE_PREFIX_PATH ${CMAKE_PREFIX_PATH} "C:/vcpkg/installed/x64-windows/debug")
else()
    set(CMAKE_PREFIX_PATH ${CMAKE_PREFIX_PATH} "C:/vcpkg/installed/x64-windows/include")
endif()

set(TBB_INCLUDE_DIR "C:/vcpkg/installed/x64-windows/include/tbb")
set(SPII_INSTALL_DIR "${PROJECT_SOURCE_DIR}/external/spii")
set(spii_INCLUDE_DIRS ${SPII_INSTALL_DIR}/include)
set(PYLON_HOME "C:/Program Files/Basler/pylon 6/Development/")
find_package(Qt5 COMPONENTS Widgets Concurrent SerialPort Charts Svg PrintSupport REQUIRED)
find_package(OpenCV REQUIRED)
find_package(Boost 1.72 REQUIRED)
find_package(TBB REQUIRED PATHS "${PROJECT_SOURCE_DIR}/external/tbb")
find_package(Eigen3 REQUIRED)
find_package(Ceres CONFIG REQUIRED)
find_package(Pylon REQUIRED)
set(TBB_LIBRARY_DEBUG "${PROJECT_SOURCE_DIR}/external/tbb/lib/intel64")
set(TBB_LIBRARY_RELEASE "${PROJECT_SOURCE_DIR}/external/tbb/lib/intel64")

find_library (spii_LIBRARY_RELEASE
        spii
        PATHS ${SPII_INSTALL_DIR}/lib/)
find_library (meschach_LIBRARY_RELEASE
        meschach
        PATHS ${SPII_INSTALL_DIR}/lib/)
if (spii_LIBRARY_RELEASE AND meschach_LIBRARY_RELEASE)
    set(spii_LIBRARIES ${spii_LIBRARY_RELEASE} ${meschach_LIBRARY_RELEASE})
    set(SPII_BUILD_FOUND TRUE)
else()
    set(spii_LIBRARIES "")
endif()

include_directories(${Qt5Core_INCLUDE_DIRS}
        ${OpenCV_INCLUDE_DIRS}
        ${Boost_INCLUDE_DIRS}
        ${TBB_INCLUDE_DIR}
        ${spii_INCLUDE_DIRS}
        ${EIGEN_INCLUDE_DIR}
        ${CERES_INCLUDE_DIRS}
        ${PYLON_INCLUDE_DIR}
        "singleeyefitter")

add_subdirectory (src)
add_subdirectory (singleeyefitter)

message(STATUS "")
message(STATUS "spii_LIBRARIES:\"${spii_LIBRARIES}\"")
message(STATUS "--- Include directories ---" )
message(STATUS " QT5Core_INCLUDE_DIRS: ${Qt5Core_INCLUDE_DIRS}" )
message(STATUS " OpenCV_INCLUDE_DIRS: ${OpenCV_INCLUDE_DIRS}" )
message(STATUS " Boost_INCLUDE_DIRS: ${Boost_INCLUDE_DIRS}" )
message(STATUS " TBB_INCLUDE_DIRS: ${TBB_INCLUDE_DIR}" )
message(STATUS " spii_INCLUDE_DIRS: ${spii_INCLUDE_DIRS}" )
message(STATUS " EIGEN_INCLUDE_DIR: ${EIGEN_INCLUDE_DIR}" )
message(STATUS " CERES_INCLUDE_DIRS: ${CERES_INCLUDE_DIRS}" )
message(STATUS " Pylon_INCLUDE_DIRS: ${PYLON_INCLUDE_DIR}" )
message(STATUS "---------------------------" )
message(STATUS "")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -march=native -O3")