### Escuela Colombiana de Ingeniería
### Arquitecturas de Software - ARSW

### Santiago Forero Yate, Juan Sebastian Cepeda Saray

## Escalamiento en Azure con Maquinas Virtuales, Sacale Sets y Service Plans

### Dependencias
* Cree una cuenta gratuita dentro de Azure. Para hacerlo puede guiarse de esta [documentación](https://azure.microsoft.com/es-es/free/students/). Al hacerlo usted contará con $100 USD para gastar durante 12 meses.

### Parte 0 - Entendiendo el escenario de calidad

Adjunto a este laboratorio usted podrá encontrar una aplicación totalmente desarrollada que tiene como objetivo calcular el enésimo valor de la secuencia de Fibonnaci.

**Escalabilidad**
Cuando un conjunto de usuarios consulta un enésimo número (superior a 1000000) de la secuencia de Fibonacci de forma concurrente y el sistema se encuentra bajo condiciones normales de operación, todas las peticiones deben ser respondidas y el consumo de CPU del sistema no puede superar el 70%.

### Parte 1 - Escalabilidad vertical

1. Diríjase a el [Portal de Azure](https://portal.azure.com/) y a continuación cree una maquina virtual con las características básicas descritas en la imágen 1 y que corresponden a las siguientes:
    * Resource Group = SCALABILITY_LAB
    * Virtual machine name = VERTICAL-SCALABILITY
    * Image = Ubuntu Server 
    * Size = Standard B1ls
    * Username = scalability_lab
    * SSH publi key = Su llave ssh publica

![Imágen 1](images/part1/part1-vm-basic-config.png)

2. Para conectarse a la VM use el siguiente comando, donde las `x` las debe remplazar por la IP de su propia VM (Revise la sección "Connect" de la virtual machine creada para tener una guía más detallada).

    `ssh scalability_lab@xxx.xxx.xxx.xxx`

3. Instale node, para ello siga la sección *Installing Node.js and npm using NVM* que encontrará en este [enlace](https://linuxize.com/post/how-to-install-node-js-on-ubuntu-18.04/).
4. Para instalar la aplicación adjunta al Laboratorio, suba la carpeta `FibonacciApp` a un repositorio al cual tenga acceso y ejecute estos comandos dentro de la VM:

    `git clone <your_repo>`

    `cd <your_repo>/FibonacciApp`

    `npm install`

5. Para ejecutar la aplicación puede usar el comando `npm FibinacciApp.js`, sin embargo una vez pierda la conexión ssh la aplicación dejará de funcionar. Para evitar ese compartamiento usaremos *forever*. Ejecute los siguientes comando dentro de la VM.

    ` node FibonacciApp.js`

6. Antes de verificar si el endpoint funciona, en Azure vaya a la sección de *Networking* y cree una *Inbound port rule* tal como se muestra en la imágen. Para verificar que la aplicación funciona, use un browser y user el endpoint `http://xxx.xxx.xxx.xxx:3000/fibonacci/6`. La respuesta debe ser `The answer is 8`.

![](images/part1/part1-vm-3000InboudRule.png)

7. La función que calcula en enésimo número de la secuencia de Fibonacci está muy mal construido y consume bastante CPU para obtener la respuesta. Usando la consola del Browser documente los tiempos de respuesta para dicho endpoint usando los siguintes valores:
    * 1000000
      > Con el tamaño inicial de la maquina (B1ls):
      > 
      >![image](https://github.com/santiforero1018/LAB9_ARSW/assets/89321404/30561e21-41fd-4e31-8531-0ebd7190df3a)
      >
      > Con el tamaño B2ms:
      > 
      >  ![image](https://github.com/santiforero1018/LAB9_ARSW/assets/88952698/be22a0ff-f0f1-4095-a61c-e693ec011800)
      > 

    * 1010000
      > Con el tamaño inicial de la maquina (B1ls):
      > 
      >![image](https://github.com/santiforero1018/LAB9_ARSW/assets/89321404/a76310da-0fc6-4a62-a386-f1308f2dc162)
      >
      > Con el tamaño B2ms:
      >
      > ![image](https://github.com/santiforero1018/LAB9_ARSW/assets/88952698/7b29c103-5c5c-45c8-a533-a6b9ea6ba799)
      > 

    * 1020000
      > Con el tamaño inicial de la maquina (B1ls):
      > 
      >![image](https://github.com/santiforero1018/LAB9_ARSW/assets/89321404/fe0e9297-0572-4531-8ebf-75099ee4acfc)
      >
      > Con el tamaño B2ms:
      >
      > ![image](https://github.com/santiforero1018/LAB9_ARSW/assets/88952698/3c71290b-c4f1-43b8-bd70-482a7df0b7f0)
      > 

    * 1030000
      > Con el tamaño inicial de la maquina (B1ls):
      > 
      >![image](https://github.com/santiforero1018/LAB9_ARSW/assets/89321404/dc1e89f8-2855-4939-bd98-ff5e3bd9a99d)
      >
      > Con el tamaño B2ms:
      >
      > ![image](https://github.com/santiforero1018/LAB9_ARSW/assets/88952698/7c042926-a4b2-4c3e-b972-c9baf5a67177)
      > 

    * 1040000
      > Con el tamaño inicial de la maquina (B1ls):
      > 
      >![image](https://github.com/santiforero1018/LAB9_ARSW/assets/89321404/23e4959a-9f21-4fd5-9d39-67155ee8220b)
      >
      > Con el tamaño B2ms:
      >
      > ![image](https://github.com/santiforero1018/LAB9_ARSW/assets/88952698/197b7773-e5e9-4768-bac7-5b9634282f7c)
      > 

    * 1050000
      > Con el tamaño inicial de la maquina (B1ls):
      > 
      >![image](https://github.com/santiforero1018/LAB9_ARSW/assets/89321404/513a2e9e-94b8-40c6-806a-2d4ec5df3d27)
      >
      > Con el tamaño B2ms:
      >
      > ![image](https://github.com/santiforero1018/LAB9_ARSW/assets/88952698/34cab36c-81eb-4195-9cb0-8326f2461b9f)
      > 

    * 1060000
      > Con el tamaño inicial de la maquina (B1ls):
      > 
      >![image](https://github.com/santiforero1018/LAB9_ARSW/assets/89321404/2d615836-777b-4138-bf9a-854b08f7e33c)
      >
      > Con el tamaño B2ms:
      >
      >  ![image](https://github.com/santiforero1018/LAB9_ARSW/assets/88952698/4d7b0c19-eb7d-4dcc-97c4-74512e6846c0)
      > 

    * 1070000
      > Con el tamaño inicial de la maquina (B1ls):
      >  
      >![image](https://github.com/santiforero1018/LAB9_ARSW/assets/89321404/429764fd-604a-4523-9ffb-c0e88e504c50)
      > Con el tamaño B2ms:
      >
      > ![image](https://github.com/santiforero1018/LAB9_ARSW/assets/88952698/4477c2eb-e861-42a6-891c-7138c92d0dd9)
      > 
 
    * 108000
      > Con el tamaño inicial de la maquina (B1ls):
      > 
      > ![image](https://github.com/santiforero1018/LAB9_ARSW/assets/88952698/b90ec16b-ef3f-4c80-97fc-e60c1927a2a4)
      >
      > Con el tamaño B2ms:
      >
      > ![image](https://github.com/santiforero1018/LAB9_ARSW/assets/88952698/6865ab27-3242-4681-b90d-e1c83dbeb5c9)
      > 

    * 1090000
      > Con el tamaño inicial de la maquina (B1ls):
      > 
      >![image](https://github.com/santiforero1018/LAB9_ARSW/assets/89321404/a47a10fd-3183-4f46-ba06-f31b75352d69)
      >
      > Con el tamaño B2ms:
      >
      > ![image](https://github.com/santiforero1018/LAB9_ARSW/assets/88952698/6e90292f-f45b-45f7-bbba-44606ffd702c)
      > 


8. Dírijase ahora a Azure y verifique el consumo de CPU para la VM. (Los resultados pueden tardar 5 minutos en aparecer).

![Imágen 2](images/part1/part1-vm-cpu.png)



Mi CPU

- Consumo de CPU con tamaño B1ls
![image](https://github.com/santiforero1018/LAB9_ARSW/assets/89321404/c7b63b85-073a-4c9d-9b00-2030e595a738)

- Consumo de CPU con tamaño B2ms
![image](https://github.com/santiforero1018/LAB9_ARSW/assets/88952698/dd6eaefb-988a-4499-a618-787df9fe8824)

9. Ahora usaremos Postman para simular una carga concurrente a nuestro sistema. Siga estos pasos.
    * Instale newman con el comando `npm install newman -g`. Para conocer más de Newman consulte el siguiente [enlace](https://learning.getpostman.com/docs/postman/collection-runs/command-line-integration-with-newman/).
    * Diríjase hasta la ruta `FibonacciApp/postman` en una maquina diferente a la VM.
    * Para el archivo `[ARSW_LOAD-BALANCING_AZURE].postman_environment.json` cambie el valor del parámetro `VM1` para que coincida con la IP de su VM.
    * Ejecute el siguiente comando.

    ```
    newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
    newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10
    ```

10. La cantidad de CPU consumida es bastante grande y un conjunto considerable de peticiones concurrentes pueden hacer fallar nuestro servicio. Para solucionarlo usaremos una estrategia de Escalamiento Vertical. En Azure diríjase a la sección *size* y a continuación seleccione el tamaño `B2ms`.

![Imágen 3](images/part1/part1-vm-resize.png)

11. Una vez el cambio se vea reflejado, repita el paso 7, 8 y 9.
12. Evalue el escenario de calidad asociado al requerimiento no funcional de escalabilidad y concluya si usando este modelo de escalabilidad logramos cumplirlo.
13. Vuelva a dejar la VM en el tamaño inicial para evitar cobros adicionales.

**Preguntas**

1. ¿Cuántos y cuáles recursos crea Azure junto con la VM?
   - Se crean en total, se crean 5 items correspondientes para la maquina virtual, los cuales se pueden ver en la siguiente imagen:
     ![image](https://github.com/santiforero1018/LAB9_ARSW/assets/88952698/cb633155-bfb7-460b-8df5-e8afa2fd35fc)
      Cabe resaltar que esto con respecto a la unica maquina virtual sin montar el balanceador de carga
2. ¿Brevemente describa para qué sirve cada recurso?
   - La virtual machine, que es donde se montaran todos los servicios para la ejecución del laboratorio
   - La ip publica de la maquina, lo que le permite identificarse en la red y poder acceder a esta maquina
   - el Network Security Mannager en general, realiza funciones de control de acceso y de trafico a la red de la maquina, casi realizando funciones similares
     a las de un firewall.
   - La virtual network correspondiente a la maquina, es lo que le permite conectarse con algunos otros componentes, por ahora, solo se conecta esta maquina          virtual
   - la interfaz de red es por donde va conectarse la maquina virtual y tendra conexión hacia internet
3. ¿Al cerrar la conexión ssh con la VM, por qué se cae la aplicación que ejecutamos con el comando `npm FibonacciApp.js`? ¿Por qué debemos crear un *Inbound port rule* antes de acceder al servicio?
   - se esta ejecutando el servicio en consola, y como en esta instancia, mientras se mantenga activa dicha consola, el servicio sigue arriba, y si llegase a         cerrarse la conexión, mata todos los servicios corriendo, entre esos, el servidor levantado con node.
   - se crea para que se pueda tener acceso a la maquina virtual de manera segura y poder ejectutar los servicios correspondientes 
4. Adjunte tabla de tiempos e interprete por qué la función tarda tando tiempo.

5. Adjunte imágen del consumo de CPU de la VM e interprete por qué la función consume esa cantidad de CPU.
   - (se adjunto la imagen anteriormente en la comparación de graficas de CPUs) existe el consumo alto de CPU todavia debido a que se esta buscando realizar la       ejecución en el menor tiempo posible, por lo que el programa explota los recursos que tiene a dispocision dentro de la maquina, reduciendo asi el tiempo de      ejecución   
7. Adjunte la imagen del resumen de la ejecución de Postman. Interprete:
    * Tiempos de ejecución de cada petición.
      -  ![image](https://github.com/santiforero1018/LAB9_ARSW/assets/88952698/61408dcf-f1dd-413c-936d-b8eb74581a86)
      -  ![image](https://github.com/santiforero1018/LAB9_ARSW/assets/88952698/392dc542-3d35-49d7-85fb-214ed0d13d05)
      -  ![image](https://github.com/santiforero1018/LAB9_ARSW/assets/88952698/ccd1de61-1781-4430-8bfe-9a1fd04591f5)
      -  ![image](https://github.com/santiforero1018/LAB9_ARSW/assets/88952698/a24ab489-c5d1-4d60-9b1e-2288eb9e14ab)
      -  ![image](https://github.com/santiforero1018/LAB9_ARSW/assets/88952698/caeaf296-8daa-4818-a8b1-6368d3468956)
    * Si hubo fallos documentelos y explique.
      - Los errores se refieren al hecho de que se realizaban cierres de conexiones inesperados, aunque tambien influye el hecho de que se calculo varias veces           el mismo numero en cada iteración
8. ¿Cuál es la diferencia entre los tamaños `B2ms` y `B1ls` (no solo busque especificaciones de infraestructura)?
   - A continuación se presentan las diferencias entre estos tamaños usando la tabla de azure
     ![image](https://github.com/santiforero1018/LAB9_ARSW/assets/88952698/5877c351-4604-49d0-b1b9-d82edec9fdf6)
      Con estas caracterisiticas, en general, se usaria un tamaño B2ms para trabajos mucho más exigentes, pero tambien exige un costo económico mayor y pasa lo        contrario con B1ls
9. ¿Aumentar el tamaño de la VM es una buena solución en este escenario?, ¿Qué pasa con la FibonacciApp cuando cambiamos el tamaño de la VM?
   - de acuerdo a lo que se ha evidenciado, no es tan buena la solución, debido a que si se logra la disminoción de tiempos, pero el consumo de CPU sigue             altisimo, haciendo que se quemen al maximo los recursos que ofrece la maquina virtual en programas como estos 
10. ¿Qué pasa con la infraestructura cuando cambia el tamaño de la VM? ¿Qué efectos negativos implica?
   - Se podria ver afectado el servicio que esta alojando la maquina virtual, afectando directamente en primer lugar la disponibilidad del servicio, al hacer el       cambio del tamaño, implica un tiempo de baja del servicio para realizar los cambios pertinenetes y volver a subir el servicio.
11. ¿Hubo mejora en el consumo de CPU o en los tiempos de respuesta? Si/No ¿Por qué?
   - hubo mejoras fue en el tiempo de respuesta, pues al disponer de un poco más de CPU, pudo tener acceso a más recursos los cuales pudo explotar al maximo el       programa pero el consumo de CPU fue el principal danmificado. 
12. Aumente la cantidad de ejecuciones paralelas del comando de postman a `4`. ¿El comportamiento del sistema es porcentualmente mejor?
   - la siguientes graficas muestran el porcentage de la ultima ejecución del sistema
     ![image](https://github.com/santiforero1018/LAB9_ARSW/assets/88952698/ae4d6fe1-45bb-4e18-ad80-23881c8e0be2)
     ![image](https://github.com/santiforero1018/LAB9_ARSW/assets/88952698/cbacb58d-c2f7-42c7-853e-ffe6698535ed)
     aunque se tiene una mayor capacidad de computo, una cantidad de peticiones asi tuvieron un tiempo de respuesta muy alto, lo que afecto el rendimiento de la      maquina, por lo que porcentualmente, no se comportó mejor el sistema.


### Parte 2 - Escalabilidad horizontal

#### Crear el Balanceador de Carga

Antes de continuar puede eliminar el grupo de recursos anterior para evitar gastos adicionales y realizar la actividad en un grupo de recursos totalmente limpio.

1. El Balanceador de Carga es un recurso fundamental para habilitar la escalabilidad horizontal de nuestro sistema, por eso en este paso cree un balanceador de carga dentro de Azure tal cual como se muestra en la imágen adjunta.

![](images/part2/part2-lb-create.png)

2. A continuación cree un *Backend Pool*, guiese con la siguiente imágen.

![](images/part2/part2-lb-bp-create.png)

3. A continuación cree un *Health Probe*, guiese con la siguiente imágen.

![](images/part2/part2-lb-hp-create.png)

4. A continuación cree un *Load Balancing Rule*, guiese con la siguiente imágen.

![](images/part2/part2-lb-lbr-create.png)

5. Cree una *Virtual Network* dentro del grupo de recursos, guiese con la siguiente imágen.

![](images/part2/part2-vn-create.png)

#### Crear las maquinas virtuales (Nodos)

Ahora vamos a crear 3 VMs (VM1, VM2 y VM3) con direcciones IP públicas standar en 3 diferentes zonas de disponibilidad. Después las agregaremos al balanceador de carga.

1. En la configuración básica de la VM guíese por la siguiente imágen. Es importante que se fije en la "Avaiability Zone", donde la VM1 será 1, la VM2 será 2 y la VM3 será 3.

![](images/part2/part2-vm-create1.png)

2. En la configuración de networking, verifique que se ha seleccionado la *Virtual Network*  y la *Subnet* creadas anteriormente. Adicionalmente asigne una IP pública y no olvide habilitar la redundancia de zona.

![](images/part2/part2-vm-create2.png)

3. Para el Network Security Group seleccione "avanzado" y realice la siguiente configuración. No olvide crear un *Inbound Rule*, en el cual habilite el tráfico por el puerto 3000. Cuando cree la VM2 y la VM3, no necesita volver a crear el *Network Security Group*, sino que puede seleccionar el anteriormente creado.

![](images/part2/part2-vm-create3.png)

4. Ahora asignaremos esta VM a nuestro balanceador de carga, para ello siga la configuración de la siguiente imágen.

![](images/part2/part2-vm-create4.png)

5. Finalmente debemos instalar la aplicación de Fibonacci en la VM. para ello puede ejecutar el conjunto de los siguientes comandos, cambiando el nombre de la VM por el correcto

```
git clone https://github.com/daprieto1/ARSW_LOAD-BALANCING_AZURE.git

curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
source /home/vm1/.bashrc
nvm install node

cd ARSW_LOAD-BALANCING_AZURE/FibonacciApp
npm install

npm install forever -g
forever start FibonacciApp.js
```

Realice este proceso para las 3 VMs, por ahora lo haremos a mano una por una, sin embargo es importante que usted sepa que existen herramientas para aumatizar este proceso, entre ellas encontramos Azure Resource Manager, OsDisk Images, Terraform con Vagrant y Paker, Puppet, Ansible entre otras.

#### Probar el resultado final de nuestra infraestructura

1. Porsupuesto el endpoint de acceso a nuestro sistema será la IP pública del balanceador de carga, primero verifiquemos que los servicios básicos están funcionando, consuma los siguientes recursos:

```
http://52.155.223.248/
http://52.155.223.248/fibonacci/1
```

2. Realice las pruebas de carga con `newman` que se realizaron en la parte 1 y haga un informe comparativo donde contraste: tiempos de respuesta, cantidad de peticiones respondidas con éxito, costos de las 2 infraestrucruras, es decir, la que desarrollamos con balanceo de carga horizontal y la que se hizo con una maquina virtual escalada.

![image](https://github.com/santiforero1018/LAB9_ARSW/assets/88952698/fae076f6-6f51-4547-a372-a1d0fb2a11e3)

* Metricas de la maquina 1
![image](https://github.com/santiforero1018/LAB9_ARSW/assets/88952698/4f933321-ddff-4ea1-9700-e00f4bcaaf88)

* Metricas de la maquina 2
![image](https://github.com/santiforero1018/LAB9_ARSW/assets/88952698/55afb7fb-cefe-42f3-b788-fcbd74944b21)



- De acuerdo con los tiempos tomados anteriormente, encontramos que al realizar este tipo de escalabilidad, tenemos tiempos de ejecución menores, ya que con el balanceador de cargas, se puede delegar más trabajos a las demas maquinas virtuales asociadas al balanceador.
- La cantidad de peticiones de exito fueron mejores usando la escalabilidad horizontal que la escalabilidad vertical, si contrastamos con los resultados de la parte anterior.
- Aunque se tiene mejores tiempos de ejecución y no se consumen tantos recursos como en la escalabilidad vertical, sale un poco más cosotos economicamente montar un sistema con escalabilidad horizontal que vertical, pues se tiene que considerar el montaje de un balanceador de cargas más el montaje de los demas nodos, mientras que en la escalabilidad vertical, el unico precio a tener en cuenta es el tamaño de la maquina virtual a implementar.

3. Agregue una 4 maquina virtual y realice las pruebas de newman, pero esta vez no lance 2 peticiones en paralelo, sino que incrementelo a 4. Haga un informe donde presente el comportamiento de la CPU de las 4 VM y explique porque la tasa de éxito de las peticiones aumento con este estilo de escalabilidad.

```
newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10
```

Nota: debido a la disponibilidad del plan de recursos de azure, no se pudo montar ni una tercera ni una cuarta maquina virtual, por lo que se realizó esta misma cantidad de peticiones usando las dos maquinas virtuales que si se lograron montar para esta parte del laboratorio, y gracias a la distribución que realiza el balanceador de cargas, pueden llegar a presentarse más peticiones de exito durante su ejecución.

**Preguntas**

* ¿Cuáles son los tipos de balanceadores de carga en Azure y en qué se diferencian?, ¿Qué es SKU, qué tipos hay y en qué se diferencian?, ¿Por qué el balanceador de carga necesita una IP pública?
  - Azure Aplication GateWay, Azure FrontDoor, Azure Load Balancer y Azure Traffic Manager, estos se difierencian principalmente en el uso de protocolos HTTP(S) y el enrutamiento Regional o Global, por ejemplo, FrontDoor y Traffic Manager solo usan el enrutamiento Global (uno usa HTTP(S) y el otro no respectivamente) mientras que Aplication Gateway es regional y usa HTTP(S), y Load Balancer puede ser tanto global como regional y no depende del uso de HTTP(S)
* ¿Cuál es el propósito del *Backend Pool*?
  Es un conjunto de servidores, adonde al hacer la petición de un servicio, el balanceador de cargas podra redireccionar a las maquinas virtuales o a los servicios montados aqui, dependiendo tambien de las reglas establecidas.
* ¿Cuál es el propósito del *Health Probe*?
  Esta propiedad le permite al balanceador de cargas determinar en que estado se encuentran las instancias de los servicios, con eso puede determinar si sigue redigiriendo peticiones a esa instancia, o mandarlas a otra
* ¿Cuál es el propósito de la *Load Balancing Rule*? ¿Qué tipos de sesión persistente existen, por qué esto es importante y cómo puede afectar la escalabilidad del sistema?.
  - El proposito es definir como el balanceador de cargas va a realizar la distribución de los sistemas
  - 
* ¿Qué es una *Virtual Network*? ¿Qué es una *Subnet*? ¿Para qué sirven los *address space* y *address range*?
* ¿Qué son las *Availability Zone* y por qué seleccionamos 3 diferentes zonas?. ¿Qué significa que una IP sea *zone-redundant*?
* ¿Cuál es el propósito del *Network Security Group*?
* Informe de newman 1 (Punto 2)
* Presente el Diagrama de Despliegue de la solución.




