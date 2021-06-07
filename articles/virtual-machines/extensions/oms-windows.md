---
title: Extensão da máquina virtual do Log Analytics para Windows
description: Implante o agente do Log Analytics na máquina virtual do Windows usando uma extensão da máquina virtual.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 06/26/2020
ms.openlocfilehash: 7757bd765bcb02782b6199f71c4a6e460b7b8143
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559011"
---
# <a name="log-analytics-virtual-machine-extension-for-windows"></a>Extensão da máquina virtual do Log Analytics para Windows

Os logs de Azure Monitor fornecem recursos de monitoramento em ativos de nuvem e locais. A extensão de máquina virtual do agente Log Analytics para Windows é publicada e suportada pela Microsoft. A extensão instala o agente do Log Analytics em máquinas virtuais do Azure e registra máquinas virtuais em um espaço de trabalho do Log Analytics existente. Este documento detalha as plataformas com opções de plataformas, configurações e implantação com suporte para a extensão da máquina virtual do Log Analytics para Windows.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operacional

Para obter detalhes sobre os sistemas operacionais Windows com suporte, consulte o artigo [visão geral do Azure monitor Agents](../../azure-monitor/agents/agents-overview.md#supported-operating-systems) .

### <a name="agent-and-vm-extension-version"></a>Versão do Agente e da Extensão de VM
A tabela a seguir fornece um mapeamento da versão da extensão de VM do Windows Log Analytics e do pacote de Log Analytics agente para cada versão. 

| Log Analytics versão do pacote do agente do Windows | Log Analytics versão da extensão de VM do Windows | Data de lançamento | Notas sobre a versão |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18053| 1.0.18053.0 | Outubro de 2020   | <ul><li>Novo solucionador de problemas do agente</li><li>Atualizações de como o agente lida com as alterações de certificado nos serviços do Azure</li></ul> |
| 10.20.18040 | 1.0.18040.2 | Agosto de 2020   | <ul><li>Resolve um problema no arco do Azure</li></ul> |
| 10.20.18038 | 1.0.18038 | Abril de 2020   | <ul><li>Habilita a conectividade sobre o link privado usando Azure Monitor escopos de link privado</li><li>Adiciona limitação de ingestão para evitar um influxo repentino e acidental na ingestão para um espaço de trabalho</li><li>Adiciona suporte para nuvens e regiões adicionais do Azure governamental</li><li>Resolve um bug em que HealthService.exe falhou</li></ul> |
| 10.20.18029 | 1.0.18029 | Março de 2020   | <ul><li>Adiciona suporte à assinatura de código SHA-2</li><li>Melhora a instalação e o gerenciamento da extensão de VM</li><li>Resolve um bug no Azure ARC para integração de servidores</li><li>Adiciona uma ferramenta de solução de problemas interna para atendimento ao cliente</li><li>Adiciona suporte para regiões adicionais do Azure governamental</li> |
| 10.20.18018 | 1.0.18018 | Outubro de 2019 | <ul><li> Correções de bugs e melhorias de estabilização secundárias </li></ul> |
| 10.20.18011 | 1.0.18011 | Julho de 2019 | <ul><li> Correções de bugs e melhorias de estabilização secundárias </li><li> Aumento de MaxExpressionDepth para 10000 </li></ul> |
| 10.20.18001 | 1.0.18001 | Junho de 2019 | <ul><li> Correções de bugs e melhorias de estabilização secundárias </li><li> Capacidade adicional de desabilitar as credenciais padrão ao fazer a conexão proxy (suporte para WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH) </li></ul>|
| 10.19.13515 | 1.0.13515 | Março de 2019 | <ul><li>Correções secundárias de estabilização </li></ul> |
| 10.19.10006 | N/D | Dec 2018 | <ul><li> Correções secundárias de estabilização </li></ul> | 
| 8.0.11136 | N/D | Setembro de 2018 |  <ul><li> Suporte adicionado para detectar a alteração da ID de recurso na movimentação da VM </li><li> Adicionado suporte para relatar a ID de recurso ao usar a instalação sem extensão </li></ul>| 
| 8.0.11103 | N/D |  Abril de 2018 | |
| 8.0.11081 | 1.0.11081 | 2017 de novembro | | 
| 8.0.11072 | 1.0.11072 | 2017 de setembro | |
| 8.0.11049 | 1.0.11049 | Fevereiro de 2017 | |


### <a name="azure-security-center"></a>Central de Segurança do Azure

A central de segurança do Azure provisiona automaticamente o agente de Log Analytics e o conecta com o espaço de trabalho de Log Analytics padrão da assinatura do Azure. Se você estiver usando a Central de Segurança do Azure, não execute as etapas neste documento. Isso substituiria o workspace configurado e interromperia a conexão com a Central de Segurança do Azure.

### <a name="internet-connectivity"></a>Conectividade com a Internet
A extensão do agente Log Analytics para Windows requer que a máquina virtual de destino esteja conectada à Internet. 

## <a name="extension-schema"></a>Esquema de extensão

O seguinte JSON mostra o esquema para a extensão do agente do Log Analytics. A extensão requer a ID do espaço de trabalho e a chave do espaço de trabalho do destino Log Analytics espaço de trabalho. Esses podem ser encontrado nas configurações para o workspace no portal do Azure. Como a chave do workspace deve ser tratada como um dado confidencial, ela é armazenada em uma configuração protegida. Os dados de configuração protegidos pela extensão da VM do Azure são criptografados, sendo descriptografados apenas na máquina virtual de destino. Observe que **workspaceId** e **workspaceKey** diferenciam maiúsculas de minúsculas.

```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```
### <a name="property-values"></a>Valores de propriedade

| Nome | Valor/Exemplo |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publicador | Microsoft.EnterpriseCloud.Monitoring |
| type | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId (por exemplo)* | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (por exemplo) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\* A workspaceId é chamada de consumerId na API do Log Analytics.

> [!NOTE]
> Para obter mais propriedades, consulte Azure [Connect Windows computers to Azure monitor](../../azure-monitor/agents/agent-windows.md).

## <a name="template-deployment"></a>Implantação de modelo

Extensões de VM do Azure podem ser implantadas com modelos do Azure Resource Manager. O esquema JSON detalhado na seção anterior pode ser usado em um modelo do Azure Resource Manager para executar a extensão do agente do Log Analytics durante uma implantação de modelo do Azure Resource Manager. Um modelo de exemplo que inclui a extensão de VM do agente Log Analytics pode ser encontrado na [Galeria de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

>[!NOTE]
>O modelo não dá suporte à especificação de mais de uma ID de espaço de trabalho e da chave do espaço de trabalho quando você deseja configurar o agente para relatar para vários espaços de trabalho. Para configurar o agente para relatar para vários espaços de trabalho, consulte [adicionando ou removendo um espaço de trabalho](../../azure-monitor/agents/agent-manage.md#adding-or-removing-a-workspace).  

O JSON para uma extensão da máquina virtual pode ser aninhado dentro do recurso de máquina virtual ou localizado no nível de raiz ou superior de um modelo JSON do Resource Manager. O posicionamento do JSON afeta o valor do tipo e nome do recurso. Para obter mais informações, consulte [Definir o nome e o tipo de recursos filho](../../azure-resource-manager/templates/child-resource-name-type.md). 

O exemplo a seguir pressupõe que a extensão Log Analytics esteja aninhada dentro do recurso de máquina virtual. Ao aninhar o recurso de extensão, o JSON é colocado no objeto `"resources": []` da máquina virtual.


```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

Ao inserir o JSON da extensão na raiz do modelo, o nome do recurso inclui uma referência na máquina virtual pai e o tipo reflete a configuração aninhada. 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

## <a name="powershell-deployment"></a>Implantação do PowerShell

O `Set-AzVMExtension` comando pode ser usado para implantar a extensão de máquina virtual do agente do Log Analytics em uma máquina virtual existente. Antes de executar o comando, as configurações públicas e privadas precisam ser armazenadas em uma tabela de hash do PowerShell. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzVMExtension -ExtensionName "MicrosoftMonitoringAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solucionar problemas

Os dados sobre o estado das implantações de extensão podem ser recuperados no Portal do Azure usando o módulo do Azure PowerShell. Para ver o estado da implantação das extensões de uma determinada VM, execute o comando a seguir usando o módulo do Azure PowerShell.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

A saída de execução da extensão é registrada nos arquivos localizados no seguinte diretório:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Suporte

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).
