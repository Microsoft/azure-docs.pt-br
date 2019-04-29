---
title: AMQP 1.0 no guia de protocolo do Barramento de Serviço e dos Hubs de Eventos do Azure | Microsoft Docs
description: Guia de protocolo para expressões e a descrição do AMQP 1.0 no Barramento de Serviço e nos Hubs de Eventos do Azure
services: service-bus-messaging,event-hubs
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: d2d3d540-8760-426a-ad10-d5128ce0ae24
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: c99f4491af8fe3e5f0f0ed7a264995ae3ec5911f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60749356"
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>AMQP 1.0 no guia de protocolo do Barramento de Serviço e dos Hubs de Eventos do Azure

O Advanced Message Queueing Protocol 1.0 é um protocolo de enquadramento e transferência padronizado para transferir mensagens de forma assíncrona, segura e confiável entre duas partes. É o principal protocolo de Mensagens do Barramento de Serviço e dos Hubs de Eventos do Azure. Ambos os serviços também oferecem suporte a HTTPS. O protocolo proprietário SBMP, que também é compatível, está sendo desativado em favor do AMQP.

O AMQP 1.0 é o resultado da colaboração de todo o setor, que reuniu fornecedores de middleware, como a Microsoft e a Red Hat, com muitos usuários de middleware de mensagens, como a JP Morgan Chase, representando o setor de serviços financeiros. O fórum de padronização técnica para as especificações de protocolo e de extensão do AMQP é o OASIS, e ele obteve aprovação formal como um padrão internacional como ISO/IEC 19494.

## <a name="goals"></a>Metas

Rapidamente, este artigo resume os principais conceitos da especificação de mensagens AMQP 1.0 juntamente com um pequeno conjunto de especificações de extensão de rascunho que atualmente está sendo finalizado no comitê técnico AMQP OASIS e explica como o Barramento de Serviço do Azure implementa e se baseia nessas especificações.

O objetivo é que qualquer desenvolvedor usando qualquer pilha de cliente existente do AMQP 1.0 em qualquer plataforma seja capaz de interagir com o Barramento de Serviço do Azure por meio do AMQP 1.0.

As pilhas comuns de uso geral do AMQP 1.0, como o Apache Proton ou o AMQP.NET Lite, já implementam todos os protocolos básicos do AMQP 1.0. Esses gestos básicos, às vezes, são encapsulados com um nível mais alto de API; o Apache Proton ainda oferece dois, a API do Messenger imperativa e a API Reactor reativa.

Na discussão a seguir, vamos pressupor que o gerenciamento de conexões, de sessões e de links do AMQP e a manipulação de transferências de quadro e controle de fluxo são manipulados pela respectiva pilha (como o Apache Proton-C) e não exigirão muito da atenção dos desenvolvedores do aplicativo. Vamos supor de forma abstrata a existência de algumas primitivas API, como a capacidade de conectar e de criar alguma forma de objetos de abstração *sender* e *receiver* que, por sua vez, têm alguma forma de operações `send()` e `receive()`, respectivamente.

Ao discutir os recursos avançados do Barramento de Serviço do Azure, como a procura de mensagens ou o gerenciamento de sessões, eles são explicados nos termos do AMQP, mas também como uma pseudoimplementação em camadas sobre essa abstração de API assumida.

## <a name="what-is-amqp"></a>O que é AMQP?

AMQP é um protocolo de enquadramento e transferência. O enquadramento significa que ele fornece a estrutura para fluxos de dados binários que fluem em qualquer direção de uma conexão de rede. A estrutura oferece delineação para que blocos de dados distintos, chamados de *quadros* sejam trocados entre as partes conectadas. Os recursos de transferência garantem que ambas as partes da comunicação possam estabelecer uma compreensão geral sobre quando os quadros deverão ser transferidos e quando as transferências deverão ser consideradas concluídas.

Ao contrário das versões de rascunho expiradas anteriores produzidas pelo grupo de trabalho de AMQP que ainda estão em uso por alguns agentes de mensagens, o protocolo AMQP 1.0 final e padronizado do grupo de trabalho não prescreve a presença de um agente de mensagem ou qualquer topologia específica para entidades dentro de um agente de mensagem.

O protocolo pode ser usado para comunicação ponto a ponto simétrica, para interação com os agentes de mensagens que oferecem suporte a filas e a entidades de publicação/assinatura, como faz o Barramento de Serviço do Azure. Ele pode também ser usado para interação com a infraestrutura de mensagens, onde os padrões de interação são diferentes das filas regulares, como é o caso com Hubs de Eventos do Azure. Um Hub de Eventos age como uma fila quando eventos são enviados para ele, mas atua mais como um serviço de armazenamento serial quando os eventos são lidos dele; ele é um pouco semelhante a uma unidade de fita. O cliente escolhe um deslocamento no fluxo de dados disponíveis e, em seguida, recebe todos os eventos do deslocamento para a versão mais recente disponível.

O protocolo AMQP 1.0 foi projetado para ser extensível, permitindo que outras especificações aperfeiçoem seus recursos. As especificações de três extensões discutidas neste documento ilustram isso. Para a comunicação na infraestrutura existente de HTTPS/WebSockets, a configuração das portas TCP AMQP nativas poderá ser difícil. Uma especificação de associação define como colocar AMQP sobre WebSockets. Para interagir com a infraestrutura de mensagens no formato solicitação/resposta para fins de gerenciamento ou para fornecer funcionalidade avançada, a especificação do gerenciamento AMQP define os primitivos de interação básicos necessários. Para a integração do modelo de autorização federado, a especificação de segurança com base em declarações AMQP define como associar e renovar tokens de autorização associados a links.

## <a name="basic-amqp-scenarios"></a>Cenários básicos de AMQP

