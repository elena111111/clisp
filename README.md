# Основы lisp

№5 Определите функцию, которая увеличивает элементы исходного списка на единицу.

```lisp
 (defun inc(li) 
	(if (null li) 
		nil 
		(cons (+ (car li) 1) (inc (cdr li)))
	)
)

> (inc nil)
NIL
> (inc '(1))
(2)
> (inc '(1 2 0 6))
(2 3 1 7)
```

№7  Определите функцию, удаляющую из исходного списка элементы с четными номерами. 

```lisp
 (defun del-even(li) 
	(if (null li) 
		nil 
		(cons (car li) (del-even(cddr li)))
	)
)

> (del-even '())
NIL
> (del-even '(1))
(1)
 > (del-even '(1 2 5 0 4))
(1 5 4)
```

№11  Определите функцию, осуществляющую разделение исходного списка на два подсписка.
 В первый из них должно попасть указанное количество элементов с начала списка, во второй — оставшиеся элементы. 


№12  Определите функцию, заменяющую в исходном списке два подряд идущих одинаковых элемента одним. 

```lisp
(defun dr2(li) 
	(if (null li) 
		nil (if (equal (car li) (cadr li))
			 (dr2 (cdr li)) (cons (car li) (dr2 (cdr li))))
	)
)

 > (dr2 '(1 1 2 1 2 1))
(1 2 1 2 1)
 > (dr2 '(1 1 2 1 2 1))
(1 2 1 2 1)
```

№13  Определите функцию, удаляющую в исходном списке все повторные вхождения элементов. 

```lisp
(defun mem(a li) 
	(if (null li) 
		nil (if (equal a (car li)) 
			t (mem a (cdr li)))
	)
)

 > (mem 1 '(1 2 3))
T
> (mem 4 '(1 2 3))
NIL

(defun dr(li) 
	(if (null li) nil 
		(if (mem (car li) (cdr li)) 
			(dr (cdr li)) (cons (car li) (dr (cdr li))))
	)
)

> (dr '(1 2 3))
(1 2 3)
> (dr '(1 2 3 1))
(2 3 1)
> (dr '(1 2 3 1 2))
(3 1 2)
```

№19 Определите функцию (ЛУКОВИЦА n), строящую N-уровневый вложенный список, элементом которого на самом глубоком уровне является N. 

```lisp
(defun luc (n &optional (li n)) 
	(if (equal n 0) 
		li 
		(luc (- n 1) (list li) )
	)
)

> (luc 1)
(1)
> (luc 2)
((2))
> (luc 0)
0
> (luc 6)
((((((6))))))
```

№31 Определите функцию (ПЕРВЫЙ-СОВПАДАЮЩИЙ х у), которая возвращает первый элемент, входящий в оба списка х и у, в противном случае NIL.

```lisp
(defun first-coin (x y) 
	(if (null x) 
		nil 
		(if (mem (car x) y) 
			(car x) 
			(first-coin (cdr x) y)
		)
	)
)

> (first-coin '(1) '(1))
1
[314]> (first-coin '(1) '(1 2))
1
> (first-coin '(1 2 3) '(1 2))
1
> (first-coin '(1 0 9 -7 6) '(2 4 5 7 -7))
-7
> (first-coin '(1 0 9 -7 6 5) '(2 4 5 7 -7))
-7
> (first-coin '(1 0 9 -7 6 5) '())
NIL
> (first-coin '(1 0 9 -7 6 5) '(-2 -5))
NIL
```
# Функции высших порядков

№1 Определите FUNCALL через функционал APPLY. 

```lisp
(defun funcall-1 (f &rest x) (apply f x))

> (funcall-1 '+ 2 3 4)
9
```

№3 Определите функционал (APL-APPLY f x), который применяет каждую функцию fi списка (f1 f2 ... fn) 
к соответствующему элементу списка x = (x1 x2 ... xn)  и возвращает список, сформированный из результатов. 

```lisp
(defun apl-apply (f x) 
	(if (null f) 
		nil 
		(cons 
			(apply (car f) (car x)) 
			(apl-apply (cdr f) (cdr x))
		) 
	)
)

> (apl-apply '(+ - equal) '((1 2) (3 4 5) (nil 0)))
(3 -6 NIL)
```