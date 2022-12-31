# Copyright 2018 Google Inc.
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
# limitations under the License.!

set(SRCS
        bpe_model.cc
        char_model.cc
        error.cc
        filesystem.cc
        init.cc
        model_factory.cc
        model_interface.cc
        normalizer.cc
        sentencepiece_processor.cc
        unigram_model.cc
        util.cc
        word_model.cc
        ${CMAKE_CURRENT_SOURCE_DIR}/../third_party/absl/strings/string_view.cc
        ${CMAKE_CURRENT_SOURCE_DIR}/../third_party/absl/flags/flag.cc)

set(TRAIN_SRCS
        builder.cc
        unicode_script.cc
        trainer_factory.cc
        trainer_interface.cc
        unigram_model_trainer.cc
        word_model_trainer.cc
        char_model_trainer.cc
        bpe_model_trainer.cc
        sentencepiece_trainer.cc
        pretokenizer_for_training.cc)

find_package(Threads REQUIRED)
find_package(Protobuf REQUIRED)

protobuf_generate_cpp(SPM_PROTO_SRCS SPM_PROTO_HDRS sentencepiece.proto)
protobuf_generate_cpp(SPM_MODEL_PROTO_SRCS SPM_MODEL_PROTO_HDRS sentencepiece_model.proto)

list(APPEND SRCS ${SPM_MODEL_PROTO_SRCS} ${SPM_PROTO_SRCS})

add_library(sentencepiece-browsermt STATIC ${SRCS})
add_library(sentencepiece_train-browsermt STATIC ${TRAIN_SRCS})

target_compile_definitions(sentencepiece-browsermt PRIVATE _USE_INTERNAL_STRING_VIEW=1)
target_compile_definitions(sentencepiece_train-browsermt PRIVATE _USE_INTERNAL_STRING_VIEW=1)

set_target_properties(sentencepiece-browsermt PROPERTIES
        VERSION "${CMAKE_PROJECT_VERSION_MAJOR}.${CMAKE_PROJECT_VERSION_MINOR}.${CMAKE_PROJECT_VERSION_PATCH}"
        SOVERSION "${CMAKE_PROJECT_VERSION_MAJOR}"
        OUTPUT_NAME sentencepiece-browsermt)
set_target_properties(sentencepiece_train-browsermt PROPERTIES
        OUTPUT_NAME sentencepiece_train-browsermt
        CXX_STANDARD 17
        VERSION "${CMAKE_PROJECT_VERSION_MAJOR}.${CMAKE_PROJECT_VERSION_MINOR}.${CMAKE_PROJECT_VERSION_PATCH}"
        SOVERSION "${CMAKE_PROJECT_VERSION_MAJOR}"
        )

target_link_libraries(sentencepiece-browsermt PRIVATE ${PROTOBUF_LITE_LIBRARY} Threads::Threads)
target_link_libraries(sentencepiece_train-browsermt PRIVATE ${PROTOBUF_LITE_LIBRARY} Threads::Threads)

#==========

target_include_directories(sentencepiece-browsermt
        PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>
        PUBLIC $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
        )

target_include_directories(sentencepiece_train-browsermt
        PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>
        PUBLIC $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
        )

install(TARGETS sentencepiece-browsermt sentencepiece_train-browsermt
        EXPORT sentencepiece-browsermtTargets
        RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
        )

install(FILES
          include/sentencepiece-browsermt/sentencepiece_trainer.h
          include/sentencepiece-browsermt/sentencepiece_processor.h
        DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/sentencepiece-browsermt/)


install(EXPORT sentencepiece-browsermtTargets
        FILE sentencepiece-browsermtTargets.cmake
        NAMESPACE sentencepiece-browsermt::
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/sentencepiece-browsermt
        )

configure_package_config_file(${CMAKE_CURRENT_SOURCE_DIR}/../Config.cmake.in
        "${CMAKE_CURRENT_BINARY_DIR}/sentencepiece-browsermtConfig.cmake"
        INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/sentencepiece-browsermt
        )

write_basic_package_version_file(
        "${CMAKE_CURRENT_BINARY_DIR}/sentencepiece-browsermtConfigVersion.cmake"
        VERSION "${CMAKE_PROJECT_VERSION}"
        COMPATIBILITY AnyNewerVersion
)

install(FILES
        "${CMAKE_CURRENT_BINARY_DIR}/sentencepiece-browsermtConfig.cmake"
        "${CMAKE_CURRENT_BINARY_DIR}/sentencepiece-browsermtConfigVersion.cmake"
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/sentencepiece-browsermt
        )