Esta seção explica o uso básico do AMQP 1.0 com o Barramento de Serviço do Azure, que inclui a criação de conexões, sessões e links, bem como a transferência bidirecional de mensagens para entidades do Barramento de Serviço, como filas, tópicos e assinaturas.

A fonte mais confiável para saber mais sobre o funcionamento do AMQP é a especificação AMQP 1.0, mas a especificação foi escrita para orientar precisamente a implementação e não para ensinar o protocolo. Esta seção se concentra na introdução da terminologia necessária para descrever como o Barramento de Serviço usa o AMQP 1.0. Para obter uma introdução mais abrangente do AMQP, bem como uma discussão mais ampla do AMQP 1.0, examine [este curso em vídeo][this video course].

### <a name="connections-and-sessions"></a>Conexões e sessões

O AMQP chama os programas de comunicação de *contêiners*; eles contêm *nós*, que são as entidades de comunicação dentro desses contêineres. Uma fila pode ser um nó assim. O AMQP permite multiplexação, para que uma única conexão possa ser usada para vários caminhos de comunicação entre os nós. Por exemplo, um cliente de aplicativo pode receber de uma fila e enviar para outra fila na mesma conexão de rede simultaneamente.

![][1]

A conexão de rede, portanto, está ancorada no contêiner. Ele é iniciado pelo contêiner na função de cliente, fazendo uma conexão de soquete TCP de saída para um contêiner na função de destinatário que escuta e aceita conexões de TCP de entrada. O handshake da conexão inclui negociar a versão do protocolo, declarando ou negociando o uso de TLS/SSL (Transport Level Security) e um handshake de autenticação/autorização no escopo de conexão que se baseia em SASL.

O Barramento de Serviço do Azure requer o uso de TLS em todos os momentos. Ele oferece suporte a conexões pela porta TCP 5671, por meio da qual a conexão TCP é sobreposta com TLS primeiro antes de inserir o handshake do protocolo AMQP e também oferece suporte a conexões pela porta TCP 5672 por meio da qual o servidor imediatamente oferece uma atualização obrigatória de conexão ao TLS usando o modelo prescrito por AMQP. A associação de WebSockets AMQP cria um encapsulamento pela porta TCP 443 e, então, é equivalente às conexões 5671 AMQP.

Depois de configurar a conexão e o TLS, o Barramento de Serviço oferece duas opções de mecanismo SASL:

* O SASL SIMPLES normalmente é usado para passar credenciais de nome de usuário e de senha para um servidor. O Barramento de Serviço não tem contas, mas [regras de Segurança de Acesso Compartilhado](service-bus-sas.md) nomeadas, que conferem direitos e estão associadas com uma chave. O nome de uma regra é usado como o nome de usuário e a chave (como texto codificado em base64) é usado como a senha. Os direitos associados à regra escolhida controlam as operações permitidas na conexão.
* O SASL ANÔNIMO é usado para ignorar a autorização SASL quando o cliente quiser usar o modelo CBS (segurança com base em declarações), que é descrito posteriormente. Com essa opção, uma conexão de cliente pode ser estabelecida anonimamente por um curto período, durante o qual o cliente só poderá interagir com o ponto de extremidade CBS e o handshake CBS deverá ser concluído.

Depois de estabelecida a conexão de transporte, os contêineres declaram o tamanho máximo do quadro com que estão dispostos a lidar e, após um tempo limite de ociosidade, eles serão unilateralmente desconectados se não houver atividade na conexão.

Eles também declararam quantos canais simultâneos têm suporte. Um canal é um caminho de transferência virtual unidirecional de saída sobre a conexão. Uma sessão usa um canal de cada um dos contêineres interconectados para formar um caminho de comunicação bidirecional.

As sessões têm um modelo de controle de fluxo baseado na janela; quando uma sessão é criada, cada parte declara quantos quadros está disposto a aceitar em sua janela de recepção. À medida que as partes trocam quadros, os quadros transferidos preenchem a janela e as transferências param quando a janela fica cheia e até a janela ser redefinida ou expandida usando *performativo de fluxo* (*performativo* é o termo AMQP para gestos no nível de protocolo trocados entre as duas partes).

Esse modelo baseado em janela é quase análogo ao conceito TCP de controle de fluxo baseado em janela, mas no nível da sessão dentro do soquete. O conceito do protocolo de permitir várias sessões simultâneas existe para que o tráfego de alta prioridade possa ser acelerado em relação ao tráfego normal limitado, como se estivesse em uma pista expressa de rodovia.

