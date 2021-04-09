## This can be your internal website page / project page

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
	  	        
  FROM [AdventureWorks2019].[Purchasing].[PurchaseOrderDetail] A
  JOIN [AdventureWorks2019].[Purchasing].[PurchaseOrderHeader] B
  ON A.[PurchaseOrderID] = B.[PurchaseOrderID]

```
