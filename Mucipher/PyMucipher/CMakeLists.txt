# This example shows how to use python
# Currently these languages have been tested:
#  perl tcl ruby php4 pike
project(PyMucipher)

cmake_policy(SET CMP0078 NEW)
cmake_policy(SET CMP0086 NEW)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC")

set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY BOTH)
set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE BOTH)

find_package(SWIG REQUIRED)
set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)

include(${SWIG_USE_FILE})
set(UseSWIG_TARGET_NAME_PREFERENCE STANDARD)

find_package(PythonLibs)
include_directories(${PYTHON_INCLUDE_PATH})

include_directories(${CMAKE_CURRENT_SOURCE_DIR})

set(CMAKE_SWIG_FLAGS "")

set_source_files_properties(mucipherc.py GENERATED)
set_source_files_properties(mucipher.i PROPERTIES CPLUSPLUS ON)
set_source_files_properties(mucipher.i PROPERTIES SWIG_FLAGS "-includeall")
swig_add_library(mucipherc LANGUAGE python SOURCES ${Mucipher_SOURCE_DIR}/aes.cpp ${Mucipher_SOURCE_DIR}/hexdigest.cpp ${Mucipher_SOURCE_DIR}/md5.cpp ${Mucipher_SOURCE_DIR}/sha.cpp ${Mucipher_SOURCE_DIR}/sha256.cpp ${Mucipher_SOURCE_DIR}/wraphelp.cpp mucipher.i )
target_link_libraries(mucipherc ${PYTHON_LIBRARIES})

set(PYMUCIPHER_LIBS
    mucipher.py
    ${CMAKE_CURRENT_BINARY_DIR}/mucipherc.py
    ${CMAKE_CURRENT_BINARY_DIR}/_mucipherc.so
    )
EXECUTE_PROCESS(COMMAND python2.7 ${CMAKE_SOURCE_DIR}/cmake/get_python_lib.py museek OUTPUT_VARIABLE sitepackages OUTPUT_STRIP_TRAILING_WHITESPACE ERROR_VARIABLE error)

if(NOT sitepackages OR error)
    message(FATAL_ERROR "Failed to find Python's site-packages directory" ${sitepackages} ${error})
endif()

install(
    FILES ${PYMUCIPHER_LIBS}
    DESTINATION ${CMAKE_INSTALL_PREFIX}/${sitepackages}
    )
message("--> PyMucipher will be installed.")
