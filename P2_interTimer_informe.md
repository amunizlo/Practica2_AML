# Práctica 2 INTERRUPCIONES por TIMER
###### Andrea Muñiz
<p></p>

## Programa + explicación

> Declaramos las cabeceras a utilizar

```
#include <Arduino.h>
```

> Definimos las variables que utilizaremos

```
volatile int interruptCounter;
int totalInterruptCounter;

hw_timer_t * timer = NULL;
portMUX_TYPE timerMux = portMUX_INITIALIZER_UNLOCKED;
```

__interruptCounter__ -> nos servirá como contador volatil.<p></p>
__totalInterruptCounter__ -> es un contador de las veces que se han producido interrupciones.<p></p>
__timer__ -> variable temporizador.<p></p>

> Función IRAM_ATTR onTimer()

Esta función lo que hará será incrementar la variable __interruptCounter__ en 1 cada vez que se produzca una nueva interrupción.

```
void IRAM_ATTR onTimer() {
    portENTER_CRITICAL_ISR(&timerMux);
    interruptCounter++;
    portEXIT_CRITICAL_ISR(&timerMux);
}
```

> Función setup()

```
void setup() {

    Serial.begin(115200);

    timer = timerBegin(0, 80, true);
    timerAttachInterrupt(timer, &onTimer, true);
    timerAlarmWrite(timer, 1000000, true);
    timerAlarmEnable(timer);
}
```

> Función bucle 

En esta función creamos un bucle en el que si el contador de interrupciones __interruptCounter__ es mayor que 0, se analiza dicha variable y se le resta 1 como muestra de que se ha reconocido la existencia de la interrupción y pasará a ser trabajada. <p></p>
A continuación se aumenta el numero de interrupciones total, __totalInterruptCounter__, y se escribe por el monitor serie "An interrupt as ocurred. Total number: " Para hacer saber al usuario que se ha recibido una nueva interrupción y cuántas interrupciones han ocurrido ya.

```
void loop() {
    if (interruptCounter > 0) {

        portENTER_CRITICAL(&timerMux);
        interruptCounter--;
        portEXIT_CRITICAL(&timerMux);

        totalInterruptCounter++;

        Serial.print("An interrupt as occurred. Total number: ");
        Serial.println(totalInterruptCounter);
    }
}
```