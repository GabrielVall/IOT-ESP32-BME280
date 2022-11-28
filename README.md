


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

## 

## Guarda 

Para crear una base de datos entraremos a phpmyadmin desde el servidor y seleccionaremos **importar**.

![App Screenshot](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/img_tutorial/img1.png)


## Dentro del recuadro agregaremos el siguiente código:

```sql
CREATE TABLE `datos_sensor` (
  `id_datos` bigint(20) NOT NULL,
  `equipo` varchar(500) DEFAULT 'Sin equipo',
  `temperatura` double DEFAULT '0',
  `humedad` double NOT NULL DEFAULT '0',
  `presion` double NOT NULL DEFAULT '0',
  `fecha_hora` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP
);
```
Ahora tendremos creada una tabla en la cual podremos insertar la información de los sensores en nuestra base de datos.

## Paso 2: Crear la vista de nuestra aplicación
Para este paso necesitaras crear 3 archivos en tu servidor
- index.html
- estilos.css
- script.js

### En el archivo *index.html* agregaremos las siguientes lineas de código:
```html
<html lang="es"><head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Practica ESP32</title>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/roundSlider/1.3.2/roundslider.min.css" rel="stylesheet">
    <link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.5.0/css/all.css" integrity="sha384-B4dIYHKNBt8Bc12p+WXckhzcICo0wtJAoU8YZTY5qE0Id1GSseTk6S+L3BlXeVIU" crossorigin="anonymous">
	<link rel="stylesheet" src="estilos.css">
</head>
<body temperatura="" presion="" humedad="">
    <div class="frame">
        <div id="slider" class="rslider rs-ie rs-control rs-animation" style="height: 72px; width: 144px;"><div class="rs-container top half" style="height: 72px; width: 144px;"><div class="rs-inner-container"><div class="rs-block rs-outer rs-border rs-split"><div class="rs-path rs-transition rs-range-color" style="transform: rotate(0deg);"></div><div class="rs-path rs-transition rs-range-color" style="opacity: 0; transform: rotate(-180deg);"></div><div class="rs-path rs-transition rs-path-color" style="transform: rotate(0deg); opacity: 1;"></div><div class="rs-path rs-transition rs-path-color" style="opacity: 1; z-index: 1; transform: rotate(-180deg);"></div><span class="rs-block" style="padding: 18px;"><div class="rs-inner rs-bg-color rs-border"></div></span></div></div><div class="rs-bar rs-transition rs-first" style="z-index: 7; transform: rotate(0deg);"><div class="rs-handle rs-move" index="2" tabindex="0" role="slider" aria-label="slider_handle" style="height: 23px; width: 23px; margin: -11.5px 0px 0px -1.5px;" aria-valuenow="10" aria-valuemin="10" aria-valuemax="50"></div></div><span class="rs-bar rs-transition rs-start" style="transform: rotate(0deg);"><span class="rs-seperator rs-border" style="width: 20px; margin-top: -0.5px;"></span></span><span class="rs-bar rs-transition rs-end" style="transform: rotate(180deg);"><span class="rs-seperator rs-border" style="width: 20px; margin-top: -0.5px;"></span></span><span class="rs-tooltip rs-tooltip-text edit" style="margin-left: -36px;">10</span></div><input type="hidden" name="slider" value="10"></div>
        <div class="thermostat">
            <div class="ring">
                <div class="bottom_overlay"></div>
            </div>
            <div class="control">
                <div class="temp_outside">PA: <span id="presion"></span></div>
                <div class="temp_room"><span>°</span></div>
                <div class="room">Hum: <span id="humedad"></span>%</div>
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

```javascript
     $('#presion').html($('body').attr('presion'));
    $('#humedad').html($('body').attr('humedad'));
    var temp = $('body').attr('temperatura');
    // jQuery v3.3.1 is supported
    $("#slider").roundSlider({
	radius: 72,
	circleShape: "half-top",
    sliderType: "min-range",
	mouseScrollAction: true,
    value: temp,
	handleSize: "+5",
	min: 10,
	max: 50
});
</script>
```
### Si abrimos la URL de nuestro sitio veremos algo como esto:
![App Screenshot]([https://via.placeholder.com/468x300?text=App+Screenshot+Here](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/img_tutorial/img2.png)

## PASO 4: Conectar la base de datos a nuestra página web
Ahora veremos  los pasos para conectar los datos de nuestro sensor en la vista publica de nuestro proyecto.

### Parte 1: Mostrar los datos:
Primero crearemos un nuevo archivo que se deberá llamar `conexion.php`, además cambiaremos el nombre de nuestro `index.html` a `index.php`

Dentro del archivo `conexion.php` incluiremos el siguiente código:
```php
<?php
class SQLConexion{
    public $conexion;
    private $server='localhost'; // Nombre del servidor
    private $usuario='conecta'; // Usuario del servidor
    private $clave=''; // Contraseña
    private $bd='conecta'; // Nombre de la base de datos
   
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
?>
```
#### Nota: Solo cambiaras los siguientes datos, donde agregaras la información de tu base de datos

`private $usuario='tu_nombre_de_usuario';`

`private $clave='contraseña_de_tu_bd';`

`private $bd='base_de_datos';`

## Ahora editaremos el archivo index.php
En el **inicio** del archivo, agregaremos las siguientes lineas de código:

```php
<?php
include_once("conexion.php");
$sql = new SQLConexion();
$datos = $sql->selectData("SELECT * FROM tabla;");
$temperatura = $datos[0]['temperatura'];
$presion = $temp = $datos[0]['presion'];
$humedad = $datos[0]['humedad'];
?>
```
En la linea 20, donde esta escrito `<body temperatura="" presion="" humedad="">` agregaremos los valores en medio de las comillas

Para imprimir datos escribiremos `<?php echo $variable; ?>`

Entonces al asignarle los valores nos quedaría algo como esto:

```php
<body temperatura="<?php echo $temperatura; ?>" presion="<?php echo $presion; ?>" humedad="<?php echo $humedad; ?>">
```
## Finalmente podremos ver los datos directamente de la base de datos en nuestro proyecto
![App Screenshot]([https://via.placeholder.com/468x300?text=App+Screenshot+Here](https://raw.githubusercontent.com/GabrielVall/IOT-ESP32-BME280/main/img_tutorial/img3.png)

### Parte 2: Crear archivo para que el esp32 pueda enviar información
Para que nuestro proyecto pueda funcionar, necesitaremos un mediador entre el  `esp32` 
y nuestro `sitio web`,para eso necesitaremos crear otro archivo llamado `insertar.php`

Nuestro archivo deberá incluir el siguiente código:
```php
<?php
if( $_SERVER["REQUEST_METHOD"] == "POST"){
    $equipo = $_POST['equipo'];
    $dispositivo = $_POST['dispositivo'];
    $id = $_POST['id'];
    $temperatura = $_POST['temperatura'];
    $humedad = $_POST['humedad'];
    $presion = $_POST['presion'];
    include_once("conect.php");
    $sql = new SQLConexion();
    $row = $sql->updateData("
    INSERT INTO nombre_de_tu_tabla (equipo,temperatura,humedad,presion,fecha_hora) 
    VALUES ('$equipo', $temperatura, $humedad, $presion, CURRENT_TIMESTAMP);
    ");
    echo "Datos registrados por via POST";
}else{
    echo "No puedes ingresar los datos manualmente.";
}
```
## PASO 5: Conectar el esp32 y subir el código
Para este paso necesitaremos tener el IDE de arduino, este lo puedes descargar desde [aquí](https://www.arduino.cc/en/software) en su página oficial.

`Nota: Si ya tienes instalado arduino y las librerias puedes omitir este paso.`
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
  delay(2500);  
}
```
