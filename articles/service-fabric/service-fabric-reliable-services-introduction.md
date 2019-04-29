---
title: Visão geral do modelo de programação de Reliable Service do Service Fabric | Microsoft Docs
description: Saiba mais sobre o modelo de programação dos Serviços Confiáveis da Malha de Serviços e comece a desenvolver seus próprios serviços.
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: vturecek; mani-ramaswamy
ms.assetid: 0c88a533-73f8-4ae1-a939-67d17456ac06
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: masnider
ms.openlocfilehash: 1789c7489e58df09dccfde3e7ab106ef54b5c1ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60727005"
---
# <a name="reliable-services-overview"></a>Visão geral dos Reliable Services
O Azure Service Fabric simplifica o desenvolvimento e o gerenciamento de Reliable Services com e sem estado. Este tópico aborda:

* O modelo de programação do Reliable Services para serviços com e sem estado.
* As decisões que você precisa tomar ao desenvolver um Reliable Service.
* Alguns cenários e exemplos de quando você usaria Reliable Services e como eles são desenvolvidos.

Os Serviços Confiáveis são um dos modelos de programação disponíveis na Malha de Serviços. O outro é o modelo de programação Reliable Actor, que fornece um modelo de programação Actor virtual sobre o modelo do Reliable Services. Para saber mais sobre o modelo de programação de Reliable Actors, confira [Introdução aos Reliable Actors do Service Fabric](service-fabric-reliable-actors-introduction.md).

