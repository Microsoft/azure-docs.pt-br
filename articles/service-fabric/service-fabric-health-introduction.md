---
title: Monitoramento de integridade do Service Fabric | Microsoft Docs
description: "Uma introdução ao modelo de Monitoramento de integridade do Service Fabric do Azure, que fornece monitoramento do cluster e seus aplicativos e serviços."
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: 
ms.assetid: 1d979210-b1eb-4022-be24-799fd9d8e003
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/19/2017
ms.author: oanapl
ms.openlocfilehash: 330ef58d89ebabaa2af7fa8e98e693ddd64dcc4e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-service-fabric-health-monitoring"></a>Introdução ao monitoramento da integridade do Service Fabric
O Service Fabric do Azure introduz um modelo de integridade que fornece avaliação e relatório de integridade avançados, flexíveis e extensíveis. O modelo permite o monitoramento do estado quase em tempo real do cluster e dos serviços que são executados nele. Você pode obter as informações sobre integridade facilmente e corrigir possíveis problemas antes que eles se espalhem e causem interrupções massivas. No modelo comum, os serviços enviam relatórios com base na respectiva exibição local e as informações são agregadas para fornecer uma exibição geral no nível de cluster.

Os componentes do Service Fabric usam esse modelo de integridade avançado para reportar o respectivo estado atual. É possível usar o mesmo mecanismo para reportar a integridade de seus aplicativos. Se investir em relatórios de integridade de alta qualidade que capturam suas condições personalizadas, você poderá detectar e corrigir problemas do seu aplicativo em execução com muito mais facilidade.

O vídeo do Microsoft Virtual Academy a seguir também descreve o modelo de integridade do Service Fabric e seu uso: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tevZw56yC_1906218965">
<img src="./media/service-fabric-health-introduction/HealthIntroVid.png" WIDTH="360" HEIGHT="244">
</a></center>

> [!NOTE]
> Iniciamos o subsistema de integridade para atender à necessidade de atualizações monitoradas. O Service Fabric fornece aplicativos monitorados e atualizações de cluster que garantem a disponibilidade completa, sem tempo de inatividade e mínima ou nenhuma intervenção do usuário. Para atingir essas metas, a atualização verifica a integridade com base nas políticas de atualização configuradas. Uma atualização pode continuar somente quando a integridade respeita os limites desejados. Caso contrário, a atualização é automaticamente revertida ou pausada para dar aos administradores a oportunidade de corrigir os problemas. Para saber mais sobre atualizações de aplicativo, consulte [este artigo](service-fabric-application-upgrade.md).
> 
> 

## <a name="health-store"></a>Repositório de Integridade
O Repositório de Integridade mantém informações relacionadas à integridade sobre entidades no cluster para facilidade de recuperação e avaliação. Ele é implementado como um serviço com estado persistente da Malha do Serviço, garantindo alta disponibilidade e escalabilidade. O Repositório de Integridade faz parte do aplicativo **fabric:/System** e é disponibilizado quando o cluster é ativado e está em execução.

## <a name="health-entities-and-hierarchy"></a>Hierarquia e entidades de integridade
As entidades de integridade são organizadas em uma hierarquia lógica que captura as interações e dependências entre diferentes entidades. O repositório de integridade cria automaticamente entidades e hierarquia com base nos relatórios recebidos dos componentes do Service Fabric.

As entidades de integridade espelham as entidades do Service Fabric. (Por exemplo, a **entidade de aplicativo de integridade** corresponde a uma instância do aplicativo implantada no cluster, enquanto a **entidade de nó de integridade** corresponde a um nó de cluster do Service Fabric). A hierarquia de integridade captura as interações das entidades do sistema e é a base para a avaliação avançada de integridade. Você pode aprender sobre os principais conceitos do Service Fabric em [Visão geral técnica do Service Fabric](service-fabric-technical-overview.md). Para saber mais sobre aplicativos, confira [Modelo de aplicativo do Service Fabric](service-fabric-application-model.md).

