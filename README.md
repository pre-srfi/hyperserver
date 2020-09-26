# SRFI nnn: Hyper Server

by Firstname Lastname, Another Person, Third Person

# Status

Early Draft

# Abstract

Simple web server that can be used as a backend application for a
single-page-application.

# Issues

??? Optional section that may point out things to be resolved. This
will not appear in the final SRFI.

# Rationale

??? detailed rationale. This should be 200-500 words long. Please
explain why the proposal should be incorporated as a standard feature
in Scheme implementations. List related standards and SRFIs, including
dependencies, conflicts, and replacements. If there are other
standards which this proposal will replace or with which it will
compete, please explain why the present proposal is a substantial
improvement.

## Survey of prior art

GitHub's version of Markdown can make tables. For example:

| System        | Procedure | Signature                 |
| ------------- |:---------:| ------------------------- |
| System A      | `jumble`  | _list_ _elem_             |
| System B      | `bungle`  | _elem_ _list_             |
| System C      | `frob`    | _list_ _elem_ _predicate_ |

# Specification

## `(hyperserver-start port-number proc)`

Start a HTTP server that listen on port `PORT-NUMBER` and that
executes `PROC` on each incoming requests. `PROC` takes three
arguments:

- `method` is a symbol for the downcased HTTP method,

- `path` is a list of the strings that makes the path of the requested
  URI. That is the strings that are between slashes that is percent
  decoded.

- `params` the parsed query string as an association list of symbols
  and strings.

- `json` is an object satisfying `eof-object?` when the request has no
  body, or the scheme object such as returned by SRFI-180 procedure
  `json-read`.

`PROC` is expected to return two values or three values:

1. The first value is always a number representing an HTTP code,

2. The second value is possibly a bytevector or a scheme object that
   can be written using SRFI-180 procedure `json-write`,

3. If the second value is a binary port, the third value must be a
   string describing the mime type associated with the
   bytevector. Otherwise, the third value if any is ignored.

In the case where there is a request body and `json-read` raise a an
error that satisfy `json-error?`, it must return a HTTP 400 (bad
request) error.

## `(hyperserver-serve-static filepath)`

If `FILEPATH` points to a file that exists it returns three values:

1. The HTTP code 200,

2. A binary port with the content of the file,

3. A string denoting the mimetype of the file (possibly based on the
   filename extension).

# Example

Simple echo server, that returns what it is posted on `/api/echo` or
serve a static file found in `%static-root`:

```scheme
(define %static-root "/var/hyperserver/static/")

(define (router method path params json)
  (match (cons method path)
    ((POST "api" "echo") (values 200 json))
    (_ (hyperserver-serve-static (apply string-join #\/  %static-root path)))))

(hyperserver-start 8000 router)
```

# Implementation

??? explanation of how it meets the sample implementation requirement
(see process), and the code, if possible, or a link to it Source for
the sample implementation.

# Acknowledgements

??? Give credits where credits is due.

# References

??? Optional section with links to web pages, books and papers that
helped design the SRFI.

# Copyright

Copyright (C) Firstname Lastname (20XY).

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
"Software"), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE
LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION
OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION
WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
