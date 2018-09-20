---
layout: default
---

## Question 1.11
```plaintext
if n < 3, then f(n) = n
if n >= 3, then f(n) = f(n - 1) + 2 * f(n - 2) + 3 * f(n - 3)
```

### Recursive
```scheme
(define (frec n)
        (if (< n 3)
            n
            (+ (frec (- n 1)) (* 2 (frec (- n 2))) (* 3 (frec (- n 3))))))
```

### Tail Recursive
```scheme
(define (fiter n)
        (define (f a b c i)
           (cond ((< n 3) n)
               ((> i n) c)
               (else (f b c (+ (* 3 a) (* 2 b) c) (+ i 1)))))
 (f 0 1 2 3))
```

## Question 1.19

Fibonacci sequence

### Recursive
```scheme
(define (fib n)
   (cond ((= n 0) 0)
         ((= n 1) 1)
         (else (+ (fib (- n 1))
                  (fib (- n 2))))))
```

### Tail Recursive
```scheme
(define (fib n)
   (define (fib-iter a b count)
      (if (= count 0)
          b
          (fib-iter (+ a b) a (- count 1))))
   (fib-iter 1 0 n))
```

### LogN
```scheme
(define (even? n)
   (= (remainder n 2) 0)))

(define (fib n)
   (define (fib-iter a b p q count)
      (cond ((= count 0) b)
            ((even? count)
             (fib-iter a
                       b
                       (+ (* p p) (* q q))
                       (+ (* q q) (* 2 p q))
                       (/ count 2)))
             (else (fib-iter (+ (* b q) (* q q) (* a p))
                     (+ (* b p) (* a q))
                     p
                     q
                     (- count 1)))))
   (fib-iter 1 0 0 1 n))
```

由数学推导，可得
```plaintext
p1 = p0 ^ 2 + q0 ^ 2
q1 = 2 * p0 * q0 + q0 ^ 2
```


