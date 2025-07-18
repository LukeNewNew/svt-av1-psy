This fork is based on the unreleased [SVT-AV1-PSY 2.3.0-C](https://github.com/psy-ex/svt-av1-psy/tree/testing-2.3.0-C), and includes backports of changes and improvements made in 3.x versions of SVT-AV1-PSY and its continuation, [SVT-AV1-PSYEX](https://github.com/BlueSwordM/svt-av1-psyex).

Please note that this fork may not be a 1-to-1 copy of changes made in 3.x, and may include additional features or changes that could potentially change, break, or fix certain behaviour.

The primary branch is [testing-2.3.0-C](https://github.com/5fish/svt-av1-psy/tree/testing-2.3.0-C); other branches may include experiments and may be out of date.

Below is a non-exhaustive list of notable differences between 3.x and this fork:
- `--frame-luma-bias` has been renamed in 3.x to `--luminance-qp-bias`; this change has not been reflected here.
- `--hbd-md` has been renamed in 3.x to `--hbd-mds`; this change has not been reflected here.

# SVT-AV1-PSY

SVT-AV1-PSY is the Scalable Video Technology for AV1 (SVT-AV1 Encoder and Decoder) with perceptual enhancements for psychovisually optimal AV1 encoding. The goal is to create the best encoding implementation for perceptual quality with AV1.

### Feature Additions

- `--variance-boost-strength` *1 to 4* (**[Merged to Mainline](https://gitlab.com/AOMediaCodec/SVT-AV1/-/merge_requests/2195)**)

Provides control over our augmented AQ Modes 0 and 2 which can utilize variance information in each frame for more consistent quality under high/low contrast scenes. Four curve options are provided, and the default is curve 2. 1: mild, 2: gentle, 3: medium, 4: aggressive

- `--variance-octile` *1 to 8* (**[Merged to Mainline](https://gitlab.com/AOMediaCodec/SVT-AV1/-/merge_requests/2195)**)

Controls how "selective" the algorithm is when boosting superblocks, based on their low/high 8x8 variance ratio. A value of 1 is the least selective, and will readily boost a superblock if only 1/8th of the superblock is low variance. Conversely, a value of 8 will only boost if the *entire* superblock is low variance. Lower values increase bitrate. The default value is 6.

- `--enable-alt-curve` *0 and 1* (**[Merged to Mainline](https://gitlab.com/AOMediaCodec/SVT-AV1/-/merge_requests/2357)**)

Enable an alternative variance boost curve, with different bit allocation and visual characteristics. The default is 0.

- `Presets -2 & -3`

Terrifically slow encoding modes for research purposes.

- `Tune 3`

A new tune based on Tune 2 (SSIM) called SSIM with Subjective Quality Tuning. Generally harms metric performance in exchange for better visual fidelity.

- `Tune 4` (**[Ported to libaom](https://aomedia.googlesource.com/aom/+/refs/tags/v3.12.0)**)

Another new tune based on Tune 2 (SSIM) called Still Picture. Optimized for still images based on SSIMULACRA2 performance on the CID22 Validation test set. Not recommended for use outside of all-intra encoding.

- `--sharpness` *-7 to 7* (**[Merged to Mainline](https://gitlab.com/AOMediaCodec/SVT-AV1/-/merge_requests/2346)**)

A parameter for modifying loopfilter deblock sharpness and rate distortion to improve visual fidelity. The default is 1.

- `--dolby-vision-rpu` *path to file*

Set the path to a Dolby Vision RPU for encoding Dolby Vision video. SVT-AV1-PSY needs to be built with the `enable-libdovi` flag enabled in build.sh (see `./Build/linux/build.sh --help` for more info) (Thank you @quietvoid !)

- `Progress 3`

A new progress mode that provides more detailed information about the encoding process.

- `--fgs-table` *path to file* (**[Merged to Mainline](https://gitlab.com/AOMediaCodec/SVT-AV1/-/commit/ae7ce1abc5f3f7913624f728ae123f8b8c1e30de)**)

Argument for providing a film grain table for synthetic film grain (similar to aomenc's '--film-grain-table=' argument).

- `Extended CRF`

Provides a more versatile and granular way to set CRF. Range has been expanded to 70 (from 63) to help with ultra-low bitrate encodes, and can now be set in quarter-step (0.25) increments.

- `--qp-scale-compress-strength` *0 to 3*

Increases video quality temporal consistency, especially with clips that contain film grain and/or contain fast-moving objects.

- `--enable-dlf 2`

Enables a more accurate loop filter that prevents blocking, for a modest increase in compute time (most noticeable at presets 7 to 9).

- `Higher-quality presets for 8K and 16K`

Lowers the minimum available preset from 8 to 2 for higher-quality 8K and 16K encoding (64 GB of RAM recommended per encoding instance).

- `--frame-luma-bias` *0 to 100* (**[Merged to Mainline](https://gitlab.com/AOMediaCodec/SVT-AV1/-/merge_requests/2348)**)

Enables frame-level luma bias to improve quality in dark scenes by adjusting frame-level QP based on average luminance across each frame.

- `--max-32-tx-size` *0 and 1*

Restricts available transform sizes to a maximum of 32x32 pixels. Can help slightly improve detail retention at high fidelity CRFs.

- `--adaptive-film-grain` *0 and 1* (**[Merged to Mainline](https://gitlab.com/AOMediaCodec/SVT-AV1/-/merge_requests/2347)**)

Adaptively varies the film grain blocksize based on the resolution of the input video. Often greatly improves the consistency of film grain in the output video, reducing grain patterns.

- `--hdr10plus-json` *path to file*

Set the path to an HDR10+ JSON file for encoding HDR10+ video. SVT-AV1-PSY needs to be built with the `enable-hdr10plus` flag enabled in build.sh (see `./Build/linux/build.sh --help` for more info) (Thank you @quietvoid !)

- `--tf-strength` *0 to 4* (**[Merged to Mainline](https://gitlab.com/AOMediaCodec/SVT-AV1/-/merge_requests/2352)**)

Manually adjust temporal filtering strength to adjust the trade-off between fewer artifacts in motion and fine detail retention. Each increment is a 2x increase in temporal filtering strength; the default value of 1 is 4x weaker than mainline SVT-AV1's default temporal filter (which would be equivalent to 3 here).

- `--chroma-qm-min` & `--chroma-qm-max` *0 to 15*

Set the minimum & maximum quantization matrices for chroma planes. The defaults are 8 and 15, respectively. These options decouple chroma quantization matrix control from the luma quantization matrix options currently available, allowing for more control over chroma quality.

- `Odd dimension encoding support` (**[Merged to Mainline](https://gitlab.com/AOMediaCodec/SVT-AV1/-/merge_requests/2350)**)

Allows the encoder to accept content with odd width and/or height (e.g. 1920x817px). Messages like "Source Width/Height must be even for YUV_420 colorspace" are no more.

- `Reduced minimum width/height requirements` (**[Merged to Mainline](https://gitlab.com/AOMediaCodec/SVT-AV1/-/merge_requests/2356)**)

Allows the encoder to accept content with width and/or height as small as 4 pixels (e.g. 32x18px).

- `--noise-norm-strength` *0 to 4*

In a scenario where a video frame contains areas with fine textures or flat regions, noise normalization helps maintain visual quality by boosting certain AC coefficients. The default value is 1; a recommended value is 3.

- `--kf-tf-strength` *0 to 4*

Manually adjust temporal filtering strength specifically on keyframes. Each increment is a 2x increase in temporal filtering strength; a value of 1 is 4x weaker than mainline SVT-AV1's default temporal filter (which would be equivalent to 3 here). The default value is 1, which reduces alt-ref temporal filtering strength by 4x on keyframes.

- `--enable-tf 2` (**[Merged to Mainline](https://gitlab.com/AOMediaCodec/SVT-AV1/-/merge_requests/2352)**)

Adaptively varies temporal filtering strength based on 64x64 block error. This can slightly improve visual fidelity in scenes with fast motion or fine detail. Setting this to 2 will override `--tf-strength` and `--kf-tf-strength`, as their values will be automatically determined by the encoder.

- `--psy-rd` *0.0 to 6.0*

Configures psychovisual rate distortion strength to improve perceived quality by measuring and attempting to preserve the visual energy distribution of high-frequency details and textures. The default is 0.

- `--spy-rd` *0 to 2*

Configure psychovisually-oriented pathways that bias towards sharpness and detail retention, at the possible expense of increased blocking and banding. The default is 0, with 1 being the most aggressive and 2 being less aggressive.


### Modified Defaults

SVT-AV1-PSY has different defaults than mainline SVT-AV1 in order to provide better visual fidelity out of the box. They include:

- Default 10-bit color depth when given a 10-bit input.
- Disable film grain denoising by default, as it often harms visual fidelity. (**[Merged to Mainline](https://gitlab.com/AOMediaCodec/SVT-AV1/-/commit/8b39b41df9e07bbcdbd19ea618762c5db3353c03)**)
- Default to Tune 2 (SSIM) instead of Tune 1 (PSNR), as it reliably outperforms Tune 1 perceptually & throughout trusted metrics.
- Enable quantization matrices by default.
- Set minimum QM level to 2 by default for more consistent performance that min QM level 0 doesn't offer.
- Set minimum chroma QM level to 8 by default to prevent the encoder from picking suboptimal chroma QMs.
- `--enable-variance-boost` enabled by default.
- `--keyint -2` (the default) uses a ~10s GOP size instead of ~5s.
- `--sharpness 1` by default to prioritize encoder sharpness.
- Sharp transform optimizations (`--sharp-tx 1`) are enabled by default to supercharge svt-av1-psy psy-rd optimizations. It is recommended to disable it if you don't use `--psy-rd`, which is set to 0.5 by default.
- `--tf-strength 1` by default for much lower alt-ref temporal filtering to decrease blur for cleaner encoding.
- `--kf-tf-strength 1` controls are available to the user and are set to 1 by default to remove KF artifacts.


*We are not in any way affiliated with the Alliance for Open Media or any upstream SVT-AV1 project contributors who have not also contributed here.*

### Other Changes

- `--color-help` (**[Merged to Mainline](https://gitlab.com/AOMediaCodec/SVT-AV1/-/merge_requests/2351)**)

Prints the information found in Appendix A.2 of the user guide in order to help users more easily understand the Color Description Options in SvtAv1EncApp.

- `Micro-Releases`

We are always continuously improving SVT-AV1-PSY, and we always recommend using the `master` branch to experience exciting new features as soon as they can be considered usable. To make our feature additions more clear, micro-release tags indicate when significant new feature additions have been made. Micro-release tags are letters starting with `A`, so new releases will be tagged as `v#.#.#-A`, `v#.#.#-B`, etc.

- `Enhanced Content Detection`

Tune 4 features a smarter content detection algorithm to optimize the encoder for either screen or photographic content based on the image. This helps Tune 4 achieve better visual fidelity on still images.

# Building

For Linux, macOS, & Windows build instructions, see the [PSY Development](Docs/PSY-Development.md) page.

# Getting Involved

For more information on SVT-AV1-PSY and this project's mission, see the [PSY Development](Docs/PSY-Development.md) page.

### Use SVT-AV1-PSY

One way to get involved is to use SVT-AV1-PSY in your own AV1 encoding projects, increasing the impact our work has on others! You and your users will also be able to provide feedback on the encoder's overall performance and report any issues you encounter. Your name will also be added to this page.

**Projects Featuring SVT-AV1-PSY:**

- [Aviator](https://github.com/gianni-rosato/aviator) ~ an AV1 encoding GUI by @gianni-rosato
- [rAV1ator CLI](https://github.com/ultimaxx/rav1ator-cli) ~ a TUI for video encoding with Av1an by @ultimaxx
- [SVT-AV1-PSY on the AUR](https://aur.archlinux.org/packages/svt-av1-psy-git) ~ by @BlueSwordM
- [SVT-AV1-PSY in CachyOS](https://github.com/CachyOS/CachyOS-PKGBUILDS/pull/144) ~ by @BlueSwordM
- [Handbrake Builds](https://github.com/Nj0be/HandBrake-SVT-AV1-PSY) ~ by @Nj0be
- [Staxrip](https://github.com/staxrip/staxrip) ~ a video & audio encoding GUI for Windows by @Dendraspis
- [Av1ador](https://github.com/porcino/Av1ador) ~ an AV1/HEVC/VP9/H264 parallel encoder GUI for FFmpeg by @porcino

### Support Development

If you'd like to directly support the team working on this project, we accept monetary donations via the "Sponsor" button at the top of this repository (it has a pink heart within the button frame). Your donations will help the core development team continue to improve the encoder, our support efforts, and our documentation - a little goes a long way, and we appreciate it immensely.

## License

Up to v0.8.7, SVT-AV1 is licensed under the BSD-2-clause license and the
Alliance for Open Media Patent License 1.0. See [LICENSE](LICENSE-BSD2.md) and
[PATENTS](PATENTS.md) for details. Starting from v0.9, SVT-AV1 is licensed
under the BSD-3-clause clear license and the Alliance for Open Media Patent
License 1.0. See [LICENSE](LICENSE.md) and [PATENTS](PATENTS.md) for details.

*SVT-AV1-PSY does not feature license modifications from mainline SVT-AV1.*

## Documentation

For additional docs, see the [PSY Development](Docs/PSY-Development.md) page.
