- Comunição  entre dois sistemas:
  
  - **Síncrona:** sistema A faz uma chamada HTTP para o sistema B (pode ser uma chamada rest ou soap). Problema: não podemos confiar na rede, essa chamada pode falhar, o 'client' pode perder conexão. Solucão: **comunição assíncrona** 
  - **Assíncrona:**  Sistema A coloca as mensagens em uma mensageria (broker), o sistema B "pergunta" para o sistema A se há mensagens salvas no broker a serem processadas. Vantagem: tolerância a indisponibilidades. Essas mensagens ficam salvas temporariamente no broker. O grande benefício da mensageria é desacoplar o sistema, a mensagem fica salva no broker, aguardando para ser consumida, ou seja, tolerante a falhas de comunição de rede
  
  - Criar queue usando o Amazon SQS
        - Tipo de queue: Standard ou FIFO (first-in-first-out delivery)
  - Criar IAM: criar user (Access key - programmatic access), selecionar a política "AmazonSQSFullAccess"
  - Usar o AWS Cli, executar os comandos para autenticar com as credenciasi:
    'aws help' pra ver se o AWS CLI está instaldo
    aws configure: 
- Primeiros comandos SQS:
    - aws sqs help: lista de comandos do sqs

    - aws sqs list-queues: lista de queues criadas

    Ex: 
    
        {
        "QueueUrls": [
            "https://sqs.sa-east-1.amazonaws.com/488929950883/alura-test"
        ]
    }
    - AWS sqs send-message help: entrega uma mensagem para uma fila específica
        parâmetros obrigatórios:
            --queue-url: url da fila
            -- message-body: corpo da mensagem
- Executando o comando para enviar a mensagem "Olá, mundo" para o SQS:
            aws sqs send-message --queue-url https://sqs.sa-east-1.amazonaws.com/488929950883/alura-test --message-body "Olá, mundo"


                    {
            "MD5OfMessageBody": "422b212378ba94b703961607191fbd49",
            "MessageId": "565bd4ac-239d-419f-8007-fe9ce13cc521"
        }


- Executando o comando para receber a mensagem do SQS: 

    - aws sqs receive-message: receber mensagem
        parâmetros:
        --queue-url:

    aws sqs receive-message https://sqs.sa-east-1.amazonaws.com/488929950883/alura-test 
    Output:  

            {
            "Messages": [
                {
                    "MessageId": "565bd4ac-239d-419f-8007-fe9ce13cc521",
                    "ReceiptHandle": "AQEBzMWOJOOAAuNA52k5bt1zZR/Ig8Ymcjl4aOzzsP2S1U9X2TixYIUAIeiLaUUmMc6ujW1MWFwNScBHuwCwWFXKJCXmilc9Ue6gn0KIyf3lIFRwkHbGcKf2Zs68IL0XyWahSt/wTUOnhfMtil2mohLatA/qNE5FTqhx0BJ4NTeeUqCGdcV1EJPllQXyduijNgkmjv3oCdxUsknG34e6gPtYOC/dshRZSKoeuZBKuadNBqYg0Hlxcc+m4xodQ3WjyRli3uRUTNQo0U/sRu7ZG/v+5Q7FvYQwMIknyPnWZUc/62AzZZWvKNIkmjQ0xgBRLFKETqBkNrLFL0NmEEv0/olHAe7+nqrEHWUaTdIAejEmspxvZw8z4B8M8Od0QdIvc/1+IluAwdt/Re1xqxclGHLOwA==",
                    "MD5OfBody": "422b212378ba94b703961607191fbd49",
                    "Body": "Olá, mundo"
                }
            ]
        }

- Pegar o URL da fila e deletar essa fila:
    1) aws sqs delete-queue help
    2) aws sqs list-queues

        {
            "QueueUrls": [
                "https://sqs.sa-east-1.amazonaws.com/488929950883/alura-test"
            ]
        }

    3) aws sqs delete-queue --queue-url https://sqs.sa-east-1.amazonaws.com/488929950883/alura-test

- Criar nova fila de mensagem:
    1) aws sqs create-queue help
    2) aws sqs create-queue --queue-name sqs-teste-lab
    3) aws sqs list-queues 

- Processamento de mensagens:
  - Excluir mensagens já processadas
  - aws sqs receive-message --queue-url https://sqs.sa-east-1.amazonaws.com/488929950883/sqs-teste-lab


        {
            "Messages": [
                {
                    "MessageId": "68b07c81-9353-4a85-8e68-b032e5a3da9c",
                    "ReceiptHandle": "AQEBiRlOmL0SARk6UNiagJRbmfo7bPGWt8/uR3serpMKHfVhOyRNEmQaBR8iUsLaVZDYmDt5TgRAhjL+IumzVzYpHl/OK1AJ6SQbjFdcqS0u2zPG+Ub80CXlHUZBQTj8DLAvodfV0g0x7V21vKf7GsWLwOJ2eZbNzLW4gzm5ihx6PjvPdBpGxXVw5/BSdclSPfkMND7hhZQK5uCINYW75MbhNguq+Nz9IvXspcmg73/gi9BmNGNQkhokYX5bKFx1ku3KWWyDrsxVr846iM8UdleQGsG9MTaQwYviCrs9dciiH/GZlX2vRPS0ltppYDd2n3VhDVzSUWw1OCwfdx2D/M92mq88sWd9O5m/mErt3dIFGJrlzPetk69riN2cdRN0dzcNOtYkp5b4NlU77bi0i8mXqw==",
                    "MD5OfBody": "422b212378ba94b703961607191fbd49",
                    "Body": "Olá, mundo"
                }
            ]
        }

  - O que é o "ReceiptHandle"? é o controle de recibo da mensagem
  - Deletando mensagens já processadas:
  
            aws sqs delete-message --receipt-handle <ReceiptHandle> --queue-url https://sqs.sa-east-1.amazonaws.com/488929950883/sqs-teste-lab
  
