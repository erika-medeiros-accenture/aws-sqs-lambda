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
    aws sqs help: lista de comandos do sqs

    aws sqs list-queues: lista de queues criadas

    Ex: 
    
    {
    "QueueUrls": [
        "https://sqs.sa-east-1.amazonaws.com/488929950883/alura-test"
    ]
}



