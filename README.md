rabbitmq-demo
=============

##########################################
#send.py
##########################################

	import pika

	# Set the connection parameters to connect to rabbit-server1 on port 5672
	# on the / virtual host using the username "guest" and password "guest"
	credentials = pika.PlainCredentials('guest', 'guest')
	parameters = pika.ConnectionParameters('selte.net',
                                       5672,
                                       '/',
                                       credentials)

	#params = pika.URLParameters('amqp://guest:guest@selte.net:5672/%2F')

	connection = pika.BlockingConnection(parameters)
	channel = connection.channel()
	channel.queue_declare(queue='hello')

	for i in range(10000):
		channel.basic_publish(exchange='',
        	              routing_key='hello',
                	      body='Hello World! %s' % i)
		print " [x] Sent %s" % i
	connection.close()



##########################################
#recv.py
##########################################

#!/usr/bin/env python
import pika


params = pika.URLParameters('amqp://guest:guest@selte.net:5672/%2F')
connection = pika.BlockingConnection(params)
channel = connection.channel()

channel.queue_declare(queue='hello')

print ' [*] Waiting for messages. To exit press CTRL+C'

def callback(ch, method, properties, body):
    print " [x] Received %r" % (body,)

channel.basic_consume(callback,
                      queue='hello',
                      no_ack=True)

channel.start_consuming()
