---
title: Tipos de recurso com suporte por meio do Azure Resource Health | Microsoft Docs
description: Tipos de recurso com suporte por meio do Azure Resource Health
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 1b0958732a96786b2fb86b2b145f5cb8d36c0c5a
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97883360"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Tipos de recursos e verificações de integridade no Azure Resource Health
Abaixo, temos uma lista completa de todas as verificações executadas por meio do Resource Health segundo o tipo do recurso.

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers
|Verificações executadas|
|---|
|<ul><li>O servidor está em funcionamento?</li><li>O servidor está funcionando fora da memória?</li><li>O servidor está iniciando?</li><li>O servidor está recuperando?</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Verificações executadas|
|---|
|<ul><li>O serviço de Gerenciamento de Api está ativo e em funcionamento?</li></ul>|

## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring
|Verificações executadas|
|---|
|<ul><li>A instância do Azure Spring Cloud está disponível?</li></ul>|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts
|Verificações executadas|
|---|
|<ul><li>A conta do Lote está em execução?</li><li>A cota do pool foi excedida para esta conta do lote?</li></ul>|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/Redis
|Verificações executadas|
|---|
|<ul><li>Todos os nós do Cache estão em funcionamento?</li><li>É possível alcançar o Cache de dentro do datacenter?</li><li>O Cache atingiu o número máximo de conexões?</li><li> O Cache esgotou a memória disponível? </li><li>O Cache está apresentando um alto número de falhas de página?</li><li>O Cache está sob uma carga pesada?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|Verificações executadas|
|---|
|<ul> <li>O portal complementar pode ser acessado para operações de configuração da CDN?</li><li>Há problemas contínuos de entrega com os pontos de extremidade da CDN?</li><li>Os usuários podem alterar a configuração de seus recursos da CDN?</li><li>Alterações de configuração estão sendo propagadas com a velocidade esperada?</li><li>Os usuários podem gerenciar a configuração da CDN usando o PowerShell, a API ou o Portal do Azure?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Verificações executadas|
|---|
|<ul><li>O servidor host está em funcionamento?</li><li>A inicialização do SO host foi concluída?</li><li>O contêiner da máquina virtual está provisionado e ligado?</li><li>Há conectividade de rede entre o host e a conta de armazenamento?</li><li>A inicialização do SO convidado foi concluída?</li><li>Há manutenção planejada contínua?</li><li>O hardware do host foi degradado e previsto para falhar em breve?</li></ul>|

## <a name="microsoftclassiccomputedomainnames"></a>Microsoft. classiccompute/nome_do_domínio
|Verificações executadas|
|---|
|<ul><li>A implantação do slot de produção está íntegra em todas as instâncias de função?</li><li>A função está íntegra em todas as suas instâncias de VM?</li><li>Qual é o status de integridade de cada VM em uma função de um serviço de nuvem?</li><li>O status da VM foi alterado devido à operação iniciada pela plataforma ou pelo cliente?</li><li>A inicialização do SO convidado foi concluída?</li><li>Há manutenção planejada contínua?</li><li>O hardware do host foi degradado e previsto para falhar em breve?</li><li>[Saiba mais](../cloud-services/resource-health-for-cloud-services.md) sobre as verificações executadas</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|Verificações executadas|
|---|
|<ul><li>É possível alcançar a conta de dentro do datacenter?</li><li>O provedor de recursos dos Serviços Cognitivos está disponível?</li><li>O Serviço Cognitivo está disponível na região apropriada?</li><li>Operações de leitura podem ser executadas na conta de armazenamento que contém os metadados do recurso?</li><li>A cota de chamadas à API foi atingida?</li><li>O limite de leitura de chamadas à API foi atingido?</li></ul>|

## <a name="microsoftcomputehostgroupshosts"></a>Microsoft.compute/hostgroups/hosts
|Verificações executadas|
|---|
|<ul><li>O host está em funcionamento?</li><li>O hardware do host está degradado?</li><li>O host está desalocado?</li><li>O serviço de hardware do host foi reparado para hardware diferente?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|Verificações executadas|
|---|
|<ul><li>O servidor que hospeda a máquina virtual está em funcionamento?</li><li>A inicialização do SO host foi concluída?</li><li>O contêiner da máquina virtual está provisionado e ligado?</li><li>Há conectividade de rede entre o host e a conta de armazenamento?</li><li>A inicialização do SO convidado foi concluída?</li><li>Há manutenção planejada contínua?</li><li>O hardware do host foi degradado e previsto para falhar em breve?</li></ul>|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters
|Verificações executadas|
|---|
|<ul><li>O cluster está em funcionamento?</li><li>Os serviços principais estão disponíveis no cluster?</li><li>Todos os nós de cluster estão prontos?</li><li>A entidade de serviço é atual e válida?</li></ul>|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.datafactory/factories
|Verificações executadas|
|---|
|<ul><li>Houve falhas de execução de pipeline?</li><li>O cluster está hospedando o Data Factory íntegro?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|Verificações executadas|
|---|
|<ul><li>Os usuários tiveram problemas ao enviar ou listar seus trabalhos do Data Lake Analytics?</li><li>As tarefas do Data Lake Analytics não podem ser concluídas devido a erros do sistema?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|Verificações executadas|
|---|
|<ul><li>Os usuários tiveram problemas para fazer o upload de dados para o Data Lake Store?</li><li>Os usuários tiveram problemas para fazer o upload de dados para o Data Lake Store?</li></ul>|

