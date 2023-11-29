# Mobile application - MyTodoList

  - Want to expose REST API that has HTTPS endpoints  
  - Serverless Architecture  
  - Users should be able to directly interact with their own folder in S3  
  - Users should authenticate through a managed serverless service  
  - Users write to-dos but mostly read them  
  - Database should scale and have high read throughput  

Basic Architecture:
![Alt text](images/TodoList_Basic.png)

If you need to give access to S3
![Alt text](images/TodoList_S3.png)
(Here wrong answer would be to store AWS credentials in mobile, you need to use Cognito and STS)

![Alt text](images/TodoList_Scale.png)
(We have very high RCU or high read throughput, using DAX will be effective and will save some cost maybe because we may not need to scale.)

![Alt text](images/TodoList_Caching.png)
(Further caching can be enabled at the API gateway, by caching the response for some API routes if the answers dont change)

