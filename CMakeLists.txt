cmake_minimum_required(VERSION 3.11)
project(dechamps_cpputil LANGUAGES CXX)

add_compile_options(
	/WX /W4 /permissive- /analyze
	
	# Suppress warnings about shadowing declarations.
	#
	# In most cases, this happens when a lambda is used to initialize some
	# variable, and the lambda declares a local variable with the same name as the
	# variable it's tasked with initializing. In such cases the shadowing is
	# actually desirable, because it prevents one from accidentally using the (not
	# yet initialized) outer variable instead of the (valid) local variable within
	# the lambda.
	/wd4458 /wd4456
)

include(TestBigEndian)
TEST_BIG_ENDIAN(DECHAMPS_CPPUTIL_BIG_ENDIAN)
configure_file("${CMAKE_CURRENT_LIST_DIR}/endian.in.h" endian.h)
add_library(endian INTERFACE)
target_include_directories(endian
	INTERFACE "$<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>"
	INTERFACE "$<INSTALL_INTERFACE:include>"
)

add_library(exception STATIC exception.cpp)
target_include_directories(exception INTERFACE "$<INSTALL_INTERFACE:include>")

add_library(find INTERFACE)
target_include_directories(find INTERFACE "$<INSTALL_INTERFACE:include>")

add_library(string INTERFACE)
target_include_directories(string INTERFACE "$<INSTALL_INTERFACE:include>")

install(FILES "${CMAKE_CURRENT_BINARY_DIR}/endian.h" exception.h find.h string.h DESTINATION include/dechamps_cpputil)
install(TARGETS endian exception find string EXPORT dechamps_cpputil-config)
install(EXPORT dechamps_cpputil-config NAMESPACE dechamps_cpputil:: DESTINATION lib/cmake/dechamps_cpputil)
