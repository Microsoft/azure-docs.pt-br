---
title: Ciclo de vida de ativação e desativação do Azure Service Fabric hospedando
description: Saiba mais sobre o ciclo de vida de um aplicativo e um pacote de pacotes em um nó.
author: tugup
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: tugup
ms.openlocfilehash: d8585d0b39e4a4ef9cf77f40ea878ddb47bcb0de
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97831815"
---
# <a name="azure-service-fabric-hosting-life-cycle"></a>Ciclo de vida de hospedagem do Azure Service Fabric

Este artigo fornece uma visão geral dos eventos que ocorrem no Azure Service Fabric quando um aplicativo é ativado em um nó. Ele explica várias configurações de cluster que controlam o comportamento.

Antes de continuar, certifique-se de entender os conceitos e as relações explicados em [modelar um aplicativo no Service Fabric][a1]. 

> [!NOTE]
> Este artigo usa algumas terminologias de maneiras especializadas. Salvo indicação em contrário:
>
> - *Réplica* refere-se tanto a uma réplica de um serviço com estado como a uma instância de um serviço sem estado.
> - *CodePackage* é tratado como equivalente a um processo de ServiceHost que registra um ServiceType. Ele hospeda réplicas de serviços do ServiceType.
>

## <a name="activate-an-applicationpackage-or-servicepackage"></a>Ativar um ApplicationPackage ou um pacote de pacotes

Para ativar um ApplicationPackage ou um pacote de pacotes:

1. Baixe o ApplicationPackage (por exemplo, *ApplicationManifest.xml*).
2. Configure um ambiente para o aplicativo. As etapas incluem, por exemplo, a criação de usuários.
3. Comece a controlar o aplicativo para desativação.
4. Baixe o arquivo de pacote (por exemplo, *ServiceManifest.xml*, código, arquivos de configuração e pacotes de dados).
5. Configure um ambiente para o pacote de pacotes. As etapas incluem, por exemplo, a configuração de um firewall e a alocação de portas para pontos de extremidade.
6. Inicie o rastreamento do pacote de pacotes para desativação.
7. Inicie o SetupEntryPoint para o CodePackages e aguarde a conclusão.
8. Inicie o MainEntryPoint para o CodePackages.

### <a name="servicetype-blocklisting"></a>Inclusão na lista do ServiceType
`ServiceTypeDisableFailureThreshold` determina o número de falhas de ativação, download e CodePackage permitidas. Depois que o limite for atingido, o ServiceType será agendado para inclusão na lista. A primeira falha de ativação, falha de download ou falha de CodePackage agenda o ServiceType inclusão na lista. 

A `ServiceTypeDisableGraceInterval` configuração determina o intervalo de carência antes que ServiceType seja incluídos no nó. À medida que esse processo é reproduzido, a ativação, o download e a reinicialização do CodePackage são repetidas em paralelo. Tentar novamente significa, por exemplo, que o CodePackage está agendado para iniciar novamente após a falha ou Service Fabric tentará baixar pacotes novamente.

Quando ServiceType for incluídos, você verá um erro de integridade: `'System.Hosting' reported Error for property 'ServiceTypeRegistration:ServiceType'. The ServiceType was disabled on the node.`

O ServiceType será habilitado novamente no nó se ocorrer algum dos seguintes eventos:
- Ativação realizada com sucesso. Ou, se falhar, atingirá o `ActivationMaxFailureCount` número máximo de tentativas.
- O download é executado com sucesso. Ou, se falhar, atingirá o `DeploymentMaxFailureCount` número máximo de tentativas.
- Um CodePackage que falhou inicia e registra com êxito o ServiceType.

`ActivationMaxFailureCount` e `DeploymentMaxFailureCount` são o número máximo de tentativas que Service Fabric fará para ativar ou baixar um aplicativo em um nó. Depois que o máximo for atingido, Service Fabric habilitará o ServiceType para ativação novamente. 

Esses limites dão ao serviço outra oportunidade de ativação. Se a ativação for realizada com sucesso, o problema será automaticamente reparado. 

Uma réplica colocada ou ativada recentemente pode disparar uma nova operação de ativação ou de download. Essa ação terá sucesso ou disparará o ServiceType inclusão na lista novamente.

> [!NOTE]
> Um CodePackage de falha que não registra um ServiceType não afeta o ServiceType. Somente um CodePackage de falha que hospeda uma réplica afeta o ServiceType.
>

### <a name="codepackage-crash"></a>Falha de CodePackage
Quando um CodePackage falha, o Service Fabric usa uma retirada para iniciá-lo novamente. A retirada é aplicada independentemente de o CodePackage ter registrado um tipo com o tempo de execução Service Fabric.

