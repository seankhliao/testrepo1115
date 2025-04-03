# testrepo1115

This module simulates an end user of simulated opentelemetry dependencies housed in
[seankhliao/testrepo1114](https://github.com/seankhliao/testrepo1114).

It depends directly on `a`, `c`, and indirectly on `b`.
It has no dependency on `d`, `e`, or `f`.

## Upgrading with a tool meta package

Go 1.24 introduces [`tool` directives](https://go.dev/ref/mod#go-mod-file-tool) in go.mod:
develop time dependencies which are added to your module graph as indirect dependencies.

This can be combined with [module graph pruning](https://go.dev/ref/mod#graph-pruning) 
introduced in Go 1.17 for creating virtual group of dependencies.

Key observations:
- [Go MVS](https://go.dev/ref/mod#minimal-version-selection) means the only version requirement we can express is `>=` for dependencies
- Tool directives allow us to add and keep a module in the dependency graph without it being reachable through build or test
- Module pruning excludes any modules that cannot cannot be reached from building or testing the main module or any packages it imports
- Test-only dependencies can more easily be pruned out

Together, this means a `meta` module can import every dependency as a test only dependency,
and be used as a `tool` dependency in the end module.
The requirements from the `meta` module can bump up versions of all its dependencies in sync,
while not adding modules not used by the end module to its depdnency graph.

An example upgrade in this repo would be running:

```sh
go get -u go.seankhliao.com/testrepo1114/meta
```

This should update all the required dependencies to `v0.0.2`,
without adding the unused modules to `go.mod`, even though the `meta` package lists them as a dependency.
