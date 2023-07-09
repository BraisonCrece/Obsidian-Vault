---
sticker: 1f4c8
tag: performance-service
banner: https://www.smallbusinessact.com/wp-content/uploads/2022/05/COMMENT-AMELIORER-LA-PERFORMANCE-FINANCIERE-DUNE-ENTREPRISE-01.png
---
At Flatex there are (probably) multiple applications responsible for handling processes and data. At multiple times per day import processes from those applications into DWH (Flatex Postgres database) tables are running, to make sure all relevant data for us is present "in time". 

The update frequency differs related on the type of data. Whilst data related to customers get synced multiple times per day, data related to accounts, depots, statements, etc might only get synced once per night.

Whitebox has no access to DWH database tables. We only have access to database views that have been set up for Whitbox use cases. You can find a bit more info [here](https://github.com/whiteboxservices/whitebox-core/tree/develop/docs/dwh) in German. Besides the views, we can access<mark style='background:#2d98da'> one specific postgreSQL function </mark>that has been set up to provide us with all data to be able to <mark style='background:#3867d6'>calculate the performance of our client's investments</mark>. The function uses both tables and views to gather the relevant data. The function is called `sp_if_wbget_daten`

Whitebox has implemented the Performance Service which is responsible for **fetching the data via the DWH function**. 

When fetching the data for multiple days or even years, the DWH function tends to get slower, because it is iterating over all days and calculating all relevant data over and over again. 

That's why we decided to set up a table in our system called `settlements` which the Performance Service writes the data, which it receives from the DWH function. To calculate the performance our performance service now accesses the internal `settlements` table.

The loss amount threshold service uses the performance service to get the time-weighted returns for a specific period to calculate the value we have to send to Flatex so that they can handle the loss amount alerts they potentially have to send to our clients.


![[Pasted image 20230602092929.png]]
WhiteBox:
- El nucleo de la aplicación, tiene acceso a vistas creadas por Flatex con los datos relevantes para WhiteBox
- El núcleo tiene acceso también a los datos de performance de los portfolios de los clientes (mediante Performance Service)
- Al mismo tiempo a través del Performance Service se actualizan los datos de rendimiento de los activos de los usuarios de WhiteBox (diariamente a las 7:30 am)
- Se calcula el `loss_amount_threshold` utilizando datos facilitados por el `Performance Service` ([[Time-Weighted Return]])

- `Performance Service` se comunica con la `Performance-Function` de Flatex, que se encuentra dentro de su servicio DWH
- DWH proporciona vistas especialmente creadas para WhiteBox con datos relevantes, utilizando tanto la función `Performance-Function` como las propias tablas de la base de datos de Flatex

Condiciones previas:
- Los mercados abren a las 08:00 a. m. 
- A las 08:00 a.m. obligatorio tener listo el cálculo del `Time-Weighted Return` y transferir el resultado a Flatex porque luego comienzan a observar las pérdidas potenciales durante el día para decidir si enviar o no enviar alertas a nuestros clientes 
- Flatex proporciona todos los datos necesarios en el DWH hasta las 07:30 
- El cálculo del umbral de la cantidad de pérdida requiere un valor proporcionado por el servicio de desempeño: rendimientos ponderados en el tiempo en un rango de tiempo determinado Por lo tanto, tenemos **30 minutos para calcular toda la pérdida umbrales de cantidad** para todos los objetivos existentes La cantidad de objetivos activos (con suerte) aumenta a diario

## <span style='color:#eb3b5a'>Problemas</span> potenciales y ya ocurridos

- **<span style='color:#fa8231'>Los datos de rendimiento no están disponibles en absoluto</span>**
     - No podemos sincronizar los datos de la función DWH en nuestra tabla de `liquidaciones` (`settlements`)
     - En este supuesto, nuestro [servicio de umbral](https://github.com/whiteboxservices/whitebox-core/blob/6232a0ccaaf284e987aadc4d7b2f844abf2bf8ee/app/services/internal/threshold_service.rb#L154) genera un error que nos indica que los datos no han sido actualizados.
     - No podemos calcular el umbral del monto de la pérdida y no podemos actualizar la alerta a través de la API de SOAP
- **<span style='color:#fa8231'>Los datos de rendimiento no están disponibles a tiempo</span>**
     - No podemos sincronizar los datos de la función DWH en nuestra tabla de `liquidaciones`
     - En este caso, se arroja el error anterior.
     - Estamos reintentando el cálculo del monto de la pérdida [siete veces](https://github.com/whiteboxservices/whitebox-core/blob/6232a0ccaaf284e987aadc4d7b2f844abf2bf8ee/app/workers/loss_amount_threshold_worker.rb#L6)
     - Si los datos no están presentes a tiempo, no podemos calcular el umbral del monto de la pérdida y no podemos actualizar la alerta a través de la API de SOAP.
- **<span style='color:#fa8231'>Los datos de rendimiento solo están parcialmente disponibles</span>**
     - En tal escenario, la función DWH devuelve una entrada para el último día, pero es posible que falten algunos datos.
     - p.ej. los datos de la cuenta de efectivo están disponibles, pero aún no se proporciona el saldo de depósito
     - Sincronizamos los datos de la función DWH en nuestra tabla de 'liquidaciones' pero no entendemos que faltan datos
- **<span style='color:#fa8231'>La función de rendimiento está desactualizada</span>**
     - Sincronizamos los datos de la función DWH en nuestra tabla de 'liquidaciones' pero no sabemos que los datos son incorrectos
     - Este podría ser el caso cuando Flatex introduce un nuevo tipo de transacción para **contribuciones o retiros**, pero la función de rendimiento no se actualiza a tiempo. Luego, el cálculo del rendimiento ve las contribuciones como datos relevantes para el rendimiento y terminamos con umbrales incorrectos de rendimiento y cantidad de pérdida.
- **<span style='color:#fa8231'>La función de rendimiento es incorrecta</span>**
     - Probablemente lo mismo que la función está desactualizada como se mencionó anteriormente, pero probablemente ocurrieron otros problemas con la sincronización
- **<span style='color:#fa8231'>La función de rendimiento es demasiado lenta</span>**
     - Como se mencionó anteriormente, la función de rendimiento DWH itera durante todos los días y luego calcula los datos relevantes sobre la marcha. Esto se vuelve más y más lento con el tiempo a medida que las inversiones envejecen.
     - Ya optimizamos este proceso configurando la tabla `liquidaciones` en nuestra base de datos que contiene una copia de los datos proporcionados por la función de rendimiento DWH.
     - Los problemas se producen como se mencionó anteriormente, especialmente cuando los datos son incorrectos o solo están parcialmente disponibles.
     - Nuestro proceso de sincronización diario siempre obtuvo los datos de los últimos siete días y con esto actualizó los valores si se corrigieron durante el último día.
     Si la función de rendimiento produce valores incorrectos durante más tiempo, entonces debemos obtener los datos manualmente.
     Esto es posible a través de nuestra interfaz de administración
- **<span style='color:#fa8231'>El umbral del monto de la pérdida no se ejecuta en ningún orden especial</span>**
     - En el pasado teníamos problemas para llegar a las 08:00 a.m. hasta actualizar la alerta de monto de pérdida a través de SOAP
     - Esto es especialmente un problema para los objetivos con retiros que ocurren el día anterior
     - p.ej. El volumen de inversión es de 120 000 €, el umbral de cantidad de pérdida se fijó en 100 000 € ayer, ayer retiramos 80 000 € y no actualizamos la alerta para este objetivo hasta que Flatex comienza a observar la cantidad de pérdida, luego el cliente recibe una alerta de cantidad de pérdida , porque Flatex sigue pensando que deberían enviar la alerta cuando el volumen de inversión cae por debajo de los 100.000€


## <span style='color:#20bf6b'>Soluciones</span> potenciales

- **<span style='color:#20bf6b'>Ejecutar la sincronización varias veces al día</span>**
     - Esto podría solucionar principalmente los gráficos de rendimiento y las vistas que mostramos a nuestros clientes. Si los datos no han estado completamente disponibles a las 07:30, se actualizarán automáticamente en la segunda o tercera prueba.
     - Esto no ayuda con el cálculo del umbral del monto de la pérdida que debe finalizar a las 08:00 a.m.
- **<span style='color:#20bf6b'>Ejecutar background job de umbral de cantidad de pérdida varias veces al día</span>**
     - En combinación con la ejecución de la sincronización DWH varias veces al día, también podríamos ejecutar el umbral del monto de pérdida más de una vez al día. Esto podría ayudar cuando los mercados caen más tarde en el día, por lo que podría darnos más tiempo para volver a calcular umbrales potencialmente incorrectos.
- **<span style='color:#20bf6b'>Resincronice manualmente los datos de DWH en</span> `asentamientos`**
     - Esto ya es posible ahora y se puede usar en los casos en que entendemos que los datos son incorrectos y podemos solucionarlo resincronizando los datos.
- **<span style='color:#20bf6b'>Ejecutar sincronización solo cuando todos los datos están en tablas</span>**
     - Como sabemos que la función de rendimiento DWH recopila los datos de las vistas y las tablas, y las tablas asignan principalmente datos de las tablas, podemos verificar si los datos de ayer están presentes en las tablas.
     - Los inconvenientes son:
         - No tenemos acceso a las tablas de la bbdd.
         - Todavía no sabemos si los datos son correctos a pesar de que están presentes
         - Tenemos que conocer realmente la función de rendimiento y reaccionar a los cambios.
- **<span style='color:#20bf6b'>Ejecutar la sincronización solo cuando todos los datos estén en vistas</span>**
     - Igual que arriba, el beneficio es que tenemos acceso a las vistas.
     - Los inconvenientes son:
         - Como arriba, todavía no sabemos si los datos son correctos
         - Tenemos que conocer la función de rendimiento y reaccionar a los cambios.
- **<span style='color:#20bf6b'>Informar vía Flatex que todos los datos han sido importados</span>**
     - Podríamos pedirle a Flatex que nos informe cuando todos los datos se hayan importado correctamente al DWH
     - Solo entonces podemos iniciar nuestros procesos automatizados para sincronizar datos e iniciar el cálculo del umbral de cantidad de pérdida
- **<span style='color:#20bf6b'>Replicar DWH en nuestra propia base de datos en AWS</span>**
     - Nuestro equipo de desarrollo ya ha iniciado este proceso
     - Si tenemos acceso a las tablas DWH, podríamos recopilar todos los datos que son relevantes para nosotros en nuestras propias vistas
     - Tendríamos la capacidad de crear y optimizar la función de rendimiento.
     - Cuando flatex agrega nuevos tipos de transacciones, podemos reaccionar mucho más rápido y cambiar la función de rendimiento. Por lo tanto, no tenemos que esperar el ciclo de implementación trimestral de Flatex hasta que corrijan la función y proporcionemos un rendimiento incorrecto a nuestros clientes.
     - Las actualizaciones de la base de datos se replicarán instantáneamente para nosotros y podremos reaccionar de inmediato ante tales eventos.

## Preguntas abiertas

- **¿Somos capaces de detectar datos incorrectos?**
     - Con las medidas anteriores, probablemente podamos reducir el riesgo de proporcionar datos de rendimiento incorrectos a nuestros clientes y calcular valores de umbral de cantidad de pérdida incorrectos.
     - Todavía no podemos estar 100% seguros de que los datos sean correctos o no
     - Dependiendo de las medidas tomadas, probablemente todavía tengamos que optimizar nuestros procesos internos, agregar más controles automatizados para reducir el riesgo mencionado anteriormente.



| Issues                                                      | Direct Solutions                                           | Indirect Solutions                                          |
|-------------------------------------------------------------|------------------------------------------------------------|-------------------------------------------------------------|
| Performance data is not available                           | Manually resync DWH data into `settlements`                | Replicate DWH into our own Database at AWS                  |
| Performance data is not available on time                   | Run sync multiple times per day                            | Get informed by flatex that all data has been imported     |
| Performance data is only partially available                | Run sync only when all data is in tables/views             | -                                                           |
| Performance function is outdated                            | -                                                          | Replicate DWH into our own Database at AWS                  |
| Performance function is incorrect                           | -                                                          | Replicate DWH into our own Database at AWS                  |
| Performance function is too slow                            | Run sync multiple times per day                            | Replicate DWH into our own Database at AWS                  |
| The loss amount threshold runs in no special order          | Run loss amount threshold worker multiple times per day    | -                                                           |
