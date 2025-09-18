
# 1. Teoria de Markdown — Comandos básicos y avanzados



## 1 Encabezados

Usa `#` para títulos. Cuantos más `#`, más pequeño el encabezado.

```markdown
# Título H1
## Título H2
### Título H3
#### Título H4
```




## 2 Texto
```markdown
**Negrita**
*Cursiva*
~~Tachado~~
***Negrita y cursiva***
```

## 3 Listas
### 3.1 Listas desordenadas
```markdown
- Item 1
- Item 2
  - Subitem
* Otra forma de -
```

### 3.2 Listas ordenadas
```markdown
1. Primer item
2. Segundo item
   1. Subitem
3. Tercer item
```


## 4 Enlaces y URLS
```markdown
[Texto del enlace](https://ejemplo.com)
<https://ejemplo.com>  # enlace automático
```


## 5 Imagenes

```markdown
![Texto alternativo](ruta/de/la/imagen.png)
```

## 6 Codigo

El codigo se puede encapsular con las siguiente etiquetas:
``````markdown
```bash
Escribe aquí dentro lo que sea
```

``````



## 7 Tablas

```markdown
| Encabezado 1 | Encabezado 2 |
|-------------|-------------|
| Celda 1     | Celda 2     |
| Celda 3     | Celda 4     |
```

## 8 Separadores

```markdown
---
***
___
```


## 9 Instalar Typora en Ubuntu

Para la instalación de typora usaremos muchos comandos bash distintos. Por ahora, conviene que os familiariceis con algunos. En el tema 3, veremos un poco sobre los distintos paquetes de las distintas distribuciones. Por ahora, nada de esto.


```bash
sudo mkdir -p /etc/apt/keyrings
rl -fsSL https://downloads.typora.io/typora.gpggg. . sudo tee /etc/apt/keyrings/typora.gpg.gpg
echo "deb [signed-by=/etc/apt/keyrings/typora.gpg] https://downloads.typora.io/linux ./" . sudo tee /etc/apt/sources.list.d/typora.list
sudo apt update
sudo apt install typora
```






# 1. Primera Práctica — Descargar Ubuntu en una VM Windows, copiar la ISO a un USB y usarla en el host para instalar una nueva VM



## Objetivo
- Descargar en la **VM Windows** la ISO de la última versión de Ubuntu Desktop.  
- Pasar la ISO al **host Linux** mediante un **pendrive USB**/ mediante descagar via ftp/ mediante el montado del disco virtual en el que arrancará la máquina cliente en windows/ mediante descarga directa del recurso con wget.  
- Crear una nueva máquina virtual en el host con esa ISO usando **virt-manager**.  


## 1. Descargar la ISO en la VM Windows

