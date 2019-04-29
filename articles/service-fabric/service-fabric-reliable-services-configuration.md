---
title: Configurar o Reliable Services do Azure Service Fabric | Microsoft Docs
description: Saiba como configurar o Reliable Services com estado no Service Fabric do Azure.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: chackdan
editor: vturecek
ms.assetid: 9f72373d-31dd-41e3-8504-6e0320a11f0e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/02/2017
ms.author: sumukhs
ms.openlocfilehash: 8ddb5d0566c57dd1d507d543ac53c0975a83dd43
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723530"
---
# <a name="configure-stateful-reliable-services"></a>Configurar serviços confiáveis com estado
Há dois conjuntos de definições de configuração para Reliable Services. Um conjunto é global para todos os Reliable Services no cluster, enquanto o outro conjunto é específico para um determinado Reliable Service.

## <a name="global-configuration"></a>Configuração Global
A configuração do Reliable Service global é especificada no manifesto do cluster para o cluster na seção KtlLogger. Ela permite a configuração do local e do tamanho do log compartilhado, além dos limites de memória global usados pelo agente. O manifesto do cluster é um arquivo XML individual que contém definições e configurações que se aplicam a todos os nós e serviços no cluster. O arquivo normalmente é chamado de ClusterManifest.xml. Você pode ver o manifesto do cluster do seu cluster usando o comando Get-ServiceFabricClusterManifest powershell.

### <a name="configuration-names"></a>Nomes da configuração
| NOME | Unidade | Valor padrão | Comentários |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Quilobytes |8388608 |O número mínimo de KB a ser alocado no modo kernel para o pool de memória do buffer de gravação do agente. Esse pool de memória é usado para armazenar em cache informações de estado antes da gravação no disco. |
| WriteBufferMemoryPoolMaximumInKB |Quilobytes |Sem limite |Tamanho máximo que o pool de memória do buffer de gravação do agente pode atingir. |
| SharedLogId |GUID |"" |Especifica um GUID exclusivo a ser usado para identificar o arquivo de log compartilhado padrão usado por todos os serviços confiáveis em todos os nós no cluster que não especificam SharedLogId na configuração específica de seu serviço. Se SharedLogId for especificada, SharedLogPath também deverá ser especificada. |
| SharedLogPath |Nome de caminho totalmente qualificado |"" |Especifica o caminho totalmente qualificado onde o arquivo de log compartilhado usado por todos os serviços confiáveis em todos os nós no cluster que não especificam o SharedLogPath na configuração específica de seu serviço. No entanto, se SharedLogPath for especificado, SharedLogId também deverá ser especificado. |
| SharedLogSizeInMB |Megabytes |8192 |Especifica o número de MB do espaço em disco a ser alocado estatisticamente para o log compartilhado. O valor deve ser de 2048 ou superior. |

No Azure ARM ou no modelo JSON de local, o exemplo a seguir mostra como alterar o log de transações compartilhado que é criado para dar apoio às coleções confiáveis para serviços com estado.

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="sample-local-developer-cluster-manifest-section"></a>Exemplo de seção de manifesto do cluster de desenvolvedor local
Se você quiser alterar isso em seu ambiente de desenvolvimento local, edite o arquivo clustermanifest.xml local.

```xml
   <Section Name="KtlLogger">
     <Parameter Name="SharedLogSizeInMB" Value="4096"/>
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
   </Section>
```

### <a name="remarks"></a>Comentários
O agente tem um pool de memória global alocado da memória kernel não paginada que está disponível para todos os serviços confiáveis em um nó para armazenar em cache os dados de estado antes de eles serem gravados no log dedicado associado à réplica de serviço confiável. O tamanho do pool é controlado pelas configurações WriteBufferMemoryPoolMinimumInKB e WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB especifica o tamanho inicial desse pool de memória e o menor tamanho para o qual o pool de memória pode ser reduzido. WriteBufferMemoryPoolMaximumInKB é o maior tamanho que o pool de memória pode atingir. Cada réplica do serviço confiável que é aberta pode aumentar o tamanho do pool de memória em uma quantidade determinada pelo sistema até WriteBufferMemoryPoolMaximumInKB. Se houver mais demanda de memória do pool do que está disponível, as solicitações de memória serão atrasadas até haver memória disponível. Portanto, se o pool de memória do buffer de gravação for muito pequeno para uma determinada configuração, o desempenho poderá ser afetado.

