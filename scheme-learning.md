---
layout: default
---

## Question
```plaintext
if n < 3, then f(n) = n
if n >= 3, then f(n) = f(n - 1) + 2 * f(n - 2) + 3 * f(n - 3)
```

## Recursive
```scheme
(define (frec n)
        (if (< n 3)
            n
            (+ (frec (- n 1)) (* 2 (frec (- n 2))) (* 3 (frec (- n 3))))))
```

## Tail Recursive
```scheme
(define (fiter n)
        (define (f a b c i)
           (cond ((< n 3) n)
               ((> i n) c)
               (else (f b c (+ (* 3 a) (* 2 b) c) (+ i 1)))))
 (f 0 1 2 3))
```
