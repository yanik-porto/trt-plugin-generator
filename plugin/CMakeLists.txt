#
# Copyright (c) 2020, NVIDIA CORPORATION. All rights reserved.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#
add_custom_target(plugin)

set(TARGET_NAME nvinfer_plugin)
set(SHARED_TARGET ${TARGET_NAME})
set(STATIC_TARGET ${TARGET_NAME}_static)

set(TARGET_DIR ${CMAKE_CURRENT_SOURCE_DIR})
set(PLUGIN_EXPORT_MAP ${TARGET_DIR}/exports.map)

if(${CMAKE_BUILD_TYPE} MATCHES "Debug")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g")
endif()

set(PLUGIN_SOURCES)
set(PLUGIN_CU_SOURCES)

set(PLUGIN_LISTS
    gridSamplerPlugin
    )

# Add BERT sources if ${BERT_GENCODES} was populated
if(BERT_GENCODES)
    set(BERT_CU_SOURCES)
    set(PLUGIN_LISTS
        ${PLUGIN_LISTS}
        )
endif()

include_directories(common common/kernels ../samples/common)

foreach(PLUGIN_ITER ${PLUGIN_LISTS})
    include_directories(${PLUGIN_ITER} /home/yannick/repos/TensorRT/include)
    add_subdirectory(${PLUGIN_ITER})
    add_library(${PLUGIN_ITER} SHARED ${PLUGIN_SOURCES} ${PLUGIN_CU_SOURCES})
endforeach(PLUGIN_ITER)

# Add common
add_subdirectory(common)

# Set gencodes
set_source_files_properties(${PLUGIN_CU_SOURCES} PROPERTIES COMPILE_FLAGS ${GENCODES})
list(APPEND PLUGIN_SOURCES "${PLUGIN_CU_SOURCES}")
if (BERT_CU_SOURCES)
    set_source_files_properties(${BERT_CU_SOURCES} PROPERTIES COMPILE_FLAGS ${BERT_GENCODES})
    list(APPEND PLUGIN_SOURCES "${BERT_CU_SOURCES}")
endif()