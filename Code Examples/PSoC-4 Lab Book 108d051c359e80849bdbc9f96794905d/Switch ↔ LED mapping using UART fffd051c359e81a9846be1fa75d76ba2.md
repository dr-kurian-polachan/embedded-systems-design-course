# Switch ↔ LED mapping using UART

> Use a switch to control an LED. Freeze this control operation through UART. Send one byte of data from UART to PC. If the byte is 0x00, freeze the control.
> 

# Schematic

### 1) UART Schematic and Configuration:

- Schematic of UART:

![Untitled](Switch%20%E2%86%94%20LED%20mapping%20using%20UART%20fffd051c359e81a9846be1fa75d76ba2/Untitled.png)

- **Configuration of UART:**

![Untitled](Switch%20%E2%86%94%20LED%20mapping%20using%20UART%20fffd051c359e81a9846be1fa75d76ba2/Untitled%201.png)

![Untitled](Switch%20%E2%86%94%20LED%20mapping%20using%20UART%20fffd051c359e81a9846be1fa75d76ba2/Untitled%202.png)

### 2) Switch Schematic and Configuration:

- Schematic of Switch:

![Untitled](Switch%20%E2%86%94%20LED%20mapping%20using%20UART%20fffd051c359e81a9846be1fa75d76ba2/Untitled%203.png)

- Configuration of Switch:

![Untitled](Switch%20%E2%86%94%20LED%20mapping%20using%20UART%20fffd051c359e81a9846be1fa75d76ba2/Untitled%204.png)

![Untitled](Switch%20%E2%86%94%20LED%20mapping%20using%20UART%20fffd051c359e81a9846be1fa75d76ba2/Untitled%205.png)

### 3) LED Schematic and Configuration:

- LED SchematicLED Configuration

![Untitled](Switch%20%E2%86%94%20LED%20mapping%20using%20UART%20fffd051c359e81a9846be1fa75d76ba2/Untitled%206.png)

- LED Configuration

![Untitled](Switch%20%E2%86%94%20LED%20mapping%20using%20UART%20fffd051c359e81a9846be1fa75d76ba2/Untitled%207.png)

### 4) Pin Layout:

![Untitled](Switch%20%E2%86%94%20LED%20mapping%20using%20UART%20fffd051c359e81a9846be1fa75d76ba2/Untitled%208.png)

# PSoC Firmware

```c
#include "project.h"

uint8_t status;
CY_ISR (Pin_Switch_Handler) //Pin_Switch is the name of the pin, Pin_Switch_Int is the name of the interrupt
{
    if(status == 0x00)
    {
        Pin_LED_Write(Pin_LED_Read());
    }
    
    else
    {
        Pin_LED_Write(~Pin_LED_Read());
    }
    
    Pin_Switch_ClearInterrupt(); 
}

CY_ISR(my_isr)
{
    status = UART_1_UartGetByte(); 
    UART_1_SpiUartClearRxBuffer();
    UART_1_ClearRxInterruptSource(UART_1_INTR_RX_ALL);
}

int main(void)
{
    CyGlobalIntEnable; /* Enable global interrupts. */

    /* Place your initialization/startup code here (e.g. MyInst_Start()) */
    UART_1_Start();
    Pin_Switch_Int_StartEx (Pin_Switch_Handler);
    isr_1_StartEx(my_isr);
    for(;;)
    {
        
    }
}
```

```python
"""
Created on Wed Apr 26 12:21:12 2023

@author: TejasBN
"""
import serial           # import the module
import time

serialPort= serial.Serial('COM6', baudrate=115200, parity=serial.PARITY_NONE, stopbits=serial.STOPBITS_ONE)
while True:
    x = input()
    if x == 'q':
        break
    elif x == '1':
        serialPort.write(b'\x01')
    elif x == '0':
        serialPort.write(b'\x00')
    #time.sleep(0.1)
serialPort.close()
```

**— END**