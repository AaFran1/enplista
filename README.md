# enplista

Para comenzar, veamos las variables de nivel de clase. PortList de enumeración es una lista de puertos devuelta por el controlador. El ComPortIdentifier, portId, es el puerto real con el que estamos interesados en trabajar, mientras que SerialPort es el java.comm.SerialPort real que usaremos para leer y escribir paquetes de datos con nuestro microcontrolador. InputStream y OutputStream, con nombres de variable del mismo nombre, son los flujos devueltos de nuestro SerialPort en funcionamiento.

El byte [] readBuffer será un búfer temporal que se utilizará mientras se lee el flujo de entrada de nuestro puerto serie. El booleano, dataIn, es una bandera indicadora que le permite a la clase saber cuánto tiempo leer el flujo de entrada en readBuffer. Finalmente, las dos últimas variables currentWrite ei se utilizan junto con el comportamiento de lectura y escritura implementado en WebSerialClient.

## Constructores

Pasando a los constructores de la clase, creé dos constructores para esta clase simplificando la creación del parámetro de entrada a solo baudios e id. Debido a que solo estoy comprobando si el puerto termina con un número específico (por ejemplo, 1,2,3, ..., n), esta clase funcionará con puertos serie basados en Windows o UNIX. En el caso de que no se establezcan los baudios, me decidí por una velocidad en baudios predeterminada de 9600. Cada constructor también llama a un método de inicio común. Dentro de este método, encontrará la misma lógica utilizada en [ListOpenPorts](https://bitcu.co/192-168-10-1/). 

Una vez que se crea una enumeración, repito esta enumeración hasta que encuentro los puertos seriales (específicamente el que termina con el int pasado como parámetro de entrada). Luego abro una instancia con nombre de este puerto y configuro los parámetros predeterminados en ocho bits de datos, un bit de parada, sin paridad y sin control de flujo. A continuación, configuro el flujo de entrada y el flujo de salida del puerto para que sean iguales a dos variables de clase del mismo nombre, seguido de agregar el detector de eventos y el indicador notifyOnDataAvailable.

Los dos métodos de pausa están ahí para esperar a que el controlador establezca los valores de los puertos. He descubierto en mi máquina con Windows XP que si no me detengo un poco, los controladores a veces devuelven excepciones. Puede eliminarlos de su máquina si lo desea, solo asegúrese de realizar una prueba de esfuerzo probando muchos puertos abiertos y cerrados en un bucle.

A continuación, discutiré las clases de implementación de la interfaz JSerialPort. El método read () es realmente un método que permanece inactivo hasta que el búfer de entrada está vacío. Una vez vacío, devuelve el contenido de readBuffer. El método readString () simplemente llama al método read () y luego convierte los bytes en una lista delimitada por tilde como una cadena. Dado que todos los datos que provienen del microcontrolador serán un flujo de bytes, quería una forma de leer bytes individuales sin tener que preocuparme de que se conviertan en un carácter que no se pueda convertir fácilmente en un int.

El método write () es un paso a través del flujo de salida con la excepción de agregar el contenido al byte currentWrite []. Guardo esto porque quiero ignorarlo si el microcontrolador me lo devuelve mientras leo el flujo de entrada.

El método close () cierra SerialPort.

Los otros dos métodos, read () y readString () con parámetros de entrada, no se usan pero están nuevamente ahí porque la interfaz jSerialPort los requiere.
El único evento que me importa es el evento DATA_AVAILABLE. Cuando se activa este evento, inicializaré una matriz de 32 bytes y luego, mientras inputStream está disponible, leeré el flujo de entrada en readBuffer. Luego realizo algo de lógica para asegurarme de que los datos devueltos no sean iguales a los datos enviados, y si ese es el caso, configuro el valor de dataIn Boolean en verdadero.

De los últimos tres métodos, setTimeout () no se usa, y setDTR () y getName () están ahí para proporcionar acceso limitado a sus correspondientes variables SerialPort.
