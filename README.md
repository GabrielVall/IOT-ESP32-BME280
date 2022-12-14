# IOT-ESP32-BME280

Guía completamente detallada para el desarrollo de un proyecto IOT utilizando un sensor BMP280/BME280
mediante una placa ESP32 y alojando en un servidor web gratuito [000webhost](https://mex.000webhost.com/)

## Autor

- [@GabrielVall](https://github.com/GabrielVall/)

#### Importante: Tenga en cuenta que este proyecto requiere conocimientos básicos en cuanto a administrar un sitio, además de un servidor compatible con php y bases de datos, ya sea un host gratuito como [000webhost](https://mex.000webhost.com/) o con cualquier otro servidor de pago.

# PARTE 1: Crear un hosting en la nube

Necesitaremos tener un hosting compatible con php, y que nos proporcione al menos una ip publica (tambien puede ser un dominio o subdominio) donde veamos los cambios de nuestro proyecto, en este caso utlizaremos [000webhost](https://mex.000webhost.com/) por ser gratuito, sin embargo puedes utilizar cualquier servicio que te proporcione lo mencionado anteriormente.

![App Screenshot](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/imagenes/hosting/000webhost_1.png)
![App Screenshot](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/imagenes/hosting/000webhost_2.png)
![App Screenshot](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/imagenes/hosting/000webhost_3.png)
![App Screenshot](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/imagenes/hosting/000webhost_4.png)
![App Screenshot](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/imagenes/hosting/000webhost_5.png)
![App Screenshot](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/imagenes/hosting/000webhost_6.png)
![App Screenshot](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/imagenes/hosting/000webhost_7.png)

# PARTE 2: Crear una base de datos

## Entraremos a la interfaz de animinstración, entrado primero a el sitio web que acabamos de crear.

![App Screenshot](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/imagenes/1.png)

## Entramos en el gestor de base de datos de nuestro sitio

![App Screenshot](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/imagenes/bd/1.png)

## Crearemos nuestra base de datos y usuario y contraseña, asegurate de guardar tu contraseña en un lugar seguro

![App Screenshot](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/imagenes/bd/2.png)

## Al crear tu base de datos puedes ver la información de la misma, deberas tener estos datos muy en cuenta
- `Nombre de la base de datos (DB Name)`
- `Usuario de la base de datos (DB User)`
- `Servidor (DB Host)`
- `Contraseña (la asignamos al crear nuestra base de datos)`

![App Screenshot](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/imagenes/bd/3.png)

## Ahora seleccionaremos el boton "Manage" y haremos clic en `phpmyadmin`

![App Screenshot](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/imagenes/bd/4.png)

## Una vez dentro de phpmyadmin, seleccionaremos importar y luego en el tab superior `SQL`

![App Screenshot](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/imagenes/bd/5.png)

## Pegaremos el siguiente código en el recuadro y presionaremos `continuar`

```sql
CREATE TABLE `datos_sensor` (
  `id_datos` bigint(20) NOT NULL,
  `equipo` varchar(500) DEFAULT 'Sin equipo',
  `temperatura` double DEFAULT '0',
  `humedad` double NOT NULL DEFAULT '0',
  `presion` double NOT NULL DEFAULT '0',
  `fecha_hora` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP
);

ALTER TABLE `datos_sensor`
ADD PRIMARY KEY (`id_datos`);

ALTER TABLE `datos_sensor`
MODIFY `id_datos` bigint(20) NOT NULL AUTO_INCREMENT;
```

![App Screenshot](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/imagenes/bd/6.png)


# PARTE 3: Crear la vista de nuestra aplicación

## Accede al gestor de archivos entrado dentro del panel anterior y seleccionando `file manager`

![App Screenshot](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/imagenes/bd/7.png)

## Ahora entraremos a la carpeta `public_html`

![App Screenshot](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/imagenes/files/1.png)

Nota: La carpeta public_html almacena los archivos publicos de nuestro servidor, es decir, cualquier persona con el enlace podría ver estos archivos .

Para este paso necesitaras crear 4 archivos en tu servidor
- index.php
- estilos.css
- script.js
- conexion.php
- datos_sensor.php

### En el archivo *index.php* agregaremos las siguientes lineas de código:

Nota: Si intentamos ver nuestro sitio nos nos arrojara un error ya que nos faltarían incluir los demás archivos.

```php
<?php
include_once("conexion.php"); // Nos conectamos a nuestra BD
$sql = new SQLConexion(); // Hacemos la conexion desde el archivo
$datos = $sql->selectData("SELECT * FROM datos_sensor ORDER BY id_datos DESC LIMIT 20;"); // Seleccionamos los ultimos 20 datos de nuestro sensor
$temperatura = $datos[0]['temperatura']; // Obtenemos el ultimo registro de temperatura
$presion = $datos[0]['presion']; // Obtenemos el ultimo registro de de presion
$humedad = $datos[0]['humedad']; // Obtenemos el ultimo registro de de humedad
?>
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Practica ESP32</title>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/roundSlider/1.3.2/roundslider.min.css" rel="stylesheet" />
    <link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.5.0/css/all.css" integrity="sha384-B4dIYHKNBt8Bc12p+WXckhzcICo0wtJAoU8YZTY5qE0Id1GSseTk6S+L3BlXeVIU" crossorigin="anonymous">
    <link rel="stylesheet" href="estilos.css">
</head>
<body temperatura="<?php echo $temperatura; ?>" humedad="<?php echo $presion; ?>" presion="<?php echo $humedad; ?>">
    <div class="frame">
        <div id="slider" class="rslider"></div>
        <div class="thermostat">
            <div class="ring">
                <div class="bottom_overlay"></div>
            </div>
            <div class="control">
                <div class="temp_outside" id="presion">PA: 1200</div>
                <div class="temp_room"><span id="temperatura"></span>°</div>
                <div class="room" id="humedad">Hum: 30%</div>
            </div>
        </div>
        <div class="instructions">
            <h4>Datos obtenidos del equipo: Nombre del tu equipo</h4>
        </div>
    </div>
<script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>                                
<script src="https://cdnjs.cloudflare.com/ajax/libs/roundSlider/1.3.2/roundslider.min.js"></script>
<script src="script.js"></script>
</body>
</html>
```
### En el archivo *estilos.css* agregaremos las siguientes lineas de código:
Nota: Esto dara el diseño de la interfaz al usuario
```css
@import url('https://fonts.googleapis.com/css?family=Rubik:300,400|Raleway:300');
 body {
	 background: #643a7b;
}
 .frame {
	 position: absolute;
	 top: 50%;
	 left: 50%;
	 width: 400px;
	 height: 400px;
	 margin-top: -200px;
	 margin-left: -200px;
	 border-radius: 2px;
	 box-shadow: 0.5rem 1rem 1rem 0 rgba(0, 0, 0, 0.6);
	 overflow: hidden;
	 color: #333;
	 font-family: 'Rubik', Helvetica, sans-serif;
	 -webkit-font-smoothing: antialiased;
	 -moz-osx-font-smoothing: grayscale;
	 background: #201c29;
}
 .thermostat {
	 position: absolute;
	 width: 200px;
	 height: 200px;
	 top: 100px;
	 left: 100px;
	 background: #f2f2f2;
	 border-radius: 50%;
	 box-shadow: 0px 0px 1rem rgba(0, 0, 0, 0.8);
}
 .thermostat .control {
	 position: absolute;
	 z-index: 5;
	 width: 130px;
	 height: 130px;
	 top: 25%;
	 left: 35px;
	 background: #e6e6e6;
	 border-radius: 50%;
	 box-shadow: 0 0 1rem rgba(0, 0, 0, 0.7);
}
 .thermostat .control .temp_outside {
	 position: absolute;
	 top: 25px;
	 left: 6px;
	 right: 0;
	 text-align: center;
	 font-weight: 300;
	 font-size: 1rem;
}
 .thermostat .control .temp_room {
	 position: absolute;
	 top: 34px;
	 left: 0;
	 right: 0;
	 text-align: center;
	 font-weight: 400;
	 font-size: 60px;
	 line-height: 60px;
	 color: #873183;
	 letter-spacing: -8px;
	 padding-right: 12px;
	 opacity: 1;
	 transform: translateX(0);
	 transition: all 0.5s ease-in-out;
}
 .thermostat .control .temp_room span {
	 position: absolute;
	 top: 0;
	 right: 37px;
	 font-size: 2rem;
	 line-height: 34px;
	 padding: 3px 0 0 7px;
	 color: #8e2275;
}
 .room {
	 position: absolute;
	 bottom: 18px;
	 left: 0;
	 right: 0;
	 text-align: center;
	 font-weight: 300;
	 font-size: 1rem;
}
 .thermostat .ring {
	 position: absolute;
	 width: 180px;
	 height: 180px;
	 top: 10px;
	 left: 10px;
	 background: url("http://100dayscss.com/codepen/thermostat-gradient.jpg") center center no-repeat;
	 border-radius: 50%;
	 box-shadow: inset 2px 4px 4px 0px rgba(0, 0, 0, 0.3);
}
 .thermostat .ring .bottom_overlay {
	 position: absolute;
	 width: 95px;
	 height: 95px;
	 top: 50%;
	 left: 50%;
	 background: #f2f2f2;
	 transform-origin: 0 0;
	 transform: rotate(45deg);
	 border-radius: 0 0 95px 0;
}
 #slider {
	 position: absolute;
	 width: 170px;
	 height: 150px;
	 top: 36%;
	 left: 32%;
	 z-index: 1000;
}
 #slider .rs-border {
	 border-color: transparent;
}
 .rs-control .rs-range-color, .rs-control .rs-path-color, .rs-control .rs-bg-color {
	 background-color: rgba(0, 0, 0, 0);
}
 .rs-control .rs-handle {
	 background-color: rgba(82, 44, 109, 0.8);
}
 .rs-tooltip.edit, .rs-tooltip .rs-input, .rs-tooltip-text {
	 font-family: rubik, helvetica, sans-serif;
	 font-size: 3.3rem;
	 background: transparent;
	 color: #8e2275;
	 font-weight: 400;
	 top: 65%;
	 height: 3.9rem;
	 padding: 0 !important;
	 width: 4.5rem;
}
 #slider:hover .rs-tooltip, .rs-tooltip:focus, .rs-tooltip-text:focus {
	 border: none;
	 transform: scale(1.1);
	 transition: 0.1s;
}
 #slider .rs-transition {
	 transition-timing-function: cubic-bezier(1, -0.53, 0.405, 1.425);
}
 .instructions {
	 position: absolute;
	 bottom: 0.5rem;
	 color: rgba(255, 255, 255, 0.25);
	 font-size: 1rem;
	 font-family: raleway, sans-serif;
	 width: 85%;
	 left: 10%;
	 font-weight: 300;
	 letter-spacing: 0.05rem;
	 line-height: 1.3;
	 text-align: center;
}
 .fas {
	 animation: pulse 1s infinite;
}
 @keyframes pulse {
	 50% {
		 transform: scale(0.9);
	}
}
```
### En el archivo *script.js* agregaremos las siguientes lineas de código:
Nota: Esto hara la animación e imprimira los datos en la vista del usuario
```javascript
$('#presion').html($('body').attr('presion'));//Obtenemos la presion
$('#humedad').html($('body').attr('humedad'));//Obtenemos la humedad
var temp = $('body').attr('temperatura'); // Obtenemos la temperatura
$("#slider").roundSlider({
    radius: 72,
    circleShape: "half-top",
    sliderType: "min-range",
    mouseScrollAction: true,
    value: temp,
    handleSize: "+5",
    min: 0,
    max: 100
});
```
### En el archivo *conexion.php* agregaremos las siguientes lineas de código:
Nota: Esto hara la animación e imprimira los datos en la vista del usuario
```php
<?php
class SQLConexion{
    public $conexion;
    private $server='localhost'; // Nombre del servidor
    private $usuario='tu_usuario'; // Usuario del servidor
    private $clave='tu_contraseña('; // Contraseña
    private $bd='nombre_de_tu_base_de_datos'; // Nombre de la base de datos
   
    public function conectar(){ // Conectamos a la base de datos
        $this->conexion=@new mysqli($this->server,$this->usuario,$this->clave,$this->bd);
        if ($this->conexion->connect_error) // Si la conexion falla
            die('Error de Conexion :(');
        else
            $this->conexion->set_charset("utf8mb4");
    }

    //desconectarse a la DB
    public function desconectar(){
        $this->conexion->close();
    }

    //Ejecuta un query y retorna el resultado en un Array
    //Solo utilizar cuando el resultado del Query provenga de un Select
    //Ya sea un Select Directo o dentro de un SP
    public function selectData($QueryString){
        $this->conectar();
        $Resultado=$this->conexion->query($QueryString); //Ejecucion del Query
        $Datos=array(); //Declaracion Array donde almacenaremos nuestros datos
        $i=0;
        while($fila=$Resultado->fetch_array()){
            $Datos[$i]=$fila;
            $i++;
        }
        $this->desconectar();
        return $Datos;
    }

    //Para obtener el resultado de ejecutar un query que no devuelve datos, como Insert,Delete,Update.
    //Resultado retorna TRUE si el query se ejecuto correctamente.
    public function updateData($QueryString){
        $this->conectar();
            $Resultado=$this->conexion->query($QueryString);
            $this->desconectar();
        Return $Resultado;
    }

    // Al igual que el update data, sin embargo es necesario enviar un parametro llamado @_ID
    public function returnId($QueryString1){
        $this->conectar();
        $Resultado1=$this->conexion->query($QueryString1);
        $Resultado2=$this->conexion->query("SELECT @_ID as _ID;"); //Ejecucion del Query
        $Datos=array(); //Declaracion Array donde almacenaremos nuestros datos
        $i=0;

        while($fila=$Resultado2->fetch_array()){
            $Datos[$i]=$fila;
            $i++;
        }
        $this->desconectar();
        return $Datos;
    }

    //Escapa caracteres especiales en un string. Ayuda a prevenir inyecciones SQL

    public function escapar($string){
        $this->conectar();
        //Evalua si el argumento que se le pasa es un arreglo
        if(is_array($string)){
            $funcion = array($this->conexion,"real_escape_string"); // Si lo es, guarda en un arreglo el contexto y el nombre de la funci贸n real_escape_string
            $escapedArray = array_map($funcion,$string); // Le aplica dicha función a todo el arreglo
            return $escapedArray; // Retorna el arreglo ya escapado y sale de la función
        }
        // Si no es un arreglo, escapa el string y lo retorna
        $escapedString = $this->conexion->real_escape_string($string);
        $this->desconectar();
        return $escapedString;
    }

}
```

### En el archivo *datos_sensor.php* agregaremos las siguientes lineas de código:
Nota: Este archivo establecera una comunicación entre el proyecto y nuestro sistema web

```php
<?php
$equipo = $_POST['equipo'];
$temperatura = $_POST['temperatura'];
$humedad = $_POST['humedad'];
$presion = $_POST['presion'];
include_once("conexion.php");
$sql = new SQLConexion();
$row = $sql->updateData("
INSERT INTO datos_sensor (equipo,temperatura,humedad,presion,fecha_hora) 
VALUES ('$equipo', $temperatura, $humedad, $presion, CURRENT_TIMESTAMP);
");
```

### Si abrimos la URL de nuestro sitio veremos algo como esto:
![App Screenshot](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/imagenes/2.png)

# PARTE 4: Enviar los datos desde nuestro ESP32
Para este paso necesitaremos tener el IDE de arduino, este lo puedes descargar desde [Este link](https://www.arduino.cc/en/software) en su página oficial.

## Agregando dependencias externas
### Entramos a preferencias en "Archivo > Preferencias" o bien "File > Preferences"
![App Screenshot](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/imagenes/arduino/1.png)
### Agregamos el siguiente link en el recuadro que esta debajo
`https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json`
![App Screenshot](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/imagenes/arduino/2.png)
### Agregamos la placa esp32 a arduino
![App Screenshot](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/imagenes/arduino/3.png)
### Buscamos esp32 en el buscador e instalamos
![App Screenshot](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/imagenes/arduino/4.png)
## Agregamos la libreria del sensor bme280
![App Screenshot](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/imagenes/arduino/5.png)
### Buscamos `adafruit bme280`
![App Screenshot](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/imagenes/arduino/6.png)
### Presionamos `install all` o `instalar todo`
![App Screenshot](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/imagenes/arduino/7.png)
### Seleccionamos la placa
![App Screenshot](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/imagenes/arduino/8.png)
### Seleccionamos puerto
![App Screenshot](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/imagenes/arduino/9.png)

```c++
#ifdef ESP32
  #include <WiFi.h>
  #include <HTTPClient.h>
#else
  #include <ESP8266WiFi.h>
  #include <ESP8266HTTPClient.h>
  #include <WiFiClient.h>
#endif

#include <Wire.h>
#include <Adafruit_Sensor.h>
#include <Adafruit_BME280.h>

// Agregar claves de internet
const char* ssid = "IOT";
const char* password = "";

// URL a la que la placa intentara acceder
const char* serverName = "http://api.conecta.rydevs.com/data_post.php";

#define SEALEVELPRESSURE_HPA (1013.25)

Adafruit_BME280 bme;  // I2C

void setup() {
  Serial.begin(115200);
  
  WiFi.begin(ssid, password);
  Serial.println("Intentando conectar a la red");
  while(WiFi.status() != WL_CONNECTED) { 
    delay(500);
    Serial.print(".");
  }
  Serial.println("");
  Serial.print("Conexion completada, la direccion ip es: ");
  Serial.println(WiFi.localIP());

  // Selecciona la entrada de datos
  bool status = bme.begin(0x76);
  if (!status) {
    Serial.println("No se encontro un sensor valido (BME280), verifica que no haya falso contacto o la entrada donde esta conectada coincida con el codigo");
    while (1);
  }
}

void loop() {
  //Si esta conectado a internet
  if(WiFi.status()== WL_CONNECTED){
    WiFiClient client;
    HTTPClient http;
    
    // La URL de tu dominio
    http.begin(client, serverName);
    
    // Especificar el content type para el header del POST
    http.addHeader("Content-Type", "application/x-www-form-urlencoded");
    
    // Preparar los datos POST a enviar
    String httpRequestData = "equipo=Docente&dispositivo=2022&id=1200&temperatura=" + String(bme.readTemperature())+ "&humedad=" + String(bme.readHumidity()) + "&presion=" + String(bme.readPressure()/100.0F) + "";
    Serial.print("httpRequestData: ");
    Serial.println(httpRequestData);

    // Envia los datos por POST
    int httpResponseCode = http.POST(httpRequestData);

    // Si la respuesta no dio error
    if (httpResponseCode>0) {
      Serial.print("HTTP Codigo de respuesta: ");
      Serial.println(httpResponseCode);
    }
    else {
      Serial.print("Codigo de error: ");
      Serial.println(httpResponseCode);
    }
    // Termina la conexion del POST
    http.end();
  }
  else {
    Serial.println("Desconectado del wifi");
  }
  //Tiempo de espera entre solicitudes al servidor
  delay(5000);  
}
```