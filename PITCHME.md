---
marp: true
theme: uncover
paginate: true
class:
  - lead
  - invert

---
# Ensemble Kalman Filter in Filterpy

---
# Filterpy

- Free and open source Kalman filter library
    - https://github.com/rlabbe/filterpy
    - Accompanied with the book: https://github.com/rlabbe/Kalman-and-Bayesian-Filters-in-Python/
        - contains many jupyter notebooks to play with
    - Author: `"I opt for clear code that matches the equations in the relevant texts on a 1-to-1 basis, even when that has a performance cost."`


---
## EnKF principles

- similar to unscented Kalman filter
- uses an ensemble of hundreds to thousands of state vectors 
    - samples "randomly" around estimate
    - Monte Carlo algorithm
- adds perturbations at each update and predict step 
---

## Parameters

- __EnsembleKalmanFilter(x, P, dim_z, dt, N, hx, fx)__
    - x: state mean
    - P : covariance of the state
    - dim_z : Number of of measurement inputs
    - dt : time step in seconds
    - N : number of sigma points (ensembles)
    - hx : Measurement function that converts state x into a measurement
    - fx : State transition function that projects
        state x into the next time period
---
### How to run it
```python
enkf = EnsembleKalmanFilter(x, P, dim_z, dt, N, hx, fx)

# Iter 1
enkf.predict() #responses
enkf.update(observations) #update step

# Iter 2
enkf.predict() #responses
enkf.update(observations) #update step
```
---
### enkf.predict()
- Sample normal distribution defined by $x^T$ and $P$ done $N$ times: ${x'}_{i=1..N}$
- Execute "forward models": $f(x_i')$ &rightarrow; $sigma_i$
- Add perturbation to $sigma_i$  defined by noise covariance
- `enkf.sigmas` provides $N$ states
---
### enkf.update(obs) - I.
- Ref: _John L Crassidis and John L. Junkins. Optimal Estimation of Dynamic Systems, 2012_
- Run $h(f(x_i')\equiv sigma_i)$ &rightarrow; $sigma_i^h$ transform into observation space
- $sigma_i^h$ provides evalution at `obs` points
---
### enkf.update(obs) - II.
- For most scenarios $h(x) \sub f(x)$
- Evalute `enkf.K` via $F(sigma_h, sigma, x)$
- $sigma_i = sigma_i + K * |obs - sigma_h|$
- `enkf.sigmas` provides $N$ updated states





    

