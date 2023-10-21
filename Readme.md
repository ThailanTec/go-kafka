# Apache Kafka

O apache Kafka é uma ***plataforma distibuída de streaming de eventos*** open-souce que é utilizada por milhares de empresas para uma alta performance de pipeline de dados, stream de analytics, integração de dados e aplicações de missão crítica. 

https://kafka.apache.org/

**************************************O mundo dos eventos**************************************

**C**ada dia mais precisamos processar mais e mais eventos em diversos tipos de plataforma. Desde sistemas que precisam se comunicar, devices para IOT, monitoramento de aplicações, sistemas de alarmes, etc.

Perguntas:

- Onde salvar esses eventos?
- Como recuperar de forma rápida e simples de forma que o feedback entre um processo e outro ou mesmo entre um sistema e outro possa acontecer de forma fluida e em tempo real?
- Como escalar?
- Como ter resiliência e alta disponibilidade?

******Kafka e seus super poderes******

- Altíssimo throughput
    - Ele consegue receber uma alta quantidade de requisições e processar tranquilamente.
- Latência extremamente baixa (2ms)
- Escalável
- Armazenamento
    - Ele tem uma área de banco de dados otimizado e segura para garantir a segurança dos dados guardados nele.
- Alta disponibilidade
- Se conecta com quase tudo
- Bibliotecas prontas para as mais diversas tecnologias (simples, mas precisa entender os processos)
- Ferramentas open-source

******************************************************Empresas que utilizam o Kafka******************************************************

- Linkedin
- Netflix
- Uber
- Twitter
- Dropbox
- Bancos…

****Dinâmica de funcionamento****

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/ea9dccf6-7ab8-443c-bff2-9d4deaf3ce72/fd005153-5368-49a5-a62b-e78ef0de13d5/Untitled.png)

O Kafka, pega os eventos, armazena e disponibiliza posteriormente para quem quiser ler os eventos. 

*Na representação da figura, temos os seguintes steps:* 

- Producer
    
    Quem está “criando” o evento.
    
- Consumer
    
    Quem está interessado em ter acesso aos dados do evento.
    

Ao invez deles falarem diretamente entre si, utilizamos o kafka pra gerar o trafego entre eles. 

O ***kafka*** ele acaba sendo um cluters, formado de “nós”, que chamamos de brokers. 

**Obs:** Cada broker, tem o seu propio banco de dados. 

Fluxo: 

*Producer → Kafka → Broker (que envia para o banco) ← Consumer (Acessa o broker e pega os dados do banco)*

O kafka não envia mensagens para ninguem, logo, ele não distruiu mensagem, ele armazena as mensagens para que um consumidor a busque. 

**Recomendação minima para colocar um kafka no ar, são de 3 brokers.**

Para a comunicação entre os brokers, o Kafka utiliza o ***zookeeper*** (serviço de service discovery). E ele auxilia o kafka, mas está de saida e o kafka está removendo ele da sua utilização. 

**************Tópicos (Topic no inglês)**************

Tópico é um canal de comunicação responsável por receber e disponibilizar os dados enviados para o kafka. 

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/ea9dccf6-7ab8-443c-bff2-9d4deaf3ce72/4dbdf144-0516-4c37-ae54-bd0263d7b8fd/Untitled.png)

Os tópicos podem ser lidos por quantos consumers, você desejar. 

**Tópicos,** funcionam mais ou menos como se fossem um log. 

**O que são log?** 

Local onde colocamos uma informação e vamos pegando uma atrás da outra. 

O kafka, permite que recuperamos as mensagens (topics), rebobina e traz de volta as mensagens. E ele armazena em disco essas mensagens. 

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/ea9dccf6-7ab8-443c-bff2-9d4deaf3ce72/42d877d9-b79d-407d-aff6-9caf51f93ee1/Untitled.png)

******Anatomia de um registro******

*São compostos por 4 partes principais*: 

1 - **Headers**

- Podemos trabalhar como se fossem metadados que podem ser util’s durante o processo.

2 - **Keys**

- Conta o contexto do tipo/agrupamento das mensagens

3 - **Value** 

- Payload, que vai conter o conteudo da mensagem em si.

4 - **Timestamp**

- Básicamente informações de horas.

******************Partições******************

