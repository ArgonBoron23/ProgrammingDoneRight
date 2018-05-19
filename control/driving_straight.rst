================
Driving Straight
================

A common problem in FRC is driving straight, in autonomous mode or otherwise. This article describes two ways to accomplish this using either encoders or a gyro.
Recommended prerequisite reading is `PID Control. <pid_control.html>`_

Using a Gyro
============
See the `Gyro <gyro.html>`_ article for gyro basics.
A gyro automatically corrects your turn as you drive. A simple way to accomplish this is using a P loop in your drive routine.::

    function drive_straight_gyro(power):
        error = -gyroAngle  # Our target angle is zero
        turn_power = kP * error
        drive.arcadeDrive(power, turn_power, squaredInputs=False)

This works well without much oscillation at speed because most of the nonlinearities in a drivetrain are taken up by the main drive power.
However, at low speed, small P values may not correct as well.
This can be fixed by "gain scheduling", which is using different values of kP/kI/kD for different situations, or by adding terms to the P loop.

Note that this example is similar to the Gyro article's rotate to angle function but with the target angle set to 0. 
Depending on the robot, field layout, direction of travel, drift, and more, your gyro may not be zero when you want it to be.
A good idea would be to zero the gyro reading before starting the drive straight routine.

Here we use the DifferentialDrive arcadeDrive function because it does the straight/rotate math for us.
We also pass an argument in telling it not to square the inputs. This is so the output is linear.

Using Encoders
==============
How this works conceptually is by using a PID loop to drive the *difference* between the left and right side encoders to zero.::

    function drive_straight_enc(power):
        error = left_encoder - right_encoder
        turn_power = kP * error
        drive.arcadeDrive(power, turn_power, squaredInputs=False)


Make note of the sign of `error` and make sure that it reacts in the same direction as your turn direction, otherwise you will continually turn.

Again, this works well at high speeds, but may have difficulty correcting small errors at low speed. 
Also similarly to the gyro, the difference may start at something that's not zero, so it's a good idea to zero both those before starting the drive routine.

What to use?
============
Both these approaches work well. 
A gyro is a fantastic sensor for angle measurement, especially over short terms. 
As turning to a specific angle is an almost-as-if-not-more-common problem than driving straight, the same sensor can easily double up for both tasks.

Encoders are less useful for turning to an angle (though technically usable), so a gyro is the recommended approach. 
However, again, encoders are acceptable if you do not have a gyro on your robot for whatever reason.

Both of these approaches can be combined with other drive methods, such as driving to a distance or using PID for drive distance.