set(TARGET whisper-cli)
add_executable(${TARGET} cli.cpp stacktrace.cpp crash_handler.cpp)

include(DefaultTargetOptions)

target_link_libraries(${TARGET} PRIVATE common whisper ${FFMPEG_LIBRARIES} ${CMAKE_THREAD_LIBS_INIT})

if(MSVC)
    # Generate PDB even in Release for crash stack trace resolution
    target_compile_options(${TARGET} PRIVATE $<$<CONFIG:Release>:/Zi>)
    target_link_options(${TARGET} PRIVATE $<$<CONFIG:Release>:/DEBUG /OPT:REF /OPT:ICF>)
elseif(CMAKE_CXX_COMPILER_ID MATCHES "GNU|Clang|AppleClang")
    target_compile_options(${TARGET} PRIVATE $<$<CONFIG:Release>:-g>)
    target_link_options(${TARGET} PRIVATE $<$<CONFIG:Release>:-rdynamic>)
endif()

install(TARGETS ${TARGET} RUNTIME)