Cada tópico pode ter uma ou mais partições para conseguir garantir a distribuição e resiliência de seus dados. 

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/ea9dccf6-7ab8-443c-bff2-9d4deaf3ce72/825f6149-b320-4bbb-b4f6-e12fda4a5880/Untitled.png)

O Kafka, cada vez que recebe uma nova mensagem, ele armazena ela em diferentes partições, pois assim, garante se caso um dos brokers esteja indisponivel ele ainda sim, consiga te entregar a mensagem. 

Quanto mais brokers, maior vai ser a divisão das dos tópicos, auxiliando na velocidade de entrega desses dados. 

Então, dessa forma, conseguimos escalar todo o kafka, pois conseguimos mandar mais maquinas lerem o mesmo topico, mas quando elas vão ler, leem de partições e brokers diferentes e fazer todo o processo de escala que o kafka faz. 

Obs: Quando vamos criar um tópico, precisamos falar quantas partições esse tópico vai ter. Assim, cada hora, as mensagens que ele receber vai ficar em uma partição diferente. 

**************************************************************Garantindo a ordem das entregas**************************************************************

Quando trabalhamos com as partições temos um problema, onde se caso um dos consumidores, esteja lento, acaba que ele pode ler uma mensagem que pode quebrar o seu fluxo de funcionamento. Ou então, caso um dos consumidores esteja muito rápido,e ele leia algo que ainda não foi processado pelo primeiro consumidor. 

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/ea9dccf6-7ab8-443c-bff2-9d4deaf3ce72/4e74e8e9-afb3-48fc-8239-a3616c582a35/Untitled.png)

Obs: As vezes temos mais partições, que consumidor, logo o consumidor vai consumir de mais de uma partição ao mesmo tempo. 

Como no exemplo da imagem, onde o consumir 1, realizou uma transferencia. Mas ocorreu um erro e houve um estorno, mas o segundo consumidor, já recebeu a mensagem de estorno antes da de transferencia, assim, causando uma quebra no fluxo da aplicação. 

Logo, chegamos a conclusão, que só conseguimos manter a ordem das mensagem com os dados na mesma partição. 

********************Sobre as “Keys”********************

Logo, podemos passar na key, que a sua chave é “movimentação”, por exemplo. Assim o Kafka vai entender e vai enviar para a mesma particação todas as mensagens que estão com as chaves iguais. Então, resolvemos o problema. 

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/ea9dccf6-7ab8-443c-bff2-9d4deaf3ce72/e9bbf28d-fdf6-4936-8dd9-0b05555c2c95/Untitled.png)

**Partições Distribuídas**

Quando criamos um tópico, podemos falar que ele vai ter um número ‘x’ de partições e replicator factor, de 2. Logo, dizemos que cada partição, vai ter 2 partições em cada broker, logo, se um broker cair eu tenho os dados seguros em outra partição com os mesmos dados daquele broker. 

*Replicator Factor* : **Fator Replicador (ele vai replicar as partições dentro dos brokers).**

Quanto maior o replication factor, mais espaço em disco precisa ter, vai consumir da sua maquina. Normalmente utilizamos de 2 a 3. No minimo 2 e 3 para dados de extrema importancia. 

****Partition leadership (Liderença das partições)****

Quando um consumidor vai ter um tópico, ele vai ser a partição lider daquele broker. Então, sempre que ele for ler um tópico, ele sempre vai ser da partição lider, independente se está em mais de um broker.

Então, quando um broker venha a cair ou ficar indisponivel, o kafka(normalmente atráves do zokeper), entende que o mesmo caiu e procura onde em outro broker tem o mesmo tópico e o transforma agora no tópico lider, deixando de ser follow (a partição reserva). 

### ****Producer: Garantia de entrega****

Temos atráves de “tags”, informa o que queremos fazer com as mensagens: 

**Ack 0 / none** =  receber a mensagem no Broker, ele vai armazenar, mas não vai te de dizer se o armazenamento foi realizado com sucesso ou não. Mas o ponto positivo, ele pode receber mais e mais mensagens. 

