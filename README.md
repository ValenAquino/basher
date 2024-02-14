# Basher
La reconocida empresa Basher está diseñando un nuevo sistema para manejar pagos en su división de agroquímicos y, para ello, ha decidido contratar a 2Diseños (dicen que laburan bien, esperemos que así sea :P) para que se hagan cargo del proyecto. 

## Contexto
Cuando un productor agropecuario compra un producto a Basher, este puede elegir pagarlo con distintos medios de pago. Por ahora, nos centraremos en aquel conocido como pago en especias, en el cual básicamente el cliente lleva el producto firmando un contrato que dice que, cuando este haya cosechado el grano cultivado, entregará una parte a Basher en forma de pago diferido, a un precio por tonelada convenido.

### Contratos
Cada vez que un cliente paga en especias, se debe crear un contrato en el sistema que refleje el acuerdo. Todos los contratos tienen una estructura similar compuesta por diferentes cláusulas donde se especifica algún tipo de condición legal, con los valores específicos para cada documento. **Las cláusulas que todo contrato debe tener son:**
* **Acuerdo entre partes:** Documenta que se está librando un contrato bajo determinadas normas legales y detalla nombre y CUIL/CUIT de Basher y de la otra persona jurídicas o física interviniente.
* **Vencimiento:** Indica cuándo es la fecha límite para entregar el grano adeudado.
* **Fijación de precio:** Detalla el monto en dinero que se adeuda, el grano con el que se pagará (maíz, trigo, soja…), el precio por tonelada de grano acordado y las toneladas de grano que se deberán entregar para alcanzar el monto adeudado.

Diferentes cláusulas del mismo tipo contendrán la misma información, pero enunciada de forma diferente. Por ejemplo: “El contrato vence el 12/07” o “El presente acuerdo caduca el 12/07”.
Esto es porque a cada contrato en particular aplican determinados marcos legales. 

Además se pueden agregar cláusulas adicionales propias del marco legal correspondiente. Su contenido es fijo, es decir, no tienen datos a completar.

Un usuario ha de poder elegir el marco para un contrato, completar sus datos específicos e imprimirlo con el texto completo de todas sus cláusulas. 

### Matching  (Asociación)


¡Casi como en Tinder!… pero no. Una vez creado un contrato, eventualmente el productor enviará un camión con el grano adeudado para que sea descargado. Es entonces cuando el sistema deberá registrar una Descarga. Esta descarga contendrá información sobre el cliente que la entrega, el grano y la cantidad entregada.

Cada vez que una descarga es ingresada en el sistema, esta debe ser matcheada (asociada) con un contrato afín, es decir, uno que sea del mismo cliente y por el mismo grano para, posteriormente, ser liquidada (ver siguiente apartado) y saldar la deuda. 

Como los camiones solo cargan 30tn y los contratos tienen tamaños arbitrarios, varias descargas pueden saldar un mismo contrato y viceversa. 
Además, el sistema debe asociar prioritariamente las descargas con contratos cuya fecha de vencimiento sea más antigua y, a igualdad de esta, elegir primero aquellos con menos saldo adeudado por cubrir.

### Liquidaciones

Finalmente, una vez asociadas, se debe proceder a liquidar las descargas. Esto es, usando el precio convenido en el o los contratos con los que hayan asociado, volver a convertir el grano en dinero para impactarlo en la cuenta corriente del cliente. 

Para esto, una vez calculado el monto a impactar se debe registrar la liquidación en AFIP mediante un Servicio Web para que tenga validez legal. Tras esto, se debe impactar esta liquidación en la cuenta corriente del cliente en el sistema contable de la compañía, SAP.  Recién en esta instancia, será posible considerar que la liquidación fue exitosamente realizada.

Por diferentes motivos, cualquiera de estas operaciones (registro en AFIP o SAP) puede fallar. En caso de que así sea, el usuario deberá poder consultar el mensaje de error asociado, para corregir el problema y luego reintentar el registro de la liquidación. En este caso, la registración deberá continuar desde donde se había producido el fallo. 

Por último, cada vez que un contrato sea totalmente liquidado, o bien pase su fecha de vencimiento sin haber sido completado, se debe enviar un mail al cliente, si este lo desea, y a el o los ejecutivos de cuentas que hayan marcado ese contrato para su seguimiento.

## Se pide: 
***Comunicar una solución usando prosa, diagramas y código o pseudocódigo:***

1) Crear un contrato con sus cláusulas específicas y obtener el texto a imprimir para el mismo.

2) Asociar una descarga con un contrato compatible cuando es ingresada al sistema, respetando el orden de prioridades.

3) Liquidar un asociación utilizando el precio del contrato y el peso correspondiente. Por ejemplo:

    > Si de una descarga de 30tn, 10tn se asociaron al contrato A de 100            USD/tn y 20tn con el contrato B de 200 USD/tn, se deberán hacer dos liquidaciones, una por 1000 USD y otra por 4000 USD.*

4) Registrar una liquidación en AFIP y SAP. Asumir que ambos utilizan interfaces JSON en las que toman el monto a liquidar y devuelven un mensaje de éxito o un mensaje de error.

5) Reintentar una liquidación desde donde se quedó. Por ejemplo:

    > Si una liquidación falló al registrarse en SAP, al presionar “Reintentar” esta no debe volver a ser enviada a la AFIP.

6) Enviar un mail a todos aquellos que estén haciéndole seguimiento al contrato (cliente, ejecutivos de cuentas):

    1) Cuando un contrato es totalmente liquidado, es decir, cuando ha sido saldado en su totalidad a nivel toneladas y todos sus asociaciones han sido liquidados exitosamente.

    2) Cuando el contrato se haya vencido sin haber sido saldado en toneladas

### ***Se cuenta con la siguiente interfaz:***

![Interfaz del MailSender]([//www.plantuml.com/plantuml/png/SoWkIImgAStDuShCAqajIajCJbNmJSpC2KxDIqajKgZcAWOoD19JKefIYtKKYakJibAJItGKal9JAZMvQhaSWB8WpG00](https://www.plantuml.com/plantuml/png/SoWkIImgAStDuShCAqajIajCJbNmJSpC2KxDIqajKgZcAWOoD19JKefIYtKKYakJibAJItGKal9JAZMvQhaSWB8WpG00)https://www.plantuml.com/plantuml/png/SoWkIImgAStDuShCAqajIajCJbNmJSpC2KxDIqajKgZcAWOoD19JKefIYtKKYakJibAJItGKal9JAZMvQhaSWB8WpG00)
