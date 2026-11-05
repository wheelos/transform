load("@core//runtime:runtime.bzl", "runtime_module")
load("@rules_cc//cc:defs.bzl", "cc_binary", "cc_library", "cc_test")
load("@rules_pkg//pkg:install.bzl", "pkg_install")
load("@rules_pkg//pkg:mappings.bzl", "pkg_attributes", "pkg_filegroup", "pkg_files", "strip_prefix")

package(default_visibility = ["//visibility:private"])

runtime_module(
    name = "module",
    components = [":libstatic_transform_component.so"],
    files = {
        "runtime/static_transform.dag": "dag/transform/static_transform.dag",
        "runtime/static_transform.launch": "launch/transform/static_transform.launch",
    },
    module_name = "transform",
    visibility = ["//visibility:public"],
)

PUBLIC_HDRS = [
    "buffer.h",
    "buffer_interface.h",
    "ground_reference.h",
    "timed_transform_resolver.h",
    "transform_broadcaster.h",
    "transform_diagnostics.h",
    "transform_query.h",
]

cc_library(
    name = "ground_reference",
    srcs = ["internal/ground_reference.cc"],
    hdrs = ["ground_reference.h"],
    visibility = ["//visibility:public"],
    deps = [
        ":transform_query",
        "@core//cyber/common:log",
        "@eigen",
    ],
)

cc_library(
    name = "buffer",
    srcs = [
        "internal/buffer.cc",
        "internal/buffer_impl.cc",
        "internal/buffer_impl.h",
        "internal/transform_listener.cc",
        "internal/transform_listener.h",
    ],
    hdrs = [
        "buffer.h",
    ],
    visibility = ["//visibility:public"],
    deps = [
        ":buffer_interface",
        "//third_party/tf2",
        "@com_google_absl//absl/strings",
        "@core//cyber",
        "@wheelos_common//modules/common/adapters:adapter_gflags",
        "@wheelos_msgs//wheelos_msgs/transform_msgs:transform_cc_proto",
    ],
)

cc_library(
    name = "transform_query",
    srcs = ["internal/transform_query.cc"],
    hdrs = ["transform_query.h"],
    visibility = ["//visibility:public"],
    deps = [
        ":buffer_interface",
        "@core//cyber/common:log",
        "@core//cyber/time",
        "@eigen",
        "@wheelos_msgs//wheelos_msgs/transform_msgs:transform_cc_proto",
    ],
)

cc_library(
    name = "timed_transform_resolver",
    srcs = [
        "internal/timed_transform_resolver.cc",
        "internal/transform_cache.cc",
        "internal/transform_cache.h",
    ],
    hdrs = ["timed_transform_resolver.h"],
    visibility = ["//visibility:public"],
    deps = [
        ":transform_query",
        "@core//cyber/common:log",
        "@core//cyber/time",
        "@eigen",
        "@wheelos_common//modules/common/util:string_util",
    ],
)

cc_library(
    name = "buffer_interface",
    hdrs = [
        "buffer_interface.h",
    ],
    visibility = ["//visibility:public"],
    deps = [
        ":transform_diagnostics",
        "@core//cyber/time",
        "@wheelos_msgs//wheelos_msgs/transform_msgs:transform_cc_proto",
    ],
)

cc_library(
    name = "transform_diagnostics",
    srcs = ["internal/transform_diagnostics.cc"],
    hdrs = ["transform_diagnostics.h"],
    visibility = ["//visibility:public"],
    deps = ["@com_github_nlohmann_json//:json"],
)

cc_library(
    name = "buffer_test_peer",
    testonly = True,
    srcs = [
        "internal/buffer_impl.h",
        "internal/transform_listener.h",
        "tests/buffer_test_peer.cc",
    ],
    hdrs = ["tests/buffer_test_peer.h"],
    visibility = [
        "@@//modules/dreamview/backend/point_cloud:__pkg__",
        "@@//modules/localization:__pkg__",
        "@@//modules/ndt_localization:__pkg__",
        "@@//modules/perception/onboard/transform_wrapper:__pkg__",
    ],
    deps = [
        ":buffer",
        "//third_party/tf2",
        "@core//cyber",
    ],
)

cc_test(
    name = "transform_query_test",
    size = "small",
    srcs = ["tests/transform_query_test.cc"],
    deps = [
        ":buffer_interface",
        ":transform_query",
        "@com_github_nlohmann_json//:json",
        "@com_google_googletest//:gtest_main",
        "@eigen",
    ],
)

cc_test(
    name = "timed_transform_resolver_test",
    size = "small",
    srcs = [
        "internal/transform_cache.h",
        "tests/timed_transform_resolver_test.cc",
    ],
    deps = [
        ":buffer_interface",
        ":timed_transform_resolver",
        ":transform_query",
        "@com_google_googletest//:gtest_main",
        "@eigen",
    ],
)

cc_library(
    name = "transform_broadcaster",
    srcs = [
        "internal/transform_broadcaster.cc",
    ],
    hdrs = ["transform_broadcaster.h"],
    visibility = ["//visibility:public"],
    deps = [
        "@core//cyber",
        "@wheelos_common//modules/common/adapters:adapter_gflags",
        "@wheelos_msgs//wheelos_msgs/transform_msgs:transform_cc_proto",
    ],
)

