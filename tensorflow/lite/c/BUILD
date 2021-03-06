load(
    "//tensorflow/lite:build_def.bzl",
    "tflite_cc_shared_object",
    "tflite_copts",
)

package(
    default_visibility = [":experimental"],
    licenses = ["notice"],  # Apache 2.0
)

package_group(
    name = "experimental",
    packages = [
        "//tensorflow/lite/...",
        "//third_party/dart/tflite_native/...",  # whitelisted
    ],
)

# Generates a platform-specific shared library containing the TensorFlow Lite C
# API implementation as define in `c_api.h`. The exact output library name
# is platform dependent:
#   - Linux/Android: `libtensorflowlite_c.so`
#   - Mac: `libtensorflowlite_c.dylib`
#   - Windows: `tensorflowlite_c.dll`
tflite_cc_shared_object(
    name = "tensorflowlite_c",
    linkopts = select({
        "//tensorflow:macos": [
            "-Wl,-exported_symbols_list,$(location //tensorflow/lite/c:exported_symbols.lds)",
        ],
        "//tensorflow:windows": [],
        "//conditions:default": [
            "-z defs",
            "-Wl,--version-script,$(location //tensorflow/lite/c:version_script.lds)",
        ],
    }),
    per_os_targets = True,
    deps = [
        ":c_api",
        ":c_api_experimental",
        ":exported_symbols.lds",
        ":version_script.lds",
    ],
)

cc_library(
    name = "c_api_internal",
    srcs = [
        "c_api.h",
        "common.h",
    ],
    hdrs = ["c_api_internal.h"],
    copts = tflite_copts(),
    visibility = ["//visibility:private"],
    deps = [
        ":common",
        "//tensorflow/lite:framework",
    ],
)

cc_library(
    name = "c_api",
    srcs = ["c_api.cc"],
    hdrs = [
        "c_api.h",
        "common.h",
    ],
    copts = tflite_copts(),
    visibility = [
        ":experimental",
    ],
    deps = [
        ":c_api_internal",
        ":common",
        "//tensorflow/lite:framework",
        "//tensorflow/lite:version",
        "//tensorflow/lite/kernels:builtin_ops",
    ],
    alwayslink = 1,
)

cc_library(
    name = "c_api_experimental",
    srcs = ["c_api_experimental.cc"],
    hdrs = ["c_api_experimental.h"],
    copts = tflite_copts(),
    deps = [
        ":c_api",
        ":c_api_internal",
        "//tensorflow/lite:kernel_api",
    ],
    alwayslink = 1,
)

cc_test(
    name = "c_api_test",
    size = "small",
    srcs = ["c_api_test.cc"],
    data = [
        "//tensorflow/lite:testdata/add.bin",
        "//tensorflow/lite:testdata/add_quantized.bin",
    ],
    deps = [
        ":c_api",
        "//tensorflow/lite/c:c_api_internal",
        "//tensorflow/lite/testing:util",
        "@com_google_googletest//:gtest",
    ],
)

cc_test(
    name = "c_api_experimental_test",
    size = "small",
    srcs = ["c_api_experimental_test.cc"],
    data = ["//tensorflow/lite:testdata/add.bin"],
    deps = [
        ":c_api",
        ":c_api_experimental",
        "//tensorflow/lite:kernel_api",
        "//tensorflow/lite/testing:util",
        "@com_google_googletest//:gtest",
    ],
)

cc_library(
    name = "common",
    srcs = ["common.c"],
    hdrs = [
        "builtin_op_data.h",
        "common.h",
    ],
    visibility = [
        "//tensorflow/lite:__subpackages__",
    ],
    alwayslink = 1,
)

# For use with library targets that can't use relative paths.
exports_files([
    "c_api.h",
    "common.h",
])

# Test the C extension API code.
cc_test(
    name = "common_test",
    size = "small",
    srcs = ["common_test.cc"],
    deps = [
        ":common",
        "@com_google_googletest//:gtest",
    ],
)

cc_test(
    name = "builtin_op_data_test",
    size = "small",
    srcs = ["builtin_op_data_test.cc"],
    copts = ["-Wno-unused-variable"],
    deps = [
        ":common",
        "@com_google_googletest//:gtest",
    ],
)
