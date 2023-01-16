protobuf_generate_cpp(PROTO_SRC PROTO_HEADER
        sentencepiece.proto
        sentencepiece_model.proto
        )

add_library(proto OBJECT ${PROTO_HEADER} ${PROTO_SRC})
set_source_files_properties(${PROTO_SRC} ${PROTO_HEADER} PROPERTIES GENERATED TRUE)

target_link_libraries(proto PUBLIC protobuf::libprotobuf-lite)
target_include_directories(proto PRIVATE
        ${Protobuf_INCLUDE_DIRS}
        ${CMAKE_CURRENT_BINARY_DIR})

set(ProtobufIncludePath ${CMAKE_CURRENT_BINARY_DIR} CACHE INTERNAL "Path to generated protobuf files.")

message(STATUS "Compiling proto ...")
message(STATUS "proto include path: ${ProtobufIncludePath}")
message(STATUS "proto HEADER: ${PROTO_HEADER}")
message(STATUS "proto SRC: ${PROTO_SRC}")