**Ack 1 / Leader** = recebe a mensagem no no Broker, ele vai armazenar e vai te retornar uma mensagem dizendo que ela foi salva com sucesso no Broker lider. Mas pode acontecer de retorna um “falso positivo”, onde ele vai te dizer que salvou a mensagem, mas pode acontecer do broker cair e a mensagem ainda não ter sido salva no follows. Logo, você perdeu essa mensagem e tempo, já que demora um pouco mais para processar a resposta. 

Ack -1 / ALL = recebe a mensagem no Broker, ele vai armazenar a mensagem e vai te retornar uma mensagem de sucesso após salvar no Broker leader e nós follows e aguarda a mensagem de sucesso de todos os brokers e follows. Você tem a garantia de que a mensagem está armazenada, mas tem uma grande perca de processamento e é muito lento, pois, demora para ele responder e receber novas mensagens. 

******************************Producer: Garantia de Entrega +******************************

**At most once:** Melhor perfomance. Pode perder algumas mensagens. 

**At least once:** Performance moderada. POde duplicar mensagens.

**Exacly once:** Pior performance. Exatamente uma vez.

### Producer: Indepotênte (Indepotência)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/ea9dccf6-7ab8-443c-bff2-9d4deaf3ce72/cb3e2b36-1e26-4700-9d81-307f8078b9b3/Untitled.png)

- Off
- Grava a mensagem 4 duplicada
- Consumer vai ler a mensagem 2x

Então, precisamos de um Indepontênte(ON), para que se caso a mensagem seja duplicada o kafka, consiga remover a mensagem e organizar a mensagem para que ela fique na ordem correta. Pode causa uma maior lentidão, mas garante a entrega perfeita das mensagens. 

### Consumer & Consumer Group

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/ea9dccf6-7ab8-443c-bff2-9d4deaf3ce72/b1e9d771-f979-4828-b5a2-b78557cbb444/Untitled.png)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/ea9dccf6-7ab8-443c-bff2-9d4deaf3ce72/9046de5e-9db4-4520-a4aa-dac4bc47e2cd/Untitled.png)

***Obs: Não tem como 2 consumidores que estão no mesmo grupo, lerem a mesma partição. podemos ter o mesmo consumer em grupos diferentes, eles podem ler, mas dentro do mesmo grupo, cada consumer ler apenas uma partição.***

O melhor dos mundos é ter a mesma quantidade de partição, para a mesma quantidade de consumidores. Se for menos, alguem vai sobrecarregar, se for mais, um deles pode ficar parado. E é bem comum de se utilizar. 

## Conceitos Básicos na prática

Link do repo: https://github.com/codeedu/fc2-kafka/blob/main/docker-compose.yaml 

### Criado o primeiro tópico

Após levantarmos o docker-compose do kafka: 

*docker compose up -d* (no local do arquivo)

Logo em seguida, vamos adentrar no docker em questão, dentro do mesmo, vamos conseguir começar a fazer as configurações e criar lá também os tópicos. 

docker exec -it ***container_name** bash* 

******************************************************Detalhes na criação de tópicos******************************************************

Sempre que formos criar algo no kafka ou vamos trabalhar na CLI, vamos precisar passar:

—bootstrap-server REQUIRED 

Básicamente é o servidor que vamos utilizar para trabalhar com o Kafka. 

Além do boostrap-server, vamos precisar passar mais algumas informações:

—create:  Vai ficar responsavel por informar que vamos criar um novo tópico. 

—partitions:  Vai ficar responsavel por informar quantas partições vamos trabalhar. 

—topic: Vai ficar responsavel por informar o nome do nosso tópico.  

Então, nosso comando de criação para um novo tópico fica: 

kafka-topics --create --topic=teste --bootstrap-server=localhost:9092 --partitions=3

***Obs: como estávamos trabalhando em um docker local, vamos passar a configuração local da máquina, então, devemos ficar atendo na hora da configuração desse comando.***

************Listando os tópicos:************

Para ver quais são os tópicos que já temos criados na nossa maquina, podemos utilizar o seguinte comando: 

kafka-topics —list —boostrap-server=localhost:9092 

***Obs: Sempre lembrar de passar o comando do boostrap-server, pois ele é um comando que sempre precisa ser passado.*** 

******************************Detalhes sobre os tópicos defaults******************************

__consumer_offsets: É onde o kafka guarda qual o offset determinado ***consumer***, está no momento da leitura dos tópicos. Assim, consegue continuar leitura, de onde ele parou. 

