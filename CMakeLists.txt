cmake_minimum_required(VERSION 3.20)

project(Breakpad C CXX ASM)
set(CMAKE_c_CREATE_STATIC_LIBRARY 1)
set(BREAKPAD_SRC_DIR ${CMAKE_CURRENT_SOURCE_DIR}/src)


#============================================================================
# libdisasm
#============================================================================
set(LIBDISASM_DIR ${BREAKPAD_SRC_DIR}/third_party/libdisasm)

set(libdisasm_SOURCES
    ${LIBDISASM_DIR}/ia32_implicit.c
    ${LIBDISASM_DIR}/ia32_insn.c
    ${LIBDISASM_DIR}/ia32_invariant.c
    ${LIBDISASM_DIR}/ia32_modrm.c
    ${LIBDISASM_DIR}/ia32_opcode_tables.c
    ${LIBDISASM_DIR}/ia32_operand.c
    ${LIBDISASM_DIR}/ia32_reg.c
    ${LIBDISASM_DIR}/ia32_settings.c
    ${LIBDISASM_DIR}/x86_disasm.c
    ${LIBDISASM_DIR}/x86_format.c
    ${LIBDISASM_DIR}/x86_imm.c
    ${LIBDISASM_DIR}/x86_insn.c
    ${LIBDISASM_DIR}/x86_misc.c
    ${LIBDISASM_DIR}/x86_operand_list.c
)

set(libdisasm_HEADERS
	${CMAKE_CURRENT_SOURCE_DIR}/src/third_party/libdisasm/
)

add_library(libdisasm STATIC ${libdisasm_SOURCES})
add_library(libdisasm::libdisasm ALIAS libdisasm)

target_compile_definitions(libdisasm PRIVATE HAVE_CONFIG_H)
target_compile_options(libdisasm PRIVATE -fPIC)

target_include_directories(libdisasm PUBLIC
    ${LIBDISASM_DIR}
)

#============================================================================
# breakpad
#============================================================================


set(BREAKPAD_PROCESSOR_SOURCES
    ${BREAKPAD_SRC_DIR}/processor/basic_code_modules.cc
    ${BREAKPAD_SRC_DIR}/processor/basic_source_line_resolver.cc
    ${BREAKPAD_SRC_DIR}/processor/call_stack.cc
    ${BREAKPAD_SRC_DIR}/processor/cfi_frame_info.cc
    ${BREAKPAD_SRC_DIR}/processor/convert_old_arm64_context.cc
    ${BREAKPAD_SRC_DIR}/processor/disassembler_x86.cc
    ${BREAKPAD_SRC_DIR}/processor/dump_context.cc
    ${BREAKPAD_SRC_DIR}/processor/dump_object.cc
    ${BREAKPAD_SRC_DIR}/processor/exploitability.cc
    ${BREAKPAD_SRC_DIR}/processor/exploitability_linux.cc
    ${BREAKPAD_SRC_DIR}/processor/exploitability_win.cc
    ${BREAKPAD_SRC_DIR}/processor/fast_source_line_resolver.cc
    ${BREAKPAD_SRC_DIR}/processor/logging.cc
    ${BREAKPAD_SRC_DIR}/processor/microdump.cc
    ${BREAKPAD_SRC_DIR}/processor/microdump_processor.cc
    ${BREAKPAD_SRC_DIR}/processor/minidump.cc
    ${BREAKPAD_SRC_DIR}/processor/minidump_processor.cc
    ${BREAKPAD_SRC_DIR}/processor/module_comparer.cc
    ${BREAKPAD_SRC_DIR}/processor/module_serializer.cc
    ${BREAKPAD_SRC_DIR}/processor/pathname_stripper.cc
    ${BREAKPAD_SRC_DIR}/processor/process_state.cc
    ${BREAKPAD_SRC_DIR}/processor/proc_maps_linux.cc
    ${BREAKPAD_SRC_DIR}/processor/simple_symbol_supplier.cc
    ${BREAKPAD_SRC_DIR}/processor/source_line_resolver_base.cc
    ${BREAKPAD_SRC_DIR}/processor/stack_frame_cpu.cc
    ${BREAKPAD_SRC_DIR}/processor/stack_frame_symbolizer.cc
    ${BREAKPAD_SRC_DIR}/processor/stackwalk_common.cc
    ${BREAKPAD_SRC_DIR}/processor/stackwalker.cc
    ${BREAKPAD_SRC_DIR}/processor/stackwalker_amd64.cc
    ${BREAKPAD_SRC_DIR}/processor/stackwalker_arm.cc
    ${BREAKPAD_SRC_DIR}/processor/stackwalker_arm64.cc
    ${BREAKPAD_SRC_DIR}/processor/stackwalker_address_list.cc
    ${BREAKPAD_SRC_DIR}/processor/stackwalker_mips.cc
    ${BREAKPAD_SRC_DIR}/processor/stackwalker_ppc.cc
    ${BREAKPAD_SRC_DIR}/processor/stackwalker_ppc64.cc
    ${BREAKPAD_SRC_DIR}/processor/stackwalker_riscv.cc
    ${BREAKPAD_SRC_DIR}/processor/stackwalker_riscv64.cc
    ${BREAKPAD_SRC_DIR}/processor/stackwalker_sparc.cc
    ${BREAKPAD_SRC_DIR}/processor/stackwalker_x86.cc
    ${BREAKPAD_SRC_DIR}/processor/symbolic_constants_win.cc
    ${BREAKPAD_SRC_DIR}/processor/tokenize.cc
    ${BREAKPAD_SRC_DIR}/processor/disassembler_objdump.cc
)

