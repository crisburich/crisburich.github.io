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
