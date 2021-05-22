	Practica final de IST utilizando Payara Server y ActiveMQ
	
	Autores: Juan Antonio Ortega Aparicio
			César Borao Moratinos
					 
	Fecha: 22/05/21

	
Este es un breve resumen sobre la práctica final realizada por los alumnos César Borao Moratinos y Juan Antonio Ortega Aparicio de la asignatura de ingeniería de sistemas telemáticos. Durante la realización de esta práctica se han visto diferentes tecnologías middleware aplicadas sobre un entorno concurrente. 

En nuestro caso particular nos ha parecido gratamente interesante dicha aplicación de la concurrencia en un sistema de telecomunicaciones, por lo que hemos decidido aplicar nuestros conocimientos al respecto incluyendo la interfaz Callable en un Producer/Consumer sobre Payara, que es un servidor de aplicaciones Jakarta EE popular.  

A su vez, por ver otra interfaz concurrente y aplicar los conocimientos adquiridos en la asignatura sobre la factoría ExecutorsService, hemos incluido la interfaz Runnable en el caso del Publisher/Subscriber, también sobre Payara. 

Además, se ha implementado un servicio de Publisher/Subscriber sobre un ActiveMQ, que es un middleware basado en broker, escrito en Java, que proporciona una implementación completa de JMS (cliente). 

*Nota: En el enunciado de la práctica se nos indica que el programa debe estar parametrizado para cambiar fácilmente el número de hilos que se ejecutan de cada tipo, para ello cada clase Run- contiene una constante que será: NSUBS, NPUBL, NPRODUCERS, NCONSUMERS que deben tener obligatoriamente un valor mayor que 0.

A continuación se tiene una descripción más detallada sobre cada uno de los programas:


	-- Parte 1: JMSconcurrPtP --

En esta primera parte de la práctica final de IST se ha implementado un servicio de JMS producer-consumer de forma concurrente y con recepción asíncrona.
Lanzaremos varios producers y consumers que interactuarán sobre la misma Cola (Queue) de forma concurrente.

Para ello, hemos implementado cuatro clases: RunProducersTest, RunConsumersTest, P2PAsyncReceiver y P2PSender.
	
	
- RunProducersTest -
	
En esta clase se ha implementado un procedimiento principal mediante el cual lanzamos en un pool N productores (N viene definido en la constante NPRODUCERS en el código). Dichos productores ejecutarán de forma concurrente el código implementado en la clase P2PSender. Finalmente, esperaremos a que termine el pool de hilos lanzados y recogemos los resultados de la ejecución de los hilos productores mediante el método RecoverResults().
	
- RunConsumersTest -
	
Esta clase implementa un procedimiento principal que crea un pool de hilos con la finalidad de lanzar concurrentemente N consumidores asíncronos (N viene definido en la constante NCONSUMERS en el código). Las tareas de consumidor se especifican en la implementación del P2PAsyncReceiver. Además, al igual que en la clase RunProducersTest, tenemos un método RecoverResults() que nos permitirá recoger el status de ejecución de cada hilo lanzado.
	
- P2PAsyncReceiver -
	
Esta clase modela a los consumidores asíncronos y está pensada para correr sobre un hilo del threadpool. Cada consumidor tendrá una nueva conexión, sesión y "receiver", para asegurar la concurrencia en la recepción de los mensajes de la Cola. Cada nuevo consumidor quedará ligado a un MessageListener que posibilita la recepción asíncrona. Cuando se reciba un mensaje CLOSE por parte de un productor, el método OnMessage de MessageListener se queda dormido (sleep) para evitar la recepción de otros mensajes CLOSE antes de cerrar la conexión del consumidor.
	
Además, la clase implementa la interfaz Callable, con la finalidad de devolver feedback al usuario a cerca de ejecuación de los consumidores. Cuando existan consumidores activos (que no hayan leído de la Cola un mensaje CLOSE) y ya no haya más hilos productores en un timeout de 60 segundos, el programa fuerza el cierre y se retorna un status de "WARNING" en los hilos
que han sido forzados a cerrar.
	
- P2PSender -
	
Esta clase modela a los productores y está pensada como una tarea para poder montarla sobre un hilo. Por ello, implementa la interfaz Runnable (nuevamente, hemos elegido Runnable antes que Callable por la ventaja que supone conocer el resultado de la ejecución de los hilos). Cada productor envía tres mensajes a la Cola, seguidos por un mensaje de CLOSE. Posteriormente cierra la conexión y retorna el status.
		
	
Toda la estructura del programa se ejecutará sobre Payara Server, un servidor de aplicaciones que proporciona soporte para JMS. Todo el programa se encuentra parametrizado para poder cambiar el valor del número de producers y consumers. Se asume que el usuario introducirá valores permitidos en dichos parámetros, como que el número de producers y de consumers es mayor o igual a cero.
	
	
	-- Parte 2: JMSconcurrPubSub --
        
