# Big-data-architecture-Practica

ARQUITECTURA DATA LAKE/TECNOLOGÍA UTILIZADA:
1. Servidor cloud (Google Cloud) 
2. Datasets: airbnb.csv (descarga en local) y hoteles_scrapy.csv (proceso de crawling)
3. Crawling: scrapy --> Dataset crawler: hoteles_scrapy.csv Web: https://www.secretplaces.es/espana/hoteles
4. Staging: dataset: hoteles_scrapy.csv/ airbnb.csv a Google Storage y almacenamiento en ruta "dataproc-78c05970-c291-47f3-843a-d313935d5526-europe-west1/hoteles_scrapy/" y "dataproc-78c05970-c291-47f3-843a-d313935d5526-europe-west1/airbnb-listings.csv" respectivamente

DESCRIPCION

PARTE 1 : DIAGRAMA DE LA ARQUITECTURA
En el diagrama de la arquitectura se muestran dos rutas que siguen los dos datasets utilizados (airbnb-listings.csv y hoteles_scrapy.csv)

Datalake:
1. Descargo el dataset airbnb-listings.csv en local -- almacenamiento en Google storage (dataproc-78c05970-c291-47f3-843a-d313935d5526-europe-west1) -- job: wordcount mediante conector en local (file:////usr/lib/hadoop-mapreduce/hadoop-mapreduce-examples.jar) -- almaceno el resultado en directorio output de hdfs -- lo saco de hdfs y almaceno el resultado del job en Google storage (dataproc-78c05970-c291-47f3-843a-d313935d5526-europe-west1/airbnb-resultado/)
2. Obtengo de la web https://www.secretplaces.es/espana/hoteles el dataset data.csv -- almacenamiento en Google Storage (STAGING) como hoteles_scrapy.csv

PARTE 2: SCRAPY GOOGLE COLLABORATORY
Utilizo este collaboratory para sacar el nombre, tipo de alojamiento y precio minimo de esta página de hoteles: https://www.secretplaces.es/espana/hoteles. Los resultados los almaceno en google drive en un dataset llamado data.csv 

https://colab.research.google.com/drive/1-nhXXTvqstDmB0_hqIx91OzOO5m1QSKj

PARTE 3: CLUSTER HADOOP DE 3 CONTENEDORES

Utilizo el cloud (Google Cloud) para montarme el cluster con un contenedor Nodo Master y dos contenedores Nodo Esclavos.






