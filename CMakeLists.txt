project(DiTwi)

cmake_minimum_required(VERSION 2.6)

# Add local modules
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH}
                      ${PROJECT_SOURCE_DIR}/../../cmake/Modules)

# Required packages
if(NOT IFEM_CONFIGURED)
  find_package(IFEM REQUIRED)
  include_directories(${IFEM_INCLUDE_DIRS})
  add_definitions(${IFEM_DEFINITIONS})
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${IFEM_CXX_FLAGS}")
endif()

set(ELASTICITY_DIR ${PROJECT_SOURCE_DIR}/../IFEM-Elasticity)
if(NOT EXISTS ${ELASTICITY_DIR})
  set(ELASTICITY_DIR ${PROJECT_SOURCE_DIR}/../Elasticity)
endif()

include_directories(${IFEM_INCLUDES}
                    ${ELASTICITY_DIR}
                    ${ELASTICITY_DIR}/Linear)

set(EXECUTABLE_OUTPUT_PATH ${CMAKE_BINARY_DIR}/bin)

add_subdirectory(${ELASTICITY_DIR} Elasticity)

set(DiTwi_SRCS ${ELASTICITY_DIR}/Linear/ModalDriver.C)

add_executable(DiTwi main_DiTwi.C ${DiTwi_SRCS})
target_link_libraries(DiTwi Elasticity ${IFEM_LIBRARIES})

list(APPEND CHECK_SOURCES ${DiTwi_SRCS})

# Installation
install(TARGETS DiTwi DESTINATION bin COMPONENT bin)

if(IFEM_COMMON_APP_BUILD)
  set(TEST_APPS ${TEST_APPS} PARENT_SCOPE)
elseif(TEST_APPS)
  # Remove when there are unit tests in this application
  if(NOT UNIT_TEST_NUMBER)
    set(UNIT_TEST_NUMBER 0)
  endif()
  add_check_target()
endif()
