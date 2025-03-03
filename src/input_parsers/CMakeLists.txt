set(MPS_DIR ${CMAKE_CURRENT_SOURCE_DIR}/mps_example)
set(ACAS_DIR ${CMAKE_CURRENT_SOURCE_DIR}/acas_example)
set(BERKELEY_DIR ${CMAKE_CURRENT_SOURCE_DIR}/berkeley_example)

set(PARSERS_OUT_DIR ${CMAKE_BINARY_DIR}/input_parsers)

file(GLOB SRCS "*.cpp")
file(GLOB HEADERS "*.h")

target_sources(${MARABOU_LIB} PRIVATE ${SRCS})
target_include_directories(${MARABOU_LIB} PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}")

target_sources(${MARABOU_TEST_LIB} PRIVATE ${SRCS})
target_include_directories(${MARABOU_TEST_LIB} PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}")

if (${BUILD_PYTHON})
    target_include_directories(${MARABOU_PY} PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}")
endif()

macro(marabou_parser name dir)
    add_executable(${name} "${dir}/main.cpp")
    target_link_libraries(${name} ${MARABOU_LIB})
    target_include_directories(${name} PRIVATE ${LIBS_INCLUDES})
    set_target_properties(${name} PROPERTIES RUNTIME_OUTPUT_DIRECTORY ${PARSERS_OUT_DIR})
endmacro()

marabou_parser(${MPS_PARSER} ${MPS_DIR})
add_custom_command(TARGET ${MPS_PARSER} POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy $<TARGET_FILE:${MPS_PARSER}> ${MPS_PARSER_PATH} )

marabou_parser(${BERKELEY_PARSER} ${BERKELEY_DIR})
marabou_parser(${ACAS_PARSER} ${ACAS_DIR})

# set_target_properties(${MPS_PARSER} PROPERTIES RUNTIME_OUTPUT_DIRECTORY  ${PARSERS_OUT_DIR})

