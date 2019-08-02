---
title: Habilitar Azure Monitor (visualização) para um ambiente híbrido | Microsoft Docs
description: Este artigo descreve como habilitar Azure Monitor para VMs para um ambiente de nuvem híbrida que contém uma ou mais máquinas virtuais.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: e8241069a8671919b70dfbe44fe28c99a05358c5
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489741"
---
# <a name="enable-azure-monitor-for-vms-preview-for-a-hybrid-environment"></a>Habilitar Azure Monitor para VMs (versão prévia) para um ambiente híbrido

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este artigo explica como habilitar a Azure Monitor para VMs (versão prévia) para máquinas virtuais ou computadores físicos hospedados em seu datacenter ou em outro ambiente de nuvem. No final desse processo, você terá iniciado com êxito o monitoramento de suas máquinas virtuais em seu ambiente e aprenderá se elas estão enfrentando problemas de desempenho ou disponibilidade. 

Antes de começar, certifique-se de revisar os [pré-requisitos](vminsights-enable-overview.md) e verificar se sua assinatura e seus recursos atendem aos requisitos. Examine os requisitos e os métodos de implantação do [agente do Linux e do Windows do Log Analytics](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>O Dependency Agent de Mapa do Azure Monitor para VMs não transmite dados por conta própria e não exige alterações em firewalls ou portas. Os dados do mapa são sempre transmitidos pelo agente de Log Analytics para o serviço Azure Monitor, diretamente ou por meio do [gateway do Operations Management Suite](../../azure-monitor/platform/gateway.md) se as suas políticas de segurança de ti não permitirem que computadores na rede se conectem à Internet.

As etapas para concluir essa tarefa são resumidas da seguinte maneira:

1. Instale o agente Log Analytics para Windows ou Linux. Antes de instalar o agente, examine o artigo [visão geral do agente de log Analytics](../platform/log-analytics-agent.md) para entender os pré-requisitos do sistema e os métodos de implantação.

2. Baixar e instalar o Dependency Agent de Mapa do Azure Monitor para VMs para [Windows](https://aka.ms/dependencyagentwindows) ou [Linux](https://aka.ms/dependencyagentlinux).

3. Habilitar a coleta de contadores de desempenho.

4. Implantar o Azure Monitor para VMs.

## <a name="install-the-dependency-agent-on-windows"></a>Instalar o Dependency Agent no Windows

Instale o Dependency Agent manualmente em computadores Windows executando `InstallDependencyAgent-Windows.exe`. Se você executar esse arquivo executável sem opções, ele iniciará um assistente de instalação que você poderá seguir para instalar o agente de forma interativa.

>[!NOTE]
>São necessários privilégios de *Administrador* para instalar ou desinstalar o agente.

A tabela a seguir destaca os parâmetros compatíveis com a instalação do agente por meio da linha de comando.

| Parâmetro | Descrição |
|:--|:--|
| /? | Retorna uma lista das opções de linha de comando. |
| /S | Realiza uma instalação silenciosa sem a interação do usuário. |

Por exemplo, para executar o programa de instalação com `/?` o parâmetro, digite **installdependencyagent-Windows. exe/?** .

Os arquivos do Dependency Agent do Windows são instalados em *C:\Arquivos de Programas\Microsoft Dependency Agent* por padrão. Se o agente de dependência não for iniciado após a conclusão da instalação, verifique os logs para obter informações detalhadas sobre o erro. O diretório de log é *%Programfiles%\Microsoft Dependency Agent\logs*.

## <a name="install-the-dependency-agent-on-linux"></a>Instale o Agente de Dependência no Linux

O Dependency Agent é instalado em servidores Linux por meio de *InstallDependencyAgent-Linux64.bin*, um script de shell com um binário de extração automática. Você pode executar o arquivo usando `sh` ou adicionar permissões de execução ao próprio arquivo.

>[!NOTE]
> O acesso root é necessário para instalar ou configurar o agente.
>

| Parâmetro | Descrição |
|:--|:--|
| -help | Obtenha uma lista das opções de linha de comando. |
| -s | Realize uma instalação silenciosa sem solicitações ao usuário. |
| --check | Verifica as permissões e o sistema operacional, mas não instala o agente. |

Por exemplo, para executar o programa de instalação com `-help` o parâmetro, insira **installdependencyagent-Linux64. bin-Help**.

Instale o agente de dependência do Linux como raiz executando o `sh InstallDependencyAgent-Linux64.bin`comando.

Se o Agente de Dependência não for iniciado, verifique os logs para obter informações de erro detalhadas. Em agentes do Linux, o diretório de log é */var/opt/microsoft/dependency-agent/log*.

Os arquivos do Agente de Dependência são colocados nos diretórios a seguir:

| Arquivos | Location |
|:--|:--|
| Arquivos de núcleo | /opt/microsoft/dependency-agent |
| Arquivos de log | /var/opt/microsoft/dependency-agent/log |
| Arquivos de configuração | /etc/opt/microsoft/dependency-agent/config |
| Arquivos executáveis de serviço | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Arquivos de armazenamento binário | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Exemplos de script de instalação

Para implantar facilmente o agente de dependência em vários servidores ao mesmo tempo, o exemplo de script a seguir é fornecido para baixar e instalar o agente de dependência no Windows ou Linux.

### <a name="powershell-script-for-windows"></a>Script do PowerShell para Windows

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Script de Shell para Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Configuração de estado desejado

Para implantar o agente de dependência por meio da Desired State Configuration (DSC), você pode usar o módulo xPSDesiredStateConfiguration e um pouco de código semelhante ao seguinte:

```powershell
configuration ServiceMap {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage 
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```

## <a name="enable-performance-counters"></a>Habilitar contadores de desempenho

Se o espaço de trabalho do Log Analytics referenciado pela solução ainda não estiver configurado para coletar os contadores de desempenho necessários para a solução, você precisará habilitá-los. Você pode fazer isso de uma das duas maneiras:
* Manualmente, conforme descrito em [Fontes de dados de desempenho do Windows e do Linux no Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Baixando e executando um script do PowerShell que está disponível na [Galeria de Azure PowerShell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>Implantar o Azure Monitor para VMs

Esse método inclui um modelo JSON que especifica a configuração para habilitar os componentes da solução no espaço de trabalho do Log Analytics.

Se você não souber como implantar recursos usando um modelo, consulte:
* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Para usar o CLI do Azure, primeiro você precisa instalar e usar a CLI localmente. Você deve estar executando a CLI do Azure versão 2.0.27 ou posterior. Para identificar sua versão, execute `az --version`. Para instalar ou atualizar o CLI do Azure, consulte [instalar o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="create-and-execute-a-template"></a>Criar e executar um modelo

1. Copie e cole a seguinte sintaxe JSON em seu arquivo:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Salve esse arquivo como *installsolutionsforvminsights.json* em uma pasta local.

1. Capture os valores de *WorkspaceName*, *ResourceGroupName* e *WorkspaceLocation*. O valor para *WorkspaceName* é o nome do seu espaço de trabalho do Log Analytics. O valor de *WorkspaceLocation* é a região na qual o workspace foi definido.

1. Você está pronto para implantar esse modelo usando o seguinte comando do PowerShell:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    A alteração de configuração pode levar alguns minutos para ser concluída. Quando ele for concluído, uma mensagem será exibida semelhante à seguinte e inclui o resultado:

    ```powershell
    provisioningState       : Succeeded
    ```
   Depois de habilitar o monitoramento, poderão ser necessários cerca de 10 minutos antes da exibição do estado de integridade e das métricas para o computador híbrido.

## <a name="troubleshooting"></a>Solução de problemas

### <a name="vm-doesnt-appear-on-the-map"></a>A VM não aparece no mapa

Se a instalação do agente de dependência for bem-sucedida, mas você não vir seu computador no mapa, diagnostique o problema seguindo estas etapas.

1. O Agente de Dependência foi instalado com êxito? Confirme isso verificando se o serviço está instalado e em execução.

    **Windows**: procure o serviço denominado "Microsoft Dependency Agent". 

    **Linux**: procure o processo em execução "microsoft-dependency-agent".

2. Você está no [tipo de preço gratuito do log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)? O plano gratuito permite até cinco computadores exclusivos. Todos os computadores subsequentes não aparecerão no mapa, mesmo que os cinco anteriores não estejam mais enviando dados.

3. O computador está enviando dados de log e desempenho para Azure Monitor logs? Execute a seguinte consulta para seu computador: 

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Retornou um ou mais resultados? Os dados são recentes? Nesse caso, seu agente de Log Analytics está operando corretamente e se comunicando com o serviço. Caso contrário, verifique o agente em seu servidor: [Solução de problemas do agente do Log Analytics para Windows](../platform/agent-windows-troubleshoot.md) ou [Solução de problemas do agente do Log Analytics para Linux](../platform/agent-linux-troubleshoot.md).

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>O computador aparece no mapa, mas não tem nenhum processo

Se você vir o servidor no mapa, mas ele não tiver dados de processo ou de conexão, isso indicará que o agente de dependência está instalado e em execução, mas o driver do kernel não foi carregado. 

Verifique o arquivo C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log (Windows) ou o arquivo /var/opt/microsoft/dependency-agent/log/service.log (Linux). As últimas linhas do arquivo devem indicar por que o kernel não foi carregado. Por exemplo, se o kernel tiver sido atualizado por você, talvez o Linux não dê suporte a ele.


## <a name="next-steps"></a>Próximas etapas

Agora que o monitoramento está habilitado para suas máquinas virtuais, essas informações estão disponíveis para análise com Azure Monitor para VMs.
 
- Para saber como usar o recurso de integridade, consulte [exibir Azure monitor para VMs integridade](vminsights-health.md).
- Para exibir as dependências de aplicativos descobertas, confira [Exibir o Mapa do Azure Monitor para VMs](vminsights-maps.md).
- Para identificar afunilamentos e a utilização geral com o desempenho da VM, consulte [Exibir o desempenho da VM do Azure](vminsights-performance.md).
- Para exibir as dependências de aplicativos descobertas, confira [Exibir o Mapa do Azure Monitor para VMs](vminsights-maps.md).