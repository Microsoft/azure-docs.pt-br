---
title: "Alterar configurações de cluster do Azure Service Fabric | Microsoft Docs"
description: "Este artigo descreve as configurações de malha e as políticas de atualização de malha que você pode personalizar."
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: 
ms.assetid: 7ced36bf-bd3f-474f-a03a-6ebdbc9677e2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/15/2017
ms.author: chackdan
ms.openlocfilehash: cedfba78d8d1186618cf4f61154c5d88a00eb12b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="customize-service-fabric-cluster-settings-and-fabric-upgrade-policy"></a>Personalizar as configurações de cluster de Service Fabric e a política de Atualização da Malha
Este documento explica como personalizar as várias configurações de malha e a política de atualização de malha para o cluster do Service Fabric. Você pode personalizá-los através do [Portal do Azure](https://portal.azure.com) ou utilizando um modelo do Azure Resource Manager.

> [!NOTE]
> Nem todas as configurações estão disponíveis no portal. No caso de uma configuração listada abaixo não está disponível por meio do portal, personalize-a usando um modelo do Azure Resource Manager.
> 

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Personalize as configurações do cluster, utilizando os modelos do Resource Manager
As etapas abaixo ilustram como adicionar uma nova configuração *MaxDiskQuotaInMB* à seção *Diagnóstico*.

1. Vá para https://resources.azure.com
2. Navegue até a sua assinatura expandindo **assinaturas** -> **grupos de recursos** -> **Microsoft.ServiceFabric** -> **\<Nome do Cluster>**
3. No canto superior direito, selecione **Leitura/Gravação.**
4. Selecione **Editar** e atualize o elemento `fabricSettings` JSON e adicione um novo elemento:

```
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

A seguir, é apresentada uma lista de configurações de Malha que você pode personalizar, organizada por seção.

## <a name="diagnostics"></a>Diagnostics
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| ConsumerInstances |Cadeia de caracteres |A lista de instâncias de consumidor DCA. |
| ProducerInstances |string |A lista de instâncias de produtor DCA. |
| AppEtwTraceDeletionAgeInDays |Int, o padrão é 3 |O número de dias após os quais é possível excluir arquivos ETL antigos que contêm rastreamentos ETW do aplicativo. |
| AppDiagnosticStoreAccessRequiresImpersonation |Bool, o padrão é true |Se a representação é necessária ou não ao acessar repositórios de diagnóstico em nome do aplicativo. |
| MaxDiskQuotaInMB |Int, o padrão é 65536 |Cota de disco em MB para arquivos de log do Windows Fabric. |
| DiskFullSafetySpaceInMB |Int, o padrão é 1024 |Espaço em disco restante em MB para proteger contra o uso pelo DCA. |
| ApplicationLogsFormatVersion |Int, o padrão é 0 |A versão do formato de logs de aplicativo. Os valores com suporte são 0 e 1. A versão 1 inclui mais campos do registro de eventos ETW que a versão 0. |
| ClusterId |string |A ID exclusiva do cluster. Ela é gerada quando o cluster é criado. |
| EnableTelemetry |Bool, o padrão é true |Isso vai habilitar ou desabilitar a telemetria. |
| EnableCircularTraceSession |Bool, o padrão é false |O sinalizador indica se as sessões de rastreamento circular devem ser usadas. |

## <a name="traceetw"></a>Rastreamento/Etw
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| Nível |Int, o padrão é 4 |O nível de Rastreamento de Eventos para Windows pode assumir os valores 1, 2, 3 e 4. Para que tenha suporte, você deve manter o nível de rastreamento em 4 |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| IsEnabled |Bool, o padrão é true |O sinalizador indica se a coleta do contador de desempenho no nó local está habilitada. |
| SamplingIntervalInSeconds |Int, o padrão é 60 |Intervalo de amostragem dos contadores de desempenho que estão sendo coletados. |
| Contadores |Cadeia de caracteres |Lista separada por vírgulas dos contadores de desempenho a serem coletados. |
| MaxCounterBinaryFileSizeInMB |Int, o padrão é 1 |Tamanho máximo (em MB) para cada arquivo binário de contador de desempenho. |
| NewCounterBinaryFileCreationIntervalInMinutes |Int, o padrão é 10 |Intervalo máximo (em segundos) após o qual um novo arquivo binário de contador de desempenho é criado. |

## <a name="setup"></a>Configuração
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| FabricDataRoot |string |Diretório raiz de dados do Service Fabric. O padrão para o Azure é d:\svcfab |
| FabricLogRoot |Cadeia de caracteres |Diretório raiz de log do Service Fabric. É nele que logs e os rastreamentos do SF são colocados. |
| ServiceRunAsAccountName |Cadeia de caracteres |O nome da conta na qual executar o serviço de host de malha. |
| ServiceStartupType |Cadeia de caracteres |O tipo de inicialização do serviço de host de malha. |
| SkipFirewallConfiguration |Bool, o padrão é false |Especifica se as configurações do firewall precisam ou não ser definidas pelo sistema. Isso se aplicará apenas se você estiver usando o Firewall do Windows. Se você estiver usando firewalls de terceiros, deverá abrir as portas para o sistema e aplicativos a utilizar |

## <a name="transactionalreplicator"></a>TransactionalReplicator
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| MaxCopyQueueSize |Uint, o padrão é 16384 |Esse é o valor máximo que define o tamanho inicial da fila que mantém as operações de replicação. Observe que ele deve ser uma potência de 2. Se, durante a o tempo de execução, a fila aumentar para esse tamanho, as operações estarão restritas entre os replicadores primários e secundários. |
| BatchAcknowledgementInterval | Tempo em segundos, o padrão é 0,015 | Especifique o intervalo de tempo em segundos. Determina a quantidade de tempo que o replicador aguarda depois de receber uma operação antes de enviar de volta uma confirmação. Outras operações recebidas durante esse período de tempo terão suas confirmações enviadas de volta em uma única mensagem-> reduzindo o tráfego de rede, mas reduzindo potencialmente a produtividade do replicador. |
| MaxReplicationMessageSize |Uint, o padrão é 52428800 | Tamanho máximo da mensagem das operações de replicação. O padrão é 50 MB. |
| ReplicatorAddress |cadeia de caracteres, o padrão é "localhost:0" | O ponto de extremidade na forma de uma cadeia de caracteres -'IP:Port', usada pelo replicador do Windows Fabric para estabelecer conexões com outras réplicas a fim de enviar/receber operações. |
| InitialPrimaryReplicationQueueSize |Uint, o padrão é 64 | Esse valor define o tamanho inicial da fila que mantém as operações de replicação no primário. Observe que ele deve ser uma potência de 2.|
| MaxPrimaryReplicationQueueSize |Uint, o padrão é 8192 |Esse é o número máximo de operações que poderiam existir na fila de replicação primária. Observe que ele deve ser uma potência de 2. |
| MaxPrimaryReplicationQueueMemorySize |Uint, o padrão é 0 |Esse é o valor máximo da fila de replicação primária em bytes. |
| InitialSecondaryReplicationQueueSize |Uint, o padrão é 64 |Esse valor define o tamanho inicial da fila que mantém as operações de replicação no secundário. Observe que ele deve ser uma potência de 2. |
| MaxSecondaryReplicationQueueSize |Uint, o padrão é 16384 |Esse é o número máximo de operações que poderiam existir na fila de replicação secundária. Observe que ele deve ser uma potência de 2. |
| MaxSecondaryReplicationQueueMemorySize |Uint, o padrão é 0 |Esse é o valor máximo da fila de replicação secundária em bytes. |
| SecondaryClearAcknowledgedOperations |Bool, o padrão é false |Bool que controla se as operações no replicador secundário serão limpas depois que elas forem confirmadas no primário (liberadas no disco). Definir isso como TRUE pode resultar em leituras de disco adicionais no novo primário ao capturar réplicas após um failover. |
| MaxMetadataSizeInKB |Int, o padrão é 4 |Tamanho máximo dos metadados de transmissão de log. |
| MaxRecordSizeInKB |Uint, o padrão é 1024 | Tamanho máximo de um registro de transmissão de log. |
| CheckpointThresholdInMB |Int, o padrão é 50 |Um ponto de verificação será iniciado quando o uso do log exceder esse valor. |
| MaxAccumulatedBackupLogSizeInMB |Int, o padrão é 800 |Tamanho máximo acumulado (em MB) de logs de backup em determinada cadeia de log de backup. Uma solicitação de backup incremental falharia se o backup incremental gerasse um log de backup que acarretasse em logs de backup acumulados, uma vez que o backup completo relevante seria maior do que esse tamanho. Nesses casos, o usuário deve fazer um backup completo. |
| MaxWriteQueueDepthInKB |Int, o padrão é 0 | Int para a profundidade máxima da fila de gravação que o agente principal pode usar, conforme especificado em quilobytes, para o log associado a essa réplica. Esse valor é o número máximo de bytes que podem estar pendentes durante as atualizações do agente principal. Pode ser 0 para o agente principal calcular um valor apropriado ou um múltiplo de 4. |
| SharedLogId |Cadeia de caracteres |Identificador de log compartilhado. Isso é um guid e deve ser único para cada log compartilhado. |
| SharedLogPath |Cadeia de caracteres |Caminho para o log compartilhado. Se esse valor for vazio, então o log compartilhado padrão será usado. |
| SlowApiMonitoringDuration |Tempo em segundos, o padrão é de 300 | Especifica a duração para a API antes que o evento de integridade do aviso seja acionado.|
| MinLogSizeInMB |Int, o padrão é 0 |Tamanho mínimo do log transacional. O log não poderá truncar um tamanho abaixo dessa configuração. 0 indica que o replicador determinará o tamanho mínimo do log de acordo com outras configurações. Aumentar esse valor aumenta a possibilidade de fazer cópias parciais e backups incrementais, uma vez que as chances de registros de log relevantes sendo truncados são reduzidas. |

## <a name="fabricclient"></a>FabricClient
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| NodeAddresses |cadeia de caracteres, o padrão é "" |Uma coleção de endereços (cadeias de conexão) em diferentes nós que podem ser usados para se comunicar com o serviço de cadastramento. Inicialmente, o cliente se conecta selecionando um dos endereços aleatoriamente. Se mais de uma cadeia de conexão for fornecida e uma conexão falhar devido a um erro de comunicação ou de tempo limite, o cliente usará o próximo endereço na sequência. Consulte a seção de repetição do Endereço do Serviço de Cadastramento para obter detalhes sobre semântica de repetição. |
| ConnectionInitializationTimeout |Tempo em segundos, o padrão é 2 |Especifique o intervalo de tempo em segundos. Intervalo do tempo limite de conexão para que cada cliente tente abrir uma conexão com o gateway. |
| PartitionLocationCacheLimit |Int, o padrão é 100000 |Número de partições em cache para a resolução do serviço (definida como 0 para nenhum limite). |
| ServiceChangePollInterval |Tempo em segundos, o padrão é 120 |Especifique o intervalo de tempo em segundos. O intervalo entre pesquisas consecutivas para que o serviço seja alterado do cliente para o gateway para retornos de chamada de notificações de alteração do serviço registrado. |
| KeepAliveIntervalInSeconds |Int, o padrão é 20 |O intervalo no qual o transporte FabricClient envia mensagens keep alive para o gateway. Para 0, keepAlive está desabilitado. Deve ser um valor positivo. |
| HealthOperationTimeout |Tempo em segundos, o padrão é 120 |Especifique o intervalo de tempo em segundos. O tempo limite para uma mensagem de relatório enviada ao Gerenciador de Integridade. |
| HealthReportSendInterval |Tempo em segundos, o padrão é de 30 |Especifique o intervalo de tempo em segundos. O intervalo no qual o componente de relatório envia relatórios de integridade acumulados ao Gerenciador de Integridade. |
| HealthReportRetrySendInterval |Tempo em segundos, o padrão é de 30 |Especifique o intervalo de tempo em segundos. O intervalo no qual o componente de relatório reenvia relatórios de integridade acumulados ao Gerenciador de Integridade. |
| RetryBackoffInterval |Tempo em segundos, o padrão é 3 |Especifique o intervalo de tempo em segundos. O intervalo de retirada antes de repetir a operação. |
| MaxFileSenderThreads |Uint, o padrão é 10 |O número máximo de arquivos transferidos paralelamente. |

## <a name="common"></a>Comum
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| PerfMonitorInterval |Tempo em segundos, o padrão é 1 |Especifique o intervalo de tempo em segundos. Intervalo de monitoramento de desempenho. Definir como 0 ou valor negativo desabilita o monitoramento. |

## <a name="healthmanager"></a>HealthManager
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| EnableApplicationTypeHealthEvaluation |Bool, o padrão é false |Política de avaliação de integridade do cluster: habilitar avaliação de integridade de tipo por aplicativo. |

## <a name="fabricnode"></a>FabricNode
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| StateTraceInterval |Tempo em segundos, o padrão é de 300 |Especifique o intervalo de tempo em segundos. O intervalo para rastrear o status do nó em cada nó e até nós em FM/FMM. |

## <a name="nodedomainids"></a>NodeDomainIds
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| UpgradeDomainId |cadeia de caracteres, o padrão é "" |Descreve o domínio de atualização ao qual o nó pertence. |
| PropertyGroup |NodeFaultDomainIdCollection |Descreve os domínios de falha aos quais o nó pertence. O domínio de falha é definido por meio de um URI que descreve o local do nó no data center.  Os URIs do domínio de falha estão no formato fd:/fd/ seguidos por um segmento de caminho do URI.|

## <a name="nodeproperties"></a>NodeProperties
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| PropertyGroup |NodePropertyCollectionMap |Uma coleção de pares chave-valor da cadeia de caracteres para propriedades do nó. |

## <a name="nodecapacities"></a>NodeCapacities
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| PropertyGroup |NodeCapacityCollectionMap |Uma coleção de capacidades de nó para diferentes métricas. |

## <a name="fabricnode"></a>FabricNode
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| StartApplicationPortRange |Int, o padrão é 0 |Início das portas do aplicativo gerenciadas pelo subsistema de hospedagem. Necessário se EndpointFilteringEnabled for true na hospedagem. |
| EndApplicationPortRange |Int, o padrão é 0 |Fim (não inclusive) das portas do aplicativo gerenciadas pelo subsistema de hospedagem. Necessário se EndpointFilteringEnabled for true na hospedagem. |
| ClusterX509StoreName |cadeia de caracteres, o padrão é "My" |Nome do repositório de certificados X.509 que contém o certificado do cluster para proteger a comunicação dentro do cluster. |
| ClusterX509FindType |cadeia de caracteres, o padrão é "FindByThumbprint" |Indica como pesquisar o certificado do cluster no repositório especificado pelos valores com suporte ClusterX509StoreName: "FindByThumbprint"; "FindBySubjectName" With "FindBySubjectName"; quando houver várias correspondências, a que tiver a data de expiração mais afastada será usada. |
| ClusterX509FindValue |cadeia de caracteres, o padrão é "" |Valor do filtro de pesquisa usado para localizar o certificado do cluster. |
| ClusterX509FindValueSecondary |cadeia de caracteres, o padrão é "" |Valor do filtro de pesquisa usado para localizar o certificado do cluster. |
| ServerAuthX509StoreName |cadeia de caracteres, o padrão é "My" |Nome do repositório de certificados X.509 que contém o certificado do servidor para o serviço de entrada. |
| ServerAuthX509FindType |cadeia de caracteres, o padrão é "FindByThumbprint" |Indica como pesquisar o certificado do servidor no repositório especificado pelo valor ServerAuthX509StoreName com suporte: FindByThumbprint; FindBySubjectName. |
| ServerAuthX509FindValue |cadeia de caracteres, o padrão é "" |Valor do filtro de pesquisa usado para localizar o certificado do servidor. |
| ServerAuthX509FindValueSecondary |cadeia de caracteres, o padrão é "" |Valor do filtro de pesquisa usado para localizar o certificado do servidor. |
| ClientAuthX509StoreName |cadeia de caracteres, o padrão é "My" |Nome do repositório de certificados X.509 que contém o certificado da função de administrador padrão FabricClient. |
| ClientAuthX509FindType |cadeia de caracteres, o padrão é "FindByThumbprint" |Indica como pesquisar o certificado no repositório especificado pelo valor ClientAuthX509StoreName com suporte: FindByThumbprint; FindBySubjectName. |
| ClientAuthX509FindValue |cadeia de caracteres, o padrão é "" | Valor do filtro de pesquisa usado para localizar o certificado para a função de administrador padrão FabricClient. |
| ClientAuthX509FindValueSecondary |cadeia de caracteres, o padrão é "" |Valor do filtro de pesquisa usado para localizar o certificado para a função de administrador padrão FabricClient. |
| UserRoleClientX509StoreName |cadeia de caracteres, o padrão é "My" |Nome do repositório de certificados X.509 que contém o certificado da função de usuário padrão FabricClient. |
| UserRoleClientX509FindType |cadeia de caracteres, o padrão é "FindByThumbprint" |Indica como pesquisar o certificado no repositório especificado pelo valor UserRoleClientX509StoreName com suporte: FindByThumbprint; FindBySubjectName. |
| UserRoleClientX509FindValue |cadeia de caracteres, o padrão é "" |Valor do filtro de pesquisa usado para localizar o certificado para a função de usuário padrão FabricClient. |
| UserRoleClientX509FindValueSecondary |cadeia de caracteres, o padrão é "" |Valor do filtro de pesquisa usado para localizar o certificado para a função de usuário padrão FabricClient. |

## <a name="paas"></a>Paas
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| ClusterId |cadeia de caracteres, o padrão é "" |Repositório de certificados X509 usado pela malha para proteção da configuração. |

## <a name="fabrichost"></a>FabricHost
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| StopTimeout |Tempo em segundos, o padrão é de 300 |Especifique o intervalo de tempo em segundos. O tempo limite para a ativação; desativação e atualização do serviço hospedado. |
| StartTimeout |Tempo em segundos, o padrão é de 300 |Especifique o intervalo de tempo em segundos. Tempo limite para inicialização do fabricactivationmanager. |
| ActivationRetryBackoffInterval |Tempo em segundos, o padrão é 5 |Especifique o intervalo de tempo em segundos. Intervalo de retirada em cada falha de ativação; em toda falha de ativação contínua, o sistema tentará novamente realizar a ativação até a MaxActivationFailureCount. O intervalo de repetição em cada tentativa é um produto da falha de ativação contínua e do intervalo de retirada de ativação. |
| ActivationMaxRetryInterval |Tempo em segundos, o padrão é de 300 |Especifique o intervalo de tempo em segundos. Máx. intervalo de repetição de ativação. Em cada falha contínua, o intervalo de repetição é calculado como Min( ActivationMaxRetryInterval; Contagem de falha contínua * ActivationRetryBackoffInterval). |
| ActivationMaxFailureCount |Int, o padrão é 10 |Essa é a contagem máxima para a qual o sistema tentará novamente a ativação com falha antes de desistir. |
| EnableServiceFabricAutomaticUpdates |Bool, o padrão é false |Isso é para habilitar a atualização automática da malha por meio do Windows Update. |
| EnableServiceFabricBaseUpgrade |Bool, o padrão é false |Isso é para habilitar a atualização de base de servidor. |
| EnableRestartManagement |Bool, o padrão é false |Isso é para habilitar a reinicialização do servidor. |


## <a name="failovermanager"></a>FailoverManager
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| UserReplicaRestartWaitDuration |Tempo em segundos, o padrão é 60,0 * 30 |Especifique o intervalo de tempo em segundos. Quando uma réplica persistente fica inativa, o Windows Fabric aguarda a duração para que a réplica fique ativa novamente antes de criar novas réplicas de posicionamento (o que exigiria uma cópia do estado). |
| QuorumLossWaitDuration |Tempo em segundos, o padrão é MaxValue |Especifique o intervalo de tempo em segundos. Essa é a duração máxima para a qual nós permitimos que uma partição esteja em um estado de perda de quorum. Se a partição ainda estiver em perda de quorum após essa duração, ela será recuperada da perda de quorum considerando as réplicas inativas como perdidas. Observe que isso eventualmente pode resultar em perda de dados. |
| UserStandByReplicaKeepDuration |Tempo em segundos, o padrão é 3600,0 * 24 * 7 |Especifique o intervalo de tempo em segundos. Quando uma réplica persistente voltar de um estado inativo, talvez ela já tenha sido substituída. Este temporizador determina por quanto tempo o FM manterá a réplica em espera antes de descartá-la. |
| UserMaxStandByReplicaCount |Int, o padrão é 1 |O número máximo padrão de réplicas Em Espera que o sistema mantém para serviços de usuário. |

## <a name="namingservice"></a>NamingService
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| TargetReplicaSetSize |Int, o padrão é 7 |O número de conjuntos de réplicas para cada partição do repositório do Serviço de Cadastramento. O aumento no número de conjuntos de réplicas aumenta o nível de confiabilidade das informações no Repositório do Serviço de Cadastramento; a diminuição da alteração de que as informações serão perdidas como resultado de falhas do nó, a um custo do aumento de carga no Windows Fabric e da quantidade de tempo que leva para realizar atualizações nos dados de nomenclatura.|
|MinReplicaSetSize | Int, o padrão é 3 | O número mínimo de réplicas do Serviço de Cadastramento necessárias para serem escritas a fim de concluir uma atualização. Se houver menos réplicas que essas ativas no sistema, o Sistema de Confiabilidade recusará atualização do Repositório do Serviço de Cadastramento até que as réplicas sejam restauradas. Esse valor nunca deve ser maior que o TargetReplicaSetSize. |
|ReplicaRestartWaitDuration | Tempo em segundos, o padrão é (60,0 * 30)| Especifique o intervalo de tempo em segundos. Quando uma réplica do Serviço de Cadastramento ficar inativa, esse temporizador será iniciado.  Quando ele expirar, o FM começará a substituir as réplicas inativas (ele não ainda não as considerará perdidas). |
|QuorumLossWaitDuration | Tempo em segundos, o padrão é MaxValue | Especifique o intervalo de tempo em segundos. Quando um Serviço de Cadastramento entra em perda de quorum, esse temporizador é iniciado.  Quando ele expirar, o FM considerará as réplicas inativas como perdidas e tentará recuperar o quorum. Observe que isso pode resultar em perda de dados. |
|StandByReplicaKeepDuration | Tempo em segundos, o padrão é 3600,0 * 2 | Especifique o intervalo de tempo em segundos. Quando uma réplica do Serviço de Cadastramento volta de um estado inativo, talvez ela já tenha sido substituída.  Este temporizador determina por quanto tempo o FM manterá a réplica em espera antes de descartá-la. |
|PlacementConstraints | cadeia de caracteres, o padrão é "" | Restrição de posicionamento para o Serviço de Cadastramento. |
|ServiceDescriptionCacheLimit | Int, o padrão é 0 | O número máximo de entradas mantidas no cache de descrição do serviço LRU no Serviço do Repositório de Cadastramento (defina como 0 para sem limite). |
|RepairInterval | Tempo em segundos, o padrão é 5 | Especifique o intervalo de tempo em segundos. Intervalo em que o reparo de inconsistência de nomenclatura entre o proprietário da autoridade e o proprietário do nome será iniciado. |
|MaxNamingServiceHealthReports | Int, o padrão é 10 | O número máximo de operações lentas que o Serviço do Repositório de Cadastramento reporta como não íntegras de uma vez. Se for 0, todas as operações lentas serão enviadas. |
| MaxMessageSize |Int, o padrão é 4*1024*1024 |O tamanho máximo da mensagem para a comunicação do nó do cliente ao usar a nomenclatura. Atenuação do ataque ao DOS, o valor padrão é 4 MB. |
| MaxFileOperationTimeout |Tempo em segundos, o padrão é de 30 |Especifique o intervalo de tempo em segundos. O tempo limite máximo permitido para a operação de serviço de repositório de arquivos. As solicitações que especificarem um tempo limite maior serão rejeitadas. |
| MaxOperationTimeout |Tempo em segundos, o padrão é 600 |Especifique o intervalo de tempo em segundos. O tempo limite máximo permitido para operações do cliente. As solicitações que especificarem um tempo limite maior serão rejeitadas. |
| MaxClientConnections |Int, o padrão é 1000 |O número máximo permitido de conexões do cliente por gateway. |
| ServiceNotificationTimeout |Tempo em segundos, o padrão é de 30 |Especifique o intervalo de tempo em segundos. O tempo limite usado ao entregar notificações de serviço ao cliente. |
| MaxOutstandingNotificationsPerClient |Int, o padrão é 1000 |O número máximo de notificações pendentes antes que o registro de um cliente seja fechado à força pelo gateway. |
| MaxIndexedEmptyPartitions |Int, o padrão é 1000 |O número máximo de partições vazias que permanecerão indexadas no cache de notificação para sincronizar clientes de reconexão. As partições vazias acima desse número serão removidas do índice em ordem crescente de versão de pesquisa. Os clientes de reconexão ainda podem sincronizar e receber atualizações de partição vazias perdidas, mas o protocolo de sincronização se torna mais caro. |
| GatewayServiceDescriptionCacheLimit |Int, o padrão é 0 |O número máximo de entradas mantidas no cache de descrição do serviço LRU no Gateway de Cadastramento (defina como 0 para sem limite). |
| PartitionCount |Int, o padrão é 3 |O número de partições do repositório do Serviço de Cadastramento a serem criadas. Cada partição tem uma chave de partição única que corresponde ao seu índice. Portanto, chaves de partição então [0; PartitionCount) existem. O aumento no número de partições do Serviço de Cadastramento aumenta a escala na qual o Serviço de Cadastramento pode ser realizado diminuindo a quantidade média de dados mantidos por qualquer conjunto de réplicas de suporte, a um custo do aumento da utilização de recursos (uma vez que as réplicas de serviço PartitionCount*ReplicaSetSize devem ser mantidas).|

## <a name="runas"></a>RunAs
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| RunAsAccountName |cadeia de caracteres, o padrão é "" |Indica o nome da conta RunAs. Isso só é necessário para o tipo de conta "DomainUser" ou "ManagedServiceAccount". Os valores válidos são "domínio\usuário" ou "user@domain". |
|RunAsAccountType|cadeia de caracteres, o padrão é "" |Indica o tipo de conta RunAs. Isso é necessário para qualquer seção RunAs Os valores válidos são "DomainUser/NetworkService/ManagedServiceAccount/LocalSystem".|
|RunAsPassword|cadeia de caracteres, o padrão é "" |Indica a senha da conta RunAs. Isso só é necessário para o tipo de conta "DomainUser". |

## <a name="runasfabric"></a>RunAs_Fabric
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| RunAsAccountName |cadeia de caracteres, o padrão é "" |Indica o nome da conta RunAs. Isso só é necessário para o tipo de conta "DomainUser" ou "ManagedServiceAccount". Os valores válidos são "domínio\usuário" ou "user@domain". |
|RunAsAccountType|cadeia de caracteres, o padrão é "" |Indica o tipo de conta RunAs. Isso é necessário para qualquer seção RunAs Os valores válidos são "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|cadeia de caracteres, o padrão é "" |Indica a senha da conta RunAs. Isso só é necessário para o tipo de conta "DomainUser". |

## <a name="runashttpgateway"></a>RunAs_HttpGateway
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| RunAsAccountName |cadeia de caracteres, o padrão é "" |Indica o nome da conta RunAs. Isso só é necessário para o tipo de conta "DomainUser" ou "ManagedServiceAccount". Os valores válidos são "domínio\usuário" ou "user@domain". |
|RunAsAccountType|cadeia de caracteres, o padrão é "" |Indica o tipo de conta RunAs. Isso é necessário para qualquer seção RunAs Os valores válidos são "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|cadeia de caracteres, o padrão é "" |Indica a senha da conta RunAs. Isso só é necessário para o tipo de conta "DomainUser". |

## <a name="runasdca"></a>RunAs_DCA
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| RunAsAccountName |cadeia de caracteres, o padrão é "" |Indica o nome da conta RunAs. Isso só é necessário para o tipo de conta "DomainUser" ou "ManagedServiceAccount". Os valores válidos são "domínio\usuário" ou "user@domain". |
|RunAsAccountType|cadeia de caracteres, o padrão é "" |Indica o tipo de conta RunAs. Isso é necessário para qualquer seção RunAs Os valores válidos são "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|cadeia de caracteres, o padrão é "" |Indica a senha da conta RunAs. Isso só é necessário para o tipo de conta "DomainUser". |

## <a name="httpgateway"></a>HttpGateway
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
|IsEnabled|Bool, o padrão é false | Habilita/desabilita o httpgateway. O httpgateway está desabilitado por padrão e essa configuração precisa ser definida para habilitá-lo. |
|ActiveListeners |Uint, o padrão é 50 | Número de leituras a serem postadas na fila de servidor http. Isso controla o número de solicitações simultâneas que podem ser atendidas pelo HttpGateway. |
|MaxEntityBodySize |Uint, o padrão é 4194304 |  Fornece o tamanho máximo do corpo que se espera de uma solicitação http. O valor padrão é 4 MB. Uma solicitação falhará no httpgateway se ele tiver um corpo de tamanho maior que esse valor. O tamanho mínimo da parte de leitura é 4096 bytes. Portanto, isso tem que ser >= 4096. |
|HttpGatewayHealthReportSendInterval |Tempo em segundos, o padrão é de 30 | Especifique o intervalo de tempo em segundos. O intervalo no qual o Gateway Http envia relatórios de integridade acumulados ao Gerenciador de Integridade. |

## <a name="ktllogger"></a>KtlLogger
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
|AutomaticMemoryConfiguration |Int, o padrão é 1 | Sinalizador que indica se as configurações de memória devem ser configuradas dinâmica e automaticamente. Se for zero, as definições de configuração de memória serão usadas diretamente e não serão alteradas com base nas condições do sistema. Se for um, as definições da memória serão configuradas automaticamente e poderão ser alteradas com base nas condições do sistema. |
|WriteBufferMemoryPoolMinimumInKB |Int, o padrão é 8388608 |O número de KB para serem inicialmente alocados para o pool de memória do buffer de gravação. Use 0 para indicar que nenhum padrão de limite deve ser consistente com SharedLogSizeInMB abaixo. |
|WriteBufferMemoryPoolMaximumInKB | Int, o padrão é 0 |O número de KB até o qual o pool de memória do buffer de gravação tem permissão para crescer. Use 0 para não indicar nenhum limite. |
|MaximumDestagingWriteOutstandingInKB | Int, o padrão é 0 | O número de KB que o log compartilhado tem permissão para ficar à frente do log dedicado. Use 0 para não indicar nenhum limite.
|SharedLogPath |cadeia de caracteres, o padrão é "" | O caminho e o nome de arquivo do local onde será inserido o contêiner do log compartilhado. Use "" para usar o caminho padrão na raiz de dados de malha. |
|SharedLogId |cadeia de caracteres, o padrão é "" |Guid exclusivo para o contêiner de log compartilhado. Use "" se usar o caminho padrão na raiz de dados de malha. |
|SharedLogSizeInMB |Int, o padrão é 8192 | O número de MB a ser alocado no contêiner do log compartilhado. |

## <a name="applicationgatewayhttp"></a>ApplicationGateway/Http
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
|IsEnabled |Bool, o padrão é false | Habilita/desabilita o HttpApplicationGateway. HttpApplicationGateway está desabilitado por padrão e essa configuração precisa ser definida para habilitá-lo. |
|NumberOfParallelOperations | Uint, o padrão é 5000 | Número de leituras a serem postadas na fila de servidor http. Isso controla o número de solicitações simultâneas que podem ser atendidas pelo HttpGateway. |
|DefaultHttpRequestTimeout |Tempo em segundos. O padrão é 120 |Especifique o intervalo de tempo em segundos.  Fornece o tempo limite da solicitação padrão para as solicitações de http que estão sendo processadas no gateway de aplicativo http. |
|ResolveServiceBackoffInterval |Tempo em segundos, o padrão é 5 |Especifique o intervalo de tempo em segundos.  Fornece o intervalo de retirada padrão antes de tentar uma operação de serviço de resolução falido novamente. |
|BodyChunkSize |Uint, o padrão é 16384 |  Fornece o tamanho da parte em bytes usado para ler o corpo. |
|GatewayAuthCredentialType |cadeia de caracteres, o padrão é "None" | Indica o tipo de credenciais de segurança para usar nos ponto de extremidade do gateway do aplicativo Os valores válidos são “None/ X509. |
|GatewayX509CertificateStoreName |cadeia de caracteres, o padrão é "My" | Nome do repositório de certificados X.509 que contém o certificado do gateway de aplicativo http. |
|GatewayX509CertificateFindType |cadeia de caracteres, o padrão é "FindByThumbprint" | Indica como pesquisar o certificado no repositório especificado pelo valor GatewayX509CertificateStoreName com suporte: FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | cadeia de caracteres, o padrão é "" | Valor do filtro de pesquisa usado para localizar o certificado de gateway de aplicativo http. Esse certificado é configurado no ponto de extremidade https e também pode ser usado para verificar a identidade do aplicativo, se necessário, para os serviços. FindValue é pesquisado primeiro; se ele não existir, FindValueSecondary será pesquisado. |
|GatewayX509CertificateFindValueSecondary | cadeia de caracteres, o padrão é "" |Valor do filtro de pesquisa usado para localizar o certificado de gateway de aplicativo http. Esse certificado é configurado no ponto de extremidade https e também pode ser usado para verificar a identidade do aplicativo, se necessário, para os serviços. FindValue é pesquisado primeiro; se ele não existir, FindValueSecondary será pesquisado.|

## <a name="management"></a>Gerenciamento
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| ImageStoreConnectionString |SecureString | Cadeia de conexão para a raiz do ImageStore. |
| ImageStoreMinimumTransferBPS | Int, o padrão é 1024 |A taxa de transferência mínima entre o cluster e o ImageStore. Esse valor é usado para determinar o tempo limite ao acessar o ImageStore externo. Altere este valor somente se a latência entre o cluster e o ImageStore for alta para dar mais tempo para o cluster baixar do ImageStore externo. |
|AzureStorageMaxWorkerThreads | Int, o padrão é 25 | O número máximo de threads de trabalho em paralelo. |
|AzureStorageMaxConnections | Int, o padrão é 5000 | O número máximo de conexões simultâneas para o armazenamento do Azure. |
|AzureStorageOperationTimeout | Tempo em segundos, o padrão é 6000 | Especifique o intervalo de tempo em segundos. Tempo limite para a operação xstore ser concluída. |
|ImageCachingEnabled | Bool, o padrão é true | Essa configuração permite habilitar ou desabilitar o cache. |
|DisableChecksumValidation | Bool, o padrão é false | Essa configuração permite habilitar ou desabilitar a validação de soma de verificação durante o provisionamento de aplicativo. |
|DisableServerSideCopy | Bool, o padrão é false | Essa configuração habilita ou desabilita a cópia do lado do servidor do pacote de aplicativos no ImageStore durante o provisionamento de aplicativo. |

## <a name="healthmanagerclusterhealthpolicy"></a>HealthManager/ClusterHealthPolicy
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| ConsiderWarningAsError |Bool, o padrão é false |Política de avaliação de integridade do cluster: os avisos são tratados como erros. |
| MaxPercentUnhealthyNodes | Int, o padrão é 0 |Política de avaliação de integridade do cluster: a porcentagem máxima de nós não íntegros permitida para que o cluster seja íntegro. |
| MaxPercentUnhealthyApplications | Int, o padrão é 0 |Política de avaliação de integridade do cluster: a porcentagem máxima de aplicativos não íntegros permitida para que o cluster seja íntegro. |
|MaxPercentDeltaUnhealthyNodes | Int, o padrão é 10 |Política de avaliação de integridade de atualização do cluster: a porcentagem máxima de nós delta não íntegros permitida para que o cluster seja íntegro. |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes | Int, o padrão é 15 |Política de avaliação de integridade de atualização do cluster: a porcentagem máxima de delta de nós não íntegros em um domínio de atualização permitida para que o cluster seja íntegro.|

## <a name="faultanalysisservice"></a>FaultAnalysisService
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| TargetReplicaSetSize |Int, o padrão é 0 |NOT_PLATFORM_UNIX_START O TargetReplicaSetSize para FaultAnalysisService. |
| MinReplicaSetSize |Int, o padrão é 0 |O MinReplicaSetSize para FaultAnalysisService. |
| ReplicaRestartWaitDuration |Tempo em segundos, o padrão é 60 minutos|Especifique o intervalo de tempo em segundos. O ReplicaRestartWaitDuration para FaultAnalysisService. |
| QuorumLossWaitDuration | Tempo em segundos, o padrão é MaxValue |Especifique o intervalo de tempo em segundos. O QuorumLossWaitDuration para FaultAnalysisService. |
| StandByReplicaKeepDuration| Tempo em segundos, o padrão é (60*24*7) minutos |Especifique o intervalo de tempo em segundos. O StandByReplicaKeepDuration para FaultAnalysisService. |
| PlacementConstraints | cadeia de caracteres, o padrão é ""| O PlacementConstraints para FaultAnalysisService. |
| StoredActionCleanupIntervalInSeconds | Int, o padrão é 3600 |Essa é a frequência com que o repositório será limpo.  Somente ações em um estado terminal; e a concluída há pelo menos CompletedActionKeepDurationInSeconds será removida. |
| CompletedActionKeepDurationInSeconds | Int, o padrão é 604800 | Isso é aproximadamente por quanto tempo serão mantidas as ações em um estado terminal.  Isso também depende de StoredActionCleanupIntervalInSeconds, uma vez que o trabalho de limpeza é realizado somente nesse intervalo. 604800 é 7 dias. |
| StoredChaosEventCleanupIntervalInSeconds | Int, o padrão é 3600 |Essa é a frequência com que o repositório será auditado para limpeza; se o número de eventos for maior do que 30000, a limpeza começará. |

## <a name="filestoreservice"></a>FileStoreService
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| NamingOperationTimeout |Tempo em segundos, o padrão é de 60 |Especifique o intervalo de tempo em segundos. O tempo limite para executar uma operação de nomenclatura. |
| QueryOperationTimeout | Tempo em segundos, o padrão é de 60 |Especifique o intervalo de tempo em segundos. O tempo limite para executar a operação de consulta. |
| MaxCopyOperationThreads | Uint, o padrão é 0 | O número máximo de arquivos paralelos que os secundários podem copiar do primário. '0' == número de núcleos. |
| MaxFileOperationThreads | Uint, o padrão é 100 | O número máximo de threads paralelos permitidos para executar FileOperations (Copiar/Mover) no primário. '0' == número de núcleos. |
| MaxStoreOperations | Uint, o padrão é 4096 |O número máximo de operações de truncamento do repositório paralelo permitido no primário. '0' == número de núcleos. |
| MaxRequestProcessingThreads | Uint, o padrão é 200 |O número máximo de threads paralelos permitidos para processar solicitações no primário. '0' == número de núcleos. |
| MaxSecondaryFileCopyFailureThreshold | Uint, o padrão é 25| O número máximo de repetições de cópia de arquivo no secundário antes de desistir. |
| AnonymousAccessEnabled | Bool, o padrão é true |Habilitar/desabilitar o acesso anônimo aos compartilhamentos do FileStoreService. |
| PrimaryAccountType | cadeia de caracteres, o padrão é "" |O AccountType primário da entidade para colocar os compartilhamentos do FileStoreService em ACL. |
| PrimaryAccountUserName | cadeia de caracteres, o padrão é "" |O nome de usuário da conta primária da entidade para colocar os compartilhamentos do FileStoreService em ACL. |
| PrimaryAccountUserPassword | SecureString, o padrão é vazio |O nome de usuário da conta primária da entidade colocar os compartilhamentos do FileStoreService em ACL. |
| FileStoreService | PrimaryAccountNTLMPasswordSecret | SecureString, o padrão é vazio | O segredo de senha usado como semente para gerar a mesma senha gerada ao usar a autenticação NTLM. |
| PrimaryAccountNTLMX509StoreLocation | cadeia de caracteres, o padrão é "LocalMachine"| O local de repositório do certificado X509 usado para gerar HMAC no PrimaryAccountNTLMPasswordSecret ao usar a autenticação NTLM. |
| PrimaryAccountNTLMX509StoreName | cadeia de caracteres, o padrão é "MY"| O nome de repositório do certificado X509 usado para gerar HMAC no PrimaryAccountNTLMPasswordSecret ao usar a autenticação NTLM. |
| PrimaryAccountNTLMX509Thumbprint | cadeia de caracteres, o padrão é ""|A impressão digital de repositório do certificado X509 usado para gerar HMAC no PrimaryAccountNTLMPasswordSecret ao usar a autenticação NTLM. |
| SecondaryAccountType | cadeia de caracteres, o padrão é ""| O AccountType secundário da entidade para colocar os compartilhamentos do FileStoreService em ACL. |
| SecondaryAccountUserName | cadeia de caracteres, o padrão é ""| O Nome de Usuário da conta secundária da entidade para colocar os compartilhamentos do FileStoreService em ACL. |
| SecondaryAccountUserPassword | SecureString, o padrão é vazio |A senha da conta secundária da entidade para colocar os compartilhamentos do FileStoreService em ACL.  |
| SecondaryAccountNTLMPasswordSecret | SecureString, o padrão é vazio | O segredo de senha usado como semente para gerar a mesma senha gerada ao usar a autenticação NTLM. |
| SecondaryAccountNTLMX509StoreLocation | cadeia de caracteres, o padrão é "LocalMachine" |O local de repositório do certificado X509 usado para gerar HMAC no SecondaryAccountNTLMPasswordSecret ao usar a autenticação NTLM. |
| SecondaryAccountNTLMX509StoreName | cadeia de caracteres, o padrão é "MY" |O nome de repositório do certificado X509 usado para gerar HMAC no SecondaryAccountNTLMPasswordSecret ao usar a autenticação NTLM. |
| SecondaryAccountNTLMX509Thumbprint | cadeia de caracteres, o padrão é ""| A impressão digital do certificado X509 usado para gerar HMAC no SecondaryAccountNTLMPasswordSecret ao usar a autenticação NTLM. |

## <a name="imagestoreservice"></a>ImageStoreService
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| Habilitado |Bool, o padrão é false |O sinalizador Enabled para ImageStoreService. |
| TargetReplicaSetSize | Int, o padrão é 7 |O TargetReplicaSetSize para ImageStoreService. |
| MinReplicaSetSize | Int, o padrão é 3 |O MinReplicaSetSize para ImageStoreService. |
| ReplicaRestartWaitDuration | Tempo em segundos, o padrão é 60,0 * 30 | Especifique o intervalo de tempo em segundos. O ReplicaRestartWaitDuration para ImageStoreService. |
| QuorumLossWaitDuration | Tempo em segundos, o padrão é MaxValue | Especifique o intervalo de tempo em segundos. O QuorumLossWaitDuration para ImageStoreService. |
| StandByReplicaKeepDuration | Tempo em segundos, o padrão é 3600,0 * 2 | Especifique o intervalo de tempo em segundos. O StandByReplicaKeepDuration para ImageStoreService. |
| PlacementConstraints | cadeia de caracteres, o padrão é "" | O PlacementConstraints para ImageStoreService. |
| ClientUploadTimeout | Tempo em segundos, o padrão é 1800 |Especifique o intervalo de tempo em segundos. Valor de tempo limite para solicitação de upload de nível superior para o Serviço de Repositório de Imagens. |
| ClientCopyTimeout | Tempo em segundos, o padrão é 1800 | Especifique o intervalo de tempo em segundos. Valor de tempo limite para solicitação de cópia de nível superior para o Serviço de Repositório de Imagens. |
| ClientDownloadTimeout | Tempo em segundos, o padrão é 1800 | Especifique o intervalo de tempo em segundos. Valor de tempo limite para solicitação de download de nível superior para o Serviço de Repositório de Imagens |
| ClientListTimeout | Tempo em segundos, o padrão é 600 | Especifique o intervalo de tempo em segundos. Valor de tempo limite para solicitação de lista de nível superior para o Serviço de Repositório de Imagens. |
| ClientDefaultTimeout | Tempo em segundos, o padrão é 180 | Especifique o intervalo de tempo em segundos. Valor de tempo limite para todas as solicitações que não são de upload/que não são de download (por exemplo, existe; excluir) para o Serviço de Repositório de Imagens. |

## <a name="imagestoreclient"></a>ImageStoreClient
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| ClientUploadTimeout |Tempo em segundos, o padrão é 1800 | Especifique o intervalo de tempo em segundos. Valor de tempo limite para solicitação de upload de nível superior para o Serviço de Repositório de Imagens. |
| ClientCopyTimeout | Tempo em segundos, o padrão é 1800 | Especifique o intervalo de tempo em segundos. Valor de tempo limite para solicitação de cópia de nível superior para o Serviço de Repositório de Imagens. |
|ClientDownloadTimeout | Tempo em segundos, o padrão é 1800 | Especifique o intervalo de tempo em segundos. Valor de tempo limite para solicitação de download de nível superior para o Serviço de Repositório de Imagens. |
|ClientListTimeout | Tempo em segundos, o padrão é 600 |Especifique o intervalo de tempo em segundos. Valor de tempo limite para solicitação de lista de nível superior para o Serviço de Repositório de Imagens. |
|ClientDefaultTimeout | Tempo em segundos, o padrão é 180 | Especifique o intervalo de tempo em segundos. Valor de tempo limite para todas as solicitações que não são de upload/que não são de download (por exemplo, existe; excluir) para o Serviço de Repositório de Imagens. |

## <a name="tokenvalidationservice"></a>TokenValidationService
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| Provedores |cadeia de caracteres, o padrão é "DSTS" |Lista separada por vírgulas dos provedores de validação de tokens para habilitar (os provedores válidas são: DSTS; AAD). No momento, somente um provedor pode ser habilitado a qualquer momento. |

## <a name="upgradeorchestrationservice"></a>UpgradeOrchestrationService
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| TargetReplicaSetSize |Int, o padrão é 0 |O TargetReplicaSetSize para UpgradeOrchestrationService. |
| MinReplicaSetSize |Int, o padrão é 0 | O MinReplicaSetSize para UpgradeOrchestrationService.
| ReplicaRestartWaitDuration | Tempo em segundos, o padrão é 60 minutos| Especifique o intervalo de tempo em segundos. O ReplicaRestartWaitDuration para UpgradeOrchestrationService. |
| QuorumLossWaitDuration | Tempo em segundos, o padrão é MaxValue | Especifique o intervalo de tempo em segundos. O QuorumLossWaitDuration para UpgradeOrchestrationService. |
| StandByReplicaKeepDuration | Tempo em segundos, o padrão é 60*24*7 minutos | Especifique o intervalo de tempo em segundos. O StandByReplicaKeepDuration para UpgradeOrchestrationService. |
| PlacementConstraints | cadeia de caracteres, o padrão é "" | O PlacementConstraints para UpgradeOrchestrationService. |
| AutoupgradeEnabled | Bool, o padrão é true | Sondagem automática e ação de atualização baseadas em um arquivo de estado de meta. |
| UpgradeApprovalRequired | Bool, o padrão é false | Configuração para fazer a atualização de código exigir aprovação do administrador antes de continuar. |

## <a name="upgradeservice"></a>UpgradeService
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| PlacementConstraints |cadeia de caracteres, o padrão é "" |O PlacementConstraints para o serviço de atualização. |
| TargetReplicaSetSize | Int, o padrão é 3 | O TargetReplicaSetSize para UpgradeService. |
| MinReplicaSetSize | Int, o padrão é 2 | O MinReplicaSetSize para UpgradeService. |
| CoordinatorType | cadeia de caracteres, o padrão é "WUTest"| O CoordinatorType para UpgradeService. |
| BaseUrl | cadeia de caracteres, o padrão é "" |BaseUrl para UpgradeService. |
| ClusterId | cadeia de caracteres, o padrão é "" | ClusterId para UpgradeService. |
| X509StoreName | cadeia de caracteres, o padrão é "My"| X509StoreName para UpgradeService. |
| X509StoreLocation | cadeia de caracteres, o padrão é "" | X509StoreLocation para UpgradeService. |
| X509FindType | cadeia de caracteres, o padrão é ""| X509FindType para UpgradeService. |
| X509FindValue | cadeia de caracteres, o padrão é "" | X509FindValue para UpgradeService. |
| X509SecondaryFindValue | cadeia de caracteres, o padrão é "" | X509SecondaryFindValue para UpgradeService. |
| OnlyBaseUpgrade | Bool, o padrão é false | OnlyBaseUpgrade para UpgradeService. |
| TestCabFolder | cadeia de caracteres, o padrão é "" | TestCabFolder para UpgradeService. |

## <a name="securityclientaccess"></a>Security/ClientAccess
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| CreateName |cadeia de caracteres, o padrão é "Admin" |Configuração de segurança para a criação do URI de nomenclatura. |
| DeleteName |cadeia de caracteres, o padrão é "Admin" |Configuração de segurança para a exclusão do URI de nomenclatura. |
| PropertyWriteBatch |cadeia de caracteres, o padrão é "Admin" |Configuração de segurança para operações de gravação da propriedade de Nomenclatura. |
| CreateService |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para a criação do serviço. |
| CreateServiceFromTemplate |cadeia de caracteres, o padrão é "Admin" |Configuração de segurança para a criação do serviço com base no modelo. |
| UpdateService |cadeia de caracteres, o padrão é "Admin" |Configuração de segurança para atualizações de serviço. |
| DeleteService  |cadeia de caracteres, o padrão é "Admin" |Configuração de segurança para a exclusão do serviço. |
| ProvisionApplicationType |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para provisionamento de tipo de aplicativo. |
| CreateApplication |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para a criação de aplicativos. |
| DeleteApplication |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para a exclusão de aplicativos. |
| UpgradeApplication |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para iniciar ou interromper atualizações do aplicativo. |
| RollbackApplicationUpgrade |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para reverter atualizações do aplicativo. |
| UnprovisionApplicationType |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para desprovisionamento de tipo de aplicativo. |
| MoveNextUpgradeDomain |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para retomar as atualizações do aplicativo com um domínio de atualização explícito. |
| ReportUpgradeHealth |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para retomar as atualizações do aplicativo com o andamento da atualização atual. |
| ReportHealth |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para relatar integridade. |
| ProvisionFabric |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para MSI e/ou provisionamento de manifesto do cluster. |
| UpgradeFabric |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para iniciar atualizações do cluster. |
| RollbackFabricUpgrade |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para reverter atualizações do cluster. |
| UnprovisionFabric |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para MSI e/ou desprovisionamento de manifesto do cluster. |
| MoveNextFabricUpgradeDomain |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para retomar as atualizações do cluster com um domínio de atualização explícito. |
| ReportFabricUpgradeHealth |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para retomar as atualizações do cluster com o andamento da atualização atual. |
| StartInfrastructureTask |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para iniciar tarefas de infraestrutura. |
| FinishInfrastructureTask |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para encerrar tarefas de infraestrutura. |
| ActivateNode |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para ativar um nó. |
| DeactivateNode |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para desativar um nó. |
| DeactivateNodesBatch |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para desativar vários nós. |
| RemoveNodeDeactivations |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para reverter a desativação de vários nós. |
| GetNodeDeactivationStatus |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para verificar o status de desativação. |
| NodeStateRemoved |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para relatar o estado do nó removido. |
| RecoverPartition |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para recuperar uma partição. |
| RecoverPartitions |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para recuperar partições. |
| RecoverServicePartitions |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para recuperar partições de serviço. |
| RecoverSystemPartitions |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para recuperar partições de serviço de sistema. |
| ReportFault |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para falha de relatório. |
| InvokeInfrastructureCommand |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para comandos de gerenciamento de tarefa de infraestrutura. |
| FileContent |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para transferência do arquivo do cliente do repositório de imagens (externo ao cluster). |
| FileDownload |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para iniciação do download do arquivo do cliente do repositório de imagens (externo ao cluster). |
| InternalList |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para operação da lista de arquivos do cliente do repositório de imagens (interno). |
| Excluir |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para operação de exclusão do repositório de imagens. |
| Carregar |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para operação de upload do repositório de imagens. |
| GetStagingLocation |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para recuperação do local de preparo do cliente do repositório de imagens. |
| GetStoreLocation |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para recuperação do local de repositório do cliente do repositório de imagens. |
| NodeControl |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para iniciar, interromper e reiniciar nós. |
| CodePackageControl |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para reiniciar pacotes de códigos. |
| UnreliableTransportControl |cadeia de caracteres, o padrão é "Admin" | Transporte não confiável para adicionar e remover comportamentos. |
| MoveReplicaControl |cadeia de caracteres, o padrão é "Admin" | Mova a réplica. |
| PredeployPackageToNode |cadeia de caracteres, o padrão é "Admin" | Predeployment api. |
| StartPartitionDataLoss |cadeia de caracteres, o padrão é "Admin" | Induz a perda de dados em uma partição. |
| StartPartitionQuorumLoss |cadeia de caracteres, o padrão é "Admin" | Induz a perda de quorum em uma partição. |
| StartPartitionRestart |cadeia de caracteres, o padrão é "Admin" | Reinicia simultaneamente algumas ou todas as réplicas de uma partição. |
| CancelTestCommand |cadeia de caracteres, o padrão é "Admin" | Cancelará um TestCommand específico se ele estiver em trânsito. |
| StartChaos |cadeia de caracteres, o padrão é "Admin" | Iniciará o caos se ele não tiver sido iniciado ainda. |
| StopChaos |cadeia de caracteres, o padrão é "Admin" | Interromperá o caos se ele já tiver sido iniciado. |
| StartNodeTransition |cadeia de caracteres, o padrão é "Admin" | Configuração de segurança para iniciar a transição de um nó. |
| StartClusterConfigurationUpgrade |cadeia de caracteres, o padrão é "Admin" | Induz StartClusterConfigurationUpgrade em uma partição. |
| GetUpgradesPendingApproval |cadeia de caracteres, o padrão é "Admin" | Induz GetUpgradesPendingApproval em uma partição. |
| StartApprovedUpgrades |cadeia de caracteres, o padrão é "Admin" | Induz StartApprovedUpgrades em uma partição. |
| Ping |cadeia de caracteres, o padrão é "Admin\|\|User" | Configurações de segurança para pings de clientes. |
| Consultar |cadeia de caracteres, o padrão é "Admin\|\|User" | Configuração de segurança para consultas. |
| NameExists |cadeia de caracteres, o padrão é "Admin\|\|User" | Configuração de segurança para verificações de existência do URI de Nomenclatura. |
| EnumerateSubnames |cadeia de caracteres, o padrão é "Admin\|\|User" | Configuração de segurança para a enumeração do URI de Nomenclatura. |
| EnumerateProperties |cadeia de caracteres, o padrão é "Admin\|\|User" | Configuração de segurança para a enumeração da propriedade de Nomenclatura. |
| PropertyReadBatch |cadeia de caracteres, o padrão é "Admin\|\|User" | Configuração de segurança para operações de leitura da propriedade de Nomenclatura. |
| GetServiceDescription |cadeia de caracteres, o padrão é "Admin\|\|User" | Configuração de segurança para notificações de serviço de sondagem longa e descrições de serviço de leitura. |
| ResolveService |cadeia de caracteres, o padrão é "Admin\|\|User" | Configuração de segurança para a resolução do serviço de reclamação. |
| ResolveNameOwner |cadeia de caracteres, o padrão é "Admin\|\|User" | Configuração de segurança para determinar o proprietário do URI de Nomenclatura. |
| ResolvePartition |cadeia de caracteres, o padrão é "Admin\|\|User" | Configuração de segurança para determinar os serviços de sistema. |
| ServiceNotifications |cadeia de caracteres, o padrão é "Admin\|\|User" | Configuração de segurança para notificações de serviço baseadas em evento. |
| PrefixResolveService |cadeia de caracteres, o padrão é "Admin\|\|User" | Configuração de segurança para resolução de prefixo do serviço de reclamação. |
| GetUpgradeStatus |cadeia de caracteres, o padrão é "Admin\|\|User" | Configuração de segurança para sondar o status de atualização do aplicativo. |
| GetFabricUpgradeStatus |cadeia de caracteres, o padrão é "Admin\|\|User" | Configuração de segurança para sondar o status de atualização do cluster. |
| InvokeInfrastructureQuery |cadeia de caracteres, o padrão é "Admin\|\|User" | Configuração de segurança para consultar tarefas de infraestrutura. |
| Listar |cadeia de caracteres, o padrão é "Admin\|\|User" | Configuração de segurança para operação da lista de arquivos do cliente do repositório de imagens. |
| ResetPartitionLoad |cadeia de caracteres, o padrão é "Admin\|\|User" | Configuração de segurança para carga de redefinição para um failoverUnit. |
| ToggleVerboseServicePlacementHealthReporting | cadeia de caracteres, o padrão é "Admin\|\|User" | Configuração de segurança para alternar o ServicePlacement HealthReporting detalhado. |
| GetPartitionDataLossProgress | cadeia de caracteres, o padrão é "Admin\|\|User" | Busca o andamento de uma chamada à API de perda de dados de invocação. |
| GetPartitionQuorumLossProgress | cadeia de caracteres, o padrão é "Admin\|\|User" | Busca o andamento de uma chamada à API de perda de quorum de invocação. |
| GetPartitionRestartProgress | cadeia de caracteres, o padrão é "Admin\|\|User" | Busca o andamento de uma chamada à API de partição de redefinição. |
| GetChaosReport | cadeia de caracteres, o padrão é "Admin\|\|User" | Busca o status de caos dentro de um determinado intervalo de tempo. |
| GetNodeTransitionProgress | cadeia de caracteres, o padrão é "Admin\|\|User" | Configuração de segurança para obter o andamento em um comando de transição do nó. |
| GetClusterConfigurationUpgradeStatus | cadeia de caracteres, o padrão é "Admin\|\|User" | Induz GetClusterConfigurationUpgradeStatus em uma partição. |
| GetClusterConfiguration | cadeia de caracteres, o padrão é "Admin\|\|User" | Induz GetClusterConfiguration em uma partição. |

## <a name="reconfigurationagent"></a>ReconfigurationAgent
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| ApplicationUpgradeMaxReplicaCloseDuration | Tempo em segundos, o padrão é 900 |Especifique o intervalo de tempo em segundos. A duração para a qual o sistema aguardará antes de encerrar os hosts de serviço que têm réplicas presas no fechamento. |
| ServiceApiHealthDuration | Tempo em segundos, o padrão é 30 minutos | Especifique o intervalo de tempo em segundos. ServiceApiHealthDuration define por quanto tempo é preciso esperar para que uma API de serviço seja executada antes que ela seja comunicada como não íntegra. |
| ServiceReconfigurationApiHealthDuration | Tempo em segundos, o padrão é de 30 | Especifique o intervalo de tempo em segundos. ServiceReconfigurationApiHealthDuration define quanto tempo antes que um serviço em reconfiguração seja comunicado como não íntegro. |
| PeriodicApiSlowTraceInterval | Tempo em segundos, o padrão é 5 minutos | Especifique o intervalo de tempo em segundos. PeriodicApiSlowTraceInterval define o intervalo durante o qual as chamadas à API lentas serão retraçadas pelo monitor da API. |
| NodeDeactivationMaxReplicaCloseDuration | Tempo em segundos, o padrão é 900 | Especifique o intervalo de tempo em segundos. O tempo máximo de espera antes de encerrar um host de serviço que está bloqueando a desativação do nó. |
| FabricUpgradeMaxReplicaCloseDuration | Tempo em segundos, o padrão é 900 | Especifique o intervalo de tempo em segundos. A duração máxima que o RA aguardará antes de encerrar o host de serviço da réplica que não está sendo fechada. |
| IsDeactivationInfoEnabled | Bool, o padrão é true | Determina se o RA usará as informações de desativação para realizar a reeleição primária Para novos clusters, essa configuração deve ser definida como true Para clusters existentes que estão sendo atualizados, consulte as notas de versão sobre como habilitar isso. |

## <a name="placementandloadbalancing"></a>PlacementAndLoadBalancing
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| TraceCRMReasons |Bool, o padrão é true |Especifica se os motivos devem ser rastreados para movimentos emitidos pelo CRM para o canal de eventos operacionais. |
| ValidatePlacementConstraint | Bool, o padrão é true | Especifica se a expressão PlacementConstraint para um serviço é validada ou não quando um ServiceDescription do serviço é atualizado. |
| PlacementConstraintValidationCacheSize | Int, o padrão é 10000 | Limita o tamanho da tabela usada para rápida validação e cache de Expressões de Restrição de Posicionamento. |
|VerboseHealthReportLimit | Int, o padrão é 20 | Define o número de vezes que uma réplica precisa estar não alocada antes que um aviso de integridade seja comunicado para ela (caso o relatório de integridade detalhado esteja habilitado). |
|ConstraintViolationHealthReportLimit | Int, o padrão é 50 | Define o número de vezes que a réplica de violação de restrição precisa ser persistentemente não fixa antes que os diagnósticos sejam realizados e os relatórios de integridade sejam emitidos. |
|DetailedConstraintViolationHealthReportLimit | Int, o padrão é 200 | Define o número de vezes que a réplica de violação de restrição precisa ser persistentemente não fixa antes que os diagnósticos sejam realizados e os relatórios detalhados de integridade sejam emitidos. |
|DetailedVerboseHealthReportLimit | Int, o padrão é 200 | Define o número de vezes que uma réplica não alocada tem que ser persistentemente não alocada antes que os relatórios detalhados de integridade sejam emitidos. |
|ConsecutiveDroppedMovementsHealthReportLimit | Int, o padrão é 20 | Define o número de vezes consecutivas que os movimentos emitidos por ResourceBalancer são soltos antes que o diagnóstico seja realizado e os avisos de integridade sejam emitidos. Negativo: nenhum aviso emitido sob essa condição. |
|DetailedNodeListLimit | Int, o padrão é 15 | Define o número de nós por restrição a serem incluídos antes do truncamento nos relatórios de Réplica Não Alocada. |
|DetailedPartitionListLimit | Int, o padrão é 15 | Define o número de partições por entrada de diagnóstico para uma restrição a serem incluídas antes do truncamento em Diagnósticos. |
|DetailedDiagnosticsInfoListLimit | Int, o padrão é 15 | Define o número de entradas de diagnóstico (com informações detalhadas) por restrição a serem incluídas antes do truncamento em Diagnósticos.|
|PLBRefreshGap | Tempo em segundos, o padrão é 1 | Especifique o intervalo de tempo em segundos. Define a quantidade mínima de tempo que deve passar antes que o PLB atualize o estado novamente. |
|MinPlacementInterval | Tempo em segundos, o padrão é 1 | Especifique o intervalo de tempo em segundos. Define a quantidade mínima de tempo que deve passar antes de dois turnos de posicionamento consecutivos. |
|MinConstraintCheckInterval | Tempo em segundos, o padrão é 1 | Especifique o intervalo de tempo em segundos. Define a quantidade mínima de tempo que deve passar antes de dois turnos de verificação de restrição consecutivos. |
|MinLoadBalancingInterval | Tempo em segundos, o padrão é 5 | Especifique o intervalo de tempo em segundos. Define a quantidade mínima de tempo que deve passar antes de dois turnos de balanceamento consecutivos. |
|BalancingDelayAfterNodeDown | Tempo em segundos, o padrão é 120 |Especifique o intervalo de tempo em segundos. Não inicie atividades de balanceamento nesse período depois de um evento de nó inativo. |
|BalancingDelayAfterNewNode | Tempo em segundos, o padrão é 120 |Especifique o intervalo de tempo em segundos. Não inicie atividades de balanceamento nesse período depois de adicionar um novo nó. |
|ConstraintFixPartialDelayAfterNodeDown | Tempo em segundos, o padrão é 120 | Especifique o intervalo de tempo em segundos. Não corrija violações de restrição FaultDomain e UpgradeDomain nesse período depois de um evento de nó inativo. |
|ConstraintFixPartialDelayAfterNewNode | Tempo em segundos, o padrão é 120 | Especifique o intervalo de tempo em segundos. Não corrija as violações de restrições FaultDomain e UpgradeDomain nesse período depois de adicionar um novo nó. |
|GlobalMovementThrottleThreshold | Uint, o padrão é 1000 | Número máximo de movimentos permitidos na fase de balanceamento no último intervalo indicado por GlobalMovementThrottleCountingInterval. |
|GlobalMovementThrottleThresholdForPlacement | Uint, o padrão é 0 | O número máximo de movimentos permitidos na fase de posicionamento no último intervalo indicado por GlobalMovementThrottleCountingInterval.0 não indica nenhum limite.|
|GlobalMovementThrottleThresholdForBalancing | Uint, o padrão é 0 | Número máximo de movimentos permitidos na fase de balanceamento no último intervalo indicado por GlobalMovementThrottleCountingInterval. 0 indica nenhum limite. |
|GlobalMovementThrottleCountingInterval | Tempo em segundos, o padrão é 600 | Especifique o intervalo de tempo em segundos. Indica o comprimento do intervalo passado para o qual rastrear movimentos de réplica por domínio (usado juntamente com GlobalMovementThrottleThreshold). Pode ser definido como 0 para ignorar a limitação global completamente. |
|MovementPerPartitionThrottleThreshold | Uint, o padrão é 50 | Nenhum movimento relacionado a balanceamento ocorrerá para uma partição se o número de movimentos relacionados a balanceamento para réplicas dessa partição tiver alcançado ou excedido MovementPerFailoverUnitThrottleThreshold no último intervalo indicado por MovementPerPartitionThrottleCountingInterval. |
|MovementPerPartitionThrottleCountingInterval | Tempo em segundos, o padrão é 600 | Especifique o intervalo de tempo em segundos. Indica o comprimento do intervalo passado para o qual rastrear movimentos de réplica para cada partição (usado juntamente com MovementPerPartitionThrottleThreshold). |
|PlacementSearchTimeout | Tempo em segundos, o padrão é 0,5 | Especifique o intervalo de tempo em segundos. Ao posicionar os serviços, pesquise por, no máximo, essa duração antes de retornar um resultado. |
|UseMoveCostReports | Bool, o padrão é false | Instrui o LB a ignorar o elemento de custo da função de pontuação, resultando em uma quantidade de movimentos potencialmente grande para obter posicionamento balanceado. |
|PreventTransientOvercommit | Bool, o padrão é false | Determina se o PLB conta imediatamente com recursos que serão liberados pelos movimentos iniciados. Por padrão, o PLB pode iniciar movimentos para fora e para dentro no mesmo nó que pode criar compromissos excessivos transitórios. Definir esse parâmetro como true impedirá que esses tipos de compromissos excessivos e a desfragmentação sob demanda (conhecida como placementWithMove) sejam desabilitados. |
|InBuildThrottlingEnabled | Bool, o padrão é false | Determine se a limitação no build está habilitada. |
|InBuildThrottlingAssociatedMetric | cadeia de caracteres, o padrão é "" | O nome da métrica associada para essa limitação. |
|InBuildThrottlingGlobalMaxValue | Int, o padrão é 0 |O número máximo de réplicas no build permitidas globalmente. |
|SwapPrimaryThrottlingEnabled | Bool, o padrão é false| Determine se a limitação de troca primária está habilitada. |
|SwapPrimaryThrottlingAssociatedMetric | cadeia de caracteres, o padrão é ""| O nome da métrica associada para essa limitação. |
|SwapPrimaryThrottlingGlobalMaxValue | Int, o padrão é 0 | O número máximo de réplicas de troca primárias permitidas globalmente. |
|PlacementConstraintPriority | Int, o padrão é 0 | Determina a prioridade da restrição de posicionamento: 0: rígida; 1: flexível; negativa: ignorar. |
|PreferredLocationConstraintPriority | Int, o padrão é 2| Determina a prioridade da restrição de local preferencial: 0: Rígida; 1: flexível; 2: Otimização; negativo: Ignorar |
|CapacityConstraintPriority | Int, o padrão é 0 | Determina a prioridade da restrição de capacidade: 0: difícil; 1: flexível; negativo: ignorar. |
|AffinityConstraintPriority | Int, o padrão é 0 | Determina a prioridade da restrição de afinidade: 0: rígida; 1: flexível; negativa: ignorar. |
|FaultDomainConstraintPriority | Int, o padrão é 0 | Determina a prioridade da restrição de domínio de falha: 0: rígida; 1: flexível; negativa: ignorar. |
|UpgradeDomainConstraintPriority | Int, o padrão é 1| Determina a prioridade da restrição de domínio de atualização: 0: rígida; 1: flexível; negativa: ignorar. |
|ScaleoutCountConstraintPriority | Int, o padrão é 0 | Determina a prioridade da restrição de contagem de dimensionamento: 0: rígida; 1: flexível; negativa: ignorar. |
|ApplicationCapacityConstraintPriority | Int, o padrão é 0 | Determina a prioridade da restrição de capacidade: 0: difícil; 1: flexível; negativo: ignorar. |
|MoveParentToFixAffinityViolation | Bool, o padrão é false | Configuração que determina se as réplicas pai podem ser movidas para corrigir restrições de afinidade.|
|MoveExistingReplicaForPlacement | Bool, o padrão é true |Configuração que determina se a réplica existente será movida durante o posicionamento. |
|UseSeparateSecondaryLoad | Bool, o padrão é true | Configuração que determina se uma carga secundária diferente será usada. |
|PlaceChildWithoutParent | Bool, o padrão é true | Configuração que determinará se a réplica de serviço filho poderá ser posicionada se nenhuma réplica pai estiver ativa. |
|PartiallyPlaceServices | Bool, o padrão é true | Determina se todas as réplicas de serviço no cluster serão posicionadas em “tudo ou nada” dados os nós adequados limitados para elas.|
|InterruptBalancingForAllFailoverUnitUpdates | Bool, o padrão é false | Determina se qualquer tipo de atualização de unidade de failover deve interromper a execução de balanceamento rápida ou lenta. Com o balanceamento “false” especificado, a execução será interrompida se FailoverUnit: for criado/excluído; tiver réplicas ausentes; o local de réplica primário tiver sido alterado ou a quantidade de réplicas tiver sido alterada. A execução de balanceamento NÃO será interrompida em outros casos – se FailoverUnit: tiver réplicas extras; qualquer outro sinalizador de réplica tiver sido alterado; somente a versão de partição ou qualquer outro caso tiver sido alterado. |

## <a name="security"></a>Segurança
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| ClusterProtectionLevel |None ou EncryptAndSign |None (padrão) para clusters não seguros, EncryptAndSign para clusters seguros. |

## <a name="hosting"></a>Hosting
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| ServiceTypeRegistrationTimeout |Tempo em segundos, o padrão é de 300 |Tempo máximo permitido para que o ServiceType seja registrado na malha |
| ServiceTypeDisableFailureThreshold |Número inteiro, o padrão é 1 |Esse é o limite para a contagem de falhas após o qual o FailoverManager (FM) é notificado para desabilitar o tipo de serviço nesse nó e tentar um nó diferente para posicionamento. |
| ActivationRetryBackoffInterval |Tempo em segundos, o padrão é de 5 |Intervalo de retirada em cada falha de ativação; em cada falha de ativação contínua, o sistema tenta novamente realizar a ativação até a MaxActivationFailureCount. O intervalo de repetição em cada tentativa é um produto da falha de ativação contínua e do intervalo de retirada de ativação. |
| ActivationMaxRetryInterval |Tempo em segundos, o padrão é de 300 |Em cada falha de ativação contínua, o sistema tenta novamente realizar a ativação até a ActivationMaxFailureCount. ActivationMaxRetryInterval especifica o intervalo de tempo de espera antes de tentar novamente após cada falha de ativação |
| ActivationMaxFailureCount |Número inteiro, o padrão é 10 |Número de vezes que o sistema tenta realizar novamente a ativação que falhou antes de desistir |

## <a name="failovermanager"></a>FailoverManager
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| PeriodicLoadPersistInterval |Tempo em segundos, o padrão é de 10 |Isso determina a frequência com que o FM verifica se há novos relatórios de carregamento |

## <a name="federation"></a>Federação
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| LeaseDuration |Tempo em segundos, o padrão é de 30 |Duração de uma concessão entre um nó e seus vizinhos. |
| LeaseDurationAcrossFaultDomain |Tempo em segundos, o padrão é de 30 |Duração de uma concessão entre um nó e seus vizinhos entre domínios de falha. |

## <a name="clustermanager"></a>ClusterManager
| **Parâmetro** | **Valores permitidos** | **Diretrizes ou Descrição resumida** |
| --- | --- | --- |
| UpgradeStatusPollInterval |Tempo em segundos, o padrão é de 60 |A frequência de sondagem do status de atualização do aplicativo. Esse valor determina a taxa de atualização para qualquer chamada GetApplicationUpgradeProgress |
| UpgradeHealthCheckInterval |Tempo em segundos, o padrão é de 60 |A frequência de verificação do status de integridade durante atualizações de um aplicativo monitorado |
| FabricUpgradeStatusPollInterval |Tempo em segundos, o padrão é de 60 |A frequência de sondagem do status de atualização de malha. Esse valor determina a taxa de atualização para qualquer chamada GetFabricUpgradeProgress |
| FabricUpgradeHealthCheckInterval |Tempo em segundos, o padrão é de 60 |A frequência de verificação do status de integridade durante uma atualização monitorada da Malha |
|InfrastructureTaskProcessingInterval | Tempo em segundos, o padrão é de 10 |Especifique o intervalo de tempo em segundos. O intervalo de processamento usado pela tarefa de infraestrutura que está processando o computador de estado. |
|TargetReplicaSetSize |Int, o padrão é 7 |O TargetReplicaSetSize para ClusterManager. |
|MinReplicaSetSize |Int, o padrão é 3 |O MinReplicaSetSize para ClusterManager. |
|ReplicaRestartWaitDuration |Tempo em segundos, o padrão é (60,0 * 30)|Especifique o intervalo de tempo em segundos. O ReplicaRestartWaitDuration para ClusterManager. |
|QuorumLossWaitDuration |Tempo em segundos, o padrão é MaxValue | Especifique o intervalo de tempo em segundos. O QuorumLossWaitDuration para ClusterManager. |
|StandByReplicaKeepDuration | Tempo em segundos, o padrão é (3600,0 * 2)|Especifique o intervalo de tempo em segundos. O StandByReplicaKeepDuration para ClusterManager. |
|PlacementConstraints | cadeia de caracteres, o padrão é "" |O PlacementConstraints para ClusterManager. |
|SkipRollbackUpdateDefaultService | Bool, o padrão é false |O CM ignorará a inversão dos serviços padrão atualizados durante a reversão da atualização do aplicativo. |
|EnableDefaultServicesUpgrade | Bool, o padrão é false |Habilite a atualização de serviços padrão durante a atualização do aplicativo. Descrições de serviço padrão serão substituídas após a atualização. |
|InfrastructureTaskHealthCheckWaitDuration |Tempo em segundos, o padrão é 0| Especifique o intervalo de tempo em segundos. A quantidade de tempo de espera antes de iniciar verificações de integridade após pós-processar uma tarefa de infraestrutura. |
|InfrastructureTaskHealthCheckStableDuration | Tempo em segundos, o padrão é 0| Especifique o intervalo de tempo em segundos. A quantidade de tempo para observar verificações de integridade passadas consecutivas antes que o pós-processamento de uma tarefa de infraestrutura seja encerrado com êxito. Observar uma verificação de integridade com falha redefinirá esse temporizador. |
|InfrastructureTaskHealthCheckRetryTimeout | Tempo em segundos, o padrão é de 60 |Especifique o intervalo de tempo em segundos. A quantidade de tempo para tentar novamente verificações de integridade com falha ao pós-processar uma tarefa de infraestrutura. Observar uma verificação de integridade aprovada redefinirá esse temporizador. |
|ImageBuilderTimeoutBuffer |Tempo em segundos, o padrão é 3 |Especifique o intervalo de tempo em segundos. A quantidade de tempo para permitir que erros de tempo limite específico do Image Builder para retornar ao cliente. Se esse buffer for pequeno demais, então o cliente atingirá o tempo limite antes do servidor e receberá um erro de tempo limite genérico. |
|MinOperationTimeout | Tempo em segundos, o padrão é de 60 |Especifique o intervalo de tempo em segundos. O tempo limite global mínimo para processar internamente operações no ClusterManager. |
|MaxOperationTimeout |Tempo em segundos, o padrão é MaxValue | Especifique o intervalo de tempo em segundos. O tempo limite global máximo para processar internamente operações no ClusterManager. |
|MaxTimeoutRetryBuffer | Tempo em segundos, o padrão é 600 |Especifique o intervalo de tempo em segundos. O tempo limite máximo da operação ao tentar novamente internamente devido a tempos limite é <Original Timeout> + <MaxTimeoutRetryBuffer>. Tempo limite adicional é adicionado em incrementos de MinOperationTimeout. |
|MaxCommunicationTimeout |Tempo em segundos, o padrão é 600 |Especifique o intervalo de tempo em segundos. O tempo limite máximo para comunicações internas entre ClusterManager e outros serviços do sistema (ou seja, Serviço de Cadastramento; Gerenciador de Failover e etc). Esse tempo limite deve ser menor que MaxOperationTimeout global (como pode haver várias comunicações entre componentes do sistema para cada operação de cliente). |
|MaxDataMigrationTimeout |Tempo em segundos, o padrão é 600 |Especifique o intervalo de tempo em segundos. O tempo limite máximo para operações de recuperação de migração de dados depois que ocorreu uma atualização do Fabric. |
|MaxOperationRetryDelay |Tempo em segundos, o padrão é 5| Especifique o intervalo de tempo em segundos. O atraso máximo para as repetições internas quando são encontradas falhas. |
|ReplicaSetCheckTimeoutRollbackOverride |Tempo em segundos, o padrão é 1200 | Especifique o intervalo de tempo em segundos. Se ReplicaSetCheckTimeout for definido como o valor máximo de DWORD, ele será substituído pelo valor dessa configuração para fins de reversão. O valor usado para avanço nunca é substituído. |
|ImageBuilderJobQueueThrottle |Int, o padrão é 10 |Restrição de contagem de threads para a fila de trabalho do proxy do Image Builder em solicitações do aplicativo. |

## <a name="next-steps"></a>Próximas etapas
Leia estes artigos para obter mais informações sobre gerenciamento de cluster:

[Adicionar, transferir e remover certificados do cluster do Azure ](service-fabric-cluster-security-update-certs-azure.md) 

