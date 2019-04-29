---
title: Aplicativo de orquestração de patch do Azure Service Fabric | Microsoft Docs
description: Aplicativo para automatizar a aplicação de patch do sistema operacional em um cluster do Service Fabric.
services: service-fabric
documentationcenter: .net
author: khandelwalbrijeshiitr
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: brkhande
ms.openlocfilehash: 6c0aa42cc22d22431d7d0270aca52e089046cb01
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773359"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Patch do sistema operacional Windows em seu cluster do Service Fabric

> [!div class="op_single_selector"]
> * [Windows](service-fabric-patch-orchestration-application.md)
> * [Linux](service-fabric-patch-orchestration-application-linux.md)
>
>


> 
> [!IMPORTANT]
> A versão 1.2 do aplicativo. * vai sem suporte em 30 de abril de 2019. Atualize para a versão mais recente.


[A escalabilidade da máquina virtual do conjunto do Azure para atualizações de imagem do sistema operacional](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) é a melhor prática para manter seus sistemas operacionais corrigidos no Azure e o POA (Patch Orchestration Application) é um wrapper do serviço Service Fabric RepairManager Systems que ativa o SO baseado em configuração agendamento de patch para clusters hospedados que não são do Azure. O POA não é necessário para clusters hospedados que não sejam do Azure, mas o agendamento da instalação de patches por domínios de atualização é necessário para corrigir os hosts dos clusters do Service Fabric sem tempo de inatividade.

O POA é um aplicativo do Azure Service Fabric que automatiza o patches do sistema operacional em um cluster do Service Fabric sem tempo de inatividade.

O aplicativo de orquestração de patch fornece os recursos a seguir:

- **Instalação da atualização automática do sistema operacional**. Atualizações do sistema operacional são baixadas e instaladas automaticamente. Nós de cluster são reiniciados conforme necessário, sem tempo de inatividade do cluster.

- **Aplicação de patch com suporte a cluster e integração de integridade**. Durante a aplicação de atualizações, o aplicativo de orquestração de patch monitora a integridade dos nós de cluster. Os nós de cluster fazem upgrade de um nó ou um domínio de atualização por vez. Se a integridade do cluster falhar devido ao processo de aplicação de patch, a aplicação de patch será interrompida para evitar agravar o problema.

## <a name="internal-details-of-the-app"></a>Detalhes internos do aplicativo

O aplicativo de orquestração de patch é composto dos seguintes subcomponentes:

- **Serviço de coordenador**: este serviço com estado é responsável por:
    - Coordenar o trabalho do Windows Update em todo o cluster.
    - Armazenar o resultado das operações concluídas do Windows Update.
- **Serviço de agente do nó**: esse serviço sem estado é executado em todos os nós de cluster do Service Fabric. O serviço é responsável por:
    - Inicialização de NTService do Agente do Nó.
    - Monitoramento do NTService do Agente do Nó.
- **NTService do Agente do Nó**: este serviço Windows NT é executado em um privilégio de nível superior (SISTEMA). Em comparação, o Serviço de Agente do Nó e o Serviço de Coordenador são executados em um nível inferior de privilégio (SERVIÇO DE REDE). O serviço é responsável por executar os seguintes trabalhos do Windows Update em todos os nós de cluster:
    - Desabilitar o Windows Update automático no nó.
    - Baixar e instalar o Windows Update de acordo com a política que o usuário forneceu.
    - Reiniciar o computador após a instalação do Windows Update.
    - Carregar os resultados das atualizações do Windows para o Serviço do Coordenador.
    - Relatórios de integridade de relatórios no caso de falha na operação após esgotar todas as novas tentativas.

> [!NOTE]
> O aplicativo de orquestração de patch usa o serviço do sistema do gerenciador de reparo do Service Fabric para habilitar ou desabilitar o nó e executar as verificações de integridade. A tarefa de reparo criada pelo aplicativo de orquestração de patch rastreia o progresso do Windows Update para cada nó.

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]
> Versão mínima do .NET framework necessária é a 4.6.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Habilite o serviço do gerenciador de reparo (se ainda não estiver em execução)

