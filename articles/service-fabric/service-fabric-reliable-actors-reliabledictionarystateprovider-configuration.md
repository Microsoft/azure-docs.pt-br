---
title: Alterar as configurações de ReliableDictionaryActorStateProvider nos Atores do Azure Service Fabric | Microsoft Docs
description: Saiba como configurar atores com monitoração de estado do Service Fabric do Azure do tipo ReliableDictionaryActorStateProvider.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: chackdan
editor: ''
ms.assetid: 79b48ffa-2474-4f1c-a857-3471f9590ded
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: 4e39357a765ec85aa64055b1aa422d8d7a01c116
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60727124"
---
# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Configurando Reliable Actors--ReliableDictionaryActorStateProvider
A configuração padrão do ReliableDictionaryActorStateProvider pode ser alterada modificando-se o arquivo settings.xml gerado na raiz pacote do Visual Studio dentro da pasta Config para o ator especificado.

O tempo de execução do Service Fabric do Azure procura nomes de seção predefinidos no arquivo settings.xml e consome os valores de configuração ao criar os componentes de tempo de execução subjacentes.

> [!NOTE]
> **Não** exclua/modifique os nomes de seção das configurações a seguir no arquivo settings.xml que é gerado na solução do Visual Studio.
> 
> 

Também há definições globais que afetam a configuração de ReliableDictionaryActorStateProvider.

## <a name="global-configuration"></a>Configuração global
A configuração global é especificada no manifesto do cluster para o cluster na seção KtlLogger. Ela permite a configuração do local e do tamanho do log compartilhado, além dos limites de memória global usados pelo agente. Observe que as alterações no manifesto do cluster afetam todos os serviços que usam ReliableDictionaryActorStateProvider e os serviços com estado confiáveis.

O manifesto do cluster é um arquivo XML individual que contém definições e configurações que se aplicam a todos os nós e os serviços no cluster. O arquivo normalmente é chamado de ClusterManifest.xml. Você pode ver o manifesto do cluster do seu cluster usando o comando Get-ServiceFabricClusterManifest powershell.

### <a name="configuration-names"></a>Nomes da configuração
| NOME | Unidade | Valor padrão | Comentários |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Quilobytes |8388608 |O número mínimo de KB a ser alocado no modo kernel para o pool de memória do buffer de gravação do agente. Esse pool de memória é usado para armazenar em cache informações de estado antes da gravação no disco. |
| WriteBufferMemoryPoolMaximumInKB |Quilobytes |Sem limite |Tamanho máximo que o pool de memória do buffer de gravação do agente pode atingir. |
| SharedLogId |GUID |"" |Especifica um GUID exclusivo a ser usado para identificar o arquivo de log compartilhado padrão usado por todos os serviços confiáveis em todos os nós no cluster que não especificam SharedLogId na configuração específica de seu serviço. Se SharedLogId for especificada, SharedLogPath também deverá ser especificada. |
| SharedLogPath |Nome de caminho totalmente qualificado |"" |Especifica o caminho totalmente qualificado onde o arquivo de log compartilhado usado por todos os serviços confiáveis em todos os nós no cluster que não especificam o SharedLogPath na configuração específica de seu serviço. No entanto, se SharedLogPath for especificado, SharedLogId também deverá ser especificado. |
| SharedLogSizeInMB |Megabytes |8192 |Especifica o número de MB do espaço em disco a ser alocado estatisticamente para o log compartilhado. O valor deve ser de 2048 ou superior. |

### <a name="sample-cluster-manifest-section"></a>Seção do exemplo do manifesto do cluster
```xml
   <Section Name="KtlLogger">
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
     <Parameter Name="SharedLogSizeInMB" Value="16383"/>
   </Section>
```

