Writing the Code for a PIDSubsystem
===================================

.. important:: RobotBuilder has been updated to support the new commandbased framework! Unfortunately, this documentation is outdated and only for the old commandbased framework. Individuals interested in updating this documentation can open a pull request on the `frc-docs <https://github.com/wpilibsuite/frc-docs>`__ repository.

PIDSubsystems use feedback to control the actuator and drive it to a particular position. In this example we use an elevator with a 10-turn potentiometer connected to it to give feedback on the height. The skeleton of the PIDSubsystem is generated by the RobotBuilder and we have to fill in the rest of the code to provide the potentiometer value and drive the motor with the output of the embedded PIDController.

Setting the PID Constants
-------------------------

Make sure the Elevator PID subsystem has been created in the RobotBuilder. Once it's all set, generate Java/C++ code for the project using the Export menu or the Java/C++ toolbar menu.

Add Constants for Elevator Preset Positions
-------------------------------------------

.. tabs::

   .. code-tab:: java

      public class Elevator extends PIDSubsystem {
         // BEGIN AUTOGENERATED CODE, SOURCE=ROBOTBUILDER ID=DECLARATION
         AnalogChannel potentiometer = RobotMap.ELEVATOR_POTENTIOMETER;
         Victor victor = RobotMap.ELEVATOR_VICTOR;
         // END AUTOGENERATED CODE, SOURCE=ROBOTBUILDER ID=DECLARATION

         public static final double BOTTOM = 4.6,
                                    STOW = 1.65,
                                    TABLE_HEIGHT = 1.58;

         public Elevator() {
            // BEGIN AUTOGENERATED CODE, SOURCE=ROBOTBUILDER, ID=PID
            super("Elevator", 1.0, 0.0, 0.0);
            getPIDController().setContinuous(false);
            // END AUTOGENERATED CODE, SOURCE=ROBOTBUILDER, ID=PID

            setSetpoint(STOW);
            enable();
         }

         public void initDefaultCommand() {

         }

         protected double returnPIDInput() {
            // BEGIN AUTOGENERATED CODE, SOURCE=ROBOTBUILDER, ID=SOURCE
            return potentiometer.pidGet();
            // END AUTOGENERATED CODE, SOURCE=ROBOTBUILDER, ID=SOURCE
         }

         protected void usePIDOutput(double output) {

         }
      }

   .. code-tab:: cpp

      #include "Elevator.h"
      #include "../Robotmap.h"
      #include "SmartDashboard/SmartDashboard.h"

      // BEGIN AUTOGENERATED CODE, SOURCE=ROBOTBUILDER ID=PID
      Elevator::Elevator() : PIDSubsystem("Elevator", 1.0, 0.0, 0.0) {
         GetPIDController()->SetContinuous(false);

         // END AUTOGENERATED COD, SOURCE=ROBOTBUILDER ID=PID
         // BEGIN AUTOGENERATED CODE, SOURCE=ROBOTBUILDER, ID=DECLARATIONS

         potentiometer = RobotMap::ELEVATOR_POTENTIOMETER;
         victor = RobotMap::ELEVATOR_VICTOR;

         // END AUTOGENERATED CODE, SOURCE=ROBOTBUILDER, ID=DECLARATIONS
         SetSetpoint(STOW);
         Enable();
      }

To make it easier to drive the elevator to preset positions, we added preset positions for the bottom, stow, and table height. Then the elevator is set to the STOW position by setting the PID setpoint and the PID controller is enabled. This will cause the elevator to move to the stowed position when the robot is enabled.

Adding the Constants to the Header File (C++ Only)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. tabs::

   .. code-tab:: cpp

      #pragma once

      #include "frc/commands/PIDSubsystem.h"
      #include "frc/AnalogInput.h"
      #include "frc/Victor.h"

      class Elevator: public PIDSubsystem {
       public:
         static const double BOTTOM = 4.6;
         static const double STOW = 1.65;
         static const double TABLE_HEIGHT = 1.58;

         // BEGIN AUTOGENERATED CODE, SOURCE=ROBOTBUILDER ID=DECLARATIONS
         AnalogInput* potentiometer;
         Victor* victor;
         // END AUTOGENERATED CODE, SOURCE=ROBOTBUILDER ID=DECLARATIONS

         Elevator();
         double ReturnPIDInput();
         void UsePIDOutput(double output);
         void InitDefaultCommand();
      };


Return PID Input Values
-----------------------

.. tabs::

   .. code-tab:: java

      protected double returnPIDInput() {
         // BEGIN AUTOGENERATED CODE, SOURCE=ROBOTBUILDER, ID=SOURCE
         return potentiometer.pidGet();
         // END AUTOGENERATED CODE, SOURCE=ROBOTBUILDER, ID=SOURCE
      }

   .. code-tab:: cpp

      double Elevator::ReturnPIDInput() {
         // BEGIN AUTOGENERATED CODE, SOURCE=ROBOTBUILDER ID=SOURCE
         return potentiometer->PIDGet();
         // END AUTOGENERATED CODE, SOURCE=ROBOTBUILDER ID=SOURCE
      }

The ``returnPIDInput()`` method is used to set the value of the sensor that is providing the feedback for the PID controller. In this case, the code is automatically generated and returns the potentiometer raw analog input value (a number that ranges from 0-1023). In our case we would like the PID controller to be based on the average voltage read by the analog input for the potentiometer, not the raw value.

If we just change the line:

``return potentiometer.pidGet();`` for Java or ``return potentiometer->PIDGet();`` for C++

it will be overwritten by RobotBuilder next time we export to Java. You can tell which lines are automatically generated by looking at the "//BEGIN AUTOGENERATED CODE" and "//END AUTOGENERATED CODE" comments. Any code in-between those markers will be overwritten next time RobotBuilder is run. You're free to change anything outside of those blocks.

Return the Average Voltage
--------------------------

.. tabs::

   .. code-tab:: java

      protected double returnPIDInput() {
         return potentiometer.getAverageVoltage();
      }

   .. code-tab:: cpp

      double Elevator::ReturnPIDInput() {
         return potentiometer->GetAverageVoltage();
      }

To get around the problem from the last step, the comment blocks can be removed. Then if the line is changed as shown, it will no longer be overwritten by RobotBuilder.

Remember, if we just wanted to add code to a method it could be added safely outside of the comment blocks.

That's all that is required to create the Elevator PIDSubsystem.
