---
title: "MAE 4910 Fast Robots"
description: "Lab 1: The Artemis board and Bluetooth"
layout: default
---

# Lab 1: The Artemis board and Bluetooth
In this lab, I set up and tested the SparkFun RedBoard Artemis Nano, using the Arduino IDE to program the board. I tested various onboard components such as the LED, temperature sensor, and microphone. I also explored Bluetooth connectivity for communication between the board and a computer, using Jupyter notebooks to send and receive data.

* * *

# Lab 1A
In this part of the lab, I ensured that the Arduino IDE was installed and updated to the latest version and tested several of the onboard components verify their functionality. Additionally, I confirmed the board's ability to communicate with the serial monitor.

## Prelab
I had already installed the Arduino IDE from a previous course, so I simply ensured it was updated to the latest version. I also installed the SparkFun Apollo3 board manager. However, I encountered some connection issues with the board and resolved them by updating the CH340 driver.


## Task 1: Blink
Following the lab manual, I tested the Blink found in File->Examples->01.Basics. This program allows for one the to test functionality of the onboard LED by toggling it on and off at one-second intervals.
<div style="display: flex; justify-content: center; align-items: center; height: 100%;">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/KpyS8cVwcT8" title="Fast Robots Lab 1 Task 1: Blink" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
</div>
<br>

## Task 2: Serial Monitor
Next, I tested the Example4_Serial sketch found under File -> Examples -> Apollo3. This program allows the user to input a message to the board, which then echoes the message back. This test ensures the proper functionality of the serial monitor and communication between the board and the computer.

![image](../images/lab1/Serial.PNG)


## Task 3: Temperature Sensor Test
I then tested the Example2_analogRead sketch found under File -> Examples -> Apollo3. This example uses the microcontroller’s internal ADC channels to measure various parameters, including the internal die temperature, and prints the sensor data to the serial monitor.

![image](../images/lab1/Serial_Temp.PNG)

However, there are a few issues with the output of Example4_Serial. The serial monitor displays the temp_raw values, which are raw ADC readings from the microcontroller, so these values are not easily interpretable as temperature. Additionally, the internal VCC and VSS voltages are displayed, which were not necessary since the focus is on the temperature data. As a result, I modified the code to output only the temperature in Fahrenheit to the serial monitor.

Original:
```c
Serial.printf("temp (counts): %d, vcc/3 (counts): %d, vss (counts): %d, time (ms) %d\n", temp_raw, vcc_3, vss, millis());
```

Modified:
```c
Serial.println(temp_f,2);
```

## Task 4: Microphone Test
Finally, I tested the Example1_MicrophoneOutput sketch found under File -> Examples -> PDM. This sketch collects audio data using the PDM microphone on the board, performs an FFT to identify the loudest frequency, and then displays that frequency on the serial monitor. I used my laptop to output various frequencies to test the microphone.
<div style="display: flex; justify-content: center; align-items: center; height: 100%;">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/A70o280L-O4" title="Fast Robots Lab 2 Task 4: Microphone Test" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
</div>
<br>

## Discussion
After this lab, I familiarized myself with using some of the board's components, such as the LED, which I’ve used on other microcontrollers, and the microphone, which was new to me. I also revisited using Serial.print to the serial monitor, a skill that will be useful in future labs.
* * *

# Lab 1B

## Prelab

## Task 1: ECHO command

## Task 2: SEND_THREE_FLOATS command

## Task 3: GET_TIME_MILLIS command
```c
Serial.printf("temp (counts): %d, vcc/3 (counts): %d, vss (counts): %d, time (ms) %d\n", temp_raw, vcc_3, vss, millis());
```

```c
Serial.println(temp_f,2);
```

## Task 4: Setup Notification Handler


### Step 5: Looping GET_TIME_MILLIS command
```c
case GET_TIME_MILLIS: 
    int time;
    time = (int)millis();
    tx_estring_value.clear();
    tx_estring_value.append("T:");
    tx_estring_value.append(time);
    tx_characteristic_string.writeValue(tx_estring_value.c_str());

    Serial.print("Sent back: ");
    Serial.println(tx_estring_value.c_str());

    break;
```

### Step 6: Get Time Array Using Notification Handler
```c
case SEND_TIME_DATA: {
    memset(time_data, 0, sizeof(time_data));
    int i = 0;

    unsigned long start_time = millis(); 
    while ((millis() - start_time < 10) && (i < array_size)) {
        
        time_data[i] = (int) millis();
        i++;
    }

    //Send back the array
    for (int j = 0; j < array_size; j++) {

      if (time_data[j] != 0) {

        tx_estring_value.clear();
        sprintf(tx_estring_value.char_array, "T%d:%d", j, time_data[j]);
        tx_characteristic_string.writeValue(tx_estring_value.c_str());

      } else break;

    }

    break;
}
```

### Step 7: Get Concurrent Temperature & Time Arrays using Notification Handler
```c
case GET_TEMP_READINGS: {
memset(time_data, 0, sizeof(time_data));
memset(temp_data, 0, sizeof(temp_data));
int i = 0;

unsigned long start_time = millis(); 
while ((millis() - start_time < 50) && (i < array_size)) {
    
    time_data[i] = (int) millis();
    temp_data[i] =  (float) getTempDegF();
    i++;
}

//Send back the array
for (int j = 0; j < array_size; j++) {

  if (time_data[j] != 0 || temp_data[j] != 0.0) {

    tx_estring_value.clear();
    sprintf(tx_estring_value.char_array, "T%d:%d, Temp:", j, time_data[j], temp_data[j]);
    tx_estring_value.append(temp_data[j]);
    tx_characteristic_string.writeValue(tx_estring_value.c_str());

  } else break;

}

break;
}
```
### Step 8: Compare Speed of Sending Individual Time Values (Step 5) vs Sending Time Arrays (Step 6)



## Reflection
This experiment helped me understand...
