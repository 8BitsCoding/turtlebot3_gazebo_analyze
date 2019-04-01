ROS_turtlebot3_gazebo_modeling
===

> 두 모델을 불러온다.
>
> common_properties.xacro, turtlebot3_burger.gazebo.xacro
> 
> common_properties.xacro : 색상 정보
>
> turtlebot3_burger.gazebo.xacro : burger 모델에 들어가는 각종 센서 정보
>
> [센서정보 분석](ROS_turtlebot3_sensor)

---

### 1 단계 : include

```s
$ roslaunch turtlebot3_th turtlebot3_world_01.launch 
```

```xml
<!-- turtlebot3_burger.urdf.xacro 내부 -->
<?xml version="1.0" ?>
<robot name="turtlebot3_burger" xmlns:xacro="http://ros.org/wiki/xacro">
  <xacro:include filename="$(find turtlebot3_description)/urdf/common_properties.xacro"/>
  <xacro:include filename="$(find turtlebot3_description)/urdf/turtlebot3_burger.gazebo.xacro"/>

  <link name="base_footprint"/>
</robot>
```

---

### 2 단계 : 3D 캐드 body include

```s
$ roslaunch turtlebot3_th turtlebot3_world_02.launch 
```

> .stl(3d 캐드) 파일을 로드하여 넣는다. 

![이미지](ROS_turtlebot3_gazebo_modeling_image_01.png)

```xml
<?xml version="1.0" ?>
<robot name="turtlebot3_burger" xmlns:xacro="http://ros.org/wiki/xacro">
	<xacro:include filename="$(find turtlebot3_description)/urdf/common_properties.xacro"/>
	<xacro:include filename="$(find turtlebot3_description)/urdf/turtlebot3_burger.gazebo.xacro"/>

	<link name="base_footprint"/>
		<joint name="base_joint" type="fixed">
			<parent link="base_footprint"/>
			<child link="base_link"/>
			<origin xyz="0.0 0.0 0.010" rpy="0 0 0"/>
		</joint>

	<link name="base_link">
		<visual>
			<origin xyz="-0.032 0 0.0" rpy="0 0 0"/>
			<geometry>
				<mesh filename="package://turtlebot3_description/meshes/bases/burger_base.stl" scale="0.001 0.001 0.001"/>
			</geometry>
			<material name="light_black"/>
		</visual>

		<collision>
			<origin xyz="-0.032 0 0.070" rpy="0 0 0"/>
			<geometry>
				<box size="0.140 0.140 0.143"/>
			</geometry>
		</collision>

        <!-- 관성모멘트를 넣는다. -->
		<inertial>
		<origin xyz="0 0 0" rpy="0 0 0"/>
		<mass value="8.2573504e-01"/>
		<inertia ixx="2.2124416e-03" ixy="-1.2294101e-05" ixz="3.4938785e-05"
		       iyy="2.1193702e-03" iyz="-5.0120904e-06"
		       izz="2.0064271e-03" />
		</inertial>
	</link>

</robot>
```

> 조금 수정해보자

```xml
	<link name="base_link">
		<visual>
            <!-- 여기수정 -->
			<origin xyz="-0.032 0 0.0" rpy="0.1 0 0"/>
			<geometry>
				<mesh filename="package://turtlebot3_description/meshes/bases/burger_base.stl" scale="0.001 0.001 0.001"/>
			</geometry>
			<material name="light_black"/>
		</visual>
```

![이미지](ROS_turtlebot3_gazebo_modeling_image_02.png)

---

### 3 단계 : 3D 캐드 wheel include

> 여기서 부터는 추가된 부분만 적는다.

```s
$ roslaunch turtlebot3_th turtlebot3_world_03.launch 
```

![이미지](ROS_turtlebot3_gazebo_modeling_image_03.png)

```xml
	<joint name="wheel_left_joint" type="continuous">
		<parent link="base_link"/>
		<child link="wheel_left_link"/>
		<origin xyz="0.0 0.08 0.023" rpy="-1.57 0 0"/>
		<axis xyz="0 0 1"/>
	</joint>

	<link name="wheel_left_link">
		<visual>
			<origin xyz="0 0 0" rpy="1.57 0 0"/>
			<geometry>
				<mesh filename="package://turtlebot3_description/meshes/wheels/left_tire.stl" scale="0.001 0.001 0.001"/>
			</geometry>
			<material name="dark"/>
		</visual>

		<collision>
			<origin xyz="0 0 0" rpy="0 0 0"/>
			<geometry>
				<cylinder length="0.018" radius="0.033"/>
			</geometry>
		</collision>

		<inertial>
			<origin xyz="0 0 0" />
			<mass value="2.8498940e-02" />
			<inertia ixx="1.1175580e-05" ixy="-4.2369783e-11" ixz="-5.9381719e-09"
			       iyy="1.1192413e-05" iyz="-1.4400107e-11"
			       izz="2.0712558e-05" />
		</inertial>
	</link>
```

---

### 4 단계 : caster(바퀴다리?) 잘 모르겠음;

```s
$ roslaunch turtlebot3_th turtlebot3_world_04.launch 
```

```xml
	<joint name="caster_back_joint" type="fixed">
		<parent link="base_link"/>
		<child link="caster_back_link"/>
		<origin xyz="-0.081 0 -0.004" rpy="-1.57 0 0"/>
	</joint>

	<link name="caster_back_link">
	<collision>
		<origin xyz="0 0.001 0" rpy="0 0 0"/>
		<geometry>
			<box size="0.030 0.009 0.020"/>
		</geometry>
	</collision>

	<inertial>
		<origin xyz="0 0 0" />
		<mass value="0.005" />
		<inertia ixx="0.001" ixy="0.0" ixz="0.0"
		       iyy="0.001" iyz="0.0"
		       izz="0.001" />
		</inertial>
	</link>
```

---

### 5 단계 : imu링크 및 레이더 링크

```s
$ roslaunch turtlebot3_th turtlebot3_world_05.launch 
```

> turtlebot3_burger.gazebo.xacro의 센서정보를 받아와 링크시킨다.

```xml
	<joint name="imu_joint" type="fixed">
		<parent link="base_link"/>
		<child link="imu_link"/>
		<origin xyz="-0.032 0 0.068" rpy="0 0 0"/>
	</joint>

	<link name="imu_link"/>

	<joint name="scan_joint" type="fixed">
		<parent link="base_link"/>
		<child link="base_scan"/>
		<origin xyz="-0.032 0 0.172" rpy="0 0 0"/>
	</joint>

	<link name="base_scan">
	<visual>
		<origin xyz="0 0 0.0" rpy="0 0 0"/>
		<geometry>
			<mesh filename="package://turtlebot3_description/meshes/sensors/lds.stl" scale="0.001 0.001 0.001"/>
		</geometry>
		<material name="dark"/>
	</visual>

	<collision>
		<origin xyz="0.015 0 -0.0065" rpy="0 0 0"/>
		<geometry>
			<cylinder length="0.0315" radius="0.055"/>
		</geometry>
	</collision>

	<inertial>
		<mass value="0.114" />
		<origin xyz="0 0 0" />
		<inertia ixx="0.001" ixy="0.0" ixz="0.0"
		       iyy="0.001" iyz="0.0"
		       izz="0.001" />
		</inertial>
	</link>
```