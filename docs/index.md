# Racket Functional Programming

## Data Types

```racket
42, -5          ; integer
3.14            ; float
1/3             ; rational
#t, #f          ; boolean
"text"          ; string
'symbol         ; symbol
'(1 2 3)        ; list
#(1 2 3)        ; vector
```

## Variables

- **Types**: Implicit, **Typing**: Dynamic

```racket
(define x 10)                    ; global binding
(let ([a 5] [b 10]) (+ a b))     ; local binding
```

## Control Structures

```racket
; Branching
(if (> x 0) "pos" "neg")
(cond [(> x 0) "pos"] [(< x 0) "neg"] [else "zero"])

; Iteration
(define (countdown n)            ; recursion
  (if (= n 0) 'done (countdown (- n 1))))
(for ([i (range 1 6)]) (display i))     ; loop
```

## Functions

```racket
(define (name param1 param2) body)       ; definition
(lambda (x) (* x x))                     ; anonymous
```

## Exercises

**1. Hello World**

```racket
(display "Hello, World!")
```

**2. Read/Echo Integer**

```racket
(display "Enter an integer: ")
(define input (read))
(printf "You entered ~a.~n" input)
```

**3. Prime Checker**

```racket
(define (is-prime? n)
  (cond [(<= n 1) #f] [(= n 2) #t] [(even? n) #f]
        [else (prime-helper n 3)]))

(define (prime-helper n div)
  (cond [(> (* div div) n) #t] [(= (remainder n div) 0) #f]
        [else (prime-helper n (+ div 2))]))

(display "Enter integer: ")
(define num (read))
(printf "~a is ~a prime.~n" num (if (is-prime? num) "" "not"))
```

**4. Math Expression: x² + (log₁₀(x) - sin(x))/√x**

```racket
(define (calc x)
  (+ (expt x 2) (/ (- (log x 10) (sin x)) (sqrt x))))

(display "Enter float x: ")
(define x (read))
(if (<= x 0)
    (display "Error: x must be positive")
    (printf "Result: ~a~n" (calc x)))
```
