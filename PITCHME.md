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
## Filterpy EnKF

- very lightweighted implementation
- uses an ensemble of hundreds to thousands of state vectors 
    - samples "randomly" around estimate
    - Monte Carlo algorithm
- adds perturbations at each update and predict step 
---

## Parameters

- __EnsembleKalmanFilter(x, P, dim_z, dt, N, hx, fx)__
    - `x`: state mean
    - `P` : covariance of the state
    - `dim_z` : Number of of measurement inputs
    - ~~`dt` : time step in seconds~~
    - `N` : number of $\sigma$ points (ensembles)
    - `hx` : Measurement function that projects state x into a measurement space
    - `fx` : State transition function 
---
### How to run it
```python
# setup input matrices and transformation functions hx and fx
enkf = EnsembleKalmanFilter(x, P, dim_z, dt, N, hx, fx)

# setup process noise
enkf.Q = ...
# setup measurement noise
enkf.R = ...

# Iter 1
enkf.predict() #responses
enkf.update(observations) #update step

# Iter 2
enkf.predict() #responses
enkf.update(observations) #update step
```
---
### enkf.predict()
- Sample $N$ times normal distribution $(x, P)$ &rightarrow; $\{{\sigma}_i\}$
- State change $\sigma_i\equiv f(\sigma_i)+v_Q$
   - apply state transition function $f$
   - perturbations $v_Q$ defined by process noise covariance $Q$
- `enkf.sigmas` contains $N$ states
- Recompute covariance
$P = \frac{1}{N-1}\sum_{i=1}^N(\sigma_i-\bar{\sigma})(\sigma_i-\bar{\sigma})^T$
---
### enkf.predict()
```python
enkf = EnsembleKalmanFilter(.)

enkf.predict()
enkf.sigmas.shape == (N, len(state))
print('New state covariance is ', enkf.P)
```
---
### enkf.update(obs) - I.
- Ref: _John L Crassidis and John L. Junkins. Optimal Estimation of Dynamic Systems, 2012_
- Apply $h(\sigma_i)$ &rightarrow; $\sigma_i^h$ that transforms states into observation space
   - Include "forward models"
- $\sigma_i^h$ provides evalution at observation points
   - $\sigma^h \equiv N\times len(obs)$ matrix
---
### enkf.update(obs) - II.
- Evaluate two matrices
   - $Cov(\sigma_h, \sigma_h)$ and $Cov(\sigma, \sigma_h)$
   - kalman gain $K = Cov(\sigma_h, \sigma_h) Cov(\sigma, \sigma_h)^{-1}$
- $\sigma = \sigma + K * (obs - sigma_h+v_R)$
- After the update step `enkf.sigmas` contains $N$ updated states






    

