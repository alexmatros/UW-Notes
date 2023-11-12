- I/O: Reread first few lessons, things about opening/closing files, << and >> overload, etc.

- Know differences between lvalue and rvalue references
	- Especially important for move/copy, assignment operators
	- Initializing lvalue references, make sure it has an address
	- Cannot create pointer to reference, array of references

- Passing an argument to a function:
	- Pass-by-value -> passes copy of item 
	- Pass-by-reference ->passes reference to item, can change it (no copy)
	- Pass-by-const-reference -> passes reference to item, cannot change it (no copy)

- Big 5
	- Once you define a new constructor, default one provided by compiler goes away
	- Be clear about the 4 steps
		- Want to initialize fields in step 3 instead of step 4 -> use MIL
		- MIL -> must be used for constants, references, and any objects without a default ctor
			- If you don't use MIL for an object, it will be default ctored (if it doesn't have a default ctor, error)

- Initializer_list
	- Be aware of syntax
	- Meant to be immutable, don't attempt to mutate, don't treat it as a singleton data structure
		- Use it as a way to initialize for your constructor only
	- Be aware of the precedence for constructors
		- Default > initializer_list > other
	- If you want other ctor to run (and conflicts with initializer_list), use `()`

- Dtor
	- Whenever an object goes out of scope, dtor runs
	- Know the 4 steps of destruction

- Copy/Move Ctor/Assignment operator
	- Make sure assignment operators work for self-assignment
	- 2 versions of each:
		- `const type&`
		- `type &&`
	- OR unified assignment operator:
		- `T &operator=(T &)`
		- Copied if lvalue ref, moved if rvalue ref (as long as copy and move ctors are defined)

- `std::move`:
	- Ensures it is treated as rvalue ref
```c++
f(T &&x) {
	T t = T{std::move(x)};
}
```

- Elision
	- When you expect that copy/move happens, but actually just used ctor to write on the stack

- Different types of values in C++
	- prvalue: temporary object (no address) `obj{1}`
	- xvalues: temporary object with an address, but you can't take it `obj{1}.x` or `std::move` return value

- Ex (assume no elision optimization):
```c++
Obj &f(Obj &b) { return b; }

int main() {
	Obj obj{1}; // ctor
	Obj obj2 = f(obj); // pass-by-ref, copy assignment
}
```
    - 1 copy
```c++
void g(Obj b) { ... }

int main() {
	Obj obj1{1}; // ctor
	g(obj1); // copy into arg
	g(obj{2}); // move into arg
}
```
    - 1 copy, 1 move

- Const methods
	- Specify const after method declaration
	- Allows to be called by const objects (can also be called by non-const)
	- Const overloading: allows different behaviour for const and non-const objects

- Iterator
	- Works as pointer inside operator
	- Needs `begin()`, `end()`, `*`, `!=`, `++`
	- Const overloading allows for iterators on const objects
	- Want to make iterator ctor private, make iterator a friend class for wrapping class

- Exceptions
	- If exception throws in ctor, object is partially ctored, when object goes out of scope, dtor will not run
		- Needs to be cleaned up
	- Exception safety:
		- Basic guarantee
		- Strong guarantee
		- No-throw guarantee

```c++
struct A {};
struct B {};

void f() { throw A{}; }
void g() { throw B{}; }
void h() {
	cout << "A" << endl;
	f();
	cout << "B" << endl;
}

int main() {
	try {
		try{ h(); }
		catch (B &b) { cout << "C" << endl; }
		g();
		cout << "D" << endl;
	}
	catch (A &a) { cout << "E" << endl; }
}
```
- Output:
```
A
E
```
- Exception safety for list
```c++
++len;
theList = new Node{n, theList};

// or

theList = new Node{n, theList};
++len;
```
- The second is better, maintains exception safety if new Node throws

```c++
void a(); // not exception safe
void b(); // basic guarantee
void c(); // strong guarantee
void d(); // no-throw guarantee


void k() {
	try { c(); }
	catch (...) { c(); }
} // c() will throw twice, since c() is strong -> entire code block is strong guarantee

void l() {
	try { d(); }
	catch (...) { a(); }
} // d is no throw, so catch will never run -> entire block is no throw

void m() {
	try { b(); }
	catch (...) { c(); }
} // possible that b throws, and c throws as well -> entire block is basic guarantee

void n() {
	try { b(); }
	catch (...) { c(); }
}
```

- Basic vs. strong guarantee
	- In strong, all the effects of the function are undone, not necessarily true for basic

- Templates (template classes, template functions)
	- Whenever you use a template with a new type, code is generated and compiled replacing the generic type with the specified one
	- Variadic templates: use the 3 dots, allows for variable number of types/arguments

- `std::forward()` 
	- Moves if it's an rvalue, does nothing if it's an lvalue
	- Must provide type information -> perfect forwarding

- Initializing without default ctor
	1. Memory allocation: `void *operator new(size_t n)`
		- Need to use `static_cast<T*>(^)`
	2. Memory initialization: `new (addr) type`
		- Initialized object of type into addr, no extra memory allocation

- RAII: resource acquisition is initialization
	- When you are using resources (file, stream, etc.), do it in a way that when it goes out of scope it will be closed

- Unique_ptr
	- When it goes out of scope, it will get deleted automatically
	- Cannot copy, when it goes out of scope it will call delete on both (double-free)
	- You can move it however

- Spaceship operator: `<=>`
	- `std::strong_ordering operator<=>(T &other) const = default`
	- Gives `<`, `==`, `>`, `<=`, `!=`, `>=` for free

- Lambdas
	- `[Capture List](Params) mutable? noexcept? {body}`
```c++
int a = 3;
auto f = [&a](int &b) mutable { ++a; return a + b; }
cout << f(a) + a << endl; // result is 12, a and b both point to same a
```

- Range Iterators
	- Pair iterators with functions for better abstractions

- Inheritance
	- Classes that share a common interface, want to show that connection
	- Subclasses don't have access to superclass private fields by default
		- Give access using protected, or provide getters
		- Ideally: keep fields private, provide protected getters/setters
	- Virtual methods
		- Understand vtables and vptrs
		- Make the dtor virtual so that the dtor are run based on actual types
			- If pure virtual, provide {} implementation still

- Casting
	- Know all the types and differences