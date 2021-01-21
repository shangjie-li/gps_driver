# novatel

## 参数配置
 - 修改`novatel/launch/novatel.launch`
   ```Shell
   <param name="port" value="/dev/ttyS1" />
   
   <param name="inspvax_topic" value="/gps"/>
   <param name="ll2utm_topic" value="/ll2utm_odom"/>
   
   <param name="location_original_x" value="-0.909"/>
   <param name="location_original_y" value="-0.005"/>
   <param name="location_expected_x" value="0.0"/>
   <param name="location_expected_y" value="0.0"/>
   ```
    - `inspvax_topic`指明发布的经纬度话题。
    - `ll2utm_topic`指明发布的ENU全局坐标话题。
    - `location_original_x` `location_original_y`为原始GPS定位点在车辆局部坐标系的坐标。
    - `location_expected_x` `location_expected_y`为期望GPS定位点在车辆局部坐标系的坐标。
    - ENU(东北天)全局坐标系的定义：X轴指向正东，Y轴指向正北，单位米。
    - 车辆局部坐标系的定义：在水平面投影图中，车辆轮廓的几何中心为坐标原点，X轴指向车辆前方，Y轴指向车辆左方，单位米。
 - 测量`location_original_x` `location_original_y`
   ```Shell
   # 1.使车头朝东，即yaw_ENU=0度，停入某东西方向的车位，记录ENU全局坐标(utm_x1,utm_y1)
   # 2.使车头朝西，即yaw_ENU=180度，再次停入该车位，记录ENU全局坐标(utm_x2,utm_y2)
   # 3.location_original_x = (utm_x1 - utm_x2)/2
   # 4.location_original_y = (utm_y1 - utm_y2)/2
 - `nav_msgs::Odometry`
   ```Shell 
   pose.pose.position                              utm坐标
   pose.pose.orientation                           gps姿态，ENU(东北天)坐标系
   twist.twist.linear                              gps速度
   # 四元数为gps姿态，航向为gps的x轴与大地东向夹角，逆时针为正
   pose.covariance[0] = deg2rad(inspvax.azimuth);  gps航向角(rad)(n系) 北东地坐标系
   pose.covariance[1] = inspvax.longitude;         经度(deg)
   pose.covariance[2] = inspvax.latitude;          纬度(deg)
   pose.covariance[3] = yaw;                       航向角(rad) 转换后姿态航向
   pose.covariance[4] = roll;                      横滚角
   pose.covariance[5] = pitch;                     俯仰角
   pose.covariance[6] = inspvax.north_velocity;    北向速度
   pose.covariance[7] = inspvax.east_velocity;     东向速度
   ```
