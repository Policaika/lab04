add_library(solver_lib STATIC solver.cpp)

target_include_directories(solver_lib PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})

target_link_directories(solver_lib PUBLIC formatter)
