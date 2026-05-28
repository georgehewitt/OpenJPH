# AVX-512 validation helper

Single-purpose branch carrying `test.sh`, an automated validator for the
[`avx512-reversible-wavelet-dispatch`](https://github.com/georgehewitt/OpenJPH/tree/avx512-reversible-wavelet-dispatch)
patch.

## Run on an Arch Linux box with AVX-512

Use process substitution (NOT a plain `curl … | bash` pipe — that would
swallow your tty and `sudo pacman` couldn't prompt for your password):

```
bash <(curl -sSfL https://raw.githubusercontent.com/georgehewitt/OpenJPH/avx512-validation-helper/test.sh)
```

Or download then run:

```
curl -sSfLo /tmp/test.sh https://raw.githubusercontent.com/georgehewitt/OpenJPH/avx512-validation-helper/test.sh
bash /tmp/test.sh
```

## What it does (~3-4 min)

1. Confirms the CPU has AVX-512F + lists variants present
2. `sudo pacman -S --needed` for `base-devel cmake git python coreutils` if missing
3. Clones unpatched (`aous72/OpenJPH master`) and patched
   (`georgehewitt/OpenJPH avx512-reversible-wavelet-dispatch`)
4. Builds both in Release (full build logs kept under `~/openjph-avx512-test/`)
5. Runs `ctest` on the patched build
6. Encodes a 2048×2048×16-bit synthetic image in reversible mode with both
   binaries; verifies the codestreams are byte-identical
7. Times 5 encodes per build, reports median and speedup ratio
8. Writes one structured report to `~/openjph-avx512-test/result.txt`

When it finishes it tells you exactly which file to send back.

## Exit codes

| code | meaning |
|---:|---|
| 0 | OK — bit-exact and (usually) faster |
| 1 | no AVX-512 on this CPU |
| 2 | build failed (last 40 lines included in result.txt) |
| 3 | ctest failed |
| 4 | codestream divergence — codestreams.tar.{xz,gz} also bundled |

This branch is not part of the upstream PR. It exists only to host the
validation script so external testers can run it via the one-liner above.
