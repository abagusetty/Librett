
set(LIBRETT_TESTS librett_test librett_bench example)

# build the following executables
foreach(_exec ${LIBRETT_TESTS})
  add_executable(${_exec} "${_exec}.cpp")
  target_link_libraries(${_exec} PUBLIC librett)
  target_include_directories(${_exec} PUBLIC ${PROJECT_SOURCE_DIR}/src)
endforeach()

# add AoT compilation for Intel GPU
if(ENABLE_SYCL)
  if(ENABLE_SYCL_HIP OR ENABLE_SYCL_CUDA)
  else()
    foreach(_exec ${LIBRETT_TESTS})
      target_compile_options(${_exec} PRIVATE -fsycl -fsycl-targets=spir64_gen -Xsycl-target-backend "-device 12.60.7" -DPERFTEST)
      target_link_options(${_exec} PRIVATE -fsycl -fsycl-targets=spir64_gen -Xsycl-target-backend "-device 12.60.7")
    endforeach()
  endif()
endif(ENABLE_SYCL)

foreach(_exec ${LIBRETT_TESTS})
  add_test(NAME ${_exec} COMMAND ${_exec})
endforeach()
