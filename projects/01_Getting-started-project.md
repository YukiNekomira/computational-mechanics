---
jupytext:
  formats: notebooks//ipynb,md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.11.4
kernelspec:
  display_name: Python 3 (ipykernel)
  language: python
  name: python3
---

```{code-cell} ipython3
import numpy as np
import matplotlib.pyplot as plt
plt.style.use('fivethirtyeight')
```

# Computational Mechanics Project #01 - Heat Transfer in Forensic Science

We can use our current skillset for a macabre application. We can predict the time of death based upon the current temperature and change in temperature of a corpse. 

Forensic scientists use Newton's law of cooling to determine the time elapsed since the loss of life, 

$\frac{dT}{dt} = -K(T-T_a)$,

where $T$ is the current temperature, $T_a$ is the ambient temperature, $t$ is the elapsed time in hours, and $K$ is an empirical constant. 

Suppose the temperature of the corpse is 85$^o$F at 11:00 am. Then, 2 hours later the temperature is 74$^{o}$F. 

Assume ambient temperature is a constant 65$^{o}$F.

1. Use Python to calculate $K$ using a finite difference approximation, $\frac{dT}{dt} \approx \frac{T(t+\Delta t)-T(t)}{\Delta t}$.

```{code-cell} ipython3
T1 = 85
Ta = 65
dT = -11
dt = 2
T2 = 74
Tt = dT/dt
K = -((T2-T1)/dt)/(T1-Ta)
print(K)
```

2. Change your work from problem 1 to create a function that accepts the temperature at two times, ambient temperature, and the time elapsed to return $K$.

```{code-cell} ipython3
import numpy as np
def K2(t1,t2,Ta,dt):
    dT = t2-t1
    dTdt = dT/dt
    
    return -dTdt/(t1-Ta)
rK= K2(85,74,65,2)

print(rK)        
```

```{code-cell} ipython3

```

3. A first-order thermal system has the following analytical solution, 

    $T(t) =T_a+(T(0)-T_a)e^{-Kt}$

    where $T(0)$ is the temperature of the corpse at t=0 hours i.e. at the time of discovery and $T_a$ is a constant ambient temperature. 

    a. Show that an Euler integration converges to the analytical solution as the time step is decreased. Use the constant $K$ derived above and the initial temperature, T(0) = 85$^o$F. 

    b. What is the final temperature as t$\rightarrow\infty$?
    
    c. At what time was the corpse 98.6$^{o}$F? i.e. what was the time of death?

```{code-cell} ipython3
import math
import matplotlib.pyplot as plt
t = np.linspace(0,2,10)
t2 = np.linspace(0,2,10)
tstep = np.zeros(len(t))
tstep2 = np.zeros(len(t))
k = -.275
T0 = 85
Ta = 65
for j in range(0,len(t)):
    tstep2[j-1] = ((k*(T0-Ta)*t2[j-1])+T0)
for i in range(0,len(t)):
    tstep[i-1] = Ta + (T0 - Ta)*(math.exp(k*t[i-1]))
plt.plot(t,tstep2,label = 'Analytical')
plt.plot(t,tstep,label = 'Numerical') 
plt.legend()
print(t)
print(tstep)
print(tstep2)
tf = (math.log((98.6-Ta)/(T0-Ta)))/k
tff = 11+tf
print(tff)
```

```{code-cell} ipython3
print('B:as final temperature goes to infinity it will get closer and closer to the ambient temperature')
print('At around 9:11 pm the dead body was 98.6 degrees')
```

4. Now that we have a working numerical model, we can look at the results if the
ambient temperature is not constant i.e. T_a=f(t). We can use the weather to improve our estimate for time of death. Consider the following Temperature for the day in question. 

    |time| Temp ($^o$F)|
    |---|---|
    |6am|50|
    |7am|51|
    |8am|55|
    |9am|60|
    |10am|65|
    |11am|70|
    |noon|75|
    |1pm|80|

    a. Create a function that returns the current temperature based upon the time (0 hours=11am, 65$^{o}$F) 
    *Plot the function $T_a$ vs time. Does it look correct? Is there a better way to get $T_a(t)$?

    b. Modify the Euler approximation solution to account for changes in temperature at each hour. 
    Compare the new nonlinear Euler approximation to the linear analytical model. 
    At what time was the corpse 98.6$^{o}$F? i.e. what was the time of death?

```{code-cell} ipython3
import math
import matplotlib.pyplot as plt
Ta = [50,51,55,60,65,70,75,80]
time = [-5,-4,-3,-2,-1,0,1,2]
tas = np.zeros(len(time))
def Tatime(x):
    return Ta[x]
for x in range(0,len(time)):
    tas[x] = Tatime(x)  
plt.plot(time,Ta)
print(tas)
```

```{code-cell} ipython3

Ta = [50,51,55,60,65,70,75,80]

time = [-5,-4,-3,-2,-1,0,1,2]
dt = time[1]-time[0]
tstep3 = np.zeros(len(time))
k = -.275
Tf = 98.6
T0 = 85

for i in range(0,len(time)):
    tstep3[i-1] = (Tf-T0)/(k*(T0-Ta[i-1]))
print(tstep3)
plt.plot(tstep3,Ta)
print(dt)
```

```{code-cell} ipython3
Ta = [50,51,55,60,65,70,75,80]

time = [-5,-4,-3,-2,-1,0,1,2]
dt = time[1]-time[0]
tstep3 = np.zeros(len(time))
k = -.275
tstep3[0] = 98.6

for i in range(0,len(time)-1):
    tstep3[i+1] = tstep3[i] - 0.275*(tstep3[i]-Ta[i])*dt 
print(tstep3)
plt.plot(time,tstep3)
print(dt)
```

```{code-cell} ipython3

```
