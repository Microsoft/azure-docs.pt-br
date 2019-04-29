---
title: Aplicativo de orquestração de patch do Azure Service Fabric para Linux | Microsoft Docs
description: Aplicativo para automatizar a aplicação de patch do sistema operacional em um cluster do Service Fabric do Linux.
services: service-fabric
documentationcenter: .net
author: novino
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/22/2018
ms.author: nachandr
ms.openlocfilehash: 537450dbc386a94fa5c2e0d9334435dce041a32f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60647101"
---
# <a name="patch-the-linux-operating-system-in-your-service-fabric-cluster"></a>Patch do sistema operacional Linux em seu cluster do Service Fabric

> [!div class="op_single_selector"]
> * [Windows](service-fabric-patch-orchestration-application.md)
> * [Linux](service-fabric-patch-orchestration-application-linux.md)
>
>

O aplicativo de orquestração de patch é um aplicativo do Azure Service Fabric que automatiza a aplicação de patches do sistema operacional em um cluster do Service Fabric sem tempo de inatividade.

O aplicativo de orquestração de patch fornece os recursos a seguir:

- **Instalação da atualização automática do sistema operacional**. Atualizações do sistema operacional são baixadas e instaladas automaticamente. Nós de cluster são reiniciados conforme necessário, sem tempo de inatividade do cluster.

- **Aplicação de patch com suporte a cluster e integração de integridade**. Durante a aplicação de atualizações, o aplicativo de orquestração de patch monitora a integridade dos nós de cluster. Os nós de cluster fazem upgrade de um nó ou um domínio de atualização por vez. Se a integridade do cluster falhar devido ao processo de aplicação de patch, a aplicação de patch será interrompida para evitar agravar o problema.

## <a name="internal-details-of-the-app"></a>Detalhes internos do aplicativo

O aplicativo de orquestração de patch é composto dos seguintes subcomponentes:

- **Serviço de coordenador**: este serviço com estado é responsável por:
    - Coordenar o trabalho da Atualização do SO em todo o cluster.
    - Armazenar o resultado das operações concluídas da Atualização do SO.
- **Serviço de agente do nó**: esse serviço sem estado é executado em todos os nós de cluster do Service Fabric. O serviço é responsável por:
    - Inicialização de daemon do Agente do Nó no linux.
    - Monitorando o serviço daemon.
- **O daemon de agente do nó**: Este serviço daemon do Linux é executado em um privilégio de nível superior (raiz). Em comparação, o Serviço de Agente do Nó e o Serviço de Coordenador são executados em um nível inferior de privilégio. O serviço é responsável por executar os seguintes trabalhos de Atualização em todos os nós de cluster:
    - Desabilitar a Atualização do SO automática no nó.
    - Fazer o download e instalar a Atualização do SO de acordo com a política que o usuário forneceu.
    - Reiniciar o computador após a instalação da Atualização do SO se necessário.
    - Carregar os resultados das atualizações do SO para o Serviço do Coordenador.
    - Relatórios de integridade de relatórios no caso de falha na operação após esgotar todas as novas tentativas.

> [!NOTE]
> O aplicativo de orquestração de patch usa o serviço do sistema do gerenciador de reparo do Service Fabric para habilitar ou desabilitar o nó e executar as verificações de integridade. A tarefa de reparo criada pelo aplicativo de orquestração de patch rastreia o progresso da Atualização para cada nó.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="ensure-that-your-azure-vms-are-running-ubuntu-1604"></a>Certifique-se de que suas VMs do Azure estejam executando Ubuntu 16.04
No momento da elaboração deste documento, o Ubuntu 16.04 (`Xenial Xerus`) é a única versão compatível.

### <a name="ensure-that-the-service-fabric-linux-cluster-is-version-62x-and-above"></a>Certifique-se de que o cluster do service fabric linux esteja na versão 6.2.x ou posterior

