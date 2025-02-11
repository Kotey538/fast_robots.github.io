---
title: "MAE 4910 Fast Robots"
description: "Lab 1: The Artemis board and Bluetooth"
layout: default
---

# Lab 1: The Artemis board and Bluetooth


# Lab 1A

## Prelab



## Task 1: Blink

## Task 2: Serial Monitor

## Task 3: Temperature Sensor Test

<div style="border: 1px solid #ccc; padding: 10px; background-color: #f9f9f9; margin-top: 20px;">
  Here is the output from the code:
  <pre>
Hello, World!
This is the code output.
  </pre>
</div>

## Task 4: Microphone Test

## Reflection

# Part 1b

## Prelab


## Lab Tasks

### Step 1: ECHO command

### Step 2: SEND_THREE_FLOATS command

### Step 3: GET_TIME_MILLIS command

### Step 4: Setup Notification Handler

### Step 5: Get Time Using Notification Handler


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

### Step 8: Compare Speed of Sending Individual Time Values (Step 5) vs Sending Time Arrays (Step 6)



## Reflection
This experiment helped me understand...
