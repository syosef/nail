cmake_minimum_required(VERSION 3.0)

#project(generator)

set(CMAKE_CXX_STANDARD 11)

function(add_templates out_var)
    set(result ${${out_var}})
    foreach(in_f ${ARGN})
        set(out_f "${CMAKE_CURRENT_BINARY_DIR}/${in_f}.cc")
        get_filename_component(out_path ${out_f} DIRECTORY)
        if(WIN32)
            message(FATAL_ERROR "no xxd on Windows")
        else(WIN32)
            add_custom_command(
                OUTPUT ${out_f}
                COMMENT "Generating code for ${in_f}"
                COMMAND xxd -i ${in_f} > ${out_f}
                DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/${in_f}
                WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
            )
        endif(WIN32)
        # add input file and output (generated) file to the sources
        list(APPEND result
            ${in_f}
            ${out_f}
        )
        # make sure input file will not be compiled
        set_source_files_properties(
            ${in_f}
            PROPERTIES HEADER_FILE_ONLY TRUE
        )
        # mark generated files as such,
        set_source_files_properties(
            ${out_f}
            PROPERTIES GENERATED true
        )
        # add the output path to the includes
        include_directories(${out_path})
    endforeach()
    # and finally apply additions to the original variable
    set(${out_var} "${result}" PARENT_SCOPE)
endfunction()

list(APPEND srcs
    main.cc
    mk_parser.cc
    mk_gen.cc
    datatype.cc
    util.cc
    new_grammar.nail.c
    new_grammar.nail.h
    mk_directparser.cc
    )

add_templates(srcs
    parser_template.cc
    parser_template.c
    parser_template.h
    )

message(STATUS "${srcs}")

add_executable(nail ${srcs})
target_include_directories(nail PRIVATE . ..)
target_link_libraries(nail PRIVATE stdc++fs)

add_executable(cnail ${srcs})
target_include_directories(cnail PRIVATE . ..)
target_link_libraries(cnail PRIVATE stdc++fs)
