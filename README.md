# Failjure [![Clojure CI Release](https://github.com/clj-commons/failjure/actions/workflows/test-and-release.yml/badge.svg)](https://github.com/clj-commons/failjure/actions/workflows/test-and-release.yml) [![Clojure CI Master](https://github.com/clj-commons/failjure/actions/workflows/test-and-snapshot.yml/badge.svg)](https://github.com/clj-commons/failjure/actions/workflows/test-and-snapshot.yml) [![Clojure CI Pull Request](https://github.com/clj-commons/failjure/actions/workflows/test.yml/badge.svg)](https://github.com/clj-commons/failjure/actions/workflows/test.yml)

Failjure is a utility library for working with failed computations in Clojure(Script).
It provides an alternative to exception-based error handling for applications
where functional purity is more important.

It was inspired by [Andrew Brehaut's error monad](https://brehaut.net/blog/2011/error_monads)
implementation.

## Installation

[![Clojars](https://img.shields.io/badge/clojars-failjure/failjure_2.3.62-blue.svg?logo=data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACAAAAAgCAMAAABEpIrGAAAABGdBTUEAALGPC/xhBQAAACBjSFJNAAB6JgAAgIQAAPoAAACA6AAAdTAAAOpgAAA6mAAAF3CculE8AAABjFBMVEUAAAAdCh0qDikdChwAAAAnDSY0EjM2FjUnDiYnDSYnDSYpDigyEDEEAQRGNUb///////8mDSYAAAAAAAAAAAAFAgUqEyoAAAAAAAAAAAAFAgUAAABXU1c2FjVMx+dQx+f///////9Nx+b////4/f6y4vRPt+RQtOT///9Qt+P///8oDSey4vRQr9/////3/P5hzelNx+dNx+dNx+f///8AAAAuDy0zETIAAAAoDScAAAAAAAARBREAAAAvDy40ETMwEC9gSF+Ne42ilKKuoK6Rg5B5ZXlaP1o4Gzf///9nTWZ4YncyEDF/bn/8/Pz9/P339/c1FTUlDCRRM1AbCRtlS2QyEDEuDy1gRWAxEDAzETIwEC/g4OAvDy40EjOaiZorDiq9sbzNyM3UzdQyEDE0ETMzETKflZ/UzdQ5Fzmu4fNYyuhNx+dPt+RLu9xQyOhBbo81GTuW2vCo4PJNx+c4MFE5N1lHiLFEhKQyEDGDboMzETI5Fjh5bXje2d57aHrIw8jc2NyWhJUrDioxe9o4AAAAPnRSTlMAkf+IAQj9+e7n6e31RtqAD/QAAAED+A0ZEQ8DwvkLBsmcR4aG8+cdAD6C8/MC94eP+qoTrgH+/wj1HA8eEvpXOCUAAAABYktHRA8YugDZAAAACXBIWXMAAAsTAAALEwEAmpwYAAAAB3RJTUUH3wcHFjou4Z/shwAAAUpJREFUOMul0/VTwzAUB/AAwyW4y3B3h8EDNuTh7u6UDHcd8I+TbHSjWdrjju/1h77kc+3Lu5aQvyakF/r6B5wu1+DQMEBomLRtG0EpozYDCEccA4iIjIqOiY0bB5iYxHgZ4FQCpYneKmmal0aQPMOXZnUAvJhLkbpInf8NFtKCTrGImK6DJcTlDGl/BXGV6oCsrSNIYAM3aQDwl2xJYBtBB5lZAuyYgWzY3YMcNcjN2wc4EGMEFTg8+hlyfgEenygAj71Q9FBExH0wKC4p1bRTJlJWXqEAVNM05ovbXfkPAHBmAUQPAGaAsXMBLiwA8z3h0gRcsWsObuAWLJu8Awb3ZoB5T8EvS/CgBo9Y5Z8TPwXBJwlUI9Ia/yRrEZ8lID71Olrf0MiamkkL4kurDEjba+C/e2sninR0wrsH8eMTvrqIWbodjh7jyjdtCY3Aniz4jwAAACV0RVh0ZGF0ZTpjcmVhdGUAMjAxNS0wNy0wN1QyMjo1ODo0NiswMjowMCgWtSoAAAAldEVYdGRhdGU6bW9kaWZ5ADIwMTUtMDctMDdUMjI6NTg6NDYrMDI6MDBZSw2WAAAAAElFTkSuQmCC)](https://clojars.org/failjure/failjure)
[![cljdoc](https://cljdoc.org/badge/failjure/failjure?2.3.62)](https://cljdoc.org/d/failjure/failjure/CURRENT)

Add the following to your build dependencies:

`deps.edn`:
```clojure
    failjure/failjure {:mvn/version "2.3.62"}
```

Leiningen:
```clojure
    [failjure/failjure "2.3.62"]
```

You can also include the specs via the [failjure-spec](https://github.com/adambard/failjure-spec) project,
if you're into that sort of thing:

[![Clojars Project](https://img.shields.io/clojars/v/failjure-spec.svg)](https://clojars.org/failjure-spec)

Failjure works on Clojure 1.10 and above, ClojureScript 1.11 and above,
and Babashka (tested against v1.13.* but should work on v1.12.*).

> Note: the tests pass for Clojure 1.8 and 1.9 but those are not officially supported here.

This project follows the version scheme MAJOR.MINOR.COMMITS where MAJOR and 
MINOR provide some relative indication of the size of the change, but do not 
follow semantic versioning. In general, all changes endeavor to be non-breaking 
(by moving to new names rather than by breaking existing names). COMMITS is an 
ever-increasing counter of commits since the beginning of this repository.

> Note: every commit to the **master** branch runs CI (GitHub Actions) and successful runs push a MAJOR.MINOR.99-SNAPSHOT build to Clojars so the very latest version of Failjure is always available either via that [snapshot on Clojars](https://clojars.org/failjure/failjure) or via a git dependency on the latest SHA.


## Example

```clojure

(require '[failjure.core :as f])

;; Write functions that return failures
(defn validate-email [email]
    (if (re-matches #".+@.+\..+" email)
      email
      (f/fail "Please enter a valid email address (got %s)" email)))

(defn validate-not-empty [s]
  (if (empty? s)
    (f/fail "Please enter a value")
    s))

;; Use attempt-all to handle failures
(defn validate-data [data]
  (f/attempt-all [email (validate-email (:email data))
                  username (validate-not-empty (:username data))
                  id (f/try* (Integer/parseInt (:id data)))]
    {:email email
     :username username}
    (f/when-failed [e]
      (log-error (f/message e))
      (handle-error e))))
```

## Quick Reference

### `HasFailed`

The cornerstone of this library, `HasFailed` is the protocol that describes a failed result.
Failjure implements HasFailed for Object (the catch-all not-failed implementation), Exception, and the
built-in Failure record type, but you can add your own very easily:

```clojure
(defrecord AnnotatedFailure [message data]
  f/HasFailed
  (failed? [self] true)
  (message [self] (:message self)))
```

### `fail`

`fail` is the basis of this library. It accepts an error message
with optional formatting arguments (formatted with Clojure's
format function) and creates a Failure object.

```clojure
(f/fail "Message here") ; => #Failure{:message "Message here"}
(f/fail "Hello, %s" "Failjure") ; => #Failure{:message "Hello, Failjure"}
```

### `failed?` and `message`

These two functions are part of the `HasFailed` protocol underpinning
failjure. `failed?` will tell you if a value is a failure (that is,
a `Failure`, a java `Exception` or a JavaScript `Error`.

### `attempt`

_Added in 2.1_

Accepts a value and a function. If the value is a failure, it is passed
to the function and the result is returned. Otherwise, value is returned.

```clojure
(defn handle-error [e] (str "Error: " (f/message e)))
(f/attempt handle-error "Ok")  ;=> "Ok"
(f/attempt handle-error (f/fail "failure"))  ;=> "Error: failure"
```

Try it with `partial`!

### `attempt-all`

`attempt-all` wraps an error monad for easy use with failure-returning
functions. You can add any number of bindings and it will short-circuit
on the first error, returning the failure.

```clojure
(f/attempt-all [x "Ok"] x)  ; => "Ok"
(f/attempt-all [x "Ok"
              y (fail "Fail")] x) ; => #Failure{:message "Fail"}
```

You can use `when-failed` to provide a function that will handle an error:

```clojure
(f/attempt-all [x "Ok"
                y (fail "Fail")]
  x
  (f/when-failed [e]
    (f/message e))) ; => "Fail"
```

### `ok->` and `ok->>`

If you're on-the-ball enough that you can represent your problem
as a series of compositions, you can use these threading macros
instead. Each form is applied to the output of the previous
as in `->` and `->>` (or, more accurately, `some->` and `some->>`),
 except that a failure value is short-circuited and returned immediately.

*Previous versions of failjure used `attempt->` and `attempt->>`, which
do not short-circuit if the starting value is a failure. `ok->` and `ok->>`
correct this shortcoming*

```clojure

(defn validate-non-blank [data field]
  (if (empty? (get data field))
    (f/fail "Value required for %s" field)
    data))

(let [result (f/ok->
              data
              (validate-non-blank :username)
              (validate-non-blank :password)
              (save-data))]
  (when (f/failed? result)
    (log (f/message result))
    (handle-failure result)))
```

### `as-ok->`

_Added in 2.1_

Like clojure's built-in `as->`, but short-circuits on failures.

```clojure

(f/as-ok-> "k" $
  (str $ "!")
  (str "O" $))) ; => Ok!

(f/as-ok-> "k" $
  (str $ "!")
  (f/try* (Integer/parseInt $))
  (str "O" $))) ; => Returns (does not throw) a NumberFormatException
```

### `try*`

This library does not handle exceptions by default. However,
you can wrap any form or forms in the `try*` macro, which is shorthand for:

```clojure
(try
  (do whatever)
  (catch Exception e e))
```

Since failjure treats returned exceptions as failures, this can be used
to adapt exception-throwing functions to failjure-style workflows.

### `try-all`

A version of `attempt-all` which automatically wraps each right side of its
bindings in a `try*` is available as `try-all` (thanks @lispyclouds):

```clojure
(try-all [x (/ 1 0)
          y (* 2 3)]
  y)  ; => java.lang.ArithmeticException (returned, not thrown)
```

### `(if-|when-)-let-(ok?|failed?)`

Failjure provides the helpers `if-let-ok?`, `if-let-failed?`, `when-let-ok?` and `when-let-failed?` to help
with branching. Each has the same basic structure:

```clojure
(f/if-let-failed? [x (something-which-may-fail)]
  (handle-failure x)
  (handle-success x))
```

* If no else is provided, the `if-` variants will return the value of x
* The `when-` variants will always return the value of x

### `assert-with`

The `assert-with` helper is a very basic way of adapting non-failjure-aware
functions/values to a failure context. The source is simply:

```clojure
(defn assert-with
  "If (pred v) is true, return v
   otherwise, return (f/fail msg)"
  [pred v msg]
  (if (pred v) v (fail msg)))
```

The usage looks like this:

```clojure
(f/attempt-all
  [x (f/assert-with some? (some-fn) "some-fn failed!")
   y (f/assert-with integer? (some-integer-returning-fn) "Not an integer.")]
  (handle-success x)
  (f/when-failed [e] (handle-failure e)))
```

The pre-packaged helpers `assert-some?`, `assert-nil?`, `assert-not-nil?`, `assert-not-empty?`, and `assert-number?`
are provided, but if you like, adding your own is as easy as `(def assert-my-pred? (partial f/assert-with my-pred?))`.

## License

Copyright (c) 2016-2026 [Adam Bard](https://adambard.com/) and [Andrew Brehaut](https://brehaut.net/)

Distributed under the Eclipse Public License v1.0 (same as Clojure).
