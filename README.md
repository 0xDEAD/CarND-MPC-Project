# CarND-Controls-MPC
Self-Driving Car Engineer Nanodegree Program

This project is a implementation of a Model Predictive Controller.
Goal is to control a car on a racetrack by processing information about the
current state of the car and the track. The given parameters are
* position, speed and heading of the car
* nearby waypoints on the track
Additionally the controller must be able to handle an artifically introduced
latency of 100ms.

## The state model

The controller is based on a kinematic model of the car. It uses 6 state variables
(position ind x and y, heading, velocity, cross-track-error and orientation-error.
As all calculation is done in a car-local coordinate-system, the position and heading
are always set to zero in the initial state. The velocity is directly taken from
the simulator and is used without any modification. Cross-track-error and orientation-
error ar calculated from a polynomium that is fitted to the waypoints.

## Polynomial fitting

Before die polynomium (and the errors) can be calcualted, all waypoints are transferred
from worl-coordinates to a lcoal coordinate-system. The fitted polynomium is always of
3rd degree.

## MPC processing

The controller tries to find an optimal solution how to modify the actuators (speed
and steering angle) so that the model has minimal costs. For that it uses a cost-function
that calculates a "overall" cost for a given state and actuator values, while having
some boundaries for the parameters that actually form the model.

### Cost tuning

Main task of the implementation was to find good parameters for the cost-function.
I tuned those parameters manually while looking at how the car behaves in the simulator.
A good idea I learned in a discussion in the forums was to add additional costs for
the situation where position and speed have a big error, which happens in sharp curves
when the speed is reduced and the car is on the outer side of the road.

## Timestep length and duration

I started with the initial values of N=10 and dT=0.1 from the classroom and that
instntly gave me good results. I also tried to increase the number of steps (N),
but that results in a trajectory beeing very long on high speeds which leads to
problems in slight curves. On the other hand decreasing the dT doesn't improve
the driving-behavior much with a big impact on computation time.

## Handling latency

The controller should be able to handle a latency of 100ms. This worked out of the box
more or less. But without special handling the car makes very late and sharp turns
and cannot handle very high speeds. A good trick was to handle it when filling the
contraints for the cost-function. Shifting the values for the actuators by one position
in the array (which corresponds to 0.1s=100ms) gives the controller enough input
to handle it correctly.
