## Problem 1: Program Input and Output

##### Basic I/O
Providing input to the program:
- Invoke the program with a sequence of args: `./programname arg1 arg2 ... argn
- Invoke the program, input from stdin: `./programname`

Streams:
- Input stream: `std::cin`
- Output stream: `std::cout`

I/O Operators:
- Input operator: `<<`
- Output operator: `>>`

##### File I/O
File streams:
- Input file stream: `std::ifstream`
- Output file stream: `std::ofstream`

	**NOTE: stream input SKIPS whitespace by default -> to turn off, apply flag: `f >> std::noskipws`**

Opening and Closing Files:
- No explicit calls are made to file open/close
- Initializing `f{"name-of-file"}` opens the file
- When `f`'s scope ends, the file is closed

	**NOTE: you cannot write a function that takes input as an: `istream`**

##### References
L-Value References:
- Must be initialized, particularly to something that has an address
- Could appear on the left side of assignments

R-Value References:
- Can not appear on the right side of assignments

Things you **CANNOT** do:
- Create a pointer to a reference: `int &*x;` -> wrong!
- Create a reference to a reference: `int &&r = p;` -> will compile, but means something different!
-  Create an array of references: `int &r[3] = {_, _, _};` -> similar to pointer to ref!
- Call a function taking a ref with a literal
	- `void f(int &n); f(5);` -> can't initialize l-value ref to a literal value

Things you **CAN** do:
- Pass ref as function parameters: `void inc(int &x);` -> const pointer to the arguement
- Assume a really big struct exists: `struct ReallyBig{...}`
	- `void f(ReallyBig rb) {...}` -> pass-by-value, copies the struct (slow)
	- `void g(ReallyBig &rb) {...}` -> pass-by-ref, no copy, but function could propagate changes to the caller
	- `void h(const ReallyBig &rb) {...}` -> pass-by-ref, no copy, function can't change the passed rb
* Call a function taking a const ref with a literal 
	* `void g(const int &n); g(5);`-> since n can never change, compiler allows it and the literal is on the stack

	**NOTE: prefer pass-by-const-ref over pass-by-value for anything larger than a ptr, unless function needs to copy anyways**

##### Problem 2: Separate Compilation
Creating a module:
```c++
echo.cc: // module interface file

export module echo; // defines a module
import <iostream>;
export void echo(istream &f);

echo-impl.cc: // module implementation file

module echo; // declares that this file will be implementation of echo, all exported functions are known
void echo(istream &f) { ... }

main.cc: // client file

import<iostream>;
import<fstream>;
import echo;

int main(int argc, char *argv[]) {
	... echo(cin) ...
	... echo(cout) ...
}
```

Compiling Order:
- Must be compiled in dependency order: module interface -> module implementation -> client
	- `g++20h iostream fstream`
	- `g++20m -c echo.cc`
	- `g++20m -c echo-impl.cc`
	- `g++20m -c main.cc`
	- `g++20m *.o -o mycat`

##### Problem 3: Linear Collections and Modularity
Allocating Memory in C++:
- Do not use malloc/free
- Use new/delete: `Mpde *n = new Node; delete n;`
	- Give it a type and it will allocate enough memory for that

Namespaces:
- Allow multiple modules to have the same names and parameters
```c++
test.cc

export module test;

namespace Test{
	export int f(int x);
}

test-impl.cc