As configurações SharedLogId e SharedLogPath sempre são usadas juntas para definir o GUID e o local para o log compartilhado padrão de todos os nós no cluster. O log compartilhado padrão é usado para todos os serviços confiáveis que não especificam as configurações em settings.xml para o serviço específico. Para melhor desempenho, os arquivos de log compartilhados devem ser colocados em discos usados exclusivamente para o arquivo de log compartilhado para reduzir a contenção.

SharedLogSizeInMB especifica a quantidade de espaço em disco a ser pré-alocada para o log compartilhado padrão em todos os nós.  SharedLogId e SharedLogPath não precisam ser especificadas para que SharedLogSizeInMB seja especificada.

## <a name="service-specific-configuration"></a>Configuração específica de serviço
A Configuração padrão dos Reliable Services com estado pode ser modificada por meio do pacote de configuração (Config) ou da implementação do serviço (código).

* **Config** – a configuração por meio do pacote de configuração é feita alterando o arquivo "Settings.xml" gerado na raiz do pacote do Microsoft Visual Studio sob a pasta "Config" para cada serviço no aplicativo.
* **Código** – A configuração via código é feita com a criação de um ReliableStateManager usando um objeto ReliableStateManagerConfiguration com o conjunto de opções apropriadas.

Por padrão, o tempo de execução do Service Fabric do Azure procura nomes de seção predefinidos no arquivo "Settings.xml" e consome os valores de configuração ao criar os componentes de tempo de execução subjacentes.

> [!NOTE]
> **Não** exclua os nomes de seção das configurações a seguir no arquivo Settings.xml gerado na solução do Visual Studio, a menos que pretenda configurar o serviço via código.
> Renomear os nomes de pacote ou a seção de configuração exigirá uma alteração de código ao configurar o ReliableStateManager.
> 
> 

### <a name="replicator-security-configuration"></a>Configuração de segurança do replicador
As configurações de segurança do replicador servem para proteger o canal de comunicação que é usado durante a replicação. Isso significa que os serviços não poderão ver o tráfego de replicação uns dos outros, garantindo que os dados com alta disponibilidade também estejam seguros. Por padrão, uma seção de configuração de segurança vazia evita a segurança de replicação.

> [!IMPORTANT]
> Em nós Linux, certificados devem estar formatados como PEM. Para saber mais sobre como localizar e configurar certificados para Linux, consulte [Configurar certificados em Linux](./service-fabric-configure-certificates-linux.md). 
> 
> 

### <a name="default-section-name"></a>Nome padrão da seção
ReplicatorSecurityConfig

> [!NOTE]
> Para alterar o nome da seção, substitua o parâmetro replicatorSecuritySectionName pelo construtor ReliableStateManagerConfiguration ao criar ReliableStateManager para este serviço.
> 
> 

### <a name="replicator-configuration"></a>Configuração do replicador
As configurações do replicador configuram o replicador que é responsável por tornar o estado do Reliable Service com estado altamente confiável por meio da replicação e da persistência do estado localmente.
A configuração padrão é gerada pelo modelo do Visual Studio e deve ser suficiente. Esta seção fala sobre configurações adicionais que estão disponíveis para ajustar o replicador.

### <a name="default-section-name"></a>Nome padrão da seção
ReplicatorConfig

> [!NOTE]
> Para alterar o nome da seção, substitua o parâmetro replicatorSettingsSectionName pelo construtor ReliableStateManagerConfiguration ao criar ReliableStateManager para este serviço.
> 
> 

