# -*- indent-tabs-mode: nil -*-
#
# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#   http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing,
# software distributed under the License is distributed on an
# "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
# KIND, either express or implied.  See the License for the
# specific language governing permissions and limitations
# under the License.

sources = files(
    'array-builder.cpp',
    'basic-array.cpp',
    'basic-data-type.cpp',
    'buffer.cpp',
    'chunked-array.cpp',
    'codec.cpp',
    'composite-array.cpp',
    'composite-data-type.cpp',
    'datum.cpp',
    'decimal.cpp',
    'decoder.cpp',
    'error.cpp',
    'expression.cpp',
    'field.cpp',
    'interval.cpp',
    'memory-pool.cpp',
    'record-batch.cpp',
    'scalar.cpp',
    'schema.cpp',
    'table-builder.cpp',
    'table.cpp',
    'tensor.cpp',
    'timestamp-parser.cpp',
    'type.cpp',
)

sources += files(
    'file-mode.cpp',
    'file.cpp',
    'input-stream.cpp',
    'output-stream.cpp',
    'readable.cpp',
    'writable-file.cpp',
    'writable.cpp',
)

sources += files(
    'ipc-options.cpp',
    'metadata-version.cpp',
    'reader.cpp',
    'writer.cpp',
)

sources += files('compute.cpp')

sources += files('file-system.cpp', 'local-file-system.cpp')

if have_arrow_orc
    sources += files('orc-file-reader.cpp')
endif

c_headers = files(
    'array-builder.h',
    'array.h',
    'arrow-glib.h',
    'basic-array-definition.h',
    'basic-array.h',
    'basic-data-type.h',
    'buffer.h',
    'chunked-array-definition.h',
    'chunked-array.h',
    'codec.h',
    'composite-array.h',
    'composite-data-type.h',
    'data-type.h',
    'datum.h',
    'decimal.h',
    'decoder.h',
    'error.h',
    'expression.h',
    'field.h',
    'interval.h',
    'memory-pool.h',
    'record-batch.h',
    'scalar.h',
    'schema.h',
    'table-builder.h',
    'table.h',
    'tensor.h',
    'timestamp-parser.h',
    'type.h',
)


c_headers += files(
    'file-mode.h',
    'file.h',
    'input-stream.h',
    'output-stream.h',
    'readable.h',
    'writable-file.h',
    'writable.h',
)

c_headers += files('ipc-options.h', 'metadata-version.h', 'reader.h', 'writer.h')

c_headers += files('compute-definition.h', 'compute.h')

c_headers += files('file-system.h', 'local-file-system.h')

if have_arrow_orc
    c_headers += files('orc-file-reader.h')
endif


cpp_headers = files(
    'array-builder.hpp',
    'array.hpp',
    'arrow-glib.hpp',
    'basic-array.hpp',
    'basic-data-type.hpp',
    'buffer.hpp',
    'chunked-array.hpp',
    'codec.hpp',
    'data-type.hpp',
    'datum.hpp',
    'decimal.hpp',
    'decoder.hpp',
    'error.hpp',
    'expression.hpp',
    'field.hpp',
    'interval.hpp',
    'memory-pool.hpp',
    'record-batch.hpp',
    'scalar.hpp',
    'schema.hpp',
    'table-builder.hpp',
    'table.hpp',
    'tensor.hpp',
    'timestamp-parser.hpp',
    'type.hpp',
)

cpp_headers += files(
    'file-mode.hpp',
    'file.hpp',
    'input-stream.hpp',
    'output-stream.hpp',
    'readable.hpp',
    'writable-file.hpp',
    'writable.hpp',
)

cpp_headers += files(
    'ipc-options.hpp',
    'metadata-version.hpp',
    'reader.hpp',
    'writer.hpp',
)

cpp_headers += files('compute.hpp')

cpp_headers += files('file-system.hpp', 'local-file-system.hpp')

if have_arrow_orc
    cpp_headers += files('orc-file-reader.hpp')
endif

cpp_internal_headers = files('internal-hash-table.hpp', 'internal-index.hpp')

version_h = configure_file(
    input: 'version.h.in',
    output: 'version.h',
    command: [
        python3,
        generate_version_header_py,
        '--library',
        'GARROW',
        '--version',
        version,
        '--input',
        '@INPUT@',
        '--output',
        '@OUTPUT@',
    ],
)

c_headers += version_h

enums = gnome.mkenums(
    'enums',
    sources: c_headers,
    identifier_prefix: 'GArrow',
    symbol_prefix: 'garrow',
    c_template: 'enums.c.template',
    h_template: 'enums.h.template',
    install_dir: join_paths(include_dir, 'arrow-glib'),
    install_header: true,
)
enums_source = enums[0]
enums_header = enums[1]

headers = c_headers + cpp_headers
install_headers(headers, subdir: meson.project_name())

gobject = dependency('gobject-2.0')
gobject_libdir = gobject.get_variable(pkgconfig: 'libdir')
# This is for Homebrew. "pkg-config --cflags gio-2.0" includes the
# "-I$(xcrun --show-sdk-path)/usr/include" flag by zlib.pc. The
# include path includes the standard C headers such as stdlib.h. It
# confuses clang++ (/usr/bin/c++).
gio = cxx.find_library('gio-2.0', dirs: [gobject_libdir], required: false)
if not gio.found()
    gio = dependency('gio-2.0')
endif
dependencies = [arrow_acero, arrow_compute, arrow, gobject, gio]
libarrow_glib = library(
    'arrow-glib',
    sources: sources + enums,
    install: true,
    dependencies: dependencies,
    implicit_include_directories: false,
    include_directories: base_include_directories,
    cpp_args: ['-DGARROW_COMPILATION'],
    c_args: ['-DGARROW_COMPILATION'],
    soversion: so_version,
    version: library_version,
)
arrow_glib = declare_dependency(
    link_with: libarrow_glib,
    include_directories: base_include_directories,
    dependencies: dependencies,
    sources: enums_header,
)

pkgconfig.generate(
    libarrow_glib,
    description: 'C API for Apache Arrow based on GLib',
    filebase: meson.project_name(),
    name: 'Apache Arrow GLib',
    requires: ['gobject-2.0', 'arrow'],
    variables: pkgconfig_variables,
    version: version,
)
if have_arrow_orc
    pkgconfig.generate(
        filebase: 'arrow-orc-glib',
        description: 'ORC modules for Apache Arrow GLib',
        name: 'Apache Arrow GLib ORC',
        requires: ['arrow-glib'],
        version: version,
    )
endif

if have_gi
    arrow_glib_gir = gnome.generate_gir(
        libarrow_glib,
        export_packages: 'arrow-glib',
        extra_args: gir_scanner_extra_args,
        header: 'arrow-glib/arrow-glib.h',
        identifier_prefix: 'GArrow',
        includes: ['GObject-2.0', 'Gio-2.0'],
        namespace: 'Arrow',
        sources: sources + c_headers + enums,
        symbol_prefix: 'garrow',
        kwargs: generate_gi_common_args,
    )

    if generate_vapi
        arrow_glib_vapi = gnome.generate_vapi(
            'arrow-glib',
            install: true,
            packages: ['gio-2.0'],
            sources: [arrow_glib_gir[0]],
        )
    endif
endif
