Se describen dos tipos de clientes según su residencia fiscal: residentes fiscales alemanes y no alemanes.

Para **residentes fiscales alemanes**, se pueden configurar:

1. **Residencia fiscal (Steuerstatus)**: puede ser residente fiscal alemán o no. Actualizable mediante formulario en papel.

2. **Número de Identificación Fiscal - TIN (STEUER-IDENTIFIKATIONSNUMMER)**: debe ser completado por todos los clientes. Es editable para residentes fiscales alemanes.

3. **Orden de Exención (Freistellungsauftrag)**: es un descuento en impuestos para residentes fiscales alemanes. Los clientes tienen un descuento de 802 euros por año.

4. **Datos de impuestos de la iglesia (Kirchensteuerdaten)**: algunos clientes alemanes podrían pagar impuestos de iglesia. Esta sección es de solo lectura.

5. **Certificado N.V. (N.V. Bescheinigung)**: es un atributo de solo lectura. Puede estar presente o no.

6. **Transacciones fiscales (Steuerbuchungen)**: muestra las transacciones fiscales para el período.

7. **Recibos fiscales (Steuerbescheinugungen)**: muestra los documentos disponibles para el período.

Para **no residentes fiscales alemanes**, se pueden configurar:

1. **Residencia fiscal (Steuerstatus)**: puede ser actualizada llenando un formulario en papel.

2. **Número de Identificación Fiscal - TIN (STEUER-IDENTIFIKATIONSNUMMER)**: este campo es de solo lectura para no residentes fiscales alemanes.

3. **Plantilla de informe fiscal (Steuerreport-Vorlage)**: solo visible si el cliente es no residente fiscal alemán, el tipo de billetera es privada o corporación, y la fecha actual es antes de una fecha de corte (30 de junio del año en curso).

Esquema:

**Residentes Fiscales Alemanes**
1. Residencia Fiscal
2. Número de Identificación Fiscal - TIN
3. Orden de Exención
4. Datos de impuestos de la iglesia
5. Certificado N.V.
6. Transacciones fiscales
7. Recibos fiscales

**No Residentes Fiscales Alemanes**
1. Residencia Fiscal
2. Número de Identificación Fiscal - TIN
3. Plantilla de informe fiscal