### <a name="configuration-names"></a>Nomes da configuração
| NOME | Unidade | Valor padrão | Comentários |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Segundos |0,015 |Período de tempo pelo qual o replicador no secundário espera após o recebimento de uma operação antes de enviar novamente uma confirmação ao primário. Todas as outras confirmações a serem enviadas para operações e processadas dentro deste intervalo são enviadas como uma única resposta. |
| ReplicatorEndpoint |N/D |Nenhum parâmetro padrão obrigatório |Endereço IP e porta que o replicador primário/secundário usará para se comunicar com outros replicadores no conjunto de réplicas. Eles devem fazer referência a um ponto de extremidade do recurso de TCP no manifesto do serviço. Consulte [Recursos do manifesto do serviço](service-fabric-service-manifest-resources.md) para saber mais sobre como definir os recursos de ponto de extremidade em um manifesto de serviço. |
| MaxPrimaryReplicationQueueSize |Número de operações |8192 |Número máximo de operações na fila principal. Uma operação é liberada depois que o replicador primário recebe uma confirmação de todos os replicadores secundários. Esse valor deve ser maior que 64 e uma potência de 2. |
| MaxSecondaryReplicationQueueSize |Número de operações |16384 |Número máximo de operações na fila secundária. Uma operação é liberada depois de tornar seu estado de altamente disponível por meio de persistência. Esse valor deve ser maior que 64 e uma potência de 2. |
| CheckpointThresholdInMB |MB |50 |Quantidade de espaço de arquivo de log depois que o estado é o ponto de verificação. |
| MaxRecordSizeInKB |KB |1024 |O maior tamanho de registro que o replicador pode gravar no log. Esse valor deve ser um múltiplo de 4 e maior que 16. |
| MinLogSizeInMB |MB |0 (sistema de determinado) |Tamanho mínimo do log transacional. O log não poderá truncar um tamanho abaixo dessa configuração. 0 indica que o replicador determinará o tamanho mínimo do log. Aumentar esse valor aumenta a possibilidade de fazer cópias parciais e backups incrementais, uma vez que as chances de registros de log relevantes sendo truncados são reduzidas. |
| TruncationThresholdFactor |Fator |2 |Determina em que tamanho do log o truncamento será disparado. O limite de truncamento é determinado pelo MinLogSizeInMB multiplicado por TruncationThresholdFactor. TruncationThresholdFactor deve ser maior que 1. MinLogSizeInMB * TruncationThresholdFactor deve ser menor que MaxStreamSizeInMB. |
| ThrottlingThresholdFactor |Fator |4 |Determina em que tamanho do log a réplica começará a ser limitada. O limite da limitação (em MB) é determinado por Max((MinLogSizeInMB * ThrottlingThresholdFactor),(CheckpointThresholdInMB * ThrottlingThresholdFactor)). O limite da limitação (em MB) deve ser maior que o limite de truncamento (em MB). O limite de truncamento (em MB) deve ser menor que MaxStreamSizeInMB. |
| MaxAccumulatedBackupLogSizeInMB |MB |800 |Tamanho máximo acumulado (em MB) de logs de backup em determinada cadeia de log de backup. Uma solicitação de backup incremental falharia se o backup incremental gerasse um log de backup que acarretasse em logs de backup acumulados, uma vez que o backup completo relevante seria maior do que esse tamanho. Nesses casos, o usuário deve fazer um backup completo. |
| SharedLogId |GUID |"" |Especifica um GUID exclusivo a ser usado para identificar o arquivo de log compartilhado usado com esta réplica. Normalmente, os serviços não devem usar essa configuração. No entanto, se SharedLogId for especificado, SharedLogPath também deverá ser especificado. |
| SharedLogPath |Nome de caminho totalmente qualificado |"" |Especifica o caminho totalmente qualificado onde o arquivo de log compartilhado para esta réplica será criado. Normalmente, os serviços não devem usar essa configuração. No entanto, se SharedLogPath for especificado, SharedLogId também deverá ser especificado. |
| SlowApiMonitoringDuration |Segundos |300 |Define o intervalo de monitoramento para as chamadas da API gerenciadas. Exemplo: o usuário fez o backup da função de callback. Após o intervalo, um relatório de integridade de aviso será enviado para o Gerenciador de Integridade. |
| LogTruncationIntervalSeconds |Segundos |0 |Intervalo configurável no qual o truncamento de log será iniciado em cada réplica. Ele é usado para garantir que o log também seja truncado com base no tempo em vez de apenas no tamanho do log. Essa configuração também força a eliminação de entradas excluídas no dicionário confiável. Por isso, ele pode ser usado para garantir que os itens excluídos sejam removidos em tempo hábil. |

