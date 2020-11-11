# Basis of scheme language to learn this book

```scheme
;;; evaluations
(+ 1 2) ; => 3
(- 4 2) ; => 2
(* 2 3) ; => 6
(/ 8 6) ; => 4/3
(/ 8 2) ; => 4
(expt 2 3) ; => 8
(quotient 5 2) ; => 2
(remainder 11 5) ; => 1

;;; booleans
#t ; literal true
#f ; literal false
(not #t) ; => #f
(and #f #t) ; => #f
(or #f #t) ; => #t

;;; define variable and procedures
(define v 4)
(define (a+b a b) (+ a b))
(a+b 2 3) ; => 5
(define a+b (lambda (a b) (+ a b))) ; define a procedure using lambda
(a+b 2 3) ; => 5

;;; immutable pairs
(cons 1 2) ; => '(1 . 2)
(car (cons 1 2)) ; => 1
(cdr (cons 1 2)) ; => 2

;;; lists
;; use a null(or nil or '() depends on implementation of scheme) to end up a list
(cons 1 (cons 2 (cons 3 null))) ; => '(1 2 3)
;; using a list constructor
(list 1 2 3) ; => '(1 2 3)
;; using a quote for literal list value
'(1 2 3) ; => '(1 2 3)
;; car and cdr while processing lists
(car '(1 2 3)) ; => 1
(cdr '(1 2 3)) ; => '(2 3)
(cadr '(1 2 3)) ; => 2, it's a syntax suger of (car (cdr '(1 2 3)))

;;; list operations
(list-ref '(1 2 3) 0) => 1
(length '(1 2 3)) ; => 3
(cons 4 '(1 2 3)) ; => '(4 1 2 3)
(append '(1 2) '(3 4)) ; => '(1 2 3 4)
;; mapping lists
(map add1 '(1 2 3)) ; => '(2 3 4)
(map + '(1 2 3) '(10 20 30)) ; => '(11 22 33)
(filter even? '(1 2 3 4)) ; => '(2 4)

```