******************************************Detalhando nosso tópico******************************************

Para conseguirmos ter informações de como está as configurações do tópico, podemos utilizar o seguinte comando: 

kafka-topics --describe --topic=teste --bootstrap-server=localhost:9092

E assim, ele vai nos retornar as seguintes informações: 

Topic: teste	PartitionCount: 3	ReplicationFactor: 1	Configs:
Topic: teste	Partition: 0	Leader: 1	Replicas: 1	Isr: 1
Topic: teste	Partition: 1	Leader: 1	Replicas: 1	Isr: 1
Topic: teste	Partition: 2	Leader: 1	Replicas: 1	Isr: 1

Então, conseguimos entender como ele está funcionando. Para conseguir ligar os dados que já aprendemos, com a parte mais pratica do kafka. 

**Consumindo e produzindo mensagens**

Para conseguir receber mensagens, utilizamos a base de comando:

kafka-console-consumer

Ele, vai ser o responsavel por iniciar as configurações de leitura e envio das mensagens. 

**************************************************************Detalhes na criação do consumer**************************************************************

—topic: Utilizamos para informar qual o nome do tópico que o consumer vai utilizar.

—bootstrap-server: Vamos sempre utilizar esse cara, ele é DEFAULT. 

—partition: Utilizamos para informar se queremos que o mesmo utilize uma partição especifica

—group: Utilizamos para informar se vai ter ou não um consumer group, caso seja necessario. (IMPORTANTE)

—from-beginning: Utilizamos para que seja a feita a leitura do off-set =0 e assim, ele vai começar a ler a partir das primeiras mensagens as quais já foram enviadas para o kafka. 

******Configurando Producer****** 

Utilizamos o comando:

kafka-console-producer --bootstrap-server=localhost:9092 --topic=teste

A partir dele, conseguimos configurar enviar as mensagens, para que o nosso *********consumer consiga ler essas mensagens.********* 

Obs: Quando saimos da leitura (***consumer***), e depois voltamos, notamos que e ele “pulou”, algumas mensagens que nós foram enviadas enquanto, estavamos “fora”, do log.
Mas quando voltamos, notamos que ele volta a receber e ler as mensagens que são enviadas pelos producer. 

Para que ele nós reporte todas as mensagens que foram exibidas, vamos utilizar o comando, que já foi listado nos detalhes do custumer: —from-beginning, para que possamos ler **TODAS** as mensagens já enviadas a nosso consumer pelo producer. 

O comando fica, então: 

kafka-console-consumer --bootstrap-server=localhost:9092 --topic=teste --from-beginning

Assim, ele recupera todas as mensagens que já foram enviadas a partir do off-set 0. 

******************************Consumer Groups******************************

Nesse momento, cada um dos consumidores que temos, está consumindo a mensagem. Pois cada um é visto como um consumidor independente. Ex: suporte, financeiro e estão lendo normalmente. 

Relembrando que quando falamos que mais de um consumidor faz parte do mesmo grupo, cada consumidor vai ler de uma parte diferente.

Quando passamos então que o ***********consumer*********** é do mesmo grupo com flag: —group, acaba que apenas uma das partições lê a mensagem. Assim, cada um vai ler uma mensagem, não necessariamente lendo a mesma mensagem duas vezes. 

**************************************************************Por dentro de um consumer group**************************************************************

Quando passamos  comando:

kafka-consumer-groups --bootstrap-server=localhost:9092 --group=x --describe

Vamos ter  o seguinte retorno:

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/ea9dccf6-7ab8-443c-bff2-9d4deaf3ce72/29063a81-22bb-4e8d-9c97-eb118093d460/Untitled.png)

Assim, em cada uma das “tabelas”, vamos ter algumas informações importantes: 

GROUP: Qual  nome do grupo em questão.

TOPIC: Qual o tópico que ele está lendo. No caso do print, é  test. 

PARTITION: Informa qual é a partição

CURRENT-OFFSET: Qual o offset que ele está lendo no momento.

LOG-END-OFFSET: São todas as mensagem que tem. (Confirmar entendimento)

LAG: Informa se ele já conseguiu ele todas as mensagens nele presente. Logo, se ele tiver lido todas, o numero do mesmo ai ser 0.

