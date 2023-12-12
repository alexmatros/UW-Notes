*Taught by Professor Joe West, F23 Offering*
# Chapter 1

## Scalar functions
- A scalar function is a rule which assigns each ordered pair of real numbers $(x, y)$ in a set $D \subseteq \mathbb{R}^2$ and a unique real number where $z=f(x,y)$
- *D* is called the domain of $f$; the set $\{f(x,y) | (x,y) \in D\}$ is called the range of $f$
## Sketching Surfaces
### Level Curves
1. Set $f(x,y)=k$ for some constant $k$
2. Simplify the remainder of the equation
3. Plot points for various possible values of $k$
### Cross Sections
1. Set $x=c$ for some constant $c$
2. Simplify the remainder of the equation
3. Plot points for various possible values of $c$
4. Repeat steps (1)-(3) with $y=d$ for some constant $d$
# Chapter 2
## Limits
### Neighbourhoods
- A **neighbourhood** of $(a,b) \in \mathbb{R}^2$ of radius $r>0$ is a subset of $\mathbb{R}^2$ defined by $N_{r(a,b)}= \{(x,y):||(x-y) - (a,b)|| < r\}$ 
### Formal Definition of a Limit
- Suppose $f(x,y)$ is defined in some neighbourhood of $(a,b)$, except possibly at $(a,b)$. If for every $\epsilon > 0$, there exists some $\delta > 0$ such that $|f(x,y) - L|<\epsilon$ whenever $0 < ||(x,y)-(a,b)||< \delta$, then we say that "the limit of $f(x,y)$ exists and equals $L$" and we write $\lim_{(x,y)->(a,b)}f(x,y) =L$ 
### Proving Limit DNE
- Idea: try different paths approaching the point
- This can be setting $y=mx$ or setting $y=const$ such that it "approaches" the limit bounds
- If you arrive at the conclusion that taking different paths yields a different value for the limit, limit DNE
### Squeeze Theorem
- If there exists a function $B(x,y)$ such that $|f(x,y)-L|\leq B(x,y)$, $\forall (x,y)$ in some neighbourhood of $(a,b)$ except possibly at $(a,b)$, and $\lim_{(x,y)->(a,b)}B(x,y)=0$, then $\lim_{(x,y)->(a,b)}f(x,y)=L$
	- Proof: let $\epsilon>0$. Then, $\exists \delta$ such that $|B(x,y) - 0| < \epsilon$ whenever $0<||(x,y)-(a,b)||<\delta$. But then, $|f(x,y)-L|\leq B(x,y) < \epsilon$, so by definition, $\lim_{(x,y)->(a,b)}f(x,y)=L$
### Tricks for Inequalities
- ${(x^2+y^2)^2=x^4+y^4+2x^{2}y^{2}\Longrightarrow}x^4+y^{4}\leq(x^2+y^2)^2$
- $(|x|-|y|)^{2}\geq 0 \Longrightarrow |x||y| \leq \displaystyle \frac{x^2+y^2}{2}$
- $|x+y| \leq |x| + |y|$
- $x^{a}=(x^c)^{\frac{a}{c}}$ **VERY USEFUL!**
# Chapter 3
## Continuity
### Continuity Definition
- A function $f(x,y)$ is **continuous** at $(a,b)$ means $lim_{(x,y)->(a,b)}f(x,y)=f(a,b)$. $f$ is continuous on $D \subseteq \mathbb{R}^2$ means that $f$ is continuous at each point in $D$
### Continuity Theorem
- Let $f(x,y)$ and $g(x,y)$ be continuous at $(a,b)$. Then:
	1. $f+g$ and $fg$ are continuous at $(a,b)$
	2. $\frac{f}{g}$ is a continuous function, provided $g(a,b)\neq 0$
### Continuity of Composition Theorem
- Let $f$ be a single-variable function and $g$ be a function of two vars. If $g$ is continuous at $(a,b)$ and $f$ is continuous at $g(a,b)$ then $f \circ g$ is continuous at $(a,b)$
# Chapter 4
## Partial Derivatives
### How to Differentiate Multi-Var
- Two ways to differentiate $f(x,y)$:
	1. Hold $y$ fixed, differentiate wrt $x$ - $\frac{\partial{f}}{\partial{x}}$
	2. Hold $x$ fixed, differentiate wrt $y$ - $\frac{\partial{f}}{\partial{y}}$
