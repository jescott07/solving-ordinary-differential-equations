# The Lorenz System

To solve the Lorenz system presented in the main [README](.. /.. /main/README. md) we will use the Runge-Kutta method to solve the system of differential equations. To do so, we will build a module in Fortran which computes the solutions through this method, and then run it in Python to analyse the results. The implementation of this method and how we can run a Fortran module in Python was discussed before (please read this [README](.. /.. /conv/README. md).

## Build the Fortran Module

First, we define the statements of the module

```fortran
implicit none

real(8), parameter :: P = 3.0d0, b=1.0d0

integer, parameter :: N = 10000

real(8), dimension(N,3) :: x

real(8) :: Tf, r
```

Here we defined the parameters P=3 e b=1 (which are parameters of the equation) and N, which are the number of iterations. Also, we defined a matrix $N \times 3$ that will contain the solutions for each step, remember that $\vec{x} = x_1 \hat{i} + x_2 \hat{j} + x_3 \hat{k}$. Notice that we did a change of variables $y \rightarrow x$ and $x here. Finally, we define the end time (Tf) and r which is a parameter in the equations (that we want to analyse).


And then we build a subfunction to run the Runge-Kutta method.

```fortran
subroutine rk4()

    real(8),dimension(3) :: k1, k2, k3, k4
    integer :: i

    dt = Tf/N
    rc = P*(P+b+3.0d0)/(P-b-1.0d0)

    ! Setting the initial conditions.
    x(1,1) = 0.
    x(1,2) = 1.
    x(1,3) = 0.
    t(1) = 0

    do i=1,n
        k1 = dt*f(x(i,:))
        k2 = dt*f(x(i,:) + (0.5*k1))
        k3 = dt*f(x(i,:) + (0.5*k2))
        k4 = dt*f(x(i,:) + k3)

        x(i+1,:) = x(i,:) + (k1 + 2.0*k2 + 2.0*k3 + k4)/6
        t(i+1) = t(i) + dt
    enddo    

    x1 = x(:,1)
    x2 = x(:,2)
    x3 = x(:,3)

end subroutine rk4

function f(v)
    real(8), dimension(3) :: f, v
    ! real(8) :: r
    f(1) = -P*(v(1)-v(2))
    f(2) = -v(2) + r*v(1) - v(1)*v(3)
    f(3) = -b*v(3) + v(1)*v(2)
end function f
```

Notice that the implementation of the Runge-Kutta method is almost the same, but now x is a three dimensional vector instead of a single float as we did before.

## Analysing the Results With Python

To analyse the results we did a small program in Python. First, we imported the module and the matplot lib library.

```python3
from ex3 import lorenz as lr

from matplotlib import pyplot as plt
```

Thus we defined the parâmeters and run the subroutine rk4.

```python3
lr.r = 10
lr.tf = 100

lr.rk4()
```

We then plotted the results

```python3
x1a,x2a,x3a = lr.x[:,0],lr.x[:,1],lr.x[:,2]

plt.figure()
plt.title('Lorenz System: ' r'$y_1 \times y_2$ ' 'for r = 10')
plt.xlabel(r'$y_1$')
plt.ylabel(r'$y_2$')
plt.plot(x1a,x2a)
plt.savefig('ls_1.png',dpi=300)
plt.show()
plt.close()
```

And did the same for others parâmeters:

```python3
lr.r = 30
lr.tf = 100

lr.rk4()

x1b,x2b,x3b = lr.x[:,0],lr.x[:,1],lr.x[:,2]

plt.figure()
plt.title('Lorenz System: ' r'$y_1 \times y_2$ ' 'for r = 30')
plt.xlabel(r'$y_1$')
plt.ylabel(r'$y_2$')
plt.plot(x1b,x2b)
plt.savefig('ls_2.png',dpi=300)
plt.show()
plt.close()
```