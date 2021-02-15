---
title: Patch do sistema operacional Windows em seu cluster do Service Fabric
description: Este artigo discute como automatizar a aplicação de patch do sistema operacional em um Cluster Service Fabric usando o aplicativo de orquestração de patch.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: atsenthi
ms.openlocfilehash: 7d52d49ab5d3a47dd69fdc1708f9e52f4f796a92
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390633"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Patch do sistema operacional Windows em seu cluster do Service Fabric

> [!IMPORTANT]
> A partir de 30 de abril de 2019, o aplicativo de orquestração de patch versão 1,2. * não é mais suportado. Certifique-se de atualizar para a versão mais recente. Atualizações de VM em que "Windows Update" aplica patches de sistema operacional sem substituir o disco do sistema operacional não têm suporte. 

> [!NOTE]
> Obter [atualizações automáticas de imagem do sistema operacional em seu conjunto de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) é a melhor prática para manter seu sistema operacional corrigido no Azure. O conjunto de dimensionamento de máquinas virtuais baseado em atualizações automáticas de imagem de so exigirá uma durabilidade prateada ou maior em uma escala. Em tipos de nó com nível de durabilidade bronze, isso não tem suporte, nesse caso, use o aplicativo de orquestração de patch.

O POA (aplicativo de orquestração de patch) é um wrapper em todo o serviço de Gerenciador de Reparos Service Fabric do Azure, que permite o agendamento de patches de so baseado em configuração para clusters não hospedados no Azure. O POA não é necessário para clusters não hospedados no Azure, mas o agendamento de instalação de patch por domínio de atualização é necessário para patches Service Fabric hosts de cluster sem incorrer em tempo de inatividade.

O POA é um aplicativo Service Fabric que automatiza a aplicação de patches do sistema operacional em um Cluster Service Fabric sem incorrer em tempo de inatividade.

O POA fornece os seguintes recursos:

- **Instalação da atualização automática do sistema operacional**. Atualizações do sistema operacional são baixadas e instaladas automaticamente. Nós de cluster são reinicializados conforme necessário sem incorrer em tempo de inatividade do cluster.

- **Aplicação de patch com suporte a cluster e integração de integridade**. Embora o POA esteja aplicando atualizações, ele monitora a integridade dos nós do cluster. Nós de cluster são atualizados em um nó por vez ou em um domínio de atualização por vez. Se a integridade do cluster falhar devido ao processo de aplicação de patch, a aplicação de patches será interrompida para evitar o agravar do problema.

## <a name="internal-details-of-poa"></a>Detalhes internos de POA

O POA é composto pelos seguintes subcomponentes:

- **Serviço de Coordenador**: este serviço com estado é responsável por:
    - Coordenar o trabalho do Windows Update em todo o cluster.
    - Armazenar o resultado das operações concluídas do Windows Update.

- **Serviço de Agente do Nó**: é um serviço sem estado, executado em todos os nós de cluster do Service Fabric. O serviço é responsável por:
    - Inicialização de NTService do Agente do Nó.
    - Monitoramento do NTService do Agente do Nó.

- **NTService do Agente do Nó**: este serviço Windows NT é executado em um privilégio de nível superior (SISTEMA). Em comparação, o Serviço de Agente do Nó e o Serviço de Coordenador são executados em um nível inferior de privilégio (SERVIÇO DE REDE). O serviço é responsável por executar os seguintes trabalhos do Windows Update em todos os nós de cluster:
    - Desabilitando atualizações automáticas do Windows no nó.
    - Baixar e instalar atualizações do Windows de acordo com a política que o usuário forneceu.
    - Reiniciando a instalação de atualizações do post-Windows do computador.
    - Carregar os resultados das atualizações do Windows para o Serviço do Coordenador.
    - Relatórios de integridade de relatórios se uma operação tiver falhado depois de esgotar todas as novas tentativas.

> [!NOTE]
> O POA usa o serviço de Gerenciador de Reparos Service Fabric para desabilitar ou habilitar o nó e realizar verificações de integridade. A tarefa de reparo criada pelo POA rastreia o Windows Update andamento de cada nó.

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]
> A versão de .NET Framework mínima necessária é 4,6.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Habilitar o serviço de Gerenciador de Reparos (se ele ainda não estiver em execução)

POA exige que o serviço de Gerenciador de Reparos seja habilitado no cluster.

#### <a name="azure-clusters"></a>Clusters do Azure

Os clusters do Azure na camada de durabilidade prateada têm o serviço Gerenciador de Reparos habilitado por padrão. Os clusters do Azure na camada de durabilidade Gold podem ou não ter o serviço Gerenciador de Reparos habilitado, dependendo de quando esses clusters foram criados. Os clusters do Azure na camada de durabilidade bronze, por padrão, não têm o serviço Gerenciador de Reparos habilitado. Se o serviço já estiver habilitado, você poderá vê-lo em execução na seção serviços do sistema em Service Fabric Explorer.

