# ROS 1 via ROS 2 Bridge (User Guide)

:::warning
**This example is out of date!** It relies on the [PX4-Fast RTPS(DDS) Bridge](/middleware/micrortps.md), which is no longer supported. We plan to retest and update it for the [XRCE-DDS (PX4-ROS 2/DDS Bridge)](../middleware/xrce_dds.md) in the near future.
:::

This topic explains how use ROS 1 with PX4, by bridging via [ROS 2](../ros/ros2.md).

It provides an overview of the ROS 1-ROS 2-PX4 architecture, along with instructions on how to install all the needed software and build ROS 1 applications. 또한, ROS 2 및 ROS 1 작업 공간을 동시에 설정하는 방법도 설명합니다.

:::note
Generally you might use this setup rather than bridging [ROS 1 with MAVROS](../ros/ros1.md) if you want deeper access to PX4 than granted by MAVLink, or if you want to use both ROS 2 and ROS 1 applications.
:::

:::note
이 설정과 이 지침은 [ROS 2](../ros/ros2.md)에 *의존*합니다. 먼저 ROS 2를 읽어보는 것이 좋습니다.
:::

:::warning
Note PX4 개발 팀은 모든 사용자가 [ROS 2로 업그레이드](../ros/ros2.md)할 것을 권장합니다.
:::

## 개요

The application pipeline for ROS 1 bridged over ROS 2 is shown below.

![ROS를 사용한 아키텍처](../../assets/middleware/micrortps/architecture_ros.png)