## <a name="microsoftdatamigrationservices"></a>Microsoft.datamigration/services
|Verificações executadas|
|---|
|<ul><li>Houve falha no provisionamento do serviço de migração de banco de dados?</li><li>O serviço de migração de banco de dados parou devido à inatividade ou à solicitação do usuário?</li></ul>|

## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts
|Verificações executadas|
|---|
|<ul><li>A conta do Data Share está em execução?</li><li>O cluster está hospedando o Data Share disponível?</li></ul>|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers
|Verificações executadas|
|---|
|<ul><li>O servidor está indisponível devido à manutenção?</li><li>O servidor está indisponível devido à reconfiguração?</li></ul>|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers
|Verificações executadas|
|---|
|<ul><li>O servidor está indisponível devido à manutenção?</li><li>O servidor está indisponível devido à reconfiguração?</li></ul>|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers
|Verificações executadas|
|---|
|<ul><li>O servidor está indisponível devido à manutenção?</li><li>O servidor está indisponível devido à reconfiguração?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs
|Verificações executadas|
|---|
|<ul><li>O hub IoT está em execução?</li></ul>|

## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft. DigitalTwins/DigitalTwinsInstances
|Verificações executadas|
|---|
|<ul><li>A instância do gêmeos digital do Azure está em funcionamento?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Verificações executadas|
|---|
|<ul><li>Alguma solicitação do banco de dados ou da coleção deixou de ser atendida devido a uma indisponibilidade de serviço do Azure Cosmos DB?</li><li>Alguma solicitação de documentos deixou de ser atendida devido a uma indisponibilidade de serviço do Azure Cosmos DB?</li></ul>|

## <a name="microsofteventhubnamespaces"></a>Microsoft.eventhub/namespaces
|Verificações executadas|
|---|
|<ul><li>O namespace dos Hubs de Eventos está apresentando erros gerados pelo usuário?</li><li>O namespace dos Hubs de Eventos está sendo atualizado no momento?</li></ul>|

## <a name="microsofthdinsightclusters"></a>Microsoft.hdinsight/clusters
|Verificações executadas|
|---|
|<ul><li>Os serviços principais estão disponíveis no cluster HDInsight?</li><li>O cluster HDInsight consegue acessar a chave de criptografia de BYOK em repouso?</li></ul>|

## <a name="microsoftiotcentraliotapps"></a>Microsoft. IoTCentral/IoTApps
|Verificações executadas|
|---|
|<ul><li>O aplicativo IoT Central está disponível?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults
|Verificações executadas|
|---|
|<ul><li>As solicitações para a falha do cofre de chave são decorrentes dos problemas da plataforma Azure KeyVault?</li><li>As solicitações para o cofre de chave estão sendo limitadas devido a muitas solicitações feitas pelo cliente?</li></ul>|

## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/clusters
|Verificações executadas|
|---|
|<ul><li>O cluster está apresentando baixas taxas de êxito de ingestão?</li><li>O cluster está apresentando alta latência de ingestão?</li><li>O cluster está apresentando um grande número de falhas de consulta?</li></ul>|

## <a name="microsoftmachinelearningwebservices"></a>Microsoft.MachineLearning/webServices
|Verificações executadas|
|---|
|<ul><li>O serviço Web está em execução?</li></ul>|

## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices
|Verificações executadas|
|---|
|<ul><li>O serviço de mídia está em execução?</li></ul>|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.network/applicationgateways
|Verificações executadas|
|---|
|<ul><li>O desempenho do Gateway de Aplicativo está degradado?</li><li>O Gateway de Aplicativo está disponível?</li></ul>|