O Service Fabric gerencia a vida útil dos serviços, desde o provisionamento e da implantação até a atualização e a exclusão, por meio do [gerenciamento de aplicativos do Service Fabric](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>O que são os Reliable Services?
Os Reliable Services fornecem um modelo de programação simples, potente e de nível superior que ajuda você a demonstrar o que é importante para seu aplicativo. Com o modelo de programação dos Reliable Services, você obtém:

* Acesso ao restante das APIs de programação do Service Fabric. Ao contrário dos serviços do Service Fabric modelados como [Executáveis de Convidado](service-fabric-guest-executables-introduction.md), os Reliable Services usam o restante das APIs do Service Fabric diretamente. Isso permite que os serviços:
  * consultar o sistema
  * relatar integridade de entidades no cluster
  * receber notificações sobre alterações de configuração e código
  * localizar e comunicar-se com outros serviços,
  * (opcionalmente) usar o [Reliable Collections](service-fabric-reliable-services-reliable-collections.md)
  * …e concedendo-lhes acesso aos vários outros recursos, tudo de um modelo de programação de primeira classe em várias linguagens de programação.
* Um modelo simples para execução de seu próprio código que parece com os modelos de programação com os quais você está acostumado. Seu código tem um ponto de entrada bem definido e um ciclo de vida fácil de gerenciar.
* Um modelo de comunicação conectável. Use o transporte de sua escolha, como HTTP com a [API Web](service-fabric-reliable-services-communication-webapi.md), WebSockets, protocolos TCP personalizados ou qualquer outro. O Reliable Services oferece excelentes opções de utilização ou permite que você forneça suas próprias.
* Para serviços com estado, o modelo de programação dos Reliable Services permite que você armazene de forma consistente e confiável o seu estado diretamente de seu serviço usando o [Reliable Collections](service-fabric-reliable-services-reliable-collections.md). O Reliable Collections é um conjunto simples de classes de coleção confiável altamente disponíveis que serão familiares para todos aqueles que já usaram coleções C#. Tradicionalmente, os serviços precisavam de sistemas externos para um gerenciamento de estado confiável. Com os Reliable Collections, você pode armazenar o estado próximo à computação com a mesma alta disponibilidade e confiabilidade esperada de repositórios externos altamente disponíveis. Esse modelo também melhora a latência, pois você está localizando a computação e o estado de funcionamento conjuntamente.

## <a name="what-makes-reliable-services-different"></a>O que torna o Reliable Services diferente?
Os Serviços Confiáveis na Malha de Serviços são diferentes dos serviços que você já desenvolveu anteriormente. A Malha de Serviços fornece confiabilidade, disponibilidade, consistência e escalabilidade.

* **Confiabilidade** – Seu serviço permanece o mesmo em ambientes não confiáveis em que suas máquinas falham, apresentam problemas de rede ou em casos em que os próprios serviços encontram erros ou falham. Para serviços com estado, este é preservado mesmo na presença de falhas de rede ou outras falhas.
* **Disponibilidade** – Seu serviço estará acessível e responderá rapidamente. O Service Fabric mantém o número desejado de cópias de execução.
* **Escalabilidade** – Os serviços são separados do hardware específico e podem ser aumentados ou reduzidos conforme necessário por meio da adição ou da remoção de hardware ou outros recursos. Os serviços são particionados com facilidade (especialmente no caso dos serviços com estado) para garantir que ele possa escalar e responder a falhas parciais. Os serviços podem ser criados e excluídos dinamicamente por meio de código, permitindo a criação de mais instâncias do que o necessário, como em resposta às solicitações do cliente, por exemplo. Por fim, o Service Fabric incentiva os serviços a serem leves. O Service Fabric permite que milhares de serviços sejam provisionados em um único processo, em vez de exigir ou dedicar instâncias ou processos inteiros do sistema operacional para uma única instância de um serviço.
* **Consistência** – É possível garantir a consistência de todas as informações armazenadas nesse serviço. Isso é verdadeiro mesmo entre várias coleções confiáveis dentro de um serviço. Alterações nas coleções dentro de um serviço podem ser feitas de forma transacional atômica.

## <a name="service-lifecycle"></a>Ciclo de vida do serviço
Independentemente de seu serviço ser com ou sem estado, o Reliable Services fornece um ciclo de vida simples que permite conectar rapidamente seu código e começar.  Há apenas um ou dois métodos que você precisa implementar para colocar o serviço em funcionamento.

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** – Esse método é onde o serviço define as pilhas de comunicações que deseja usar. A pilha de comunicações, tal como a [API Web](service-fabric-reliable-services-communication-webapi.md), é o que define o ponto de extremidade de escuta ou os pontos de extremidade do serviço (como os clientes o alcançarão). Ele também define como as mensagens que aparecem interagem com o restante do código do serviço.
* **RunAsync** – Esse método é no qual o seu serviço executa sua lógica de negócios e no qual ele seria iniciaria as tarefas em segundo plano que devem ser executado durante o tempo de vida do serviço. O token de cancelamento fornecido é um sinal de quando esse trabalho deve parar. Por exemplo, se o serviço precisar extrair mensagens de um Reliable Queue e processá-las, é ali esse trabalho ocorrerá.

Se você estiver aprendendo sobre Reliable Services pela primeira vez, continue lendo! Se estiver procurando um passo a passo detalhado sobre o ciclo de vida dos Reliable Services, visite [este artigo](service-fabric-reliable-services-lifecycle.md).

## <a name="example-services"></a>Exemplo de serviços
Conhecendo este modelo de programação, vamos dar uma olhada em dois serviços diferentes para ver como essas peças se encaixam.

### <a name="stateless-reliable-services"></a>Serviços Confiáveis sem estado
Um serviço sem estado é aquele em que não há nenhum estado mantido dentro do serviço entre as chamadas. Qualquer estado presente é totalmente descartável e não requer sincronização, replicação, persistência ou alta disponibilidade.

Por exemplo, considere uma calculadora que não tenha memória e que receba todos os termos e operações para serem executados ao mesmo tempo.

Nesse caso, o `RunAsync()` (C#) ou `runAsync()` (Java) do serviço pode estar vazio, pois não há processamentos de tarefas em segundo plano que o serviço precisa executar. Quando o serviço da calculadora for criado, ele retornará um `ICommunicationListener` (C#) ou `CommunicationListener` (Java) (por exemplo, [API Web](service-fabric-reliable-services-communication-webapi.md)) que abre um ponto de extremidade de escuta em alguma porta. Esse ponto de extremidade de escuta se conectará aos diferentes métodos de cálculo (por exemplo: "Add (n1, n2)") que definem a API pública da calculadora.

Quando é feita uma chamada de um cliente, o método apropriado é invocado e o serviço da calculadora executa as operações nos dados fornecidos e retorna o resultado. O estado não é armazenado.

Não armazenar nenhum estado interno torna este exemplo de calculadora muito simples. Mas a maioria dos serviços não é realmente sem estado. Em vez disso, eles externalizam o estado para algum outro repositório. (Por exemplo, qualquer aplicativo Web que depende da manutenção do estado de sessão em um repositório de backup ou do cache não é sem estado.)

Um exemplo comum de como serviços sem estado são usados na Malha de Serviços é um front-end que expõe a API voltada ao público para um aplicativo Web. O serviço front-end se comunica com serviços com estado para concluir a solicitação de um usuário. Nesse caso, chamadas de clientes são direcionadas para uma porta conhecida, por exemplo, a 80, na qual o serviço sem estado está escutando. Esse serviço sem estado recebe a chamada e determina se ela é de uma parte confiável, bem como o serviço ao qual ela se destina.  Em seguida, o serviço sem estado encaminha a chamada para a partição correta do serviço com estado e aguarda uma resposta. Quando o serviço sem estado recebe uma resposta, ele responde ao cliente original. Veja um exemplo de um serviço como esse em nossas amostras de [C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) / [Java](https://github.com/Azure-Samples/service-fabric-java-getting-started). Esse é apenas um exemplo desse padrão nos exemplos, também existem outros nos demais exemplos.

### <a name="stateful-reliable-services"></a>Serviços Confiáveis com estado
Um serviço com estado deve ter alguma parte do estado consistente e presente para que o serviço funcione. Considere um serviço que calcula constantemente uma média móvel de algum valor com base em atualizações que recebe. Para fazer isso, é necessário ter o conjunto atual de solicitações de entrada que precisam ser processados e a média atual. Qualquer serviço que recupera, processa e armazena informações em um repositório externo (como um repositório de tabelas ou blob do Azure) é um serviço com estado. Ele simplesmente mantém seu estado no repositório de estado externo.

A maioria dos serviços atuais armazena seu estado externamente, pois o repositório externo é o que fornece confiabilidade, disponibilidade, escalabilidade e consistência para esse estado. No Service Fabric, os serviços não são necessários para armazenar seu estado externamente. O Service Fabric cuida desses requisitos para o código e o estado do serviço.

Digamos que desejamos criar um serviço que processa imagens. Para fazer isso, o serviço obtém uma imagem e a série de conversões a serem executadas na imagem. Este serviço retorna um ouvinte de comunicação (suponhamos que é uma WebAPI) que expõe uma API como `ConvertImage(Image i, IList<Conversion> conversions)`. Quando ele recebe uma solicitação, o serviço a armazena em um `IReliableQueue` e retorna uma ID para o cliente para que ele possa rastrear a solicitação.

Nesse serviço, `RunAsync()` poderia ser mais complexo. O serviço tem um loop dentro de seu `RunAsync()` que recebe solicitações de `IReliableQueue` e executa as conversões solicitadas. Os resultados são armazenados em um `IReliableDictionary` para que, quando o cliente voltar, ele possa obter suas imagens convertidas. Para garantir que a imagem não seja perdida mesmo se algo der errado, esse Reliable Service sairia da fila, executaria as conversões e armazenaria o resultado em uma transação. Nesse caso, a mensagem é removida da fila e os resultados são armazenados no dicionário de resultados após a conclusão das conversões. Como alternativa, o serviço poderia extrair a imagem da fila e armazená-la imediatamente em um repositório remoto. Isso reduz a quantidade de estado que do serviço precisa gerenciar, porém aumenta a complexidade, visto que o serviço deve manter os metadados necessários para gerenciar o repositório remoto. Com essa abordagem, se algo falhar no meio a solicitação, esta permanecerá na fila aguardando para ser processada.

Um ponto a ser observado sobre esse serviço é que ele se parece um serviço .NET normal. A única diferença é que as estruturas de dados que estão sendo usadas (`IReliableQueue` e `IReliableDictionary`) são fornecidas pelo Service Fabric e, portanto, são altamente confiáveis, disponíveis e consistentes.

## <a name="when-to-use-reliable-services-apis"></a>Quando usar APIs de Reliable Services
Se um dos itens a seguir caracterizar as necessidades de serviço do aplicativo, considere as APIs de Reliable Services:

* O ideal é que o código do serviço (e opcionalmente, o estado) seja altamente disponível e confiável
* Você precisa de garantias transacionais entre várias unidades de estado (por exemplo, pedidos e itens de linha do pedido).
* O estado do aplicativo pode ser modelado naturalmente como Filas e Dicionários Confiáveis.
* Seu código ou o estado dos aplicativos precisam ser altamente disponíveis com baixa latência em leituras e gravações.
* Seu aplicativo precisa controlar a simultaneidade ou a granularidade das operações realizadas em uma ou mais Reliable Collections.
* Você deseja gerenciar as comunicações e controlar o esquema de particionamento do seu serviço.
* Seu código precisa de um ambiente de tempo de execução de thread livre.
* Seu aplicativo precisa criar ou destruir Reliable Dictionaries, Queues ou Services completos no tempo de execução.
* Você precisa controlar os recursos de backup e restauração fornecidos pelo Service Fabric por meio de programação para o estado do seu serviço.
* Seu aplicativo precisa manter um histórico de alterações para suas unidades de estado.
* Você deseja desenvolver ou consumir provedores de estado personalizados desenvolvidos por terceiros.

## <a name="next-steps"></a>Próximas etapas
* [Início Rápido dos Serviços Confiáveis](service-fabric-reliable-services-quick-start.md)
* [Coleções Confiáveis](service-fabric-reliable-services-reliable-collections.md)
* [O modelo de programação de Reliable Actors](service-fabric-reliable-actors-introduction.md)