cc_library(
    name = "static_transform_loader",
    srcs = ["calibration/static_transform_loader.cc"],
    hdrs = ["calibration/static_transform_loader.h"],
    visibility = [
        "@@//modules/dreamview/backend/point_cloud:__pkg__",
        "@@//modules/perception/onboard/transform_wrapper:__pkg__",
        "@@//modules/tools/vehicle_calibration:__pkg__",
    ],
    deps = [
        "@com_github_jbeder_yaml_cpp//:yaml-cpp",
        "@core//cyber/common:log",
        "@wheelos_common//modules/common/vehicle_calibration:registry",
        "@wheelos_msgs//wheelos_msgs/transform_msgs:transform_cc_proto",
    ],
)

cc_test(
    name = "static_transform_loader_test",
    srcs = ["tests/static_transform_loader_test.cc"],
    deps = [
        ":static_transform_loader",
        "@com_google_googletest//:gtest_main",
        "@eigen",
        "@wheelos_common//modules/common/vehicle_calibration:test_bundle",
    ],
)

cc_test(
    name = "buffer_static_test",
    srcs = ["tests/buffer_static_test.cc"],
    deps = [
        ":buffer",
        ":buffer_test_peer",
        ":ground_reference",
        ":transform_query",
        "@com_google_googletest//:gtest_main",
        "@core//cyber",
        "@eigen",
    ],
)

cc_library(
    name = "static_transform_component_lib",
    srcs = ["components/static_transform_component.cc"],
    hdrs = ["components/static_transform_component.h"],
    copts = ['-DMODULE_NAME=\\"static_transform\\"'],
    deps = [
        ":static_transform_loader",
        "@core//cyber",
        "@wheelos_common//modules/common/adapters:adapter_gflags",
        "@wheelos_common//modules/common/util:util_tool",
        "@wheelos_common//modules/common/vehicle_calibration:registry",
        "@wheelos_msgs//wheelos_msgs/transform_msgs:transform_cc_proto",
    ],
    alwayslink = True,
)

cc_test(
    name = "static_transform_component_test",
    size = "small",
    srcs = ["tests/static_transform_component_test.cc"],
    copts = ["-fno-access-control"],
    data = [
        "@core//cyber:cyber_conf",
    ],
    deps = [
        ":buffer",
        ":buffer_test_peer",
        ":static_transform_component_lib",
        "@bazel_tools//tools/cpp/runfiles",
        "@com_google_googletest//:gtest",
        "@core//cyber",
        "@wheelos_common//modules/common/adapters:adapter_gflags",
        "@wheelos_common//modules/common/vehicle_calibration:test_bundle",
    ],
)

cc_binary(
    name = "libstatic_transform_component.so",
    linkshared = True,
    linkstatic = False,
    visibility = ["//visibility:public"],
    deps = [":static_transform_component_lib"],
)

pkg_files(
    name = "package_files_data",
    srcs = [":runtime_data"],
    attributes = pkg_attributes(mode = "0644"),
    prefix = "transform",
    strip_prefix = strip_prefix.from_root("modules/transform"),
)

pkg_files(
    name = "package_files_libraries",
    srcs = [":libstatic_transform_component.so"],
    attributes = pkg_attributes(mode = "0755"),
    prefix = "transform/lib",
    strip_prefix = strip_prefix.from_root("modules/transform"),
)

pkg_files(
    name = "package_files_archives",
    srcs = [
        ":buffer",
        ":ground_reference",
        ":timed_transform_resolver",
        ":transform_broadcaster",
        ":transform_diagnostics",
        ":transform_query",
    ],
    attributes = pkg_attributes(mode = "0644"),
    prefix = "lib",
    strip_prefix = strip_prefix.from_root("modules/transform"),
)

pkg_filegroup(
    name = "package_files",
    srcs = [
        ":package_files_archives",
        ":package_files_data",
        ":package_files_libraries",
    ],
    visibility = ["//visibility:public"],
)

pkg_install(
    name = "install_dir",
    srcs = [":package_files"],
    tags = ["manual"],
    visibility = ["//visibility:public"],
)

filegroup(
    name = "runtime_data",
    srcs = glob([
        "dag/*.dag",
        "launch/*.launch",
    ]),
    visibility = ["//visibility:public"],
)

pkg_filegroup(
    name = "source_files",
    srcs = [
        ":package_transform_hdrs",
        ":package_transform_src",
    ],
    visibility = ["//visibility:public"],
)

pkg_install(
    name = "source_dir",
    srcs = [":source_files"],
    tags = ["manual"],
    visibility = ["//visibility:public"],
)

pkg_files(
    name = "package_transform_src_data",
    srcs = glob([
        "**/*.cc",
        "**/*.h",
        "BUILD",
        "README.md",
        "dag/*.dag",
        "launch/*.launch",
    ]),
    attributes = pkg_attributes(mode = "0644"),
    prefix = "transform/src",
    strip_prefix = strip_prefix.from_pkg(),
)

pkg_filegroup(
    name = "package_transform_src",
    srcs = [":package_transform_src_data"],
)

pkg_files(
    name = "package_transform_hdrs_data",
    srcs = PUBLIC_HDRS,
    attributes = pkg_attributes(mode = "0644"),
    prefix = "transform/include",
    strip_prefix = strip_prefix.from_pkg(),
)

pkg_filegroup(
    name = "package_transform_hdrs",
    srcs = [":package_transform_hdrs_data"],
)