### <a name="sample-configuration-via-code"></a>Amostra de configuração via código
```csharp
class Program
{
    /// <summary>
    /// This is the entry point of the service host process.
    /// </summary>
    static void Main()
    {
        ServiceRuntime.RegisterServiceAsync("HelloWorldStatefulType",
            context => new HelloWorldStateful(context, 
                new ReliableStateManager(context, 
        new ReliableStateManagerConfiguration(
                        new ReliableStateManagerReplicatorSettings()
            {
                RetryInterval = TimeSpan.FromSeconds(3)
                        }
            )))).GetAwaiter().GetResult();
    }
}    
```
```csharp
class MyStatefulService : StatefulService
{
    public MyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica stateManager)
        : base(context, stateManager)
    { }
    ...
}
```


### <a name="sample-configuration-file"></a>Arquivo de exemplo de configuração
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="512" />
   </Section>
   <Section Name="ReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```


### <a name="remarks"></a>Comentários
BatchAcknowledgementInterval controla a latência de replicação. Um valor '0' resulta na menor latência possível, ao custo de taxa de transferência (como mais mensagens de confirmação devem ser enviadas e processadas, cada uma contendo menos confirmações).
Quanto maior o valor para BatchAcknowledgementInterval, maior será a produtividade geral da replicação, ao custo da maior latência de operação. Isso se converte diretamente para a latência de confirmações de transações.

O valor para CheckpointThresholdInMB controla a quantidade de espaço em disco que o replicador pode usar para armazenar as informações de estado no arquivo de log dedicado da replicação. Aumentar isso para um valor maior que o padrão pode resultar em tempos de reconfiguração mais rápidos quando uma nova réplica é adicionada ao conjunto. Isso ocorre porque a transferência de estado parcial que ocorre devido à disponibilidade de mais histórico de operações no log. Potencialmente, isso pode aumentar o tempo de recuperação de uma réplica após uma falha.

A configuração MaxRecordSizeInKB define o tamanho máximo de um registro que pode ser gravado pelo replicador no arquivo de log. Na maioria dos casos, o tamanho padrão do registro de 1.024 KB é ideal. Porém, se o serviço estiver fazendo com que itens de dados maiores sejam parte das informações de estado, esse valor talvez precise ser aumentado. Há poucas vantagens em tornar o MaxRecordSizeInKB menor que 1.024, já que registros menores usam apenas o espaço necessário para o registro menor. A expectativa é de que esse valor precise ser alterado somente em casos raros.

As configurações de SharedLogId e SharedLogPath são sempre usadas juntas para fazer um serviço usar um log compartilhado separado do log compartilhado padrão para o nó. Para obter maior eficiência, devem ser especificados o máximo de serviços possível para o mesmo log compartilhado. Arquivos de log compartilhados devem ser colocados em discos que são usados exclusivamente para que o arquivo de log compartilhado, para reduzir a contenção de movimentação do cabeçote. A expectativa é de que esse valor precise ser alterado somente em casos raros.

## <a name="next-steps"></a>Próximas etapas
* [Depurar seu aplicativo do Service Fabric usando o Visual Studio](service-fabric-debugging-your-application.md)
* [Referência do desenvolvedor para Reliable Services](https://msdn.microsoft.com/library/azure/dn706529.aspx)