O aplicativo de orquestração de patches linux usa alguns recursos de tempo de execução que só estão disponíveis na versão de tempo de execução do service fabric 6.2.x e posterior.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Habilite o serviço do gerenciador de reparo (se ainda não estiver em execução)

O aplicativo de orquestração de patch exige que o serviço do sistema do gerenciador de reparo esteja habilitado no cluster.

#### <a name="azure-clusters"></a>Clusters do Azure

Clusters Linux do Azure na camada de durabilidade prata e ouro têm o serviço do gerenciador de reparo habilitado por padrão. Cluster do Azure na camada de durabilidade bronze, por padrão, não têm o serviço do gerenciador de reparo habilitado. Se o serviço já está habilitado, você pode ver ele em execução na seção de serviços do sistema no Service Fabric Explorer.

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

Não há suporte para clusters autônomos de Linux do Service Fabric no momento da elaboração deste documento.

### <a name="disable-automatic-os-update-on-all-nodes"></a>Desabilite a Atualização automática do SO em todos os nós

As atualizações automáticas do SO podem levar à perda de disponibilidade e/ou à alteração no comportamento dos aplicativos em execução. O aplicativo de orquestração de patch, por padrão, tenta desabilitar a Atualização automática do SO em cada nó de cluster para impedir esses cenários.
Para Ubuntu, [atualizações autônomas](https://help.ubuntu.com/community/AutomaticSecurityUpdates) estão desabilitadas pelo aplicativo de orquestração de patches.

## <a name="download-the-app-package"></a>Baixar o pacote do aplicativo

O aplicativo juntamente com scripts de instalação podem ser baixados do [link de arquivo](https://go.microsoft.com/fwlink/?linkid=867984).

O aplicativo no formato sfpkg pode ser baixado do [link sfpkg](https://aka.ms/POA/POA_v2.0.3.sfpkg). Isso é útil para a [Implantação de aplicativo baseado no Azure Resource Manager](service-fabric-application-arm-resource.md).

## <a name="configure-the-app"></a>Configurar o aplicativo

O comportamento do aplicativo de orquestração de patch pode ser configurado para atender às suas necessidades. Substitua os valores padrão passando o parâmetro de aplicativo durante a criação ou atualização do aplicativo. Parâmetros do aplicativo podem ser fornecidos especificando `ApplicationParameter` aos cmdlets `Start-ServiceFabricApplicationUpgrade` ou `New-ServiceFabricApplication`.

|**Parâmetro**        |**Tipo**                          | **Detalhes**|
|:-|-|-|
|MaxResultsToCache    |long                              | Número máximo de resultados da Atualização, que devem ser armazenados em cache. <br>O valor padrão é 3000, supondo que o: <br> - Número de nós é 20. <br> - Número de atualizações acontecendo em um nó por mês seja de cinco. <br> – Número de resultados por operação possa ser de 10. <br> - Resultados para os últimos três meses devem ser armazenados. |
|TaskApprovalPolicy   |Enum <br> { NodeWise, UpgradeDomainWise }                          |A TaskApprovalPolicy indica a política a ser usada pelo Serviço do Coordinator para instalar atualizações em todos os nós de cluster do Service Fabric.<br>                         Valores permitidos são: <br>                                                           <b>NodeWise</b>. As atualizações são instaladas em um nó por vez. <br>                                                           <b>UpgradeDomainWise</b>. As atualizações são instaladas em um domínio de atualização por vez. (No máximo, todos os nós que pertencem a um domínio de atualização podem ir para a atualização.)
| UpdateOperationTimeOutInMinutes | Int <br>(Padrão: 180)                   | Especifica o tempo limite para qualquer operação de Atualização (fazer o download ou instalar). Se a operação não for concluída dentro do tempo limite especificado, ela será anulada.       |
| RescheduleCount      | Int <br> (Padrão: 5)                  | O número máximo de vezes que o serviço reagendaria a atualização do SO no caso de falha persistente na operação.          |
| RescheduleTimeInMinutes  | Int <br>(Padrão: 30) | O intervalo ao qual o serviço reagendaria a atualização do SO no caso de persistência da falha. |
| UpdateFrequency           | Cadeia de caracteres separada por vírgula (Padrão: "Semanais, quarta-feira, 7:00:00")     | A frequência para instalar atualizações do SO no cluster. O formato e os valores possíveis são: <br>-   Mensal, DD, HH:MM:SS, por exemplo, Mensal, 5, 12:22:32. <br> -   Semanal, DIA, HH:MM:SS, por exemplo, Semanal, terça-feira, 12:22:32.  <br> -   Diário, HH:MM:SS, por exemplo, Diário, 12:22:32.  <br> -  Nenhum indica que a atualização não deve ser executada.  <br><br> Todos os horários estão em UTC.|
| UpdateClassification | Cadeia de caracteres separada por vírgula (Padrão: "securityupdates") | Tipo de atualizações que devem ser instaladas nos nós de cluster. Os valores aceitáveis são securityupdates, all. <br> - securityupdates - instalaria somente atualizações de segurança <br> - all - instalaria todas as atualizações disponíveis do apt.|
| ApprovedPatches | Cadeia de caracteres separada por vírgula (padrão: "") | Essa é a lista de atualizações aprovadas que devem ser instaladas em nós de cluster. A lista separada por vírgulas contém pacotes aprovados e a versão de destino opcional.<br> Por exemplo: "apt-utils = 1.2.10ubuntu1, python3-jwt, apt-transport-https < 1.2.194, libsystemd0 >= 229-4ubuntu16" <br> Isso instalará <br> - apt-utils com versão 1.2.10ubuntu1 se estiver disponível no apt-cache. Se essa versão específica não estiver disponível, será não operacional. <br> - atualizações de python3-jwt para a versão mais recente disponível. Se o pacote não estiver presente, ela será não operacional. <br> - atualizações de apt-transport-https para a versão mais recente inferior a 1.2.194. Se essa versão não estiver presente, ela será não operacional. <br> - atualizações de libsystemd0 para a versão mais recente superior ou igual a 229-4ubuntu16. Se tal versão não existir, ele será não operacional.|
| RejectedPatches | Cadeia de caracteres separada por vírgula (padrão: "") | Essa é a lista de atualizações que não devem ser instaladas nos nós de cluster <br> Por exemplo: "bash, sudo" <br> A etapa anterior filtra bash, sudo para que não recebam atualizações. |


> [!TIP]
> Se você quiser que a Atualização do SO seja executada imediatamente, defina `UpdateFrequency` em relação ao tempo de implantação do aplicativo. Por exemplo, suponha que você tem um cluster de teste de cinco nós e planeja implantar o aplicativo em torno de 5:00 PM UTC. Se você considera que o upgrade ou implantação do aplicativo leva 30 minutos no máximo, defina a UpdateFrequency como "Diariamente, 17:30:00".

## <a name="deploy-the-app"></a>Implantar o aplicativo

1. Prepare o cluster concluindo todas as etapas necessárias.
2. Implante o aplicativo de orquestração de patch como qualquer outro aplicativo do Service Fabric. Você pode implantar o aplicativo usando o PowerShell ou a CLI do Azure Service Fabric. Siga as etapas em [Implantar e remover aplicativos usando o PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) ou [Implantar o aplicativo usando a CLI do Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/scripts/cli-deploy-application)
3. Para configurar o aplicativo no momento da implantação, passe o `ApplicationParameter` para o cmdlet `New-ServiceFabricApplication` ou os scripts fornecidos. Para sua conveniência, scripts de powershell (Deploy.ps1) e de bash (Deploy.sh) são fornecidos com o aplicativo. Para usar o script:

    - Conecte-se a um cluster do Service Fabric.
    - Execute o script de implantação. Se desejar, passe o parâmetro do aplicativo para o script. Por exemplo: .\Deploy.ps1 -ApplicationParameter @{ UpdateFrequency = "Daily, 11:00:00"} OR ./Deploy.sh "{\"UpdateFrequency\":\"Daily, 11:00:00\"}" 

> [!NOTE]
> Mantenha a pasta de scripts e de aplicativos PatchOrchestrationApplication no mesmo diretório.

## <a name="upgrade-the-app"></a>Upgrade do aplicativo

Para fazer upgrade de um aplicativo de orquestração de patch existente, execute as etapas em [Upgrade do aplicativo do Service Fabric usando o PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell) ou [Atualização de aplicativo do Service Fabric usando a CLI do Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade)

## <a name="remove-the-app"></a>Remover o aplicativo

Para remover o aplicativo, siga as etapas em [Implantar e remover aplicativos usando o PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) ou [Remover o aplicativo usando a CLI do Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-delete)

Para sua conveniência, scripts de powershell (Undeploy.ps1) e de bash (Undeploy.sh) são fornecidos com o aplicativo. Para usar o script:

  - Conecte-se a um cluster do Service Fabric.
  - Executar o script Undeploy.ps1 ou Undeploy.sh

> [!NOTE]
> Mantenha a pasta de scripts e de aplicativos PatchOrchestrationApplication no mesmo diretório.

## <a name="view-the-update-results"></a>Exibir os resultados da Atualização

O aplicativo de orquestração de patch expõe as APIs REST para exibir os resultados históricos do usuário. Veja o exemplo de resultado a seguir: ```testadm@bronze000001:~$ curl -X GET http://10.0.0.5:20002/PatchOrchestrationApplication/v1/GetResults```
```json
[ 
  { 
    "NodeName": "_bronze_0", 
    "UpdateOperationResults": [ 
      { 
        "OperationResult": "succeeded", 
        "NodeName": "_bronze_0", 
        "OperationTime": "2017-11-21T12:39:29.0435917Z", 
        "UpdateDetails": [ 
          { 
            "UpdateId": "linux-cloud-tools-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-headers-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-image-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-tools-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "python3-apport:amd64=2.20.1-0ubuntu2.13", 
            "ResultCode": "succeeded" 
          }, 
        ], 
        "OperationType": "installation", 
        "UpdateClassification": "securityupdates", 
        "UpdateFrequency": "Daily, 7:00:00", 
        "RebootRequired": true, 
        "ApprovedList": "", 
        "RejectedList": "" 
      } 
    ] 
  } 
] 
```

Campos do JSON são descritos da seguinte maneira:

Campo | Valores | Detalhes
-- | -- | --
OperationResult | 0 - Êxito<br> 1 - Êxito com erros<br> 2 - Falha<br> 3 - Anulado<br> 4 - Anulado com tempo limite | Indica o resultado da operação geral (normalmente envolvendo a instalação de uma ou mais atualizações).
ResultCode | O mesmo que OperationResult | Este campo indica o resultado da operação de instalação para uma atualização individual.
OperationType | 1 - Instalação<br> 0 - Pesquisar e baixar.| A instalação é o único OperationType que seria mostrado nos resultados por padrão.
UpdateClassification | O padrão é "securityupdates" | Tipo de atualizações que é instalado durante a operação de atualização
UpdateFrequency | O padrão é "Semanais, quarta-feira, 7:00:00" | Frequência de atualização configurada para essa atualização.
RebootRequired | true - a reinicialização foi necessária<br> false - a reinicialização não foi necessária | Indica que uma reinicialização foi necessária para concluir a instalação de atualizações.
ApprovedList | O padrão é "" | Lista de patches aprovados para esta atualização
RejectedList | O padrão é "" | Lista de patches rejeitados para esta atualização

Se nenhuma atualização estiver agendada ainda, o resultado JSON estará vazio.

Faça logon no cluster para consultar os resultados da Atualização. Em seguida, descubra o endereço de réplica para o primário do Serviço do Coordenador e pressione a URL do navegador: http://&lt;REPLICA-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetResults.

O ponto de extremidade REST para o Serviço do Coordenador tem uma porta dinâmica. Para verificar a URL exata, consulte o Service Fabric Explorer. Por exemplo, os resultados estão disponíveis em `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetResults`.

![Imagem do ponto de extremidade REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

## <a name="diagnosticshealth-events"></a>Eventos de diagnóstico/integridade

### <a name="diagnostic-logs"></a>Logs de diagnóstico

Os logs do aplicativo de orquestração de patch são coletados como parte dos logs do tempo de execução do Service Fabric.

Caso você queira capturar logs por meio da ferramenta de diagnóstico/pipeline de sua escolha. O aplicativo de orquestração de patch usa as IDs de provedores fixos a seguir para registrar eventos por meio do [eventsource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netstandard-2.0)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Relatórios de integridade

O aplicativo de orquestração de patch também publica relatórios de integridade em relação ao Serviço do Coordinator ou ao Serviço de Agente do Nó nos seguintes casos:

#### <a name="an-update-operation-failed"></a>Falha na operação de atualização

Se uma operação de atualização falhar em um nó, um relatório de integridade em relação ao Serviço de Agente do Nó será gerado. Detalhes do relatório de integridade contêm o nome do nó problemático.

Após a aplicação de pach ser concluída com sucesso no nó problemático, o relatório é apagado automaticamente.

#### <a name="the-node-agent-daemon-service-is-down"></a>O Serviço Daemon do Agente do Nó está inativo

Se o serviço Daemon do Agente do Nó estiver inativo em um nó, o relatório de integridade no nível de aviso será gerado no Serviço de Agente do Nó.

#### <a name="the-repair-manager-service-is-not-enabled"></a>O serviço do gerenciador de reparo não está habilitado

Um relatório de integridade no nível de aviso será gerado para o Serviço do Coordinator se o serviço do gerenciador de reparo não for encontrado no cluster.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

P. **Por que consigo ver meu cluster em um estado de erro quando o aplicativo de orquestração de patch está em execução?**

a. Durante o processo de instalação, o aplicativo de orquestração de patches desabilita ou reinicia os nós. Essa operação pode temporariamente resultar na redução da integridade do cluster.

Com base na política para o aplicativo, um nó pode ficar inativo durante uma operação de aplicação de patch *ou* todo um domínio de atualização pode ficar inativo ao mesmo tempo.

No fim da instalação, os nós serão reabilitados após a reinicialização.

No exemplo a seguir, o cluster veio de um estado de erro temporário porque dois nós estavam inativos e a política MaxPercentageUnhealthyNodes foi violada. O erro é temporário até que a operação de aplicação de patch esteja em andamento.

![Imagem do cluster não íntegro](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Caso o problema persista, consulte a seção de Solução de problemas.

P. **O aplicativo de orquestração de patch está em estado de aviso**

a. Verifique para ver se um relatório de integridade publicado em relação ao aplicativo é a causa raiz. Geralmente, o aviso contém detalhes do problema. Se o problema for transitório, o aplicativo deve esperar recuperar-se automaticamente desse estado.

P. **O que fazer se o cluster não está íntegro e preciso fazer uma atualização urgente do sistema operacional?**

a. O aplicativo de orquestração de patch não instala atualizações enquanto o cluster não está íntegro. Para desbloquear o fluxo de trabalho do aplicativo de orquestração de patch, coloque o cluster em um estado íntegro.

P. **Por que a execução da aplicação de patch nos clusters leva tanto tempo?**

a. O tempo que o aplicativo de orquestração de patch leva depende principalmente dos seguintes fatores:

- A política do Serviço do Coordinator. 
  - A política padrão, `NodeWise`, resulta na aplicação de patch em apenas um nó por vez. Especialmente se houver um cluster maior, é recomendável que você use a política `UpgradeDomainWise` para alcançar a aplicação de patch em cluster mais rápida.
- O número de atualizações disponíveis para baixar e instalar. 
- O tempo médio necessário para baixar e instalar uma atualização, que não deve exceder alguma horas.
- O desempenho da VM e largura da banda de rede.

P. **Como o aplicativo de orquestração de patches decide quais atualizações são atualizações de segurança.**

a. O aplicativo de orquestração de patches usa lógica específica de distribuição para determinar quais atualizações entre as atualizações disponíveis são as atualizações de segurança. Por exemplo:  No ubuntu, o aplicativo procura por atualizações de arquivos $RELEASE-segurança, $RELEASE-atualizações ($RELEASE = xenial ou a versão de lançamento de base padrão do linux). 

 
P. **Como fixo em uma versão específica do pacote?**

a. Use as configurações de ApprovedPatches para fixar seus pacotes em uma versão específica. 


P. **O que acontece com as atualizações automáticas habilitadas no Ubuntu?**

a. Assim que você instalar o aplicativo de orquestração de patches no seu cluster, atualizações autônomas em seu nó de cluster serão desabilitadas. Todo o fluxo de trabalho de atualização periódica será determinado pelo aplicativo de orquestração de patches.
Para que o ambiente de clusters seja consistente, é recomendável instalar as atualizações por meio apenas do aplicativo de orquestração de patches. 
 
P. **Após a atualização, o aplicativo de orquestração de patches realiza a limpeza de pacotes não utilizados?**

a. Sim, a limpeza ocorre como parte das etapas pós-instalação. 

P. **O aplicativo de Orquestração de Patch pode ser usado para o cluster de desenvolvimento (cluster de um nó) do patch?**

a. Não, o aplicativo de Orquestração de Patch não pode ser usado para cluster de um nó do patch. Essa limitação ocorre por design, uma vez que os [serviços de sistema do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) ou todos os aplicativos cliente enfrentam tempo de inatividade e, portanto, qualquer trabalho de reparo para aplicação de patch nunca seria aprovado pelo gerenciador de reparo.

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

O aplicativo de orquestração de patch tenta instalar uma atualização de acordo com a política de reagendamento. O serviço tenta recuperar o nó e ignorar a atualização de acordo com a política do aplicativo.

Nesse caso, será gerado um relatório de integridade no nível de aviso em relação ao Serviço de Agente do Nó. O resultado para a atualização também contém o possível motivo da falha.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>A integridade do cluster entrou em estado de erro enquanto a atualização é instalada

Uma atualização com falha pode reduzir a integridade de um aplicativo ou cluster em um nó ou domínio de atualização específico. O aplicativo de orquestração de patch interrompe qualquer operação subsequente de atualização até que o cluster esteja íntegro novamente.

Um administrador deve intervir e determinar por que o aplicativo ou cluster se tornou não íntegro devido a uma atualização instalada anteriormente.

## <a name="disclaimer"></a>Isenção de responsabilidade

O aplicativo de orquestração de patch coleta a telemetria para acompanhar o uso e o desempenho. A telemetria do aplicativo segue a definição da configuração de telemetria do tempo de execução do Service Fabric (ativada por padrão).

## <a name="release-notes"></a>Notas de versão

### <a name="version-010"></a>Versão 0.1.0
- Versão prévia particular

### <a name="version-200"></a>Versão 2.0.0
- Versão pública

### <a name="version-201"></a>Versão 2.0.1
- Recompilação do aplicativo usando o SDK mais recente do Service Fabric

### <a name="version-202"></a>Versão 2.0.2 
- Corrigido um problema com o aviso de integridade sendo deixado para trás durante a reinicialização.

### <a name="version-203-latest"></a>Versão 2.0.3 (mais recente)
- Correção do problema em que o uso da CPU do serviço do daemon de agente do nó atingido até 99% em VMs Standard_D1_v2.
- Corrigindo o problema que afetado a aplicação de patch cyle de vida em um nó em caso de nós com o nome que é um subconjunto do nome do nó atual. Para esses nós, é possível que aplicação de patch seja ignorada ou a reinicialização fique pendente.
- Corrigido um bug devido à qual o daemon de agente do nó fica travando quando configurações corrompidas são passadas para o serviço.