## <a name="microsoftnetworkbastionhosts"></a>Microsoft. Network/bastionhosts
|Verificações executadas|
|---|
|<ul><li>O host de bastiões está em funcionamento?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|Verificações executadas|
|---|
|<ul><li>O túnel VPN está conectado?</li><li>Há conflitos de configuração na conexão?</li><li>As chaves pré-compartilhadas estão configuradas corretamente?</li><li>O dispositivo de VPN local é acessível?</li><li>Há incompatibilidades na política de segurança IPsec/IKE?</li><li>A conexão VPN S2S está provisionada corretamente ou em um estado de falha?</li><li>A conexão VNET para VNET está provisionada corretamente ou em um estado de falha?</li></ul>|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.network/expressroutecircuits
|Verificações executadas|
|---|
|<ul><li>O circuito do ExpressRoute está íntegro?</li></ul>|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.network/frontdoors
|Verificações executadas|
|---|
|<ul><li>Os back-ends do Front Door respondem com erros nas investigações de integridade?</li><li>As alterações de configuração estão atrasadas?</li></ul>|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.network/LoadBalancers
|Verificações executadas|
|---|
|<ul><li>Os pontos de extremidade do balanceamento de carga estão disponíveis?</li></ul>|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficmanagerprofiles
|Verificações executadas|
|---|
|<ul><li>Há algum problema afetando o perfil do Gerenciador de tráfego?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Verificações executadas|
|---|
|<ul><li>O Gateway de VPN pode ser acessado pela Internet?</li><li>O Gateway de VPN está em modo de espera?</li><li>O serviço de VPN está em execução no gateway?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|Verificações executadas|
|---|
|<ul><li>Operações de runtime como o registro, a instalação ou o envio podem ser executadas no namespace?</li></ul>|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.operationalinsights/workspaces
|Verificações executadas|
|---|
|<ul><li>Há atrasos de indexação do espaço de trabalho?</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities
|Verificações executadas|
|---|
|<ul><li>O recurso de capacidade está funcionando?</li><li>Todas as cargas de trabalho estão funcionando?</li></ul>

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Verificações executadas|
|---|
|<ul><li>Operações de diagnóstico podem ser executadas no cluster?</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces
|Verificações executadas|
|---|
|<ul><li>Os clientes estão enfrentando erros de Barramento de Serviço gerado pelo usuário?</li><li>Os usuários estão enfrentando um aumento de erros transitórios devido a uma atualização de namespace do Barramento de Serviço?</li></ul>|

## <a name="microsoftservicefabricclusters"></a>Microsoft.ServiceFabric/clusters
|Verificações executadas|
|---|
|<ul><li>O cluster Service Fabric está em funcionamento?</li><li>O cluster Service Fabric pode ser gerenciado por meio do Azure Resource Manager?</li></ul>|

## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.SQL/managedInstances/databases
|Verificações executadas|
|---|
|<ul><li>O banco de dados está em execução?</li></ul>|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.SQL/servers/databases
|Verificações executadas|
|---|
|<ul><li>Houve logons no banco de dados?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts
|Verificações executadas|
|---|
|<ul><li>As solicitações para ler dados da conta de Armazenamento falham devido a problemas da plataforma do Azure Storage?</li><li>As solicitações para gravar dados da conta de Armazenamento falham devido a problemas da plataforma do Azure Storage?</li><li>O cluster de Armazenamento em que a conta de armazenamento reside está indisponível?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Verificações executadas|
|---|
|<ul><li>Todos os hosts em que o trabalho está sendo executado estão em funcionamento?</li><li>Não foi possível iniciar o trabalho?</li><li>Há atualizações de runtime em andamento?</li><li>O trabalho está em um estado esperado (por exemplo, em execução ou parado pelo cliente)?</li><li>O trabalho encontrou exceções de memória insuficiente?</li><li>Há atualizações de computação agendadas em andamento?</li><li>O Gerenciador de Execução (plano de controle) está disponível?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Verificações executadas|
|---|
|<ul><li>O servidor host está em funcionamento?</li><li>Os Serviços de Informações da Internet estão em execução?</li><li>O balanceador de carga está em execução?</li><li>O Plano do Serviço de Aplicativo pode ser alcançado a partir do datacenter?</li><li>A conta de armazenamento que está hospedando o conteúdo dos sites para o serverFarm está disponível?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|Verificações executadas|
|---|
|<ul><li>O servidor host está em funcionamento?</li><li>Os servidor de Informações da Internet está em execução?</li><li>O balanceador de carga está em execução?</li><li>É possível alcançar o aplicativo Web de dentro do datacenter?</li><li>A conta de armazenamento que está hospedando o conteúdo do site está disponível?</li></ul>|

## <a name="next-steps"></a>Próximas etapas
-  Consulte [Introdução ao painel de Integridade do Serviço do Azure](service-health-overview.md) e [Introdução ao Azure Resource Health](resource-health-overview.md) para entender mais sobre eles. 
-  [Perguntas frequentes sobre o Azure Resource Health](resource-health-faq.md)
- Configure alertas para receber notificações de problemas de integridade. Para obter mais informações, consulte [Configurar alertas do eventos do Service Health](./alerts-activity-log-service-notifications-portal.md).