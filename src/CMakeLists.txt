file(GLOB HEADER_LIST CONFIGURE_DEPENDS "../include/${PROJECT_NAME}/*.hpp")


foreach(file ${HEADER_LIST})
    source_group (TREE include/${PROJECT_NAME}
        PREFIX "include"               
        FILES  ${file}
    )
endforeach()


file(GLOB SOURCE_LIST CONFIGURE_DEPENDS "*.cpp")

foreach(file ${SOURCE_LIST})
    source_group (TREE ${CMAKE_CURRENT_LIST_DIR}
        PREFIX "src"               
        FILES  ${file}
    )
endforeach()



add_library (ph_file
	${SOURCE_LIST}  ${HEADER_LIST} 
	# ALIAS ${PROJECT_NAME}::${PROJECT_NAME}
)

set_target_properties (
	${PROJECT_NAME} 

	PROPERTIES
  		CXX_STANDARD 20
  		CXX_STANDARD_REQUIRED ON
  		CXX_EXTENSIONS OFF

	FOLDER
		""
)
# Make override a feature requirement only if available
target_compile_features (${PROJECT_NAME} 
	PUBLIC
  		$<$<COMPILE_FEATURES:cxx_override>:cxx_override>
)
# Define the foo_OVERRIDE symbol so it provides the
# override keyword if available or empty otherwise
target_compile_definitions (${PROJECT_NAME} 
	# PUBLIC
  	# 	$<$<COMPILE_FEATURES:cxx_override>:-D${PROJECT_NAME}_OVERRIDE=override>
  	# 	$<$<NOT:$<COMPILE_FEATURES:cxx_override>>:-D${PROJECT_NAME}_OVERRIDE>

	PRIVATE 
		"$<$<CONFIG:DEBUG>:DEBUG> $<$<CONFIG:RELEASE>:RELEASE>"
)


# target_compile_definitions (lib PRIVATE "$<$<CONFIG:DEBUG>:DEBUG> $<$<CONFIG:RELEASE>:RELEASE>")


# target_sources (ph_concepts PRIVATE in/platform.hpp)

set_target_properties (${PROJECT_NAME} PROPERTIES CXX_STANDARD 20)
target_compile_features (${PROJECT_NAME} PUBLIC cxx_std_20)

ph_precompile (TARGET ${PROJECT_NAME} PRIVATE PCH ${std_pch})

# add_library (${PROJECT_NAME} ALIAS ${PROJECT_NAME}::${PROJECT_NAME})
# target_link_libraries (lib PRIVATE    ${Vulkan_LIBRARIES} glfw)
# target_link_libraries (lib PRIVATE ph_file)

target_include_directories (${PROJECT_NAME} PUBLIC ${CMAKE_PROJECT_SOURCE_DIR}/include)
# target_include_directories (ph_concepts PRIVATE ${Boost_INCLUDE_DIRS})

target_compile_features (${PROJECT_NAME} PUBLIC cxx_std_20)

# target_compile_options(ph_concepts PUBLIC "-ast-dump  -fsyntax-only")


target_include_directories(${PROJECT_NAME} PRIVATE
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/../include/${PROJECT_NAME}>
    $<INSTALL_INTERFACE:include/${PROJECT_NAME}>
)

target_include_directories(${PROJECT_NAME} PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/../include>
    $<INSTALL_INTERFACE:include>
)

if (PROJECT_NAME STREQUAL PROJECT_NAME)
    # set_target_properties (lib PROPERTIES FOLDER "lib/")
else ()
    set_target_properties (${PROJECT_NAME} PROPERTIES FOLDER "dependencies/${PROJECT_NAME}")
endif ()



# IDE()