O valor de retirada é `Min(RetryTime, ActivationMaxRetryInterval)` . O valor é incrementado em valores constantes, lineares ou exponenciais com base na `ActivationRetryBackoffExponentiationBase` definição de configuração:

- **Constante**: se `ActivationRetryBackoffExponentiationBase == 0` , então `RetryTime = ActivationRetryBackoffInterval` .
- **Linear**: se  `ActivationRetryBackoffExponentiationBase == 0` , em `RetryTime = ContinuousFailureCount ActivationRetryBackoffInterval` que `ContinuousFailureCount` é o número de vezes que um CodePackage falha ou falha ao ser ativado.
- **Exponencial**: `RetryTime = (ActivationRetryBackoffInterval in seconds) * (ActivationRetryBackoffExponentiationBase ^ ContinuousFailureCount)` .
    
Você pode controlar o comportamento alterando os valores. Por exemplo, se você quiser várias tentativas de reinicialização rápida, poderá usar valores lineares definindo `ActivationRetryBackoffExponentiationBase` como `0` e definindo `ActivationRetryBackoffInterval` como `10` . Portanto, se um CodePackage falhar, o intervalo de início será após 10 segundos. Se o pacote continuar a falhar, a retirada mudará para 20 segundos, 30 segundos, 40 segundos e assim por diante, até que a ativação do CodePackage tenha sucesso ou o CodePackage seja desativado. 
    
A quantidade máxima de tempo que Service Fabric faz logoff (ou seja, espera) depois que uma falha é regida pelo `ActivationMaxRetryInterval` .
    
Se o CodePackage falhar e voltar, ele precisará permanecer ativo para o período de tempo especificado pelo `CodePackageContinuousExitFailureResetInterval` . Após esse intervalo, Service Fabric considera o CodePackage íntegro. Em seguida, Service Fabric substitui o relatório de integridade de erro anterior como OK e redefine o `ContinuousFailureCount` .

### <a name="codepackage-not-registering-servicetype"></a>CodePackage não registrando ServiceType
Se um CodePackage permanecer ativo e for esperado registrar um ServiceType, mas não o Service Fabric gerará um relatório de integridade de aviso após o `ServiceTypeRegistrationTimeout` . O relatório indica que o ServiceType não foi registrado dentro do período de tempo esperado.

### <a name="activation-failure"></a>Falha na ativação
Quando Service Fabric encontra um erro durante a ativação, ele sempre usa uma retirada linear, como faz com uma falha de CodePackage. A operação de ativação é exibida após novas tentativas nestes intervalos: (0 + 10 + 20 + 30 + 40) = 100 segundos. (A primeira tentativa é imediata.) Após essa sequência, a ativação não é repetida.
    
A retirada de ativação máxima pode ser `ActivationMaxRetryInterval` . A repetição pode ser `ActivationMaxFailureCount` .

### <a name="download-failure"></a>Falha no download
Service Fabric sempre usa uma retirada linear quando encontra um erro durante um download. A operação de ativação é exibida após novas tentativas nestes intervalos: (0 + 10 + 20 + 30 + 40) = 100 segundos. (A primeira tentativa é imediata.) Após essa sequência, o download não será repetido. 

A retirada linear para um download é igual a `ContinuousFailureCount`  *  `DeploymentRetryBackoffInterval` . A retirada máxima pode ser `DeploymentMaxRetryInterval` . Assim como as ativações, as operações de download podem tentar novamente o `ActivationMaxFailureCount` limite.

> [!NOTE]
> Antes de alterar essas configurações, tenha em mente os seguintes exemplos:
>
>* Se o CodePackage continuar falhando e fazendo o backup, o ServiceType será desabilitado. Mas se a configuração de ativação tiver uma reinicialização rápida, o CodePackage poderá acabar algumas vezes antes que o ServiceType seja realmente incluídos. 
>
>    Por exemplo, digamos que seu CodePackage Venha, registra o ServiceType com Service Fabric e, em seguida, falha. Nesse caso, após a hospedagem receber um registro de tipo, o `ServiceTypeDisableGraceInterval` período será cancelado. Esse processo pode ser repetido até que seu CodePackage faça o logoff para um valor maior que o `ServiceTypeDisableGraceInterval` período. Em seguida, o ServiceType será incluídos no nó. O ServiceType inclusão na lista pode levar mais tempo do que o esperado.
>
>* No caso de ativações, quando um sistema de Service Fabric precisa posicionar uma réplica em um nó, o agente de reconfiguração solicita que o subsistema de hospedagem ative o aplicativo. Ele tenta novamente a solicitação de ativação a cada 15 segundos. (A duração é regida pela definição de `RAPMessageRetryInterval` configuração.) Service Fabric não pode saber que ServiceType foi incluídos, a menos que a operação de ativação na hospedagem esteja ativa por um período mais longo do que o intervalo de repetição e o `ServiceTypeDisableGraceInterval` . 
>
>    Por exemplo, suponha que o cluster `ActivationMaxFailureCount` está definido como 5 e `ActivationRetryBackoffInterval` está definido como 1 segundo. Nesse caso, a operação de ativação é exibida após intervalos de (0 + 1 + 2 + 3 + 4) = 10 segundos. (A primeira tentativa é imediata.) Após essa sequência, a hospedagem dará repetição. A operação de ativação é concluída e não tentará novamente após 15 segundos. 
>
>    Service Fabric esgotou todas as tentativas permitidas dentro de 15 segundos. Assim, cada repetição do agente de reconfiguração cria uma nova operação de ativação no subsistema de hospedagem e o padrão continua repetindo. Como resultado, o ServiceType nunca é incluídos no nó. Como o ServiceType não será incluídos no nó, a réplica não será movida e tentada em um nó diferente.
> 

