cmake_minimum_required(VERSION 3.18)

project("luceneplusplus-playground")

SET(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -static")


get_filename_component(liblucene++_ROOT "${liblucene++_DIR}/../../" ABSOLUTE)

add_library(liblucene++ STATIC IMPORTED)

set_property(
  TARGET liblucene++ APPEND PROPERTY IMPORTED_CONFIGURATIONS RELEASE
)

set_target_properties(liblucene++ PROPERTIES
  IMPORTED_LINK_INTERFACE_LANGUAGES_RELEASE "CXX"
  IMPORTED_LOCATION_RELEASE "${liblucene++_ROOT}/lib/liblucene++.a"
)

# ${liblucene++_ROOT}/lib/liblucene++-contrib.a
  
set_target_properties(liblucene++ PROPERTIES
  MAP_IMPORTED_CONFIG_MINSIZEREL Release
  MAP_IMPORTED_CONFIG_RELWITHDEBINFO Release
)

target_include_directories(liblucene++ INTERFACE "${liblucene++_ROOT}/include")



add_library(zlib STATIC IMPORTED)

set_property(
  TARGET zlib APPEND PROPERTY IMPORTED_CONFIGURATIONS RELEASE
)

set_target_properties(zlib PROPERTIES
  IMPORTED_LINK_INTERFACE_LANGUAGES_RELEASE "CXX"
  IMPORTED_LOCATION_RELEASE "${ZLIB_ROOT}/lib/libzlibstatic.a"
)
  
set_target_properties(zlib PROPERTIES
  MAP_IMPORTED_CONFIG_MINSIZEREL Release
  MAP_IMPORTED_CONFIG_RELWITHDEBINFO Release
)

target_include_directories(zlib INTERFACE "${ZLIB_ROOT}/include")




set(Boost_NO_SYSTEM_PATHS TRUE)
set(Boost_USE_STATIC_RUNTIME TRUE)
set(Boost_USE_STATIC_LIBS TRUE)

find_package(Boost REQUIRED COMPONENTS filesystem iostreams date_time regex system thread)

if(Boost_FOUND)
  include_directories(${Boost_INCLUDE_DIRS})

  SET(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -static")

  add_executable(${PROJECT_NAME} 
    "src/main.cpp"
  )

  target_link_libraries(${PROJECT_NAME} 
    PRIVATE
    Ws2_32
    zlib
    ${Boost_LIBRARIES}
    liblucene++
    # lucene++::lucene++-contrib
  )
    
  
endif()