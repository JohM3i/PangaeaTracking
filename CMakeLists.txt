cmake_minimum_required(VERSION 3.10)
project(PangaeaTracking)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/modules/")

include_directories(include)
include_directories(src)
include_directories(include/gui_app)
include_directories(include/main_engine)
include_directories(include/global/image_source)
include_directories(include/main_engine/rendering)
include_directories(include/main_engine/tracker)
include_directories(include/main_engine/utils)
include_directories(include/third_party)

# include libraries ...
# https://cmake.org/cmake/help/v3.0/module/FindwxWidgets.html
# build widgets with gtk environment.
set(wxWidgets_ROOT_DIR /usr/local/include/wx)
set(wxWidgets_CONFIGURATION unix)

#set(wxWidgets_CONFIG_OPTIONS --toolkit=base --prefix=/usr/local/include/wx)
find_package(wxWidgets REQUIRED net gl core base)
include(${wxWidgets_USE_FILE})


find_package(Eigen3 REQUIRED)
include_directories(${EIGEN3_INCLUDE_DIRS})

# OpenGL -lGL -lGLU -lX11 -lGLEW
# https://cmake.org/cmake/help/v3.0/module/FindOpenGL.html
# GLU is here too.
find_package(OpenGL REQUIRED)
include_directories(${OPENGL_INCLUDE_DIR})
# https://cmake.org/cmake/help/v3.1/module/FindGLEW.html
find_package(GLEW REQUIRED)
include_directories(${GLEW_INCLUDE_DIRS})
# https://cmake.org/cmake/help/v3.0/module/FindX11.html
find_package(X11 REQUIRED)
include_directories(${X11_INCLUDE_DIR})

find_package(Boost COMPONENTS filesystem system thread REQUIRED)
include_directories(${Boost_INCLUDE_DIRS})

# https://docs.opencv.org/2.4.13.4/doc/tutorials/introduction/linux_gcc_cmake/linux_gcc_cmake.html
#find_package(OpenCV COMPONENTS core highgui imgproc imgcodecs REQUIRED)
find_package(OpenCV REQUIRED)


find_package(Ceres REQUIRED)
include_directories(${CERES_INCLUDE_DIRS})
# see /cmake/modules/findLMDB.cmake
find_package(LMDB REQUIRED)
include_directories(${LMDB_INCLUDE_DIR})

# https://cmake.org/cmake/help/v3.0/module/FindHDF5.html
find_package(HDF5 REQUIRED)
include_directories(${HDF5_INCLUDE_DIR})
#stop include libraries


set(GUI_SRCS
        src/gui_app/BasicGLPane.cpp
        src/gui_app/CameraControl.cpp
        src/gui_app/controlPanel.cpp
        src/gui_app/ImagePanel.cpp
        src/gui_app/MainFrame.cpp
        src/gui_app/Matrices.cpp
        )
set(GUI_APP_SRC src/gui_app/PangaeaTracking.cpp)
set(CONSOLE_APP_SRC src/console_app/PangaeaTracking_console.cpp)


add_library(main_engine STATIC
        src/main_engine/image_source/ImagesBufferReader.cpp
        src/main_engine/image_source/ImageSequenceReader.cpp
        src/main_engine/rendering/CCamera.cpp
        src/main_engine/rendering/DepthBuffer.cpp
        src/main_engine/rendering/NcvGlXContext.cpp
        src/main_engine/tracker/DeformNRSFMTracker.cpp
        src/main_engine/tracker/FeaturePyramid.cpp
        src/main_engine/tracker/FeatureReader.cpp
        src/main_engine/tracker/ImagePyramid.cpp
        src/main_engine/tracker/Mesh.cpp
        src/main_engine/tracker/MeshBufferReader.cpp
        src/main_engine/tracker/MeshPyramidReader.cpp
        src/main_engine/tracker/MeshSequenceReader.cpp
        src/main_engine/tracker/OptimizationStrategy.cpp
        src/main_engine/tracker/ProblemWrapper.cpp
        src/main_engine/tracker/residual_util.cpp
        src/main_engine/tracker/ShapesBufferReader.cpp
        src/main_engine/tracker/ShapeSequenceReader.cpp
        src/main_engine/utils/global.cpp
        src/main_engine/utils/settings.cpp
        src/main_engine/MainEngine.cpp)

target_include_directories(main_engine PUBLIC include/third_party)
target_include_directories(main_engine PUBLIC include/main_engine/)

# Link libraries
target_link_libraries(main_engine ${wxWidgets_LIBRARIES})
target_link_libraries(main_engine Boost::filesystem Boost::system Boost::thread)
target_link_libraries(main_engine ${OpenCV_LIBS})

# Link these to use OpenGL and GLU
target_link_libraries(main_engine ${OPENGL_LIBRARIES})
target_link_libraries(main_engine ${X11_LIBRARIES})
target_link_libraries(main_engine ${GLEW_LIBRARIES})
target_link_libraries(main_engine Eigen3::Eigen)
target_link_libraries(main_engine ${LMDB_LIBRARIES})
target_link_libraries(main_engine ${HDF5_LIBRARIES})
target_link_libraries(main_engine ${CERES_LIBRARIES})


add_executable(PangaeaTracking_console src/console_app/PangaeaTracking_console.cpp)
target_link_libraries(PangaeaTracking_console main_engine)

add_executable(PangaeaTracking
        src/gui_app/BasicGLPane.cpp
        src/gui_app/CameraControl.cpp
        src/gui_app/controlPanel.cpp
        src/gui_app/ImagePanel.cpp
        src/gui_app/MainFrame.cpp
        src/gui_app/Matrices.cpp
        src/gui_app/PangaeaTracking.cpp
        )
target_link_libraries(PangaeaTracking main_engine)