En esta parte de la práctica final de IST hemos implementado un servicio de JMS publisher-subscriber de forma concurrente y asíncrono. Para ello hemos implementado 4 clases : TestPubSubAsyncReceiver, PubSubAsyncReceiver, PubSubSender y TestPubSubSender.

- RunSubscribers -

En esta clase se ha implementado un procedimiento principal mediante el cual lanzamos en un pool N subscriptores (N viene definido en la constante NSUB en el código). Dichos subscriptores ejecutarán de forma concurrente el código implementado en la clase PubSubAsyncReceiver. Finalmente esperaremos a que termine el pool de hilos lanzados, asegurando con la cláusura finally de que esto será asi pese a cualquier interrupción.

- AsyncSubscriber -

Esta clase implementa la interfaz Runnable, y se encargará de conectar a cada subscriptor de nuestro patrón de diseño publisher-subscriber. Es en esta clase donde esperamos a recibir mensajes de algún publicador, incluido el mensaje de cierre (Dicho mensaje viene especificado en la constante "STOP" y que además deberá coincidir con la constante "STOP" de la clase TestPubSubSender). Finalmente nos aseguramos de que la conexión de cada subscriptor se cierre mediante la cláusura finally.

- RunPublisher -

Esta clase es la que se encarga de establecer la conexión de publicador y de lanzar N publicadores concurrentes (N viene definido en la constante NPUB en el código) en nuestro patrón de diseño publisher/subscriber. Además garantiza el cierre de la conexión aunque haya alguna interrupción mediante la cláusura finally. Finalmente se publica el mensaje de cierre establecido en la constante "STOP" de nuestro código, que además debe coincidir con la constante "STOP" de la clase PubSubAsyncReceiver tal y como hemos mencionado anteriormente.

- Publisher -

Esta clase implementa la interfaz Runnable, y se encargará de establecer la sesión de cada publicador de nuestro patrón de diseño publisher-subscriber. Es en esta clase donde enviamos los mensajes desde el lado del publicador (los mensajes en nuestro código son N números empezando en el 0). Finalmente nos aseguramos de que la sesión de cada publicador se cierre mediante la cláusura finally.
  

	-- Parte 3: ActiveMQconcurrPubSub --

En esta tercera parte de la práctica final de IST hemos implementado un servicio de ActiveMQ publisher-subscriber de forma concurrente y asíncrono.
Para ello hemos implementado 4 clases: AsyncSubscriber, Publisher, RunPublishers y RunSubscribers.
	
- AsyncSubscriber -
	
Esta clase modela una tarea de subcriber asíncrono. Para ello, implementa tres interfaces: la interfaz Runnable para poder ejecutar la tarea sobre un pool de threads, la interfaz ExceptionListener para poder imprimir error en el caso de que suceda una excepción de JMS, y la interfaz que posibilita la recepción asíncrona de los mensajes del topic: la interfaz MessageListener. Al igual que el la práctica del PubSub asíncrono con Payara, hemos escogido implementar la interfaz Runnable en vez de Callable, puesto que en el caso de un escenario publishers-subscribers todos los consumidores se cierran al recibir el mismo mensaje "CLOSE".
	
La clase configura todo lo necesario para ejecutar un subscriber (conexión, sesión y subscriber). Luego, lee todos los mensajes que mande el publisher al topic, y cuando recibe un mensaje "CLOSE", cierra la conexión. Otra cosa a destacar, es que en esta clase cerramos las conexiones en orden: primero el subscriber, luego la sesión y lo último la conexión. Esto es diferente al funcionamiento con Payara server de las otras partes de la práctica, donde únicamente cerrábamos la conexión (puesto que con hacer eso, se cerraba todo lo que iba por debajo), gracias al soporte que nos daba el servidor de comunicaciones

- Publisher -
	
Esta clase modela una tarea de publisher. Implementa la interfaz Runnable para poder ejecutar la tarea sobre un pool de threads.La clase configura todo lo necesario para ejecutar un publisher (conexión, sesión y subscriber) de forma concurrente. A continuación manda tres mensajes al Topic y un último mensaje "CLOSE". Una vez realizada la tarea, cierra la sesión y la conexión y termina.
	
- RunPublishers -

Ejecuta un pool de hilos que realizan de forma concurrente tareas Publisher. Lo primero que hace es conectarse a la ConnectionFactory del broker (lanzado como proceso independiente) y a partir de ahí lanza N publishers (parametrizado) de forma concurrente.
Una vez ha acabado, espera que termine el threadpool y termina su ejecución.
	
- RunSubscribers -

Ejecuta un pool de hilos que realizan de forma concurrente tareas Subscriber asíncronos. Lo primero que hace es conectarse a la ConnectionFactory del broker (lanzado como proceso independiente) y a partir de ahí lanza N subscribers (parametrizado) de forma concurrente. Una vez ha acabado, espera que termine el threadpool y termina su ejecución.
	
	
Nota: Todo el programa se encuentra parametrizado para poder cambiar el valor del número de publishers y subscribers. Se asume que el usuario introducirá valores permitidos en dichos parámetros, como que el número de publishers y de subscribers es mayor o igual a cero.
	
	


