set(files
  adxintrin.h
  altivec.h
  ammintrin.h
  arm_acle.h
  armintr.h
  arm64intr.h
  avx2intrin.h
  avx512bf16intrin.h
  avx512bwintrin.h
  avx512bitalgintrin.h
  avx512vlbitalgintrin.h
  avx512cdintrin.h
  avx512vpopcntdqintrin.h
  avx512dqintrin.h
  avx512erintrin.h
  avx512fintrin.h
  avx512ifmaintrin.h
  avx512ifmavlintrin.h
  avx512pfintrin.h
  avx512vbmiintrin.h
  avx512vbmivlintrin.h
  avx512vbmi2intrin.h
  avx512vlvbmi2intrin.h
  avx512vlbf16intrin.h
  avx512vlbwintrin.h
  avx512vlcdintrin.h
  avx512vldqintrin.h
  avx512vlintrin.h
  avx512vp2intersectintrin.h
  avx512vlvp2intersectintrin.h
  avx512vpopcntdqvlintrin.h
  avx512vnniintrin.h
  avx512vlvnniintrin.h
  avxintrin.h
  bmi2intrin.h
  bmiintrin.h
  __clang_cuda_builtin_vars.h
  __clang_cuda_cmath.h
  __clang_cuda_complex_builtins.h
  __clang_cuda_device_functions.h
  __clang_cuda_intrinsics.h
  __clang_cuda_libdevice_declares.h
  __clang_cuda_math_forward_declares.h
  __clang_cuda_runtime_wrapper.h
  cetintrin.h
  cldemoteintrin.h
  clzerointrin.h
  cpuid.h
  clflushoptintrin.h
  clwbintrin.h
  emmintrin.h
  enqcmdintrin.h
  f16cintrin.h
  float.h
  fma4intrin.h
  fmaintrin.h
  fxsrintrin.h
  gfniintrin.h
  htmintrin.h
  htmxlintrin.h
  ia32intrin.h
  immintrin.h
  intrin.h
  inttypes.h
  invpcidintrin.h
  iso646.h
  limits.h
  lwpintrin.h
  lzcntintrin.h
  mm3dnow.h
  mmintrin.h
  mm_malloc.h
  module.modulemap
  movdirintrin.h
  msa.h
  mwaitxintrin.h
  nmmintrin.h
  opencl-c.h
  opencl-c-base.h
  pkuintrin.h
  pmmintrin.h
  pconfigintrin.h
  popcntintrin.h
  prfchwintrin.h
  ptwriteintrin.h
  rdseedintrin.h
  rtmintrin.h
  sgxintrin.h
  s390intrin.h
  shaintrin.h
  smmintrin.h
  stdalign.h
  stdarg.h
  stdatomic.h
  stdbool.h
  stddef.h
  __stddef_max_align_t.h
  stdint.h
  stdnoreturn.h
  tbmintrin.h
  tgmath.h
  tmmintrin.h
  unwind.h
  vadefs.h
  vaesintrin.h
  varargs.h
  vecintrin.h
  vpclmulqdqintrin.h
  waitpkgintrin.h
  wbnoinvdintrin.h
  wmmintrin.h
  __wmmintrin_aes.h
  __wmmintrin_pclmul.h
  x86intrin.h
  xmmintrin.h
  xopintrin.h
  xsavecintrin.h
  xsaveintrin.h
  xsaveoptintrin.h
  xsavesintrin.h
  xtestintrin.h
  )

set(cuda_wrapper_files
  cuda_wrappers/algorithm
  cuda_wrappers/complex
  cuda_wrappers/new
)

set(cilk_header_files
  cilk/cilk.h
  cilk/cilk_api.h
  cilk/cilk_api_linux.h
  cilk/cilk_stub.h
  cilk/cilk_undocumented.h
  cilk/common.h
  cilk/holder.h
  cilk/hyperobject_base.h
  cilk/metaprogramming.h
  cilk/reducer.h
  cilk/reducer_file.h
  cilk/reducer_list.h
  cilk/reducer_max.h
  cilk/reducer_min.h
  cilk/reducer_min_max.h
  cilk/reducer_opadd.h
  cilk/reducer_opand.h
  cilk/reducer_opmul.h
  cilk/reducer_opor.h
  cilk/reducer_opxor.h
  cilk/reducer_ostream.h
  cilk/reducer_string.h
  cilk/reducer_vector.h
)

