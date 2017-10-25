---
title: "Aplicativo de orquestração de patch do Azure Service Fabric | Microsoft Docs"
description: "Aplicativo para automatizar a aplicação de patch do sistema operacional em um cluster do Service Fabric."
services: service-fabric
documentationcenter: .net
author: novino
manager: timlt
editor: 
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/9/2017
ms.author: nachandr
ms.openlocfilehash: c37180262981bbbcdecb0504e2717db27568586d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Patch do sistema operacional Windows em seu cluster do Service Fabric

O aplicativo de orquestração de patch é um aplicativo do Azure Service Fabric que automatiza a aplicação de patches do sistema operacional em um cluster do Service Fabric sem tempo de inatividade.

O aplicativo de orquestração de patch fornece o seguinte:

- **Instalação da atualização automática do sistema operacional**. Atualizações do sistema operacional são baixadas e instaladas automaticamente. Nós de cluster são reiniciados conforme necessário, sem tempo de inatividade do cluster.

- **Aplicação de patch com suporte a cluster e integração de integridade**. Durante a aplicação de atualizações, o aplicativo de orquestração de patch monitora a integridade dos nós de cluster. Os nós de cluster fazem upgrade de um nó ou um domínio de atualização por vez. Se a integridade do cluster falhar devido ao processo de aplicação de patch, a aplicação de patch será interrompida para evitar agravar o problema.

## <a name="internal-details-of-the-app"></a>Detalhes internos do aplicativo

O aplicativo de orquestração de patch é composto dos seguintes subcomponentes:

- **Serviço de Coordenador**: este serviço com estado é responsável por:
    - Coordenar o trabalho do Windows Update em todo o cluster.
    - Armazenar o resultado das operações concluídas do Windows Update.
- **Serviço de Agente do Nó**: é um serviço sem estado, executado em todos os nós de cluster do Service Fabric. O serviço é responsável por:
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

### <a name="minimum-supported-service-fabric-runtime-version"></a>Versão de tempo de execução do Service Fabric com suporte mínimo

#### <a name="azure-clusters"></a>Clusters do Azure
O aplicativo de orquestração de patch deve ser executado nos clusters do Azure que têm a versão de tempo de execução v5.5 ou posterior do Service Fabric.

#### <a name="standalone-on-premises-clusters"></a>Clusters locais autônomos
O aplicativo de orquestração de patch deve ser executado nos clusters autônomos que têm a versão de tempo de execução v5.6 ou posterior do Service Fabric.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Habilite o serviço do gerenciador de reparo (se ainda não estiver em execução)

O aplicativo de orquestração de patch exige que o serviço do sistema do gerenciador de reparo esteja habilitado no cluster.

#### <a name="azure-clusters"></a>Clusters do Azure

Clusters do Azure na camada de durabilidade prata têm o serviço do gerenciador de reparo habilitado por padrão. Clusters do Azure na camada de durabilidade ouro podem ou não ter o serviço do gerenciador de reparo habilitado, dependendo de quando esses clusters foram criados. Cluster do Azure na camada de durabilidade bronze, por padrão, não têm o serviço do gerenciador de reparo habilitado. Se o serviço já está habilitado, você pode ver ele em execução na seção de serviços do sistema no Service Fabric Explorer.