### Formal Definition
1. $\displaystyle \frac{\partial{f}}{\partial{x}}(a,b) = \lim_{h\to0}\frac{f(a+h,b)-f(a,b)}{h}$ 
2. $\displaystyle \frac{\partial{f}}{\partial{y}}(a,b) = \lim_{h\to0}\frac{f(a,b+h)-f(a,b)}{h}$ 
### Second Derivatives
- Apply partial derivatives to the results of the first partials
### Clairaut's Theorem
- If $f_{x}, f_{y}, f_{xy}, f_{yx}$ are all defined in some neighbourhood of $(a,b)$ and $f_{xy}, f_{yx}$ are continuous at $(a,b)$, then, $f_{xy}(a,b)=f_{yx}(a,b)$
### Higher-Order Derivatives
- You can have $f_{xxx}, f_{xxy}, f_{yxy}, \dots$
- Can also do partials on more variables, simply differentiate wrt one of the variables, hold rest fixed
- $f\in C^k$, "$f$ is of class $C^k$" means that the $k^{th}$ partial derivatives of f are continuous
### Geometric Interpretation
- $\frac{\partial{f}}{\partial{x}}(a,b)$ refers to the slope in the $x$-direction for the function $f$ at the point $(a,b)$
- Similar for y
## Tangent Planes
### Equation of Tangent Plane
- The equation for the tangent plane to the surface $z=f(x,y)$ at the point $(a,b,f(a,b))$ is: $z=f(a,b)+f_x(a,b)(x-a)+f_y(a,b)(y-b)$
- Not always valid
### Definitions for Linear Approx.
- The **linearization** of $f(x,y)$ at $(a,b)$ is: $L_{(a,b)}(x,y)=f(a,b)+f_x(a,b)(x-a)+f_y(a,b)(y-b)$
- The **linear approximation** formula is: $f(x,y) \approx L_{(a,b)}(x,y)$ for $(x,y)$ near $(a,b)$
### Increment Form of Linear Approx.
- Let $\triangle x =x-a$, $\triangle y=y-b$ and $\triangle f = f(x,y)-f(a,b)$
- Rearranging the linear approximation formula yields: $\triangle f \approx \frac{\partial{f}}{\partial{x}}(a,b)\triangle x + \frac{\partial{f}}{\partial{y}}(a,b) \triangle y$
### Gradient
- The **gradient** of $f$ at $\underline{a}$ is defined by: $\nabla f(\underline{a})=(f_{x}(\underline{a}), f_{y}(\underline{a}), f_{z}(\underline{a}))$
# Chapter 5
## Differentiability
### Definition of Differentiability
- A function $f$ is differentiable at $(a,b)$ if and only if $\lim_{(x,y)\to(a,b)}\displaystyle \frac{|R_{1,(a,b)}(x,y)|}{||(x,y)-(a,b)||} = 0$
- Where $R_{1,(a,b)}(x,y) = f(x,y)-(f(a,b)+f_x(a,b)(x-a)+f_y(a,b)(y-b)) = f(x,y) - L_{(a,b)}(x,y)$
### Differentiability Implies Continuity
- If $f(x,y)$ is differentiable at $(a,b)$, then $f$ is continuous at $(a,b)$
- If $f$ is not continuous at $(a,b)$, then $f$ is not differentiable at $(a,b)$
### Partial Derivatives and Differentiability
- If $f_x$ and $f_y$ are continuous at $(a,b)$, then $f$ is differentiable at $(a,b)$
## Parametric and Vector Curves
- Defines curves in terms of $x(t), y(t)$ such that both depend on the same variable, $t$
# Chapter 6
## Chain Rule
### Definition of Chain Rule
- Normal form: $\displaystyle \frac{d}{dt}f(x(t),y(t))=\frac{df}{dt}=\frac{\partial{f}}{\partial{x}}\frac{dx}{dt}+\frac{\partial{f}}{\partial{y}}\frac{dy}{dt}$
- Vector form: $\displaystyle \frac{df}{dt}=(\frac{\partial{f}}{\partial{x}}, \frac{\partial{f}}{\partial{y}})\cdot(\frac{dx}{dt},\frac{dy}{dt})=\nabla f \cdot \underline{x}'(t)$
- Need to assume that $f$ is differentiable
### Complex Chain Rule
- Draw a dependency graph
- For whatever you are differentiating, follow the graph all the way down
- If the end variable is alone, simple derivative
- If the end variable is amongst others, partial derivative
# Chapter 7
## Directional Derivatives
### Definition of Directional Derivatives
- The **directional derivative** of $f(x,y)$ at $\underline{a}=(a,b)$ in direction of unit vector $\hat{u}=(u_{1},u_{2})$ is: $D_{\hat{u}}f(a,b)=\frac{d}{ds}f(\underline{a}+s\hat{u})|_{s=0}$
- If $f$ is differentiable at $(a,b)$, then $D_{\hat{u}}f(a,b)=\nabla f(a,b) \cdot \hat{u}$
- If $f$ is not differentiable, must use definition
- If direction vector $\underline{u}$ is not a unit vector, must normalize it: $\hat{u}=\displaystyle \frac{\underline{u}}{||\underline{u}||}$
### Interpretation of Directional Derivative
- $D_{\hat{u}}f(a,b)$ is the rate of change of f wrt **distance** in the $\hat{u}$ direction
### Choosing $\hat{u}$
- Maximizing $D_{\hat{u}}f(a,b)$:
	- Choose $\hat{u}=\displaystyle \frac{\nabla f(a,b)}{||\nabla f(a,b)||}$