set(ppc_wrapper_files
  ppc_wrappers/mmintrin.h
  ppc_wrappers/xmmintrin.h
  ppc_wrappers/mm_malloc.h
  ppc_wrappers/emmintrin.h
)

set(openmp_wrapper_files
  openmp_wrappers/math.h
  openmp_wrappers/cmath
  openmp_wrappers/__clang_openmp_math.h
  openmp_wrappers/__clang_openmp_math_declares.h
)

set(output_dir ${LLVM_LIBRARY_OUTPUT_INTDIR}/clang/${CLANG_VERSION}/include)
set(out_files)
set(generated_files)

function(copy_header_to_output_dir src_dir file)
  set(src ${src_dir}/${file})
  set(dst ${output_dir}/${file})
  add_custom_command(OUTPUT ${dst}
    DEPENDS ${src}
    COMMAND ${CMAKE_COMMAND} -E copy_if_different ${src} ${dst}
    COMMENT "Copying clang's ${file}...")
  list(APPEND out_files ${dst})
  set(out_files ${out_files} PARENT_SCOPE)
endfunction(copy_header_to_output_dir)

function(clang_generate_header td_option td_file out_file)
  clang_tablegen(${out_file} ${td_option}
  -I ${CLANG_SOURCE_DIR}/include/clang/Basic/
  SOURCE ${CLANG_SOURCE_DIR}/include/clang/Basic/${td_file})

  copy_header_to_output_dir(${CMAKE_CURRENT_BINARY_DIR} ${out_file})
  set(out_files ${out_files} PARENT_SCOPE)
  list(APPEND generated_files "${CMAKE_CURRENT_BINARY_DIR}/${out_file}")
  set(generated_files ${generated_files} PARENT_SCOPE)
endfunction(clang_generate_header)


# Copy header files from the source directory to the build directory
foreach( f ${files} ${cuda_wrapper_files} ${ppc_wrapper_files} ${openmp_wrapper_files} ${cilk_header_files})
  copy_header_to_output_dir(${CMAKE_CURRENT_SOURCE_DIR} ${f})
endforeach( f )

# Generate header files and copy them to the build directory
# Generate arm_neon.h
clang_generate_header(-gen-arm-neon arm_neon.td arm_neon.h)
# Generate arm_fp16.h
clang_generate_header(-gen-arm-fp16 arm_fp16.td arm_fp16.h)

add_custom_target(clang-resource-headers ALL DEPENDS ${out_files})
set_target_properties(clang-resource-headers PROPERTIES
  FOLDER "Misc"
  RUNTIME_OUTPUT_DIRECTORY "${output_dir}")

set(header_install_dir lib${LLVM_LIBDIR_SUFFIX}/clang/${CLANG_VERSION}/include)

install(
  FILES ${files} ${generated_files}
  DESTINATION ${header_install_dir}
  COMPONENT clang-resource-headers)

install(
  FILES ${cuda_wrapper_files}
  DESTINATION ${header_install_dir}/cuda_wrappers
  COMPONENT clang-resource-headers)

install(
  FILES ${ppc_wrapper_files}
  DESTINATION ${header_install_dir}/ppc_wrappers
  COMPONENT clang-resource-headers)

install(
  FILES ${openmp_wrapper_files}
  DESTINATION ${header_install_dir}/openmp_wrappers
  COMPONENT clang-resource-headers)

install(
  FILES ${cilk_header_files}
  DESTINATION ${header_install_dir}/cilk
  COMPONENT clang-resource-headers)

if (NOT LLVM_ENABLE_IDE)
  add_llvm_install_targets(install-clang-resource-headers
                           DEPENDS clang-resource-headers
                           COMPONENT clang-resource-headers)
endif()
