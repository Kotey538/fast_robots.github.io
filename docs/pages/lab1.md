---
title: "MAE 4910 Fast Robots"
description: "Lab 1: The Artemis board and Bluetooth"
layout: default
---

# Lab 1: The Artemis board and Bluetooth
In this part of the lab, we ensured that the Arduino IDE was installed and updated to the latest version. We tested several of the onboard components, including the LED, temperature sensor, and microphone, to verify their functionality. Additionally, we confirmed the board's ability to communicate with the serial monitor by running basic examples and checking for successful output.

# Lab 1A

## Prelab



## Task 1: Blink

## Task 2: Serial Monitor

## Task 3: Temperature Sensor Test
```c
Serial.printf("temp (counts): %d, vcc/3 (counts): %d, vss (counts): %d, time (ms) %d\n", temp_raw, vcc_3, vss, millis());
```

```c
Serial.println(temp_f,2);
```

## Task 4: Microphone Test

## Reflection

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
