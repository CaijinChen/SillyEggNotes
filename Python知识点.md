# Python知识点

- map(f, [x1,x2,x3]) = [f(x1), f(x2), f(x3)]

		def f(x):
			return x\*\*2
		a = list(map(f, [1, 2, 3]))
		//[1, 4, 9]
- reduce(f, [x1, x2, x3， x4]) = f(f(f(x1,x2),x3),x4)

		def add(x, y):
			return x+y
		a = reduce(add, [1, 2, 3, 4, 5])
		//15
- filter(f, [x1,x2,x3]) = [x1], f(x1)=True, f(x2)=False, f(x3)=False

		def is_odd(x):
		    return x%2 == 1
		a = list(filter(is_odd, [1,2,3,4,5,6,7]))
		//[1, 3, 5, 7]
- sorted(iterable, /, *, key=None, reverse=False)
- 