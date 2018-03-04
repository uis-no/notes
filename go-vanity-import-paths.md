# How to Avoid Changing the Import Path

## Background

In Go it is common to use import paths that point to a `github.com` repository,
which allows users to use `go get github.com/pkg/foo` to download the code.

These imports may look like this in the source code:

```go
package bar

import "github.com/pkg/foo"
```

Now this is a great feature of Go, but it becomes problematic when we wish to
use this for lab assignments in our courses, because every year we need to
create a new GitHub organization and apply for approval from GitHub. This is
necessary (or at least preferable) for several reasons, one being that it helps
to keep the student repositories around for archival reasons, and helps to
avoid resetting organizations.

However, to keep the different organizations separate from one another, it is
recommended that a specific naming scheme is adopted. For example, the DAT320
Operating Systems course in fall 2017 would be named like this:

```
"github.com/uis-dat320-fall17"
```

This works fine within one year, but it requires that all import paths from
previous years needs to be updated. This is usually handled manually, and is
not difficult task, but it is also easy to forget some files. It could also be
done with a simple `sed` or `awk` command, but this is also arguably a manual
step that must be performed, committed and pushed.


## A Solution

A solution to these problems is to use _vanity import paths_ together with
_canonical import paths_. That is, at least one file, e.g. `doc.go`, in the
repo should contain a package definition with a comment specifying the
canonical import path to be used when importing the package, in this case the
`bar` package.

```go
package bar // import "ux.uis.no/opsys/bar"
```

That is, even though the `bar` package resides at
`https://github.com/uis-dat320-fall17/bar`, it must be imported as follows:

```go
import "ux.uis.no/opsys/bar"
```

To make this possible, the following html content must be accessible at
`https://ux.uis.no/opsys/bar?go-get=1`

```html
<!DOCTYPE html>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
<meta name="go-import" content="ux.uis.no/opsys/bar git https://github.com/uis-dat320-fall17/bar">
</head>
<body>
Nothing to see here; <a href="https://github.com/uis-dat320-fall17/bar">move along</a>.
</body>
```

I believe this to be a pretty good solution for our situation, where we need to
point our students to a new organization every year. We now only need to
remember to update the html file in one place, to point to the appropriate code
base on GitHub.


## Some Issues to Check

This solution should work fine for all code that we supply to the students. I
also believe that, even if students change/add code within our supplied
packages, they will still be able to compile and run their own code using these
`ux.uis.no` import paths, even if they commit and push their code to their own
repo. This is because they should have their code base in their own remote, and
will not be using `go get` to fetch their changes.

One issue to check is if the students create their own packages within their
own repo; will they be allowed to use `ux.uis.no` prefix in their import paths?

If not, they could just point their imports to their own GitHub repo.


## References

* [cmd/go import path checking](https://golang.org/pkg/cmd/go/#hdr-Import_path_checking)
* [cmd/go remote import paths](https://golang.org/cmd/go/#hdr-Remote_import_paths)
* [Go 1.4 Release Notes; Canonical imports](https://golang.org/doc/go1.4#canonicalimports)
* [Canonical and vanity import paths](https://texlution.com/post/golang-canonical-import-paths/)
* [Issues with forking and canonical import paths](https://kostasbariotis.com/go-canonical-import-paths-github-forks/)