Atualmente, o Barramento de Serviço do Azure usa exatamente uma sessão para cada conexão. O tamanho máximo de quadro do Barramento de Serviço é de 262.144 bytes (256 KB) para o Barramento de Serviço Standard e os Hubs de Eventos. Ele é de 1.048.576 (1 MB) para o Barramento de Serviço Premium. O Barramento de Serviço não impõe as janelas de limitação de nível de sessão específicas, mas redefine a janela regularmente como parte do controle de fluxo de nível de vinculação (veja [a próxima seção](#links)).

As conexões, os canais e as sessões são efêmeros. Se a conexão subjacente for recolhida,as conexões, o túnel TLS, o contexto de autorização SASL e as sessões deverão ser restabelecidas.

### <a name="links"></a>Links

O AMQP transfere mensagens sobre links. Um link é um caminho de comunicação criado em uma sessão que permite transferir mensagens em uma direção; a negociação de status de transferência é feita no link e é bidirecional entre as partes conectadas.

![][2]

Os links podem ser criados por um contêiner a qualquer momento e em uma sessão existente, o que torna AMQP diferente de muitos outros protocolos, incluindo HTTP e MQTT, em que a inicialização de transferências e o caminho de transferência são um privilégio exclusivo da parte ao criar a conexão de soquete.

O contêiner de link da inicialização solicita que o contêiner oposto aceite um link e escolha uma função de remetente ou de destinatário. Portanto, o contêiner pode iniciar a criação de caminhos de comunicação unidirecional ou bidirecional, com o último modelado como pares de links.

Os links são nomeados e associados a nós. Conforme mencionado no início, os nós são as entidades estão se comunicando dentro de um contêiner.

No Barramento de Serviço, um nó é diretamente equivalente a uma fila, um tópico, uma assinatura ou uma subfila de mensagens mortas de uma fila ou assinatura. O nome de nó usado no AMQP, portanto, é o nome relativo da entidade dentro do namespace do Barramento de Serviço. Se uma fila for chamada de `myqueue`, esse também será o nome do nó AMQP. Uma assinatura de tópico segue a convenção de API HTTP, sendo classificada em uma coleção de recursos de "assinaturas" e, assim, uma assinatura **sub** ou um tópico **mytopic** tem o nome do nó AMQP **mytopic/subscriptions/sub**.

O cliente de conexão também é necessário para usar um nome de nó local para criar links; o Barramento de Serviço não é prescritivo sobre esses nomes de nó e não os interpreta. As pilhas de cliente do AMQP 1.0 geralmente usam um esquema para garantir que esses nomes de nó efêmero sejam exclusivos no escopo do cliente.

### <a name="transfers"></a>Transferências

Após o estabelecimento de um link, as mensagens podem ser transferidas sobre esse link. No AMQP, uma transferência é executada com um gesto de protocolo explícito (a *transferência* performativa) que move uma mensagem do remetente ao destinatário sobre um link. Uma transferência é concluída quando é "liquidada", o que significa que as duas partes estabeleceram uma compreensão geral do resultado dessa transferência.

![][3]

No caso mais simples, o remetente pode optar por enviar mensagens "previamente liquidadas", o que significa que o cliente não está interessado no resultado e o receptor não fornece comentários sobre o resultado da operação. Esse modo é compatível com o Barramento de Serviço no nível do protocolo AMQP, mas não é exposto em nenhuma uma das APIs de cliente.

O caso comum é que as mensagens estão sendo enviadas não liquidadas e o receptor, então, indica a aceitação ou a rejeição usando a performativa de *disposição*. A rejeição ocorre quando o destinatário não pode aceitar a mensagem por algum motivo, e a mensagem de rejeição contém informações sobre o motivo, que é uma estrutura de erro definida pelo AMQP. Se as mensagens forem rejeitadas devido a erros internos dentro do Barramento de Serviço, o serviço retornará informações extras dentro dessa estrutura, que pode ser usada para fornecer dicas de diagnóstico à equipe de suporte se você estiver atendendo às solicitações de suporte. Posteriormente, você aprenderá mais detalhes sobre os erros.

Uma forma especial de rejeição é o estado *lançado*, que indica que o receptor não possui objeções técnicas à transferência, mas também não tem interesse em liquidar a transferência. Esse caso ocorre, por exemplo, quando uma mensagem é entregue a um cliente do Barramento de Serviço e o cliente opta por "abandonar" a mensagem, pois não será possível executar o trabalho resultante do processamento da mensagem; a entrega de mensagem em si não apresenta falha. Uma variação desse estado é o estado *modificado*, que permite alterações na mensagem quando liberada. Esse estado não é usado pelo Barramento de Serviço no momento.

A especificação do AMQP 1.0 define um estado de disposição adicional chamado *recebido*, que ajuda especificamente a lidar com a recuperação de link. O link de recuperação permite a reconstituição do estado de um link e de qualquer entrega pendente de uma nova conexão e sessão, quando a conexão e a sessão anteriores tiverem sido perdidas.

O Barramento de Serviço não dá suporte à recuperação de link; se o cliente perder a conexão com o Barramento de Serviço com uma transferência de mensagem não liquidada pendente, essa transferência de mensagem será perdida e o cliente deverá se reconectar, restabelecer o vínculo e repetir a transferência.

Dessa forma, o Barramento de Serviço e os Hubs de Eventos dão suporte à transferência "pelo menos uma vez", em que o remetente pode ter a certeza de que a mensagem foi armazenada e aceita, mas não dão suporte a transferências "exatamente uma vez" no nível do AMQP, em que o sistema tentaria recuperar o link e continuar a negociar o estado de entrega para evitar a duplicação da transferência da mensagem.

Para compensar possíveis envios de duplicidades, o Barramento de Serviço dá suporte à detecção de duplicidades como um recurso opcional em filas e tópicos. A detecção de duplicidades registra as IDs de mensagem de todas as mensagens de entrada durante uma janela de tempo definida pelo usuário e descarta silenciosamente todas as mensagens enviadas com as mesmas IDs de mensagem durante a mesma janela.

### <a name="flow-control"></a>Controle de fluxo

Além do modelo de controle de fluxo no nível de sessão discutido anteriormente, cada link tem seu próprio modelo de fluxo de controle. O controle de fluxo de nível de sessão protege o contêiner de ter que lidar com muitos quadros de uma vez, o controle de fluxo no nível de link torna o aplicativo responsável por quantas mensagens ele deseja tratar de um link e quando.

![][4]

Em um link, as transferências só podem acontecer quando o remetente tem *crédito de link* suficiente. O crédito de link é um contador definido pelo destinatário usando a performativa *fluxo*, que tem como escopo um link. Quando o remetente recebe o crédito de link, ele tenta usar esse crédito ao entregar mensagens. Cada entrega de mensagem decrementa o crédito de link restante em um. Quando o crédito de link acabar, as entregas serão interrompidas.

Quando o Barramento de Serviço está na função de receptor, ele instantaneamente fornece ao remetente crédito de link suficiente para que as mensagens possam ser enviadas imediatamente. À medida que o crédito de link é usado, o Barramento de Serviço às vezes envia um *fluxo* performativo ao remetente para atualizar o saldo do crédito de link.

Na função de remetente, o Barramento de Serviço envia mensagens para usar qualquer crédito de link pendente.

Uma chamada de "recebimento" no nível de API se traduz em *fluxo* performativo enviado para o Barramento de Serviço pelo cliente, e o Barramento de Serviço consome esse crédito retirando a primeira mensagem desbloqueada, disponível da fila, bloqueando-a e transferindo-a. Se não houver nenhuma mensagem prontamente disponível para entrega, qualquer crédito pendente por qualquer link estabelecido com essa determinada entidade permanecerá gravado em ordem de chegada e as mensagens serão bloqueadas e transferidas quando estiverem disponíveis para usar qualquer crédito pendente.

O bloqueio de uma mensagem é liberado quando a transferência é incorporada a um dos estados terminais*accepted*, *rejected* ou *released*. A mensagem será removida do Barramento de Serviço quando o estado terminal for *accepted*. Ela permanece no Barramento de Serviço e será entregue ao próximo receptor quando a transferência atingir qualquer um dos outros estados. O Barramento de Serviço move automaticamente a mensagem na fila de mensagens mortas da entidade quando atingir a contagem máxima de entregas permitida para a entidade devido a versões ou rejeições repetidas.

Embora as APIs do Barramento de Serviço não exponham diretamente tal opção hoje, um cliente do protocolo AMQP de nível inferior pode usar o modelo de crédito de link para ativar a interação do "estilo pull" de emissão de uma unidade de crédito para cada solicitação de recebimento em um modelo de "estilo push", emitindo um grande número de créditos de link e recebendo mensagens assim que estiverem disponíveis sem qualquer interação adicional. O push tem suporte por meio das configurações da propriedade [MessagingFactory.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) ou [MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) . Quando elas forem diferentes de zero, o cliente AMQP as usará como o crédito de link.

Nesse contexto, é importante entender que o relógio de expiração do bloqueio na mensagem dentro da entidade começa quando a mensagem é retirada da entidade, e não quando a mensagem é colocada na conexão. Sempre que o cliente indicar que está pronto para receber mensagens, emitindo um crédito de link, espera-se portanto que ele esteja efetuando ativamente pull de mensagens pela rede e esteja pronto para lidar com elas. Caso contrário, o bloqueio da mensagem pode ter expirado de a mensagem ter sido entregue. O uso do controle de fluxo de crédito de link deve refletir diretamente a preparação para lidar com mensagens disponíveis expedidas para o receptor.

Em resumo, as seções a seguir fornecem uma visão geral esquemática do fluxo performativo durante as interações de API diferentes. Cada seção descreve uma operação lógica diferente. Algumas dessas interações podem ser "lentas", o que significa que elas só podem ser executadas quando for necessário. A criação de um remetente da mensagem pode não causar uma interação na rede até que a primeira mensagem seja enviada ou solicitada.

As setas na tabela a seguir mostram a direção do fluxo performativo.

#### <a name="create-message-receiver"></a>Criar receptor da mensagem

| Cliente | Barramento de Serviço |
| --- | --- |
| --> attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**receiver**,<br/>source={entity name},<br/>target={client link ID}<br/>) |O cliente o anexa a entidade como receptor |
| o Barramento de Serviço responde ao anexar o final do link |<-- attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**sender**,<br/>source={entity name},<br/>target={client link ID}<br/>) |

