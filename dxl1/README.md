1106  
dxl2 다이나믹셀  

^C를 눌러도 한번에 꺼지지 않는 이유  
컨트롤 c는 인터럽터로 눌러도 버퍼에 남는 것이 아니라 bool 변경  
코드 내에서는 while문이 cin에 막혀서 if문까지 가지 않음

급가속 급감속 해결  
코드 작성해서 천천히 해당 속도에 도달하도록 설정  

![image](https://github.com/user-attachments/assets/d0463e58-6f5b-43c4-9af0-86ffb055c759)

  