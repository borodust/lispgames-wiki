## Q: Why do I keep getting "too many recursive errors" when compiling my project with SBCL?

##### A: What is happening is that there are so many macros calling other macros that SBCL has reached a limit where it believes that there is an infinite recursion of macro expansion going on and it stops. Luckily, it is really easy to fix this.

In your asd file, place this before the defsystem:
```
;; The defauls on SBCL are sometimes not good enough to expand the
;; optimization macro passes in certain files. So fix it here and apply
;; to each file as needed.
(defun using-better-limits (thunk)
  (let (#+sbcl (sb-ext::*inline-expansion-limit* 1024) )
    #+sbcl (format t "; Setting higher SBCL limits to compile this file...~%")
    (funcall thunk)))
```

Then, in the defsystem form, for the file which is emiting the error, do this:
```
(:file "foo" :around-compile using-better-limits)
```
What that does is apply the using-better-limits function around the compilation or loading of the file "foo.lisp". You'd change things to be as needed for yourself.

