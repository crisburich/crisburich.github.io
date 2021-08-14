![bikes](https://user-images.githubusercontent.com/82124948/129451229-f01a4473-284f-4b91-8591-c94884d0b56a.JPG)



Este proyecto forma parte de un caso de estudio correspondiente al final del Google Data Analytics Professional Certificate. El escenario planteado es el de una empresa que provee bicicletas para transporte urbano y buscan aumentar la cantidad de usuarios con membresías anuales, ya que estos le proveen la mayor rentabilidad. 

### **1era etapa: Ask**

En esta primera etapa planteamos las preguntas relevantes que queremos responder. En este caso, sabemos que la compañía busca aumentar la cantidad de suscripciones anuales a su servicio. Para lograr este objetivo debemos comprender en qué difieren ambos tipos de usuarios y qué los lleva a elegir diferentes opciones. Luego intentaremos averiguar que llevaría a un usuario ocasional a adquirir una suscripción anual. Con esta información en mente quizás podamos potenciar una campaña de marketing orientada a persuadir a los usuarios ocasionales de obtener una membresía anual.

### **2da etapa: Prepare**

Utilizaremos el siguiente dataset con información sobre los viajes realizados por los usuarios:

https://divvy-tripdata.s3.amazonaws.com/index.html

Se trata de datos de carácter público proporcionados por Motivate International Inc

La herramienta seleccionada para comenzar a trabajar es SQL. La decisión fue tomada en base a la cantidad de datos, ya que para esta cantidad de filas el trabajo en Excel se vuelve engorroso. Trabajar directamente con R también era una opción interesante. 

En esta parte del proceso se descargaron los archivos correspondientes a los últimos seis meses del 2020, se descomprimieron los CSV y se archivaron en una carpeta siguiendo la siguiente convención: 
YYYY_MM_tripdata, por ejemplo 2020_07_tripdata corresponde a la información de julio del año 2020.

Luego creo una base de datos en SQL y se procedió a crear las seis tablas correspondientes a los seis archivos mensuales en formato CSV. La información que se nos provee sobre cada viaje (ver imagen abajo) indica el lugar de partida y llegada con sus respectivos horarios y el tipo de membresía. Cada viaje está identificado con un único “ride_id”, lo cual puede limitar la capacidad de responder las preguntas planteadas; sería más útil un id que identificara a cada usuario, pero al trabajar con datos publicos las cuestiones de privacidad limitan la calidad de la información con la que contamos. 

```sql
SELECT TOP (1000) [ride_id]     
      ,[started_at]
      ,[ended_at]
      ,[start_station_name]
      ,[end_station_name]    
      ,[member_casual]
  FROM [bikes capstone].[dbo].[2020_07_tripdata]  
```
![1](https://user-images.githubusercontent.com/82124948/129451040-84bb4ef3-9efd-4dcb-97f3-7ef60de80dcc.JPG)
    
### **3era etapa: Process**

En este paso nos aseguramos de que los datos con los que trabajamos estén listos para el análisis. En general los datos están bien estructurados y a primera vista no hay errores de valores faltantes ni problemas de formato.

Por cuestiones de utilidad, calcularemos la duración del viaje aprovechando que tenemos las horas de partida y llegada, creando la columna “elapsed_minutes”

```sql
SELECT [ride_id]     
      ,[started_at]
      ,[ended_at]
      ,[start_station_name]
      ,[end_station_name]    
      ,[member_casual]
      ,[elapsed_minutes] = DATEDIFF(MINUTE, [started_at], [ended_at])
  FROM [bikes capstone].[dbo].[2020_07_tripdata]
```
![2](https://user-images.githubusercontent.com/82124948/129451050-bdd4acf6-fdf4-4b3c-a643-376c6ffdb480.JPG)


Al ordenar los datos por cantidad de minutos se observa que hay algunos pocos con números negativos. Dado que el tiempo debe ser positivo, esto se trata de un error; de ser posible intentaríamos ubicar la fuente del error para saber si afecta la integridad de nuestros datos. Como se trata de datos públicos, en este caso simplemente dejaremos afuera a los viajes con tiempo negativo. Lo mismo haremos con los viajes que hayan sido registrados con una duración mayor a 10 horas. 

### **4ta etapa: Analyze** 

Ahora calculamos la media de tiempo de viaje:


```sql
  SELECT AVG(DATEDIFF(MINUTE, [started_at], [ended_at])) AS mean
   FROM [bikes capstone].[dbo].[2020_07_tripdata]
    WHERE DATEDIFF(MINUTE, [started_at], [ended_at]) > 0
   AND DATEDIFF(MINUTE, [started_at], [ended_at]) < 600
   AND [member_casual] = 'casual' 
```

El resultado para el primer mes en análisis es de 29 minutos.

Agregando una simple cláusula WHERE podemos obtener la media para cada tipo usuario (casual y con membresía)
Para los usuarios casuales la media es de 41 minutos, mientras que para los miembros es de 17 minutos. Esta información nos da información interesante respecto a cómo difieren estos dos tipos de clientes. 

Los primeros hacen viajes más largos, por lo que es posible que solo decidan utilizar el servicio cuando realmente lo amerita (quizás les conviene caminar si el viaje es corto) o bien lo utilizan por cuestiones de ocio, por ejemplo, para pasear los fines de semana. 

Los usuarios con membresía hacen viajes considerablemente más cortos (en promedio). Antes de continuar el análisis sería interesante considerar el share de cada tipo usuario en la cantidad de viajes. Para esto usamos la función COUNT


```sql
  SELECT AVG(DATEDIFF(MINUTE, [started_at], [ended_at])) AS mean
   FROM [bikes capstone].[dbo].[202012-divvy-tripdata]
    WHERE DATEDIFF(MINUTE, [started_at], [ended_at]) > 0
    AND DATEDIFF(MINUTE, [started_at], [ended_at]) < 600
    AND [member_casual] = 'casual' 
```

El resultado es que ambos tienen un peso similar, representando la mitad de los viajes cada uno. Si tuviéramos un ID por usuario podríamos ver la intensidad con la que cada clase utiliza el servicio, lo cual sería interesante para realizar un análisis más completo.  

### **5ta etapa: Share**

![graf](https://user-images.githubusercontent.com/82124948/129451130-b19bad60-57d5-4d44-9df6-5b25fdc3f31e.png)

La diferencia en el tiempo de viaje observada para el primer mes de análisis se replica para el resto del año. 

De esta forma logramos responder a la primera pregunta que planteamos, comprendiendo la forma en que los dos tipos de usuarios utilizan el servicio. El mismo análisis abre lugar a nuevas preguntas. Por ejemplo, sería interesante saber si la tendencia a la baja en la duración de los viajes se trata de un fenómeno estacional o es algo de lo que deberíamos preocuparnos.  

Podemos plantear la hipótesis de que los usuarios que se pasan a membresías anuales son aquellos cuya cantidad de viajes determina que exista un ahorro con el plan anual. Con esto en mente, una campaña de marketing que ponga como objetivo fidelizar a aquellos usuarios casuales con mayor cantidad de viajes podría resultar efectiva. Para confirmar la hipótesis sería útil tener datos por usuario e intentar comprender los patrones de uso de los que adquirieron una membresía anual. 

