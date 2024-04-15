# Herramientas_de_BigData

# Practica Integradora

Durante esta practica la idea es emular un ambiente de trabajo, desde un área de innovación solicitan construir un MVP(Producto viable mínimo) de un ambiente de Big Data donde se deban cargar unos archivos CSV que anteriormente se utilizaban en un datawarehouse en MySQl, pero ahora en un entorno de Hadoop.

Desde la gerencia de Infraestructura no están muy convencidos de utilizar esta tecnología por lo que no se asigno presupuesto alguna para esta iniciativa, de forma tal que por el momento no es posible utilizar un Vendor(Azure, AWS, Google) para implementar dicho entorno, es por esto que todo el MVP se deberá implementar utilizando Docker de forma tal que se pueda hacer una demo al sector de infraestructura mostrando las ventajas de utilizar tecnologías de Big Data.


Se pesenta un entorno Docker con Hadoop (HDFS) y la implementación de:
* Spark
* Hive
* HBase
* MongoDB
* Neo4J
* Zeppelin
* Kafka


Es importante mencionar que el entorno completo consume muchos recursos de su equipo, motivo por el cuál, se propondrán ejercicios pero con ambientes reducidos, en función de las herramientas utilizadas.

Implementar y ejecutar cada script por separado. 

    '''
    git clone https://github.com/lopezdar222/herramientas_big_data
    cd herramientas_big_data
    sudo docker-compose -f docker-compose-vX.yml up -d
    '''

## 1) HDFS

Utilizar el entorno docker-compose-v1.yml
````
    sudo docker-compose -f docker-compose-v1.yml up -d
````
Creamos el contenedor llamado (namenode) y entramos a su carpeta.
````
    sudo docker exec -it namenode bash
````
````
    cd home 
````

Crearemos el directorio Datasets y salimos
````        
    mkdir Datasets
````
````
    exit
````

Ejecutar el archivo 'Paso00.sh' el cual contiene los comandos para copiar los archivos desde tu sistema local al contenedor.
 
Para poder ejecutarlo le damos permiso de ejecucion
````
    chmod u+x Paso00.sh
````
````
    sudo ./Paso00.sh
````        
Ingresamos al contenedor "namenode":
````
    sudo docker exec -it namenode bash
````
Nos ubicamos en el directorio 'home':
````
    cd home
````    
Creamos el directorio 'data':
````
    hdfs dfs -mkdir -p /data
````   
Pegamos los archivos csv provistos a HDFS:
````
    hdfs dfs -put /home/Datasets/* /data
````
Este proceso de creación de la carpeta data y copiado de los arhivos, debe poder ejecutarse desde un shell script.

Nota: Busque dfs.blocksize y dfs.replication en http://<IP_Anfitrion>:9870/conf para encontrar los valores de tamaño de bloque y factor de réplica respectivamente entre otras configuraciones del sistema Hadoop.

![image](https://github.com/ylathan/Herramientas-de-BigData/assets/98925562/596a9f3f-0322-4eab-a5b8-71ff7263a386)

## 2) Hive

Vamos a utilizar el entorno docker-compose-v2.yml, por lo cual es necesario detener los contenedores anteriores.
````
    sudo docker stop $(sudo docker ps -a -q)
 ````       
 ````  
    sudo docker-compose -f docker-compose-v2.yml up -d
 ````       
   
Copiamos el archivo 'Paso02.hql' desde tu sistema de archivos local al directorio '/opt/' dentro del contenedor llamado "hive-server".
````
    sudo docker cp ./Paso02.hql hive-server:/opt/
````        
Creamos una conexion interactiva con contenedor llamado "hive-server".Ejecutamos el archivo 'Paso02.hql'.Finalizamos la conexion interactiva.
````
    sudo docker exec -it hive-server bash
````
````      
    hive -f Paso02.hql
````
````       
    exit
````
Para comprobar que cargo correctamente la base de datos entramos a hive y ejecutamos una query.

![image](https://github.com/ylathan/Herramientas-de-BigData/assets/98925562/254ed713-c805-4fca-bbc9-ac073f1f5f04)

## 3) Formatos de Almacenamiento
````
    sudo docker-compose -f docker-compose-v2.yml up -d
````
El comando copia el archivo 'Paso03.hql' desde tu sistema de archivos local al directorio '/opt/' dentro del contenedor llamado "hive-server".
````        
    sudo docker cp ./Paso03.hql hive-server:/opt/
````
ubicarse dentro del contenedor, acceder al archivo 'Paso03.hql'  en este lugar es donde se realizan los pruebas hay que tener paciencia para que se carge el código.Si accedes a la hive en este lugar puedes realizar las consultas y para salir usas con el comando ('exit;' o 'quit')
````
    sudo docker exec -it hive-server bash
````
````
    hive -f Paso03.hql
````
````
    exit
````

![image](https://github.com/ylathan/Herramientas-de-BigData/assets/98925562/137fdd60-9f2c-433b-b6d3-65ea8083e65a)

## 4) SQL

La mejora en la velocidad de consulta que puede proporcionar un índice tiene el costo del procesamiento adicional para crear el índice y el espacio en disco para almacenar las referencias del índice. Se recomienda que los índices se basen en las columnas que utiliza en las condiciones de filtrado. El índice en la tabla puede degradar su rendimiento en caso de que no los esté utilizando. Crear índices en alguna de las tablas cargadas y probar los resultados:

Dentro del hive realizamos diferentes consultas y les medimos el tiempo.Estas consultas se realizan antes de agregar los indices.
````
    SELECT IdProducto, sum(Precio*Cantidad) FROM venta GROUP BY IdProducto;
````
![image](https://github.com/ylathan/Herramientas-de-BigData/assets/98925562/bf2d0e48-e53f-49dd-bcf9-21b11121fa6a)
````
    SELECT Fecha,sum(Cantidad) FROM compra GROUP BY Fecha;
````
![image](https://github.com/ylathan/Herramientas-de-BigData/assets/98925562/0829944a-0e44-4e14-844a-a36d3fef6de7)
````
    SELECT cIdProveedor,p.Nombre FROM compra c JOIN proveedor p USING (IdProveedor);
````
![image](https://github.com/ylathan/Herramientas-de-BigData/assets/98925562/6e5f1788-3e3f-4df3-bbb1-f970fdea6c3d)


Insertamos los indices

         CREATE INDEX index_Fecha_compra ON TABLE venta(Fecha) AS 'org.apache.hadoop.hive.ql.index.compact.CompactIndexHandler' WITH DEFERRED REBUILD;
         








   










        


