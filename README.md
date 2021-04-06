# Promtail with Journal Support

Basically just follows the [Loki releases][loki-releases] and builds images with
the Promtail binary. The key difference is that the binaries include journal support,
something missing from the release binaries. The images themselves are just basic
debian buster on the following architectures:

- arm64v8
- amd64
- arm32v5
- arm32v7

They don't have any native functionality, rather just intended to be used as a
build image since making the binary takes a while. The [Promtail addon][addon-promtail]
uses the image in its pipeline.

[loki-releases]: https://github.com/grafana/loki/releases
[addon-promtail]: https://github.com/mdegat01/addon-promtail