#### <a name="create-message-sender"></a>Criar remetente da mensagem

| Cliente | Barramento de Serviço |
| --- | --- |
| --> attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**sender**,<br/>source={client link ID},<br/>target={entity name}<br/>) |Nenhuma ação |
| Nenhuma ação |<-- attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**receiver**,<br/>source={client link ID},<br/>target={entity name}<br/>) |

#### <a name="create-message-sender-error"></a>Criar remetente da mensagem (erro)

| Cliente | Barramento de Serviço |
| --- | --- |
| --> attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**sender**,<br/>source={client link ID},<br/>target={entity name}<br/>) |Nenhuma ação |
| Nenhuma ação |<-- attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**receiver**,<br/>source=null,<br/>target=null<br/>)<br/><br/><-- detach(<br/>handle={numeric handle},<br/>closed=**true**,<br/>error={error info}<br/>) |

#### <a name="close-message-receiversender"></a>Fechar receptor/remetente da mensagem

| Cliente | Barramento de Serviço |
| --- | --- |
| --> detach(<br/>handle={numeric handle},<br/>closed=**true**<br/>) |Nenhuma ação |
| Nenhuma ação |<-- detach(<br/>handle={numeric handle},<br/>closed=**true**<br/>) |

#### <a name="send-success"></a>Enviar (êxito)

