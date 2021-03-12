cmake_minimum_required(VERSION 3.14)
project(tcmalloc CXX)

## dependencies
if(NOT TARGET absl::base)
    include(FetchContent)
    FetchContent_Declare(abseil_github
        GIT_REPOSITORY https://github.com/abseil/abseil-cpp.git
        GIT_TAG master
        GIT_PROGRESS TRUE
    )
    FetchContent_MakeAvailable(abseil_github)
endif()

## common parts of tcmalloc
enable_language(ASM)
add_library(tcmalloc_common STATIC
    tcmalloc/arena.cc
    tcmalloc/background.cc
    tcmalloc/central_freelist.cc
    tcmalloc/common.cc
    tcmalloc/cpu_cache.cc
    tcmalloc/experiment.cc
    tcmalloc/experimental_56_size_class.cc
    tcmalloc/guarded_page_allocator.cc
    tcmalloc/huge_address_map.cc
    tcmalloc/huge_allocator.cc
    tcmalloc/huge_cache.cc
    tcmalloc/huge_page_aware_allocator.cc
    tcmalloc/legacy_size_classes.cc
    tcmalloc/malloc_extension.cc
    tcmalloc/noruntime_size_classes.cc
    tcmalloc/page_allocator.cc
    tcmalloc/page_allocator_interface.cc
    tcmalloc/page_heap.cc
    tcmalloc/pagemap.cc
    tcmalloc/parameters.cc
    tcmalloc/peak_heap_tracker.cc
    tcmalloc/runtime_size_classes.cc
    tcmalloc/sampler.cc
    tcmalloc/size_classes.cc
    tcmalloc/span.cc
    tcmalloc/stack_trace_table.cc
    tcmalloc/static_vars.cc
    tcmalloc/stats.cc
    tcmalloc/system-alloc.cc
    tcmalloc/thread_cache.cc
    tcmalloc/transfer_cache.cc
    tcmalloc/want_hpaa.cc
    tcmalloc/want_hpaa_subrelease.cc
    tcmalloc/want_legacy_spans.cc
    tcmalloc/want_no_hpaa.cc

    tcmalloc/internal/environment.cc
    tcmalloc/internal/logging.cc
    tcmalloc/internal/memory_stats.cc
    tcmalloc/internal/mincore.cc
    tcmalloc/internal/percpu.cc
    #TODO add switch for arch
    #tcmalloc/internal/percpu_rseq_aarch64.S
    #tcmalloc/internal/percpu_rseq_asm.S
    #tcmalloc/internal/percpu_rseq_ppc.S
    #tcmalloc/internal/percpu_rseq_unsupported.cc
    tcmalloc/internal/percpu_rseq_x86_64.S
    tcmalloc/internal/proc_maps.cc
    tcmalloc/internal/util.cc
)

target_include_directories(tcmalloc_common PUBLIC . )
target_compile_features(tcmalloc_common PUBLIC cxx_std_17)
target_link_libraries(tcmalloc_common PUBLIC
    absl::base
    absl::debugging
)
add_library(tcmalloc::common ALIAS tcmalloc_common)

## overrides
# always
add_library(tcmalloc_always STATIC
    tcmalloc/tcmalloc.cc
)
target_include_directories(tcmalloc_always PUBLIC . )
target_compile_features(tcmalloc_always PUBLIC cxx_std_17)
target_link_libraries(tcmalloc_always PUBLIC
    tcmalloc_common
)
add_library(tcmalloc::always ALIAS tcmalloc_always)