## <a name="deactivation"></a>Desativação

Quando um pacote de pacotes é ativado em um nó, ele é acompanhado para desativação. A desativação funciona de duas maneiras:

- **Desativação periódica**: em cada `DeactivationScanInterval` , o sistema verifica se há pacotes de serviço que *nunca* hospedaram uma réplica e os marca como candidatos para desativação.
- **Desativação de ReplicaClose**: se uma réplica for fechada, o desativador Obtém um `DecrementUsageCount` . Uma contagem é em 0 quando o pacote de pacotes não hospeda nenhuma réplica, portanto, o pacote de pacotes é um candidato para desativação.

O modo de ativação determina quando os candidatos são agendados para desativação. No modo compartilhado, os candidatos à desativação são agendados após o `DeactivationGraceInterval` . Em modo exclusivo, eles são agendados após o `ExclusiveModeDeactivationGraceInterval` . Se um novo posicionamento de réplica chegar entre esses horários, a desativação será cancelada. 

Para obter mais informações, consulte modo [exclusivo e compartilhado][a2].

### <a name="periodic-deactivation"></a>Desativação periódica
Aqui estão alguns exemplos de desativação periódica:

* **Exemplo 1**: digamos que o desativador inicie uma verificação no tempo T (o `DeactivationScanInterval` ). Sua próxima verificação será em 2T. Suponha que uma ativação do pacote de pacotes ocorreu em T + 1. Esse pacote de pacotes não hospeda uma réplica, portanto, precisa ser desativado. 

    Para ser um candidato à desativação, o pacote de pacotes precisa não hospedar nenhuma réplica por pelo menos T time. Ele será elegível para desativação em 2T + 1. Portanto, a verificação em 2T não identificará esse pacote como um candidato para desativação. 

    O próximo ciclo de desativação, 3T, agendará este pacote de desativação porque agora o pacote está em um estado de não réplica para o tempo T.  

* **Exemplo 2**: digamos que um pacote de pacotes seja ativado na hora T-1 e o desativador inicie uma verificação em t. O pacote de pacotes não hospeda uma réplica. Na próxima verificação, 2T, o pacote de pacotes será identificado como um candidato para desativação, portanto, ele será agendado para desativação.  

* **Exemplo 3**: digamos que um pacote de pacotes é ativado em T – 1, e o desativador inicia uma verificação em t. O pacote de pacotes não hospeda uma réplica ainda. Agora em T + 1, uma réplica é colocada. Ou seja, a hospedagem Obtém um `IncrementUsageCount` , o que significa que uma réplica é criada. 

    Em 2T, esse pacote de pacotes não será agendado para desativação. Como o pacote contém uma réplica, a desativação seguirá a lógica ReplicaClose, como explica a próxima seção neste artigo.

* **Exemplo 4**: digamos que seu pacote de pacotes seja de 10 GB. Como o pacote é grande, demora um tempo para baixar no nó. Quando um aplicativo é ativado, o desativador rastreia seu ciclo de vida. Se o `DeactivationScanInterval` for definido como um valor pequeno, seu pacote de pacotes poderá não ter tempo para ser ativado no nó devido ao tempo necessário para o download. Para resolver esse problema, você pode [baixar o pacote de progresso com antecedência no nó][p1] ou aumentar o `DeactivationScanInterval` . 

> [!NOTE]
> Um pacote de pacotes pode ser desativado em qualquer lugar entre ( `DeactivationScanInterval` até 2 * `DeactivationScanInterval` ) + `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` . 
>

### <a name="replicaclose-deactivation"></a>Desativação de ReplicaClose