| Cliente | Barramento de Serviço |
| --- | --- |
| --> transfer(<br/>delivery-id={numeric handle},<br/>delivery-tag={binary handle},<br/>settled=**false**,,more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |Nenhuma ação |
| Nenhuma ação |<-- disposition(<br/>role=receiver,<br/>first={delivery ID},<br/>last={delivery ID},<br/>settled=**true**,<br/>state=**accepted**<br/>) |

#### <a name="send-error"></a>Enviar (erro)

| Cliente | Barramento de Serviço |
| --- | --- |
| --> transfer(<br/>delivery-id={numeric handle},<br/>delivery-tag={binary handle},<br/>settled=**false**,,more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |Nenhuma ação |
| Nenhuma ação |<-- disposition(<br/>role=receiver,<br/>first={delivery ID},<br/>last={delivery ID},<br/>settled=**true**,<br/>state=**rejected**(<br/>error={error info}<br/>)<br/>) |

#### <a name="receive"></a>Receber

| Cliente | Barramento de Serviço |
| --- | --- |
| --> flow(<br/>link-credit=1<br/>) |Nenhuma ação |
| Nenhuma ação |< transfer(<br/>delivery-id={numeric handle},<br/>delivery-tag={binary handle},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| --> disposition(<br/>role=**receiver**,<br/>first={delivery ID},<br/>last={delivery ID},<br/>settled=**true**,<br/>state=**accepted**<br/>) |Nenhuma ação |

#### <a name="multi-message-receive"></a>Receber várias mensagens

| Cliente | Barramento de Serviço |
| --- | --- |
| --> flow(<br/>link-credit=3<br/>) |Nenhuma ação |
| Nenhuma ação |< transfer(<br/>delivery-id={numeric handle},<br/>delivery-tag={binary handle},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| Nenhuma ação |< transfer(<br/>delivery-id={numeric handle+1},<br/>delivery-tag={binary handle},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| Nenhuma ação |< transfer(<br/>delivery-id={numeric handle+2},<br/>delivery-tag={binary handle},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| --> disposition(<br/>role=receiver,<br/>first={delivery ID},<br/>last={delivery ID+2},<br/>settled=**true**,<br/>state=**accepted**<br/>) |Nenhuma ação |

### <a name="messages"></a>Mensagens

As seções a seguir explicam quais propriedades das seções padrão de mensagem AMQP são usadas pelo Barramento de Serviço e como elas são mapeadas para o conjunto de APIs do Barramento de Serviço.

Toda propriedade que o aplicativo precisa definir deve ser mapeada para o mapa de `application-properties` do AMQP.

#### <a name="header"></a>cabeçalho

| Nome do campo | Uso | Nome da API |
| --- | --- | --- |
| durável |- |- |
| prioridade |- |- |
| ttl |Vida útil desta mensagem |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| primeiro comprador |- |- |
| Contagem de entrega |- |[DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="properties"></a>propriedades

| Nome do campo | Uso | Nome da API |
| --- | --- | --- |
| message-id |Identificador de forma livre definido pelo aplicativo para esta mensagem. Usado para detecção de duplicidade. |[MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| user-id |Identificador de usuário definido pelo aplicativo, não interpretado pelo Barramento de Serviço. |Não é acessível por meio da API do Barramento de Serviço. |
| para |Identificador de destino definido pelo aplicativo, não é interpretado pelo Barramento de Serviço. |[Para](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| subject |Identificador de finalidade de mensagem definido pelo aplicativo, não é interpretado pelo Barramento de Serviço. |[Rótulo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| reply-to |Identificador reply-path definido pelo aplicativo, não é interpretado pelo Barramento de Serviço. |[ReplyTo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| correlation-id |Identificador de correlação definido pelo aplicativo, não é interpretado pelo Barramento de Serviço. |[CorrelationId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| content-type |Identificador content-type definido pelo aplicativo para o corpo, não é interpretado pelo Barramento de Serviço. |[ContentType](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| content-encoding |Identificador content-encoding definido pelo aplicativo para o corpo, não é interpretado pelo Barramento de Serviço. |Não é acessível por meio da API do Barramento de Serviço. |
| absolute-expiry-time |Declara em qual instante absoluto a mensagem expira. Ignorado na entrada (a vida útil do cabeçalho é observada), autoritativo na saída. |[ExpiresAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| creation-time |Declara a hora em que a mensagem foi criada. Não é usado pelo Barramento de Serviço |Não é acessível por meio da API do Barramento de Serviço. |
| group-id |Identificador definido pelo aplicativo para um conjunto relacionado de mensagens. Usado para sessões do Barramento de Serviço. |[SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| group-sequence |Contador que identifica o número de sequência relativa da mensagem em uma sessão. Ignorado pelo Barramento de Serviço. |Não é acessível por meio da API do Barramento de Serviço. |
| reply-to-group-id |- |[ReplyToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="message-annotations"></a>Anotações de mensagem

Há algumas outras propriedades de mensagem do barramento de serviço que não fazem parte das propriedades de mensagem do AMQP e são passadas como `MessageAnnotations` na mensagem.

| Chave do mapa de anotação | Uso | Nome da API |
| --- | --- | --- |
| x-opt-scheduled-enqueue-time | Declara a hora em que a mensagem deve aparecer na entidade |[ScheduledEnqueueTime](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc?view=azure-dotnet) |
| x-opt-partition-key | Chave definida pelo aplicativo que determina em qual partição a mensagem deve chegar. | [PartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey?view=azure-dotnet) |
| x-opt-via-partition-key | Valor de chave da partição definido pelo aplicativo quando uma transação deve ser usada para enviar mensagens por meio de uma fila de transferência. | [ViaPartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey?view=azure-dotnet) |
| x-opt-enqueued-time | Hora UTC definida pelo serviço que representa a hora real do enfileiramento da mensagem. Ignorado na entrada. | [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc?view=azure-dotnet) |
| x-opt-sequence-number | Número exclusivo definido pelo serviço atribuído a uma mensagem. | [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber?view=azure-dotnet) |
| x-opt-offset | Número de sequência da mensagem enfileirada definido pelo serviço. | [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber?view=azure-dotnet) |
| x-opt-locked-until | Definido pelo serviço. A data e hora até quando a mensagem ficará bloqueada na fila/assinatura. | [LockedUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc?view=azure-dotnet) |
| x-opt-deadletter-source | Definido pelo serviço. A origem da mensagem original se a mensagem for recebida da fila de mensagens mortas. | [DeadLetterSource](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource?view=azure-dotnet) |

### <a name="transaction-capability"></a>Recurso de transação

Uma transação agrupa duas ou mais operações em um escopo de execução. Por natureza, essa transação deve garantir que todas as operações que pertencem a determinado grupo de operações sejam concluídas com êxito ou com falha em conjunto.
As operações são agrupadas por um identificador `txn-id`.

Para a interação transacional, o cliente age como um `transaction controller` que controla as operações que devem ser agrupadas. O Serviço de Barramento de Serviço age como um `transactional resource` e executa o trabalho conforme solicitado pelo `transaction controller`.

O cliente e o serviço se comunicam por meio de um `control link` que é estabelecido pelo cliente. As mensagens `declare` e `discharge` são enviadas pelo controlador pelo link de controle para alocar e concluir as transações respectivamente (elas não representam a demarcação do trabalho transacional). O envio/recebimento real não é executado neste link. Cada operação transacional solicitada é identificada explicitamente com o `txn-id` desejado e, portanto, pode ocorrer em qualquer link da conexão. Se o link de controle for fechado enquanto houver transações não descarregadas criadas por ele, todas essas transações serão revertidas imediatamente, e a tentativa de realizar mais trabalho transacional nelas levará à falha. As mensagens no link de controle não podem ser pré-estabelecidas.

Cada conexão tem que iniciar seu próprio link de controle para poder iniciar e terminar transações. O serviço define um destino especial que funciona como um `coordinator`. O cliente/controlador estabelece um link de controle com esse destino. O link de controle fica fora do limite de uma entidade, ou seja, o mesmo link de controle pode ser usado para iniciar e descarregar transações de várias entidades.

#### <a name="starting-a-transaction"></a>Iniciando uma transação

Para iniciar o trabalho transacional. o controlador precisa obter um `txn-id` do coordenador. Ele faz isso enviando uma mensagem do tipo `declare`. Se a declaração for bem-sucedida, o coordenador responderá com um resultado de disposição que contém o `txn-id` atribuído.

| Cliente (controlador) | | Barramento de Serviço (coordenador) |
| --- | --- | --- |
| attach(<br/>name={link name},<br/>... ,<br/>role=**sender**,<br/>target=**Coordinator**<br/>) | ------> |  |
|  | <------ | attach(<br/>name={link name},<br/>... ,<br/>target=Coordinator()<br/>) |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (**Declare()**)}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=**Declared**(<br/>**txn-id**={transaction ID}<br/>))|

#### <a name="discharging-a-transaction"></a>Descarregando uma transação

O controlador conclui o trabalho transacional enviando uma mensagem `discharge` ao coordenador. O controlador indica que deseja confirmar ou reverter o trabalho transacional definindo o sinalizador `fail` no corpo de descarga. Se o coordenador não puder concluir a descarga, a mensagem será rejeitada com esse resultado contendo o `transaction-error`.

> Observação: fail=true refere-se à reversão de uma transação e fail=false refere-se à confirmação.

| Cliente (controlador) | | Barramento de Serviço (coordenador) |
| --- | --- | --- |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=Declared(<br/>txn-id={transaction ID}<br/>))|
| | . . . <br/>Trabalho transacional<br/>em outros links<br/> . . . |
| transfer(<br/>delivery-id=57, ...)<br/>{ AmqpValue (<br/>**Discharge(txn-id=0,<br/>fail=false)**)}| ------> |  |
| | <------ | disposition( <br/> first=57, last=57, <br/>state=**Accepted()**)|

#### <a name="sending-a-message-in-a-transaction"></a>Enviando uma mensagem em uma transação

Todo trabalho transacional é feito com o estado de entrega transacional `transactional-state` que contém o txn-id. No caso do envio de mensagens, o transactional-state está contido no quadro de transferência da mensagem. 

| Cliente (controlador) | | Barramento de Serviço (coordenador) |
| --- | --- | --- |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=Declared(<br/>txn-id={transaction ID}<br/>))|
| transfer(<br/>handle=1,<br/>delivery-id=1, <br/>**state=<br/>TransactionalState(<br/>txn-id=0)**)<br/>{ payload }| ------> |  |
| | <------ | disposition( <br/> first=1, last=1, <br/>state=**TransactionalState(<br/>txn-id=0,<br/>outcome=Accepted()**))|

#### <a name="disposing-a-message-in-a-transaction"></a>Descartando uma mensagem em uma transação

A disposição da mensagem inclui operações como `Complete` / `Abandon` / `DeadLetter` / `Defer`. Para executar essas operações em uma transação, passe o `transactional-state` com a disposição.

| Cliente (controlador) | | Barramento de Serviço (coordenador) |
| --- | --- | --- |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=Declared(<br/>txn-id={transaction ID}<br/>))|
| | <------ |transfer(<br/>handle=2,<br/>delivery-id=11, <br/>state=null)<br/>{ payload }|  
| disposition( <br/> first=11, last=11, <br/>state=**TransactionalState(<br/>txn-id=0,<br/>outcome=Accepted()**))| ------> |


## <a name="advanced-service-bus-capabilities"></a>Recursos avançados do Barramento de Serviço

Esta seção aborda os recursos avançados do Barramento de Serviço do Azure que se baseiam em extensões de rascunho para AMQP, atualmente sendo desenvolvidos no Comitê Técnico OASIS para AMQP. O Barramento de Serviço implementa as versões mais recentes desses rascunhos e adota as alterações introduzidas quando esses rascunhos atingirem o status padrão.

> [!NOTE]
> As operações avançadas de Mensagens do Barramento de Serviço têm suporte por meio de um padrão de solicitação/resposta. Os detalhes dessas operações são descritos no artigo [AMQP 1.0 no Barramento de Serviço: operações baseadas em solicitação/resposta](service-bus-amqp-request-response.md).
> 
> 

### <a name="amqp-management"></a>Gerenciamento de AMQP

A especificação de gerenciamento de AMQP é a primeira das extensões de rascunho abordadas nesse artigo. Essa especificação define um conjunto de protocolos em camadas sobre o protocolo AMQP que permite interações de gerenciamento com a infraestrutura de mensagens sobre AMQP. A especificação define operações genéricas como *criação*, *leitura*, *atualização* e *exclusão* para gerenciar as entidades dentro de uma infraestrutura de mensagens e um conjunto de operações de consulta.

Todos os gestos exigem uma interação de solicitação/resposta entre o cliente e a infraestrutura de mensagens e, portanto, a especificação define como modelar esse padrão de interação sobre AMQP: o cliente se conecta à infraestrutura de mensagens, inicia uma sessão e cria um par de links. Em um link, o cliente atua como remetente e no outro atua como receptor, criando assim um par de links que pode atuar como um canal bidirecional.

| Operação Lógica | Cliente | Barramento de Serviço |
| --- | --- | --- |
| Criar caminho de resposta de solicitação |--> attach(<br/>name={*link name*},<br/>handle={*numeric handle*},<br/>role=**sender**,<br/>source=**null**,<br/>target=”myentity/$management”<br/>) |Nenhuma ação |
| Criar caminho de resposta de solicitação |Nenhuma ação |\<-- attach(<br/>name={*link name*},<br/>handle={*numeric handle*},<br/>role=**receiver**,<br/>source=null,<br/>target=”myentity”<br/>) |
| Criar caminho de resposta de solicitação |--> attach(<br/>name={*link name*},<br/>handle={*numeric handle*},<br/>role=**receiver**,<br/>source=”myentity/$management”,<br/>target=”myclient$id”<br/>) | |
| Criar caminho de resposta de solicitação |Nenhuma ação |\<-- attach(<br/>name={*link name*},<br/>handle={*numeric handle*},<br/>role=**sender**,<br/>source=”myentity”,<br/>target=”myclient$id”<br/>) |

Com esse par de links definido, a implementação de solicitação/resposta é simples: uma solicitação é uma mensagem enviada a uma entidade dentro a infraestrutura de mensagens que compreende esse padrão. Nessa mensagem de solicitação, o campo *reply-to* na seção *properties* é definida como o identificador *target*para o link para o qual será fornecida a resposta. A entidade de tratamento processa a solicitação e então fornece a resposta pelo link cujo identificador *target* corresponda ao identificador *reply-to* indicado.

O padrão obviamente requer que o contêiner do cliente e o identificador gerado pelo cliente para o destino de resposta sejam exclusivos em todos os clientes e, por motivos de segurança, também sejam difíceis prever.

As trocas de mensagens usadas para o protocolo de gerenciamento e para todos os outros protocolos que usam o mesmo padrão ocorrem no nível do aplicativo; eles não definem novos gestos no nível do protocolo AMQP. Isso é intencional para que os aplicativos possam aproveitar imediatamente essas extensões com pilhas AMQP 1.0 compatíveis.

Atualmente, o Barramento de Serviço não implementa nenhum dos principais recursos da especificação de gerenciamento, mas o padrão de solicitação/resposta definido pela especificação de gerenciamento é a base do recurso de segurança baseado em declarações e de quase todos os recursos avançados discutidos nas seções a seguir:

### <a name="claims-based-authorization"></a>Autorização baseada em declarações

O rascunho da especificação CBS (Autorização com Base em Declarações) do AMQP se baseia no padrão de solicitação/resposta da especificação de gerenciamento e descreve um modelo generalizado de como usar tokens de segurança federados com AMQP.

O modelo de segurança padrão do AMQP discutido na introdução se baseia no SASL e se integra ao handshake de conexão AMQP. O uso de SASL tem a vantagem de que ele oferece um modelo extensível para o qual foi definido um conjunto de mecanismos e do qual qualquer protocolo que dependa formalmente de SASL pode se beneficiar. Entre esses mecanismos estão: "PLAIN", para a transferência de nomes de usuário e senhas, "EXTERNAL", para associar a segurança no nível de TLS, "ANONYMOUS", para expressar a ausência de autenticação/autorização explícita, além de uma ampla variedade de mecanismos adicionais que permitem a passagem de credenciais de autenticação e/ou autorização, ou de tokens.

A integração do SASL do AMQP tem duas desvantagens:

* Todas as credenciais e os tokens têm como escopo a conexão. Um infraestrutura de mensagens pode querer fornecer controle de acesso diferenciado por entidade; por exemplo, permitindo que o portador de um token faça um envio para a fila A, mas não para a fila B. Com o contexto de autorização ancorado na conexão, não é possível usar uma única conexão e ainda usar tokens de acesso diferentes para a fila A e a fila B.
* Os tokens de acesso geralmente só são válidos por um período limitado. Essa validade exige que o usuário readquira tokens periodicamente, além de oferecer uma oportunidade para o emissor do token de se recusar a emitir um token novo, caso as permissões de acesso do usuário tenham sido alteradas. As conexões AMQP podem durar por longos períodos. O modelo SASL apenas fornece uma oportunidade para definir um token no momento da conexão, o que significa que a infraestrutura de mensagens precisa desconectar o cliente quando o token expira ou precisa aceitar o risco de permitir a comunicação contínua com um cliente cujos direitos de acesso possam ter sido revogados nesse ínterim.

A especificação CBS do AMQP, implementada pelo Barramento de Serviço permite que uma solução elegante para ambos os problemas: Ele permite que um cliente associe tokens de acesso a cada nó e atualize esses tokens antes de expirarem, sem interromper o fluxo de mensagens.

O CBS define um nó de gerenciamento virtual, chamado *$cbs*, a ser fornecido pela infraestrutura de mensagens. O nó de gerenciamento aceita tokens em nome de qualquer outro nó na infraestrutura de mensagens.

O gesto de protocolo é uma troca de solicitação/resposta, conforme definido pela especificação de gerenciamento. Isso significa que o cliente estabelece um par de links com o nó *$cbs* e, em seguida, passa uma solicitação no link de saída e aguarda a resposta no link de entrada.

A mensagem de solicitação tem as seguintes propriedades de aplicativo:

| Chave | Opcional | Tipo de valor | Conteúdo de valor |
| --- | --- | --- | --- |
| operation |Não  |string |**put-token** |
| Tipo |Não  |string |O tipo do token colocado. |
| Nome |Não  |string |O "público" ao qual o token se aplica. |
| expiração |Sim | timestamp |A hora de expiração do token. |

A propriedade *name* identifica a entidade à qual o token deve ser associado. No Barramento de Serviço, é o caminho para a fila ou tópico/assinatura. A propriedade *type* identifica o tipo de token:

| Tipo de token | Descrição do token | Tipo de corpo | Observações |
| --- | --- | --- | --- |
| amqp:jwt |JWT (Token Web JSON) |Valor de AMQP (cadeia de caracteres) |Ainda não está disponível. |
| amqp:swt |SWT (Token Web Simples) |Valor de AMQP (cadeia de caracteres) |Só tem suporte para tokens SWT emitidos pelo AAD/ACS |
| servicebus.windows.net:sastoken |Token SAS do barramento de serviço |Valor de AMQP (cadeia de caracteres) |- |

Os tokens conferem direitos. O Barramento de Serviço do Microsoft Azure conhece três direitos fundamentais: “Enviar", permite o envio, "Ouvir", permite o recebimento, e "Gerenciar", permite a manipulação de entidades. Os tokens SWT emitidos pelo ACS/AAD incluem explicitamente esses direitos como declarações. Os tokens SAS do Barramento de Serviço consultam regras configuradas no namespace ou na entidade, e essas regras são configuradas com direitos. Assinar o token com a chave associada a essa regra, portanto, faz com que o token expresse os respectivos direitos. O token associado a uma entidade que usa *put-token* permite que o cliente conectado interaja com a entidade de acordo com os direitos do token. Um link em que o cliente assume a função de *remetente* requer o direito "Envio", e assumir a função de *receptor* requer o direito "Ouvir".

A mensagem de resposta tem os seguintes valores de *application-properties*

| Chave | Opcional | Tipo de valor | Conteúdo de valor |
| --- | --- | --- | --- |
| status-code |Não  |int |Código de resposta HTTP **[RFC2616]**. |
| status-description |Sim |string |A descrição do status. |

O cliente pode chamar *put-token* repetidamente e para qualquer entidade na infraestrutura de mensagens. Os tokens estão no escopo do cliente atual e ancorados na conexão atual, o que significa que o servidor cancela todos os tokens retidos quando a conexão cair.

A implementação atual do Barramento de Serviço permite apenas CBS em conjunto com o método SASL "ANÔNIMO". Uma conexão SSL/TLS sempre deve existir antes do handshake SASL.

O mecanismo ANÔNIMO, portanto, deve ser suportado pelo cliente AMQP 1.0 escolhido. O acesso anônimo significa que o handshake de conexão inicial, incluindo a criação da sessão inicial, acontece sem que o Barramento de Serviço saiba quem está criando a conexão.

Uma vez estabelecida a conexão e a sessão, anexar os links ao nó *$cbs* e enviar a solicitação *put-token* são as únicas operações permitidas. Um token válido deve ser definido com êxito usando uma solicitação *put-token* para algum nó de entidade em até 20 segundos depois que a conexão tiver sido estabelecida, caso contrário, a conexão será cancelada unilateralmente pelo Barramento de Serviço.

O cliente é subsequentemente responsável por manter o controle de expiração do token. Quando um token expira, o Barramento de Serviço descarta imediatamente todos os links da conexão com a respectiva entidade. Para evitar que o problema ocorra, o cliente pode substituir o token do nó por um novo a qualquer momento através do nó de gerenciamento virtual *$cbs* com o mesmo *gesto token* e sem ficando no caminho do tráfego de carga que flui em links diferentes.

### <a name="send-via-functionality"></a>Funcionalidade Enviar via

[Enviar via/Remetente de transferência](service-bus-transactions.md#transfers-and-send-via) é uma funcionalidade que permite que o barramento de serviço encaminhe uma determinada mensagem a uma entidade de destino por meio de outra entidade. Esse recurso é usado principalmente para executar operações em entidades em uma única transação.

Com essa funcionalidade, você pode criar um remetente e estabelecer o link com a `via-entity`. Ao estabelecer o link, informações adicionais são passadas para estabelecer o destino real das mensagens/transferências nesse link. Com a conexão bem-sucedida, todas as mensagens enviadas nesse link são encaminhadas automaticamente para a *destination-entity* por meio da *via-entity*. 

> Observação: A autenticação deve ser executada para *via-entity* e para *destination-entity* antes do estabelecimento desse link.

| Cliente | | Barramento de Serviço |
| --- | --- | --- |
| attach(<br/>name={link name},<br/>role=sender,<br/>source={client link ID},<br/>target=**{via-entity}**,<br/>**properties=map [(<br/>com.microsoft:transfer-destination-address=<br/>{destination-entity} )]** ) | ------> | |
| | <------ | attach(<br/>name={link name},<br/>role=receiver,<br/>source={client link ID},<br/>target={via-entity},<br/>properties=map [(<br/>com.microsoft:transfer-destination-address=<br/>{destination-entity} )] ) |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o AMQP, confira os seguintes links:

* [Visão geral do Barramento de Serviço para AMQP]
* [Suporte a AMQP 1.0 para filas e tópicos particionados do Barramento de Serviço]
* [AMQP no Barramento de Serviço para Windows Server]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Visão geral do Barramento de Serviço para AMQP]: service-bus-amqp-overview.md
[Suporte a AMQP 1.0 para filas e tópicos particionados do Barramento de Serviço]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP no Barramento de Serviço para Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
