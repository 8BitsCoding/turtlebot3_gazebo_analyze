ROS_Turtlebot3_gazebo_sim
===

```s
$ roslaunch turtlebot3_gazebo turtlebot3_world.launch 
$ roslaunch turtlebot3_teleop turtlebot3_teleop_key.launch 
$ roslaunch turtlebot3_gazebo turtlebot3_gazebo_rviz.launch 
```

> 하나씩 분석해보도록 하자.

---

```s
$ roslaunch turtlebot3_gazebo turtlebot3_world.launch 
```

> gazebo를 통하여 로봇 모델과 맵을 로딩한다.

---

```s
$ roslaunch turtlebot3_teleop turtlebot3_teleop_key.launch 
```

> 로봇 제어를 위한 커멘드 프로그램 실행

---

```s
$ roslaunch turtlebot3_gazebo turtlebot3_gazebo_rviz.launch 
```

> rviz를 로드하고 로봇 모델을 받아온다.

---

### 내부 분석 1

```s
$ roslaunch turtlebot3_gazebo turtlebot3_world.launch 
```

```xml
<launch>
    <arg name="model" default="$(env TURTLEBOT3_MODEL)" doc="model type [burger, waffle, waffle_pi]"/>
    <arg name="x_pos" default="-2.0"/>
    <arg name="y_pos" default="-0.5"/>
    <arg name="z_pos" default="0.0"/>


    <!-- gazebo로드 - turtlebot3_world.world을 연다 -->
    <include file="$(find gazebo_ros)/launch/empty_world.launch">
        <arg name="world_name" value="$(find turtlebot3_gazebo)/worlds/turtlebot3_world.world"/>
        <arg name="paused" value="false"/>
        <arg name="use_sim_time" value="true"/>
        <arg name="gui" value="true"/>
        <arg name="headless" value="false"/>
        <arg name="debug" value="false"/>
    </include>

    <!-- gazebo에 로봇 모델을 연다. turtlebot3_burger.urdf.xacro -->
    <param name="robot_description" command="$(find xacro)/xacro --inorder $(find turtlebot3_description)/urdf/turtlebot3_$(arg model).urdf.xacro" />

    <node pkg="gazebo_ros" type="spawn_model" name="spawn_urdf"  args="-urdf -model turtlebot3_$(arg model) -x $(arg x_pos) -y $(arg y_pos) -z $(arg z_pos) -param robot_description" />
</launch>
```

#### (중요) turtlebot3_burger.urdf.xacro에 관해서

> 로봇 모델을 불러오는 부분이라 중요하다. 
>
> 어떻게 작성되는지 분석이 필욧!

> 아래와 같은데 ... 코드로 보면 이해가 어렵다. 역시 가장 좋은 방법은 따라 코딩하기...
>
> [여기](ROS_turtlebot3_gazebo_modeling)서 해보았다.

---

### 내부 분석 2

```s
$ roslaunch turtlebot3_gazebo turtlebot3_gazebo_rviz.launch 
```

```xml
<launch>
    <arg name="model" default="$(env TURTLEBOT3_MODEL)" doc="model type [burger, waffle, waffle_pi]"/>

    <include file="$(find turtlebot3_bringup)/launch/turtlebot3_remote.launch">
        <arg name="model" value="$(arg model)"/>
    </include>

    <node name="rviz" pkg="rviz" type="rviz" args="-d $(find turtlebot3_gazebo)/rviz/turtlebot3_gazebo_model.rviz"/>
</launch>
```

```xml
<launch>
    <arg name="model" default="$(env TURTLEBOT3_MODEL)" doc="model type [burger, waffle, waffle_pi]"/>
    <arg name="multi_robot_name" default=""/>

    <include file="$(find turtlebot3_bringup)/launch/includes/description.launch.xml">
        <arg name="model" value="$(arg model)" />
    </include>

    <node pkg="robot_state_publisher" type="robot_state_publisher" name="robot_state_publisher">
        <param name="publish_frequency" type="double" value="50.0" />
        <param name="tf_prefix" value="$(arg multi_robot_name)"/>
    </node>
</launch>
```

```xml
<launch>
    <arg name="model"/>
    <arg name="urdf_file" default="$(find xacro)/xacro --inorder '$(find turtlebot3_description)/urdf/turtlebot3_$(arg model).urdf.xacro'" />
    <param name="robot_description" command="$(arg urdf_file)" />
</launch>
```