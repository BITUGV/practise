一、创建workspace
1.mkdir -p ~/catkin_ws/src
  cd ~/catkin_ws/src
  catkin_init_workspace
2.编译
  cd ~/catkin_ws
  catkin_make
3.注册
  source devel/setup.bash

二、创建package
  cd ~/catkin_ws/src
  catkin_create_pkg beginner <依赖包>(std_msgs roscpp rospy)
  (cd ~/catkin_ws
  catkin_make)
三、发布、订阅 程序
  beginner/src下创建talker.cpp和listener.cpp
  写入代码
  示例程序

    #include "ros/ros.h"
    #include "std_msgs/String.h"
    #include <sstream>
    int main(int argc,char **argv)
    {
      ros::init(argc,argv,"talker");
      ros::NodeHandle n;
      ros::Publisher chatter_pub = n.advertise<std_msgs::String>("chatter",1000);
      ros::Rate loop_rate(10);
      int count=0;
      while (ros::ok())  
      {  
        std_msgs::String msg;
        std::stringstream ss;
        ss<<"hello world"<<count;
        msg.data = ss.str();
        ROS_INFO("%s", msg.data.c_str());
        chatter_pub.publish(msg);
        ros::spinOnce();
        loop_rate.sleep();  
        ++count;  
      }
      return 0;  
    }

#include "ros/ros.h"
#include "std_msgs/String.h"
 void chatterCallback(const std_msgs::String::ConstPtr& msg)
{
  ROS_INFO("I heard: [%s]", msg->data.c_str());
}
int main(int argc,char **argv)
{
  ros::init(argc,argv, "listener");
  ros::NodeHandle n;
  ros::Subscriber sub = n.subscribe("chatter",1000, chatterCallback);
 ros::spin();
  return 0;
}

  在Cmakelist.txt中添加
    include_directories(include ${catkin_INCLUDE_DIRS})

    add_executable(talker src/talker.cpp)
    target_link_libraries(talker ${catkin_LIBRARIES})
    add_dependencies(talker beginner_generate_messages_cpp)
      
    add_executable(listener src/listener.cpp)
    target_link_libraries(listener ${catkin_LIBRARIES})
    add_dependencies(listener beginner_generate_messages_cpp)

  cd ~/catkin_ws
  catkin_make
  (显示Built target listener
       Built target talker)

四、启动
 终端1
  cd ~/catkin_ws
  roscore
 终端2
  cd ~/catkin_ws
  source devel/setup.bash
  rosrun beginner talker
 终端3
  cd ~/catkin_ws
  source devel/setup.bash
  rosrun beginner listener
