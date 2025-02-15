# 다중 차량 시뮬레이션

PX4는 다음 시뮬레이터를 사용하여 다중 차량 시뮬레이션을 지원합니다.
- [Multi-Vehicle Sim with Gazebo](../sim_gazebo_gz/multi_vehicle_simulation.md)
- [Multi-Vehicle Sim with Gazebo Classic](../sim_gazebo_classic/multi_vehicle_simulation_gazebo.md) (both with and without ROS)
- [FlightGear 다중 차량 시뮬레이션](../simulation/multi_vehicle_flightgear.md)
- [JMAVSim 다중 차량 시뮬레이션](../simulation/multi_vehicle_jmavsim.md)

시뮬레이터의 선택은 시뮬레이션할 차량, 시뮬레이션 퀄러티, 시뮬레이션 기능, 시뮬레이션 차량 대수에 따라 달라집니다.

- FlightGear는 제일 정확한 시뮬레이터이며, 따라서 제일 무거운 환경입니다. 고 퀄러티 시뮬레이션이 필요하고, 한 번에 너무 많지 않은 차량을 시뮬레이션하는 경우에 적절합니다. 다른 유형의 차량을 동시에 시뮬레이션 하는 경우에도 사용합니다.
- [Gazebo](sim_gazebo_gz/gazebo.md) is less accurate and less heavy-weight and supports many features that aren't available for FlightGear. It supports multiple vehicles simultaneously but, at the time of writing, only one frame (X500 quadcopter) is implemented in PX4. Note, this is the successor of [Gazebo Classic](sim_gazebo_classic/gazebo.md) (below).
- [Gazebo Classic](sim_gazebo_classic/gazebo.md) is less accurate and less heavy-weight and supports many features and vehicles that aren't available for FlightGear. 한 번에 단일 유형의 차량만 시뮬레이션할 수 있지만, FlightGear보다 한 번에 더 많은 차량을 시뮬레이션할 수 있습니다. It can only be used with Ubuntu 20.04 and earlier.
- JMAVSim은 쿼드콥터만 지원하는 초경량 시뮬레이터입니다. 많은 쿼드콥터의 근사치를 시뮬레이션하는 경우에 권장됩니다.
