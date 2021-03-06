cmake_minimum_required(VERSION 3.5)
project(CfTracking)
#ADD_DEFINITIONS ( -std=c++11 )
SET(CMAKE_CXX_FLAGS "-std=c++11 -Wall -O3 -fPIC")

find_package(OpenCV 3 REQUIRED)
find_package(PythonLibs 2.7 EXACT REQUIRED)

include_directories(${PYTHON_INCLUDE_DIRS})
include_directories(${OpenCV_INCLUDE_DIRS})
include_directories(${CMAKE_CURRENT_SOURCE_DIR})
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/src)

#include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
#include_directories(SYSTEM ${CONAN_INCLUDE_DIRS})
#include(CheckCXXCompilerFlag)

#find_package(PythonLibs REQUIRED)
#find_package(OpenCV REQUIRED)

# add 3rdparty source
add_subdirectory(pybind11)

set(CF_TCLAP_DIR "src/3rdparty/tclap")
set(CF_CV_EXT_DIR "src/3rdparty/cv_ext")
set(CF_PIOTR_DIR "src/3rdparty/piotr")

include_directories(
                    pybind11/include
                    src/cf_libs/kcf
                    src/cf_libs/dsst
                    src/cf_libs/common)


include_directories(
    ${PYTHON_INCLUDE_DIR}
    #${PROJECT_SOURCE_DIR}/pybind11/include
    ${OpenCV_DIR}/include
    ${CF_TCLAP_DIR}
    ${CF_CV_EXT_DIR}
    ${CF_PIOTR_DIR}
    ${CF_PIOTR_DIR}/src
    src/main
    src/cf_libs/kcf
    src/cf_libs/dsst
    src/cf_libs/common)

set(CF_PIOTR_SOURCES
    ${CF_PIOTR_DIR}/gradientMex.hpp
    ${CF_PIOTR_DIR}/src/gradientMex.cpp
    ${CF_PIOTR_DIR}/src/sse.hpp
    ${CF_PIOTR_DIR}/src/wrappers.hpp)

set(CF_LIB_COMMON_SOURCES
    src/cf_libs/common/feature_channels.hpp
    src/cf_libs/common/mat_consts.hpp
    src/cf_libs/common/math_helper.hpp
    src/cf_libs/common/math_helper.cpp
    src/cf_libs/common/cf_tracker.hpp
    src/cf_libs/common/tracker_debug.hpp
    src/cf_libs/common/scale_estimator.hpp
    src/cf_libs/common/cv_ext.hpp
    ${CF_CV_EXT_DIR}/shift.cpp
    ${CF_CV_EXT_DIR}/shift.hpp
    ${CF_CV_EXT_DIR}/math_spectrums.cpp
    ${CF_CV_EXT_DIR}/math_spectrums.hpp
    ${CF_CV_EXT_DIR}/psr.hpp
    ${CF_PIOTR_SOURCES})

set(CF_MAIN_SOURCES
    src/main/image_acquisition.hpp
    src/main/image_acquisition.cpp
    ${CF_CV_EXT_DIR}/init_box_selector.hpp
    ${CF_CV_EXT_DIR}/init_box_selector.cpp
    ${CF_CV_EXT_DIR}/tracker_run.hpp
    ${CF_CV_EXT_DIR}/tracker_run.cpp
    ${CF_TCLAP_DIR}/tclap/CmdLine.h)

#add_library(eb SHARED src/main/example.cpp)
#target_link_libraries(eb ${PYTHON_LIBRARIES} ${OpenCV_LIBS})
#SET_TARGET_PROPERTIES(eb PROPERTIES PREFIX "")


add_library(DSST  SHARED
    src/main/main_dsst.cpp
    src/cf_libs/dsst/dsst_debug.hpp
    src/cf_libs/dsst/dsst_tracker.hpp
    ${CF_MAIN_SOURCES}
    ${CF_LIB_COMMON_SOURCES})
target_link_libraries(DSST ${OpenCV_LIBS} ${PYTHON_LIBRARIES})
set_target_properties(DSST PROPERTIES PREFIX "")
set_target_properties(DSST PROPERTIES SUFFIX ".so")