1. Inicia la **VM Windows** en virt-manager.  
2. Dentro de Windows, abre un navegador (Edge/Chrome).  
3. Ve a la página oficial: [https://ubuntu.com/download/desktop](https://ubuntu.com/download/desktop)  
4. Descarga la ISO (ejemplo: `ubuntu-25.04-desktop-amd64.iso`).  
5. Guarda la ISO en `C:\Users\<TuUsuario>\Downloads`.



## 2. Preparar un pendrive en el host

1. Inserta el pendrive en el **host físico**.  
2. En el host, identifica el dispositivo:  
   ```bash
   lsblk
   ```

    ```text
    sda    100G
    ├─sda1  96G /
    └─sda2   4G swap
    sdb    16G   ← este es tu pendrive
    └─sdb1 16G
    ```
   
Con el administrados de discos de gnome, prueba a darle formato FAT (el soportado por Windows y distribuciones linux)
Mediante consola de comandos, sería de la siguiente forma, pero NO LO HAREMOS ya que necesitamos verlo previamente...

   ```bash
   sudo apt install -y exfatprogs   # si no está instalado
   sudo umount /dev/sdb   # el dispositivo no puede estar montado en el sistema de archivos
   sudo mkfs.exfat /dev/sdb
   ```



## 3. Pasar el pendrive a la VM Windows (USB passthrough)

1. Abre virt-manager, selecciona tu VM Windows y entra en Detalles de hardware.
2. Con la VM encendida, en el panel izquierdo pulsa Add Hardware → USB Host Device.
3. Elige tu pendrive de la lista y pulsa Finish.
4. Dentro de Windows debería aparecer el pendrive como una nueva unidad (E:\, por ejemplo).


   
## 4. Copiar la ISO al pendrive en Windows

1. En Windows, abre el Explorador de Archivos.
2. Copia la ISO (ubuntu-25.04-desktop-amd64.iso) desde Downloads al pendrive (E:\).
3. Espera a que termine la copia.
4. Usa Quitar hardware con seguridad en Windows para desmontar el USB.



## 5. Volver a montar el pendrive en el host

1. En virt-manager, quita el USB de la VM (o apaga la VM).
2. El host detectará de nuevo el pendrive.
3. Monta el pendrive: para ello, vete a gnome disk y dale al boton de "play". Dejo también el comando, aunque NO debes USARLO.
  ```bash   
  lsblk              # identifica el dispositivo (ej. /dev/sdb1)
  sudo mount /dev/sdb1 /mnt
  ```
4. Copia la ISO al host: esto se hace manejando archivos de forma normal, no hace falta que uses el comando CP.
  ```bash
    mkdir -p ~/isos
    cp /mnt/ubuntu-25.04-desktop-amd64.iso ~/isos/
    sudo umount /mnt
  ```




## 6. Crear una nueva VM Ubuntu en el host con virt-manager
En caso de que el dispositivo USB no funcione, la ISO está compartida tanto por FTP como SMB y así no tienes que hacer caso a los 5 primeros puntos anteriores.
FTP es un protocolo de red para transferir archivos entre un cliente y un servidor. Funciona sobre TCP, típicamente en los puertos 21 (control) y 20 (datos).
Por otro lado, SMB es un protocolo de red para compartir archivos, impresoras y recursos entre computadoras, principalmente en entornos Windows.

### Windows

- \\<IP-del-servidor>\Publica (smb poner usuario Manolo contraseña 123456)

- ftp://alumnosiso:Iso15092025@IP-servidor


### Ubuntu

- ftp://alumnosiso:Iso15092025@IP-servidor

- smb://<IP-servidor>/Publica


## 7. Crear una nueva VM Ubuntu en el host con virt-manager
Si todo lo anterior no funciona lo más limpio es decargar la distribución mediante el comando wget, que permitirá la descarga desde terminal. Otra opción comentada al principio es la de
montar directamente el disco windows donde se ha descargado la iso de ubuntu desktop en tu host. Esto puede ser también un tanto catastrófico ya que requiere de que tengáis permisos de administrador, aunque
el proceso en sí, es simple. Para ello, primero, hay que entender que ubuntu no funciona igual. P

### Primero tenemos que entender que es el directorio /mnt en linux.

- /mnt es una carpeta del sistema reservada como punto de montaje.

- Montar = decirle al sistema operativo:

- “Muestra el contenido de este disco/dispositivo dentro de esta carpeta”.

- Si no hay nada montado, /mnt es una carpeta vacía normal en el host.

### Uso en Discos físicos (ejemplo: un pendrive)

Un pendrive aparece como dispositivo, por ejemplo /dev/sdb1. Para montarlo manualmente:

  ```bash
sudo mount /dev/sdb1 /mnt

  ```
Ahora en /mnt ves los archivos del pendrive.

Si creas algo dentro de /mnt, se escribe en el pendrive.

Cuando desmontas (sudo umount /mnt), el contenido del host en /mnt reaparece (si había algo).



### Ahora bien, aunque similar, para montar discos virtual es un poco distinto. Lo primero es instalar mediante apt una libreria:


  ```bash
sudo guestmount -a /var/lib/libvirt/images/mi_vm.qcow2 -i --ro /mnt
  ```
    -a → ruta al disco virtual.

    -i → auto-detección de particiones y sistemas de archivos.

    --ro → modo solo lectura (muy recomendado si no quieres estropear la VM).

    /mnt → punto de montaje en tu host.


### Por ultimo vemos como pasar la informacion de la VM al host

- Navega hasta: 
```bash
/mnt/Users/manolo/Downloads/
```

- Copiar el archivo windows.iso de esa carpeta al Downloads del usuario manolo del host Ubuntu:

```bash
cp "/mnt/Users/manolo/Downloads/windows.iso" "/home/manolo/Downloads/"
  ```

- Desmontar el disco virtual (es igual que el umount visto en el punto 5.4 de este punto teórico:

```bash
sudo guestunmount /mnt
```





## 8. Crear una nueva VM Ubuntu en el host con virt-manager

Una vez tienes el ISO mediante alguno de los procedimientos anteriores (pen, ftp, montando disco virtual, wget) , se pide que:

1. Abre virt-manager.

2. Pulsa New (Nuevo).

3. Elige Local install media (ISO image or CDROM).

4. Pulsa Browse → Browse Local, selecciona la ISO en ~/isos/.

5. Virt-manager detectará automáticamente que es Ubuntu. Pulsa Forward.

6. Asigna recursos (ejemplo: 4 GB RAM, 2 CPUs, 40 GB disco).

7. 8. Finaliza: la VM arrancará desde la ISO y verás el instalador de Ubuntu.

8. Sigue los pasos de clase para instalar Ubuntu. Recuerda cambiar el idioma del teclado y descargar el paquete gráfico.

9. En caso de que algo vaya mal con gnome-disk, mátalo el proceso con: pkill gnome-disk




---


# 2. Teoria de Almacenamiento de datos

## 2.1. Unidades de medida de la información

En informática, la información se mide en bits y sus múltiplos. Las unidades de medida permiten cuantificar la capacidad de almacenamiento o el tamaño de datos.

1. Bit

Símbolo: b, es la unidad mínima de información con valores posibles 0 o 1. Representa un estado lógico, verdadero/falso, encendido/apagado.

2. Byte

Símbolo: B, representa un conjunto de 8 bits con rango de valores: 0 a 255 (2⁸ posibles combinaciones). Puede representar un carácter de texto, un valor numérico pequeño o un color de 8 bits.

3. Resto de medidas


| Unidad   | Símbolo | Equivalencia binaria                    | Equivalencia decimal             | Uso típico                        |
| -------- | ------- | --------------------------------------- | -------------------------------- | --------------------------------- |
| Kilobyte | KB      | 2¹⁰ bytes = 1.024 B                     | 10³ bytes = 1.000 B              | Pequeños archivos de texto        |
| Megabyte | MB      | 2²⁰ bytes = 1.048.576 B                 | 10⁶ bytes = 1.000.000 B          | Archivos de música, imágenes      |
| Gigabyte | GB      | 2³⁰ bytes = 1.073.741.824 B             | 10⁹ bytes = 1.000.000.000 B      | Memoria RAM, discos duros         |
| Terabyte | TB      | 2⁴⁰ bytes ≈ 1.099.511.627.776 B         | 10¹² bytes = 1.000.000.000.000 B | Almacenamiento masivo, servidores |
| Petabyte | PB      | 2⁵⁰ bytes ≈ 1.125.899.906.842.624 B     | 10¹⁵ bytes                       | Centros de datos grandes          |
| Exabyte  | EB      | 2⁶⁰ bytes ≈ 1.152.921.504.606.846.976 B | 10¹⁸ bytes                       | Internet global (teórico)         |

## Formas de guardar la información

- Cuando hablamos de cómo se guarda la información en un ordenador, siempre debemos volver a lo esencial: todo se reduce a bits, ceros y unos. Estos valores, que representan estados eléctricos o magnéticos, necesitan un soporte físico en el que almacenarse para que el sistema pueda conservarlos incluso cuando se apaga la máquina.

- Durante décadas, ese soporte ha sido el disco duro mecánico o HDD. Un HDD funciona con platos recubiertos de material magnético que giran a gran velocidad. Un cabezal de lectura y escritura se desplaza sobre esos platos, cambiando la orientación de pequeñísimas zonas magnéticas para representar un 0 o un 1. La información no se almacena de forma caótica, sino organizada en sectores (unidades mínimas de almacenamiento, normalmente de 512 bytes o 4 KB), que a su vez forman pistas y cilindros. Esta organización permite que el sistema operativo localice y lea los datos de manera ordenada.

- Más recientemente aparecieron los SSD o discos de estado sólido, que funcionan de una manera completamente diferente. En lugar de partes mecánicas y magnéticas, los SSD utilizan memoria flash NAND. Cada bit de información queda guardado como una carga eléctrica dentro de transistores microscópicos. La ventaja fundamental es que no hay partes móviles: la lectura y escritura es casi instantánea y mucho más fiable a largo plazo frente a golpes o vibraciones. La información se organiza en páginas (que suelen ser de 4 a 16 KB). Esas páginas no están sueltas: se agrupan en bloques, normalmente de 1 MB (varias páginas juntas), pero el principio sigue siendo el mismo: representar ceros y unos que luego el sistema operativo agrupa en archivos y carpetas. El borrado siempre se hace a nivel de bloque completo, es decir, la unidad mínima de borrado es 1MB.

- Y aquí entra en juego una herramienta clásica de Linux: el comando dd. Este programa trabaja copiando datos a bajo nivel, en bloques cuyo tamaño puede configurarse con el parámetro bs (block size). Si usas dd con un tamaño de bloque alineado con las páginas o bloques físicos del SSD (por ejemplo, 4K o 1M), el rendimiento será mucho mejor. Si eliges tamaños muy pequeños (1K, 512 bytes), el SSD tiene que hacer más operaciones internas.

  - En un SSD, la unidad mínima física de escritura es la página (4 KB – 16 KB).

  - Si usas dd con un tamaño de bloque (bs) que coincide con la página física (por ejemplo, 4K), la escritura se alinea bien y es eficiente. Supongamos que quiero llenar un SSD con datos de prueba:
  
    - if = input file (/dev/urandom, datos aleatorios).

    - of = output file (prueba.img).
  
    - bs=1M → escribo en bloques de 1 MB.

    - Esto coincide bastante con el tamaño de un bloque interno del SSD (≈128 páginas de 8K = 1 MB).



## 2.2 Consola. Linea de comandos.

La interfaz de línea de comandos, en inglés Command Line Interface o CLI, es el conjunto de elementos visuales que permiten, sobre un dispositivo de salida, indicar órdenes o comandos utilizando un dispositivo de entrada. Concretamente se utiliza el teclado para invocar comandos configurados para realizar una serie de acciones y el resultado de la ejecución de estas acciones se mostrará por pantalla.

En cualquier CLI se pueden encontrar los siguientes elementos, que se estudian con el caso concreto de un terminal Linux (figura 1.13):

- Prompt de usuario: indica el nombre del usuario (fer), el nombre de la máquina (fer-pc), el directorio en el que se encuentra actualmente (~/Documentos) y si el usuario tiene permisos de administrador (#) o es un usuario corriente ($).

- Comando: programa que va a ejecutar el usuario (echo).

- Parámetros: valores de configuración que se dan al programa (“Hola ISO”). Un tipo de parámetros especiales son los flags (también llamado opción o switch) es un parámetro que se añade a un comando para modificar su comportamiento.

- Operadores: elementos para enlazar la salida de un programa con la entrada de otro o con la escritura de datos en ficheros.

- Salida: datos o información que ofrece el programa como resultado de su procesamiento.

## 2.3 Ejemplos en ubuntu:
Uno de los comandos más comunes que veremos cuando listemos la estructura de un directorio en linux (siguiente tema), será el comando ls. En los siguientes ejemplos se muestran casos sencillos de uso dividiendo en la terminología usada anteriormente. La estructura de directorio de un debion también se verá en el siguiente tema y no debe ser objeto de preocupación del alumnado.

### Ejemplo 1: ls simple

```bash
fer@pc:~/Documentos$ ls
```

Prompt de usuario: fer@pc:~/Documentos$

Comando: ls

Parámetro: (ninguno en este caso)

Operadores: (ninguno)

Salida: lista de archivos del directorio actual, ej.:
- informe.txt
- tareas.pdf
- notas.csv

### Ejemplo 2: ls con parámetro (otro directorio)

```bash
fer@pc:~$ ls /etc
```

Prompt de usuario: fer@pc:~$

Comando: ls

Parámetro: /etc (indico el directorio que quiero listar)

Operadores: (ninguno)

Salida:
-hosts
-passwd
-shadow
-ssh


### Ejemplo 3: ls con flag (parámetro especial)

```bash
fer@pc:~/Descargas$ ls -l
```

Prompt de usuario: fer@pc:~/Descargas$

Comando: ls

Parámetro (flag): -l (listado largo)

Operadores: (ninguno)

Salida:
```bash
-rw-r--r-- 1 fer fer  1024 sep  1 10:00 informe.txt
drwxr-xr-x 2 fer fer  4096 sep  2 11:30 proyectos
```


### Ejemplo 4: ls con operador de redirección

```bash
fer@pc:~$ ls /bin > lista.txt
```

Prompt de usuario: fer@pc:~$

Comando: ls

Parámetro (flag): /bin

Operadores: > (redirige la salida a un archivo)

Salida: no se muestra nada en pantalla; se guarda en lista.txt.



# 2. Práctica — Observar la diferencia en notación decimal vs binaria. Probamos la CLI de Ubuntu.

Habitualmente, cuando compramos un disco duro, lo solecmos comprar con "unidades redondas", es decir, 1tb, 500gb...
Sin embargo, a la hora de ver el disco dentro de nuestro ordenador, nos encontramos con sorpresas.
En esta práctica, usando la maquina virtual de Ubuntu, vais a comprobar el tamaño real de vuestro disco duro y a relacionarlo con las unidades de medida de información que hemos estudiado.
Entrega un Mardown via PDF explicando cada paso.


## 1. Abrir la terminal

Presiona `Ctrl + Alt + T` para abrir la terminal en Ubuntu.



## 2. Listar los discos y particiones

Escribe el siguiente comando:
```bash
lsblk -o NAME,SIZE,TYPE
```

Haz clasificación de comando según: Prompt de usuario, Comando, Parámetro, Operadores, Salida y observa los discos (TYPE=disk) y sus tamaños (SIZE).


## 3. Obtener el tamaño exacto en bytes
Escribe:

```bash
sudo fdisk -l
```

Haz clasificación de comando según: Prompt de usuario, Comando, Parámetro, Operadores, Salida y busca la línea que corresponda a tu disco, por ejemplo:

```bash
Disk /dev/sda: 500107 MB, 500107862016 bytes
```

Anota el tamaño en bytes y comprueba cómo se traduce a GB: GB = bytes ÷ 1.073.741.824 ¿Por qué sale este número, es un número redondo como los que anuncian los fabricantes?



## 4. Obtener el tamaño exacto en bytes
Intenta sacar esta información (de alguna forma) en un sistema opeerativo windows a través de tu VM.


## 5. ¿Qué tipo de CLI puede usar Windows? Busca información al respecto.
Intenta sacar esta información (de alguna forma) en un sistema opeerativo windows a través de tu VM.

## 6. ¿Qué es un Live CD o un disco booteable? Busca información al respecto de la relación entre dd y un Live CD, así como precauciones del comando dd.


---

# 3. Teoría de Sistemas Numéricos en Informática

## 1. Sistema Binario

El sistema binario es la base de la informática moderna, ya que los circuitos electrónicos manejan dos estados: encendido (1) y apagado (0).

### 1.1 Definición

Sistema de numeración en base 2. Solo utiliza los dígitos 0 y 1.

### 1.2 Uso en informática

Representa bits, el nivel más básico de información. Todos los datos y operaciones internas de la CPU se manejan en binario.

Ejemplos:
| Decimal | Binario |
| ------- | ------- |
| 0       | 0       |
| 1       | 1       |
| 2       | 10      |
| 3       | 11      |
| 4       | 100     |
| 5       | 101     |
| 6       | 110     |
| 7       | 111     |
| 8       | 1000    |
| 9       | 1001    |
| 10      | 1010    |



## 2. Sistema Hexadecimal

El sistema hexadecimal es un sistema de base 16 muy usado en informática para representar grandes cantidades de bits de forma más compacta.

### 1.1 Definición

Base 16: utiliza los dígitos 0-9 y las letras A-F (A=10, B=11, …, F=15). Cada dígito hexadecimal equivale exactamente a 4 bits (medio byte o nibble).

### 1.2 Uso en informática

Representar direcciones de memoria, valores de bytes y colores en programación. Facilita la lectura y escritura de números binarios largos.


| Hexadecimal | Binario | Decimal |
| ----------- | ------- | ------- |
| 0           | 0000    | 0       |
| 1           | 0001    | 1       |
| 2           | 0010    | 2       |
| 3           | 0011    | 3       |
| 4           | 0100    | 4       |
| 5           | 0101    | 5       |
| 6           | 0110    | 6       |
| 7           | 0111    | 7       |
| 8           | 1000    | 8       |
| 9           | 1001    | 9       |
| A           | 1010    | 10      |
| B           | 1011    | 11      |
| C           | 1100    | 12      |
| D           | 1101    | 13      |
| E           | 1110    | 14      |
| F           | 1111    | 15      |




## 3. Convertir sistemas


### 3.1 Bineario a hexadecimal

El sistema binario usa base 2 (solo 0 y 1) y el hexadecimal usa base 16 (0–9 y A–F).
La conversión es muy sencilla porque 1 dígito hexadecimal equivale exactamente a 4 bits (un nibble).

### Paso 1

Toma el número binario que quieres convertir. Agrupa los bits de derecha a izquierda en bloques de 4. Si el último grupo de la izquierda tiene menos de 4 bits, agrega ceros a la izquierda para completar el grupo.

### Paso 2

Convertir cada bloque a hexadecimal mirando la tabla siguiente ya mencionada antes
¿Qué se hace si el numero tiene 10 bits? Pensarlo


| Hexadecimal | Binario | Decimal |
| ----------- | ------- | ------- |
| 0           | 0000    | 0       |
| 1           | 0001    | 1       |
| 2           | 0010    | 2       |
| 3           | 0011    | 3       |
| 4           | 0100    | 4       |
| 5           | 0101    | 5       |
| 6           | 0110    | 6       |
| 7           | 0111    | 7       |
| 8           | 1000    | 8       |
| 9           | 1001    | 9       |
| A           | 1010    | 10      |
| B           | 1011    | 11      |
| C           | 1100    | 12      |
| D           | 1101    | 13      |
| E           | 1110    | 14      |
| F           | 1111    | 15      |


### 3.2 Hexadecimal al Binario

Es exactamente lo mismo, al reves. Cada dígito del hexadecimal representa un número binario.


### 3.3 Decimal al Binario

El sistema decimal usa base 10 (0–9), y el sistema binario usa base 2 (0 y 1).
Para pasar de decimal a binario, usamos el método de divisiones sucesivas:

Toma el número decimal que quieres convertir.

Divídelo entre 2.

Anota el residuo (0 o 1).

Divide el cociente entre 2 nuevamente y repite el proceso hasta que el cociente sea 0.

```text
13 ÷ 2 = 6 residuo 1
6 ÷ 2  = 3 residuo 0
3 ÷ 2  = 1 residuo 1
1 ÷ 2  = 0 residuo 1
```


# 3. Práctica — Observar la diferencia en notación decimal vs binaria. Rellena la siguiente tabla
Entrega en Markdown (via PDF) la explicación de las operaciones.

| Decimal | Binario  | Hexadecimal |
| ------- | -------- | ----------- |
| 5       |          |             |
| 12      |          |             |
| 25      |          |             |
| 60      |          |             |
|         | 11010110 |             |
|         | 10101100 |             |
|         |          | 1F          |
|         |          | A7          |



----

# 4. Teoria — Hardware básico de un ordenador. CPU y RAM


## 1. ¿Qué es la CPU?

La CPU (Unidad Central de Procesamiento) es el cerebro del ordenador. Se encarga de ejecutar instrucciones de los programas y procesa datos que se encuentran en la memoria o que recibe de dispositivos de entrada.
Está formada por varias subunidades internas, como la unidad aritmético-lógica (ALU) y la unidad de control.
Los registros son pequeñas áreas de memoria dentro de la CPU que almacenan temporalmente datos o direcciones. Son mucho más rápidos que la RAM y cada registro puede almacenar una cantidad fija de bits, según la arquitectura (8, 16, 32 o 64 bits). 




| Tipo de registro                    | Ejemplos       | Uso principal                                              | Ejemplo práctico                                                                                                  |
| ----------------------------------- | -------------- | ---------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| **Registros de propósito general**  | AX, BX, CX, DX | Almacenar temporalmente datos y resultados de operaciones  | `MOV AX, 5` → almacena 5 en AX; `ADD AX, BX` → suma BX a AX                                                       |
| **Registros de segmento**           | CS, DS, SS, ES | Contienen direcciones base de segmentos de memoria         | `MOV AX, [DS:1234h]` → lee un valor desde un segmento de datos DS. El registro DS tiene, por ejemplo, el valor 2000h. a CPU suma DS + 1234h|
| **Puntero de instrucción**          | IP/EIP/RIP     | Apunta a la siguiente instrucción que la CPU debe ejecutar | Antes de ejecutar la instrucción MOV AX, BX, el registro de puntero de instrucción (IP) contiene el valor 2000h, es decir, apunta al inicio de esa instrucción en memoria. Cuando la CPU empieza a ejecutar MOV AX, BX, IP sigue indicando 2000h mientras la CPU lee la instrucción y copia el valor de BX a AX. Una vez que la instrucción se ha completado, la CPU incrementa automáticamente el IP en 3 bytes (el tamaño de la instrucción) para que ahora apunte a la siguiente instrucción en memoria, ADD AX, 5, que comienza en la dirección 2003h.|


La unidad aritmético-lógica (ALU) es la parte de la CPU que se encarga de realizar todas las operaciones matemáticas y lógicas. Por ejemplo, suma, resta, multiplicación, división, así como operaciones lógicas como AND, OR, XOR o comparaciones entre números. Cada vez que ejecutamos una instrucción como ADD AX, BX, la ALU calcula el resultado de la operación y actualiza los indicadores del estado de la CPU, como la bandera de cero o de acarreo.

Por otro lado, la unidad de control (CU) coordina todo el funcionamiento de la CPU. Su función principal es decodificar las instrucciones, decidir qué señales enviar a la ALU, a los registros y a los buses de memoria, y controlar el flujo de datos dentro de la CPU. (MOV AX, [1234h]) Gracias a la unidad de control, la CPU sabe qué operación ejecutar, qué registros usar y cuándo leer o escribir datos en la memoria.





## 2. ¿Qué es la RAM?
La RAM es la memoria principal del sistema. Se organiza en direcciones lineales, cada dirección contiene 1 byte, ¿qué es un byte? Mira lo explciado anteriormente
La CPU puede leer o escribir en la RAM mediante el bus de direcciones (dice dónde) y el bus de datos (dice qué valor).

Ejemplo: la instrucción

```bash
MOV AX, [1234h]
```

La CPU coloca 1234h en el bus de direcciones.
Activa una lectura de memoria. El valor que hay en RAM en esa dirección entra por el bus de datos y se guarda en AX.


# 4. Práctica — Curiosidades y importancia de los sistemas de enumeración binario y hexadecimal en la informatica.

Entrega en formato PDF los puntos; 4.2 y 4.3


## 4.1 Traduciendo a esamblador, da la instrucción de forma correcta en hexadecimal (Lo resolvemos en clase).

| Nº | Instrucción a realizar                     | Registro/Dato  | Dirección en decimal | Dirección en hexadecimal |
| -- | ------------------------------------------ | -------------- | -------------------- | ------------------------ |
| 1  | Mover un valor a un registro               | AX             | 4660                 | ?                        |
| 2  | Mover un valor desde memoria a un registro | BX             | 12345                | ?                        |
| 3  | Sumar el contenido de un registro a otro   | AX + BX        | –                    | –                        |
| 4  | Mover un valor inmediato a un registro     | CX             | –                    | –                        |
| 5  | Sumar un valor de memoria a un registro    | DX + \[Memory] | 54321                | ?                        |


MOV AX, [-]
MOV BX, [-]
ADD AX, BX
MOV CX, 10
ADD DX, [-]


## 4.2 Codificaciones de textos

El texto que ves en tu pantalla no es más que una codificación del lenguaje binario. Todo lo que se muestra en la pantalla, ya sean letras, números o símbolos, se traduce finalmente a ceros y unos, que la computadora puede interpretar.
A lo largo de la historia de la informática, se han desarrollado distintas codificaciones de caracteres para representar texto en binario: EBCDIC, ASCII,Extended ASCII y Unicode,

Establece una linea temporal de las distintas codificaciones, dando ejemplos y el número de bits que utliza cada una así como el número total de caracteres posibles. Si un tipo de codificación admitía hasta 7 bits: ¿cuál es la cantidad de carácteres admitidos en total?

| Carácter | UTF-8 (binario)                       | Unicode (hexadecimal) |
| -------- | ------------------------------------- | --------------------- |
| A        | `01000001`                            | (rellenar)  
| ñ        | `11000011 10110001`                   | (rellenar)            |
| €        | (rellenar)                                | U+20AC            |
| 😂       | (rellenar)                             | U+1F602            |
| 中        | `11100100 10111000 10101101`          | (rellenar)            |



## 4.3 Codificaciones de textos

En la arquitectura x86 de 32 bits, los ordenadores tenían un límite teórico de 4GB de ram, ¿por qué? ¿cuál es el límite teórico a día de hoy?



----

# 5. Práctica — Comparación GUI vs CLI en Ubuntu e instalación de KDE
Abre tu máquina virtual de Ubuntu. Los objetivos de estas práctica son: Comprender las diferencias entre trabajar con GUI y CLI en Ubuntu, realizar tareas equivalentes en ambos entornos e instalar y probar un nuevo desktop environment (KDE Plasma) mediante CLI. Haz capturas del proceso y entrega un archivo pdf.

## Ejecuta el siguiente código en bash

```bash
uname -a
lsb_release -a
```
- A partir de este comando, saca la versión del kernel y busca en información del sistema a través la información proporcionada por el comando.



## Ejecuta el siguiente código en bash

```bash
ps -eo comm,%cpu,%mem --sort=-%mem | head -15
```

![Texto alternativo](Imágenes/captura_practica2.png)



- Busca en monitorización del sistema a través la información proporcionada por el comando.



![Texto alternativo](Imágenes/captura_practica.png)



## Instala la GUI de KDE y pruebala al cerrar sesión

```bash
sudo apt install kde-plasma-desktop -y
```

