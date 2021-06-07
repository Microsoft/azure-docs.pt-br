---
title: Versões de Service Fabric do Azure
description: Notas de versão do Azure Service Fabric. Inclui informações sobre os recursos e melhorias mais recentes em Service Fabric.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 7f99c37cd9f029c7478d4102bd17c952ffd52f8c
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044270"
---
# <a name="service-fabric-releases"></a>Versões Service Fabric

- <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Guias de solução de problemas</a> 
- <a href="https://github.com/Azure/service-fabric-issues" target="blank">Acompanhamento de questões</a> 
- <a href="/azure/service-fabric/service-fabric-support" target="blank">Opções de suporte</a> 
- <a href="/azure/service-fabric/service-fabric-versions" target="blank">Versões com suporte</a> 
- <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Exemplos de código</a>

Este artigo fornece mais informações sobre as versões e atualizações mais recentes para o Service Fabric Runtime e SDKs.

## <a name="service-fabric-72"></a>Service Fabric 7,2

Estamos empolgados em anunciar que a versão 7,2 do tempo de execução do Service Fabric começou a distribuir para várias regiões do Azure, juntamente com as ferramentas e atualizações do SDK. As atualizações para o SDK do .NET, o SDK do Java e o tempo de execução do Service Fabric estão disponíveis por meio de Web Platform Installer, pacotes NuGet e repositórios Maven.

### <a name="key-announcements"></a>Principais anúncios

- Versão **prévia**: [**Service Fabric clusters gerenciados**](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-managed-clusters-are-now-in-public-preview/ba-p/1721572) estão agora em visualização pública. Service Fabric clusters gerenciados visam simplificar a implantação e o gerenciamento de cluster encapsulando os recursos subjacentes que compõem um Cluster Service Fabric em um único recurso ARM. Para obter mais detalhes, consulte [visão geral do Service Fabric cluster gerenciado](./overview-managed-cluster.md).
- **Visualização**: [**o suporte a serviços sem estado com um número de instâncias maior que o número de nós**](./service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) agora está em visualização pública. Uma política de posicionamento permite a criação de várias instâncias sem estado de uma partição em um nó.
- [**FabricObserver (fo) 3,0**](https://aka.ms/sf/fabricobserver) já está disponível.
    - Agora você pode executar o FabricObserver em clusters do Linux e do Windows.
    - Agora você pode criar plugins de observador personalizados. Consulte o [Leiame de plugins](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Plugins.md) e o [projeto de plug-in de exemplo](https://github.com/microsoft/service-fabric-observer/tree/master/SampleObserverPlugin) para obter detalhes e código.
    - Agora você pode alterar qualquer configuração de observador por meio da atualização de parâmetros de aplicativo. Isso significa que você não precisa mais reimplantar a FO para modificar as configurações específicas do observador. Consulte o [exemplo](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Using.md#parameterUpdates).
- [**Suporte para imagens de contêiner do Ubuntu 18, 4 Onebox**](https://hub.docker.com/_/microsoft-service-fabric-onebox).
- **Visualização**: [ **referência de keyvault para aplicativos Service Fabric dá suporte **apenas a segredos com versão**. Não há suporte para segredos sem versões.**](./service-fabric-keyvault-references.md)
- O SDK da it requer a atualização mais recente do VS 2019, 16.7.6 ou 16,8 Preview 4, para poder criar novos projetos .NET Framework sem monitoração de estado/com estado/de atores. Se você não tiver a atualização mais recente do VS, depois de criar o projeto de serviço, use o Gerenciador de pacotes para instalar o Microsoft. Service Fabric. Services (versão 4.2. x) para projetos com estado/sem estado e Microsoft. Service Fabric. atores (versão 4.2. x) para projetos de ator de nuget.org.
- **RunToCompletion**: o Service Fabric dá suporte ao conceito de execução até a conclusão para executáveis de convidado. Com essa atualização depois que a réplica for executada até a conclusão, os recursos de cluster alocados a esta réplica serão liberados.
- O [**suporte à governança de recursos foi aprimorado**](./service-fabric-resource-governance.md): permitindo as especificações de solicitações e limites para recursos de CPU e memória.

### <a name="service-fabric-72-releases"></a>Versões do Service Fabric 7,2
| Data de liberação | Versão | Obter mais informações |
|---|---|---|
| 21 de outubro de 2020 | [Service Fabric do Azure 7,2](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-release/ba-p/1805653)  | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72-releasenotes.md)|
| 9 de novembro de 2020 | [Lançamento da segunda atualização do Azure Service Fabric 7,2](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-second-refresh-release/ba-p/1874738) | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU2-releasenotes.md) |
| 10 de novembro de 2020  | Versão de atualização do terceiro Service Fabric 7,2 do Azure | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU3-releasenotes.md) |
| 2 de dezembro de 2020 | [Lançamento da quarta atualização 7,2 do Azure Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fourth-refresh-release/ba-p/1950584) | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU4.md)
| 25 de janeiro de 2021 | [Versão de atualização do quinto 7,2 do Azure Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fifth-refresh-release/ba-p/2096575) | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU5-ReleaseNotes.md)
| 17 de fevereiro de 2021 | [Azure Service Fabric 7,2 sexta versão de atualização](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-sixth-refresh-release/ba-p/2144685) | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU6-ReleaseNotes.md)
| 10 de março de 2021 | [Azure Service Fabric 7,2 sétima versão de atualização](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-seventh-refresh-release/ba-p/2201100) | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU7-releasenotes.md)

