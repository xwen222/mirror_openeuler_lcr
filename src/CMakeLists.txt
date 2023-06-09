# lcr: utils library for iSula
#
# Copyright (c) Huawei Technologies Co., Ltd. 2020. All rights reserved.
#
# Authors:
# Haozi007 <liuhao27@huawei.com>
#
# This library is free software; you can redistribute it and/or
# modify it under the terms of the GNU Lesser General Public
# License as published by the Free Software Foundation; either
# version 2.1 of the License, or (at your option) any later version.
#
# This library is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
# Lesser General Public License for more details.
#
# You should have received a copy of the GNU Lesser General Public
# License along with this library; if not, write to the Free Software
# Foundation, Inc., 51 Franklin Street, Fifth Floor, Boston, MA  02110-1301  USA
#

set(check_incs
    ${LIBLXC_INCLUDE_DIR}
    )

set(check_libs
    ${LIBLXC_LIBRARY}
    )

list(REMOVE_DUPLICATES check_incs)
list(REMOVE_DUPLICATES check_libs)

set(check_isula_utils_incs
    ${LIBYAJL_INCLUDE_DIR}
    )
set(check_isula_utils_libs
    ${LIBYAJL_LIBRARY}
    )
list(REMOVE_DUPLICATES check_isula_utils_libs)
list(REMOVE_DUPLICATES check_isula_utils_incs)


# generate .c and .h files for json
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/json)

# get json source files
aux_source_directory(${CMAKE_BINARY_DIR}/json generatesrcs)

aux_source_directory(${CMAKE_CURRENT_SOURCE_DIR}/json topjsonsrcs)

# get top source files
aux_source_directory(${CMAKE_CURRENT_SOURCE_DIR} topsrcs)
list(REMOVE_ITEM topsrcs "${CMAKE_CURRENT_SOURCE_DIR}/utils.c")
message("--  Get top srcs: " ${topsrcs})

set(isula_libutils_srcs
    ${CMAKE_CURRENT_SOURCE_DIR}/utils.c
    ${THIRD_PARTY_SRCS}
    ${generatesrcs}
    ${commonjsonsrcs}
    ${topjsonsrcs}
    )

set(isula_libutils_incs
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${CMAKE_CURRENT_SOURCE_DIR}/json
    ${CMAKE_CURRENT_SOURCE_DIR}/json/schema/src
    ${CMAKE_BINARY_DIR}/json
    ${THIRD_PARTY_INCS}
    ${check_isula_utils_incs}
    )

# set libisula_libutils library
add_library(isula_libutils
    SHARED
    ${isula_libutils_srcs}
    )

# set include dirs
#PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/utils
target_include_directories(isula_libutils
    PUBLIC ${isula_libutils_incs}
    )

target_link_libraries(isula_libutils ${check_isula_utils_libs})

add_library(isula_libutils_s
    STATIC
    ${isula_libutils_srcs}
    )

target_include_directories(isula_libutils_s
    PUBLIC ${isula_libutils_incs}
    )
target_link_libraries(isula_libutils_s ${check_isula_utils_libs})
set_target_properties(isula_libutils_s PROPERTIES OUTPUT_NAME isula_libutils)

if (ENABLE_LIBLCR)
# set liblcr library
add_library(liblcr SHARED ${topsrcs})

set(liblcr_incs
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${CMAKE_BINARY_DIR}/conf
    ${CMAKE_CURRENT_SOURCE_DIR}/json/schema/src
    ${CMAKE_CURRENT_SOURCE_DIR}/json
    ${check_incs}
)

# set liblcr include dirs
target_include_directories(liblcr
    PUBLIC ${liblcr_incs}
    )

# set liblcr compile flags
set_target_properties(liblcr PROPERTIES PREFIX "")
target_link_libraries(liblcr ${check_libs} isula_libutils)

if (CMAKE_TOOLCHAIN_FILE)
    target_link_directories(liblcr PUBLIC ${CMAKE_LIBRARY_PATH})
    target_link_libraries(liblcr ${EXTRAL_LINK_LIBS})
endif()


add_library(liblcr_s STATIC ${topsrcs})
target_include_directories(liblcr_s
    PUBLIC ${liblcr_incs}
    )
target_link_libraries(liblcr_s ${check_libs} isula_libutils)
set_target_properties(liblcr_s PROPERTIES PREFIX "")
set_target_properties(liblcr_s PROPERTIES OUTPUT_NAME liblcr)

# install all files
install(TARGETS liblcr
    LIBRARY DESTINATION ${LIB_INSTALL_DIR_DEFAULT} PERMISSIONS OWNER_READ OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE)
install(TARGETS liblcr_s
    ARCHIVE DESTINATION ${LIB_INSTALL_DIR_DEFAULT} PERMISSIONS OWNER_READ OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE)
endif()

install(TARGETS isula_libutils
    LIBRARY DESTINATION ${LIB_INSTALL_DIR_DEFAULT} PERMISSIONS OWNER_READ OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE)
install(TARGETS isula_libutils_s
    ARCHIVE DESTINATION ${LIB_INSTALL_DIR_DEFAULT} PERMISSIONS OWNER_READ OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE)
