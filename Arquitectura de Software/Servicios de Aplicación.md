---
sticker: 1f3ed
---
# Servicios de Aplicación

![](https://s3.amazonaws.com/pathwright-uploads/mGslZSJNSM26idCbW11L_09EkzTi5Sg2O1LmchmN0_03%25252BQue%252525CC%25252581%25252Bes%25252Bla%25252BArquitectura%25252BHexagonal.key%25252B2017-12-15%25252B12-10-33.png)

- Son los **puntos de entrada a nuestra aplicación**. Es decir, como se ve en el gráfico, los controladores de línea de comandos o de nuestra API HTTP invocarán a los servicios de aplicación.
- **Representan de forma atómica un caso de uso de nuestro sistema**. En caso de modificaciones del estado de nuestra aplicación:
    - Podrán hacer las de **barrera transaccional** con el sistema de persistencia.
    - Publicarán los eventos de dominio respectivos.
- Coordinan las llamadas a los distintos elementos de nuestro sistema para ejecutar un determinado caso de uso.
- Les llamaremos indistintamente Servicio de Aplicación como caso de uso.

