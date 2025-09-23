cmake_minimum_required(VERSION 3.20)

project(Breakpad C CXX ASM)
set(CMAKE_c_CREATE_STATIC_LIBRARY 1)

#### libdisasm ####
set(libdisasm_SOURCE
    ${CMAKE_CURRENT_SOURCE_DIR}/src/third_party/libdisasm/ia32_implicit.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/third_party/libdisasm/ia32_insn.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/third_party/libdisasm/ia32_invariant.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/third_party/libdisasm/ia32_modrm.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/third_party/libdisasm/ia32_opcode_tables.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/third_party/libdisasm/ia32_operand.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/third_party/libdisasm/ia32_reg.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/third_party/libdisasm/ia32_settings.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/third_party/libdisasm/x86_disasm.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/third_party/libdisasm/x86_format.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/third_party/libdisasm/x86_imm.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/third_party/libdisasm/x86_insn.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/third_party/libdisasm/x86_misc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/third_party/libdisasm/x86_operand_list.c
)

set(libdisasm_HEADERS
	${CMAKE_CURRENT_SOURCE_DIR}/src/third_party/libdisasm/
)

add_library(libdisasm STATIC ${libdisasm_HEADERS} ${libdisasm_SOURCE})
#set_target_properties(libdisasm PROPERTIES LINKER_LANGUAGE c)
add_library(libdisasm::libdisasm ALIAS libdisasm)
target_compile_definitions(libdisasm PRIVATE HAVE_CONFIG_H)
target_include_directories(libdisasm PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/src/third_party/libdisasm/ PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/src/third_party/libdisasm/)
####


set(Breakpad_SOURCES
	${CMAKE_CURRENT_SOURCE_DIR}/src/common/convert_UTF.cc
	${CMAKE_CURRENT_SOURCE_DIR}/src/common/md5.cc
	${CMAKE_CURRENT_SOURCE_DIR}/src/common/string_conversion.cc
)
set(Breakpad_HEADERS
	${CMAKE_CURRENT_SOURCE_DIR}/src/
)

add_library(breakpad STATIC)
target_compile_definitions(breakpad PRIVATE HAVE_CONFIG_H _GLIBCXX_USE_CXX11_ABI=0)
target_link_options(breakpad PRIVATE 
    -static-libstdc++
    -static-libgcc
)

target_include_directories(breakpad PUBLIC
    src/
    breakpad-config/linux/
)

target_compile_features(breakpad PRIVATE cxx_std_20)

target_sources(breakpad PRIVATE
    # breakpad
    src/processor/basic_code_modules.cc
    src/processor/basic_source_line_resolver.cc
    src/processor/call_stack.cc
    src/processor/cfi_frame_info.cc
    src/processor/convert_old_arm64_context.cc
    src/processor/disassembler_x86.cc
    src/processor/dump_context.cc
    src/processor/dump_object.cc
    src/processor/exploitability.cc
    src/processor/exploitability_linux.cc
    src/processor/exploitability_win.cc
    src/processor/fast_source_line_resolver.cc
    src/processor/logging.cc
    src/processor/microdump.cc
    src/processor/microdump_processor.cc
    src/processor/minidump.cc
    src/processor/minidump_processor.cc
    src/processor/module_comparer.cc
    src/processor/module_serializer.cc
    src/processor/pathname_stripper.cc
    src/processor/process_state.cc
    src/processor/proc_maps_linux.cc
    src/processor/simple_symbol_supplier.cc
    src/processor/source_line_resolver_base.cc
    src/processor/stack_frame_cpu.cc
    src/processor/stack_frame_symbolizer.cc
    src/processor/stackwalk_common.cc
    src/processor/stackwalker.cc
    src/processor/stackwalker_amd64.cc
    src/processor/stackwalker_arm.cc
    src/processor/stackwalker_arm64.cc
    src/processor/stackwalker_address_list.cc
    src/processor/stackwalker_mips.cc
    src/processor/stackwalker_ppc.cc
    src/processor/stackwalker_ppc64.cc
    src/processor/stackwalker_riscv.cc
    src/processor/stackwalker_riscv64.cc
    src/processor/stackwalker_sparc.cc
    src/processor/stackwalker_x86.cc
    src/processor/symbolic_constants_win.cc
    src/processor/tokenize.cc

    src/common/linux/scoped_pipe.cc
    src/common/linux/scoped_tmpfile.cc
    src/processor/disassembler_objdump.cc

    # breakpad-client
    src/client/minidump_file_writer.cc
    src/client/linux/crash_generation/crash_generation_client.cc
    src/client/linux/crash_generation/crash_generation_server.cc
    src/client/linux/dump_writer_common/thread_info.cc
    src/client/linux/dump_writer_common/ucontext_reader.cc
    src/client/linux/handler/exception_handler.cc
    src/client/linux/handler/minidump_descriptor.cc
    src/client/linux/log/log.cc
    src/client/linux/microdump_writer/microdump_writer.cc
    src/client/linux/minidump_writer/linux_core_dumper.cc
    src/client/linux/minidump_writer/linux_dumper.cc
    src/client/linux/minidump_writer/linux_ptrace_dumper.cc
    src/client/linux/minidump_writer/minidump_writer.cc
    src/client/linux/minidump_writer/pe_file.cc
    src/common/linux/elf_core_dump.cc
    src/common/linux/elfutils.cc
    src/common/linux/file_id.cc
    src/common/linux/guid_creator.cc
    src/common/linux/linux_libc_support.cc
    src/common/linux/memory_mapped_file.cc
    src/common/linux/safe_readlink.cc
    src/common/linux/http_upload.cc

    src/common/dwarf_cfi_to_module.cc
    src/common/dwarf_cu_to_module.cc
    src/common/dwarf_line_to_module.cc
    src/common/dwarf_range_list_handler.cc
    src/common/language.cc
    src/common/module.cc
    src/common/path_helper.cc
    src/common/stabs_reader.cc
    src/common/stabs_to_module.cc
    src/common/dwarf/bytereader.cc
    src/common/dwarf/dwarf2diehandler.cc
    src/common/dwarf/dwarf2reader.cc
    src/common/dwarf/elf_reader.cc
    src/common/linux/crc32.cc
    src/common/linux/dump_symbols.cc
    src/common/linux/elf_symbols_to_module.cc
    src/common/linux/breakpad_getcontext.S
)