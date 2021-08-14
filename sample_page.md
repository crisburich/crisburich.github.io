## Proyecto 1
**Project description:** SQL cuenta con una serie de bases de datos ficticias que permiten aplicar conceptos de manera practica y similar a un entorno de trabajo concreto. En los ejemplos se utilizará AdventureWorks2019
### 1. Conceptos aplicados: Joins, Case Statement


```sql

SELECT A.[PurchaseOrderID]
      ,A.[PurchaseOrderDetailID]
      ,A.[OrderQty]
      ,A.[UnitPrice]
      ,A.[LineTotal]
      ,B.[OrderDate]
      ,[OrderSizeCategory] = 
	  CASE
	  WHEN [OrderQty] > 500 THEN 'Large'
	  WHEN [OrderQty] BETWEEN 51 AND 500 THEN 'Medium'
	  ELSE 'Small'
	  END
      ,C.[Name] AS 'Product Name'
      ,[Subcategory] = ISNULL(D.[Name], 'None')
      ,E.[Name] AS 'Category'
	  	  	        
  FROM [Purchasing].[PurchaseOrderDetail] A
  JOIN [Purchasing].[PurchaseOrderHeader] B
  ON A.[PurchaseOrderID] = B.[PurchaseOrderID]
  JOIN [Production].[Product] C
  ON A.[ProductID] = C.[ProductID]
  LEFT JOIN [Production].[ProductSubcategory] D 
  ON C.[ProductSubcategoryID] = D.[ProductSubcategoryID]
  JOIN [Production].[Product] E
  ON A.[ProductID] = E.[ProductID]

  WHERE MONTH(B.[OrderDate]) = 12
  AND [UnitPrice] > 40
  OR D.[Name] = 'Pedals'
  ORDER BY 4

```


Este proyecto forma parte de un caso de estudio correspondiente al final del Google Data Analytics Professional Certificate. El escenario planteado es el de una empresa que provee bicicletas para transporte urbano y buscan aumentar la cantidad de usuarios con membresías anuales, ya que estos le proveen la mayor rentabilidad. 

**1era etapa: Ask**

En esta primera etapa planteamos las preguntas relevantes que queremos responder. En este caso, sabemos que la compañía busca aumentar la cantidad de suscripciones anuales a su servicio. Para lograr este objetivo debemos comprender en qué difieren ambos tipos de usuarios y qué los lleva a elegir diferentes opciones. Luego intentaremos averiguar que llevaría a un usuario ocasional a adquirir una suscripción anual. Con esta información en mente quizás podamos potenciar una campaña de marketing orientada a persuadir a los usuarios ocasionales de obtener una membresía anual.

**2da etapa: Prepare**

Utilizaremos el siguiente dataset con información sobre los viajes realizados por los usuarios:

https://divvy-tripdata.s3.amazonaws.com/index.html

Se trata de datos de carácter público proporcionados por Motivate International Inc
La herramienta seleccionada para comenzar a trabajar es SQL. La decisión fue tomada en base a la cantidad de datos, ya que para esta cantidad de filas el trabajo en Excel se vuelve engorroso. Trabajar directamente con R también era una opción interesante. 
En esta parte del proceso se descargaron los archivos correspondientes a los últimos seis meses del 2020, se descomprimieron los CSV y se archivaron en una carpeta siguiendo la siguiente convención: 
YYYY_MM_tripdata, por ejemplo 2020_07_tripdata corresponde a la información de julio del año 2020.
Se creo una base de datos en SQL y se procedió a crear las seis tablas correspondientes a los seis archivos mensuales en formato CSV. La información que se nos provee sobre cada viaje (ver imagen abajo) indica el lugar de partida y llegada con sus respectivos horarios y el tipo de membresía. Cada viaje está identificado con un único “ride_id”, lo cual puede limitar la capacidad de responder las preguntas planteadas; sería más útil un id que identificara a cada usuario, pero al trabajar con datos publicos las cuestiones de privacidad limitan la calidad de la información con la que contamos. 

```sql
SELECT TOP (1000) [ride_id]     
      ,[started_at]
      ,[ended_at]
      ,[start_station_name]
      ,[end_station_name]    
      ,[member_casual]
  FROM [bikes capstone].[dbo].[2020_07_tripdata]  
  
    ´´´
    
3era etapa: Process

En este paso nos aseguramos de que los datos con los que trabajamos estén listos para el análisis. En general los datos están bien estructurados y a primera vista no hay errores de valores faltantes ni problemas de formato.
Por cuestiones de utilidad, calcularemos la duración del viaje aprovechando que tenemos las horas de partida y llegada, creando la columna “elapsed_minutes”

SELECT [ride_id]     
      ,[started_at]
      ,[ended_at]
      ,[start_station_name]
      ,[end_station_name]    
      ,[member_casual]
      ,[elapsed_minutes] = DATEDIFF(MINUTE, [started_at], [ended_at])
  FROM [bikes capstone].[dbo].[2020_07_tripdata]