As entidades de integridade e a hierarquia proporcionam relatório, depuração e monitoramento efetivos do cluster e dos aplicativos. O modelo de integridade oferece uma representação precisa e *granular* da integridade das muitas partes móveis do cluster.

![Entidades de integridade.][1]
As entidades de integridade, organizadas em uma hierarquia com base nas relações de pai/filho.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

As entidades de integridade são:

* **Cluster**. Representa a integridade de um cluster da Malha do Serviço. Os relatórios de integridade do cluster descrevem condições que afetam todo o cluster. Essas condições afetam várias entidades no cluster ou o próprio cluster. Com base na condição, o gerador de relatórios não consegue restringir o problema para um ou mais filhos não íntegros. O exemplo inclui a personalidade do cluster sendo dividida devido ao particionamento da rede ou problemas de comunicação.
* **Nó**. Representa a integridade de um nó da Malha do Serviço. Os relatórios de integridade do nó descrevem condições que afetam toda a funcionalidade do nó. Normalmente, elas afetam todas as entidades implantadas em execução. Os exemplos incluem um nó sem espaço em disco (ou outras propriedades em todo o computador, como memória, conexões) ou quando o nó está desativado. A entidade de nó é identificada pelo nome do nó (cadeia de caracteres).
* **Aplicativo**. Representa a integridade de uma instância do aplicativo em execução no cluster. Os relatórios de integridade do aplicativo descrevem condições que afetam a integridade geral do aplicativo. Elas não podem ser limitadas a filhos individuais (serviços ou aplicativos implantados). Os exemplos incluem a interação de ponta a ponta entre diferentes serviços no aplicativo. A entidade de aplicativo é identificada pelo nome do aplicativo (URI).
* **Serviço**. Representa a integridade de um serviço em execução no cluster. Os relatórios de integridade do serviço descrevem condições que afetam a integridade geral do serviço. O gerador de relatórios não consegue restringir o problema para uma partição ou réplica não íntegras. Os exemplos incluem uma configuração de serviço (como compartilhamento de arquivos externos ou porta) que está causando problemas em todas as partições. A entidade de serviço é identificada pelo nome do serviço (URI).
* **Partição**. Representa a integridade de uma partição de serviço. Os relatórios de integridade da partição descrevem condições que afetam todo o conjunto de réplicas. Os exemplos incluem quando o número de réplicas está abaixo da contagem de destino e quando a partição está na perda do quórum. A entidade de partição é identificada pela ID da partição (GUID).
* **Réplica**. Representa a integridade de uma réplica de serviço com estado ou uma instância de serviço sem estado. A réplica é menor unidade que os watchdogs e os componentes do sistema podem reportar para um aplicativo. Para serviços com estado, os exemplos incluem uma réplica primária que não consegue replicar operações em locais secundários e replicação lenta. Além disso, uma instância sem estado pode relatar quando estiver em execução sem recursos ou tiver problemas de conectividade. A entidade de réplica é identificada pela ID da partição (GUID) e pela ID de réplica ou instância (longo).
* **DeployedApplication**. Representa a integridade de um *aplicativo que é executado em um nó*. Os relatórios de integridade do aplicativo implantado descrevem condições específicas ao aplicativo no nó que não podem ser limitados aos pacotes de serviço implantados no mesmo nó. Os exemplos incluem erros quando o pacote de aplicativos não pode ser baixado nesse nó e problemas na configuração das entidades de segurança do aplicativo no nó. O aplicativo implantado é identificado pelo nome do aplicativo (URI) e pelo nome do nó (cadeia de caracteres).
* **DeployedServicePackage**. Representa a integridade de um pacote de serviço em execução em um nó no cluster. Descreve as condições específicas a um pacote de serviço que não afetam os outros pacotes de serviço no mesmo nó para o mesmo aplicativo. Os exemplos incluem um pacote de códigos no pacote de serviço que não pode ser iniciado e um pacote de configuração que não pode ser lido. O pacote de serviço implantado é identificado pelo nome do aplicativo (URI), o nome do nó (cadeia de caracteres), o nome do manifesto do serviço (cadeia de caracteres) e a ID de ativação do pacote de serviço (cadeia de caracteres).