## <a name="previous-versions"></a>Versões anteriores

### <a name="service-fabric-71"></a>Service Fabric 7,1

Devido à crise atual de COVID-19 e levando em consideração os desafios enfrentados por nossos clientes, estamos disponibilizando 7,1, mas não atualizaremos automaticamente os clusters definidos para receber atualizações automáticas. Estamos pausando atualizações automáticas até um aviso adicional para garantir que os clientes possam aplicar atualizações quando mais apropriado para elas, para evitar interrupções inesperadas.

Você poderá atualizar para o 7,1 por meio do [portal do Azure](./service-fabric-cluster-upgrade-version-azure.md#upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal) ou por meio de uma [implantação de Azure Resource Manager](./service-fabric-cluster-upgrade-version-azure.md#set-the-upgrade-mode-using-a-resource-manager-template).

Service Fabric clusters com atualizações automáticas habilitadas começarão a receber a atualização 7,1 automaticamente depois que retomarmos o procedimento de distribuição padrão. Forneceremos outro comunicado antes que a distribuição padrão seja iniciada no [site da comunidade Service Fabric Tech](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).
Também publicamos atualizações na data de fim do suporte para versões principais a partir de 6,5 até 7,1 [aqui](./service-fabric-versions.md#supported-versions). 

#### <a name="key-announcements"></a>Principais anúncios

- **Disponibilidade geral** de [ **Service Fabric identidades gerenciadas para aplicativos Service Fabric**](./concepts-managed-identity.md)
- [**Suporte para Ubuntu 18, 4**](./service-fabric-tutorial-create-vnet-and-linux-cluster.md)
 - [**Visualização: conjunto de dimensionamento de máquinas virtuais suporte a disco do so efêmero**](./service-fabric-cluster-azure-deployment-preparation.md#use-ephemeral-os-disks-for-virtual-machine-scale-sets)* *: os discos do sistema operacional efêmero são o armazenamento criado na máquina virtual local e não são salvos no armazenamento remoto do Azure. Eles são recomendados para todos os tipos de nó de Service Fabric (primário e secundário), porque comparados aos discos do sistema operacional persistente tradicional, discos do sistema operacional efêmero:
      -  Reduzir a latência de leitura/gravação para o disco do sistema operacional
      -  Habilitar operações de gerenciamento de nó de redefinição/recriação de imagem mais rápidas
      -  Reduzir os custos gerais (os discos são gratuitos e não incorrem nenhum custo de armazenamento adicional)
- Suporte para declaração de [**certificados de ponto de extremidade de serviço de aplicativos Service Fabric por nome comum de assunto**](./service-fabric-service-manifest-resources.md).
- [**Suporte para investigações de integridade para serviços em contêineres**](./probes-codepackage.md): suporte para mecanismo de teste de vida para aplicativos em contêineres. A teste de vida ajuda a anunciar a vida do aplicativo em contêiner e quando eles não respondem em tempo hábil, isso resultará em uma reinicialização. 
- [**Suporte para pacotes de códigos inicializadores**](./initializer-codepackages.md) para [contêineres](./service-fabric-containers-overview.md) e aplicativos [executáveis de convidado](./service-fabric-guest-executables-introduction.md) . Isso permite a execução de pacotes de código (por exemplo, contêineres), em uma ordem especificada, para executar a inicialização do pacote de serviço.
- **FabricObserver e ClusterObserver** são aplicativos sem estado que capturam Service Fabric telemetria relacionada a diferentes aspectos de um cluster do it. Ambos os aplicativos estão prontos para implantação em clusters de produção do Windows para capturar telemetria avançada com suporte implementado para ApplicationInsights, EventSource e LogAnalytics.
    - [**FabricObserver (fo) 2,0**](https://github.com/microsoft/service-fabric-observer)– é executado em todos os nós, gera eventos de integridade, emite telemetria quando os limites de uso de recursos configurados pelo usuário são atingidos. Esta versão contém vários aprimoramentos em monitoramento, gerenciamento de dados, detalhes do evento de integridade, telemetria estruturada.
     - [**ClusterObserver (co) 1,1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) -executa em um nó, captura a telemetria de integridade no nível do cluster. Nesta versão, o ClusterObserver também monitora o status do nó e emite telemetria quando o nó está inativo/desabilitando/desabilitado por mais tempo do que o período especificado pelo usuário.

#### <a name="improve-application-life-cycle-experience"></a>Melhorar a experiência do ciclo de vida do aplicativo

- **[Visualização: solicitação drenada](./service-fabric-application-upgrade-advanced.md#avoid-connection-drops-during-stateless-service-planned-downtime)**: durante a manutenção planejada do serviço, como atualizações de serviço ou desativação de nó, você gostaria de permitir que os serviços esvaziassem as conexões normalmente. Esse recurso adiciona uma duração de atraso de fechamento de instância na configuração de serviço. Durante as operações planejadas, o it removerá o endereço do serviço da descoberta e, em seguida, aguardará essa duração antes de desligar o serviço.
- **[Detecção e balanceamento automáticos de subcluster](./cluster-resource-manager-subclustering.md)**: o subcluster ocorre quando os serviços com restrições de posicionamento diferentes têm uma [métrica de carga](./service-fabric-cluster-resource-manager-metrics.md)comum. Se a carga nos diferentes conjuntos de nós diferir significativamente, o Service Fabric cluster Resource Manager acredita que o cluster está desequilibrado, mesmo quando ele tem o melhor equilíbrio possível devido às restrições de posicionamento. Como resultado, ele tenta reequilibrar o cluster, potencialmente causando movimentos de serviço desnecessários (já que o "desequilíbrio" não pode ser substancialmente melhorado). A partir desta versão, o Gerenciador de recursos de cluster tentará detectar automaticamente esses tipos de configurações e entender quando o desequilíbrio pode ser corrigido por meio de movimento e, quando em vez disso, deve deixar as coisas sozinhas, já que não é possível realizar uma melhoria substancial.  
- [**Custo de movimentação diferente para réplicas secundárias**](./service-fabric-cluster-resource-manager-movement-cost.md): apresentamos o novo valor de custo de movimentação VeryHigh que fornece flexibilidade adicional em alguns cenários para definir se um custo de movimentação separado deve ser usado para réplicas secundárias.
- Mecanismo de [**teste de vida**](./probes-codepackage.md) habilitado para aplicativos em contêineres. A teste de vida ajuda a anunciar a vida do aplicativo em contêiner e quando eles não respondem em tempo hábil, isso resultará em uma reinicialização.
- [**Executar até a conclusão/uma vez para serviços**](./run-to-completion.md)**

#### <a name="image-store-improvements"></a>Aprimoramentos de Repositório de Imagens
 - Service Fabric 7,1 usa o **transporte personalizado para proteger a transferência de arquivos entre os nós por padrão**. A dependência do compartilhamento de arquivos SMB é removida da versão 7,1. Os compartilhamentos de arquivos SMB protegidos ainda existem em nós que contêm Repositório de Imagens réplica de serviço para a escolha do cliente para recusar do padrão e para atualização e downgrade para a versão antiga.
       
 #### <a name="reliable-collections-improvements"></a>Aprimoramentos de coleções confiáveis

- [**Na memória armazene apenas o suporte para serviços com estado usando coleções confiáveis**](./service-fabric-work-with-reliable-collections.md#volatile-reliable-collections): as coleções confiáveis voláteis permitem que os dados sejam persistidos em disco para durabilidade em caso de interrupções em grande escala, podem ser usados para cargas de trabalho como cache replicado, por exemplo, onde a perda de dados ocasional pode ser tolerada. Com base nas [limitações e restrições das coleções confiáveis voláteis](./service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections), recomendamos isso para cargas de trabalho que não precisam de persistência, para serviços que lidam com raras ocasiões de perda de quorum.
- Versão [**prévia: Service Fabric o Gerenciador de backup**](https://github.com/microsoft/service-fabric-backup-explorer): para facilitar o gerenciamento de backups de coleções confiáveis para Service Fabric aplicativos com estado, Service Fabric o Gerenciador de backup permite que os usuários
    - Auditar e examinar o conteúdo das coleções confiáveis,
    - Atualizar o estado atual para uma exibição consistente
    - Criar backup do instantâneo atual das coleções confiáveis
    - Corrigir corrupção de dados
                 
#### <a name="service-fabric-71-releases"></a>Versões do Service Fabric 7,1
| Data de liberação | Versão | Obter mais informações |
|---|---|---|
| 20 de abril de 2020 | [Service Fabric do Azure 7,1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [Notas de versão](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|
| 16 de junho de 2020 | [Primeira atualização do Microsoft Azure Service Fabric 7,1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-first-refresh-release/ba-p/1466517) | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU1-releasenotes.md)
| 20 de julho de 2020 | [Microsoft Azure Service Fabric segunda atualização 7,1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-second-refresh-release/ba-p/1534246) | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU2-releasenotes.md)
| 12 de agosto de 2020 | [Microsoft Azure Service Fabric terceira atualização 7,1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-third-refresh-release/ba-p/1587586) | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU3-releasenotes.md)
| 10 de setembro de 2020 | [Microsoft Azure Service Fabric quarta atualização 7,1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-fourth-refresh-release/ba-p/1653859)  | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU5-releasenotes.md)|
| 7 de outubro de 2020 | Microsoft Azure Service Fabric 7,1 sexta atualização | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU6-releasenotes.md)|
| 23 de novembro de 2020 | Microsoft Azure Service Fabric 7,1 oitava atualização | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU8-releasenotes.md)|


### <a name="service-fabric-70"></a>Service Fabric 7,0

O Azure Service Fabric 7,0 já está disponível! Você poderá atualizar para o 7,0 por meio do portal do Azure ou por meio de uma implantação de Azure Resource Manager. Devido aos comentários dos clientes sobre as versões em todo o período de Natal, não começaremos a atualizar automaticamente os clusters definidos para receber atualizações automáticas até Janeiro.
Em Janeiro, retomaremos o procedimento de distribuição padrão e os clusters com as atualizações automáticas habilitadas começarão a receber a atualização 7,0 automaticamente. Forneceremos outro comunicado antes de o lançamento começar.
Também atualizaremos nossas datas de lançamento planejadas para indicar que levamos essa política em consideração. Procure atualizações em nossos [cronogramas de lançamentos](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)futuros.

#### <a name="key-announcements"></a>Principais anúncios
 - [**Suporte KeyVaultReference para segredos do aplicativo (versão prévia)**](./service-fabric-keyvault-references.md): Service Fabric aplicativos que habilitaram [identidades gerenciadas](./concepts-managed-identity.md) agora podem fazer referência diretamente a uma URL secreta Key Vault como uma variável de ambiente, um parâmetro de aplicativo ou uma credencial de repositório de contêiner. Service Fabric resolverá automaticamente o segredo usando a identidade gerenciada do aplicativo. 
     
- **Segurança de atualização aprimorada para serviços sem estado**: para garantir a disponibilidade durante uma atualização de aplicativo, introduzimos novas configurações para definir o [número mínimo de instâncias para que os serviços sem estado](/dotnet/api/system.fabric.description.statelessservicedescription) sejam considerados disponíveis. Anteriormente, esse valor era 1 para todos os serviços e não era possível alterá-lo. Com essa nova verificação de segurança por serviço, você pode garantir que seus serviços mantenham um número mínimo de instâncias acima durante atualizações de aplicativos, atualizações de cluster e outras manutenções que se baseiam em verificações de integridade e segurança de Service Fabric.
  
- [**Limites de recursos para serviços de usuário**](./service-fabric-resource-governance.md#enforcing-the-resource-limits-for-user-services): os usuários podem configurar limites de recursos para os serviços de usuário em um nó para evitar cenários como esgotamento de recursos dos serviços do sistema Service Fabric. 
  
- [**Custo de movimentação de serviço muito alto**](./service-fabric-cluster-resource-manager-movement-cost.md) para um tipo de réplica. As réplicas com custo de movimentação muito alta serão movidas somente se houver uma violação de restrição no cluster que não possa ser corrigida de nenhuma outra maneira. Consulte o documento vinculado para obter informações adicionais sobre quando o uso de um custo de movimentação "muito alto" é razoável e para considerações adicionais.
  
-  **Verificações de segurança de cluster adicionais**: nesta versão, apresentamos uma verificação de segurança de quorum de nó de semente configurável. Isso permite que você personalize quantos nós de semente devem estar disponíveis durante cenários de gerenciamento e ciclo de vida do cluster. Operações que levam o cluster abaixo do valor configurado são bloqueadas. Hoje, o valor padrão é sempre um quorum dos nós de semente, por exemplo, se você tiver sete nós de semente, uma operação que o levaria abaixo de 5 nós de semente seria bloqueada por padrão. Com essa alteração, você pode tornar o valor de segurança mínimo 6, o que permitiria que apenas um nó de semente fique inativo por vez.
   
- Suporte adicionado para [**gerenciar o serviço de backup e restauração no Service Fabric Explorer**](./service-fabric-backuprestoreservice-quickstart-azurecluster.md). Isso torna as seguintes atividades possíveis diretamente de dentro de SFX: descoberta do serviço de backup e restauração, criação de política de backup, habilitação de backups automáticos, obtenção de backups adhoc, disparo de operações de restauração e navegação de backups existentes.

- Anunciando a disponibilidade do [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool): essa ferramenta ajuda a validar que os tipos usados em coleções confiáveis são compatíveis com versões posteriores e retroativas durante uma atualização de aplicativo sem interrupção. Isso ajuda a evitar falhas de atualização ou perda de dados e corrupção de dados devido a tipos ausentes ou incompatíveis.

- [**Habilitar leituras estáveis em réplicas secundárias**](./service-fabric-reliable-services-configuration.md#configuration-names-1): as leituras estáveis restringirão as réplicas secundárias a retornar valores que foram confirmado de quorum.

Além disso, esta versão contém outros novos recursos, correções de bugs e melhorias de desempenho, confiabilidade e suporte. Para obter a lista completa de alterações, consulte as [notas de versão](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md).

#### <a name="service-fabric-70-releases"></a>Versões do Service Fabric 7,0

| Data de liberação | Versão | Obter mais informações |
|---|---|---|
| 18 de novembro de 2019 | [Service Fabric do Azure 7,0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 30 de janeiro de 2020 | [Versão de atualização do Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 6 de fevereiro de 2020 | [Versão de atualização do Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2 de março de 2020 | [Versão de atualização do Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)
| 6 de maio de 2020 | [Azure Service Fabric 7,0 sexta versão de atualização](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-sixth-refresh-release/ba-p/1365709) | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU6-releasenotes.md)|
| 9 de outubro de 2020 | Azure Service Fabric 7,0 nona atualização de versão | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU9-releasenotes.md)|

### <a name="service-fabric-65"></a>Service Fabric 6,5

Esta versão inclui melhorias de suporte, confiabilidade e desempenho, novos recursos, correções de bugs e aprimoramentos para facilitar o gerenciamento do ciclo de vida do cluster e do aplicativo.

> [!IMPORTANT]
> Service Fabric 6,5 é a versão final com suporte a ferramentas de Service Fabric no Visual Studio 2015. Os clientes são aconselhados a mudar para o [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) no futuro.

O que há de novo no Service Fabric 6,5:

- Service Fabric Explorer inclui um [Visualizador de repositório de imagens](service-fabric-visualizing-your-cluster.md#image-store-viewer) para inspecionar aplicativos que você carregou no repositório de imagens.

- O [POA (patch Orchestration Application)](service-fabric-patch-orchestration-application.md) versão [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) inclui muitos aprimoramentos de autodiagnóstico. Os clientes do POA são recomendados para migrar para esta versão.

- O [serviço EventStore está habilitado por padrão](service-fabric-visualizing-your-cluster.md#event-store) para clusters Service Fabric 6,5, a menos que você tenha optado por você.

- [Eventos de ciclo de vida de réplica](service-fabric-diagnostics-event-generation-operational.md#replica-events) adicionados para serviços com estado.

- [Melhor visibilidade do status do nó de semente](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), incluindo avisos de nível de cluster se um nó de semente não estiver íntegro (*inativo*, *removido* ou *desconhecido*).

- [Service Fabric ferramenta de recuperação de desastres de aplicativos](https://github.com/Microsoft/Service-Fabric-AppDRTool) permite que Service Fabric serviços com estado se recuperem rapidamente quando o cluster primário encontra um desastre. Os dados do cluster primário são sincronizados continuamente no aplicativo em espera secundário usando backup e restauração periódicos.

- Suporte do Visual Studio para [publicação de aplicativos .NET Core em clusters baseados em Linux](service-fabric-how-to-publish-linux-app-vs.md).

- A [CLI do Service Fabric do Azure (SFCTL)](./service-fabric-cli.md) será instalada automaticamente para o Service Fabric 6,5 (e versões posteriores) quando você atualizar ou criar um novo cluster do Linux no Azure.

- O [SFCTL](./service-fabric-cli.md) é instalado por padrão em clusters Onebox do MacOS/Linux.

Para obter mais detalhes, consulte as [notas de versão do Service Fabric 6,5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

#### <a name="service-fabric-65-releases"></a>Versões do Service Fabric 6,5

| Data de liberação | Versão | Obter mais informações |
|---|---|---|
| 11 de junho de 2019 | [Service Fabric do Azure 6,5](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 de julho de 2019 | [Versão de atualização do Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 de julho de 2019 | [Versão de atualização do Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Aug 23, 2019 | [Versão de atualização do Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14 de outubro de 2019 | [Versão de atualização do Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Notas de versão] (https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |


### <a name="service-fabric-64-releases"></a>Versões do Service Fabric 6,4

| Data de liberação | Versão | Obter mais informações |
|---|---|---|
| 30 de novembro de 2018 | [Service Fabric do Azure 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 de dezembro de 2018 | [Versão de atualização do Azure Service Fabric 6,4 para clusters do Windows](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 de fevereiro de 2019 | [Versão de atualização do Azure Service Fabric 6,4](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 de março de 2019 | [Versão de atualização do Azure Service Fabric 6,4](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 de abril de 2019 | [Versão de atualização do Azure Service Fabric 6,4](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 de maio de 2019 | [Versão de atualização do Azure Service Fabric 6,4](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 de maio de 2019 | [Versão de atualização do Azure Service Fabric 6,4](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)