- Minimizing $D_{\hat{u}}f(a,b)$:
	- Choose $\hat{u}=-\displaystyle \frac{\nabla f(a,b)}{||\nabla f(a,b)||}$
- Setting equal to 0
	- Choose a perpendicular $\hat{u}$
- **Important**: $\nabla f(a,b)$ gives the direction of the max increase of $f$
### Orthogonality to Level Curves
- If $f(x,y)$ is differentiable at $(a,b)$ and $\nabla f(a,b) \neq (0,0)$, then $\nabla f(a,b)$ is orthogonal to the level curve $f(x,y)=k$
### Equation of the Tangent Plane
- Since $\nabla f(\underline{a})$ is perpendicular to the level set $f(\underline{x}) = k$, we have that the equation of the tangent plane to the surface $f(x,y,z)=k$ at $(a,b,c)$ is $\nabla f(\underline{a})\cdot(\underline{x}-\underline{a})=0$
# Chapter 8
## Taylor Polynomials
### Definition of Taylor Polynomials
- The Taylor polynomial for $f(x,y)$ with $f \in C^2$ is: 
	- $P_{1,(a,b)}(x,y)=f(a,b)+f_x(a,b)(x-a)+f_y(a,b)(y-b)=L_{(a,b)}(x,y)$
	- $P_{2,(a,b)}(x,y)=P_{2,(a,b)}(x,y)+\displaystyle \frac{1}{2!}[f_{xx}(a,b)(x-a)^2+2f_{xy}(a,b)(x-a)(y-b)+f_{yy}(a,b)(y-b)^2]$
### Taylor's Formula
- If $f \in C^2$ in some neighbourhood of $(a,b)$, then $\exists \underline{c}=(c,d)$ on the line segment joining $\underline{a}=(a,b)$ and $\underline{x}=(x,y)$ such that: $f(\underline{a})+f_x(\underline{a}))(x-a)+f_y(\underline{a})(y-b)+R_{1,\underline{a}}(\underline{x})$
- Where $R_{1,\underline{a}}(\underline{x})=\displaystyle \frac{1}{2!}[f_{xx}(\underline{c})(x-a)^2+2f_{xy}(\underline{c})(x-a)(y-b)+f_{yy}(\underline{c})(y-b)^2]$
- In general, for $f \in C^2,$ $|R_{1}|\leq M[(x-a)^2+(y-b)^2]$ where $M$ is some constant
### Generalizations
- Can be generalized for any indexed Taylor polynomial:
	- $P_{k,(a,b)}(x,y)=P_{k-1,(a,b)}(x,y)+\displaystyle \frac{1}{k!}[(x-a)\frac{\partial}{\partial{x}}+(y-b)\frac{\partial}{\partial{y}}]^kf(a,b)$
	- $R_{k,(a,b)}(x,y)=\displaystyle \frac{1}{(k+1)!}[(x-a)\frac{\partial}{\partial{x}}+(y-b)\frac{\partial{}}{\partial{y}}]^{k+1}f(c,d)$
	- $f(x,y) = P_{k,(a,b)}(x,y) + R_{k,(a,b)}(x,y)$
- Can be generalized to any number of variables $f(x_1,x_2,\dots,x_n)$:
	- Use $(x_1-a_1)D_1+(x_2-a_2)D_2+\dots+(x_n-a_n)D_n=(\underline{x}-\underline{a})\cdot\nabla$
