project(replication-booster)
cmake_minimum_required(VERSION 2.6)

set(SOURCE replication_booster.cc prefetch_worker.cc options.cc check_local.cc)

# Find MySQL client library and header files
find_library(MySQL_LIBRARY NAMES mysqlclient_r mysqlclient PATHS
/usr/lib64/mysql /usr/lib/mysql)
find_path(MySQL_INCLUDE_DIR mysql.h
  /usr/local/include/mysql /usr/include/mysql)
include_directories(${MySQL_INCLUDE_DIR})

# Find MySQL replication listener and header files
find_library(Replication_LIBRARY replication)
find_path(Replication_INCLUDE_DIR binlog_api.h)
include_directories(${Replication_INCLUDE_DIR})

# Find Boost
set(Boost_DEBUG FALSE)
set(Boost_FIND_REQUIRED TRUE)
set(Boost_FIND_QUIETLY TRUE)
set(Boost_USE_STATIC_LIBS TRUE)
find_package(Boost REQUIRED system thread regex)
include_directories(${Boost_INCLUDE_DIRS})

# Add DEBUG definition for debug build types.
if (CMAKE_BUILD_TYPE STREQUAL "Debug" OR
    CMAKE_BUILD_TYPE STREQUAL "RelWithDebInfo")
  add_definitions("-DDEBUG")
endif()

add_executable(replication_booster ${SOURCE})
target_link_libraries(replication_booster
  ${Boost_LIBRARIES} ${Replication_LIBRARY} ${MySQL_LIBRARY})

install(TARGETS replication_booster DESTINATION bin)