module test;
int Test::f(int x) { ... };
```
- Namespaces are open, anyone can add items to any namespace
	- Except adding to namespace std is not permitted

##### Problem 4: Linear Collections and Memory Management
Arrays:
- Creating arrays:
	- `int a[10];` -> creates array on the *stack*, fixed size
	- `int *p = new int[10];` -> creates array on the *heap*, fixed size
- Deleting arrays:
	- `delete [] p`
	- In general, use `new []` with `delete []`, mismatching these causes undefined behaviour

- What if array isn't big enough? No realloc for new/delete
	- Use abstraction to solve the problem with `vector`
	- Has an internal array and manages the memory on its own, increasing cap and copying array when needed

	**NOTE: `type &f(type arg)` returns a reference to item of type, allowing for mutation**

Argument-Dependent Lookup (ADL)
- Also called Koenig lookup
- If a function takes an argument in a namespace, it is allowed to look in that namespace for the particular function
- If the type of a function f's argument x belongs to a namespace n, then C++ will search the namespace n, as well as the current scope for a function matching f

##### Problem 5: You're Doing It Wrong!
Introduction to Classes:
- **Classes**: structs can contain functions
- **Methods**: functions that are inside structs
- **Objects**: instances of a class

- Methods differ from functions functions in that methods take an implicit param called `this`
	* `this` is a pointer to the receiver object
	* Fields of the receiver object can be accessed without needing to use `this->field`

###### Object Creation
Initializing Objects:
- **Constructor**: method with the same name as class
- Initialize assuming constructor is defined:
	- On stack: `Example e{1, 2, 3};` or `Example e = Example{1, 2, 3};` (equivalent)
	- On heap: `Example *e = new Example{1, 2, 3};`

- Once a constructor is defined, C-style field initialization (default) is no longer enabled
- Advantages of constructors:
	- Can specify default parameters
	- Allows for overloading (multiple different constructors)
	- Sanity checks (validity of arguments passed)

- Every class comes with a built-in default (zero-arg) constructor -> default constructs all fields that are objects
- Ex. `Example e;` calls default constructor
- This default constructor goes away if you write any constructor

Object Creation Steps:
1. Space is allocated
2. (will explore later)
3. Fields are constructed in declaration order -> field constructors are called for fields that are objects
4. Constructor body runs

	**NOTE: field initialization should happen in step 3, but ctor body is in step 4 -> consequence is object fields could be initialized twice; solution: MIL**

Member Initialization List (MIL)
- New syntax:
```c++
struct Example {
	String e1;
	int e2, e3;
	
	Example(string e1, int e2, int e3) : e1{e1}, e2{e2}, e3{e3} {}
}
```
- Object creation steps:
	- Step 3: `e1{e1}, e2{e2}, e3{e3}`
	- Step 4: `{}`
- MIL **must** be used for fields that are constants, references, and objects without a default ctor

Careful - Single-Arg Constructors:
- Single arg constructors create implicit conversions
```c++
struct Node {
	...
	Node(int data, Node *next = nullptr) : data{data}, next{next} {}
};

Node n{4}; // this is OK
Node n = 4; // this is OK... int implicitlly converted to Node
void f(Node n); f(4); // this is OK... may be trouble
```
- To stop this, define them explicitly:
```c++
struct Node {
	...
	explicit Node(...) : ... {}
}
```
- `explicit` keyword disables the implicit conversion

###### Object Destruction
Destroying Objects:
- **Destructor**: method that runs automatically when an object goes out of scope
- Built-in destructor: calls dtors on all fields that are objects
- Does NOT free any pointers

Object Destruction Steps:
1. Dtor body runs
2. Fields destructed (dtor called on fields that are objects) in reverse declaration order
3. (will explore later)
4. Space deallocated

Writing Custom Dtors:
```c++
struct Node {
	...
	~Node() { delete next; }
};
delete n; // frees the entire list
```

##### Problem 6: The Copier is Broken
Copying Objects:
```c++
Node n = Node{1, Node{2, Node{3, nullptr}}};
Node m = n; // allowed, constructs m as a copy of n
```
- `Node m = n;` constructs m as a copy of n, invokes the copy ctor
- By default, makes a shallow copy -> minor annoyance when in scope, creates double-free error when trying to delete
```c++
struct Node {
	int data;
	Node *next;
	...
	Node(const Node &other) : data{other.data}, next {other.next ? new Node{*other.next} : nullptr} {}
}
```
- Need to pass by reference to copy ctor so it doesn't invoke copy... wouldn't make sense to copy calling copy

Copy Assignment Operator:
```c++
Node n;
Node m;
m = n;
```
- This invokes a copy but not construction -> copy assignment operator
	- Compiler-supplied: copies each field (shallow), leaks m's old data
- Cannot simply copy other's data and then delete it -> it won't work in the case of self-assignment

Copy and Swap Idiom:
```c++
import <utility>
struct Node {
	...
	void swap(Node &other) {
		using std::swap;
		swap(data, other.data);
		swap(next, other.next);
	}
	Node &operator=(const Node &other) {
		Node tmp = other;
		swap(tmp);
		return *this;
	}
};
```
* `std::swap(a, b);`: exchanges the values of a and b

##### Problem 7: Thievery
R-Value References:
- `Node &&` is a reference to a temporary object (rvalue) of type Node

Move Constructor:
```c++
struct Node {
	...
	Node(Node &&other) : data{other.data}, next{other.next} {
		other.next = nullptr;
	}
};
```
* Copies other's data, and then delete's other's ptrs

	**NOTE: move runs in constant time vs linear time of copying**

Move Assignment Operator:
```c++
struct Node {
	...
	Node &operator=(Node &&other) {
		swap(data, other.data);
		swap(next, other.next);
		return *this;
	}
};
```
1. Steal other's data
2. Destroy my old data
- Easy to do: swap without copy
	- Swap exchanges values through a temp variable

Combined Copy and Move Assignment:
```c++
struct Node {
	...
	Node &operator=(Node other) {
		swap(other);
		return *this;
	}
};
```
- This impl uses a unified assignment operator
- Takes arg as pass-by-value
	- Invokes copy ctor if arg is an lvalue
	- Invokes move ctor if arg is an rvalue
	- => copies if and only if arg is an lvalue

Important to Know:
- If you don't define move operations, copy operations will be used
- If you do define them, they replace copy operations whenever the arg is temporary (rvalue)

Copy/Move Elision:
```c++
vector makeAVector() { return vector{}; }
vector v = makeAVector();
```
- Just the basic ctor runs, no copy/move
	- In many circumstances (including this one), compiler must skip calling the copy/move ctors
	- `makeAVector()` writes its result directly into the space occupied by v in the caller, rather than copy it later

Other Examples of Elision:
```c++
vector v = vector{};
```
- Looks like a basic ctor of a temporary and a copy/move construction
	- Compiler is required to **not** make a temporary here, so no copy/move ctor is done, basic ctor only, written right into v
```c++
void doSomething(vector v);
doSomething(makeAVector());
```
- Result of makeAVector written directly into the param, no copy
	- This happens even if dropping ctor calls would change the behaviour of the program (ie. the ctor prints something)

	**NOTE: does not mean copy/move ctors don't get run, just not as often as you'd expect**

###### C++ Value Categories
```mermaid
graph TD

