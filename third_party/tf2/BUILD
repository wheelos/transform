load("@rules_cc//cc:defs.bzl", "cc_library")

package(
    default_visibility = ["//modules/transform:__pkg__"],
)

cc_library(
    name = "tf2_headers",
    hdrs = glob([
        "include/geometry_msgs/**",
        "include/tf2_msgs/**",
        "include/tf2/**",
    ]),
    strip_include_prefix = "include",
)

cc_library(
    name = "tf2",
    srcs = [
        "src/buffer_core.cpp",
        "src/cache.cpp",
        "src/static_cache.cpp",
        "src/time.cpp",
    ],
    deps = [
        ":tf2_headers",
        "@boost.container",
        "@boost.foreach",
        "@boost.function",
        "@boost.lexical_cast",
        "@boost.signals2",
        "@boost.thread",
        "@boost.tuple",
    ],
)
