
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