##### <a name="the-azure-portal"></a>O portal do Azure
Você pode habilitar Gerenciador de Reparos do portal do Azure ao configurar o cluster. Quando você estiver configurando o cluster, selecione a opção **incluir Gerenciador de reparos** em **recursos de complemento**.

![Imagem de habilitação de Gerenciador de Reparos do portal do Azure](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="the-azure-resource-manager-deployment-model"></a>O modelo de implantação Azure Resource Manager
Como alternativa, você pode usar o [modelo de implantação Azure Resource Manager](./service-fabric-cluster-creation-via-arm.md) para habilitar o serviço de Gerenciador de reparos em clusters de Service Fabric novos e existentes. Obtenha o modelo para o cluster que você deseja implantar. Você pode usar os modelos de exemplo ou criar um modelo de implantação do Azure Resource Manager personalizado. 

Para habilitar o serviço de Gerenciador de Reparos usando o [modelo de modelo de implantação Azure Resource Manager](./service-fabric-cluster-creation-via-arm.md), faça o seguinte:

1. Verifique se `apiVersion` o está definido como *2017-07-01-Preview* para o recurso *Microsoft. perfabric/clusters* . Se for diferente, você precisará atualizar `apiVersion` para *2017-07-01-Preview* ou posterior:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

1. Habilite o serviço de Gerenciador de Reparos adicionando a seguinte `addonFeatures` seção após a `fabricSettings` seção:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Depois de atualizar o modelo de cluster com essas alterações, aplique-as e permita que a atualização seja concluída. Agora você pode ver o serviço Gerenciador de Reparos em execução no cluster. Ele é chamado de *Fabric:/System/RepairManagerService* na seção serviços do sistema em Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Clusters locais autônomos

Para habilitar o serviço de Gerenciador de Reparos em um cluster de Service Fabric novo ou existente, você pode usar as [definições de configuração para o cluster autônomo do Windows](./service-fabric-cluster-manifest.md).

Para habilitar o serviço de Gerenciador de Reparos:

1. Verifique se `apiVersion` [as configurações de cluster geral](./service-fabric-cluster-manifest.md#general-cluster-configurations) estão definidas como *04-2017* ou posteriores, conforme mostrado aqui:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

1. Habilite o serviço de Gerenciador de Reparos adicionando a `addonFeatures` seção a seguir após a `fabricSettings` seção, conforme mostrado aqui:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

1. Atualize o manifesto do cluster com essas alterações usando o manifesto de cluster atualizado [crie um novo cluster](./service-fabric-cluster-creation-for-windows-server.md) ou [atualize a configuração do cluster](./service-fabric-cluster-upgrade-windows-server.md). 

   Depois que o cluster estiver em execução com um manifesto de cluster atualizado, você poderá ver o serviço de Gerenciador de Reparos em execução no cluster. Ele é chamado de *Fabric:/System/RepairManagerService* e está na seção serviços do sistema em Service Fabric Explorer.

### <a name="configure-windows-updates-for-all-nodes"></a>Configurar atualizações do Windows para todos os nós

As atualizações automáticas do Windows podem levar à perda de disponibilidade, pois vários nós de cluster podem ser reiniciados ao mesmo tempo. O POA, por padrão, tenta desabilitar as atualizações automáticas do Windows em cada nó de cluster. No entanto, se as configurações forem gerenciadas por um administrador ou um Política de Grupo, recomendamos definir a política de Windows Update como "notificar antes do download" explicitamente.

## <a name="download-the-application-package"></a>Baixar o pacote de aplicativos

Para baixar o pacote de aplicativos, vá para a [página de lançamento do aplicativo de orquestração de patch](https://github.com/microsoft/Service-Fabric-POA/releases/latest/) no github.

## <a name="configure-poa-behavior"></a>Configurar o comportamento de POA

Você pode configurar o comportamento do POA para atender às suas necessidades. Substitua os valores padrão passando o parâmetro Application enquanto você estiver criando ou atualizando o aplicativo. Você pode fornecer parâmetros de aplicativo especificando `ApplicationParameter` para os `Start-ServiceFabricApplicationUpgrade` `New-ServiceFabricApplication` cmdlets ou.

| Parâmetro        | Tipo                          | Detalhes |
|:-|-|-|
|MaxResultsToCache    |long                              | O número máximo de resultados de Windows Update que devem ser armazenados em cache. <br><br>O valor padrão é 3000, supondo que: <br> &nbsp;&nbsp;-O número de nós é 20. <br> &nbsp;&nbsp;-O número de atualizações para um nó por mês é 5. <br> &nbsp;&nbsp;-O número de resultados por operação pode ser 10. <br> &nbsp;&nbsp;-Os resultados dos últimos três meses devem ser armazenados. |
|TaskApprovalPolicy   |Enumeração <br> { NodeWise, UpgradeDomainWise }                          |A TaskApprovalPolicy indica a política a ser usada pelo Serviço do Coordinator para instalar atualizações do Windows em todos os nós de cluster do Service Fabric.<br><br>Os valores permitidos são: <br>*NodeWise*: as atualizações do Windows são instaladas em um nó por vez. <br> *UpgradeDomainWise*: as atualizações do Windows são instaladas em um domínio de atualização por vez. (No máximo, todos os nós que pertencem a um domínio de atualização podem ir para um Windows Update.)<br><br> Para ajudar a decidir qual política é mais adequada para seu cluster, consulte a seção [perguntas frequentes](#frequently-asked-questions) .
|LogsDiskQuotaInMB   |long  <br> (Padrão: *1024*)               | O tamanho máximo dos logs do aplicativo de orquestração de patch em MB, que pode ser persistido localmente em nós.
| WUQuery               | string<br>(Padrão: *IsInstalled = 0*)                | Consulta para obter atualizações do Windows. Para obter mais informações, consulte [WuQuery.](/windows/win32/api/wuapi/nf-wuapi-iupdatesearcher-search)
| InstallWindowsOSOnlyUpdates | *Boolean* <br> (padrão: false)                 | Use esse sinalizador para controlar quais atualizações devem ser baixadas e instaladas. Os seguintes valores são permitidos <br>true – instala somente as atualizações do sistema operacional Windows.<br>false – instala todas as atualizações disponíveis no computador.          |
| WUOperationTimeOutInMinutes | int <br>(Padrão: *90*)                   | Especifica o tempo limite para qualquer operação do Windows Update (pesquisar, baixar ou instalar). Se a operação não for concluída dentro do tempo limite especificado, ela será anulada.       |
| WURescheduleCount     | int <br> (Padrão: *5*)                  | O número máximo de vezes que o serviço reagendará o Windows Update se uma operação falhar de forma persistente.          |
| WURescheduleTimeInMinutes | int <br>(Padrão: *30*) | O intervalo no qual o serviço reagendará as atualizações do Windows se a falha persistir. |
| WUFrequency           | Cadeia de caracteres separada por vírgula (padrão: *semanalmente, quarta-feira, 7:00:00*)     | A frequência de instalação das atualizações do Windows. O formato e os valores possíveis são: <br>-Mensal, DD, HH: MM: SS (exemplo: *mensal, 5, 12:22:32*). Os valores permitidos para o campo _DD_ (Day) são números de 1 a 28 e _Last_. <br>-Semanalmente, dia, HH: MM: SS (exemplo: *semanalmente, terça-feira, 12:22:32*)  <br>-Diário, HH: MM: SS (exemplo: *Daily, 12:22:32*)  <br>-Week, Day, HH: MM: SS (exemplo: *2, sexta-feira, 21:00:00* indica 9:00 PM UTC na sexta-feira da segunda semana de cada mês) <br>- *Nenhum* indica que as atualizações do Windows não devem ser feitas.  <br><br> Os horários estão em UTC.|
| AcceptWindowsUpdateEula | Boolean <br>(Padrão: *true*) | Ao definir esse sinalizador, o aplicativo aceita o Contrato de licença do usuário final para o Windows Update em nome do proprietário do computador.              |

> [!TIP]
> Se você quiser que as atualizações do Windows ocorram imediatamente, defina `WUFrequency` em relação ao tempo de implantação do aplicativo. Por exemplo, suponha que você tem um cluster de teste de cinco nós e planeja implantar o aplicativo em torno de 5:00 PM UTC. Se você assumir que a atualização ou a implantação do aplicativo levará 30 minutos no máximo, defina o WUFrequency como *diário, 17:30:00*.

## <a name="deploy-poa"></a>Implantar POA

1. Conclua todas as etapas necessárias para preparar o cluster.
1. Implante POA como qualquer outro aplicativo Service Fabric. Para implantá-lo usando o PowerShell, consulte [implantar e remover aplicativos usando o PowerShell](./service-fabric-deploy-remove-applications.md).
1. Para configurar o aplicativo no momento da implantação, passe o `ApplicationParameter` para o cmdlet `New-ServiceFabricApplication`. Para sua conveniência, fornecemos o script Deploy.ps1 juntamente com o aplicativo. Para usar o script:

    - Conectar a um cluster do Service Fabric usando `Connect-ServiceFabricCluster`.
    - Execute o script do PowerShell Deploy.ps1 com o valor `ApplicationParameter` apropriado.

> [!NOTE]
> Mantenha o script e a pasta do aplicativo *PatchOrchestrationApplication* no mesmo diretório.

## <a name="upgrade-poa"></a>Atualizar POA

Para atualizar sua versão do POA usando o PowerShell, siga as instruções em [Service Fabric atualização do aplicativo usando o PowerShell](./service-fabric-application-upgrade-tutorial-powershell.md).

## <a name="remove-poa"></a>Remover POA

Para remover o aplicativo, siga as instruções em [implantar e remover aplicativos usando o PowerShell](./service-fabric-deploy-remove-applications.md).

Para sua conveniência, fornecemos o script Undeploy.ps1 junto com o aplicativo. Para usar o script:

  - Conectar a um cluster do Service Fabric usando ```Connect-ServiceFabricCluster```.
  - Execute o script do PowerShell Undeploy.ps1.

> [!NOTE]
> Mantenha o script e a pasta do aplicativo *PatchOrchestrationApplication* no mesmo diretório.

## <a name="view-the-windows-update-results"></a>Exibir os resultados do Windows Update

O POA expõe as APIs REST para exibir os resultados históricos para os usuários. Aqui está um exemplo do resultado JSON:

```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2019-05-13T08:44:56.4836889Z",
        "OperationStartTime": "2019-05-13T08:44:33.5285601Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0,
            "HResult": 0
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

Os campos JSON são descritos na tabela a seguir:

Campo | Valores | Detalhes
-- | -- | --
OperationResult | 0 - Êxito<br> 1 - Êxito com erros<br> 2 - Falha<br> 3 - Anulado<br> 4 - Anulado com tempo limite | Indica o resultado da operação geral, que normalmente envolve a instalação de uma ou mais atualizações.
ResultCode | O mesmo que OperationResult | Esse campo indica o resultado da operação de instalação para uma atualização individual.
OperationType | 1 - Instalação<br> 0-Pesquisar e baixar| Por padrão, a instalação é o único OperationType mostrado nos resultados.
WindowsUpdateQuery | O padrão é "IsInstalled=0" | A consulta Windows Update que foi usada para Pesquisar atualizações. Para obter mais informações, consulte [WuQuery](/windows/win32/api/wuapi/nf-wuapi-iupdatesearcher-search).
RebootRequired | true - a reinicialização foi necessária<br> false - a reinicialização não foi necessária | Indica se uma reinicialização foi necessária para concluir a instalação das atualizações.
OperationStartTime | Datetime | Indica a hora em que a operação (download/instalação) foi iniciada.
Operationtime | Datetime | Indica a hora em que a operação (download/instalação) foi concluída.
HResult | 0-êxito<br> outro-falha| Indica o motivo da falha da atualização do Windows com a UpdateId "7392acaf-6a85-427c-8a8d-058c25beb0d6".

Se nenhuma atualização estiver agendada ainda, o resultado JSON estará vazio.

Entre no cluster para consultar Windows Update resultados. Descubra o endereço IP de réplica para o endereço principal do serviço de coordenador e abra a seguinte URL no navegador: http:// &lt; réplica-IP &gt; : &lt; ApplicationPort &gt; /PatchOrchestrationApplication/v1/GetWindowsUpdateResults.

O ponto de extremidade REST para o Serviço do Coordenador tem uma porta dinâmica. Para verificar a URL exata, consulte Service Fabric Explorer. Por exemplo, os resultados estão disponíveis em *http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults* .

![Imagem do ponto de extremidade REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

Se o proxy reverso estiver habilitado no cluster, você também poderá acessar a URL de fora do cluster.

O ponto de extremidade que você precisa atingir é *http:// &lt; ServerURL &gt; : &lt; REVERSEPROXYPORT &gt; /PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults*.

Para habilitar o proxy reverso no cluster, siga as instruções em [proxy reverso no Azure Service Fabric](./service-fabric-reverseproxy.md). 

> 
> [!WARNING]
> Depois que o proxy reverso é configurado, todos os microserviços no cluster que expõem um ponto de extremidade HTTP são endereçáveis de fora do cluster.

## <a name="diagnostics-and-health-events"></a>Diagnóstico e eventos de integridade

Esta seção discute como depurar ou diagnosticar problemas com atualizações de patch por meio do POA em clusters Service Fabric.

> [!NOTE]
> Para obter muitos dos seguintes aprimoramentos de diagnóstico, desconhecidos, você deve ter o POA versão 1.4.0 ou posterior instalado.

O NTService do agente de nó cria [tarefas de reparo](/dotnet/api/system.fabric.repair.repairtask) para instalar atualizações nos nós. Cada tarefa é preparada pelo serviço de coordenador de acordo com a política de aprovação de tarefa. Por fim, as tarefas preparadas são aprovadas por Gerenciador de Reparos, que não aprova nenhuma tarefa se o cluster estiver em um estado não íntegro. 

Para ajudá-lo a entender como as atualizações procedem em um nó, vamos passar passo a passo:

1. NodeAgentNTService, em execução em cada nó, procura atualizações disponíveis do Windows no horário agendado. Se as atualizações estiverem disponíveis, elas serão baixadas no nó.

1. Depois que as atualizações forem baixadas, o NTService do agente de nó criará uma tarefa de reparo correspondente para o nó com o nome *POS___ \<unique_id>*. Você pode exibir essas tarefas de reparo usando o cmdlet [Get-ServiceFabricRepairTask](/powershell/module/servicefabric/get-servicefabricrepairtask) ou usando o SFX na seção detalhes do nó. Depois que a tarefa de reparo for criada, ela passará rapidamente para o [estado *declarado*](/dotnet/api/system.fabric.repair.repairtaskstate).

1. Periodicamente, o serviço de coordenador procura tarefas de reparo no estado *declarado* e, em seguida, atualiza-as para *preparando* o estado com base em a taskapprovalpolicy. Se A taskapprovalpolicy estiver configurado para ser NodeWise, uma tarefa de reparo que corresponde a um nó será preparada somente se nenhuma outra tarefa de reparo estiver atualmente em *preparação*, *aprovado*, em *execução* ou em estado de *restauração* . 

   Da mesma forma, no caso de UpgradeWise A taskapprovalpolicy, há tarefas nos Estados anteriores somente para os nós que pertencem ao mesmo domínio de atualização. Depois que uma tarefa de reparo for movida para o estado de *preparação* , o nó de Service Fabric correspondente será [desabilitado](/powershell/module/servicefabric/disable-servicefabricnode) com a intenção definida para *reiniciar*.

   POA versões 1.4.0 e posteriores postam eventos com a propriedade ClusterPatchingStatus em CoordinatorService para exibir os nós que estão sendo corrigidos. As atualizações são instaladas em _poanode_0, conforme mostrado na imagem a seguir:

    [![Imagem do status de aplicação de patch do cluster](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

1. Depois que o nó é desabilitado, a tarefa de reparo é movida para o estado de *execução* . 
   
   > [!NOTE]
   > Um nó que está preso no estado *desabilitado* pode bloquear uma nova tarefa de reparo, o que interrompe a operação de aplicação de patch no cluster.

1. Quando a tarefa de reparo estiver em estado de *execução* , a instalação do patch nesse nó será iniciada. Depois que o patch é instalado, o nó pode ou não ser reiniciado, dependendo do patch. Em seguida, a tarefa de reparo é movida para o estado de *restauração* , que reabilita o nó. A tarefa de reparo é marcada como concluída.

   No POA versões 1.4.0 e posteriores, você pode encontrar o status da atualização exibindo os eventos de integridade em NodeAgentService com a \<NodeName> Propriedade WUOperationStatus. As seções realçadas nas imagens a seguir mostram o status das atualizações do Windows em nós *poanode_0* e *poanode_2*:

   [![Captura de tela mostra a janela do console de Windows Update status da operação com poanode_0 realçada.](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![Captura de tela mostra a janela do console de Windows Update status da operação com poanode_1 realçada.](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   Você também pode obter os detalhes usando o PowerShell. Para fazer isso, você se conecta ao cluster e busca o estado da tarefa de reparo usando [Get-ServiceFabricRepairTask](/powershell/module/servicefabric/get-servicefabricrepairtask). 
   
   No exemplo a seguir, a tarefa "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15" está no estado *DownloadComplete* . Isso significa que as atualizações foram baixadas no nó *poanode_2* , e a instalação será tentada quando a tarefa for movida para o estado de *execução* .

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   Se mais problemas ainda estiverem localizados, entre na VM (máquina virtual) ou nas VMs e saiba mais sobre eles usando os logs de eventos do Windows. A tarefa de reparo mencionada anteriormente pode existir somente nos seguintes subestados do executor:

      ExecutorSubState | Descrição
    -- | -- 
      Nenhum = 1 |  Implica que não havia uma operação em andamento no nó. O estado pode estar em transição.
      DownloadCompleted = 2 | Implica que a operação de download foi concluída com êxito, falha parcial ou falha.
      InstallationApproved = 3 | Implica que a operação de download foi concluída anteriormente e Gerenciador de Reparos aprovou a instalação.
      InstallationInProgress = 4 | Corresponde ao estado de execução da tarefa de reparo.
      InstallationCompleted = 5 | Implica que a instalação foi concluída com êxito, êxito parcial ou falha.
      RestartRequested = 6 | Implica que a instalação do patch foi concluída e que há uma ação de reinicialização pendente no nó.
      RestartNotNeeded = 7 |  Implica que a reinicialização não era necessária após a conclusão da instalação do patch.
      RestartCompleted = 8 | Implica que a reinicialização foi concluída com êxito.
      OperationCompleted = 9 | A operação de Windows Update foi concluída com êxito.
      OperationAborted = 10 | Implica que a operação de Windows Update foi anulada.

1. No POA versões 1.4.0 e posteriores, quando uma tentativa de atualização de nó é concluída, um evento com a propriedade "WUOperationStatus-[NodeName]" é Postado no NodeAgentService para notificá-lo quando a próxima tentativa de baixar e instalar as atualizações do Windows começar. Isso é exibido na imagem a seguir:

     [![Captura de tela mostra a janela do console de Windows Update status da operação com o NodeAgentService.](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostics-logs"></a>Logs de diagnóstico

Os logs do aplicativo de orquestração de patch são coletados como parte dos logs do Service Fabric Runtime.

Você pode capturar logs usando a ferramenta de diagnóstico ou pipeline de sua escolha. O POA usa as seguintes IDs de provedor fixas para registrar eventos por meio da [origem do evento](/dotnet/api/system.diagnostics.tracing.eventsource):

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Relatórios de integridade

O POA também publica relatórios de integridade no serviço de agente de nó ou no serviço de coordenador nos seguintes cenários:

* O NTService do Agente do Nó está inativo

   Se o NTService do Agente do Nó estiver inativo em um nó, o relatório de integridade no nível de aviso será gerado no Serviço de Agente do Nó.

* O serviço de Gerenciador de Reparos não está habilitado

   Se o serviço de Gerenciador de Reparos não for encontrado no cluster, um relatório de integridade no nível de aviso será gerado para o serviço de coordenador.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**P: por que vejo meu cluster em um estado de erro quando o POA está em execução?**

R: durante o processo de instalação, o POA desabilita ou reinicia os nós, o que pode resultar temporariamente em um cluster não íntegro.

Dependendo da política do aplicativo, um nó pode ficar inativo durante uma operação de aplicação de patch *ou* um domínio de atualização inteiro pode ficar inativo de uma só vez.

Ao final da instalação das atualizações do Windows, os nós são reabilitados após a reinicialização.

No exemplo a seguir, o cluster foi para um estado de erro temporariamente porque dois nós estavam inativos e a política MaxPercentageUnhealthyNodes foi violada. O erro é temporário até que a operação de aplicação de patch possa começar.

![Imagem do cluster não íntegro](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Caso o problema persista, consulte a seção de Solução de problemas.

**P: o que posso fazer se POA estiver em um estado de aviso?**

R: Verifique se um relatório de integridade Postado no aplicativo indica a causa raiz. Geralmente, o aviso contém detalhes do problema. Se o problema for transitório, espera-se que o aplicativo se recupere automaticamente.

**P: o que posso fazer se o cluster não estiver íntegro e eu precisar fazer uma atualização urgente do sistema operacional?**

R: o POA não instala atualizações enquanto o cluster não está íntegro. Tente colocar o cluster em um estado íntegro para desbloquear o fluxo de trabalho do POA.

**P: devo definir A taskapprovalpolicy como "NodeWise" ou "UpgradeDomainWise" para o meu cluster?**

R: a configuração "UpgradeDomainWise" acelera o reparo geral do cluster ao aplicar patches em paralelo em todos os nós que pertencem a um domínio de atualização. Durante o processo, os nós que pertencem a um domínio de atualização inteiro ficam indisponíveis (no [estado *desabilitado*](/dotnet/api/system.fabric.query.nodestatus#System_Fabric_Query_NodeStatus_Disabled)).

Por outro lado, a configuração "NodeWise" corrige apenas um nó por vez, o que significa que a aplicação geral de patches do cluster pode demorar mais. No entanto, apenas um nó no máximo não estará disponível (no estado *desabilitado* ) durante o processo de aplicação de patch.

Se o cluster puder tolerar a execução em um número N-1 de domínios de atualização durante o ciclo de aplicação de patch (em que N é o número total de domínios de atualização no cluster), você poderá definir a política como "UpgradeDomainWise". Caso contrário, defina-o como "NodeWise".

**P: quanto tempo leva para corrigir um nó?**

R: a aplicação de patch de um nó pode levar de minutos (por exemplo, [atualizações de definições do Windows Defender](https://www.microsoft.com/en-us/wdsi/definitions)) a horas (por exemplo, [atualizações cumulativas do Windows](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)). O tempo necessário para corrigir um nó depende principalmente de: 
 - O tamanho das atualizações.
 - O número de atualizações, que devem ser aplicadas em uma janela de aplicação de patch.
 - O tempo que leva para instalar as atualizações, reinicialize o nó (se necessário) e conclua as etapas de instalação após a reinicialização.
 - O desempenho da VM, da máquina e das condições de rede.

**P: quanto tempo leva para corrigir um cluster inteiro?**

R: o tempo necessário para corrigir um cluster inteiro depende de:

- O tempo necessário para aplicar patch a um nó.

- A política do Serviço do Coordinator. A política padrão, "NodeWise", resulta na aplicação de patch em apenas um nó por vez, uma abordagem que é mais lenta do que usar "UpgradeDomainWise". 

   Por exemplo: se um nó levar cerca de 1 hora para ser corrigido, a aplicação de patches em um cluster de 20 nós (mesmo tipo de nós) com 5 domínios de atualização, cada um contendo 4 nós, requer:
    - Para "NodeWise": ~ 20 horas.
    - Para "UpgradeDomainWise": ~ 5 horas.

- O carregamento do cluster. Cada operação de aplicação de patch requer a realocação da carga de trabalho do cliente para outros nós disponíveis no cluster. Um nó que está sendo corrigido estaria no estado de [ *desabilitação*](/dotnet/api/system.fabric.query.nodestatus#System_Fabric_Query_NodeStatus_Disabling) durante esse tempo. Se o cluster estiver executando perto da carga de pico, o processo de desabilitação levará mais tempo. Portanto, o processo geral de aplicação de patch pode parecer lento em tais condições sob medida.

- Falhas de integridade do cluster durante a aplicação de patch. Qualquer [degradação](/dotnet/api/system.fabric.health.healthstate#System_Fabric_Health_HealthState_Error) na [integridade do cluster](./service-fabric-health-introduction.md) interromperia o processo de aplicação de patch. Esse problema seria adicionado ao tempo geral necessário para aplicar o patch a todo o cluster.

**P: por que vejo algumas atualizações na Windows Update resultados obtidos por meio da API REST, mas não sob o histórico de Windows Update no computador?**

R: algumas atualizações do produto aparecem apenas em seu próprio histórico de atualização ou patch. Por exemplo, as atualizações do Windows Defender podem ou não ser exibidas no histórico de Windows Update no Windows Server 2016.

**P: POA pode ser usado para corrigir meu cluster de desenvolvimento (cluster de um nó)?**

R: não, o POA não pode ser usado para corrigir um cluster de um nó. Essa limitação é por design, porque [Service Fabric serviços do sistema](./service-fabric-technical-overview.md#system-services) ou outros aplicativos de cliente incorrem em tempo de inatividade. Portanto, os trabalhos de reparo de patch nunca seriam aprovados pelo Gerenciador de Reparos.

**P: Como fazer nós de cluster de patch no Linux?**

R: para saber mais sobre a orquestração de atualizações no Linux, confira [conjunto de dimensionamento de máquinas virtuais do Azure atualizações automáticas de imagem do sistema operacional](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md).

**P: por que o ciclo de atualização está demorando tanto tempo?**

R: consulte o resultado JSON, insira o ciclo de atualização de todos os nós e, em seguida, você pode tentar descobrir o tempo gasto pela instalação da atualização em cada nó usando OperationStartTime e Operationtime (OperationCompletionTime). 

Se houver uma janela de tempo grande na qual nenhuma atualização está ocorrendo, o cluster poderá estar em um estado de erro e, consequentemente, Gerenciador de Reparos não poderá aprovar nenhuma tarefa de reparo POA. Se a instalação da atualização estiver demorando muito em qualquer nó, esse nó poderá não ter sido atualizado há algum tempo. Muitas atualizações podem ter uma instalação pendente, o que pode resultar em atrasos. 

Também pode ser possível que a aplicação de patches de nó seja bloqueada porque está presa no estado de *desabilitação* . Isso geralmente acontece porque a desabilitação do nó pode levar a situações de perda de dados ou de quorum.

**P: por que o nó deve ser desabilitado quando o POA está corrigindo-o?**

R: POA desabilita o nó com uma intenção de *reinicialização* , que interrompe ou realoca todos os serviços de Service Fabric que estão em execução no nó. O POA faz isso para garantir que os aplicativos não acabem usando uma combinação de DLLs novas e antigas, portanto, é recomendável não aplicar patches em um nó sem desabilitá-lo.

**P: Qual é o número máximo de nós que podem ser atualizados usando POA?**

R: o POA usa Service Fabric Gerenciador de Reparos para criar tarefas de reparo para nós de atualizações. No entanto, não há mais do que 250 tarefas de reparo podem estar presentes ao mesmo tempo. Atualmente, o POA cria tarefas de reparo para cada nó ao mesmo tempo; portanto, o POA pode atualizar no máximo 250 nós em um cluster. 

## <a name="disclaimers"></a>Avisos de Isenção de Responsabilidade

- POA aceita o contrato de licença End-User para Windows Update em nome do usuário. A definição opcionalmente pode ser desativada na configuração do aplicativo.

- O POA coleta a telemetria para acompanhar o uso e o desempenho. A telemetria do aplicativo segue a definição da configuração de telemetria do runtime do Service Fabric (ativada por padrão).

## <a name="troubleshooting"></a>Solução de problemas

Esta seção fornece soluções de solução de problemas possíveis para problemas com nós de aplicação de patches.

### <a name="a-node-is-not-coming-back-to-up-state"></a>O nó não volta para o estado ativo

* O nó pode estar paralisado ao *desabilitar* o estado porque:

  - Uma verificação de segurança está pendente. Para corrigir essa situação, verifique se há nós suficientes disponíveis em um estado íntegro.

* O nó pode estar paralisado no estado *desabilitado* porque:

  - Ele foi desabilitado manualmente.
  - Ele foi desabilitado devido a um trabalho de infraestrutura do Azure em andamento.
  - Ele foi desabilitado temporariamente pelo POA para corrigir o nó.

* O nó pode estar paralisado em um estado inativo porque:

  - Ele foi colocado em um estado inativo manualmente.
  - Ele está passando por uma reinicialização (que pode ser disparada por POA).
  - Ele tem uma VM ou máquina com defeito, ou está tendo problemas de conectividade de rede.

### <a name="updates-were-skipped-on-some-nodes"></a>As atualizações foram ignoradas em alguns nós

O POA tenta instalar uma atualização do Windows de acordo com a política de reagendamento. O serviço tenta recuperar o nó e ignorar a atualização de acordo com a política do aplicativo.

Nesse caso, será gerado um relatório de integridade no nível de aviso em relação ao Serviço de Agente do Nó. O resultado de Windows Update também contém o motivo possível para a falha.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-is-being-installed"></a>A integridade do cluster vai para o erro enquanto a atualização está sendo instalada

Um Windows Update com falha pode reduzir a integridade de um aplicativo ou cluster em um nó ou domínio de atualização específico. POA descontinuará qualquer operação de Windows Update subsequente até que o cluster esteja íntegro novamente.

Um administrador deve intervir e determinar por que o aplicativo ou o cluster se tornou não íntegro devido à Windows Update.

## <a name="poa-release-notes"></a>Notas de versão do POA

>[!NOTE]
> Para POA versões 1.4.0 e posteriores, você pode encontrar notas de versão e versões na [página de lançamento do aplicativo de orquestração de patch](https://github.com/microsoft/Service-Fabric-POA/releases/) no github.

### <a name="version-110"></a>Version 1.1.0
- Versão pública

### <a name="version-111"></a>Versão 1.1.1
- Correção de bug no SetupEntryPoint de NodeAgentService que impediu a instalação de NodeAgentNTService.

### <a name="version-120"></a>Versão 1.2.0

- Correções de bugs em torno do fluxo de trabalho de reinício do sistema.
- Correção de bug na criação de tarefas RM devido à qual integridade seleção durante a preparação de tarefas de reparo não estava acontecendo conforme o esperado.
- O modo de inicialização do serviço Windows POANodeSvc foi alterado de automático para atrasado-automático.

### <a name="version-121"></a>Versão 1.2.1

- Correção de bug no fluxo de trabalho de redução de escala de cluster. Introduziu a lógica de coleta de lixo para tarefas de reparo POA pertencentes a nós inexistentes.

### <a name="version-122"></a>Versão 1.2.2

- Diversas correções de bugs.
- Binários agora são assinados.
- Adição do link do sfpkg para o aplicativo.

### <a name="version-130"></a>Versão 1.3.0

- A definição de InstallWindowsOSOnlyUpdates como falso agora instala todas as atualizações disponíveis.
- Alteração da lógica de desabilitação de atualizações automáticas. Isso corrige um bug em que as atualizações Automáticas não são desabilitadas no Server 2016 e posterior.
- Restrição de posicionamento com parâmetros para ambos os microserviços de POA para casos de uso avançados.

### <a name="version-131"></a>Versão 1.3.1
- Corrigindo a regressão em que POA 1.3.0 não funcionará no Windows Server 2012 R2 ou anterior devido a uma falha ao desabilitar as atualizações automáticas. 
- Correção de bug, em que a configuração InstallWindowsOSOnlyUpdates sempre é escolhida como True.
- Alteração do valor padrão de InstallWindowsOSOnlyUpdates para False.

### <a name="version-132"></a>Versão 1.3.2
- Corrigir um problema que afeta o ciclo de vida da aplicação de patch em um nó, se houver nós com um nome que seja um subconjunto do nome do nó atual. Para esses nós, é possível que os patches tenham sido perdidos ou uma reinicialização esteja pendente.
