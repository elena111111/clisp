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
(defun dr2 (li)
	(if (null li)
		nil
		(( lambda (first second res-tail)
			(if (equal first second)
				res-tail 
				(cons first res-tail)
			)
		) (car li) (cadr li) (dr2 (cdr li)) ) 
	)
)

 > (dr2 '(1 1 2 1 2 1))
(1 2 1 2 1)
```

№13  Определите функцию, удаляющую в исходном списке все повторные вхождения элементов. 

```lisp
(defun mem(a li) 
	(if (null li) 
		nil 
		(if (equal a (car li)) 
			t 
			(mem a (cdr li))
		)
	)
)

 > (mem 1 '(1 2 3))
T
> (mem 4 '(1 2 3))
NIL

(defun dr(li) 
	(if (null li) 
		nil
		((lambda (first tail res-tail)
			(if (mem first tail) 
				res-tail
				(cons first res-tail)
			)
		)(car li) (cdr li) (dr (cdr li))) 
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
	((lambda (head tail)
        		(if (null x) 
            			nil 
            			(if (mem head y) 
                				head 
                				(first-coin tail y)
			)
		)
	) (car x) (cdr x))
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

№41  Реализовать генератор деревьев, чтобы выдаваемые им деревья имели количество вершин, точно соответствующее числу, 
указанному в его первом аргументе. 

```lisp
(defun tree (n &optional (k 0))
	(( lambda (index)
		(if (<= n k)
			nil
			(list k (tree n index) (tree n (+ index 1)) )
 
		)) (+ (* k 2) 1)
	)

)

>(tree 0)
nil
>(tree 1)
(0 NIL NIL)
>(tree 7)
(0 (1 (3 NIL NIL) (4 NIL NIL)) (2 (5 NIL NIL) (6 NIL NIL)))
>(tree 8))
(0 (1 (3 (7 NIL NIL) NIL) (4 NIL NIL)) (2 (5 NIL NIL) (6 NIL NIL)))
```

№47 Определите функцию УДАЛИТЬ-ВСЕ-СВОЙСТВА, которая удаляет все свойства символа.

```lisp
(defun del-all-prop (symb)
	( (lambda (s-list)
		(if (null s-list)
			nil

			(cons (remprop symb (car s-list)) (del-all-prop symb))
		)) (symbol-plist symb)
	)
)

>(setf (get 'ice 'color) 'white)
>(setf (get 'ice 'weight) 100)
>(setf (get 'ice 'price) 30)


>(symbol-plist 'ice)
(PRICE 30 WEIGHT 100 COLOR WHITE)
>(del-all-prop 'ice)
>(symbol-plist 'ice)
nil
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
	(mapcar 'apply f x)
)

> (apl-apply '(+ - equal) '((1 2) (3 4 5) (nil 0)))
(3 -6 NIL)
```

№5 Определитефункциональныйпредикат(НЕКОТОРЫй пред список),которыйистинен, когда, 
являющейся функциональным аргументом предикат пред истинен хотя бы для одного элемента списка список. 

```lisp
(defun some-1 (p li)
	(not (null (mapcan 
		#'(lambda (x) (if (funcall p x) (list t))) li)))
)

> (some-1 'numberp '(5 e u 9))
T
> (some-1 'numberp '(5g e7 u r))
NIL
> (some-1 'numberp '((5 6 7) e u p))
NIL
> (some-1 'numberp '(5))
T
```
№9 Напишите генератор порождения чисел Фибоначчи: 0, 1, 1, 2, 3, 5, ... 

```lisp
(defun fib ()
	(let ((prev-prev -1) (prev 0) (next 1))
		(lambda () 
			(if (= prev-prev -1)
				(setq prev-prev 0)
				(setq prev-prev prev prev next next (+ prev prev-prev))
			)
		)
	)
)

> (setq f (fib))
> (funcall f)
0
> (funcall f)
1
> (funcall f)
1
> (funcall f)
2
> (funcall f)
3
> (funcall f)
5

```

№11 Определите фукнционал МНОГОФУН, который использует функции, являющиеся аргументами, по следующей схеме: (МНОГОФУН ’(f g ... h) x) ⇔ (LIST (f x) (g x) ... (h x)). 

```lisp
(defun fun (f x)
	(mapcar #'(lambda (y) (apply y x)) f)
)

> (fun '(+ - eq) '(5 3))
(8 2 NIL)
``` 
# К. р.

№7 Написать программу для нахождения минимального из чисел, являющихся максимальными в каждой из строк заданной 
прямоугольной матрицы. Использовать применяющие и/или отображающие функционалы. 

```lisp
(defun min-max (x)
    	(apply 'min 
		(mapcar #'(lambda (y) (apply 'max y)) x) 
	)
)

> (min-max '((1 2 3 4) (5 6 7 8) (-1 -2 -3 -4)))
-1
> (min-max '((1)))
1
> (min-max '((0 9)))
9
> (min-max '((0) (9)))
0
```
№2 Написать генератор совершенных чисел.Число называется совершенным, если оно равно сумме своих делителей. 

```lisp
(defun dividers (a &optional (b (floor a 2)))
	(if (= b 0) nil
		((lambda (dec)
			(if (eq (mod a b) 0) 
				(cons b dec)
				dec
			)
		)  (dividers a (- b 1))) 
	)
)

(defun is-perfect (x) 
	(if (= (apply '+ (dividers x)) x) t nil)
)

(defun perfect ()
	(let ((x 0))
		(lambda () 
			(if (is-perfect (setq x (+ x 1))) x (funcall tmp))
		)
	)
)

(setq tmp (perfect))
(setq g1 (perfect))
> (funcall g1)
6
> (funcall g1)
28
> (funcall g1)
496
```