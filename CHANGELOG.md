## Changelog

#### 2.3.next in progress

* Address [#31](https://github.com/clj-commons/failjure/issues/31) via PR [#42](https://github.com/clj-commons/failjure/pull/42) from [@gzmask](https://github.com/gzmask).

#### 2.3.62 2026-08-11

**This release is functionally identical to 2.3.0 but is the first release since the project migrated to clj-commons.**

* Switched to MAJOR.MINOR.COMMITS versioning scheme.
* Added GitHub Actions workflow for CI and deployment.
* Added `bb.edn` to drive tests & builds. This includes multi-version testing against Clojure 1.10 through 1.13 (alpha).
* Switched from Leiningen to `deps.edn` (so Failjure can be used via git deps).
* Split CHANGELOG out from README.
* Added ORIGINATOR and CODEOWNERS per clj-commons conventions.

#### 2.3.0

Added clj-kondo support and indent annotations.

#### 2.2.0

(Re-)added AOT compilation to the new leiningen project. This may help resolve errors with some project configurations.

#### 2.1.1

Fix a deployment whoopsie causing `attempt` to have reversed argument order from what is documented
here. It was fine in my REPL, I swear!

#### 2.1.0

**USE 2.1.1 INSTEAD**

Added `attempt` and `as-ok->`. Changed from boot to leiningen for builds.

#### 2.0.0

Added ClojureScript support. Since the jar now includes .cljc instead of .clj files, which could
break older builds, I've decided this should be a major version. It should in general be totally
backwards-compatible though.

Notable changes:

* ClojureScript support (thanks @snorremd)
* `*try` now wraps its inputs in a function and returns `(try-fn *wrapped-fn*)`. This was necessary
  to keep the clj and cljs APIs consistent, but could break some existing use cases (probably).

#### 1.5.0

Added `try-all` feature

#### 1.4.0

Resolved issues caused by attempting to destructure failed results.

#### 1.3.0

Fix bug where `ok->/>` would sometimes double-eval initial argument.

#### 1.2.0

Refactored `attempt-all`, `attempt->`, and `attempt->>` to remove dependency on monads

#### 1.1.0

Added assert helpers

#### 1.0.1

This version is fully backwards-compatible with 0.1.4, but failjure
 has been in use long enough to be considered stable. Also I added
a .1 because nobody trusts v1.0.0.

* Added `ok?`, `ok->`, `ok->>`, `if-let-ok?`, `when-let-ok?`, `if-let-failed?` and `when-let-failed?`

#### 0.1.4

* Added changelog.