# Chapter 9
## Critical Points
### Definition of Local Max/Min
- If $f(x,y)\leq f(a,b)$ in a neighbourhood of $(a,b)$ means that $(a,b)$ is a **local max**
- If $f(x,y)\geq f(a,b)$ in a neighbourhood of $(a,b)$ means that $(a,b)$ is a **local min**

- If $(a,b)$ is a local max or a local min of $f$, then $f_{x}(a,b)=0$ (or $DNE$) AND $f_{y}(a,b)=0$ (or $DNE$) 
### Definition of Critical Points
- A critical point is a point which satisfies that $f_{x}(a,b)=0$ (or $DNE$) AND $f_{y}(a,b)=0$ (or $DNE$) 
- A saddle point is a critical point that is neither a local min nor a local max
### Finding Critical Points
- Set $f_x=0$ and $f_y=0$
- Solve for possible values of $(a,b)$
### Classifying Critical Points
- Define the Hessian matrix:
	- $\det{\begin{pmatrix}{f_{xx}(a,b)}&{f_{xy}(a,b)}\\{f_{xy}(a,b)}&{f_{yy}(a,b)}\end{pmatrix}} = \det{(Hf(a,b))}$ where $H$ represents the Hessian
#### Second Derivative Test
- If $f \in C^2$ in a neighbourhood of critical point $(a,b)$, then:
	1. If $\det{(Hf(a,b))} > 0$ and $f_{xx}(a,b)>0$, then $(a,b)$ is a **local min**
	2. If $\det{(Hf(a,b))} > 0$ and $f_{xx}(a,b)<0$, then $(a,b)$ is a **local max**
	3. If $\det{(Hf(a,b))} < 0$, then $(a,b)$ is a **saddle point**
	4. If $\det{(Hf(a,b))} = 0$, then there is **no conclusion** from this theorem
### Definition of Absolute Max and Min
- If $f(a,b)\geq f(x,y), \forall(x,y)\in S$, we call $(a,b)$ an **absolute max** point of $f$ on $S$
- If $f(a,b)\leq f(x,y), \forall(x,y)\in S$, we call $(a,b)$ an **absolute min** point of $f$ on $S$
# Chapter 10
### Extreme Value Theorem
- If $S \subseteq \mathbb{R}^{2}$ is closed and bounded and $f$ is continuous on S, then there exist points $(c_1,d_1)$ and $(c_2,d_2)$ in $S$ such that $f(c_1,d_{1})\leq f(x,y) \leq f(c_2,d_{2}), \forall (x,y)\in S$
	- $(c_1,d_1)$ is an absolute min, $(c_2,d_2)$ is an absolute max
### Basic Algorithm for Finding Extreme Values
- For a continuous $f$, closed and bounded $S$:
	1. Evaluate $f$ at all critical points in $S$
	2. Find extremes of $f$ on the boundary of $S$
	3. Choose points from (1) and (2) with the greatest/least values of $f$
### Method of Lagrange Multipliers
- To find the absolute max and min of a differentiable function $f(x,y)$ subject to constraint $g(x,y)=k$, $g\in C^2$, evaluate $f$ at all points $(a,b)$ which satisfy:
	1. $\nabla f(a,b)=\lambda \nabla g(a,b)$ and $g(a,b)=k$    OR
	2. $\nabla g(a,b)=\underline{0}$ and $g(x,y)=k$    OR
	3. $(a,b)$ is an endpoint of the constraint curve
- If $g(x,y)=k$ is unbounded (ex. hyperbola), then check "endpoints" by: $\lim_{||(x,y)||\to \infty}f(x,y)$ along the constraint curve
- Generalizes for $f(x_1,x_2,\dots,x_n)$
# Chapter 11
## Definition of Double Integral
- $\displaystyle \iint_{D} f(x,y)dA = \lim_{n\to\infty}\sum\limits_{i=1}^{n}f(x_i,y_i)\triangle A_i$ - split $D$ into many small patches, extend it up to $f$ and create a column, sum all of them up
	- $f(x_i,y_i)$ is the height of the surface
	- $\triangle A_i$ is the area of a patch
	- $f(x_i,y_{i}) \triangle A_i$ is the volume of the $i^{th}$ column