### <a name="remarks"></a>Comentários
O agente tem um pool de memória global alocado da memória kernel não paginada que está disponível para todos os serviços confiáveis em um nó para armazenar em cache os dados de estado antes de eles serem gravados no log dedicado associado à réplica de serviço confiável. O tamanho do pool é controlado pelas configurações WriteBufferMemoryPoolMinimumInKB e WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB especifica o tamanho inicial desse pool de memória e o menor tamanho para o qual o pool de memória pode ser reduzido. WriteBufferMemoryPoolMaximumInKB é o maior tamanho que o pool de memória pode atingir. Cada réplica do serviço confiável que é aberta pode aumentar o tamanho do pool de memória em uma quantidade determinada pelo sistema até WriteBufferMemoryPoolMaximumInKB. Se houver mais demanda de memória do pool do que está disponível, as solicitações de memória serão atrasadas até haver memória disponível. Portanto, se o pool de memória do buffer de gravação for muito pequeno para uma determinada configuração, o desempenho poderá ser afetado.

As configurações SharedLogId e SharedLogPath sempre são usadas juntas para definir o GUID e o local para o log compartilhado padrão de todos os nós no cluster. O log compartilhado padrão é usado para todos os serviços confiáveis que não especificam as configurações em settings.xml para o serviço específico. Para melhor desempenho, os arquivos de log compartilhados devem ser colocados em discos usados exclusivamente para o arquivo de log compartilhado para reduzir a contenção.

SharedLogSizeInMB especifica a quantidade de espaço em disco a ser pré-alocada para o log compartilhado padrão em todos os nós.  SharedLogId e SharedLogPath não precisam ser especificados para que SharedLogSizeInMB seja especificado.

## <a name="replicator-security-configuration"></a>Configuração de segurança do replicador
As configurações de segurança do replicador servem para proteger o canal de comunicação que é usado durante a replicação. Isso significa que os serviços não podem ver o tráfego de replicação uns dos outros, garantindo que os dados que têm alta disponibilidade também estejam seguros.
Por padrão, uma seção de configuração de segurança vazia evita a segurança de replicação.

