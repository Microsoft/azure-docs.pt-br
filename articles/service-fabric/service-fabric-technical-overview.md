---
title: Aprenda a terminologia do Azure Service Fabric | Microsoft Docs
description: "Uma visão geral da terminologia do Service Fabric. Aborda os principais conceitos de terminologia e os termos usados no restante da documentação."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: chackdan;subramar
ms.assetid: 3a970679-e19e-43b3-9be8-71773f307c57
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/30/2017
ms.author: ryanwi
ms.openlocfilehash: 204c415a6dc77af1be78f8b28a1a5cbcd2fa7883
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-terminology-overview"></a>Visão geral da terminologia do Service Fabric
Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, implantação e gerenciamento de microsserviços escalonáveis e confiáveis. Este tópico fornece detalhes da terminologia usada pelo Service Fabric para compreensão dos termos usados na documentação.

Os conceitos listados nesta seção também são abordados nos seguintes vídeos da Microsoft Virtual Academy: <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">Conceitos principais</a>, <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965">Conceitos de tempo de design</a> e <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">Conceitos de tempo de execução</a>.

## <a name="infrastructure-concepts"></a>Conceitos de infraestrutura
**Cluster**: um conjunto de máquinas físicas ou virtuais conectadas em rede, no qual os microsserviços são implantados e gerenciados.  Os clusters podem ser dimensionados para milhares de máquinas.

**Nó**: uma máquina ou VM que faz parte de um cluster é chamado de nó. Cada nó recebe um nome de nó (uma cadeia de caracteres). Os nós têm características como propriedades de posicionamento. Cada máquina ou VM tem um serviço do Windows de início automático, `FabricHost.exe` que começa a ser executado na inicialização e então inicia dois executáveis: `Fabric.exe` e `FabricGateway.exe`. Esses dois executáveis compõem o nó. Para cenários de teste, você pode hospedar vários nós em um único computador ou VM, executando várias instâncias do `Fabric.exe` e do `FabricGateway.exe`.

## <a name="application-concepts"></a>Conceitos de aplicativo
**Tipo de Aplicativo**: o nome/versão atribuído a uma coleção de tipos de serviço. São definidas em um arquivo `ApplicationManifest.xml` , inserido em um diretório de pacote de aplicativos que é então copiado para o repositório de imagens do cluster do Service Fabric. Assim, você pode criar um aplicativo nomeado desse tipo de aplicativo dentro do cluster.

Leia o artigo [Modelo de aplicativo](service-fabric-application-model.md) para saber mais.

**Pacote de Aplicativos`ApplicationManifest.xml`: um diretório de disco contendo o arquivo**  do tipo de aplicativo. Faz referência aos pacotes de serviço de cada tipo de serviço que compõe o tipo de aplicativo. Os arquivos no diretório do pacote de aplicativos são copiados no armazenamento de imagens do cluster do Service Fabric. Por exemplo, um pacote de aplicativos para um tipo de aplicativo de email pode conter referências a um pacote de serviços Fila, um pacote de serviços front-end e um pacote de serviços de banco de dados.

**Aplicativo Nomeado**: depois de copiar um pacote de aplicativos para o repositório de imagens, você cria uma instância do aplicativo no cluster, especificando o tipo de aplicativo do pacote de aplicativos (usando seu nome/versão). Cada instância do tipo de aplicativo é atribuído a um nome URI com esta aparência: `"fabric:/MyNamedApp"`. Em um cluster, você pode criar vários aplicativos nomeados a partir de um tipo de aplicativo único. Você também pode criar aplicativos nomeados a partir de diferentes tipos de aplicativos. Cada aplicativo nomeado é gerenciado e possui controle de versão independente.      

**Tipo de Serviço**: o nome/versão atribuída aos pacotes de códigos, pacotes de dados e pacotes de configuração de um serviço. São definidas em um arquivo `ServiceManifest.xml`, inserido em um diretório de pacote de serviços, e o diretório de pacote de serviços é referenciado pelo arquivo `ApplicationManifest.xml` de um pacote de aplicativos. Dentro do cluster, depois de criar um aplicativo nomeado, você pode criar um serviço nomeado a partir de um dos tipos de serviço do tipo de aplicativo. O arquivo `ServiceManifest.xml` do tipo de serviço descreve o serviço.

Leia o artigo [Modelo de aplicativo](service-fabric-application-model.md) para saber mais.

Há dois tipos de serviço:

* **Sem monitoração de estado:** use um serviço sem estado quando o estado persistente do serviço for armazenado em um serviço de armazenamento externo, como o Armazenamento do Azure, Banco de Dados SQL do Azure ou Azure Cosmos DB. Use um serviço sem estado quando o serviço não tiver nenhum armazenamento persistente. Por exemplo, um serviço de calculadora no qual os valores são passados para o serviço, um cálculo é realizado usando esses valores e um resultado é retornado.
* **Com estado:** use um serviço com estado quando quiser que o Service Fabric gerencie o estado de seu serviço por meio de seus modelos de programação Reliable Collections ou Reliable Actors. Especifique quantas partições você deseja distribuir pelo seu estado (para fins de escalabilidade) ao criar um serviço nomeado. Especifique também quantas vezes deve replicar o estado entre os nós (para fins de confiabilidade). Cada serviço nomeado tem uma única réplica primária e várias réplicas secundárias. Modifique o estado do serviço nomeado escrevendo para a réplica primária. O Service Fabric replica então esse estado para todas as réplicas secundárias, mantendo seu estado em sincronia. O Service Fabric detecta automaticamente quando uma réplica primaria falha e promove uma réplica secundária existente a réplica primária. O Service Fabric cria então uma nova réplica secundária.  

**Réplicas ou instâncias** consulte o código (e o estado de serviços com estado) de um serviço implantado e em execução. Consulte [Replicas and Instances](service-fabric-concepts-replica-lifecycle.md) (Réplicas e instâncias)

**Reconfiguração** refere-se ao processo de qualquer alteração no conjunto de réplicas de um serviço. Consulte [Reconfiguration](service-fabric-concepts-reconfiguration.md) (Reconfiguração)

**Pacote de Serviços`ServiceManifest.xml`: um diretório de disco contendo o arquivo**  do tipo de serviço. Este arquivo faz referência ao código, aos dados estáticos e a pacotes de configuração para o tipo de serviço. Os arquivos no diretório do pacote de serviços são referenciados pelo arquivo `ApplicationManifest.xml` do tipo de aplicativo. Por exemplo, um pacote de serviços pode fazer referência ao código, aos dados estáticos e aos pacotes de configuração que compõem um serviço de banco de dados.

**Serviço Nomeado**: depois de criar um aplicativo nomeado, você pode criar uma instância de um de seus tipos de serviço no cluster, especificando o tipo de serviço (usando seu nome/versão). Cada instância de tipo de serviço recebe um nome de URI com escopo dentro do URI de seu aplicativo nomeado. Por exemplo, se você criar um serviço chamado "MyDatabase" dentro de um aplicativo chamado "MyNamedApp", o URI terá esta aparência: `"fabric:/MyNamedApp/MyDatabase"`. Dentro de um aplicativo nomeado, você pode criar vários serviços nomeados. Cada serviço nomeado pode ter seu próprio esquema de partição e contagens de instância/réplica.

**Pacote de Códigos**: um diretório de disco contendo arquivos executáveis do tipo de serviço (normalmente, arquivos EXE/DLL). Os arquivos no diretório do pacote de códigos são referenciados pelo arquivo `ServiceManifest.xml` do tipo de serviço. Quando um serviço nomeado é criado, o pacote de códigos é copiado para os nós selecionados para executar o serviço nomeado. Em seguida, o código começa a ser executado. Há dois tipos de arquivos executáveis de pacote de códigos:

* **Executáveis convidados**: executáveis que são executados “como são” no sistema operacional host (Windows ou Linux). Ou seja, esses executáveis não criam um vínculo com, ou fazem referência a, arquivos de tempo de execução do Service Fabric e, portanto, não usam qualquer um dos modelos de programação do Service Fabric. Esses executáveis não conseguem usar alguns recursos do Service Fabric, como o serviço de nomenclatura para descoberta de ponto de extremidade. Executáveis de convidado não podem relatar métricas de carga específicas para cada instância de serviço.
* **Executáveis do Host de Serviço**: são executáveis que usam modelos de programação do Service Fabric criando vínculos com arquivos de tempo de execução do Service Fabric, habilitando os recursos deste. Por exemplo, uma instância de serviço nomeada pode registrar pontos de extremidade com o Serviço de Nomenclatura do Service Fabric, e também pode relatar métricas de carga.      

**Pacote de Dados**: um diretório de disco contendo os arquivos de dados estáticos e somente leitura do tipo de serviço (normalmente arquivos de foto, som e vídeo). Os arquivos no diretório do pacote de dados são referenciados pelo arquivo `ServiceManifest.xml` do tipo de serviço. Quando um serviço nomeado é criado, o pacote de dados é copiado para os nós selecionados para executar o serviço nomeado.  O código começa a ser executado e agora pode acessar os arquivos de dados.

**Pacote de Configuração**: um diretório de disco contendo os arquivos de configuração estáticos e somente leitura do tipo de serviço (normalmente arquivos de texto). Os arquivos no diretório do pacote de configuração são referenciados pelo arquivo `ServiceManifest.xml` do tipo de serviço. Quando um serviço nomeado é criado, os arquivos no pacote de configuração é copiado para um ou mais nós selecionados para executar o serviço nomeado. O código começa a ser executado e agora pode acessar os arquivos de configuração.