CONSUMER-ID: O consumer que está conectado no momento. 

HOST: Mostra  host onde está a particioner

CLIENT-ID: ID do usuario

****Navegando pelo Confluent control center****

Acessando a rota:

localhost:9021

Vamos ter como visualizar as informações do nosso kafka de uma forma visual mais “fácil”.  
Assim, podemos navegar pelas abas e vê as configurações relacionadas ao nosso ****Clusters.****  

**Schema registry:** 

Ele é utilizado para que possamos setar um padrão de mensagem e quando enviar a mensagem, ela deve seguir o padrão da mensagem. 

**Connect:** 

Utilizado para que possamos integrar integrar sistemas, pegar info de um sistema  e jogar em outros sistemas por exemplo. 

Obs: ele possui o ksdb, que permite fazermos querys sql, para obter os dados de forma tranquila. 

## ************************************************Ambiente de Trabalho P/GO************************************************

Para que consigamos utilizar em Go e em outras linguagens o Kafka, temos de utilizar uma lib, na qual a mesma foi feita em C e faz a comunicação entre o sistema. A lib é:

https://github.com/confluentinc/librdkafka

Ela possui ferramentas que auxiliam outras linguagens a acessar o kafka. 

***Obs: Caso ela não esteja instalada, não vai funcionar o kafka, a lib é REQUIRED!***

 

Após a configuração dos dockers (só rodar: docker compose up -d), vamos ter um ambiente quase pronto. 

**Configurando o GO**

Sendo assim, vamos configurar o seguinte caminho: 

docker exec it *******************docker_image_name******************* e nela, vamos ter acesso as mesmas pastas do que está aparente no nosso terminal. Assim, vamos conseguir começar a trabalhar na nossa aplicação. 

Rodar um go mod init… E começar o desenvolvimento. 

************************************Criando e configurando o Producer************************************ 

**Docker**

- Vamos criar uma nova ou reutilizar um topic:
    - kafka-topics --create --bootstrap-server=localhost:9092 --topic=goteste --partitions=3
- Logo em seguida, vamos criar no docker também, um novo consumer, para receber as nossas mensagens:
    - kafka-console-consumer --bootstrap-server=localhost:9092 --topic=goteste

**GO**

- Criar uma função New… que vai retornar uma kafkaproducer da biblioteca defatul do kafka para go da confluent.
    - https://github.com/confluentinc/confluent-kafka-go
