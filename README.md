# WheelOS Transform

An independent `wheelos_transform` Bzlmod module for transform queries,
time-aware resolution, Cyber transport and validated static TF publication.
It is not part of hardware and has no dependency on functional drivers or the
Apollo product.

```starlark
bazel_dep(name = "wheelos_transform", version = "0.1.0")
```

Use `@wheelos_transform//modules/transform:transform_query` for the query API
and `:libstatic_transform_component.so` for the mainboard plugin. Existing
`modules/transform/...` C++ includes remain unchanged. See
[the API and calibration contract](modules/transform/README.md).

The vendored tf2 implementation lives only in this module. It is private
implementation, not a public dependency for consumers. Query APIs retain public
visibility; the calibration loader and test-only buffer peer grant access only
to their existing product consumers. Internal users in the same Bazel package
retain access without a public visibility grant.

`hardware.build` supplies shared build and installation rules, not runtime
hardware services. All installation consumers share its `InstallInfo` provider.
The product assembles the plugin, mainboard, runtime libraries and configuration
into one relocatable source/runtime archive layout.

These boundaries use native [Bzlmod dependencies](https://bazel.build/external/module),
[target visibility](https://bazel.build/concepts/visibility) and
[C++ shared binaries](https://bazel.build/reference/be/c-cpp#cc_binary.linkshared);
mainboard loads filesystem paths, not Bazel labels.

Local development uses `.bazelrc.dev` in the product, with
`wheelos_transform` overridden to this checkout. The source remote and Registry
publication are not provisioned yet. Do not deliver only the product's source
deletions: this ignored checkout must accompany them until source publication
and override-free Registry consumption are verified.