**Contêineres**: por padrão, o Service Fabric implanta e ativa esses serviços como processos. O Service Fabric também pode implantar serviços em imagens de contêiner. Contêineres são uma tecnologia de virtualização que virtualiza o sistema operacional subjacente de aplicativos. Um aplicativo e seu tempo de execução, suas dependências e suas bibliotecas de sistema são executados dentro de um contêiner com acesso privado e completo à exibição isolada própria do contêiner dos constructos do sistema operacional. O Service Fabric dá suporte a contêineres do Docker em contêineres Linux e Windows Server.  Para obter mais informações, leia [Service Fabric e contêineres](service-fabric-containers-overview.md).

**Esquema de Partição**: ao criar um serviço nomeado, você especifica um esquema de partição. Serviços com grandes quantidades de estado dividem os dados entre as partições, que distribuem o estado pelos nós do cluster. Isso permite que o estado de seu serviço nomeado seja dimensionado. Dentro de uma partição, serviços nomeados sem estado têm instâncias, enquanto serviços nomeados com estado têm réplicas. Normalmente, os serviços nomeadas sem estado têm apenas uma partição, já que não têm estado interno. As instâncias de partição fornecem disponibilidade; se uma instância falha, outras instâncias continuam a funcionar normalmente e, em seguida, o Service Fabric cria uma nova instância. Serviços nomeados com estado mantêm seu estado dentro de réplicas, e cada partição tem sua própria réplica, com todo o estado mantido em sincronia. Caso uma réplica falhe, o Service Fabric compila uma nova réplica a partir das réplicas existentes.

Leia o artigo [Particionar os Reliable Services do Service Fabric](service-fabric-concepts-partitioning.md) para saber mais.

## <a name="system-services"></a>Serviços do sistema
Há serviços do sistema que são criados em cada cluster que fornece os recursos de plataforma do Service Fabric.

**Serviço de Nomenclatura**: cada cluster do Service Fabric tem um serviço de nomenclatura que resolve nomes de serviço para uma localização no cluster. Gerencie os nomes de serviço e propriedades, semelhante a um DNS (Serviço de Nomes de Domínio ) da Internet para o cluster. Os clientes podem comunicar-se com segurança com qualquer nó no cluster usando o Serviço de Nomenclatura para resolver um nome de serviço e sua localização.  Os aplicativos são movidos no cluster, por exemplo, devido a falhas, balanceamento de recursos ou redimensionamento do cluster. Você pode desenvolver serviços e clientes que resolvem o local de rede atual. Os clientes obtêm o endereço IP do computador real e a porta em que ele está sendo executado.

Leia [Comunicar-se com os serviços](service-fabric-connect-and-communicate-with-services.md) para obter mais informações sobre como usar as APIs de comunicação do serviço e do cliente que funciona com o serviço de Nomenclatura.

**Serviço de Repositório de Imagens**: cada cluster do Service Fabric tem um serviço de Repositório de Imagens em que os pacotes de aplicativos implantados com versão são mantidos. Copie um pacote de aplicativos para o Repositório de Imagens e registre o tipo de aplicativo nesse pacote de aplicativos. Após o provisionamento do tipo de aplicativo, você cria um aplicativo nomeado com base nele. Você pode cancelar o registro de um tipo de aplicativo do serviço de Repositório de Imagens após a exclusão de todos os seus aplicativos nomeados.

Leia [Entender a configuração ImageStoreConnectionString](service-fabric-image-store-connection-string.md) para saber mais sobre o serviço de Armazenamento de Imagens.

Leia o artigo [Implantar um aplicativo](service-fabric-deploy-remove-applications.md) para saber mais sobre como implantar aplicativos no repositório de imagens.

**Serviço FM (Gerenciador de Failover)**: Cada cluster do Service Fabric tem um serviço Gerenciador de Failover responsável por executar funções relacionadas à alta disponibilidade e à consistência de serviços, assim como por orquestrar atualizações de cluster e de aplicativo e por interagir com outros componentes do sistema.

## <a name="built-in-programming-models"></a>Modelos de programação internos
Há dois modelos de programação do .NET Framework disponíveis para compilação de serviços do Service Fabric:

**Reliable Services**: uma API para criar serviços com e sem estado. O serviço com estado armazena seu estado em Reliable Collections (como um dicionário ou uma fila). Você também pode conectar várias pilhas de comunicação, como API Web e WCF (Windows Communication Foundation).

**Reliable Actors**: uma API para criar objetos com e sem estado por meio do modelo de programação de ator virtual. Esse modelo pode ser útil quando você tem muitas unidades independentes de computação/estado. Como esse modelo usa um modelo de threading baseado em turno, é melhor evitar um código que chama outros atores ou serviços, já que um ator individual não pode processar outras solicitações de entrada até que todas as solicitações de saída tenham sido concluídas.

Leia o artigo [Escolher um modelo de programação para o serviço](service-fabric-choose-framework.md) para saber mais.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o Service Fabric:

* [Visão geral da Malha do Serviço](service-fabric-overview.md)
* [Por que usar uma abordagem de microsserviço para construir aplicativos?](service-fabric-overview-microservices.md)
* [Cenários de aplicativos](service-fabric-application-scenarios.md)

