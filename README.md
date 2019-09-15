# Big-data-architecture-Practica

**ARQUITECTURA DATA LAKE/TECNOLOGÍA UTILIZADA:**
1. Servidor cloud (Google Cloud) 
2. Datasets: airbnb-listings.csv (descarga en local) y data.csv (hoteles_scrapy) (proceso de crawling)
3. Crawling: scrapy --> Dataset crawler: data.csv Web: https://www.secretplaces.es/espana/hoteles
4. Staging: dataset: data.csv/ airbnb-listings.csv a Google Storage y almacenamiento en ruta "dataproc-78c05970-c291-47f3-843a-d313935d5526-europe-west1/hoteles.csv" y "dataproc-78c05970-c291-47f3-843a-d313935d5526-europe-west1/airbnb-listings.csv" respectivamente

**DESCRIPCION**

***PARTE 1 : DIAGRAMA DE LA ARQUITECTURA***
En el diagrama de la arquitectura se muestran dos rutas que siguen los dos datasets utilizados (airbnb-listings.csv y data.csv)

*Datalake:*
PRIMERA RUTA (en azul). Descargo el dataset **airbnb-listings.csv** en local -- almacenamiento en Google storage (dataproc-78c05970-c291-47f3-843a-d313935d5526-europe-west1) -- job: wordcount mediante conector *.jar* en local (file:////usr/lib/hadoop-mapreduce/hadoop-mapreduce-examples.jar) -- almaceno el resultado en directorio *output* de hdfs -- lo saco de hdfs y almaceno el resultado del job en Google storage en el directorio *airbnb-resultado* (dataproc-78c05970-c291-47f3-843a-d313935d5526-europe-west1/airbnb-resultado/)

SEGUNDA RUTA (en rojo). Obtengo de la web https://www.secretplaces.es/espana/hoteles el dataset **data.csv** -- almacenamiento en Google Storage (STAGING) como **hoteles.csv** (dataproc-78c05970-c291-47f3-843a-d313935d5526-europe-west1) -- mediante HIVE añado elementos a las tablas (utilizo la shell) -- guardo los resultados de HIVE en el directorio *hoteles_scrapy* de GS (dataproc-78c05970-c291-47f3-843a-d313935d5526-europe-west1/hoteles_scrapy/)

***PARTE 2: SCRAPY GOOGLE COLLABORATORY***
Utilizo este collaboratory para sacar el nombre, tipo de alojamiento y precio minimo de esta página de hoteles: https://www.secretplaces.es/espana/hoteles. Los resultados los almaceno en google drive en un dataset llamado data.csv (que para la parte 4 de la práctica subiré a Google Cloud en el apartado *Enviar resultados a Google Cloud Storage*).

https://colab.research.google.com/drive/1-nhXXTvqstDmB0_hqIx91OzOO5m1QSKj

***PARTE 3: CLUSTER HADOOP DE 3 CONTENEDORES***

Utilizo el cloud (Google Cloud) para montarme el cluster con un contenedor Nodo Master y dos contenedores Nodo Esclavos.

![image](https://user-images.githubusercontent.com/46502030/64920811-e4dc2680-d7bb-11e9-8145-fb4eed699497.png)

***PARTE 4: SUBIR DATASET PARTE 2 A HDFS Y REALIZAR JOB WORDCOUNT***

Para hacer esta parte, utilizo el dataset **airbnb-listings.csv.**
- Creo una regla de cortafuegos para habilitar el puerto tcp:9870 para la conexión a HDFS y el puerto tcp: 8088 para la conexion a YARN
![image](https://user-images.githubusercontent.com/46502030/64920894-db9f8980-d7bc-11e9-8cc5-2b3e05eadd28.png)
- Subo al segmento de GS "dataproc-78c05970-c291-47f3-843a-d313935d5526-europe-west1" el dataset airbnb-listings.csv
- Para hacer el wordcount, inicio nueva tarea con el .jar que se encuentra en el local, y paso como argumentos el nombre del job (wordcount), el dataset almacenado en gs (airbnb-listings.csv) y el directorio de salida en hdfs (output)
![image](https://user-images.githubusercontent.com/46502030/64920919-23261580-d7bd-11e9-98a8-dfb6b6de5b6c.png)
- Me conecto a la url de HDFS mediante el puerto 9870 y veo el resultado en la ruta /user/root/output
![image](https://user-images.githubusercontent.com/46502030/64920964-b19a9700-d7bd-11e9-87c8-05cb0b6cf6cf.png)
- Observo el mapreduce wordcount en Yarn 
![image](https://user-images.githubusercontent.com/46502030/64921010-19e97880-d7be-11e9-9d01-eec8c3555c46.png)
- Hago otro staging y guardo los resultados que habia en HDFS en google storage en el mismo segmento (dataproc-78c05970-c291-47f3-843a-d313935d5526-europe-west1) en el directorio "airbnb-resultado"
![image](https://user-images.githubusercontent.com/46502030/64921040-587f3300-d7be-11e9-8689-785057d727a2.png)
![image](https://user-images.githubusercontent.com/46502030/64921085-e78c4b00-d7be-11e9-9079-d66197cd2777.png)

***PARTE 5: INSERTAR DATOS DEL DATASET DE hoteles.csv MEDIANTE HIVE ***

Habilito el puerto tcp:10000 para la conexión con HIVE
![image](https://user-images.githubusercontent.com/46502030/64921242-20c5ba80-d7c1-11e9-9c86-36df45655433.png)

Voy al cluster y me conecto a la SSH a través de *"beeline -u jdbc:hive2://localhost:10000"*
![image](https://user-images.githubusercontent.com/46502030/64921255-5bc7ee00-d7c1-11e9-9361-23204170a57d.png)
Ejecuto los siguientes comandos en la shell:

CREATE TABLE hoteles_scrapy (NAME STRING, TIPO STRING, PRECIO_MIN STRING) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',';
LOAD DATA INPATH 'gs://dataproc-78c05970-c291-47f3-843a-d313935d5526-europe-west1/hoteles.csv' INTO TABLE hoteles_scrapy;
INSERT OVERWRITE DIRECTORY 'gs://dataproc-78c05970-c291-47f3-843a-d313935d5526-europe-west1/hoteles_scrapy' ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' select * from hoteles_scrapy;
![image](https://user-images.githubusercontent.com/46502030/64921302-f7f1f500-d7c1-11e9-8f92-ebd445aee988.png)

Lo guardo en el mismo segmento de GS en la ruta *hoteles_scrapy/*
![image](https://user-images.githubusercontent.com/46502030/64921305-06401100-d7c2-11e9-8383-f07a561b16d2.png)











