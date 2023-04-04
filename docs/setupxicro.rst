Setup XICRO
===========

Generated Contents
******************
XICRO allows user to generate 2 things.
    1.) ROS2-interface firmware library for the desired MCU
    2.) Python Executable for interfacing the desired MCU with ROS2 network 
    
Setup Parameters in the Configuration File
******************************************
To configure proper code generation, you must modify a configuration file called "setup_xicro.yaml", which is located in the config folder of xicro_pkg package under the meta-package. Use your favorite editor to modify the file. (In this case, we use Visual Studio code).

.. code-block:: sh

    cd ~/xxx_ws/src/Xicro/xicro_pkg/config     # cd to floder contain setup_xicro.yaml
    code setup_xicro.yaml

Configuration file: setup_xicro.yaml
************************************

In this configuration file, you can modify the following parameters to match your needs.

microcontroller
      - ### microcontroller
      
    - 1\. idmcu : This sets the ID of the MCU of the generated contents. [0-15] 
    
    - 2\. namespace : This is the name of the file that will be generated, i.e. "imu"

    - 3\. generate_library_Path : This is the location where the generated firmware library will be put in.
    
      XICRO will create a folder with files at this path.

        Path reference from ~/
        
    - 4\. connection
        
        - 1\. type : Support 2 mode 1.)Serial UART 2.)Wifi UDP (Only arduino and esp32)
          
        - 2\. serial_port : Name of the open serial port of the MCU
      
        - 3\. baudrate : This affects the data transmission rate. Users can use <=2000000
      
            \**Recommended at 115200 bit/s.

        - 4\. ip_address_mcu : IP address of mcu in WLAN
          
        - 5\. udp_port_mcu : port of connection UDP mode
        
  - ### ros : setup ros  
           
    - 1\. publisher : Configuration for publishing to a topic from MCU to ROS2.
        
        In format : [ [ID_topic,Name_topic,Interface],[ID_topic_2,Name_topic_2,Interface_2],.......]
        - 1\. ID_topic : This sets the ID of the published topic [0 to 255]
        - 2\. Name_topic : Specify the topic name you want MCU to publish to ROS2.
        - 3\. Interface : variable interface file configuration for use in the topic, which must be in the following format. 

      "(The name of the package that contains the interface file)/(Name_of_fileinterface).msg" 

    - 2\. subscriber : Configuration for subscribing to a topic from ROS2 to MCU.
        
        In format : [ [ID_topic,Name_topic,Interface],[ID_topic_2,Name_topic_2,Interface_2],.......]
  
        - 1\. ID_topic : It sets the ID of the subscribed topic [0 to 255].
        - 2\. Name_topic : Specify the topic name you want MCU subscribe from ROS2.
        - 3\. Interface : variable interfacefile configuration for use in the topic, which must be in the following format. 
        
        "(The name of the package that contains the interface file)/(Name_of_fileinterface).msg" 

    - 3\. srv_client : Configuration for service client. 
        
        In format : [ [ID_service,Name_service,Interface,time_out],[ID_service_2,Name_service_2,Interface_2,time_out_2],.......]

      - 1\. ID_service : It sets the ID of the service_client [0 to 255]
      - 2\. Name_service : Specify the servive name you want MCU service call to ROS2.
      - 3\. Interface : variable interfacefile configuration for use in the service, which must be in the following format. 
        
        "(The name of the package that contains the interface file)/(Name_of_fileinterface).srv"
        
      - 4\. time_out : Limit the maximum service usage time. (In type float)

    - 4\. srv_server : Configuration for service server. 
        In format : [ [ID_service,Name_service,Interface,time_out],[ID_service_2,Name_service_2,Interface_2,time_out_2],.......]

      - 1\. ID_service : It sets the ID of the service_server [0 to 255].
      - 2\. Name_service : Specify the servive name you want  ROS2 service call to MCU.
      - 3\. Interface : variable interfacefile configuration for use in the service, which must be in the following format. 
        
        "(The name of the package that contains the interface file)/(Name_of_fileinterface).srv"
      - 4\. time_out : Limit the maximum service usage time. (In type float)

    - 5\. action_client : Configuration for action client. 
        In format : [ [ID_action,Name_action,Interface,time_out],[ID_action_2,Name_action_2,Interface_2,time_out_2],.......]

      - 1\. ID_action : It sets the ID of the action_client [0,255]
      - 2\. Name_action : Specify the action name you want MCU action send_goal to ROS2.
      - 3\. Interface : variable interfacefile configuration for use in the action, which must be in the following format. 
        
        "(The name of the package that contains the interface file)/(Name_of_fileinterface).action"        
      - 4\. time_out : Limit the maximum action usage time. (In type float)        
      
    - 6\. action_server : Configuration for action server. 
        In format : [ [ID_action,Name_action,Interface,time_out],[ID_action_2,Name_action_2,Interface_2,time_out_2],.......]

      - 1\. ID_action : It sets the ID of the action_server [0 to 255]
      - 2\. Name_action : Specify the action name you want ROS2 action send_goal to MCU.
      - 3\. Interface : variable interfacefile configuration for use in the action, which must be in the following format. 
        
        "(The name of the package that contains the interface file)/(Name_of_fileinterface).action" 
      - 4\. time_out : Limit the maximum action usage time. (In type float)  
      
      
Build your the package to finish setting up the code-generation.

.. code-block:: sh

  cd ~/xxx_ws          # cd to your workspace
  colcon build


Generating Firmware library
***************************

The library will be generated based on setup_xicro.yaml. The firmware library .h , .cpp will be created at $generate_library_Path according to setup_xicro.yaml.

.. code-block:: sh

  cd ~/xxx_ws/src      #cd to your workspace
  colcon build
  ros2 run xicro_pkg generate_library.py -mcu_type -module_name 
  
-mcu_type (require) : Users must specify the family of MCU [arduino , esp , stm32]
      
-module_name : HAL library
    
- If you don't use stm32 family, ignore this argument. 
  
### Example 
ARDUINO

.. code-block:: sh

    ros2 run xicro_pkg generate_library.py -mcu_type arduino  // Example generate for arduino family.
    
ESP

.. code-block:: sh

    ros2 run xicro_pkg generate_library.py -mcu_type esp  // Example generate for esp family.
    
STM32F411RE

.. code-block:: sh

    ros2 run xicro_pkg generate_library.py -mcu_type stm32 -module_name "stm32f4xx_hal.h"  // Example generate for stm32F4xx
    
Generating Python Executable
****************************
The Python executabe will be generated based on "setup_xicro.yaml". The executable will be generated at path ~xxx_ws/src/Xicro/xicro_pkg/scripts.

.. code-block:: sh

    ros2 run xicro_pkg generate_xicro_node.py -mcu_type arduino 
    
-mcu_type (require) : Users must specify the family of MCU [arduino , esp , stm32]

The entry point is automatically added by the command.
    

General Pipeline for using XICRO
********************************

1. Generate the firmware library
2. Generate the Python Executable
3. Write and upload your firmware to the MCU
4. Connect the MCU to the computer
5. Verify the permission for the open port

.. code-block:: sh
    
    sudo chown $USERNAME /port     #Changing permissions port 

6. Execute the previously generated Python executable (run the generated node)

.. code-block:: sh
    
    ros2 run xicro_pkg xicro_xxx_node  
        
Congratulations!!! Now your MCU can connect to ROS2 network.