id1(glvalue)
id2(lvalue)
id3(xvalue)

id4(rvalue)
id5(prvalue)

id1 --> id2 & id3
id4 --> id3 & id5
```
glvalues:
- Denote a storage location ("generalized location")

lvalues:
- Can be on the left-hand-side of an assignment
	- Vars (including const), field references, array lookups, ptr derefs, etc.

rvalues:
- Can't be on the left-hand-side of an assignment
	- Address cannot be taken

prvalues:
- "pure rvalues"
	- Do **not** denote storage locations
	- Ex. literals, arithmetic expressions, function calls with non-ref return types
	- Most rvalues you would come up with are prvalues

xvalues:
- "expiring values"
	- Considered both rvalues and glvalues
	- Can't take its address but does denote a location
	- Ex. `std::move(...)` or `f(...)` returning an rvalue ref
	- "lvalue construction on rvalues"

General rules:
- prvalues do not generate temporaries (so no copy/move happens)
	- Only xvalues generate temps
- Only xvalues get moved from, only xvalues and lvalues get copied from

Named Return Value Optimization:
- Some situations allow for elision, but it is not required:
```c++
vector f() {
	vector v = makeAVector();
	... // changes to v
	return v; // not a prvalue -> could be elided, but not required
}

vector g() {
	vector v = makeAVector();
	vector w = makeAVector();
	... // changes to v and w
	if (...) return v;
	else return w; // no way to know which one to put into stack frame and avoid temp, thus eliminating the temp may not always be possible
}
```

Summary - Rule of the Big 5:
- If you need to customize any one of the following 5 operations... then you usually need to customize all 5:
	1. Copy ctor
	2. Copy assignment
	3. Dtor
	4. Move ctor
	5. Move assignment

##### Problem 8: I Don't Like Change
Declaring Const Methods:
- Add const after method signature, means methods will not modify fields
	- Can be called on const objects
```c++
struct vector {
	...
	size_t size() const;
	int &itemAt(size_t i) const;
}

