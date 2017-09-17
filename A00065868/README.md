### Taller 3
**Universidad ICESI**  
**Curso:** Sistemas Operativos  
**Docente:** Daniel Barragán C.  
**Estudiante:** Ana Valderrama V.
**Código:** A00065868  
**Tema:** Llamadas al sistema (syscalls)  
**Correo:** daniel.barragan at correo.icesi.edu.co


### Objetivos
* Conocer y explicar la función de las llamadas al sistema en un sistema operativo

### Prerrequisitos
* Virtualbox o WMWare
* Máquina virtual con sistema operativo CentOS7
* Aplicativo strace

### Descripción
El tercer taller del curso sistemas operativos trata sobre las llamadas al sistema y su importancia para el sistema operativo

### Actividades

1. Empleando el aplicativo **strace** obtenga 5 llamadas al sistema para uno o varios comandos de linux. Explique por qué los comandos seleccionados emplean las llamadas al sistema encontradas, para ello debe emplear los manuales de Linux en Internet o del sistema operativo (comando **man**). Debe incluir la explicación de los parámetros que reciben las llamadas al sistema encontradas. Consigne capturas de pantalla donde muestre las llamadas al sistema obtenidas (sugerencia: emplear -etrace para filtrar los resultados)

**Comando:** kill
![][1]  
Este comando se ejecutó desde root y su principal función es terminar un proceso específico, recibe como parámetro el proceso que se desea detener (enviando una señal específica a él).  
  
| Syscall | Uso en kill | Parámetros  |
|------|------|------|
| brk | Define en que segmento finaliza el programa | **addr** es un valor de memoria al cual se van a dirigir los datos, si es un valor razonable y el sistema tiene suficiente memoria el proceso se lleva a cabo. |  
| mprotect | Cambia las protecciones de acceso a las que el proceso quiere acceder | **addr** es un valor de memoria al cual el proceso desea acceder.  **len** la longitud de memoria del sistema el rango al cual el proceso puede accder esta entre [addr, addr+len-1].  **prot** es la combinación de unas banderas de acceso a memoria.  |
| fstat | Lee las estadísticas/estado del archivo | **fd** es el descriptor de archivo que hace referencia al archivo del cual se retornará su estado.  **statbuf** define hacia donde irá la información retornada.  |
| execve | Ejecuta el programa asociado | **filename** archivo al cual apunta donde se encuentra el programa a ejecutar.  **argv[]** un arreglo de argumentos de tipo String para que el programa sea ejecutado, por ejemplo argv[0] contiene el nombre del archivo que será ejecutado.  **envp[]** es un arreglo que contiene el ambiente del nuevo programa. |
| arch_prtcl | Cambia una arquitura específica para el proceso | **code** selecciona una subfunción que será aplicada a una dirección específica.  **addr** long sin signo al que se le van a aplicar las operaciones, dirección de memoria.  |

**Llamadas al sistema de kill**  

![][2]  
**5 llamadas al sistema kill -L**  

![][3]


2. Realice la compilación del código fuente adjunto y su ejecución empleando el aplicativo **strace**. Identifique las llamadas al sistema encargadas de enviar y recibir datos a través de la red. A partir de los manuales de Linux en Internet o del sistema operativo explique las llamadas al sistema encontradas y sus parámetros.  
  
Se compila y se ejecuta el código adjunto curl.c  

![][4]  
![][5]  
![][6]  

Posteriormente con el aplicativo strace -c se observan todas las llamadas al sistema que ocurren con la ejecución del código  
  
 ![][7]  
 ![][8]  
   
 Las llamadas al sistema que se encargan de enviar y recibir datos de la red son sendto y recvfrom respectivamente, socket solo sirve para establcer el canal de comunicación:

| Syscall | Función | Parámetros  |
|------|------|------|
| sendto | Es usada para transmitir un mensaje a otro socket (la red, por ejemplo) | **sockfd** es el decriptor de archivo de el socket que se esta enviando. **message** apunta a un buffer que contiene el mensaje que va a ser enviado. **length** especifica el tamaño del mensaje que va a ser enviado [en bytes]. **flags** especifica el tipo de transmisión. |
| recvfrom | Es usada para recibir los mensajes provenientes de un socket | **sockfd** es el decriptor de archivo de el socket por medio del cual se estan recibiendo datos. **buf** apunta a un buffer en el cual se almacenará el mensaje. **length** especifica el tamaño del buffer al cual se esta apuntando. [en bytes]. **flags** especifica el tipo de recepción del mensaje. |

  
![][9]  
  
Por ejemplo, en este caso el identificador del socket establecido para enviar y recibir datos es 3, lo siguiente al 3 es el buffer del cual se están enviando y al cual se está destinando los datos, el **mensaje enviado** tiene un tamaño de 50 bytes y el **mensaje recibido** un tamaño de 16384. 

**Debian**
```
# apt-get install libcurl4-openssl-dev
$ gcc -o curl curl.c -lcurl
```
**CentOS**
```
# yum install libcurl-devel
$ gcc -o curl curl.c -lcurl
```

### Nota

El informe debe ser entregado en formato README.md y debe ser subido a un repositorio de github. El repositorio de github debe ser un fork de https://github.com/ICESI-Training/so-workshop3 y para la entrega deberá hacer un Pull Request (PR) respetando la estructura definida. El código fuente y la url de github deben incluirse en el informe.  

## Referencias

* http://man7.org/linux/man-pages/man2/syscalls.2.html  
* https://jvns.ca/blog/2014/09/18/you-can-be-a-kernel-hacker/

[1]: images/killcommand.PNG
[2]: images/killcommand_syscalls.PNG
[3]: images/syscalls_killL.PNG
[4]: images/ejecucioncurl1.PNG
[5]: images/ejecucioncurl2.PNG
[6]: images/ejecucioncurl3.PNG
[7]: images/syscalls_curl.PNG
[8]: images/syscalls_curl2.PNG
[9]: images/syscalls_sendreceive.PNG
