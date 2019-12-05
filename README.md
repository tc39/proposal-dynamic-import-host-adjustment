# Dynamic Import Host Adjustment

|               |               |
| ------------- | ------------- |
| **Stage**     | [2](https://tc39.es/process-document/) |
| **Spec**      | [source](https://github.com/tc39/dynamic-import-host-adjustment/blob/master/spec.emu), [output](https://tc39.es/dynamic-import-host-adjustment/) |
| **Tests**     | [TODO](#testing) |
| **Champion**  | @mikesamuel |
| **Reviewers** | @bakkot, @erights, @bmeck |

[Trusted Types][] guards sensitive APIs; it double checks that values
have been trusted by policy code before performing operations that
cannot be undone.

The dynamic import operator, `import(...)`, loads code and initializes modules.
Loading code from an untrustworthy source is an operation that cannot be undone.

This adjusts the host callout which enables dynamic loading.  With it:

1.  The host receives the original specifier (before it is stringified) so can use runtime type
    information to decide whether to allow code loading to proceed.
1.  The host callout can control stringification and convey the result to FinishDynamicImport
    to avoid repeated stringification, and to integrate with [default policies][default policy].

## Testing

Tests, to be written, will be implemented as
[web-platform-tests](https://github.com/web-platform-tests/wpt) and
will focus on the following properties:

1.  Polymorphic objects stringified once.  Something like
    ```js
    importScripts("/resources/testharness.js");

    test(
      () => {
        let stringifyCount = 0;
        import({
          toString() {
            let specifier = `data:text/javascript,export default ${ stringifyCount }`;
            ++stringifyCount;
            return specifier;
          }
        })
        .then(
          (defaultExport) => {
            assert_equals(stringifyCount, 1);
            assert_equals(defaultExport, 0);
          },
          (err) => {
            assert_equals(err, null);
          })
        .finally(done);
      },
      'DynamicImportStringifiesSpecifierOnce');
    ```
1.  The above, with coverage for both *null* and non-*null* referencing modules.
1.  Tests specific to trusted-types host implementation.

See also [webappsec-csp #243](https://github.com/w3c/webappsec-csp/issues/243)
on `import('data:...')` as CSP bypass.

[Trusted Types]: https://wicg.github.io/trusted-types/dist/spec/
[default policy]: https://wicg.github.io/trusted-types/dist/spec/#default-policy-hdr