void f(const vector &v) {
	v.itemAt(0) = 4;
}
```
- v is a const object
	- **Cannot** change n, cap, theVector
	- **Can** change items pointed at by the vector

Const Overloading:
- Define multiple methods - one for use if object is const, one for use if object is not const
```c++
struct vector {
	...
	const int &itemAt(size_t i) const; // will be called if v is const
	int &itemAt(size_t i); // will be called if v is const
	...
}
```
- First const denotes return type, second const denotes promise that the method won't change the object, and thus can be called by const objects (can also be called by non-cosnt objects)

Inline:
- Keyword `inline` hints to compiler to place function call inline rather than on stack
	- Saves the cost of a function call
	- Doesn't have to listen, decides on its own even without inline
	- Metho body inside class implicitly suggests inline

##### Problem 9: Keep it a Secret to Everybody
Interfering with ADTs:
1. **Forgery**: creating an object without using a ctor function
	- Not possible once we wrote ctors
2. **Tampering**: accessing the internals without using a provided interface function... still an issue
	- Need to provide and rely on abstractions that have invariants

Private and Public:
- **Private** fields/methods are only accessible to the class itself
- **Public** fields/methods are accessible to anyone
- In a **struct**: defaults to public access
- In a **class**: defaults to private access

##### Problem 10: Walk Faster
Design Patterns:
- Well-known solutions to well-studied problems, adapted to suit current needs

Iterator Pattern:
- Efficient iteration over a collection without exposing the underlying structure
- Create a subclass, iterator, which needs the following:
	- Within the iterator class:
		- `iterator(...) : ... {}` - constructor
		- `bool operator != (const iterator &other) const { ... }` - not equal operator
		- `type &operator*() { ... }` - dereference operator
		- `iterator &operator++() { ... }` - add 1 operator
	- Outside of iterator class, inside wrapper class:
		- `iterator begin() { ... }` - begin method, defines start of iterator
		- `iterator end() { ... }` - end method, defines end of iterator
- Iterator over const is different over iterator over non-const -> need a second iterator class
	- Exact same as above, new class `const_iterator`
	- Only difference is declare `const type &operator*() const { ... }`

Auto:
- `auto x = expr` keyword saves time for writing the desired type
- Gives x the same type as the expr's value
	- Faster because compiler does not need to do any type checking to see if it matches

Range-Based For Loops:
```c++
for (auto n : l) {
	out << n << ' ';
}
```
- Available for any class that satisfies:
	1. Methods/functions **begin/end** (must be called begin/end) that return an iterator object (doesn't need to be called iterator)
	2. The iterator class must support unary `*`, prefix `++`, and `!=`
- Examples:
	- `for (auto n : l) ++n;` -> n declared by value, ++n will increment n, but not list items
	- `for (auto &n : l) ++n;` -> n declared by reference, ++n will increment list items
	- `for (const auto &n : l) ++n;` -> n declared by const reference, no copying but cannot be modified

Friendship:
- `friend` keyword gives class access to private attributes of friend class
- Advice: limit friends, it weakens encapsulation

##### Problem 11: Now You've Gone Too Far
Raising Exceptions:
- Raise an exception by doing `throw exception{};` which constructs an object of type exception and throws it

Try/Catch Blocks:
- Attempt code withing a `try { ... }` block
- If an exception is raised, catch it:
	- Catch a specific exception: `catch (exception &e) { ... }`
	- Catch all exceptions: `catch (...) { ... }` (literal `...` inside the brackets!)

Throw to Caller:
- The exception will propagate back through the call chain until a handler is found
	- Called unwinding the stack
	- If no handler is found, program aborts
	- Control resumes after the catch block (problem code is not retried)

What if Ctor Throws?
- Object is partially constructed
- Dtor will not run on partially ctored objects
- Ctor must clean up after itself and dtor the members that have already been ctored

What if Dtor Throws?
- Trouble - by default, program aborts immediately (`std::terminate` is called)
- If you really want a throwing dtor, tag it with `noexcept(false)`, but watch out...

##### Problem 12: But I Want a Vector of Chars
Templates:
```c++
template<typename T> class vector{
	size_t n, cap;
	T *theVector;
  public:
    vector();
    ...
    void push_back(T n);
    ...
}

template<typename T> vector<T>::vector() : n{0}, cap{1}, theVector{new T[cap]} {}
template<typename T> void vector<T>::push_back(T n) { ... }
```
- Generalize abstraction over types

	**NOTE: must put implementation in interface file, cannot put it in impl file** 

Semantics of Templates:
- The first time the compiler encounters `vector<type1>` where `type1` is a type, it creates a version of the code for vector where `type1` replaces `T` and compiles the new class
- Same thing for every new `type2`, ...
	- Compiler can't do this unless it knows all the details about the class
	- So the implementation need to be available, ie. in the `.h` file
	- Can also write the method bodies inline

##### Problem 13: Better Initialization
Initializer List Construction:
```c++
#include <initializer_list>
template<typename T> class vector {
  public:
    vector();
    vector(size_t n, T i = T{}) : theVector{new T[n]}, n{n}, cap{n} {
	    for (size_t j = 0; j < n; ++j) {
		    theVector[j] = i;
	    }
	}
    vector(std::initializer_list<T> init) : theVector{new T[init.size()]}, n{init.size()}, cap{init.size()} {
	    size_t i = 0;
	    for (auto &t : init) theVector[i++] = t;
    } 
}
```
- Allows for true array-style initialization
```c++
vector<int> v {1, 2, 3, 4, 5}; // 1 2 3 4 5
vector<int> v; // empty
vector<int> v{5}; // 5
vector<int> v{3, 5}; // 3 5
```
- Default constructors > initializer list constructors > other constructors
	- To get initializer list constructors to run, need brace bracket init
	- To get other constructors to run, need round bracket init
- Initializer contain items stored in contiguous memory (begin method returns a ptr)
	- Using one array to build another
- Initializer lists meant to be immutable, do not modify contents, do not use memory as standalone data structures
- **In general:** if you know how big your vector will be, you can save reallocation cost by requesting the memory up front

##### Problem 14: Actually... I Want a Vector of Posns
Creating a Vector of Posns:
```c++
struct Posn {
	int x, y;
	Posn(int x, int y) : x{x}, y{y} {}
};