set(BREAKPAD_CLIENT_LINUX_SOURCES
    ${BREAKPAD_SRC_DIR}/client/minidump_file_writer.cc
    ${BREAKPAD_SRC_DIR}/client/linux/crash_generation/crash_generation_client.cc
    ${BREAKPAD_SRC_DIR}/client/linux/crash_generation/crash_generation_server.cc
    ${BREAKPAD_SRC_DIR}/client/linux/dump_writer_common/thread_info.cc
    ${BREAKPAD_SRC_DIR}/client/linux/dump_writer_common/ucontext_reader.cc
    ${BREAKPAD_SRC_DIR}/client/linux/handler/exception_handler.cc
    ${BREAKPAD_SRC_DIR}/client/linux/handler/minidump_descriptor.cc
    ${BREAKPAD_SRC_DIR}/client/linux/log/log.cc
    ${BREAKPAD_SRC_DIR}/client/linux/microdump_writer/microdump_writer.cc
    ${BREAKPAD_SRC_DIR}/client/linux/minidump_writer/linux_core_dumper.cc
    ${BREAKPAD_SRC_DIR}/client/linux/minidump_writer/linux_dumper.cc
    ${BREAKPAD_SRC_DIR}/client/linux/minidump_writer/linux_ptrace_dumper.cc
    ${BREAKPAD_SRC_DIR}/client/linux/minidump_writer/minidump_writer.cc
    ${BREAKPAD_SRC_DIR}/client/linux/minidump_writer/pe_file.cc
)

set(BREAKPAD_COMMON_SOURCES
    ${BREAKPAD_SRC_DIR}/common/convert_UTF.cc
    ${BREAKPAD_SRC_DIR}/common/md5.cc
    ${BREAKPAD_SRC_DIR}/common/string_conversion.cc
    ${BREAKPAD_SRC_DIR}/common/language.cc
    ${BREAKPAD_SRC_DIR}/common/module.cc
    ${BREAKPAD_SRC_DIR}/common/path_helper.cc
    ${BREAKPAD_SRC_DIR}/common/stabs_reader.cc
    ${BREAKPAD_SRC_DIR}/common/stabs_to_module.cc
    ${BREAKPAD_SRC_DIR}/common/dwarf_cfi_to_module.cc
    ${BREAKPAD_SRC_DIR}/common/dwarf_cu_to_module.cc
    ${BREAKPAD_SRC_DIR}/common/dwarf_line_to_module.cc
    ${BREAKPAD_SRC_DIR}/common/dwarf_range_list_handler.cc
    ${BREAKPAD_SRC_DIR}/common/dwarf/bytereader.cc
    ${BREAKPAD_SRC_DIR}/common/dwarf/dwarf2diehandler.cc
    ${BREAKPAD_SRC_DIR}/common/dwarf/dwarf2reader.cc
    ${BREAKPAD_SRC_DIR}/common/dwarf/elf_reader.cc
)

set(BREAKPAD_COMMON_LINUX_SOURCES
    ${BREAKPAD_SRC_DIR}/common/linux/scoped_pipe.cc
    ${BREAKPAD_SRC_DIR}/common/linux/scoped_tmpfile.cc
    ${BREAKPAD_SRC_DIR}/common/linux/elf_core_dump.cc
    ${BREAKPAD_SRC_DIR}/common/linux/elfutils.cc
    ${BREAKPAD_SRC_DIR}/common/linux/file_id.cc
    ${BREAKPAD_SRC_DIR}/common/linux/guid_creator.cc
    ${BREAKPAD_SRC_DIR}/common/linux/linux_libc_support.cc
    ${BREAKPAD_SRC_DIR}/common/linux/memory_mapped_file.cc
    ${BREAKPAD_SRC_DIR}/common/linux/safe_readlink.cc
    ${BREAKPAD_SRC_DIR}/common/linux/http_upload.cc
    ${BREAKPAD_SRC_DIR}/common/linux/crc32.cc
    ${BREAKPAD_SRC_DIR}/common/linux/dump_symbols.cc
    ${BREAKPAD_SRC_DIR}/common/linux/elf_symbols_to_module.cc
    ${BREAKPAD_SRC_DIR}/common/linux/breakpad_getcontext.S
)

add_library(breakpad STATIC)
add_library(breakpad::breakpad ALIAS breakpad)
target_compile_definitions(breakpad PRIVATE HAVE_CONFIG_H _GLIBCXX_USE_CXX11_ABI=0)
target_compile_features(breakpad PRIVATE cxx_std_17)
target_compile_options(breakpad PRIVATE -fPIC)
target_link_libraries(breakpad PRIVATE
    libdisasm::libdisasm
)

target_link_options(breakpad PRIVATE 
    -static-libstdc++
    -static-libgcc
)

target_sources(breakpad PRIVATE
    ${BREAKPAD_PROCESSOR_SOURCES}
    ${BREAKPAD_CLIENT_LINUX_SOURCES}
    ${BREAKPAD_COMMON_SOURCES}
    ${BREAKPAD_COMMON_LINUX_SOURCES}
)
target_include_directories(breakpad PUBLIC
    ${BREAKPAD_SRC_DIR}
)