- Dentro da função, a parte principal vai ser o configMap, nele vai ficar as configurações dos parametros para criação do producer. A principal delas, sendo:
    
    *boostrap.server*
    
    ***Obs: No boostrap.server, vamos passar o nome o broker/docker que está ocorrendo, junto com  numero da porta. Como no código abaixo:***
    
    Ficando assim, nossa função:
    
    ```go
    func NewKafkaProducer() *kafka.Producer {
    	configMap := &kafka.ConfigMap{
    		"boostrap.server": "kafka-kafka-1:9092",
    	}
    	p, err := kafka.NewProducer(configMap)
    	if err != nil {
    		log.Println("error create a new producer", err.Error())
    	}
    	return p
    }
    ```
    
    ********Publicando a primeira mensagem********
    
    Para isso, vamos precisar de uma função que vai ficar responsavel por enviar essa mensagem.
    
    - Ela vai receber 3 dados: *msg, topic como strings, key como slice de bits e producer que vai ser do tipo kafka.Producer. E vai nos retornar um erro.*
    
    O conteudo da mensagem, vai ser o seguinte:
    
    - Value: Ele tem que ser no formato de array de bits.
    - TopicPartition: Vai receber as informações sobre o topico e sobre a partição, no exemplo de código, passamos um ******************kafka.PartitionAny******************, para que assim, informar que qualquer partição pode ler os dados.
    - Key: Que vai ser do tipo array de bytes, recebe os dados da nossa chave.
        
        ***Ficando assim o nosso código até então:***
        
        ```go
        func Publish(msg, topic string, producer *kafka.Producer, key []byte) error {
        	message = &kafka.Message{
        		Value:          []byte(msg),
        		TopicPartition: kafka.TopicPartition{Topic: &topic, Partition: kafka.PartitionAny},
        		Key:            key,
        	}
        
        }
        ```
        
    - Agora, vamos configurar a lógica para que ela faça a criação das mensagens, para isso vamos chamar o producer. Produce, para literalmente produzir a mensagem. Ele retorna um erro, mas dentro dos seus parametros ele recebe um uma msg do tipo *kafka.message e um deliveryChan, do tipo *chan,* do go.
    
    **Finalizando nossa função então:**
    
    ```go
    func Publish(msg, topic string, producer *kafka.Producer, key []byte) error {
    	message := &kafka.Message{
    		Value:          []byte(msg),
    		TopicPartition: kafka.TopicPartition{Topic: &topic, Partition: kafka.PartitionAny},
    		Key:            key,
    	}
    
    	err := producer.Produce(message, nil)
    	if err != nil {
    		return err
    	}
    	return nil
    }
    ```
    
    Para de fato enviar a mensagem, vamos fazer o seguinte, vamos chamar no main, nossa função e passar para ela as informações, ficando da seguinte maneira:
    
    ```go
    func main() {
    	producer := NewKafkaProducer()
    	Publish("mensagem", "goteste", producer, nil)
    	producer.Flush(1000)
    }
    ```
    
    ***Obs: passamos no final o producer.Flush, para que o Go, espere receber um sinal antes de fechar a lógica, para que assim dê tempo do sistema receber a mensagem. Passamos em milisegundos.*** 
    
    ****Delivery report****
    
    Vamos utilizar, para ter o retorno das informações um *deliveryChannel.*
    
    ********************************************************O que é um delivery channel?********************************************************
    
    *É um canal de comunicação entre a publicação e nossa mensagem ou uma rotina que executamos.* 
    
    ********************************Dados de forma Sincrona:********************************
    
    *OBS: Quando fazemos isso de forma sincrona, comprometemos a velocidade, pois precisamos esperar o retorno da mensagem para prosseguir com a aplicação.* 
    
    *De forma assincrona, temos um outro “cara”, para ficar aguardando  resultado dessas ações.*
    
- Agora, vamos implementar uma *channels,* que vai ficar responsavel por ficar lendo o retorno e nós trazer a informação de que a mensagem foi recebida com sucesso lá ou não.
    
    Vamos alterar no nossa nossa função de envio para que ao invez de nil, vamos receber agora deliveryChan que é do tipo *chan(channel)* mas do tipo kafka.Event. 
    
    Assim, a mesma vai nós retonar a mensagem que vem referente ao sucesso ou qualquer coisa que ocorreu durante o envio da mensagem.
    
    **Ficando então nosso código:**
    
    ```go
    func Publish(msg, topic string, producer *kafka.Producer, key []byte, deliveryChan chan kafka.Event) error {
    	message := &kafka.Message{
    		Value:          []byte(msg),
    		TopicPartition: kafka.TopicPartition{Topic: &topic, Partition: kafka.PartitionAny},
    		Key:            key,
    	}
    
    	err := producer.Produce(message, deliveryChan)
    	if err != nil {
    		return err
    	}
    	return nil
    }
    
    ```
    