O aplicativo de orquestração de patch exige que o serviço do sistema do gerenciador de reparo esteja habilitado no cluster.

#### <a name="azure-clusters"></a>Clusters do Azure

Clusters do Azure na camada de durabilidade prata têm o serviço do gerenciador de reparo habilitado por padrão. Clusters do Azure na camada de durabilidade ouro podem ou não ter o serviço do gerenciador de reparo habilitado, dependendo de quando esses clusters foram criados. Cluster do Azure na camada de durabilidade bronze, por padrão, não têm o serviço do gerenciador de reparo habilitado. Se o serviço já está habilitado, você pode ver ele em execução na seção de serviços do sistema no Service Fabric Explorer.

##### <a name="azure-portal"></a>Portal do Azure
Você pode habilitar o Gerenciador de reparo do portal do Azure no momento da configuração do cluster. Selecione a opção **Incluir Gerenciador de Reparos** em **Recursos de complemento** no momento da configuração do cluster.
![Imagem do Gerenciador de reparo de habilitação do portal do Azure](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-deployment-model"></a>modelo de implantação do Azure Resource Manager
Como alternativa, é possível usar o [modelo de implantação do Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) para habilitar o serviço do gerenciador de reparos em clusters do Service Fabric novos e existentes. Obtenha o modelo para o cluster que você deseja implantar. Você pode usar os modelos de exemplo ou criar um modelo de implantação do Azure Resource Manager personalizado. 

Para habilitar o serviço do gerenciador de reparos usando o [modelo de implantação do Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm):

1. Primeiro, verifique se `apiversion` está definido como `2017-07-01-preview` para o recurso `Microsoft.ServiceFabric/clusters`. Se estiver diferente, será necessário atualizar `apiVersion` para o valor `2017-07-01-preview` ou superior:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Agora habilite o serviço do gerenciador de reparo adicionando a seguinte seção `addonFeatures` após a seção `fabricSettings`:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Depois de atualizar o modelo de cluster com essas alterações, aplique-as e permita a conclusão do upgrade. Agora você pode ver o serviço do sistema do gerenciador de reparo em execução no cluster. Ele é chamado de `fabric:/System/RepairManagerService` na seção de serviços do sistema no Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Clusters locais autônomos

Você pode usar as [Definições de configuração para o cluster autônomo do Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest) para habilitar o serviço do gerenciador de reparo em clusters novos e existentes do Service Fabric.

Para habilitar o serviço do gerenciador de reparo:

1. Primeiro verifique se o `apiversion` nas [Configurações de cluster geral](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) está definido como `04-2017` ou superior:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

2. Agora habilite o serviço do gerenciador de reparo adicionando a seguinte seção `addonFeatures` após a seção `fabricSettings`, conforme mostrado abaixo:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Atualize o manifesto do cluster com essas alterações, usando o manifesto do cluster atualizado [crie um novo cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) ou [atualize a configuração do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server). Com o cluster em execução com o manifesto do cluster atualizado, agora você poderá ver o serviço do sistema do gerenciador de reparo em execução no seu cluster, que é chamado de `fabric:/System/RepairManagerService`, sob a seção de serviços do sistema no Service Fabric Explorer.

### <a name="configure-windows-updates-for-all-nodes"></a>Configurar o Windows Update para todos os nós

As Atualizações Automáticas do Windows podem causar a perda de disponibilidade porque vários nós de cluster podem ser reiniciados ao mesmo tempo. O aplicativo de orquestração de patch, por padrão tenta desabilitar o Windows Update automático em cada nó de cluster. No entanto, se as configurações forem gerenciadas por um administrador ou uma Política de Grupo, é recomendável configurar a política do Windows Update explicitamente como "Notificar antes de Baixar".

## <a name="download-the-app-package"></a>Baixar o pacote do aplicativo

O aplicativo juntamente com scripts de instalação podem ser baixados do [link de arquivo](https://go.microsoft.com/fwlink/?linkid=869566).

O aplicativo no formato sfpkg pode ser baixado do [link sfpkg](https://aka.ms/POA/POA.sfpkg). Isso é útil para a [Implantação de aplicativo baseado no Azure Resource Manager](service-fabric-application-arm-resource.md).

## <a name="configure-the-app"></a>Configurar o aplicativo

O comportamento do aplicativo de orquestração de patch pode ser configurado para atender às suas necessidades. Substitua os valores padrão passando o parâmetro de aplicativo durante a criação ou atualização do aplicativo. Parâmetros do aplicativo podem ser fornecidos especificando `ApplicationParameter` aos cmdlets `Start-ServiceFabricApplicationUpgrade` ou `New-ServiceFabricApplication`.

|**Parâmetro**        |**Tipo**                          | **Detalhes**|
|:-|-|-|
|MaxResultsToCache    |long                              | Número máximo de resultados do Windows Update, que devem ser armazenados em cache. <br>O valor padrão é 3000, supondo que o: <br> - Número de nós é 20. <br> - Número de atualizações acontecendo em um nó por mês seja de cinco. <br> – Número de resultados por operação possa ser de 10. <br> - Resultados para os últimos três meses devem ser armazenados. |
|TaskApprovalPolicy   |Enum <br> { NodeWise, UpgradeDomainWise }                          |A TaskApprovalPolicy indica a política a ser usada pelo Serviço do Coordinator para instalar atualizações do Windows em todos os nós de cluster do Service Fabric.<br>                         Valores permitidos são: <br>                                                           <b>NodeWise</b>. O Windows Update é instalado em um nó por vez. <br>                                                           <b>UpgradeDomainWise</b>. O Windows Update é instalado em um domínio de atualização por vez. (No máximo, todos os nós que pertencem a um domínio de atualização podem ir para o Windows Update.)<br> Consulte a seção [Perguntas Frequentes](#frequently-asked-questions) sobre como decidir qual é a política mais adequada para seu cluster.
|LogsDiskQuotaInMB   |long  <br> (Padrão: 1024)               |Tamanho máximo dos logs do aplicativo de orquestração de patch em MB, que pode ser mantido localmente no nó.
| WUQuery               | string<br>(Padrão: "IsInstalled=0")                | Consulta para obter atualizações do Windows. Para obter mais informações, consulte [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| InstallWindowsOSOnlyUpdates | Boolean <br> (padrão: false)                 | Use esse sinalizador para controlar quais atualizações devem ser baixadas e instaladas. Os seguintes valores são permitidos <br>true – instala somente as atualizações do sistema operacional Windows.<br>false – instala todas as atualizações disponíveis no computador.          |
| WUOperationTimeOutInMinutes | Int <br>(Padrão: 90)                   | Especifica o tempo limite para qualquer operação do Windows Update (pesquisar, baixar ou instalar). Se a operação não for concluída dentro do tempo limite especificado, ela será anulada.       |
| WURescheduleCount     | Int <br> (Padrão: 5)                  | O número máximo de vezes que o serviço reagendaria o Windows Update no caso de falha persistente na operação.          |
| WURescheduleTimeInMinutes | Int <br>(Padrão: 30) | O intervalo ao qual o serviço reagendaria o Windows Update no caso de persistência da falha. |
| WUFrequency           | Cadeia de caracteres separada por vírgula (Padrão: "Semanais, quarta-feira, 7:00:00")     | A frequência para a instalação do Windows Update. O formato e os valores possíveis são: <br>-   Mensal, DD, HH:MM:SS, por exemplo, Mensal, 5,12:22:32.<br>Os valores permitidos para o campo DD (dia) são números entre o intervalo de 1-28 e "last". <br> -   Semanal, DIA, HH:MM:SS, por exemplo, Semanal, terça-feira, 12:22:32.  <br> -   Diário, HH:MM:SS, por exemplo, Diário, 12:22:32.  <br> -  Nenhum indica que o Windows Update não deve ser executado.  <br><br> Observe que os horários estão em UTC.|
| AcceptWindowsUpdateEula | Boolean <br>(Padrão: true) | Ao definir esse sinalizador, o aplicativo aceita o Contrato de licença do usuário final para o Windows Update em nome do proprietário do computador.              |

> [!TIP]
> Se você quiser que o Windows Update seja executado imediatamente, defina `WUFrequency` em relação ao tempo de implantação do aplicativo. Por exemplo, suponha que você tem um cluster de teste de cinco nós e planeja implantar o aplicativo em torno de 5:00 PM UTC. Se você considera que o upgrade ou implantação do aplicativo leva 30 minutos no máximo, defina a WUFrequency como "Diariamente, 17:30:00"

## <a name="deploy-the-app"></a>Implantar o aplicativo

1. Conclua todas as etapas necessárias para preparar o cluster.
2. Implante o aplicativo de orquestração de patch como qualquer outro aplicativo do Service Fabric. Você pode implantar o aplicativo usando o PowerShell. Siga as etapas em [Implantar e remover aplicativos usando o PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
3. Para configurar o aplicativo no momento da implantação, passe o `ApplicationParameter` para o cmdlet `New-ServiceFabricApplication`. Para sua conveniência, fornecemos o script Deploy.ps1 juntamente com o aplicativo. Para usar o script:

    - Conectar a um cluster do Service Fabric usando `Connect-ServiceFabricCluster`.
    - Execute o script do PowerShell Deploy.ps1 com o valor `ApplicationParameter` apropriado.

> [!NOTE]
> Mantenha a pasta de scripts e de aplicativos PatchOrchestrationApplication no mesmo diretório.

## <a name="upgrade-the-app"></a>Upgrade do aplicativo

Para fazer upgrade de um aplicativo de orquestração de patch existente usando o PowerShell, execute as etapas em [Upgrade do aplicativo do Service Fabric usando o PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell).

## <a name="remove-the-app"></a>Remover o aplicativo

Para remover o aplicativo, siga as etapas em [Implantar e remover aplicativos usando o PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).

Para sua conveniência, fornecemos o script Undeploy.ps1 juntamente com o aplicativo. Para usar o script:

  - Conectar a um cluster do Service Fabric usando ```Connect-ServiceFabricCluster```.

  - Execute o script do PowerShell Undeploy.ps1.

> [!NOTE]
> Mantenha a pasta de scripts e de aplicativos PatchOrchestrationApplication no mesmo diretório.

## <a name="view-the-windows-update-results"></a>Exibir os resultados do Windows Update

O aplicativo de orquestração de patch expõe as APIs REST para exibir os resultados históricos do usuário. Um exemplo de resultado JSON:
```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2017-05-21T11:46:52.1953713Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0
          }
        ],
        "OperationType": 1,
        "WindowsUpdateQuery": "IsInstalled=0",
        "WindowsUpdateFrequency": "Daily,10:00:00",
        "RebootRequired": false
      }
    ]
  },
  ...
]
```

Campos do JSON são descritos abaixo.

Campo | Valores | Detalhes
-- | -- | --
OperationResult | 0 - Êxito<br> 1 - Êxito com erros<br> 2 - Falha<br> 3 - Anulado<br> 4 - Anulado com tempo limite | Indica o resultado da operação geral (normalmente envolvendo a instalação de uma ou mais atualizações).
ResultCode | O mesmo que OperationResult | Este campo indica o resultado da operação de instalação para uma atualização individual.
OperationType | 1 - Instalação<br> 0 - Pesquisar e baixar.| A instalação é o único OperationType que seria mostrado nos resultados por padrão.
WindowsUpdateQuery | O padrão é "IsInstalled=0" |Consulta atualização do Windows que foi usada para procurar atualizações. Para obter mais informações, consulte [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
RebootRequired | true - a reinicialização foi necessária<br> false - a reinicialização não foi necessária | Indica se a reinicialização foi necessária para concluir a instalação de atualizações.

Se nenhuma atualização estiver agendada ainda, o resultado JSON estará vazio.

Faça logon no cluster para consultar os resultados do Windows Update. Em seguida, descubra o endereço de réplica para o primário do Serviço do Coordenador e pressione a URL do navegador: http://&lt;REPLICA-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetWindowsUpdateResults.

O ponto de extremidade REST para o Serviço do Coordenador tem uma porta dinâmica. Para verificar a URL exata, consulte o Service Fabric Explorer. Por exemplo, os resultados estão disponíveis em `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults`.

![Imagem do ponto de extremidade REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


Se o proxy reverso estiver habilitado no cluster, você pode acessar a URL de fora do cluster também.
O ponto de extremidade que precisa ser atingido é http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults.

Para habilitar o proxy reverso no cluster, siga as etapas em [Proxy reverso no Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). 

> 
> [!WARNING]
> Depois que o proxy reverso estiver configurado, todos os microsserviços do cluster que exponham um ponto de extremidade HTTP serão endereçáveis de fora do cluster.

## <a name="diagnosticshealth-events"></a>Eventos de diagnóstico/integridade

### <a name="diagnostic-logs"></a>Logs de diagnóstico

Os logs do aplicativo de orquestração de patch são coletados como parte dos logs do tempo de execução do Service Fabric.

Caso você queira capturar logs por meio da ferramenta de diagnóstico/pipeline de sua escolha. O aplicativo de orquestração de patch usa as IDs de provedores fixos abaixo para registrar eventos por meio do [eventsource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Relatórios de integridade

O aplicativo de orquestração de patch também publica relatórios de integridade em relação ao Serviço do Coordinator ou ao Serviço de Agente do Nó nos seguintes casos:

#### <a name="a-windows-update-operation-failed"></a>Uma falha na operação do Windows Update

Se uma operação do Windows Update falhar em um nó, um relatório de integridade em relação ao Serviço de Agente do Nó será gerado. Detalhes do relatório de integridade contêm o nome do nó problemático.

Após a aplicação de pach ser concluída com sucesso no nó problemático, o relatório é apagado automaticamente.

#### <a name="the-node-agent-ntservice-is-down"></a>O NTService do Agente do Nó está inativo

Se o NTService do Agente do Nó estiver inativo em um nó, o relatório de integridade no nível de aviso será gerado no Serviço de Agente do Nó.

#### <a name="the-repair-manager-service-is-not-enabled"></a>O serviço do gerenciador de reparo não está habilitado

Se o serviço do gerenciador de reparo não for encontrado no cluster, um relatório de integridade no nível de aviso será gerado para o Serviço do Coordinator.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

P. **Por que consigo ver meu cluster em um estado de erro quando o aplicativo de orquestração de patch está em execução?**

a. Durante o processo de instalação, o aplicativo de orquestração de patch desabilita ou reinicia os nós, isso pode resultar em redução temporária da integridade do cluster.

Com base na política para o aplicativo, um nó pode ficar inativo durante uma operação de aplicação de patch *ou* todo um domínio de atualização pode ficar inativo ao mesmo tempo.

Até o fim da instalação do Windows Update, os nós serão reabilitados após a reinicialização.

No exemplo a seguir, o cluster foi para um estado de erro temporariamente porque dois nós estavam inativos e a política MaxPercentageUnhealthyNodes foi violada. O erro é temporário até que a operação de aplicação de patch esteja em andamento.

![Imagem do cluster não íntegro](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Caso o problema persista, consulte a seção de Solução de problemas.

P. **O aplicativo de orquestração de patch está em estado de aviso**

a. Verifique para ver se um relatório de integridade publicado em relação ao aplicativo é a causa raiz. Geralmente, o aviso contém detalhes do problema. Se o problema for transitório, o aplicativo deve esperar recuperar-se automaticamente desse estado.

P. **O que fazer se o cluster não está íntegro e preciso fazer uma atualização urgente do sistema operacional?**

a. O aplicativo de orquestração de patch não instala atualizações enquanto o cluster não está íntegro. Tente colocar o cluster em um estado íntegro para desbloquear o fluxo de trabalho do aplicativo de orquestração de patch.

P. **Eu devo definir TaskApprovalPolicy como 'NodeWise' ou 'UpgradeDomainWise' para meu cluster?**

a. 'UpgradeDomainWise' acelera a aplicação de patch do cluster em termos gerais, por meio da aplicação de patch em paralelo a todos os nós que pertencem a um determinado domínio de atualização. Isso significa que nós que pertencem a um domínio de atualização inteiro ficariam indisponíveis (no estado [Desabilitado](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled)) durante o processo de aplicação de patch.

Por outro lado, a política 'NodeWise' aplica patch em apenas um nó por vez, o que faz com que a aplicação de patch nos clusters, em termos gerais, leve mais tempo. No entanto, somente um nó estaria indisponível, no máximo (no estado [Desabilitado](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled)) durante o processo de aplicação de patch.

Se o cluster puder tolerar a execução em um número N-1 de domínios de atualização durante o ciclo de aplicação de patch (em que N é o número total de domínios de atualização no cluster), então você poderá definir a política como 'UpgradeDomainWise', caso contrário, defina-a como 'NodeWise'.

P. **Quanto tempo demora a aplicação de patch em um nó?**

a. A correção de um nó pode levar alguns minutos (por exemplo: [Atualizações de definições do Windows Defender](https://www.microsoft.com/en-us/wdsi/definitions)) horas (por exemplo: [atualizações cumulativas do Windows](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)). O tempo necessário para a aplicação de patch em um nó depende principalmente 
 - Do tamanho das atualizações
 - Do número de atualizações que precisam ser aplicadas em uma janela de aplicação de patch
 - Do tempo necessário para instalar as atualizações, reinicializar o nó (se necessário) e concluir as etapas de instalação pós-reinicialização.
 - Do desempenho da VM/computador e das condições de rede.

P. **Quanto tempo leva a aplicação de patch a um cluster inteiro?**

a. O tempo necessário para aplicação de patch a um cluster inteiro depende dos seguintes fatores:

- Tempo necessário para aplicar o patch a um nó.
- A política do Serviço do Coordinator. – A política padrão, `NodeWise`, resulta na aplicação de patch em apenas um nó por vez, o que seria mais lento que `UpgradeDomainWise`. Por exemplo:  Se um nó leva cerca de 1 hora para ser corrigido, a fim de corrigir um cluster de 20 nós (do mesmo tipo de nós) com 5 domínios de atualização, cada um contendo 4 nós.
    - Deve levar aproximadamente 20 horas para aplicar o patch em todo o cluster, se a política é `NodeWise`
    - Deve levar cerca de 5 horas se a política é `UpgradeDomainWise`
- Carga do cluster – cada operação de aplicação de patch exige realocação da carga de trabalho do cliente para outros nós disponíveis no cluster. O nó passando por aplicação de patch estaria no estado [Desabilitando](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling) durante esse tempo. Se o cluster está executando perto de carga de pico, o processo de desabilitação levaria mais tempo. Portanto, o processo geral de aplicação de patch pode parecer lento em condições assim, sob pressão.
- Falhas de integridade do cluster durante a aplicação de patch – qualquer [degradação](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error) na [integridade do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction) interromperia o processo de aplicação de patch. Isso aumentaria o tempo total necessário para aplicar o patch em todo o cluster.

P. **Por que vejo algumas atualizações no Windows Update resultados obtidos por meio de API REST, mas não sob o histórico do Windows Update no computador?**

a. Algumas atualizações de produtos aparecem somente no respectivo histórico de patch/atualização. Por exemplo, atualizações do Windows Defender podem ou não aparecer no histórico do Windows Update no Windows Server 2016.

P. **O aplicativo de Orquestração de Patch pode ser usado para o cluster de desenvolvimento (cluster de um nó) do patch?**

a. Não, o aplicativo de Orquestração de Patch não pode ser usado para cluster de um nó do patch. Essa limitação ocorre por design, uma vez que os [serviços de sistema do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) ou todos os aplicativos cliente enfrentam tempo de inatividade e, portanto, qualquer trabalho de reparo para aplicação de patch nunca seria aprovado pelo gerenciador de reparo.

## <a name="disclaimers"></a>Avisos de Isenção de Responsabilidade

- O aplicativo de orquestração de patch aceita o Contrato de licença do usuário final do Windows Update em nome do usuário. A definição opcionalmente pode ser desativada na configuração do aplicativo.

- O aplicativo de orquestração de patch coleta a telemetria para acompanhar o uso e o desempenho. A telemetria do aplicativo segue a definição da configuração de telemetria do tempo de execução do Service Fabric (ativada por padrão).

## <a name="troubleshooting"></a>solução de problemas

### <a name="a-node-is-not-coming-back-to-up-state"></a>O nó não volta para o estado ativo

**O nó pode estar paralisado em um estado de desabilitação porque**:

Uma verificação de segurança está pendente. Para corrigir essa situação, verifique se há nós suficientes disponíveis em um estado íntegro.

**O nó pode estar paralisado em um estado desabilitado porque**:

- O nó foi desabilitado manualmente.
- O nó foi desabilitado devido a um trabalho de infraestrutura do Azure em andamento.
- O nó foi desabilitado temporariamente pelo aplicativo de orquestração de patch para aplicar o patch ao nó.

**O nó pode estar paralisado em um estado inativo porque**:

- O nó foi colocado no estado inativo manualmente.
- O nó está passando por uma reinicialização (que pode ser disparada pelo aplicativo de orquestração de patch).
- O nó está inativo devido a problemas de conectividade de rede ou VM/computador com defeito.

### <a name="updates-were-skipped-on-some-nodes"></a>As atualizações foram ignoradas em alguns nós

O aplicativo de orquestração de patch tenta instalar o Windows Update de acordo com a política de reagendamento. O serviço tenta recuperar o nó e ignorar a atualização de acordo com a política do aplicativo.

Nesse caso, será gerado um relatório de integridade no nível de aviso em relação ao Serviço de Agente do Nó. O resultado para o Windows Update também contém o possível motivo da falha.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>A integridade do cluster entrou em estado de erro enquanto a atualização é instalada

Uma atualização do Windows com falha pode reduzir a integridade de um aplicativo ou cluster em um nó ou domínio de atualização específico. O aplicativo de orquestração de patch interrompe qualquer operação subsequente do Windows Update até que o cluster esteja íntegro novamente.

Um administrador deve intervir e determinar por que o aplicativo ou cluster se tornou não íntegro devido ao Windows Update.

## <a name="release-notes"></a>Notas de versão

### <a name="version-110"></a>Version 1.1.0
- Versão pública

### <a name="version-111"></a>Versão 1.1.1
- Correção de bug no SetupEntryPoint de NodeAgentService que impediu a instalação de NodeAgentNTService.

### <a name="version-120"></a>Versão 1.2.0

- Correções de bugs em torno do fluxo de trabalho de reinício do sistema.
- Correção de bug na criação de tarefas RM devido à qual integridade seleção durante a preparação de tarefas de reparo não estava acontecendo conforme o esperado.
- Alterado o modo de inicialização para o serviço windows POANodeSvc de auto para delayed-auto.

### <a name="version-121"></a>Versão 1.2.1

- Correção de bug no fluxo de trabalho de redução de escala de cluster. Introduziu a lógica de coleta de lixo para tarefas de reparo POA pertencentes a nós inexistentes.

### <a name="version-122"></a>Versão 1.2.2

- Diversas correções de bugs.
- Binários agora são assinados.
- Adição do link do sfpkg para o aplicativo.

### <a name="version-130"></a>Versão 1.3.0

- A definição de InstallWindowsOSOnlyUpdates como falso agora instala todas as atualizações disponíveis.
- Alteração da lógica de desabilitação de atualizações automáticas. Isso corrige um bug em que as atualizações Automáticas não são desabilitadas no Server 2016 e posterior.
- Restrição de posicionamento parametrizado para os dois microsserviços do POA para casos de uso avançados.

### <a name="version-131"></a>Versão 1.3.1
- Correção da regressão, em que o POA 1.3.0 não funcionará no Windows Server 2012 R2 ou anterior devido a uma falha ao desabilitar as atualizações automáticas. 
- Correção de bug, em que a configuração InstallWindowsOSOnlyUpdates sempre é escolhida como True.
- Alteração do valor padrão de InstallWindowsOSOnlyUpdates para False.

### <a name="version-132"></a>Versão 1.3.2
- Corrigindo um problema que afetado a aplicação de patch cyle de vida em um nó em caso de nós com o nome que é um subconjunto do nome do nó atual. Para esses nós, é possível que aplicação de patch seja ignorada ou a reinicialização fique pendente. 