> [!NOTE]
> Esta seção refere-se às seguintes definições de configuração:
> - **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**: o tempo dado a um pacote de pacotes para hospedar outra réplica se já tiver hospedado qualquer réplica. 
> - **DeactivationScanInterval**: o tempo mínimo dado a um pacote de pacotes para hospedar uma réplica se *nunca* tiver hospedado nenhuma réplica, ou seja, se não tiver sido usada.
>

O sistema mantém a contagem das réplicas que um pacote de pacotes mantém. Se um pacote de pacotes estiver mantendo uma réplica e a réplica estiver fechada ou inativa, a hospedagem obterá um `DecrementUsageCount` . Quando uma réplica é aberta, a hospedagem Obtém um `IncrementUsageCount` . 

O decréscimo indica que o número de réplicas que o pacote de pacotes está hospedando foi reduzido por uma réplica. Quando a contagem cai para 0, o pacote de pacotes é agendado para desativação. O tempo após o qual ele será desativado é `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` . 

Por exemplo, digamos que um decréscimo ocorra em T, e o pacote de pacotes está agendado para desativar em 2T + X ( `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` ). Durante esse tempo, se a hospedagem obtiver um `IncrementUsage` porque uma réplica é criada, a desativação será cancelada.

### <a name="ctrl--c"></a>Ctrl + C
Se um pacote de pacotes passar no `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` e ainda não estiver hospedando uma réplica, a desativação não poderá ser cancelada. CodePackages receber um manipulador CTRL + C. Agora, o pipeline de desativação precisa ser concluído para reduzir o processo. 

Durante esse tempo, se uma nova réplica para o mesmo pacote de pacotes estiver tentando ser colocada, ela falhará porque o processo não pode fazer a transição da desativação para a ativação.

## <a name="cluster-configurations"></a>Configurações de cluster

Esta seção lista as configurações que têm padrões que afetam a ativação e a desativação.

### <a name="servicetype"></a>ServiceType
- **ServiceTypeDisableFailureThreshold**: padrão: 1. Limite para a contagem de falhas; Depois que esse limite for atingido, o Failovermanager será notificado para desabilitar o tipo de serviço no nó e tentar um nó diferente para posicionamento.
- **ServiceTypeDisableGraceInterval**: padrão: 30 segundos. Intervalo de tempo após o qual o tipo de serviço pode ser desabilitado.
- **ServiceTypeRegistrationTimeout**: padrão: 300 segundos. Tempo limite para o ServiceType se registrar com Service Fabric.

### <a name="activation"></a>Ativação
- **ActivationRetryBackoffInterval**: padrão: 10 segundos. Intervalo de retirada para cada falha de ativação.
- **ActivationMaxFailureCount**: padrão: 20. Contagem máxima para a qual o sistema tentará novamente uma falha de ativação antes de desistir. 
- **ActivationRetryBackoffExponentiationBase**: padrão: 1,5.
- **ActivationMaxRetryInterval**: padrão: 3.600 segundos. Intervalo máximo de tentativas de retirada para ativação após falhas.
- **CodePackageContinuousExitFailureResetInterval**: padrão: 300 segundos. Intervalo de tempo limite para redefinir a contagem de falhas de saída contínua para o CodePackage.

### <a name="download"></a>Baixar
- **DeploymentRetryBackoffInterval**: padrão: 10. Intervalo de retirada para a falha de implantação.
- **DeploymentMaxRetryInterval**: padrão: 3.600 segundos. Intervalo de retirada máximo para a implantação após falhas.
- **DeploymentMaxFailureCount**: padrão: 20. A implantação do aplicativo será repetida por `DeploymentMaxFailureCount` momentos antes de falhar na implantação desse aplicativo no nó.

### <a name="deactivation"></a>Desativação
- **DeactivationScanInterval**: padrão: 600 segundos. Tempo mínimo fornecido ao pacote de pacotes para hospedar uma réplica se nunca tiver hospedado nenhuma réplica (ou seja, se não for usado).
- **DeactivationGraceInterval**: padrão: 60 segundos. Em um modelo de processo *compartilhado* , o tempo fornecido a um pacote para hospedar novamente outra réplica depois que ele já tiver hospedado qualquer réplica.
- **ExclusiveModeDeactivationGraceInterval**: padrão: 1 segundo. Em um modelo de processo *exclusivo* , o tempo fornecido a um pacote para hospedar novamente outra réplica depois que ele já tiver hospedado qualquer réplica.

## <a name="next-steps"></a>Próximas etapas

- [Empacotar um aplicativo][a3] e prepará-lo para a implantação.
- [Implantar e remover aplicativos][a4] no PowerShell.

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode
