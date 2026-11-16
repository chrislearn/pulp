# Compatible 0.22 maintenance backport

This branch keeps the published `pulp` 0.22.3 API and feature set needed by
`exr` 1.74.2. It consumes the maintained `pastey` implementation directly;
there is no package-name shim and no image format has been removed.

The following fixes are already merged in the upstream 0.23 development line
and are backported here with their original authors and `cherry-pick -x` links:

- `601de0c071ba32df3fd7ff33fc4218c9bd712aba` / upstream #39: replace unmaintained paste.
- `0807ff9f3ea6cf4613d929431f2a44a5db6975f8` / upstream #37: correct the second
  256-bit half's pointer offset in V3_512b masked loads.
- `4747da19fc0c56a94f52ca4facbf26a7eb378c1b` / upstream #36: implement signed
  64-bit minimum using minimum rather than maximum.

The upstream masked-load regression is retained and a minimum regression covers
mixed signs and integer extremes. The fork's validation runs on Linux x86_64,
checks the original 1.84 MSRV without dev dependencies, and compiles both WASM
feature configurations. Native aarch64 tests and x86_64 cross-compilation are
also run locally. An unsupported SIMD CPU can skip those architecture-specific
test bodies; the Linux logs explicitly report when AVX2 regressions execute.

Two compatibility fixes keep validation strict on current toolchains: benchmark
fixtures retain 128-byte alignment but allocate their large buffers on the heap,
and AArch64 no_std detection maps the runtime `pmull` / `asimd` aliases to rustc's
`aes` / `neon` target features. The latter is cross-checked with and without AES
enabled; standard-library runtime detection is unchanged.

This is a temporary exact-revision Cargo patch until a compatible maintained
release is available. The upstream main branch already contains these fixes;
no duplicate PR against that branch is being opened. The workflow is scoped to
this maintenance branch and does not request macOS runner capacity.
