cc_library(
    name = "nghttp2_lib",
    srcs = glob([
        "lib/libnghttp2.a",
    ]),
    hdrs = glob([
        "include/nghttp2/nghttp2.h",
        "include/nghttp2/nghttp2ver.h",
    ]),
    includes = ["include"],
    linkstatic = 1,
    visibility = ["//visibility:public"],
    deps = [
         "@boringssl//:ssl",
         "@boringssl//:crypto",
    ],
    alwayslink = 1,
)

cc_library(
    name = "nghttp2_asio_lib",
    srcs = glob([
        "lib/libnghttp2_asio.a",
    ]),
    hdrs = glob([
        "include/nghttp2/asio_http2.h",
        "include/nghttp2/asio_http2_client.h",
        "include/nghttp2/asio_http2_server.h",
    ]),
    includes = ["include"],
    linkstatic = 1,
    visibility = ["//visibility:public"],
    deps = [
         "@boost//:asio",
         "@boost//:system",
         "@boost//:thread",
         ":nghttp2_lib",
    ],
    alwayslink = 1,
)
