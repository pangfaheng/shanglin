# Get Rabbitmq Status Command

```shell
sudo rabbitmqctl cluster_status
sudo rabbitmqctl list_queues
```

# Install Python Module

```shell
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
python3 get-pip.py
python3 -m pip3 install pika --upgrade
```

# Python Code

## sending message to rabbitmq server

```python
#!/usr/bin/env python3
import pika
import random, string

connection = pika.BlockingConnection(
    pika.ConnectionParameters('localhost'))
channel = connection.channel()

channel.queue_declare(queue='hello')

channel.basic_publish(exchange='',
                      routing_key='hello',
                      body='Hello World!')
print(" [x] Sent 'Hello World!'")

connection.close()
```

## sending messages to rabbitmq server

```python
#!/usr/bin/env python3
import pika
import random, string

connection = pika.BlockingConnection(
    pika.ConnectionParameters('localhost'))
channel = connection.channel()

for i in range(0,100000):
    random_body = ''.join([random.choice(string.ascii_lowercase + string.ascii_uppercase + string.digits + string.punctuation) for _ in range(128)])
    channel.queue_declare(queue='hello')
    channel.basic_publish(exchange='',routing_key='hello', body=random_body)

connection.close()
```

```shell
for ((i=1;i<=100;i++)); do nohup ./sending_message.py & done
```



## receiving messages from rabbitmq server

```python
#!/usr/bin/env python3
import pika

connection = pika.BlockingConnection(
    pika.ConnectionParameters('localhost'))
channel = connection.channel()

channel.queue_declare(queue='hello')

def callback(ch, method, properties, body):
    print(f" [x] Received {body}")

channel.basic_consume(queue='hello',
    auto_ack=True,
    on_message_callback=callback)

print(' [*] Waiting for messages. To exit press CTRL+C')
channel.start_consuming()

if __name__ == '__main__':
    try:
        main()
    except KeyboardInterrupt:
        print('Interrupted')
        try:
            sys.exit(0)
        except SystemExit:
            os._exit(0)

```