두 버전 간에 메시지를 번역하는 추가 [`ros1_bridge`](https://github.com/ros2/ros1_bridge) 패키지(Open Robotics 제공)가 있다는 점을 제외하면 기본적으로 ROS 2와 동일합니다. 이것은 ROS의 원래 버전이 RTPS를 지원하지 않기 때문에 필요합니다.

다른 주요 차이점은 `px4_ros_com` 및 `px4_msgs`가 별도의 `ros1` 분기를 패키징한다는 것입니다. `ros1_bridge`와 **함께** 사용하기 위한 ROS 메시지 헤더와 소스 파일을 생성합니다. 이 분기에는 예제 리스너 및 광고주 노드도 포함됩니다.


## 설치 및 설정

[ROS 2 사용자 가이드 > 설치 및 설정](../ros/ros2_comm.md#installation-setup)을 참고하여 ROS 2를 설치합니다.

### Build ROS 1 Workspace

ROS는 ROS2와 다른 환경이 필요하기 때문에 별도의 작업 공간을 만들어야 합니다. 여기에는 `ros1_bridge`와 함께 `px4_ros_com`와 `px4_msgs`의 `ros` 분기가 포함됩니다.

작업 공간을 만들고 빌드합니다.

1. ROS 1 작업 공간 디렉토리를 생성합니다.
   ```sh
   $ mkdir -p ~/px4_ros_com_ros1/src
   ```
1. ROS 1 브리지 패키지 `px4_ros_com`와 `px4_msgs`를 `/src` 디렉토리(`ros1` 분기)에 복제합니다.
   ```sh
   $ git clone https://github.com/PX4/px4_ros_com.git ~/px4_ros_com_ros1/src/px4_ros_com -b ros1 # clones the 'ros1' branch
   $ git clone https://github.com/PX4/px4_msgs.git ~/px4_ros_com_ros1/src/px4_msgs -b ros1
   ```
1. `build_ros1_bridge.bash` 스크립트를 사용하여 ROS 작업 공간(`px4_ros_com`, `px4_msgs` 및 `ros1_bridge` 포함)을 빌드합니다.
   <!-- we didn't clone `ros1_bridge` ? -->
   ```sh
   $ git checkout ros1
   $ cd scripts
   $ source build_ros1_bridge.bash
   ```
:::tip
You can also build both ROS 1 and ROS 2 workspaces with a single script: `build_all.bash`. The most common way of using it, is by passing the ROS 1 workspace directory path and PX4 Autopilot directory path:
   ```sh
   $ source build_all.bash --ros1_ws_dir <path/to/px4_ros_com_ros1/ws>
   ```

### 설치 상태 확인

[ROS 2 사용자 가이드 > 설치 상태 확인](../ros/ros2_comm.md#sanity-check-the-installation) 설치를 확인하는 방법은 브리지가 PX4 시뮬레이터에서 PX4와 통신 여부를 테스트하는 것입니다.

To use ROS 1 **and** ROS 2 (you need both for this!):

1. [PX4 Ubuntu Linux 개발 환경 설정](../dev_setup/dev_env_linux_ubuntu.md) - 기본 지침은 최신 버전의 PX4 소스를 다운로드하고 필요한 도구들을 설치합니다.
1. Open a new terminal in the root of the **PX4 Autopilot** project, and then start a PX4 [Gazebo Classic](../sim_gazebo_classic/README.md) simulation using:

   ```sh
   make px4_sitl_rtps gazebo-classic
   ```

   PX4가 시작되면, 터미널에 [NuttShell/System Console](../debug/system_console.md)이 표시됩니다.

1. 다른 터미널에서 ROS 2 환경과 작업 공간을 소싱하고 `ros1_bridge`를 시작합니다(이렇게 하면 ROS 2와 ROS 노드가 서로 통신할 수 있음). 또한, `roscore`가 실행 중이거나 실행될 `ROS_MASTER_URI`를 설정합니다.
   ```sh
   $ source /opt/ros/dashing/setup.bash
   $ source ~/px4_ros_com_ros2/install/local_setup.bash
   $ export ROS_MASTER_URI=http://localhost:11311
   $ ros2 run ros1_bridge dynamic_bridge
   ```

1. 다른 터미널에서 ROS 작업 공간을 소싱하고, `sensor_combined` 리스너 노드를 시작합니다. `roslaunch`를 통해 시작하므로, `roscore`도 자동으로 시작됩니다.
   ```sh
   $ source ~/px4_ros_com_ros1/install/setup.bash
   $ roslaunch px4_ros_com sensor_combined_listener.launch
   ```

1. 다른 터미널에서 ROS 2 작업 공간을 소싱한 다음 UDP를 전송 프로토콜로 사용하여 `micrortps_agent` 데몬을 시작합니다.
   ```sh
   $ source ~/px4_ros_com_ros2/install/setup.bash
   $ micrortps_agent -t UDP
   ```

1. [NuttShell/System Console](../debug/system_console.md)에서 UDP `micrortps_client` 데몬을 시작합니다.
   ```sh
   > micrortps_client start -t UDP
   ```

   브리지가 올바르게 작동하면, ROS 수신기를 시작한 터미널에서 데이터가 인쇄되는 것을 확일할 수 있습니다.
   ```sh
   RECEIVED DATA FROM SENSOR COMBINED
   ================================
   ts: 870938190
   gyro_rad[0]: 0.00341645
   gyro_rad[1]: 0.00626475
   gyro_rad[2]: -0.000515705
   gyro_integral_dt: 4739
   accelerometer_timestamp_relative: 0
   accelerometer_m_s2[0]: -0.273381
   accelerometer_m_s2[1]: 0.0949186
   accelerometer_m_s2[2]: -9.76044
   accelerometer_integral_dt: 4739
   ```

:::note
`build_all.bash` 스크립트를 사용하면, 필요한 모든 터미널이 자동으로 열리고 소싱되므로 각 터미널에서 해당 앱을 실행하기만 하면 됩니다.
:::

## Creating a ROS 1 listener

Since the creation of ROS nodes is a well known and documented process, we are going to leave this section out from this guide, and you can find an example of a ROS listener for `SensorCombined` messages the `ros1` branch of the `px4_ros_com` repository, under the following path `src/listeners/`.

