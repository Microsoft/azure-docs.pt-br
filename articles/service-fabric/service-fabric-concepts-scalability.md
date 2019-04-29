---
title: Escalabilidade de serviços do Service Fabric | Microsoft Docs
description: Descreve como dimensionar os serviços de malha do serviço
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: ed324f23-242f-47b7-af1a-e55c839e7d5d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 14a7389fe562b5f3206b81411d2224257051c636
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60781098"
---
# <a name="scaling-in-service-fabric"></a>Dimensionamento no Service Fabric
O Azure Service Fabric facilita o build de aplicativos escalonáveis gerenciando os serviços, as partições e as réplicas em todos os nós em um cluster. Executar várias cargas de trabalho no mesmo hardware permite a utilização máxima dos recursos, mas também fornece flexibilidade em termos de como você opta por dimensionar suas cargas de trabalho. Este vídeo do Channel 9 descreve como você pode criar aplicativos de microsserviço escalonáveis:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T116/player]

O dimensionamento no Service Fabric é feito várias maneiras diferentes:

1. Dimensionamento criando ou removendo instâncias de serviço sem estado
2. Dimensionamento criando ou removendo novos serviços nomeados
3. Dimensionamento criando ou removendo novas instâncias de aplicativos nomeados
4. Dimensionamento usando serviços particionados
5. Dimensionamento adicionando e removendo nós do cluster 
6. Dimensionamento usando métricas do Gerenciador de Recursos de Cluster

## <a name="scaling-by-creating-or-removing-stateless-service-instances"></a>Dimensionamento criando ou removendo instâncias de serviço sem estado
Uma das formas mais simples de dimensionar dentro do Service Fabric é com os serviços sem estado. Quando cria um serviço sem estado, você tem a oportunidade de definir um `InstanceCount`. `InstanceCount` define quantas cópias em execução do código do serviço são criadas quando o serviço é iniciado. Digamos, por exemplo, que haja 100 nós no cluster. Digamos também que um serviço seja criado com um `InstanceCount` igual a 10. Durante o tempo de execução, essas 10 cópias em execução do código podem ficar todas muito ocupadas (ou podem não ficar ocupadas o suficiente). Uma maneira de dimensionar essa carga de trabalho é alterar o número de instâncias. Por exemplo, uma parte do código de monitoramento ou gerenciamento pode alterar o número existente de instâncias para 50 ou para 5, dependendo de a carga de trabalho precisar ser reduzida ou escalada horizontalmente com base na carga. 

C#:

```csharp
StatelessServiceUpdateDescription updateDescription = new StatelessServiceUpdateDescription(); 
updateDescription.InstanceCount = 50;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Powershell:

```posh
Update-ServiceFabricService -Stateless -ServiceName $serviceName -InstanceCount 50
```
### <a name="using-dynamic-instance-count"></a>Usando a Contagem de Instâncias Dinâmica
Especificamente para serviços sem estado, o Service Fabric oferece uma maneira automática de alterar a contagem de instâncias. Isso permite que o serviço seja dimensionado dinamicamente com o número de nós que estão disponíveis. A maneira de aceitar esse comportamento é definir a contagem de instâncias como -1. InstanceCount = -1 é uma instrução para o Service Fabric que diz "Executar este serviço sem estado em todos os nós". Se o número de nós for alterado, o Service Fabric alterará automaticamente a contagem de instâncias para corresponder à alteração, garantindo que o serviço seja executado em todos os nós válidos. 

C#:

```csharp
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//Set other service properties necessary for creation....
serviceDescription.InstanceCount = -1;
await fc.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless -PartitionSchemeSingleton -InstanceCount "-1"
```

## <a name="scaling-by-creating-or-removing-new-named-services"></a>Dimensionamento criando ou removendo novos serviços nomeados
Uma instância de serviço nomeada é uma instância específica de um tipo de serviço (consulte [Ciclo de vida do aplicativo do Service Fabric](service-fabric-application-lifecycle.md)) dentro de uma instância de aplicativo nomeada no cluster. 

Novas instâncias de serviço nomeadas podem ser criadas (ou removidas) conforme os serviços ficarem mais ou menos ocupados. Isso permite que as solicitações sejam disseminadas em mais instâncias de serviço, geralmente permitindo que a carga nos serviços existentes diminua. Ao criar serviços, o Gerenciador de Recursos de Cluster do Service Fabric coloca os serviços do cluster de maneira distribuída. As decisões exatas são governadas pelas [métricas](service-fabric-cluster-resource-manager-metrics.md) no cluster e por outras regras de posicionamento. Os serviços podem ser criados de várias maneiras diferentes, mas as mais comuns são por meio de ações administrativas, como alguém chamar [`New-ServiceFabricService`](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps) ou o código chamar [`CreateServiceAsync`](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync?view=azure-dotnet). `CreateServiceAsync` pode ser chamado até mesmo de dentro de outros serviços em execução no cluster.

A criação de serviços de forma dinâmica pode ser usada em todo tipo de cenário e é um padrão comum. Por exemplo, considere um serviço com estado que representa um fluxo de trabalho específico. Chamadas que representam trabalho aparecerão nesse serviço e o serviço executará as etapas do fluxo de trabalho e registrará o andamento. 

Como você dimensionaria esse serviço específico? O serviço pode ser multilocatário de alguma forma e pode aceitar chamadas e disparar etapas para muitas instâncias diferentes do mesmo fluxo de trabalho, tudo ao mesmo tempo. No entanto, isso pode tornar o código mais complexo, pois agora ele precisa se preocupar com muitas instâncias diferentes do mesmo fluxo de trabalho, todas elas em estágios diferentes e de clientes diferentes. Além disso, lidar com vários fluxos de trabalho ao mesmo tempo não resolve o problema de escala. Isso ocorre porque, em algum momento, esse serviço consumirá recursos demais para caber em um determinado computador. Muitos serviços que não foram criados para esse padrão também apresentam dificuldades devido a algum gargalo ou lentidão inerente em seu código. Esses tipos de problemas fazem com que o serviço não funcione tão bem quando o número de fluxos de trabalho simultâneos que ele está acompanhando aumenta.  

Uma solução é criar uma instância do serviço para cada instância diferente do fluxo de trabalho que você deseja acompanhar. Este é um ótimo padrão e funciona quer o serviço seja com ou sem estado. Para que esse padrão funcione, geralmente há outro serviço que atua como um "Serviço de gerenciador de carga de trabalho". A função desse serviço é receber solicitações e encaminhar essas solicitações para outros serviços. O gerenciador pode criar dinamicamente uma instância do serviço de carga de trabalho quando receber a mensagem e, em seguida, passar solicitações para esses serviços. O serviço de gerenciador também pode receber retornos de chamada quando um determinado serviço de fluxo de trabalho concluir seu trabalho. Quando receber esses retornos de chamada, o gerenciador pode excluir essa instância do serviço de fluxo de trabalho ou deixá-la se mais chamadas forem esperadas. 

Versões avançadas desse tipo de gerenciador podem até mesmo criar pools dos serviços que gerencia. O pool ajuda a garantir que, quando uma nova solicitação for recebida, ela não precise aguardar o serviço para ser executada. Em vez disso, o gerenciador pode apenas escolher um serviço de fluxo de trabalho que não esteja ocupado no pool ou pode encaminhar aleatoriamente. Manter um pool de serviços disponível torna a manipulação de novas solicitações mais rápida, pois é menos provável que a solicitação precise aguardar um novo serviço para ser executada. A criação de novos serviços é rápida, mas não é gratuita ou instantânea. O pool ajuda a minimizar o tempo que a solicitação precisa aguardar antes de ser atendida. Frequentemente, você verá esse padrão de gerenciador e pool quando os tempos de resposta forem a prioridade. Enfileirar a solicitação e criar o serviço em segundo plano e, _em seguida_, transmiti-lo também é um padrão popular de gerenciador, assim como a criação e a exclusão de serviços com base no acompanhamento da quantidade de trabalho que o serviço tem pendente no momento. 

## <a name="scaling-by-creating-or-removing-new-named-application-instances"></a>Dimensionamento criando ou removendo novas instâncias de aplicativos nomeados
Criar e excluir instâncias inteiras do aplicativo é semelhante ao padrão de criação e exclusão de serviços. Para esse padrão, há um serviço de gerenciador que está tomando a decisão com base nas solicitações que está vendo e nas informações que está recebendo de outros serviços dentro do cluster. 

Quando a criação de uma nova instância de aplicativo nomeada deve ser usada em vez da criação de uma nova instância de serviço nomeada em algum aplicativo já existente? Há alguns casos:

  * A nova instância de aplicativo é destinada a um cliente cujo código precisa ser executado com algumas configurações de segurança ou identidade específicas.
    * O Service Fabric permite definir diferentes pacotes de códigos para serem executados em identidades específicas. Para iniciar o mesmo pacote de códigos em identidades diferentes, as ativações precisam ocorrer em instâncias diferentes do aplicativo. Considere um caso em que você tem as cargas de trabalho de um cliente existente implantadas. Elas podem estar em execução sob uma identidade específica, de modo que você pode monitorar e controlar seu acesso a outros recursos, como bancos de dados remotos ou outros sistemas. Nesse caso, quando um novo cliente se inscreve, você provavelmente não deseja ativar seu código no mesmo contexto (espaço de processo). Embora seja possível fazer isso, ficaria mais difícil para o código de seu serviço atuar no contexto de uma identidade específica. Normalmente, você precisa ter mais código de gerenciamento de identidade, isolamento e segurança. Em vez de usar diferentes instâncias de serviço nomeadas na mesma instância do aplicativo e, portanto, o mesmo espaço de processo, você pode usar diferentes instâncias nomeadas de aplicativo do Service Fabric. Isso torna mais fácil definir contextos de identidade diferentes.
  * A nova instância de aplicativo também serve como um meio de configuração
    * Por padrão, todas as instâncias de serviço nomeadas de um tipo de serviço específico dentro de uma instância do aplicativo serão executadas no mesmo processo em um determinado nó. Isso significa que, embora você possa configurar cada instância de serviço de forma diferente, fazer isso é complicado. Os serviços precisam ter algum token que eles usem para procurar sua configuração dentro de um pacote de configuração. Normalmente, isso é apenas o nome do serviço. Isso funciona bem, mas associa a configuração aos nomes das instâncias de serviço nomeadas individuais dentro dessa instância do aplicativo. Isso pode ser confuso e difícil de gerenciar, pois a configuração normalmente é um artefato de tempo de design com valores específicos de instância do aplicativo. A criação de mais serviços sempre significa mais atualizações de aplicativo para alterar as informações nos pacotes de configuração ou para implantar novos pacotes, para que os novos serviços possam procurar suas informações específicas. Normalmente, é mais fácil criar uma nova instância de aplicativo nomeada. Em seguida, você pode usar os parâmetros do aplicativo para definir a configuração que for necessária para os serviços. Dessa forma, todos os serviços que forem criados dentro dessa instância de aplicativo nomeada podem herdar configuração específicas. Por exemplo, em vez de ter um único arquivo de configuração com as configurações e personalizações de cada cliente, como segredos ou limites de recursos por cliente, você teria uma instância de aplicativo diferente para cada cliente com essas configurações substituídas. 
  * O novo aplicativo funciona como um limite de atualização
    * Dentro do Service Fabric, instâncias de aplicativo nomeadas diferentes servem como limites de atualização. Uma atualização de uma instância de aplicativo nomeada não afetará o código que outra instância de aplicativo nomeada está executando. Os diferentes aplicativos terminarão executando versões diferentes do mesmo código nos mesmos nós. Isso pode ser um fator quando você precisar tomar uma decisão de dimensionamento, porque você pode escolher se o novo código deve seguir as mesmo atualizações que o outro serviço ou não. Por exemplo, digamos que chegue ao serviço de gerenciamento uma chamada responsável por dimensionar as cargas de trabalho de um determinado cliente criando e excluindo serviços dinamicamente. Nesse caso, no entanto, a chamada é para uma carga de trabalho associada a m _novo_ cliente. A maioria dos clientes preferem ficar isolados uns dos outros não apenas pelos motivos de segurança e configuração listados anteriormente, mas porque isso oferece mais flexibilidade em termos de executar versões específicas do software e escolher quando elas são atualizadas. Você também pode criar uma nova instância de aplicativo e criar o serviço nela, simplesmente para particionar ainda mais a quantidade de seus serviços que qualquer atualização afetará. Instâncias de aplicativo separadas proporcionam maior granularidade ao fazer atualizações de aplicativo e também habilitam testes A/B e implantações de verde/azul. 
  * A instância de aplicativo existente está cheia
    * No Service Fabric, a [capacidade do aplicativo](service-fabric-cluster-resource-manager-application-groups.md) é um conceito que você pode usar para controlar a quantidade de recursos disponíveis para instâncias de aplicativo específicas. Por exemplo, você pode decidir que um determinado serviço precisa ter outra instância criada para ser dimensionado. No entanto, essa instância de aplicativo está sem capacidade para uma determinada métrica. Se esse cliente ou carga de trabalho específica ainda for receber mais recursos, então você poderá aumentar a capacidade existente para esse aplicativo ou criar um novo aplicativo. 

## <a name="scaling-at-the-partition-level"></a>Dimensionamento no nível da partição
O Service Fabric permite o particionamento. O particionamento divide um serviço em várias seções lógicas e físicas, e cada uma delas funciona de forma independente. Isso é útil com serviços com estado, pois nenhum conjunto de réplicas precisa lidar com todas as chamadas e manipular todos os estados de uma vez. A [Visão geral de particionamento](service-fabric-concepts-partitioning.md) fornece informações sobre os tipos de esquemas de particionamento para os quais há suporte. As réplicas de cada partição são distribuídas entre os nós de um cluster, distribuindo a carga do serviço e garantindo que nem o serviço inteiro, nem nenhuma partição tenha um ponto de falha. 

Considere um serviço que usa um esquema de particionamento de intervalo com uma chave baixa de 0, uma chave alta de 99 e uma contagem de partições de 4. Em um cluster de três nós, o serviço pode ser disposto com quatro réplicas que compartilham os recursos em cada nó, conforme mostrado aqui:

<center>

![Layout de partição com três nós](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

Se você aumentar o número de nós, o Service Fabric moverá algumas das réplicas existentes nele. Por exemplo, digamos que o número de nós aumente para quatro e que as réplicas sejam redistribuídas. Agora, o serviço tem três réplicas em execução em cada nó, cada um pertencente a diferentes partições. Isso permite uma melhor utilização dos recursos, desde que o novo nó não seja frio. Normalmente, isso também melhora o desempenho, porque cada serviço tem mais recursos disponíveis para ele.

<center>

![Layout de partição com quatro nós](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-by-using-the-service-fabric-cluster-resource-manager-and-metrics"></a>Dimensionamento usando métricas e o Gerenciador de Recursos de Cluster do Service Fabric
[Métricas](service-fabric-cluster-resource-manager-metrics.md) são como os serviços expressam seu consumo de recursos ao Service Fabric. O uso de métricas dá ao Gerenciador de Recursos de Cluster a oportunidade de reorganizar e otimizar o layout do cluster. Por exemplo, pode haver vários recursos no cluster, mas eles podem não estar alocados aos serviços que estão fazendo o trabalho atualmente. O uso de métricas permite que o Gerenciador de Recursos de Cluster reorganize o cluster para garantir que os serviços tenham acesso aos recursos disponíveis. 


## <a name="scaling-by-adding-and-removing-nodes-from-the-cluster"></a>Dimensionamento adicionando e removendo nós do cluster 
Outra opção para dimensionar com o Service Fabric é alterar o tamanho do cluster. Alterar o tamanho do cluster significa adicionar ou remover nós de um ou mais tipos de nós no cluster. Por exemplo, considere um caso em que todos os nós no cluster estejam quentes. Isso significa que os recursos do cluster estão quase todos consumidos. Nesse caso, adicionar mais nós ao cluster é a melhor maneira de dimensionar. Após os novos nós ingressarem no cluster, o Gerenciador de Recursos de Cluster do Service Fabric move serviços para eles, fazendo com que haja menos carga total nos nós existentes. Para serviços sem estado com contagem de instâncias = -1, mais instâncias de serviço são criadas automaticamente. Isso permite que algumas chamadas sejam movidas dos nós existentes para novos nós. 

Para obter mais informações, consulte [dimensionamento do cluster](service-fabric-cluster-scaling.md).

## <a name="putting-it-all-together"></a>Juntando as peças
Vamos reunir tudo o que discutimos aqui e aplicar a um exemplo. Considere o seguinte serviço: você está tentando criar um serviço que atue como um catálogo de endereços, mantendo nomes e informações de contato. 

À direita com antecedência você tem um monte de perguntas relacionadas à escala: Quantos usuários você pretende ter? Quantos contatos cada usuário armazenará? Tentar descobrir isso enquanto você está tentando fazer o serviço funcionar pela primeira vez é difícil. Digamos que você optasse por um único serviço estático com uma contagem de partições específica. As consequências de escolher a contagem de partições errada poderia causar problemas de escala mais tarde. Da mesma forma, mesmo se escolher a contagem correta, talvez você não tenha todas as informações necessárias. Por exemplo, você também precisa decidir o tamanho do cluster logo no início, tanto em termos do número de nós quanto de seus tamanhos. Geralmente, é difícil prever quantos recursos um serviço consumirá durante seu tempo de vida. Também pode ser difícil saber antecipadamente o padrão de tráfego que realmente passa pelo serviço. Por exemplo, talvez as pessoas adicionem e removam contatos somente de manhã ou talvez isso seja distribuído uniformemente ao longo do dia. Com base nisso, talvez seja necessário reduzir e escalar horizontalmente de forma dinâmica. Talvez você possa aprender a prever quando precisará reduzir e escalar horizontalmente, mas, de qualquer forma, você provavelmente precisará reagir a alterações no consumo de recursos por seu serviço. Isso pode envolver alterar o tamanho do cluster para fornecer mais recursos quando reorganizar o uso de recursos existentes não for suficiente. 

Mas por que mesmo tentar escolher um único esquema de partição para todos os usuários? Por que limitar-se a um serviço e um cluster estático? A situação real costuma ser mais dinâmica. 

Ao criar algo para ser dimensionado, considere o seguinte padrão dinâmico. Talvez seja necessário adaptá-lo à sua situação:

1. Em vez de tentar escolher um esquema de particionamento para todos antecipadamente, crie um "serviço de gerenciador".
2. O trabalho do serviço de gerenciador é examinar as informações do cliente quando ele se inscreve no serviço. Depois, dependendo dessas informações, o serviço de gerenciador cria uma instância de seu serviço de armazenamento de contatos _real_ _apenas para esse cliente_. Se ele precisar de uma configuração específica, de isolamento ou de atualizações, você também poderá optar por criar uma instância de Aplicativo para esse cliente. 

Esse padrão de criação dinâmica traz muitos benefícios:

  - Você não fica tentando adivinhar a contagem correta de partições para todos os usuários com antecedência ou criar um único serviço infinitamente dimensionável por conta própria. 
  - Usuários diferentes não precisam ter a mesma contagem de partições, contagem de réplicas, restrições de posicionamento, métricas, cargas padrão, nomes de serviço, configurações de DNS ou qualquer uma das outras propriedades especificadas no nível do serviço ou do aplicativo. 
  - Você obtém uma maior segmentação de dados. Cada cliente tem sua própria cópia do serviço
    - Cada serviço de cliente pode ser configurado de forma diferente e pode ter mais ou menos recursos, com mais ou menos partições ou réplicas, conforme a necessidade baseada na escala esperada.
      - Por exemplo, digamos que o cliente tenha pago pela camada "Ouro" – ele poderia receber mais réplicas ou uma contagem maior de partições e, potencialmente, mais recursos dedicados a seus serviços por meio das métricas e capacidades do aplicativo.
      - Ou digamos que ele tenha fornecido informações indicando que o número de contatos de que precisava era "Pequeno" — ele receberia apenas algumas partições ou poderia até mesmo ser colocado em um pool de serviços compartilhados com outros clientes.
  - Você não vai executar um monte de instâncias de serviços ou réplicas enquanto aguarda os clientes
  - Se um cliente sair, remover suas informações do serviço é tão simples quanto a exclusão de um serviço ou aplicação por um gerente.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os conceitos do Service Fabric, confira os seguintes artigos:

* [Disponibilidade dos serviços de malha do serviço](service-fabric-availability-services.md)
* [Particionando serviços da Malha do Serviço](service-fabric-concepts-partitioning.md)
