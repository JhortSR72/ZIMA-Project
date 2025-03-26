# ZIMA-Project
ZIMA Blue project (Pre-Alpha)

import time #minutes/hours that the ZIMA will be up and running
import RPi.GPIO as GPIO #hardware modules
import random #variable bytes generator #(data is still unrefer)

#setup
GPIO.setmode(GPIO.BCM)

# Motor Pins (example)
MOTOR_LEFT_PIN = 17
MOTOR_RIGHT_PIN = 18
TRIGGER_PIN = 23  #for ultrasonic sensor
ECHO_PIN = 24     #for ultrasonic sensor

#setup GPIO pins
GPIO.setup(MOTOR_LEFT_PIN, GPIO.OUT)
GPIO.setup(MOTOR_RIGHT_PIN, GPIO.OUT)
GPIO.setup(TRIGGER_PIN, GPIO.OUT)
GPIO.setup(ECHO_PIN, GPIO.IN)

#initialize motors
left_motor = GPIO.PWM(MOTOR_LEFT_PIN, 100)  #100Hz PWM frequency
right_motor = GPIO.PWM(MOTOR_RIGHT_PIN, 100)
left_motor.start(0)
right_motor.start(0)

#function to measure distance using ultrasonic sensor
def get_distance():
    GPIO.output(TRIGGER_PIN, GPIO.HIGH)
    time.sleep(0.00001)
    GPIO.output(TRIGGER_PIN, GPIO.LOW)

    while GPIO.input(ECHO_PIN) == GPIO.LOW:
        pulse_start = time.time()

    while GPIO.input(ECHO_PIN) == GPIO.HIGH:
        pulse_end = time.time()

    pulse_duration = pulse_end - pulse_start
    distance = pulse_duration * 17150  #distance in cm
    return distance

# Movement Functions
def move_forward():
    left_motor.ChangeDutyCycle(50)  #50% speed
    right_motor.ChangeDutyCycle(50)  #50% speed

def stop():
    left_motor.ChangeDutyCycle(0)
    right_motor.ChangeDutyCycle(0)

def turn_left():
    left_motor.ChangeDutyCycle(0)
    right_motor.ChangeDutyCycle(50)

def turn_right():
    left_motor.ChangeDutyCycle(50)
    right_motor.ChangeDutyCycle(0)

# Main Loop for autonomous cleaning
def clean_pool():
    while True:
        distance = get_distance()
        print(f"Distance: {distance} cm")

        # Avoid obstacles
        if distance < 30:  #if an obstacle is detected within 30 cm
            stop()
            time.sleep(1)
            turn_left()
            time.sleep(1)
            move_forward()
        else:
            move_forward()

        time.sleep(0.1)

try:
    clean_pool()
except KeyboardInterrupt:
    GPIO.cleanup()

    #CODE SUBJECT TO CHANGE

