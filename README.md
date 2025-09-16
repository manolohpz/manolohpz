
# 📝 Apuntes de Markdown — Comandos básicos y avanzados

---

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
![Alt](https://ejemplo.com/imagen.jpg)
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











# Práctica — Descargar Ubuntu en una VM Windows, copiar la ISO a un USB y usarla en el host para instalar una nueva VM

---

## Objetivo
- Descargar en la **VM Windows** la ISO de la última versión de Ubuntu Desktop.  
- Pasar la ISO al **host Linux** mediante un **pendrive USB**.  
- Crear una nueva máquina virtual en el host con esa ISO usando **virt-manager**.  

---

## 1. Descargar la ISO en la VM Windows

1. Inicia la **VM Windows** en virt-manager.  
2. Dentro de Windows, abre un navegador (Edge/Chrome).  
3. Ve a la página oficial: [https://ubuntu.com/download/desktop](https://ubuntu.com/download/desktop)  
4. Descarga la ISO (ejemplo: `ubuntu-25.04-desktop-amd64.iso`).  
5. Guarda la ISO en `C:\Users\<TuUsuario>\Downloads`.

---

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
   sudo mkfs.exfat /dev/sdb1
   ```

---

## 3. Pasar el pendrive a la VM Windows (USB passthrough)

1. Abre virt-manager, selecciona tu VM Windows y entra en Detalles de hardware.
2. Con la VM encendida, en el panel izquierdo pulsa Add Hardware → USB Host Device.
3. Elige tu pendrive de la lista y pulsa Finish.
4. Dentro de Windows debería aparecer el pendrive como una nueva unidad (E:\, por ejemplo).

---
   
## 4. Copiar la ISO al pendrive en Windows

1. En Windows, abre el Explorador de Archivos.
2. Copia la ISO (ubuntu-25.04-desktop-amd64.iso) desde Downloads al pendrive (E:\).
3. Espera a que termine la copia.
4. Usa Quitar hardware con seguridad en Windows para desmontar el USB.

---

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


---

## 6. Crear una nueva VM Ubuntu en el host con virt-manager
En caso de que el dispositivo USB no funcione, la ISO está compartida tanto por FTP como SMB y así no tienes que hacer caso a los tres puntos anteriores.
FTP es un protocolo de red para transferir archivos entre un cliente y un servidor. Funciona sobre TCP, típicamente en los puertos 21 (control) y 20 (datos).
Por otro lado, SMB es un protocolo de red para compartir archivos, impresoras y recursos entre computadoras, principalmente en entornos Windows.

### Windows

\\<IP-del-servidor>\Publica (smb poner usuario Manolo contraseña 123456)
ftp://alumnosiso:Iso15092025@<IP-servidor>


### Ubuntu

ftp://alumnosiso:Iso15092025@<IP-servidor>
smb://<IP-servidor>/Publica


## 7. Crear una nueva VM Ubuntu en el host con virt-manager

1. Abre virt-manager.

2. Pulsa New (Nuevo).

3. Elige Local install media (ISO image or CDROM).

4. Pulsa Browse → Browse Local, selecciona la ISO en ~/isos/.

5. Virt-manager detectará automáticamente que es Ubuntu. Pulsa Forward.

6. Asigna recursos (ejemplo: 4 GB RAM, 2 CPUs, 20 GB disco).

7. Finaliza: la VM arrancará desde la ISO y verás el instalador de Ubuntu.

8. En caso de que algo vaya mal con gnome-disk, mátalo el proceso con: pskill gnome-disk
