cmake_minimum_required(VERSION 3.0)

function(add_nail out_var)
    set(result ${${out_var}})
    foreach(in_f ${ARGN})
        set(out_f "${CMAKE_CURRENT_BINARY_DIR}/${in_f}.hh")
        get_filename_component(out_path ${out_f} DIRECTORY)
        add_custom_command(
            OUTPUT ${out_f} ${CMAKE_CURRENT_BINARY_DIR}/${in_f}.cc
            COMMENT "Generating code for ${in_f} -> ${CMAKE_CURRENT_BINARY_DIR}"
            COMMAND nail ${in_f} -o "${CMAKE_CURRENT_BINARY_DIR}"
            COMMAND astyle ${out_f}
            COMMAND astyle ${CMAKE_CURRENT_BINARY_DIR}/${in_f}.cc
            DEPENDS nail
            WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
        )

        # add input file and output (generated) file to the sources
        list(APPEND result
            ${in_f}
            ${out_f}
            "${CMAKE_CURRENT_BINARY_DIR}/${in_f}.cc"
        )
        # make sure input file will not be compiled
        set_source_files_properties(
            ${in_f}
            PROPERTIES HEADER_FILE_ONLY TRUE
        )
        set_source_files_properties(
            "${CMAKE_CURRENT_BINARY_DIR}/${in_f}.cc"
            PROPERTIES HEADER_FILE_ONLY TRUE
        )
        # mark generated files as such,
        set_source_files_properties(
            ${out_f}
            PROPERTIES GENERATED true
        )
        set_source_files_properties(
            "${CMAKE_CURRENT_BINARY_DIR}/${in_f}.cc"
            PROPERTIES GENERATED true
        )
        # add the output path to the includes
        include_directories(${out_path})
    endforeach()
    # and finally apply additions to the original variable
    set(${out_var} "${result}" PARENT_SCOPE)
endfunction()

function(add_cnail out_var)
    set(result ${${out_var}})
    foreach(in_f ${ARGN})
        set(out_f "${CMAKE_CURRENT_BINARY_DIR}/${in_f}.h")
        get_filename_component(out_path ${out_f} DIRECTORY)
        add_custom_command(
            OUTPUT ${out_f}
            COMMENT "Generating code for ${in_f}"
            COMMAND cnail ${in_f} -o "${CMAKE_CURRENT_BINARY_DIR}"
            DEPENDS cnail
            WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
        )

        # add input file and output (generated) file to the sources
        list(APPEND result
            ${in_f}
            ${out_f}
            "${CMAKE_CURRENT_BINARY_DIR}/${in_f}.c"
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
        set_source_files_properties(
            "${CMAKE_CURRENT_BINARY_DIR}/${in_f}.c"
            PROPERTIES GENERATED true
        )
        # add the output path to the includes
        include_directories(${out_path})
    endforeach()
    # and finally apply additions to the original variable
    set(${out_var} "${result}" PARENT_SCOPE)
endfunction()

#add_subdirectory(dns)
add_subdirectory(dns_cpp)
