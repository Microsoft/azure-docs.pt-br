---
title: Alterar as configurações de KVSActorStateProvider nos Atores do Azure Service Fabric | Microsoft Docs
description: Saiba como configurar atores com monitoração de estado do Service Fabric do Azure do tipo KVSActorStateProvider.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: chackdan
editor: ''
ms.assetid: dbed72f4-dda5-4287-bd56-da492710cd96
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: 8b10ef18fd389179a4f5422783606c45fa2e0d32
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60728042"
---
# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Configurando Reliable Actors--KVSActorStateProvider
A configuração padrão do KVSActorStateProvider pode ser alterada modificando-se o arquivo settings.xml gerado na raiz pacote do Microsoft Visual Studio dentro da pasta Config para o ator especificado.

O tempo de execução do Service Fabric do Azure procura nomes de seção predefinidos no arquivo settings.xml e consome os valores de configuração ao criar os componentes de tempo de execução subjacentes.

> [!NOTE]
> **Não** exclua/modifique os nomes de seção das configurações a seguir no arquivo settings.xml que é gerado na solução do Visual Studio.
> 
> 

## <a name="replicator-security-configuration"></a>Configuração de segurança do replicador
As configurações de segurança do replicador servem para proteger o canal de comunicação que é usado durante a replicação. Isso significa que os serviços não podem ver o tráfego de replicação uns dos outros, garantindo que os dados que têm alta disponibilidade também estejam seguros.
Por padrão, uma seção de configuração de segurança vazia evita a segurança de replicação.

> [!IMPORTANT]
> Em nós Linux, certificados devem estar formatados como PEM. Para saber mais sobre como localizar e configurar certificados para Linux, consulte [Configurar certificados em Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Nome da seção
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Configuração do replicador
Configurações do replicador configuram o replicador que será responsável por tornar o Provedor de Estado do Ator altamente confiável.
A configuração padrão é gerada pelo modelo do Visual Studio e deve ser suficiente. Esta seção fala sobre configurações adicionais que estão disponíveis para ajustar o replicador.

### <a name="section-name"></a>Nome da seção
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Nomes da configuração
| NOME | Unidade | Valor padrão | Comentários |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Segundos |0,015 |Período de tempo pelo qual o replicador no secundário espera após o recebimento de uma operação antes de enviar novamente uma confirmação ao primário. Todas as outras confirmações a serem enviadas para operações e processadas dentro deste intervalo são enviadas como uma única resposta. |
| ReplicatorEndpoint |N/D |Nenhum parâmetro padrão obrigatório |Endereço IP e porta que o replicador primário/secundário usará para se comunicar com outros replicadores no conjunto de réplicas. Eles devem fazer referência a um ponto de extremidade do recurso de TCP no manifesto do serviço. Consulte [Recursos do manifesto do serviço](service-fabric-service-manifest-resources.md) para saber mais sobre como definir os recursos de ponto de extremidade no manifesto de serviço. |
| RetryInterval |Segundos |5 |Período de tempo após o qual o replicador transmite novamente uma mensagem caso não receba uma confirmação para uma operação. |
| MaxReplicationMessageSize |Bytes |50 MB |Tamanho máximo de dados de replicação que podem ser transmitidos em uma única mensagem. |
| MaxPrimaryReplicationQueueSize |Número de operações |1024 |Número máximo de operações na fila principal. Uma operação é liberada depois que o replicador primário recebe uma confirmação de todos os replicadores secundários. Esse valor deve ser maior que 64 e uma potência de 2. |
| MaxSecondaryReplicationQueueSize |Número de operações |2048 |Número máximo de operações na fila secundária. Uma operação é liberada depois de tornar seu estado de altamente disponível por meio de persistência. Esse valor deve ser maior que 64 e uma potência de 2. |

## <a name="store-configuration"></a>Configuração de armazenamento
As configurações de armazenamento servem para configurar o armazenamento local que é usado para manter o estado que está sendo replicado.
A configuração padrão é gerada pelo modelo do Visual Studio e deve ser suficiente. Esta seção fala sobre configurações adicionais que estão disponíveis para ajustar o armazenamento local.

### <a name="section-name"></a>Nome da seção
&lt;ActorName&gt;ServiceLocalStoreConfig

### <a name="configuration-names"></a>Nomes da configuração
| NOME | Unidade | Valor padrão | Comentários |
| --- | --- | --- | --- |
| MaxAsyncCommitDelayInMilliseconds |Milissegundos |200 |Define o intervalo máximo de envio em lotes para a confirmação do local de armazenamento durável . |
| MaxVerPages |Número de páginas |16384 |O número máximo de páginas de versão no banco de dados do armazenamento local. Ele determina o número máximo de transações pendentes. |

## <a name="sample-configuration-file"></a>Arquivo de exemplo de configuração
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
   </Section>
   <Section Name="MyActorServiceLocalStoreConfig">
      <Parameter Name="MaxVerPages" Value="8192" />
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