- Agora na função *main*, vamos configurar o nosso recebimento o *channel*, junto com a leitura das informações.
    - Criamos então uma nova variavel para fazer a criação do canal do tipo que precisamos.
    - Criamos mais uma váriavel e informamos que ela vai receber as informações do *channel,* através do sinal ←, que no GO, indica que vai receber as mensagens para ser lida por esse canal.
    - Criamos para concluir uma várivel *msg,* para receber então as informações de ‘e’, nossa váriavel criada para receber as informações do *channel* criado.
    - E então, conseguimos validar as informações que vem lá de dentro, fazendo ainda uma validação dos erros que podem ocorrer.
        
        ******************************************************************Ficando assim então nosso código:******************************************************************
        
        ```go
        func main() {
        	deliveryChan := make(chan kafka.Event)
        	producer := NewKafkaProducer()
        	Publish("mensagem", "goteste", producer, nil, deliveryChan)
        
        	e := <-deliveryChan
        
        	msg := e.(*kafka.Message)
        	if msg.TopicPartition.Error != nil {
        		fmt.Println("Err to send message")
        	} else {
        		fmt.Println("send message sucessfull:", msg.TopicPartition)
        	}
        
        	producer.Flush(1000)
        }
        ```
        
    
    *OBS: Dessa forma, não é muito legal, pois só continua  execução do código quando chegar o resultado da entrega, causando lentidão na aplicação.*
    
    **************************Dados de forma Assincrona**************************
    
    Para trabalhar com ele assincrono, vamos criar uma nova função que vai ficar responsavel por ler de forma “infinita” as mensagens que vão chegando. Assim, não precisamos “esperar”, para continuar com o fluxo da aplicação. 
    
    Nossa função fica então,da seguinte forma:
    
    ```go
    func DeliveryReport(deliveryChan chan kafka.Event) {
    	for e := range deliveryChan {
    		switch ev := e.(type) {
    		case *kafka.Message:
    			if ev.TopicPartition.Error != nil {
    				fmt.Println("Err to send message")
    			} else {
    				fmt.Println("send message sucessfull:", ev.TopicPartition)
    			}
    		}
    	}
    }
    ```
    
    Mas dessa forma, temos um problema, pois a aplicação entra em um fluxo infinito, não parando “nunca”.
    
    Assim, vamos implementar uma *go routine,* assim vamos enviando aquela função para uma outra tread. 
    
    Então, na nossa função *main*, vamos passar o “sinal” da go routine, onde ela vai ficar responsavel por trabalhar em cima desse “erro”, mas também vamos precisar do *producer.Flush*, onde ele vai ficar responsavel por fazer nosso sistema esperar para receber as mensagens.
    
    Nossa função, então fica:
    
    ```go
    func main() {
    	deliveryChan := make(chan kafka.Event)
    	producer := NewKafkaProducer()
    	Publish("goroutine", "goteste", producer, nil, deliveryChan)
    	go DeliveryReport(deliveryChan)
    
    	producer.Flush(1000)
    }
    ```
    
    Agora, que temos um retorno de sucesso dessas mensagens, podemos fazer diversas operações, como:
    
    - Salvar no banco de dados
    - Ex: Confirmar que uma transferencia ocorreu. E se não ocorreu, fazer o retry, por exemplo.
    

****Parâmetros Importantes****

Na documentação, temos uma série de dados que podemos passar, lá podemos vê quais servem para producer ou consumer. *Obs: Se tiver um *, serve para ambos.*

***Link da doc:*** https://github.com/confluentinc/librdkafka/blob/master/CONFIGURATION.md

Importantes:

**delivery.timeout.ms** = O mesmo é utilizado apenas pelo P(producer), e server para informar qual o tempo de recebimento de mensagem. Quando o tempo acaba, recebemos uma mensagem falando que tal mensagem não foi entregue. Se setar o tempo com ‘0’, dizemos que é infinito.

**acks** = Quando informamos sobre o retorno (tem mais sobre isso na parte de cima da doc). Resumindo: 0 - Não preciso receber retorno, mais perfomartica 1 - aguardo apenas  leader informar que já persistiu a mensagem, ideal. All- o lider e todos os envolvidos retornem uma mensagem de sucesso, sendo o mais lento. 

enable.idempotence = O mesmo por padrão vem setado como false, assim podemos perder algumas mensagens, pular algumas mensagens, duplicar algumas mensagens, para ter certeza da ordem da chegada das mensagens e ter certeza da entrega, vamos coloca-la como true. 

auto.offset.reset = Para  mesmo pegar todo o historico de mensagens daquele tópico.

***OBS: Ela habilitada como true, o acks tem que ser ALL.*** 

Deixando então, nossa função de NewKafkaProducer na configuração do map, da seguinte forma: 

```
func NewKafkaProducer() *kafka.Producer {
	configMap := &kafka.ConfigMap{
		"bootstrap.servers":   "kafka-kafka-1:9092",
		"delivery.timeout.ms": "0",
		"acks":                "all",
		"enable.idempotence":  "true",
		"auto.offset.reset": "earliest",
	}
	p, err := kafka.NewProducer(configMap)
	if err != nil {
		log.Println("error create a new producer", err.Error())
	}
	return p
}
```

Essa configuração, vai mudar drasticamente a forma na qual vamos trabalhar com o kafka. 

****************************************Trabalhando com Keys****************************************

