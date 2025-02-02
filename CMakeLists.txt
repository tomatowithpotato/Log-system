cmake_minimum_required(VERSION 3.0)
project(haha_log)

set(CMAKE_VERBOSE_MAKEFILE ON)                  # make 过程中显示信息

if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE "Release")
endif()

set(CXX_FLAGS
    -g
    # -MMD
    -std=c++2a
    -rdynamic
    # -DVALGRIND
    -DCHECK_PTHREAD_RETURN_VALUE
    -D_FILE_OFFSET_BITS=64
    -Wall
    -Wno-deprecated
    -Werror
    -Wno-unused-function
    -Wno-builtin-macro-redefined
 )

if(CMAKE_BUILD_TYPE AND (CMAKE_BUILD_TYPE STREQUAL "Debug"))
    set(CMAKE_CXX_FLAGS_DEBUG "-O0")
    message("Debug mode:${CMAKE_CXX_FLAGS_DEBUG}")
    SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin/debug)                   # 设置可执行文件的位置
    SET(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib/debug)                      # 设置动态链接库的位置
elseif(CMAKE_BUILD_TYPE AND (CMAKE_BUILD_TYPE STREQUAL "Release"))
    set(CMAKE_CXX_FLAGS_RELEASE "-O2")
    message("Release mode:${CMAKE_CXX_FLAGS_RELEASE}")
    SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin/release)                   # 设置可执行文件的位置
    SET(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib/release)                      # 设置动态链接库的位置
else()
    message("else:${CMAKE_BUILD_TYPE}")
    message("else:${CMAKE_CXX_FLAGS_RELEASE}")
    set(CMAKE_BUILD_TYPE "Release")
    set(CMAKE_CXX_FLAGS_RELEASE "-O2")
    SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin/release)                   # 设置可执行文件的位置
    SET(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib/release)                      # 设置动态链接库的位置
endif()

string(REPLACE ";" " " CMAKE_CXX_FLAGS "${CXX_FLAGS}")
message(STATUS "CMAKE_CXX_FLAGS = " ${CMAKE_CXX_FLAGS})
message(STATUS "CMAKE_BUILD_TYPE = " ${CMAKE_BUILD_TYPE})

# # 设置编译参数
# set(CMAKE_CXX_FLAGS "$ENV{CXXFLAGS} -rdynamic -O0 -ggdb -std=c++2a -Wall -Wno-deprecated -Werror -Wno-unused-function \
# -Wno-builtin-macro-redefined")

# # 设置编译参数
# set(CMAKE_CXX_FLAGS "$ENV{CXXFLAGS} -rdynamic -O2 -ggdb -std=c++2a -Wall -Wno-deprecated -Werror -Wno-unused-function \
# -Wno-builtin-macro-redefined")

include_directories(. ./haha_log)
# include_directories(${YAML_CPP_INCLUE})
include_directories(/usr/local/include)             # 添加头文件路径
link_directories(/usr/local/lib)                    # 添加库文件路径


aux_source_directory(${PROJECT_SOURCE_DIR}/haha_log/base SRC_BASE_LIST)        # 将制定路径内的源文件全部加入变量中
aux_source_directory(${PROJECT_SOURCE_DIR}/haha_log/log SRC_LOG_LIST)        # 将制定路径内的源文件全部加入变量中

set(
    LIB_SRC
    ${SRC_BASE_LIST}
    ${SRC_LOG_LIST}
)

# MESSAGE( STATUS "LIB_SRC = ${LIB_SRC}.")

add_library(haha_log SHARED ${LIB_SRC})                                # SHARED 指示生成so文件 即动态链接库

set(
    ALL_LIBS
    haha_log
    pthread
    uuid
)

add_executable(asyncLog_threadPool_test.out tests/asyncLog_threadPool_test.cc)             # 设置要生成可执行文件的代码
add_dependencies(asyncLog_threadPool_test.out haha_log)                               # 设置依赖关系
target_link_libraries(asyncLog_threadPool_test.out ${ALL_LIBS})                   # 设置要链接的库

add_executable(asyncLog_singleThread_test.out tests/asyncLog_singleThread_test.cc)                # 设置要生成可执行文件的代码
add_dependencies(asyncLog_singleThread_test.out haha_log)                               # 设置依赖关系
target_link_libraries(asyncLog_singleThread_test.out ${ALL_LIBS})                   # 设置要链接的库

add_executable(syncLog_singleThread_test.out tests/syncLog_singleThread_test.cc)                # 设置要生成可执行文件的代码
add_dependencies(syncLog_singleThread_test.out haha_log)                               # 设置依赖关系
target_link_libraries(syncLog_singleThread_test.out ${ALL_LIBS})                   # 设置要链接的库

add_executable(syncLog_threadPool_test.out tests/syncLog_threadPool_test.cc)                # 设置要生成可执行文件的代码
add_dependencies(syncLog_threadPool_test.out haha_log)                               # 设置依赖关系
target_link_libraries(syncLog_threadPool_test.out ${ALL_LIBS})                   # 设置要链接的库

add_executable(syncLog_stdout_test.out tests/syncLog_stdout_test.cc)                # 设置要生成可执行文件的代码
add_dependencies(syncLog_stdout_test.out haha_log)                               # 设置依赖关系
target_link_libraries(syncLog_stdout_test.out ${ALL_LIBS})                   # 设置要链接的库

# SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)                   # 设置可执行文件的位置
# SET(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)                      # 设置动态链接库的位置