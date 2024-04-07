import machine
import utime
import dht

DATA_PIN = machine.Pin(13, machine.Pin.IN, machine.Pin.PULL_UP)

sensor = dht.DHT22(DATA_PIN)

HUMIDITY_HIGH_THRESHOLD = 70
HUMIDITY_LOW_THRESHOLD = 40
TEMPERATURE_LOW_THRESHOLD = 20

LOG_FILE = "sensor_log.txt"

def start_watering(low_speed):
    try:
        print("Watering started at {} speed...".format("low" if low_speed else "high"))
    except Exception as e:
        print("Error while starting watering:", e)

def stop_watering():
    try:
        print("Watering stopped.")
    except Exception as e:
        print("Error while stopping watering:", e)

def read_and_display_data():
    try:
        sensor.measure()
        temp = sensor.temperature()
        humidity = sensor.humidity()
        print("Temperature: {:.2f}°C, Humidity: {:.2f}%".format(temp, humidity))
        log_sensor_data(temp, humidity)
        control_watering(temp, humidity)
    except Exception as e:
        print("Error reading sensor data:", e)

def log_sensor_data(temperature, humidity):
    try:
        with open(LOG_FILE, "a") as f:
            f.write("Temperature: {:.2f}°C, Humidity: {:.2f}%\n".format(temperature, humidity))
    except Exception as e:
        print("Error logging sensor data:", e)

def control_watering(temperature, humidity):
    try:
        if humidity > HUMIDITY_HIGH_THRESHOLD:
            stop_watering()
        elif humidity < HUMIDITY_LOW_THRESHOLD and temperature > TEMPERATURE_LOW_THRESHOLD:
            start_watering(low_speed=False)
        elif temperature < TEMPERATURE_LOW_THRESHOLD:
            start_watering(low_speed=True)
        else:
            stop_watering()
    except Exception as e:
        print("Error controlling watering:", e)

while True:
    read_and_display_data()
    utime.sleep(1)