## Interpretations of Double Integrals
- $\displaystyle \iint_{D}1dA =$ area of $D$
- $\displaystyle \iint_{D}p(x,y)dA=$ mass of $D$, where $p(x,y)$ is the areal mass density
- Average value of $f(x,y)$ on $D$: $f_{av}=\displaystyle \frac{\iint_{D}f(x,y)dA}{\iint_{D}1dA}$
## Evaluating Double Integrals
- Find bounds on $D$ for both $x$ and $y$, then create an iterated integral
	- These are the bounds on the integral
	- Ensure most-exterior integral has constant bound, interior bounds can be in terms of other variables
- Describe $D$ by: $x_{l}\leq x \leq x_{u}$ and $y_{l}(x)\leq y \leq y_{u}(x)$
- Then: $\displaystyle \iint_{D} f(x,y)dA = \int_{x_l}^{x_u}(\int_{y_l(x)}^{y_u(x)}f(x,y)dy)dx$ 
## Properties of Double Integrals
1. $\displaystyle \iint_{D}(f+g)dA=\iint_{D}fdA+\iint_{D}gdA$
2. If $f\leq g$, then $\displaystyle \iint_{D}fdA \leq \iint_{D}gdA$
3. $\displaystyle |\iint_{D}fdA|\leq \iint_{D}|f|dA$ (triangle inequality)
4. $\displaystyle \iint_{D}fdA = \iint_{D_1}fdA+\iint_{D_2}fdA$ (decomposition)
## Symmetry of Double Integrals
- Consider $\displaystyle \iint_{D}f(x,y)dA$ such that $D$ is symmetric around the origin
	- If $f(x,y)$ is an odd function (ie. the degree of one of x, y is odd), integral equals to 0 (columns cancel out in pairs)
	- If $f(x,y)$ is an even function, integral is equal to 4 times the integral over just a corner of the domain
# Chapter 12
## Mappings
### Definition of Mappings
- A function whose domain is a subset of $\mathbb{R}^n$ and whose codomain is a subset of $\mathbb{R}^m$ is called a **vector-valued** function
- A vector-valued function whose domain is the subset of $\mathbb{R}^n$ and whose codomain is a subset of $\mathbb{R}^n$ is called a **mapping**
### Definition of Jacobian
- Consider the map $(u,v)=F(x,y)=(f(x,y),g(x,y))$
- The **Jacobian** of the mapping is written as follows: $\displaystyle \frac{\partial{(u,v)}}{\partial{(x,y)}}=\det{\begin{pmatrix}{f_{x}}&{f_{y}}\\{g_{x}}&{g_{y}}\end{pmatrix}}$
# Chapter 13
## More Mappings
### Inverse Mapping Theorem
* Assume $f,g \in C^1$ at $(a,b)$. If $\displaystyle \frac{\partial{(u,v)}}{\partial{(x,y)}} \neq 0$ at $(a,b)$, then there exists a neighbourhood at $(a,b)$ in which $F$ has an inverse $F^{-1}$ which has continuous partials
#### Inverse Property
- $\displaystyle \frac{\partial{(u,v)}}{\partial{(x,y)}} = \frac{1}{\frac{\partial{(x,y)}}{\partial{(u,v)}}}$
- Denominator of RHS is the Jacobian of the inverse map $(x,y)=F^{-1}(u,v)$
### Inventing Mappings
- Strategy 1:
	- Factor the function into the form $(\dots_{u})^{2} + (\dots_{v})^2$
	- Set $u =$ contents of the first bracket, $v=$ contents of the second
- Strategy 2:
	- Analyze boundary curves of original surface
	- Should have 4 formulas
	- Manipulate formulas to have them in the form of:
		- $\alpha = \dots$
		- $\alpha = \dots$
		- $\beta = \dots$
		- $\beta = \dots$
	- Set $u=\alpha$, $v=\beta$, and the dots on the other side are the new boundary curves
### Jacobian Scale Factor
- The absolute value of the Jacobian represents a local area scale factor
	- Accounts for change through mapping
### Change of Variables Theorem ("Substitution Rule")
- $\displaystyle \iint_{D_{xy}}f(x,y)dA=\iint_{D_{uv}}f(g(u,v), h(u,v))|\displaystyle \frac{\partial{(x,y)}}{\partial{(u,v)}}|dudv$
	- $f$ is continuous
	- $dA = dxdy$
	- $D_{xy}$ and $D_{uv}$ are closed and bounded
	- The Jacobian is non-zero
	- The mapping is $(x,y)=G(u,v)=(g(u,v),h(u,v))$
		- $g,h \in C^1$
