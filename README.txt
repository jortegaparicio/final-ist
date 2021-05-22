	Practica final de IST utilizando Payara Server y ActiveMQ
	
	Autores: Juan Antonio Ortega Aparicio
			César Borao Moratinos
					 
	Fecha: 22/05/21

	
Este es un breve resumen sobre la práctica final realizada por los alumnos César Borao Moratino y Juan Antonio Ortega Aparicio de la asignatura de ingeniería de sistemas telemáticos. Durante la realización de esta práctica se han visto diferentes tecnologías middleware aplicadas sobre un entorno concurrente. 

En nuestro caso particular nos ha parecido gratamente interesante dicha aplicación de la concurrencia en un sistema de telecomunicaciones, por lo que hemos decidido aplicar nuestros conocimientos al respecto incluyendo la interfaz Callable en un Producer/Consumer sobre Payara, que es un servidor de aplicaciones Jakarta EE popular.  

A su vez, por ver otra interfaz concurrente y aplicar los conocimientos adquiridos en la asignatura sobre la factoría ExecutorsService, hemos incluido la interfaz Runnable en el caso del Publisher/Subscriber, también sobre Payara. 

Además, se ha implementado un servicio de Publisher/Subscriber sobre un ActiveMQ, que es un middleware basado en broker, escrito en Java, que proporciona una implementación completa de JMS (cliente). 

*Nota: En el enunciado de la práctica se nos indica que el programa debe estar parametrizado para cambiar fácilmente el número de hilos que se ejecutan de cada tipo, para ello cada clase Run- contiene una constante que será: NSUBS, NPUBL, NPRODUCERS, NCONSUMERS que deben tener obligatoriamente un valor mayor que 0.

A continuación se tiene una descripción más detallada sobre cada uno de los programas:

-- JMSconcurrPubSub --
        
En esta parte de la práctica final de IST hemos implementado un servicio de JMS publisher-subscriber de forma concurrente y asíncrono.
Para ello hemos implementado 4 clases : TestPubSubAsyncReceiver, PubSubAsyncReceiver, PubSubSender y TestPubSubSender.

- RunSubscribers -

En esta clase se ha implementado un procedimiento principal mediante el cual lanzamos en un pool N subscriptores (N viene definido en la constante
NSUB en el código). Dichos subscriptores ejecutarán de forma concurrente el código implementado en la clase PubSubAsyncReceiver.
Finalmente esperaremos a que termine el pool de hilos lanzados, asegurando con la cláusura finally de que esto será asi pese a cualquier interrupción.

- AsyncSubscriber -

Esta clase implementa la interfaz Runnable, y se encargará de conectar a cada subscriptor de nuestro patrón de diseño publisher-subscriber. 
Es en esta clase donde esperamos a recibir mensajes de algún publicador, incluido el mensaje de cierre (Dicho mensaje viene especificado en la
constante "STOP" y que además deberá coincidir con la constante "STOP" de la clase TestPubSubSender). Finalmente nos aseguramos de que la conexión
 de cada subscriptor se cierre mediante la cláusura finally.

- RunPublisher -

Esta clase es la que se encarga de establecer la conexión de publicador y de lanzar N publicadores concurrentes (N viene definido en la constante NPUB en el código) 
en nuestro patrón de diseño publisher/subscriber. Además garantiza el cierre de la conexión aunque haya alguna interrupción mediante la cláusura finally. 
Finalmente se publica el mensaje de cierre establecido en la constante "STOP" de nuestro código, que además debe coincidir con la constante "STOP" de la clase
PubSubAsyncReceiver tal y como hemos mencionado anteriormente.

- Publisher -

Esta clase implementa la interfaz Runnable, y se encargará de establecer la sesión de cada publicador de nuestro patrón de diseño publisher-subscriber. 
Es en esta clase donde enviamos los mensajes desde el lado del publicador (los mensajes en nuestro código son N números empezando en el 0). 
Finalmente nos aseguramos de que la sesión de cada publicador se cierre mediante la cláusura finally.
  
