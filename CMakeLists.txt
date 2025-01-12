cmake_minimum_required(VERSION 3.5)

project(cute-gis VERSION 0.1 LANGUAGES CXX)

include ( CTest )

set(CMAKE_INCLUDE_CURRENT_DIR ON)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(QT NAMES Qt6 REQUIRED COMPONENTS Widgets OpenGLWidgets)
find_package(Qt6 REQUIRED COMPONENTS Widgets OpenGLWidgets Network)


find_package(GTest REQUIRED)
find_package(GDAL REQUIRED)
find_package(CURL REQUIRED)

include_directories(${GDAL_INCLUDE_DIR})
link_directories(${GDAL_LIBRARY_DIRS})

set(PROJECT_SOURCES
    main.cpp
    # Backend files
    src/back/wfsdata.h src/back/wfsdata.cpp
    src/back/citygmlfile.h src/back/citygmlfile.cpp
    src/back/rasterdata.h src/back/rasterdata.cpp
    src/back/datamanagment.h src/back/datamanagment.cpp
    src/back/vectordata.h src/back/vectordata.cpp
    src/back/API_WMS.h src/back/API_WMS.cpp
    src/back/API_WMTS.h src/back/API_WMTS.cpp
    src/back/API_WFS.h src/back/API_WFS.cpp
    src/back/DataProvider.h src/back/DataProvider.cpp
    src/back/citygmlparser.h src/back/citygmlparser.cpp
    src/back/session.h src/back/session.cpp
    # Frontend files
    src/front/mainwindow.cpp src/front/mainwindow.h src/front/mainwindow.ui
    src/front/camera.h src/front/camera.cpp
    src/front/controller.h src/front/controller.cpp
    src/front/renderer.h src/front/renderer.cpp
    src/front/objectloader.h src/front/objectloader.cpp
    src/lib/tiny_obj_loader.h src/lib/tiny_obj_loader.cc
    src/front/layer2d.h src/front/layer2d.cpp
    src/front/addFluxData.cpp
    src/front/addFluxData.h
    src/front/addFluxData.ui
    src/front/layerraster.h src/front/layerraster.cpp
    src/front/renderer2d.h src/front/renderer2d.cpp
    src/front/renderer3d.h src/front/renderer3d.cpp
    src/front/boundingbox.h src/front/boundingbox.cpp
    src/front/layerbase.h src/front/layerbase.cpp
)

set(TEST_SOURCES_1
    tests/testDataManagment.cpp
    src/back/datamanagment.h src/back/datamanagment.cpp
    src/back/vectordata.h src/back/vectordata.cpp
)


set(TEST_SOURCES_WMS
    tests/API_WMSTest.cpp
    src/back/API_WMS.cpp
    src/back/API_WMS.h
    src/back/DataProvider.cpp
    src/back/DataProvider.h
)

set(TEST_SOURCES_WMTS
    tests/API_WMTSTest.cpp
    src/back/API_WMTS.cpp
    src/back/API_WMTS.h
    src/back/DataProvider.cpp
    src/back/DataProvider.h
)

set(TEST_SOURCES_WFS
    tests/API_WFSTest.cpp
    src/back/API_WFS.cpp
    src/back/API_WFS.h
    src/back/DataProvider.cpp
    src/back/DataProvider.h
)

set(TEST_SOURCES_rasterdata
    tests/rasterdataTest.cpp
    src/back/rasterdata.cpp
    src/back/rasterdata.h
    src/back/datamanagment.cpp
    src/back/datamanagment.h
)

set(TEST_SOURCES_citygmldata
    tests/api_citygmlparsertest.cpp
    src/back/citygmlparser.cpp
    src/back/citygmlparser.h
    src/back/datamanagment.cpp
    src/back/datamanagment.h
)

# Backend tests
add_executable(testDataManagment ${TEST_SOURCES_1})
add_executable(API_WMSTest ${TEST_SOURCES_WMS})
add_executable(API_WFSTest ${TEST_SOURCES_WFS})
add_executable(API_WMTSTest ${TEST_SOURCES_WMTS})
add_executable(RasterData ${TEST_SOURCES_rasterdata})
add_executable(citygmldata ${TEST_SOURCES_citygmldata})



# Main application
qt_add_executable(cute-gis
    MANUAL_FINALIZATION
    ${PROJECT_SOURCES}
)

# Link libraries
target_link_libraries(cute-gis PRIVATE Qt6::Widgets Qt6::OpenGLWidgets Qt6::Network glfw GLEW libGLEW.so libGLU.so libGL.so CURL::libcurl ${GDAL_LIBRARIES})
target_link_libraries(testDataManagment PRIVATE GTest::GTest GTest::Main ${GDAL_LIBRARIES})
target_link_libraries(API_WMSTest PRIVATE Qt${QT_VERSION_MAJOR}::Widgets GTest::GTest GTest::Main CURL::libcurl ${GDAL_LIBRARIES})
target_link_libraries(API_WFSTest PRIVATE Qt${QT_VERSION_MAJOR}::Widgets GTest::GTest GTest::Main CURL::libcurl ${GDAL_LIBRARIES})
target_link_libraries(API_WMTSTest PRIVATE Qt${QT_VERSION_MAJOR}::Widgets GTest::GTest GTest::Main CURL::libcurl ${GDAL_LIBRARIES})
target_link_libraries(RasterData PRIVATE Qt${QT_VERSION_MAJOR}::Widgets GTest::GTest GTest::Main CURL::libcurl ${GDAL_LIBRARIES})
target_link_libraries(citygmldata PRIVATE Qt${QT_VERSION_MAJOR}::Widgets GTest::GTest GTest::Main ${GDAL_LIBRARIES})


# Enable testing
enable_testing()


add_test(NAME testDataManagment COMMAND testDataManagment)
add_test(NAME API_WMSTest COMMAND API_WMSTest)
add_test(NAME API_WFSTest COMMAND API_WFSTest)
add_test(NAME API_WMTSTest COMMAND API_WMTSTest)
add_test(NAME RasterData COMMAND RasterData)
add_test(NAME citygmldata COMMAND citygmldata)



# Finalize application
if(QT_VERSION_MAJOR EQUAL 6)
    qt_finalize_executable(cute-gis)
endif()
