# Tarefas assíncronas com Django e Celery

[asynchronous-tasks-with-django-and-celery](https://realpython.com/asynchronous-tasks-with-django-and-celery/)

Celery é uma fial de tarefas distrubuída que processa tarefas de forma 
assíncrona, evitando atrasos no tempo de resposta do seu aplicativo da 
web. Integrar o Celery com seu aplicativo Django permite que você 
descarregue tarefas demoradas, garantindo experiências de usuário suaves. 
Ao usar o Celery com Django, você pode gerenciar tarefas de forma 
eficiente, como enviar e-mails, processar imagens e analisar dados sem 
deixar seu aplicativo lento.

- Celery é uma **fila de tarefas distribuídas** que manipula tarefas fora 
do fluxo principal do aplicativo Django.
- O Celery do Python é excelente em **descarregar trabalho** e
**agendar tarefas** de forma independente.
- Usar o Celery no Djanfo ajuda
**a manter a capacidade de resposta do aplicativo** durante tarefas que 
exigem muito tempo.
- Configurar o Celery no Django envolve
**configurar um broker de mensagens** e **definir tarefas**.
- Executar uma tarefa no Celery requer chamar a tarefa com `.delay()` ou 
`apply_async()`.
- O Celery não é uma fila de mensagens, mas
**usa um corretor de mensagens como o Redis** para comunicação.

## Noções básicas sobre Python Celery

[Celery](https://docs.celeryq.dev/en/stable/index.html) de tarefas 
distribuídas que pode coletar, registrar, agendar e executar tarefas fora 
do seu programa principal.

> **Observação:** o Celery deixou de oferecer suporte ao Windows na versão 
4, então, embora você ainda consiga fazê-lo funcionar no Windows, é melhor 
usa uma fila de tarefas diferente, como
[huey](https://huey.readthedocs.io/en/latest/index.html) ou
[Dramatic](https://dramatiq.io/index.html).

Para receber tarefas do seu programa e enviar resultados para um back-end, 
o Celery requer um
[message broker](https://en.wikipedia.org/wiki/Message_broker) para 
comunicação. **Redis** e **RabbitMQ** são dois message brokers que os 
desenvolvedores costumam usar junto com o Celery.

Se você quiser acompanhar os resultados das execuções de suas tarefas, 
também precisará configurar um banco de dados **de resultados**.

> **Nota:** Conectar o Celery a um backend de resultados é opcional. 
Depois que você instruir o Celery a executar uma tarefa, ele fará seu 
trabalho, quer você acompanhe o resultado da tarefa ou não. No entanto, 
manter um registro de todos os resultados de tarefas geralmente é útil, 
especialmente se você estiver distribuindo tarefas para várias filas. Para 
persistir informações sobre os resultados de tarefas, você precisa de um 
back-end de banco de dados.

Você pode usar muitos bancos de dados diferentes para acompanhar os 
resultados de tarefas do Celery. Nesse projeto o
[Redis](https://realpython.com/python-redis/) será usado tanto como um 
broker de mensagens quanto como um back-end de resultados. Ao usar o 
Redis, você limita as dependências que precisa instalar, pois ele pode 
assumir ambas as funções.

## Por que usar Celery?

Há dois motivos principais pelos quais a maioria dos desenvolvedores 
deseja começar a usar o Celery:

1. **Descarregando o trabalho** do seu aplicativo para processos 
distribuídos que podem ser executados independemente do seu aplicativo.
2. **Agendar e execução de tarefas** em um horário específico, às vezes 
como eventos recorrents.

Celery é uma excelente escolha para ambos os casos de uso. Ele se define 
como "uma fila de tarefas como foco em processamento em tempo real, ao 
mesmo tempo em que oferece ao agendamento de tarefas"
([Fonte](https://docs.celeryq.dev/en/stable/index.html)).

Embora ambas as funcionalidades façam parte do Celery, elas geralmente são 
abordadas separadamente:

1. **Os workers do Celery** são processos de trabalho que executam 
tarefas independentemente uns dos outros e fora do contexto do seu serviço 
principal.
2. **Celery beat** é um agendador que orquestra quando executar tarefas. 
Você pode usá-lo para agendar tarefas periódicas também.

Os `workers` do Celery são a espinha dorsal do Celery. Mesmo que você 
queira agendar tarefas recorrentes usando o Celery beat, um `worker` do 
Celery pegará suas instruções e as tratará no horário agendado. O que o 
Celery beat adiciona à mistura é um agendador baseado em tempo para 
`workers` do Celery.

## Como você pode aproveitar o Celery para seu aplicativo Django?

O Celery não é útil apenas para aplicativos web, mas certamente é popular 
nesse contexto. Isso porque você pode lidar eficientemente com algumas 
situações cotidianas no desenvolvimento web usando uma fila de tarefas 
distribuídas como o Celery:

- **Envio de e-mail:** você pode querer enviar uma verificação de e-mail, 
um e-mail de redefinição de senha ou uma confirmação de envio de 
formulário. Enviar e-mails pode demorar um pouco e deixar seu aplicativo 
lento, especialmente se ele tiver muitos usuários.

- **Processamento de imagem:** você pode querer redimensionar imagens de 
avatar que os usuários carregam ou aplicar alguma codificação em todas as 
imagens que os usuários podem compartilhar na sua plataforma. O 
processamento de imagem geralmente é uma tarefa que exige muitos recursos 
e pode deixar seu aplicativo da web lento, principalmente se você tiver 
atendendo a uma grande comunidade de usuários.

- **Processamento de texto:** Se você permitir que os usuários adicionem 
dados ao seu aplicativo, talvez você queira monitorar a entrada deles. Por 
exemplo, você pode querer verificar se há palavrões nos comentários ou 
traduzir o texto enviado pelo usuário para um idioma diferente. Lidar com 
todo esse trabalho no contexto do seu aplicativo da web pode prejudicar 
significativamente o desempenho.

- **Chamadas de API e outras solicitações da web:** Se você precisa fazer 
solicitações da web para fornecer o serviço que seu aplicativo oferece, 
então você pode rapidamente se deparar com tempos de espera inesperados. 
Isso é verdade para solicitações de API com taxa limitada, assim como para 
outras tarefas, como
[web scraping](https://realpython.com/beautiful-soup-web-scraper-python/). 
Muitas vezes, é melhor entregar essas solicitações para um processo 
diferente.

- **Análise de dados:** A trituração de dados é notoriamente intensiva em 
recursos. Se seu aplicativo web analisa dados para seus usuários, você 
verá rapidamente seu aplicativo ficar sem resposta se estiver lidando com todo o trabalho diretamente no Django.

- **Execuções do modelo de machine learning:** Assim como em outras 
análises de dados, esperar pelos resultados das operações de machine 
learning pode levar um tempo. Em vez de deixar seus usuários esperarem a 
conclusão dos cálculos, você pode descarregar esse trabalho no Celery para 
que eles possam continuar navegando em seu aplicativo da web até que os 
resultados retornem.

- **Geração de relatórios:** Se você estiver servindo um aplicativo que 
permite que os usuários gerem relatórios a partir de dados fornecidos por 
eles, você notará que a construção de arquivos PDF não acontece instantaneamente. Será uma experiência melhor para o usuário se você 
deixar o Celery lidar com isso em segundo plano em vez de congelar seu 
aplicativo da web até que o relatório esteja pronto para download.

A configuração principal para todos esses diferentas casos de uso será 
semelhante. Assim qua você entender como entregar processos intensivos em 
computação ou tempo para uma fila de tarefas distribuída, você liberará o 
Django para lidar com o
[ciclo de solicitação-resposta HTTP](https://realpython.com/python-web-applications/#review-the-http-request-response-cycle).

---
