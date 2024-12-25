Elevator System

# Requirements
The elevator system should consist of multiple elevators serving multiple floors.
Each elevator should have a capacity limit and should not exceed it.
Users should be able to request an elevator from any floor and select a destination floor.
The elevator system should efficiently handle user requests and optimize the movement of elevators to minimize waiting time.
The system should prioritize requests based on the direction of travel and the proximity of the elevators to the requested floor.


# Entity

The diagram describes everything,

noting down the pattern used

State Pattern for the current state of the lift, up / down / stationary / open(loading). This is used to direct the LiftCar entity to move (LiftCar interfaces between the hardware and the direction)
Lift is a combination of a LiftCar entity and the InnerDisplay entity

Lift also the Observer Pattern so the subscriber can subsriber for the alert scenario
Lift also uses the StrategyPattern to describe the algorithm to move, this helps computes the nextFloor to move to / with the help from the button clicked from inner display

the nextFloor can be used to go to the particular floor
this strategy pattern helps define the strategy for each lift you create, different lifts can have different strategy on a floor



the outerdisplay + lift can be added to a floor,

the outerdisplay has a direction enum depending on the button that is clicked, also has the valid direction valid buttons, lowest floor can go down / top most floor can go up. For now, it could be added