- Ajustando o visibility timeout:
    a) Visibility timeout é o período em que uma mensagem recebida de uma fila não ficará visível para outros consumidores de mensagens. Tempo limite de visibilidade deve ser o tempo máximo pra processar  e deletar a mensagem.
        Conta de um visibility timeout: recebimento + processamento + exclusão
        -> tornar a aplicação resiliente

- Como economizar evitando requisições desnecessárias?
  - Short Polling: N requisições até receber uma nova mensagem (isso gera custos, o SQS $0.40 a cada 1 milhão de requisi)
  - Long Polling: Cria uma conexão até encontrar uma mensagem nova ou até atingir o tempo limite dessa conexão


     --wait-time-seconds (integer): The duration (in seconds) for which the call waits for a message to arrive in the queue before returning. If a message is available, the call returns sooner than WaitTimeSeconds . If no messages are available  and  the wait time expires, the call returns successfully with an empty list of messages.

        aws sqs receive-message --queue-url https://sqs.sa-east-1.amazonaws.com/488929950883/sqs-teste-lab --wait-time-seconds 20

        Obs: é recomendável sempre usar o long polling nas suas aplicações. Ao passar o wait time seconds de 20 dá pra economizar 19 chamadas, pensando que sem passar isso serão 20 chamadas, sendo 1 por segundo, logo, se não chegar mensagens a cada segundo, então haverá chamadas de API desnecessárias.

- Tratar erros e evitar o poison messages:
  O que o SQS deve fazer com mensagens que não foram processadas?
   
   - Dead Letter Queues (fila de mensagens mortas) - DLQ`s: destino de uma fila, caso a mensagem possa ser envenenada, possa ser uma mensagem que contenha veneno, aquela mensagem que não consegue ser processada. Ex.: uma aplicação espera receber JSON e recebe XML
   
   - O SQS mantém um controle de quantas vezes a mensagem chega na fila de origem, tem um contador que pode ser usado como parâmetro para mandar as mensagens para DQL. 
  
    A mensagem chegou na sqs de origem, a aplicação tentou consumir essa mensagem, é xml, não consegue, lança o erro, e o contador dessa mensagem era 1. A mensagem vai voltar para a fila, o sqs vai disponibilizar essa mensagem depois que o visibility timeout foi respeitado e a aplicação, toda inocente, sem saber o conteúdo da mensagem vai tentar consumir essa mensagem de novo, e vai ver que é xml de novo e não consegue processar.  aplicação vai lançar outro erro. A aplicação na maior boa vontade dela, inocentemente, mais uma vez busca a mesma mensagem do sqs, e dessa vez ela falha de novo, porque é uma mensagem xml que ela não sabe processar, mas o contador agora era 3, atinge o limite máximo e a mensagem é encaminhada para DLQ configurada.

  - Configurar DLQ: A criação de uma fila DLQ para cada fila regular. Após o número máximo de tentativas de processamento de uma mensagem ser esgotado, a mensagem deve ser direcionada para a DLQ. As filas DLQ devem possuir consumidores que alertam o time responsável quando novas mensagens forem detectadas.

    ![DLQ](Screen%20Shot%202022-03-13%20at%2020.44.30.png)

- Ordem de mensagens: 
    - Filas FIFO (First-in-first-out delivery): na sqs FIFO temos a garantia que essas mensagens vão ser entregues na ordem que elas foram publicadas e que não haverá duplicidade dos dados  (se a mensagem chegar dentro de 5 minutos) ->  quando a ordem das operações e dos eventos é essencial ou quando duplicatas não podem ser toleradas. O nome de uma fila FIFO deve terminar com o sufixo .fifo

- Enviar a mesma mensagem para diferentes filas:
  Uso de tópico para transmitir uma mensagem para várias filas. O tópico (SNS - Simple Notification Service) é responsável por notificar as filas que uma nova mensagem existe e não só notificar, mas também enviar mensagens. Quando uma mensagem chegar no SNS é possível inscrever vários endpoints, por exemplo, uma fila, SMS, endpoint http, outro. O SNS resolve o problema de paralelismo, ou seja, não vai ter uma fila tentando pegar mensagem de outra, é tudo independente e paralelizado. É usado em casos de replicar a mensagem.