# Chapter 14
## Triple Integrals
### Definition of Triple Integrals
- $\displaystyle \iiint_{D}f(x,y,z)dV=\lim_{n \to \infty}\sum_{i=1}^{n}f(x_i,y_i,z_i)\triangle V_i$
	- "Slice" $D$ into n "boxes"
### Interpretations of Triple Integrals
- $\displaystyle \iiint_{D}1dV=$ volume of $D$
- $\displaystyle \iiint_{D}p(x,y,z)dV=$ total mass of $D$, where $p(x,y,z)=$ mass density
- Average value of $f$ over $D$: $f_{av}=\displaystyle \frac{\iiint_{D}f(x,y,z)dV}{\iiint_{D}1dV}$
### Evaluating Triple Integrals
- Want to describe $D$ by $(x,y) \in D_{xy}$, $z_{l}(x,y)\leq z \leq z_{u}(x,y)$
	- Then: $\displaystyle \iiint_{D}f(x,y,z)dV=\iint_{D_{xy}}(\int_{z_l(x,y)}^{z_u(x,y)}f(x,y,z)dz)dA$
	- The interior integral evaluates to some function in terms of $x,y$
	- Proceed with the remainder as with a double integral
- Other ways: start with $x$ or $y$ first
### Change of Variables for Triple Integrals
- $\displaystyle \iiint_{D_{xyz}}f(x,y,z)dV=\iiint_{D_{uvw}}f(x(u,v,w),y(u,v,w),z(u,v,w))|\frac{\partial{(x,y,z)}}{\partial{(u,v,w)}}|dudvdw$
	- $f$ is continuous
	- $dV = dxdydz$
	- $D_{xyz}$ and $D_{uvw}$ are closed and bounded
	- The Jacobian is non-zero
	- The mapping is $(x,y,z)=G(u,v,w)=(x(u,v,w), y(u,v,w), z(u,v,w))$
		- $x(u,v,w), y(u,v,w), z(u,v,w) \in C^1$ (ie. map one-to-one)
# Appendix B
## B.1 Polar Coordinates
### Equivalent Formulas
- $\begin{cases} x = r\cos{\theta} \\ y = r\sin{\theta} \end{cases}$
- $r = \sqrt{x^{2}+y^{2}}\geq0$
- $\tan{\theta}=\frac{y}{x}$
### Converting Polar ->Cartesian
- Ex. $(r, \theta)=(1, \pi) \Longrightarrow (x,y)=(1 * \cos{\pi}, 1 * \sin{\pi})$
### Converting Cartesian ->Polar
- Ex. $(x,y)=(-1,\sqrt(3)) \Longrightarrow (r, \theta)=(2, \frac{2\pi}{3})$
	- First, $r = \sqrt{(-1)^2+(\sqrt{3})^{2}}=2$
	- Then, $\tan{\theta}=\frac{\sqrt{3}}{-1}=-\sqrt{3}$
		- Find angle using special triangles/CAST on cartesian plane
### Describing Cartesian Regions with Polar
- Works well for circular regions
- Find bound on $r, \theta$
	- Make use of conversions (ie. $x=r\cos{\theta}$, $y=r\sin{\theta}$), etc.
### Jacobian
- When using a polar mapping, Jacobian = $r$
## B.2 Cylindrical Coordinates
### Equivalent Formulas
- $\begin{cases} x = r\cos{\theta} \\ y = r\sin{\theta} \\ z = z \end{cases}$
### Converting Cartesian <-> Cylindrical
- Use similar strategy as with polar
- Make use of formulas, map out the region and identify angle bounds
- Make $z$ in terms of $x,y$ then in terms of their equivalent polar formula
### Jacobian
- When using a cylindrical mapping, Jacobian = $r$
## Spherical Coordinates
### Equivalent Formulas
- $\begin{cases} x = p\sin{\phi}\cos{\theta} \\ y = p\sin{\phi}\sin{\theta} \\ z = p\cos{\phi} \end{cases}$
- $p \geq 0$
- $0 \leq \theta \leq 2\pi$
- $0 \leq \phi \leq \pi$
### Converting Cartesian <-> Spherical
- Again, similar strategy to polar
- Make use of formulas, map out the region and identify angle bounds
- If hard to see, look at cross sections
### Jacobian
- When using a spherical mapping, Jacobian = $p^{2}sin{\phi}$