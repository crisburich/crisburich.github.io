## This can be your internal website page / project page

**Project description:** Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

### 1. Suggest hypotheses about the causes of observed phenomena

Sed ut perspiciatis unde omnis iste natus error sit voluptatem accusantium doloremque laudantium, totam rem aperiam, eaque ipsa quae ab illo inventore veritatis et quasi architecto beatae vitae dicta sunt explicabo. 

```javascript

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

### 2. Assess assumptions on which statistical inference will be based

```javascript
if (isAwesome){
  return true
}
```

### 3. Support the selection of appropriate statistical tools and techniques

<img src="images/dummy_thumbnail.jpg?raw=true"/>

### 4. Provide a basis for further data collection through surveys or experiments

Sed ut perspiciatis unde omnis iste natus error sit voluptatem accusantium doloremque laudantium, totam rem aperiam, eaque ipsa quae ab illo inventore veritatis et quasi architecto beatae vitae dicta sunt explicabo. 

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).