> [!IMPORTANT]
> Em nós Linux, certificados devem estar formatados como PEM. Para saber mais sobre como localizar e configurar certificados para Linux, consulte [Configurar certificados em Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Nome da seção
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Configuração do replicador
As configurações do replicador servem para configurar o replicador que será responsável por tornar o Provedor de Estado do Ator altamente confiável por meio da replicação e persistência do estado localmente.
A configuração padrão é gerada pelo modelo do Visual Studio e deve ser suficiente. Esta seção fala sobre configurações adicionais que estão disponíveis para ajustar o replicador.

### <a name="section-name"></a>Nome da seção
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Nomes da configuração
| NOME | Unidade | Valor padrão | Comentários |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Segundos |0,015 |Período de tempo pelo qual o replicador no secundário espera após o recebimento de uma operação antes de enviar novamente uma confirmação ao primário. Todas as outras confirmações a serem enviadas para operações e processadas dentro deste intervalo são enviadas como uma única resposta. |
| ReplicatorEndpoint |N/D |Nenhum parâmetro padrão obrigatório |Endereço IP e porta que o replicador primário/secundário usará para se comunicar com outros replicadores no conjunto de réplicas. Eles devem fazer referência a um ponto de extremidade do recurso de TCP no manifesto do serviço. Veja [Recursos do manifesto do serviço](service-fabric-service-manifest-resources.md) para ler mais sobre como definir os recursos de ponto de extremidade no manifesto do serviço. |
| MaxReplicationMessageSize |Bytes |50 MB |Tamanho máximo de dados de replicação que podem ser transmitidos em uma única mensagem. |
| MaxPrimaryReplicationQueueSize |Número de operações |8192 |Número máximo de operações na fila principal. Uma operação é liberada depois que o replicador primário recebe uma confirmação de todos os replicadores secundários. Esse valor deve ser maior que 64 e uma potência de 2. |
| MaxSecondaryReplicationQueueSize |Número de operações |16384 |Número máximo de operações na fila secundária. Uma operação é liberada depois de tornar seu estado de altamente disponível por meio de persistência. Esse valor deve ser maior que 64 e uma potência de 2. |
| CheckpointThresholdInMB |MB |200 |Quantidade de espaço de arquivo de log depois que o estado é o ponto de verificação. |
| MaxRecordSizeInKB |KB |1024 |O maior tamanho de registro que o replicador pode gravar no log. Esse valor deve ser um múltiplo de 4 e maior que 16. |
| OptimizeLogForLowerDiskUsage |Boolean |verdadeiro |Quando verdadeiro, o log é configurado de forma que o arquivo de log dedicado da replicação seja criado usando um arquivo esparso do NTFS. Isso reduz o uso do espaço real em disco para o arquivo. Quando falso, o arquivo é criado com alocações fixas que oferecem o melhor desempenho de gravação. |
| SharedLogId |GUID |"" |Especifica um guid exclusivo a ser usado para identificar o arquivo de log compartilhado usado com esta réplica. Normalmente, os serviços não devem usar essa configuração. No entanto, se SharedLogId for especificado, SharedLogPath também deverá ser especificado. |
| SharedLogPath |Nome de caminho totalmente qualificado |"" |Especifica o caminho totalmente qualificado onde o arquivo de log compartilhado para esta réplica será criado. Normalmente, os serviços não devem usar essa configuração. No entanto, se SharedLogPath for especificado, SharedLogId também deverá ser especificado. |

## <a name="sample-configuration-file"></a>Arquivo de exemplo de configuração
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="180" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
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

## <a name="remarks"></a>Comentários
O parâmetro BatchAcknowledgementInterval controla a latência de replicação. Um valor '0' resulta na menor latência possível, ao custo de taxa de transferência (como mais mensagens de confirmação devem ser enviadas e processadas, cada uma contendo menos confirmações).
Quanto maior o valor para BatchAcknowledgementInterval, maior será a produtividade geral da replicação, ao custo da maior latência de operação. Isso se converte diretamente para a latência de confirmações de transações.

O parâmetro CheckpointThresholdInMB controla a quantidade de espaço em disco que o replicador pode usar para armazenar as informações de estado no arquivo de log dedicado da replicação. Aumentar isso para um valor maior que o padrão pode resultar em tempos de reconfiguração mais rápidos quando uma nova réplica é adicionada ao conjunto. Isso ocorre porque a transferência de estado parcial que ocorre devido à disponibilidade de mais histórico de operações no log. Potencialmente, isso pode aumentar o tempo de recuperação de uma réplica após uma falha.

Se você definir OptimizeForLowerDiskUsage como verdadeiro, o espaço do arquivo de log será excessivamente provisionado, de modo que réplicas ativas poderão armazenar mais informações de estado em seus arquivos de log enquanto réplicas inativas usarão menos espaço em disco. Isso torna possível hospedar mais réplicas em um nó. Se você definir OptimizeForLowerDiskUsage como falso, as informações de estado serão gravadas nos arquivos de log mais rapidamente.

A configuração MaxRecordSizeInKB define o tamanho máximo de um registro que pode ser gravado pelo replicador no arquivo de log. Na maioria dos casos, o tamanho padrão do registro de 1.024 KB é ideal. Porém, se o serviço estiver fazendo com que itens de dados maiores sejam parte das informações de estado, esse valor talvez precise ser aumentado. Há poucas vantagens em tornar o MaxRecordSizeInKB menor que 1.024, já que registros menores usam apenas o espaço necessário para o registro menor. A expectativa é de que esse valor precise ser alterado somente em casos raros.

As configurações de SharedLogId e SharedLogPath são sempre usadas juntas para fazer um serviço usar um log compartilhado separado do log compartilhado padrão para o nó. Para obter maior eficiência, devem ser especificados o máximo de serviços possível para o mesmo log compartilhado. Arquivos de log compartilhados devem ser colocados em discos que são usados exclusivamente para o arquivo de log compartilhado, para reduzir a contenção de movimentação do cabeçote. A expectativa é de que esses valores precisem ser alterados somente em casos raros.