A granularidade do modelo de integridade facilita a detecção e correção dos problemas. Por exemplo, se um serviço não estiver respondendo, é possível relatar que a instância do aplicativo não está íntegra. No entanto, a emissão de relatórios neste nível não é ideal porque o problema talvez não esteja afetando todos os serviços no aplicativo. O relatório deve ser aplicado no serviço não íntegro ou partição filha específica, se mais informações apontarem para essa partição. Os dados emergem automaticamente pela hierarquia e uma partição não íntegra fica visível nos níveis de serviço e aplicativo. Essa agregação ajuda a identificar e resolver a causa raiz do problema mais rapidamente.

A hierarquia de integridade é composta por relações de pai/filho. Um cluster é composto de nós e aplicativos. Os aplicativos possuem serviços e aplicativos implantados. Os aplicativos implantados possuem pacotes de serviço implantados. Os serviços têm partições, e cada partição tem uma ou mais réplicas. Há uma relação especial entre nós e entidades implantadas. Um nó não íntegro conforme reportado por seu componente de sistema de autoridade, o Serviço do Gerenciador de Failover, afeta os aplicativos implantados, os pacotes de serviço e as réplicas implantadas nele.

A hierarquia de integridade representa o estado mais recente do sistema com base em relatórios de integridade mais recentes, que é uma informação quase em tempo real.
Os watchdogs internos e externos podem reportar as mesmas entidades com base na lógica específica do aplicativo ou nas condições monitoradas personalizadas. Os relatórios do usuário coexistem com os relatórios do sistema.

Planeje para investir em como relatar e responder à integridade durante o criação de um serviço de nuvem grande. Este investimento inicial facilita a depuração, monitoramento e operação do serviço.

## <a name="health-states"></a>Estados de integridade
O Service Fabric usa três estados de integridade para descrever se uma entidade está íntegra ou não: OK, Aviso e Erro. Qualquer relatório enviado ao Repositório de Integridade deve especificar um desses estados. O resultado da avaliação de integridade é um desses estados.

Os [estados de integridade](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate) possíveis são:

* **OK**. A entidade está íntegra. Não há nenhum problema conhecido reportado em seus filhos (quando aplicável).
* **Aviso**. A entidade tem alguns problemas, mas ainda funciona corretamente. Por exemplo, há atrasos, mas eles ainda não causam problemas funcionais. Em alguns casos, a condição de aviso pode corrigir-se sozinha sem intervenção externa. Nesses casos, os relatórios de integridade aumentam o reconhecimento e fornecem visibilidade sobre o que está acontecendo. Em outros casos, a condição de Aviso pode se transformar em um problema grave sem intervenção do usuário.
* **Erro**. A entidade não está íntegra. Uma medida deve ser tomada para corrigir o estado da entidade, pois ela não funciona corretamente.
* **Desconhecido**. A entidade não existe no repositório de integridade. Esse resultado pode ser obtido em consultas distribuídas que mesclam resultados de vários componentes. Por exemplo, a consulta para obter a lista de nós vai para **FailoverManager** e **ClusterManager** e **HealthManager** e a consulta para obter a lista de aplicativos vai para **ClusterManager** e **HealthManager**. Essas consultas mesclam os resultados de vários componentes do sistema. Se outro componente do sistema retornar uma entidade que não está presente no repositório de integridade, o resultado mesclado terá um estado de integridade desconhecido. Uma entidade não está no repositório porque os relatórios de integridade ainda não foram processados ou a entidade foi apagada após a exclusão.

## <a name="health-policies"></a>Políticas de integridade
O Repositório de Integridade aplica políticas de integridade para determinar se uma entidade está íntegra com base em seus relatórios e filhos.