Lembrando que quando não trabalhamos com keys, não conseguimos manter a ordem de leitura dessas mensagens. Pois podemos ter diversos consumidores e cada uma pode está lendo uma partição diferente. 

Quando queremos que uma mensagem chegue sempre na mesma partição, precisamos então passar uma key. E ela vai garantir que todas as mensagens fiquem na mesma partição.

- Então, no nosso código, ao invez de enviar um nil, vamos enviar dentro do array de byte, nossa string. Que vai ser nossa chave, assim, todos os dados que vierem posteriormente vão cair apenas nessa partição.
    
    Ficando assim então, nosso código:
    
    ```go
    func main() {
    	deliveryChan := make(chan kafka.Event)
    	producer := NewKafkaProducer()
    	Publish("51dols", "goteste", producer, []byte("transfer2"), deliveryChan)
    	go DeliveryReport(deliveryChan)
    
    	producer.Flush(5000)
    }
    ```
    

## Consumindo as mensagens

Vamos criar agora um ********consumer******** que fica responsavel por “assinar” um tópico e ficar lá, lendo as mensagens desse tópico. 

- Vamos criar então o configMap, nele vamos passar as configurações do nosso consumer.
    - Detalhe importante para o campo: “group.id”, ele vai ser responsavel por informar a qual grupo pertence, sem ele, não conseguimos consumir nenhuma mensagem.
    - Podemos passar vários consumer com o mesmo [group.id](http://group.id) ou um consumer por group.id
    - OBS: SEMPRE ter a mesma quantidade de worker para quantidade de partições.
- Vamos então, passar uma variavel para receber os dados e uma para processar o erro e em seguida, vamos criar um array de tópicos, para informal qual tópico vamos ler do kafka.
    - Temos 2 opções: Subscribe - para ler apenas um tópico ou SubscribeTopics - para ler mais de um tópico. Normalmente usamos SubscribeTopics, pois ela aceita também, apenas um tópico.
- Outro ponto é rebalnceCb, ele é para caso haja a queda de um dos consumers, então o kafka faz e forma automatica.
- Para ler as mensagens vamos criar um loop, contendo a mensagem e o erro. Passando um time out, caso, desejamos que ele leia durante um tempo, passamos o tempo, mas se quisermos que ele fique de forma infinita, só passar o -1.
- Agora, diferente dos outros, vemos se o erro é igual a nil, vamos passar a mensagem. Passando ela primeiro para string, pois ele nós retorna um array de bytes e também pegamos as informações do tópico na qual está  mensagem.
Nosso código fica então:
    
    ```go
    func main() {
    	configMap := &kafka.ConfigMap{
    		"bootstrap.servers": "kafka-kafka-1:9092",
    		"client.id":         "goapp-consumer",
    		"group.id":          "goapp-group",
    	}
    	c, err := kafka.NewConsumer(configMap)
    	if err != nil {
    		log.Println("err create new consumer", err.Error())
    	}
    
    	topics := []string{"goteste"}
    	c.SubscribeTopics(topics, nil)
    
    	for {
    		msg, err := c.ReadMessage(-1)
    		if err == nil {
    			fmt.Println(string(msg.Value), msg.TopicPartition)
    		}
    	}
    }
    ```
    

**************************************************************Trabalhando com Consumer Groups**************************************************************

Quando nós apenas lemos as informações das partições, nosso unico worker, fica responsavel por ler cada uma delas, mas se caso, alteramos o campo “client.id”, para cada um do novo terminal que formos abrindo, cada um vai ficar responsavel por ler uma determinada partição. Lembrando que é no máximo 3, pois temos apenas 3 partição, se adicionarmos mais workers que partição, um deles não vai conseguir ler nada. 

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/ea9dccf6-7ab8-443c-bff2-9d4deaf3ce72/1f23852a-7ff6-4a18-8e1c-9d41b418bea0/Untitled.png)

Mas, se caso, tenhamos um consumer caido, o kafka, vai automaticamente colocar o que tinha sobrado e colocou um novo para ler os dados. Fazendo automaticamente um rebalance.

OBS: Tudo isso só está acontecendo pois estão no mesmo group.id. E se caso não tivessemos adicionado ele iria ler do zero e teriamos dois grupo lendo as mensagens agora.