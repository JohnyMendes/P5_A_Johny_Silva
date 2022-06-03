# Johny Silva Mendes
# PRACTICA 5: BUS I2C

PRÁCTICA 5: Buses de comunicación I (introducción y I2C)

Objetivo: conectar dispositivo I2C a través de la ESP32.

## EJERCICIO PRACTICO 1: ESCÁNER I2C

la librería <Wire.h> permite hacer la conexión bus I2C. El setup sólo inicializa la conexión con el puerto serie 
```cpp
#include <Arduino.h>
#include <Wire.h> 
 
void setup(){
  Wire.begin();
  Serial.begin(9600);
  while (!Serial);       // Leonardo: wait for serial monitor
  Serial.println("\nI2C Scanner");
}
 ```
 Dentro del Loop se puede observar la parte más importante del código. 
 Primero se definen dos variables tipo *byte* que servirá para guardar la dirección del dispotivo I2C y el return de la función *wire.endTransmission()*. A continuación se inicializará el contador de dispositivos a 0. 

```cpp
void loop(){
  byte error, address;
  int nDevices;
 
  Serial.println("Scanning...");
 
  nDevices = 0;
  ```
Siguiendo con el código se entra en un bucle de 1 a 127 para detectar cualquier dispositivo I2C.
La función *Wire.endTransmission()* devuelve un byte con valores de 0 a 4 dependiendo de cada situación
* 0: éxito
* 1: datos demasiado largo para caber en la memoria intermedia de transmisión
* 2: NACK recibido en transmisión de dirección
* 3: recibido NACK en transmisión de datos
* 4: otro error
Por lo tanto, si *Wire.endTransmission()* encuentra una conexión, el byte *error* será 0 y se mostrará por pantalla la dirección del dispositivo detectado. 

Si *error*=4 se informará de que hubo un error en la conexión, y por último, si no es ninguna de estas opciones se mostrará el mensaje de que no se ha detectado ningún dispositivo I2C. 
También cabe decir que una vez se ha ejecutado el Loop, el dispositivo I2C no se mantiene conectado a la micro, ya que se habrá realizado la operación de finalizar la transmisión.

```cpp
  for(address = 1; address < 127; address++ ){
  // The i2c_scanner 
  // the Write.endTransmisstion to see if
  // a device did acknowledge to the address.
    Wire.beginTransmission(address);
    error = Wire.endTransmission();
 
    if (error == 0)
    {
      Serial.print("I2C device found at address 0x");
      if (address<16)
        Serial.print("0");
      Serial.print(address,HEX);
      Serial.println("  !");
 
      nDevices++;
    }
    else if (error==4)
    {
      Serial.print("Unknown error at address 0x");
       if (address<16)
        Serial.print("0");
        Serial.println(address,HEX);
    }    
}
  
if (nDevices == 0)
  Serial.println("No I2C devices found\n");

else
  Serial.println("done\n");
  delay(5000);           // wait 5 seconds for next scan
}
```
En el caso de que la conexión haya ocurrido con éxito, el puerto serie tendría que ser el siguiente, en vez de aparecer 3C, saldría la dirección del dispositivo I2C:
```
Scanning...
I2C device found at address 0x3C  ! 
done
```