##### <a name="azure-portal"></a>Portal do Azure
Você pode habilitar o Gerenciador de reparo do portal do Azure no momento da configuração do cluster. Selecione a opção **Incluir Gerenciador de Reparos** em **Recursos de complemento** no momento da configuração do cluster.
![Imagem do Gerenciador de reparo de habilitação do portal do Azure](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager
Como alternativa, você pode usar o [modelo do Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) para habilitar o serviço do gerenciador de reparo em clusters novos e existentes do Service Fabric. Obtenha o modelo para o cluster que você deseja implantar. Você pode usar os modelos de exemplo ou criar um modelo do Resource Manager personalizado. 

Para habilitar o serviço de Gerenciador de reparo usando [modelo do Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm):

1. Primeiro, verifique se a `apiversion` está definida como `2017-07-01-preview` para o recurso `Microsoft.ServiceFabric/clusters`, conforme mostrado no trecho a seguir. Se for diferente, você precisará atualizar a `apiVersion` para o valor `2017-07-01-preview`:

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

2. Agora habilite o serviço do gerenciador de reparo adicionando a seguinte seção `addonFeaturres` após a seção `fabricSettings`, conforme mostrado abaixo:

    ```json
    "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "RepairManager"
        ],
    ```

3. Atualize o manifesto do cluster com essas alterações, usando o manifesto do cluster atualizado [crie um novo cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) ou [atualize a configuração do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server#Upgrade-the-cluster-configuration). Com o cluster em execução com o manifesto do cluster atualizado, agora você poderá ver o serviço do sistema do gerenciador de reparo em execução no seu cluster, que é chamado de `fabric:/System/RepairManagerService`, sob a seção de serviços do sistema no Service Fabric Explorer.

### <a name="disable-automatic-windows-update-on-all-nodes"></a>Desabilite o Windows Update automático em todos os nós

As atualizações automáticas do Windows podem causar a perda de disponibilidade porque vários nós de cluster podem ser reiniciados ao mesmo tempo. O aplicativo de orquestração de patch, por padrão tenta desabilitar o Windows Update automático em cada nó de cluster. No entanto, se as configurações forem gerenciadas pela política de grupo ou administrador, é recomendável configurar a política do Windows Update explicitamente como "Notificar antes de baixar".

### <a name="optional-enable-azure-diagnostics"></a>Opcional: habilitar o Diagnóstico do Microsoft Azure

Clusters que executam a versão de tempo de execução do Service Fabric `5.6.220.9494` e acima de logs de aplicativo de orquestração patch coletar como parte da malha do serviço de logs.
Você pode ignorar esta etapa se o cluster está em execução na versão de tempo de execução do Service Fabric `5.6.220.9494` e acima.

Para clusters que executam a versão de tempo de execução do Service Fabric menor `5.6.220.9494`, logs do aplicativo de orquestração de patch são coletados localmente em cada um de nós do cluster.
É recomendável que você configure o diagnóstico do Azure para carregar os logs de todos os nós em um local central.

Para obter mais informações sobre o Diagnóstico do Microsoft Azure, consulte [Coletar logs usando o Diagnóstico do Microsoft Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-how-to-setup-wad).

Os logs para o aplicativo de orquestração de patch serão gerados nas seguintes IDs de provedor fixas:

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

No modelo do Resource Manager acesse a seção `EtwEventSourceProviderConfiguration` em `WadCfg` e adicione as seguintes entradas:

```json
  {
    "provider": "e39b723c-590c-4090-abb0-11e3e6616346",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
      "eventDestination": "PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "fc0028ff-bfdc-499f-80dc-ed922c52c5e9",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "24afa313-0d3b-4c7c-b485-1047fd964b60",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "05dc046c-60e9-4ef7-965e-91660adffa68",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  }
```

> [!NOTE]
> Se o cluster do Service Fabric tiver vários tipos de nó, a seção anterior deve ser adicionada para todas as seções `WadCfg`.

## <a name="download-the-app-package"></a>Baixar o pacote do aplicativo

Baixe o aplicativo usando o [link para download](https://go.microsoft.com/fwlink/P/?linkid=849590).

## <a name="configure-the-app"></a>Configurar o aplicativo

O comportamento do aplicativo de orquestração de patch pode ser configurado para atender às suas necessidades. Substitua os valores padrão passando o parâmetro de aplicativo durante a criação ou atualização do aplicativo. Parâmetros do aplicativo podem ser fornecidos especificando `ApplicationParameter` aos cmdlets `Start-ServiceFabricApplicationUpgrade` ou `New-ServiceFabricApplication`.

|**Parâmetro**        |**Tipo**                          | **Detalhes**|
|:-|-|-|
|MaxResultsToCache    |long                              | Número máximo de resultados do Windows Update, que devem ser armazenados em cache. <br>O valor padrão é 3000, supondo que o: <br> - Número de nós é 20. <br> - Número de atualizações acontecendo em um nó por mês seja de cinco. <br> – Número de resultados por operação possa ser de 10. <br> - Resultados para os últimos três meses devem ser armazenados. |
|TaskApprovalPolicy   |Enum <br> { NodeWise, UpgradeDomainWise }                          |A TaskApprovalPolicy indica a política a ser usada pelo Serviço do Coordinator para instalar atualizações do Windows em todos os nós de cluster do Service Fabric.<br>                         Valores permitidos são: <br>                                                           <b>NodeWise</b>. O Windows Update é instalado em um nó por vez. <br>                                                           <b>UpgradeDomainWise</b>. O Windows Update é instalado em um domínio de atualização por vez. (No máximo, todos os nós que pertencem a um domínio de atualização podem ir para o Windows Update.)
|LogsDiskQuotaInMB   |long  <br> (Padrão: 1024)               |Tamanho máximo dos logs do aplicativo de orquestração de patch em MB, que pode ser mantido localmente no nó.
| WUQuery               | string<br>(Padrão: "IsInstalled=0")                | Consulta para obter atualizações do Windows. Para obter mais informações, consulte [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| InstallWindowsOSOnlyUpdates | Bool <br> (padrão: True)                 | Esse sinalizador permite a instalação das atualizações do sistema operacional Windows.            |
| WUOperationTimeOutInMinutes | int <br>(Padrão: 90)                   | Especifica o tempo limite para qualquer operação do Windows Update (pesquisar, baixar ou instalar). Se a operação não for concluída dentro do tempo limite especificado, ela será anulada.       |
| WURescheduleCount     | int <br> (Padrão: 5)                  | O número máximo de vezes que o serviço reagendaria o Windows Update no caso de falha persistente na operação.          |
| WURescheduleTimeInMinutes | int <br>(Padrão: 30) | O intervalo ao qual o serviço reagendaria o Windows Update no caso de persistência da falha. |
| WUFrequency           | Cadeia de caracteres separada por vírgula (padrão: "Semanalmente, quarta-feira, 7:00:00")     | A frequência para a instalação do Windows Update. O formato e os valores possíveis são: <br>-   Mensal, DD, HH:MM:SS, por exemplo, Mensal, 5, 12:22:32. <br> -   Semanal, DIA, HH:MM:SS, por exemplo, Semanal, terça-feira, 12:22:32.  <br> -   Diário, HH:MM:SS, por exemplo, Diário, 12:22:32.  <br> -  Nenhum indica que o Windows Update não deve ser executado.  <br><br> Observe que todos os horários estão em UTC.|
| AcceptWindowsUpdateEula | Bool <br>(Padrão: true) | Ao definir esse sinalizador, o aplicativo aceita o Contrato de licença do usuário final para o Windows Update em nome do proprietário do computador.              |

> [!TIP]
> Se você quiser que o Windows Update seja executado imediatamente, defina `WUFrequency` em relação ao tempo de implantação do aplicativo. Por exemplo, suponha que você tem um cluster de teste de cinco nós e planeja implantar o aplicativo em torno de 5:00 PM UTC. Se você considera que o upgrade ou implantação do aplicativo leva 30 minutos no máximo, defina a WUFrequency como "Diariamente, 17:30:00".

## <a name="deploy-the-app"></a>Implantar o aplicativo

1. Conclua todas as etapas necessárias para preparar o cluster.
2. Implante o aplicativo de orquestração de patch como qualquer outro aplicativo do Service Fabric. Você pode implantar o aplicativo usando o PowerShell. Siga as etapas em [Implantar e remover aplicativos usando o PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
3. Para configurar o aplicativo no momento da implantação, passe o `ApplicationParamater` para o cmdlet `New-ServiceFabricApplication`. Para sua conveniência, fornecemos o script Deploy.ps1 juntamente com o aplicativo. Para usar o script:

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
OperationType | 1 - Instalação<br> 0 - Pesquisar e baixar.| A instalação é o único OperationType exibido por padrão nos resultados.
WindowsUpdateQuery | O padrão é "IsInstalled=0" |Consulta do Windows Update que foi usada para procurar atualizações. Para obter mais informações, consulte [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
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

### <a name="collect-patch-orchestration-app-logs"></a>Colete logs do aplicativo de orquestração de patch

Os logs do aplicativo de orquestração de patch são coletados como parte dos logs do Service Fabric a partir da versão de tempo de execução `5.6.220.9494` e superior.
Para clusters que executam a versão de tempo de execução do Service Fabric anteriores à `5.6.220.9494`, os logs podem ser coletados usando um dos métodos a seguir.

#### <a name="locally-on-each-node"></a>Localmente em cada nó

Logs são coletados localmente em cada nó de cluster do Service Fabric se a versão de tempo de execução do Service Fabric é menor que `5.6.220.9494`. O local para acessar os logs é \[Service Fabric\_Instalação\_Unidade\]:\\PatchOrchestrationApplication\\logs.

Por exemplo, se o Service Fabric for instalado na unidade D, o caminho será D:\\PatchOrchestrationApplication\\logs.

#### <a name="central-location"></a>Local central

Se o Diagnóstico do Microsoft Azure estiver configurado como parte das etapas de pré-requisitos, os logs para o aplicativo de orquestração de patch estarão disponíveis no Armazenamento do Microsoft Azure.

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

R. Durante o processo de instalação, o aplicativo de orquestração de patch desabilita ou reinicia os nós, isso pode resultar em redução temporária da integridade do cluster.

Com base na política para o aplicativo, um nó pode ficar inativo durante uma operação de aplicação de patch *ou* todo um domínio de atualização pode ficar inativo ao mesmo tempo.

Até o fim da instalação do Windows Update, os nós serão reabilitados após a reinicialização.

No exemplo a seguir, o cluster veio de um estado de erro temporário porque dois nós estavam inativos e a política MaxPercentageUnhealthNodes foi violada. O erro é temporário até que a operação de aplicação de patch esteja em andamento.

![Imagem do cluster não íntegro](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Caso o problema persista, consulte a seção de Solução de problemas.

P. **O aplicativo de orquestração de patch está em estado de aviso**

R. Verifique para ver se um relatório de integridade publicado em relação ao aplicativo é a causa raiz. Geralmente, o aviso contém detalhes do problema. Se o problema for transitório, o aplicativo deve esperar recuperar-se automaticamente desse estado.

P. **O que fazer se o cluster não está íntegro e preciso fazer uma atualização urgente do sistema operacional?**

R. O aplicativo de orquestração de patch não instala atualizações enquanto o cluster não está íntegro. Tente colocar o cluster em um estado íntegro para desbloquear o fluxo de trabalho do aplicativo de orquestração de patch.

P. **Por que a execução da aplicação de patch nos clusters leva tanto tempo?**

R. O tempo que o aplicativo de orquestração de patch leva depende principalmente dos seguintes fatores:

- A política do Serviço do Coordinator. 
  - A política padrão, `NodeWise`, resulta na aplicação de patch em apenas um nó por vez. Especialmente no caso de clusters maiores, é recomendável que você use a política `UpgradeDomainWise` para alcançar a aplicação de patch em clusters mais rápida.
- O número de atualizações disponíveis para baixar e instalar. 
- O tempo médio necessário para baixar e instalar uma atualização, que não deve exceder alguma horas.
- O desempenho da VM e largura da banda de rede.

P. **Por que vejo algumas atualizações no Windows Update resultados obtidos por meio da API REST, mas não sob o histórico do Windows Update no computador?**

R. Algumas atualizações de produto precisam ser verificadas no seu respectivo histórico de atualização/aplicação de patch. Por exemplo, atualizações do Windows Defender não aparecem no histórico do Windows Update no Windows Server 2016.

## <a name="disclaimers"></a>Avisos de Isenção de Responsabilidade

- O aplicativo de orquestração de patch aceita o Contrato de licença do usuário final do Windows Update em nome do usuário. A definição opcionalmente pode ser desativada na configuração do aplicativo.

- O aplicativo de orquestração de patch coleta a telemetria para acompanhar o uso e o desempenho. A telemetria do aplicativo segue a definição da configuração de telemetria do tempo de execução do Service Fabric (ativada por padrão).

## <a name="troubleshooting"></a>Solucionar problemas

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

### <a name="version-120-latest"></a>Versão 1.2.0 (mais recente)

- Correções de bugs em torno do fluxo de trabalho de reinício do sistema.
- Correção de bug na criação de tarefas RM devido à qual integridade seleção durante a preparação de tarefas de reparo não estava acontecendo conforme o esperado.
- Alterado o modo de inicialização para o serviço windows POANodeSvc de auto para delayed-auto.
