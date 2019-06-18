# Dynamic Import Host Adjustment

|               |               |
| ------------- | ------------- |
| **Stage**     | [0](https://tc39.es/process-document/) |
| **Spec**      | [source](https://github.com/mikesamuel/dynamic-import-host-adjustment/blob/master/spec.emu), [output](https://mikesamuel.github.io/dynamic-import-host-adjustment/) |
| **Tests**     | TODO |
| **Champion**  | @mikesamuel |
| **Reviewers** | TBD |

[Trusted Types][] guards sensitive APIs; it double checks that values
have been trusted by policy code before performing operations that
cannot be undone.

The dynamic import operator, `import(...)`, loads code and initializes modules.
Loading code from an untrustworthy source is an operation that cannot be undone.

This adjusts the host callout which enables dynamic loading to enable:

1.  The host callout to receive a specifier before it is stringified which will
    allow checking whether the value is an instance of an appropriate trusted type.
1.  The host callout to control stringification and convey the result to FinishDynamicImport
    to avoid repeated stringification, and to integrate with [default policies][default policy].

TODO: Find bug on `import('data:...')` as CSP bypass.

[Trusted Types]: https://wicg.github.io/trusted-types/dist/spec/
[default policy]: https://wicg.github.io/trusted-types/dist/spec/#default-policy-hdr