> [!NOTE]
> As políticas de integridade podem ser especificadas no manifesto do cluster (para avaliação de integridade do cluster e do nó) ou no manifesto do aplicativo (para avaliação de aplicativo e qualquer um de seus filhos). As solicitações de avaliação de integridade também podem passar pelas políticas de avaliação de integridade personalizadas, que são usadas apenas para avaliação.
> 
> 

Por padrão, o Service Fabric aplica regras rígidas (tudo deve estar íntegro) para a relação hierárquica pai-filho. Se mesmo um dos filhos tem um evento não íntegro, o pai será considerado não íntegro.

### <a name="cluster-health-policy"></a>Política de integridade do cluster
A [política de integridade do cluster](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy) é usada para avaliar o estado de integridade do cluster e os estados de integridade do nó. A política pode ser definida no manifesto do cluster. Se não estiver presente, a política padrão (zero falhas toleradas) é usada.
A política de integridade do cluster contém:

* [ConsiderWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Especifica se os relatórios de integridade Aviso devem ser tratados como erros durante a avaliação de integridade. Padrão: falso.
* [MaxPercentUnhealthyApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications). Especifica a porcentagem máxima tolerada de aplicativos que podem estar não íntegros antes de o cluster ser considerado com erro.
* [MaxPercentUnhealthyNodes](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes). Especifica a porcentagem máxima tolerada de nós que podem estar não íntegros antes de o cluster ser considerado com erro. Em clusters grandes, sempre há nós desativados ou ausentes para reparo, de modo que esse percentual deve ser configurado para tolerá-los.
* [ApplicationTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap). O mapa da política de integridade do tipo de aplicativo pode ser usado durante a avaliação da integridade do cluster para descrever tipos especiais de aplicativo. Por padrão, todos os aplicativos são colocados em um pool e avaliados com MaxPercentUnhealthyApplications. Se alguns tipos de aplicativos tiverem que ser tratados de forma diferente, eles poderão ser criados fora do pool global. Em vez disso, eles são avaliados em relação às porcentagens associadas com seu nome de tipo de aplicativo no mapa. Por exemplo, em um cluster, há milhares de aplicativos de diferentes tipos e algumas instâncias de aplicativo de controle de um tipo especial de aplicativo. Os aplicativos de controle nunca deve apresentar erro. Você pode especificar MaxPercentUnhealthyApplications global como 20% para tolerar algumas falhas, mas para o tipo de aplicativo "ControlApplicationType", defina MaxPercentUnhealthyApplications como 0. Dessa forma, se alguns dos muitos aplicativos estiverem em estado não íntegro, mas abaixo da porcentagem de não integridade global, o estado do cluster será Aviso. Um estado de integridade de aviso não afeta a atualização do cluster nem outros recursos de monitoramento disparados pelo estado de integridade Erro. Mas, até mesmo um controle de aplicativo em erro poderia tornar o cluster não íntegro, que dispara a reversão ou pausa a atualização de cluster, dependendo da configuração de atualização.
  Para os tipos de aplicativo definidos no mapa, todas as instâncias do aplicativo são retiradas do pool global de aplicativos. Eles são avaliados com base no número total de aplicativos do tipo de aplicativo, usando MaxPercentUnhealthyApplications específico do mapa. O restante dos aplicativos permanece no pool global e é avaliado com MaxPercentUnhealthyApplications.

O exemplo a seguir é um trecho de um manifesto do cluster. Para definir as entradas no mapa do tipo de aplicativo, prefixe o nome do parâmetro com “ApplicationTypeMaxPercentUnhealthyApplications-”, seguido do nome do tipo de aplicativo.

```xml
<FabricSettings>
  <Section Name="HealthManager/ClusterHealthPolicy">
    <Parameter Name="ConsiderWarningAsError" Value="False" />
    <Parameter Name="MaxPercentUnhealthyApplications" Value="20" />
    <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
    <Parameter Name="ApplicationTypeMaxPercentUnhealthyApplications-ControlApplicationType" Value="0" />
  </Section>
</FabricSettings>
```

### <a name="application-health-policy"></a>Política de integridade do aplicativo
A [política de integridade do aplicativo](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy) descreve como a avaliação da agregação dos estados de eventos e filhos é feita para aplicativos e seus filhos. Ela pode ser definida no manifesto do aplicativo, **ApplicationManifest.xml**, no pacote de aplicativos. Se nenhuma política for especificada, o Service Fabric suporá que a entidade não está íntegra se ela tiver um relatório de integridade ou um filho no estado de integridade com erro ou aviso.
As políticas configuráveis são:

* [ConsiderWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.considerwarningaserror.aspx). Especifica se os relatórios de integridade Aviso devem ser tratados como erros durante a avaliação de integridade. Padrão: falso.
* [MaxPercentUnhealthyDeployedApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications). Especifica a porcentagem máxima tolerada de aplicativos implantados que podem estar não íntegros antes de o aplicativo ser considerado com erro. Este percentual é calculado pela divisão do número de aplicativos implantados não íntegros pelo número de nós em que os aplicativos estão atualmente implantados no cluster. O cálculo é arredondado para cima para tolerar uma falha em um número pequeno de nós. Porcentagem padrão: zero.
* [DefaultServiceTypeHealthPolicy](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy). Especifica a política de integridade do tipo de serviço padrão, que substitui a política de integridade padrão para todos os tipos de serviço no aplicativo.
* [ServiceTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap). Fornece um mapa de políticas de integridade do serviço por tipo de serviço. Estas políticas substituem as políticas de integridade do tipo de serviço padrão para cada tipo de serviço especificado. Por exemplo, se um aplicativo tiver um tipo de serviço de gateway sem estado e um tipo de serviço de mecanismo com estado, você poderá configurar as políticas de integridade da avaliação de maneira diferente. Ao especificar a política por tipo de serviço você permite um controle mais granular da integridade do serviço.

### <a name="service-type-health-policy"></a>Políticas de integridade do tipo de serviço
A [política de integridade do tipo de serviço](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy) especifica como avaliar e agregar os serviços e os filhos dos serviços. A política contém:

* [MaxPercentUnhealthyPartitionsPerService](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice). Especifica a porcentagem máxima tolerada de partições não íntegras antes de um serviço ser considerado não íntegro. Porcentagem padrão: zero.
* [MaxPercentUnhealthyReplicasPerPartition](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition). Especifica a porcentagem máxima tolerada de réplicas não íntegras antes de uma partição ser considerada não íntegro. Porcentagem padrão: zero.
* [MaxPercentUnhealthyServices](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices). Especifica a porcentagem máxima tolerada de serviços não íntegros antes de um aplicativo ser considerado não íntegro. Porcentagem padrão: zero.

O exemplo a seguir é um trecho de um manifesto do aplicativo:

```xml
    <Policies>
        <HealthPolicy ConsiderWarningAsError="true" MaxPercentUnhealthyDeployedApplications="20">
            <DefaultServiceTypeHealthPolicy
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="10"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="FrontEndServiceType"
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="20"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="BackEndServiceType"
                   MaxPercentUnhealthyServices="20"
                   MaxPercentUnhealthyPartitionsPerService="0"
                   MaxPercentUnhealthyReplicasPerPartition="0">
            </ServiceTypeHealthPolicy>
        </HealthPolicy>
    </Policies>
```

## <a name="health-evaluation"></a>Avaliação da integridade
Os usuários ou serviços automatizados podem avaliar a integridade de qualquer entidade a qualquer momento. Para avaliar a integridade de uma entidade, o Repositório de Integridade agrega todos os relatórios de integridade na entidade e avalia todos os seus filhos (quando aplicável). O algoritmo de agregação de integridade usa políticas de integridade que especificam como avaliar os relatórios de integridade e como agregar estados de integridade dos filhos (quando aplicável).

### <a name="health-report-aggregation"></a>Agregação do relatório de integridade
Uma entidade pode ter vários relatórios de integridade enviados por diferentes relatores (componentes do sistema ou watchdogs) em propriedades diferentes. A agregação usa as políticas de integridade associadas, especificamente o membro ConsiderWarningAsError da política de integridade do aplicativo ou cluster. ConsiderWarningAsError especifica como avaliar os avisos.

O estado de integridade agregada é disparado pelos *piores* relatórios de integridade da entidade. Se houver pelo menos um relatório de integridade com Erro, o estado de integridade agregada será um erro.

![Agregação de relatório de integridade com o relatório de erro.][2]

Uma entidade de integridade que tem um ou mais relatórios de integridade de erro é avaliada como Erro. O mesmo é verdadeiro para um relatório de integridade expirado, independentemente do seu estado de integridade.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Se não houver relatórios de erro, e um ou mais avisos, o estado de integridade agregada será aviso ou erro, dependendo do sinalizador de política ConsiderWarningAsError.

![Agregação do relatório de integridade com o relatório de aviso e ConsiderWarningAsError false.][3]

A agregação do relatório de integridade com relatório de aviso e ConsiderWarningAsError definido como falso (padrão).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Agregação de integridade de filhos
O estado de integridade agregada de uma entidade reflete os estados de integridade dos filhos (quando aplicável). O algoritmo para agregar estados de integridade dos filhos usa as políticas aplicáveis de integridade com base no tipo de entidade.

![Agregação de integridade de entidades filho.][4]

Agregação de filhos com base nas políticas de integridade.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Depois que o Repositório de Integridade tiver avaliado todos os filhos, ele agregará seus estados de integridade com base no percentual máximo configurado de filhos não íntegros. Este percentual é obtido da política com base no tipo de entidade e filho.

* Se todos os filhos tiverem estados OK, o estado de integridade agregada dos filhos será OK.
* Se todos os filhos tiverem estados OK e aviso, o estado de integridade agregada dos filhos é um aviso.
* Se houver filhos com estados de erro que não respeitarem a porcentagem máxima permitida de filhos não íntegros, o estado de integridade agregada é um erro.
* Se os filhos com estados de erro respeitarem a porcentagem máxima permitida de filhos não íntegros, o estado de integridade agregada é um aviso.

## <a name="health-reporting"></a>Relatório de integridade
Os componentes do sistema, os aplicativos do System Fabric e os watchdogs internos/externos podem ser relatados em relação às entidades do Service Fabric. Os relatores fazem uma determinação *local* da integridade da entidades monitoradas com base em algumas condições que estão monitorando. Eles não precisam observar nenhum estado global ou dados agregados. O comportamento desejado é ter relatores simples e organismos não complexos que precisariam observar muitas circunstâncias para inferir quais informações enviar.

Para enviar dados de integridade ao repositório de integridade, o relator precisa identificar a entidade afetada e criar um relatório de integridade. Para enviar o relatório, use a API [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth), relate as APIs de integridade expostos nos objetos `Partition` ou `CodePackageActivationContext`, os cmdlets do PowerShell ou REST.

### <a name="health-reports"></a>Relatórios de integridade
Os [relatórios de integridade](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthreport) de cada uma das entidades no cluster contêm as seguintes informações:

* **SourceId**. Uma cadeia de caracteres que identifica exclusivamente o relator do evento de integridade.
* **Identificador de entidade**. Identifica a entidade na qual o relatório é aplicado. Varia de acordo com o [tipo de entidade](service-fabric-health-introduction.md#health-entities-and-hierarchy):
  
  * Cluster. Nenhuma.
  * Nó. Nome do nó (cadeia de caracteres).
  * Console. Nome do aplicativo (URI). Representa o nome da instância do aplicativo implantado no cluster.
  * Serviço. Nome do serviço (URI). Representa o nome da instância do serviço implantado no cluster.
  * Partição. ID da partição (GUID). Representa o identificador exclusivo da partição.
  * Réplica. A ID de réplica do serviço com estado ou a ID da instância do serviço sem estado (INT64).
  * DeployedApplication. Nome do aplicativo (URI) e o nome do nó (cadeia de caracteres).
  * DeployedServicePackage. Nome do aplicativo (URI), nome do nó (cadeia de caracteres) e nome do manifesto do serviço (cadeia de caracteres).
* **Property**. Um *cadeia de caracteres* (não uma enumeração fixa) que permite ao relator classificar o evento de integridade para uma propriedade específica da entidade. Por exemplo, o relator A pode relatar a integridade da propriedade “Storage” de Node01 e o relator B pode relatar a integridade na propriedade “Connectivity” de Node01. No Repositório de Integridade, esses relatórios são tratados como eventos de integridade distintos para a entidade Node01.
* **Description**. Uma cadeia de caracteres que permite ao relator fornecer informações detalhadas sobre o evento de integridade. **SourceId**, **Property** e **HealthState** devem descrever o relatório por completo. A descrição adiciona informações legíveis sobre o relatório. O texto facilita para que os administradores e os usuários entendam o relatório de integridade.
* **HealthState**. Uma [enumeração](service-fabric-health-introduction.md#health-states) que descreve o estado de integridade do relatório. Os valores aceitáveis são OK, Aviso e Erro.
* **TimeToLive**. Um período de tempo que indica por quanto tempo o relatório de integridade é válido. Em conjunto com **RemoveWhenExpired**, permite que o repositório de integridade saiba como avaliar eventos expirados. Por padrão, o valor é infinito e o relatório é válido indefinidamente.
* **RemoveWhenExpired**. Um valor booleano. Se definido como verdadeiro, o relatório de integridade expirado será removido automaticamente do Repositório de Integridade e o relatório não afetará a avaliação da integridade da entidade. Usado quando o relatório for válido apenas por um período especificado de tempo e o relator não precisar removê-lo explicitamente. Ele também é usado para excluir relatórios do repositório de integridade (por exemplo, um watchdog é alterado e interrompe o envio de relatórios com propriedade e origem anteriores). Portanto, ele pode enviar um relatório com TimeToLive curto e RemoveWhenExpired para eliminar qualquer estado anterior do Repositório de Integridade. Se o valor for definido como false, o relatório expirado será tratado como erro na avaliação de integridade. O valor false indica ao repositório de integridade que a origem deve relatar periodicamente essa propriedade. Caso contrário, deve haver algo errado com o watchdog. A integridade do watchdog é capturada ao considerar o evento como um erro.
* **SequenceNumber**. Um inteiro positivo que sempre precisa ser aumentado, pois ele representa a ordem dos relatórios. Ele é usado pelo Repositório de Integridade para detectar relatórios obsoletos que são recebidos em atraso devido a atrasos na rede ou outros problemas. Um relatório é rejeitado se o número da sequência for menor ou igual ao aplicado mais recentemente para a mesma entidade, origem e propriedade. Se não for especificado, o número de sequência é gerado automaticamente. É necessário colocar o número de sequência apenas ao relatar transições de estado. Nessa situação, a origem precisa lembrar quais relatórios enviou e manter as informações de recuperação no failover.

Essas quatro informações: SourceId, identificador da entidade, Propriedade e HealthState são obrigatórios para cada relatório de integridade. A cadeia de caracteres SourceId não pode iniciar com o prefixo "**System.**", que é reservado para relatórios do sistema. Para a mesma entidade, há apenas um relatório para a mesma origem e propriedade. Vários relatórios para a mesma fonte e propriedade substituirão uns aos outros, seja no lado do cliente de integridade (se estiverem divididos em lotes), seja no lado do Repositório de Integridade. A substituição é baseada nos números da sequência: relatórios mais recentes (com número de sequência mais alto) substituem os relatórios mais antigos.

### <a name="health-events"></a>Eventos de integridade
Internamente, o Repositório de Integridade mantém os [eventos de integridade](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthevent), que contêm todas as informações de relatórios e metadados adicionais. Os metadados incluem a hora em que o relatório foi fornecido ao cliente de integridade e a hora em que ele foi modificado no lado do servidor. Os eventos de integridade são retornados pelas [consultas de integridade](service-fabric-view-entities-aggregated-health.md#health-queries).

Os metadados adicionados contêm:

* **SourceUtcTimestamp**. A hora que o relatório foi fornecido ao cliente de integridade (Coordinated Universal Time)
* **LastModifiedUtcTimestamp**. A hora em que o relatório foi modificado pela última vez no lado do servidor (Coordinated Universal Time).
* **IsExpired**. Um sinalizador para indicar se o relatório estava expirado no momento que a consulta foi executada pelo Repositório de Integridade. Um evento pode ter expirado somente se RemoveWhenExpired for false. Caso contrário, o evento não é retornado pela consulta e é removido do armazenamento.
* **LastOkTransitionAt**, **LastWarningTransitionAt**, **LastErrorTransitionAt**. A última hora para transições OK/aviso/erro. Esses campos fornecem o histórico das transições de estado de integridade para o evento.

Os campos de transição de estado podem ser usados para gerar alertas mais inteligentes ou informações “históricas” de evento de integridade. Eles permitem cenários como:

* Alertar quando uma propriedade estiver no estado aviso/erro por mais de X minutos. A verificação da condição para um período de tempo evita alertas em condições temporárias. Por exemplo, um alerta se o estado da integridade estiver em aviso por mais de 5 minutos pode ser traduzido em (HealthState == Aviso e Agora - LastWarningTransitionTime > 5 minutos).
* Alertar apenas em condições que mudaram nos últimos X minutos. Se um relatório estiver em estado de erro desde antes da hora especificada, ele poderá ser ignorado pois já tinha sido sinalizado anteriormente.
* Se uma propriedade estiver alternando entre aviso e erro, determine por quanto tempo ele esteve não íntegro (isto é, não OK). Por exemplo, um alerta se a propriedade não esteve íntegra por mais de 5 minutos pode ser traduzido em: (HealthState != Ok e Agora - LastOkTransitionTime > 5 minutos).

## <a name="example-report-and-evaluate-application-health"></a>Exemplo: relatar e avaliar integridade do aplicativo
O exemplo a seguir envia um relatório de integridade por meio PowerShell no aplicativo **fabric:/WordCount** da origem **MyWatchdog**. O relatório de integridade contém informações sobre a propriedade de integridade "availability" em um estado de integridade de erro, com TimeToLive infinito. Em seguida, ele consulta a integridade do aplicativo, que retornará erro do estado de integridade agregada e o evento de integridade relatado como parte da lista de eventos de integridade.

```powershell
PS C:\> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Error event: SourceId='MyWatchdog', Property='Availability'.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 131032204762818013
                                  SentAt                : 3/23/2016 3:27:56 PM
                                  ReceivedAt            : 3/23/2016 3:27:56 PM
                                  TTL                   : Infinite
                                  Description           :
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Ok->Error = 3/23/2016 3:27:56 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="health-model-usage"></a>Uso do modelo de integridade
O modelo de integridade permite que os serviços de nuvem e a plataforma subjacente do Service Fabric ajustem a escala, pois o monitoramento e a determinação da integridade são distribuídos entre os diferentes monitores no cluster.
Outros sistemas têm um único serviço centralizado no nível de cluster, que analisa todas as informações *potencialmente* úteis emitidas pelos serviços. Essa abordagem impede a escalabilidade dos mesmos. Isso também impede que eles coletem informações específicas que ajudam a identificar problemas e problemas potenciais mais próximos possíveis da causa raiz.

O modelo de integridade é muito usado para monitoramento e diagnóstico, para avaliar a integridade do cluster e do aplicativo, bem como para atualizações monitoradas. Outros serviços usam os dados de integridade para executar reparos automáticos, para criar histórico de integridade do cluster e para emitir alertas em determinadas condições.

## <a name="next-steps"></a>Próximas etapas
[Como exibir relatórios de integridade do Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Usar relatórios de integridade do sistema para solução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Como relatar e verificar a integridade do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Adicionar relatórios de integridade personalizados do Service Fabric](service-fabric-report-health.md)

[Monitorar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização de aplicativos do Service Fabric](service-fabric-application-upgrade.md)