int main() {
	vector<Posn> v; // won't compile
	...
}
```
- Creating a vector of type Posn initializes the internal array of Posns... but Posn doesn't have a default ctor
- Need to separate memory allocation (Step 1) from object initialization (Step 2-4)

Allocation:
```c++
void *operator new(size_t n)
```
- Allocate n bytes (no initialization), return `void *`
- In C: `void*` implicitly converts to any ptr type
- In C++: the conversion requires a cast

Initialization:
```c++
new (addr) type
```
- "placement new"
- Constructs `type` object at `addr`, does not allocate any memory

Putting it Together:
```c++
template<typename T> class vector {
	...
  public:
    vector() : n{0}, cap{1}, theVector{static_cast<T*> (operator new(cap * sizeof(T)))} {}
    vector(size_t n, T x = T{}) : n{n}, cap{n}, theVector{static_cast<T*> (operator new(cap * sizeof(T)))} {
	    for (size_t i = 0; i < n; ++i) {
		    new (theVector ++i) T(x);
	    }
    }
	
	void push_back(T x) {
		increaseCap();
		new (theVector + (n++)) T(x);
	}
	void pop_back() {
		if (n) theVector[--n].~T(); // explicitely invoke the dtor
	}
	void clear() { while(n) pop_back(); }
	~vector() {
		clear();
		operator delete(theVector);
	}
}
```

##### Problem 15: Less Copying
Template Functions:
```c++
template<typename T> void swap(T &a, T &b) {
	T tmp {std::move(a)};
	a = std::move(b);
	b = std::move(tmp);
}
```
- As with template classes, the type arg can be omitted if C++ can deduce it from the types of args

Variadic Templates:
```c++
template<typename T> class vector {
  public:
	template<typename... Args>
	void emplace_back(Args... args) {
		increaseCap();
		new (theVector + n++) T(args...);
	}
};
```
- `...` signifies variable number
- `Args` is a sequence of type variables denoting the types of the actual arguments
- `args` is a sequence of program variables denoting the actual args

How to Take Args by Reference?
```c++
template<typename... Args> void emplace_back(Args &&... args) {
	increaseCap();
	new (theVector + (n++)) T{args...};
}
```
- `Args &&` is a universal reference, can bind to an lvalue or an rvalue
- When is a reference universal? Must have the form `T&&` where `T` is a type arg being deduced for the current template function call
```c++
template<typename T> class C {
  public:
    template<typename U> void g(U &&x); // universal
    template<typename U> void h(const U &&x); // not universal
    void k(T &&x); // not universal
}
```

Preserving lvalue/rvalue Information:
- Don't know if args are lvalues, rvalues, or a mix -> want to call move on args iff args are rvalues
```c++
template<typename... Args> void emplace_back(Args &&... args) {
	increaseCap();
	new (theVector + (n++)) T (std::forward<Args>(args)...);
}
```
- `std::forward` calls move if arg is an rvalue ref, else does nothing
- This allows for args to be passed to T's ctor with lvalue/rvalue information preserved, called **perfect forwarding**

Shorthand Auto Notation for Template Functions:
- If you see a function with an argument of type `auto`, that function is a template:
```c++
auto max(auto x, auto y) { ... }
// is equivalent to
template<typename T1, typename T2> auto max(T1 x, T2 y) { ... }
```

##### Problem 16: Memory Management is Hard
Exception Safety:
- Raising and handling an exception should not corrupt the program
- Leaks are a corruption of the program's memory -> will eventually degrade performance and crash the program

What Constitutes Exception Safety? 3 Levels:
1. Basic guarantee
	- Once an exception has been handled, the program is in some valid state
	- No leaked memory, no corrupted data structures, all variants are maintained
2. Strong guarantee
	- If an exception propagates out of a function f, then the state of the program will be as if f had not been called
	- f either succeeds completely or not at all
3. No-throw guarantee
	- A function offers the no-throw guarantee if it never emits an exception and always accomplishes its purpose

Unique Pointers:
- 