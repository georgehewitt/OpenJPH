# AVX-512 validation helper

Single-purpose branch carrying `test.sh`, an automated validator for the
[`avx512-reversible-wavelet-dispatch`](https://github.com/georgehewitt/OpenJPH/tree/avx512-reversible-wavelet-dispatch)
patch.

## Usage (Arch Linux, AVX-512 CPU)

```
curl -sSfL https://raw.githubusercontent.com/georgehewitt/OpenJPH/avx512-validation-helper/test.sh | bash
```

What it does (~3-4 min):

1. Confirms the CPU has AVX-512F + lists variants
2. `sudo pacman -S --needed` for `base-devel cmake git python coreutils` if missing
3. Clones unpatched (`aous72/OpenJPH master`) and patched
   (`georgehewitt/OpenJPH avx512-reversible-wavelet-dispatch`)
4. Builds both in Release
5. Runs `ctest` on the patched build
6. Encodes a 2048×2048×16-bit synthetic image in reversible mode with both
   binaries; verifies the codestreams are byte-identical
7. Times 5 encodes per build, reports median and speedup ratio
8. Writes one structured report to `~/openjph-avx512-test/result.txt` and
   tells the friend exactly which file to send back

Exit codes: 0 OK · 1 no AVX-512 · 2 build fail · 3 ctest fail · 4 codestream divergence.

This branch is not part of the upstream PR. It exists only to host the
validation script so external testers can run it via the curl one-liner
above.
