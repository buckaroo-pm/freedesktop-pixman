load('//:subdir_glob.bzl', 'subdir_glob')
load('//:buckaroo_macros.bzl', 'buckaroo_deps')

platform_srcs = [
  "pixman/pixman-arm-neon.c",
  "pixman/pixman-arm-simd.c",
  "pixman/pixman-mips-dspr2.c",
  "pixman/pixman-mmx.c",
  "pixman/pixman-region.c",
  "pixman/pixman-sse2.c",
  "pixman/pixman-ssse3.c",
  "pixman/pixman-vmx.c"
]

genrule(
  name = 'pixman-version',
  out = 'pixman-version.h',
  srcs = ['pixman/pixman-version.h.in'],
  cmd = " ".join([
    "cat $SRCS |",
    "sed",
    "-e 's/@PIXMAN_VERSION_MAJOR@/0/'", 
    "-e 's/@PIXMAN_VERSION_MINOR@/35/'", 
    "-e 's/@PIXMAN_VERSION_PATCH@/1/'", 
    "> $OUT",
  ])
)

configH = [('config.h', 'config.h')]
private_headers = dict(
  subdir_glob([
    ("pixman", "**/*.h"),
    ("test", "**/*.h"),
    ("pixman", "**/*.c")
  ]).items() + configH
)

cxx_library(
  name = "pixman-1",
  header_namespace = '',
  exported_headers = {
    "pixman/pixman.h": "pixman/pixman.h",
    "pixman/pixman-version.h": ":pixman-version",
  },
  headers = private_headers,
  srcs = glob(
    ["pixman/*.c"], 
    exclude = platform_srcs),
  platform_srcs = [
    ('.*arm', ["pixman/pixman-arm-neon.c", "pixman/pixman-arm-simd.c"]),
    ('.*x86_64', [
      "pixman/pixman-mmx.c", 
      "pixman/pixman-sse2.c", 
      "pixman/pixman-ssse3.c"])
  ],
  exported_linker_flags = ['-fopenmp'],
  visibility = ['PUBLIC'],
  deps = buckaroo_deps()
)

cxx_library(
  name = "test-utils",
  header_namespace = '',
  exported_headers = private_headers,
  srcs = ["test/utils.c", "test/utils-prng.c"],
  deps = [":pixman-1"],
  visibility = []
)

cxx_binary(
  name = "blitters-test", 
  srcs = ["test/thread-test.c"],
  deps = [":test-utils"], 
)

cxx_binary(
  name = "solid-test", 
  srcs = ["test/solid-test.c"],
  deps = [":test-utils"], 
)


cxx_binary(
  name = "oob-test", 
  srcs = ["test/oob-test.c"],
  deps = [":test-utils"], 
)

