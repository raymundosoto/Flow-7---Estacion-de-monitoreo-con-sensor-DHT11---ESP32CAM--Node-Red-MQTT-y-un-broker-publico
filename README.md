# Flow-7-Estacion-de-monitoreo-con-sensor-DHT11-ESP32CAM-Node-Red-MQTT-y-un-broker-publico

> Escrito por  [Raymundo Soto](https://github.com/raymundosoto).

## Introducción

De acuerdo al [Sistema Nacional de Información de la Calidad del Aire, SINAICA](https://sinaica.inecc.gob.mx/) un *Sistema de Monitoreo de la Calidad del Aire, SMCA,* es un  conjunto organizado de recursos humanos, técnicos y administrativos empleados para operar una o un conjunto de estaciones de monitoreo y/o muestreo que miden la calidad del aire en una zona o región.   *La red  de muestreo y/o monitoreo, RM,* es una red de medición conformada por más de una estación de muestreo y/o monitoreo y representan el conjunto de estaciones que miden la calidad del aire en una región determinada.   *La estación de monitoreo* tiene uno o más instrumentos diseñados para medir, de forma continua, la concentración de contaminantes en aire ambiente, con el fin de evaluar la calidad del aire en un área determinada.   Los datos generados en las estaciones se denominan *datos crudos* y son los datos que se obtienen en las redes de monitoreo que no han sido validados. La *validación de datos* es un proceso para determinar la calidad analítica de un conjunto de datos de acuerdo con las necesidades particulares del Sistema de Monitoreo de la Calidad del Aire y de su plan de aseguramiento de calidad [1].

En este ejercicio se creó una estación de monitoreo de temperatura y humedad local con el sensor DHT11 y un ESP32 CAM. Se programó el sensor DHT11 en el IDE de Arduino para realizar la adquisición de los datos y se creo una interfaz gráfica (dashboard) usando node-red. Los datos del sensor se envían a través de un broker público para compartirlos con los compañeros del curso. En el dashboard final se observan los datos de temperatura, humedad, índice UV y calidad del aire enviados por todos los participantes del curso y que fueron obtenidos a través de la API de [Openweathermap](https://openweathermap.org/api).  De igual forma se observan los datos obtenidos de temperatura y humedad por el sensor de forma local y los datos recolectados por los participantes del curso. Este ejercicio permitió conjuntar todos los conocimientos adquiridos del curso (conexión del sensor al controlador ESP32 cam,  conexión del circuito con MQTT de forma local y a broker público y programación del controlador en  arduino y documentación en Github).

## Material necesario
 - Sensor dht11 
 - Controlador ESP32cam
 - Modulo Adaptador usb a serial ttl ftdi
 - Cables jumper
 - Protoboard
 - Cable serial
 - Conectar el circuito de la siguiente forma:
 
![imagen](https://user-images.githubusercontent.com/72757419/186284422-7f02778f-1358-4439-acd9-e5abf82e4333.png)

 ![imagen](https://user-images.githubusercontent.com/72757419/186284390-2c967e07-8850-4e61-ab2b-5eee4c5e786e.png)

## Software necesario

 - Ubuntu 22 (https://ubuntu.com/download/desktop)
 - Arduino IDE (https://www.arduino.cc/en/software) corriendo en Ubuntu
 - Mosquitto (https://mosquitto.org/download/) corriendo en Ubuntu
 - Node-red (https://nodered.org/docs/getting-started/local)
 - Programa para conectar sensor dht11 al ESP32 y enviar datos al broker Hivemq   [Link al programa](https://github.com/raymundosoto/Flow-6-Sensor-DHT11-con-MQTT-y-Dashboard-en-node-red/blob/8a97178c9d1ada07a29efcadc62bf6767b74c830/json_mqtt_esp32_cam_dht11/json_mqtt_esp32_cam_dht11.ino)
 - Flow de node-red para adquisición, envío y visualización de datos en el broker [archivo json del flow ](https://github.com/raymundosoto/Flow-7---Estacion-de-monitoreo-con-sensor-DHT11---ESP32CAM--Node-Red-MQTT-y-un-broker-publico/blob/627bb53ce3f26d6435504aeee759c1e13fbc34e8/flow_7_dht11_esp32_MQTT_.json)
 - Biblioteca DHT11 de adafruit en arduino [DHT11](https://github.com/adafruit/DHT-sensor-library)
 - Biblioteca [PubSubClient](https://pubsubclient.knolleary.net/) en arduino
 - Grafana (se instala de acuerdo a la guía)
 - MySQL para la base de datos
 
##  Instrucciones
1. Realizar las conexiones del circuito para conectar el ESP32CAM y el sensor dht11. Asegurarse que los pines está correctamente conectados (se pueden ver en el archivo .ino)
2. Cargar el programa en el  ESP32Cam con el IDE de arduino. cerciorarse que hay lecturas de datos con el monitor serial del IDE de arduino (revisar el [repositorio de conexión básica por MQTT entre ESP32 CAM y dht11](https://github.com/hugoescalpelo/ESP32CAM_MQTT-Basic.git)
3. Entorno de Mosquitto MQTT configurado. Tener modificado el archivo mosquitto.conf para que acepte conexiones externas y no autentificadas. Deberás tener el firewall de Ubuntu abierto para permitir conexiones en el puerto 1883.
4. Verificar que MQTT está funcionando y que el ESP32 está subscrito en el tema codigoIoT/ejemplo/mqtt y publica en el tema codigoIoT/ejemplo/mqttin de forma local usando la terminal de ubuntu
5. Energizar el circuito conectándolo a la entrada USB de la computadora con el cable serial (o se puede conectar a una fuente de 5 volts, de cualquier forma el ESP32 se conectará al WIFI configurado).
6. Activar node-red y cargar el  [flow ](https://github.com/raymundosoto/Flow-7---Estacion-de-monitoreo-con-sensor-DHT11---ESP32CAM--Node-Red-MQTT-y-un-broker-publico/blob/627bb53ce3f26d6435504aeee759c1e13fbc34e8/flow_7_dht11_esp32_MQTT_.json) y verificar en el dashboard que hay lecturas de datos y se publican en el broker local con los temas del paso 3
7. Los nodos MQTT del flow deberán estar configurados de tal forma que publiquen en el tema `mosquitto_sub -h 3.121.170.193 -t codigoIoT/g7/mosquitto/API`y en el tema `mosquitto_sub -h 3.121.170.193 -t codigoIoT/g7/mosquitto/sensores`la ip del broker  [HIVEMQ](https://www.hivemq.com/) cambia en el tiempo por lo que hay que revisar cuál es la del día actual.
8. Comprobar que las funciones del flow están enviando y recibiendo los json  los datos de temperatura y humedad del sensor y los obtenidos vía API (Los códigos vienen en los nodos *function*).
9. Crear la base de datos con el nombre, temperatura y humedad.
10. Abir Grafana, conectarla a la base de datos crear las gráficas
11. Copiar el código en grafana para insertar las gráficas en NodeRed

## Funcionamiento

 1. El sensor DHT11 adquiere los datos y los envía por MQTT usando el ESP32CAM publicándolos de forma local.
 2. El flow de Node-red los lee de forma local y los envía al servidor de HIVEMQ para publicarlos en `mosquitto_sub -h 3.121.170.193 -t codigoIoT/g7/mosquitto/API` y `mosquitto_sub -h 3.121.170.193 -t codigoIoT/g7/mosquitto/sensores`
 3. El flow de node-red se suscribe a los temas y lee los datos enviados por los demás integrantes del curso a los temas  `mosquitto_sub -h 3.121.170.193 -t codigoIoT/g7/mosquitto/API   mosquitto_sub -h 3.121.170.193 -t codigoIoT/g7/mosquitto/sensores`
 4. El flow de node-red obtiene los datos del sensor y los enviados desde el broker y los gráfica en el dashboard
 5. De forma adicional si la temperatura local es mayor a 28 ºC se activa el led del ESP32CAM

## Resultados
En las imágenes se pueden observar las lecturas locales de temperatura y humedad, además de los datos reportados por API y por los sensores de los integrantes del curso.
![imagen](https://user-images.githubusercontent.com/72757419/186284640-8acc9fa9-017f-47f4-b543-3654278358e5.png)
![imagen](https://user-images.githubusercontent.com/72757419/186284690-8cb5767d-cf78-476a-a3c2-55e3f2781a17.png)

Flow con las gráfica de grafana
![imagen](https://user-images.githubusercontent.com/72757419/190494325-e58daa26-b85d-41db-b17f-c7a273982e09.png)


## Evidencia
Cómo evidencia se realizó el siguiente vídeo de tiktok

https://vm.tiktok.com/ZMNWoTjhF/

# Conclusiones

Los sistemas de monitoreo de datos ambientales son un claro ejemplo de que el internet de las cosas permite la conjunción de diversas áreas del conocimiento humano con el fin de medir, procesar, enviar, recibir y analizar datos para crear aplicaciones útiles para el ser humano. En lo particular, me resulta genial poder adquirir datos a través de un sensor y poderlos visualizar en una interfaz gráfica y además de poderlos enviar al internet con ayuda de los protocolos de comunicación. La metodología de trabajo empleada en este prototipo nos permitirá diseñar sistemas que trabajen con la misma intención del uso de los datos.   

·

### Fuentes bibliográficas
1. _Sistema Nacional de Información de la Calidad del Aire, SINAICA_. (s. f.). Gobierno de México. Recuperado 23 de agosto de 2022, de https://sinaica.inecc.gob.mx/
