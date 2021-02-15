---
title: Extensão de VM de Diagnóstico de Desempenho do Azure para Windows | Microsoft Docs
description: Apresenta a Extensão de VM de Diagnóstico de Desempenho do Azure para Windows.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 9edba575b35613abb8bc3081964a37b838bb358b
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656588"
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Extensão de VM de Diagnóstico de Desempenho do Azure para Windows

Extensão de VM de Diagnóstico de Desempenho do Azure ajuda a coletar dados de diagnóstico de desempenho das máquinas virtuais do Windows. A extensão executa uma análise e fornece um relatório dos resultados e recomendações para identificar e resolver problemas de desempenho na máquina virtual. Essa extensão instala uma ferramenta de solução de problemas chamada [PerfInsights](./how-to-use-perfinsights.md).

> [!NOTE]
> Se você quer executar o diagnóstico na sua VM do portal do Azure para VMs não clássicas, é recomendável usar a nova experiência. Para obter mais informações, consulte [Diagnóstico de Desempenho para máquinas virtuais do Azure](performance-diagnostics.md) 

## <a name="prerequisites"></a>Pré-requisitos

Esta extensão pode ser instalada em
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows 10
* Windows 8.1
* Windows 8

## <a name="extension-schema"></a>Esquema de extensão
O JSON a seguir mostra o esquema para a Extensão da VM de Diagnóstico de Desempenho do Azure. Essa extensão requer o nome e a chave de uma conta de armazenamento para armazenar a saída de diagnóstico e o relatório. Esses valores são confidenciais. A chave de conta de armazenamento deve ser armazenada em uma configuração protegida. Os dados de configuração protegidos pela extensão da VM do Azure são criptografados, sendo descriptografados apenas na máquina virtual de destino. Observe que **storageAccountName** e **storageAccountKey** diferenciam maiúsculas de minúsculas. Outros parâmetros necessários são listados na seção a seguir.

```JSON
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "storageAccountName": "[parameters('storageAccountName')]",
          "performanceScenario": "[parameters('performanceScenario')]",
          "traceDurationInSeconds": "[parameter('traceDurationInSeconds')]",
          "perfCounterTrace": "[parameters('perfCounterTrace')]",
          "networkTrace": "[parameters('networkTrace')]",
          "xperfTrace": "[parameters('xperfTrace')]",
          "storPortTrace": "[parameters('storPortTrace')]",
          "srNumber": "[parameters('srNumber')]",
          "requestTimeUtc":  "[parameters('requestTimeUtc')]",
          "resourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"        
        }
      }
    }
```

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor/Exemplo | Descrição |
|--|--|--|
| apiVersion | 2015-06-15 | A versão da API. |
| editor | Microsoft.Azure.Performance.Diagnostics | O namespace do publicador para a extensão. |
| tipo | AzurePerformanceDiagnostics | O tipo da extensão de VM. |
| typeHandlerVersion | 1.0 | A versão do manipulador de extensão. |
| performanceScenario | básico | O cenário de desempenho para o qual capturar dados. Os valores válidos são: **basic**, **vmslow**, **azurefiles** e **custom**. |
| traceDurationInSeconds | 300 | A duração dos rastreamentos se qualquer uma das opções de rastreamento estiver selecionada. |
| perfCounterTrace | p | Opção para habilitar o rastreamento do contador de desempenho. Os valores válidos são **p** ou valor vazio. Se você não deseja capturar esse rastreamento, deixe o valor como vazio. |
| networkTrace | n | Opção para habilitar o Rastreamento de Rede. Os valores válidos são **n** ou um valor vazio. Se você não deseja capturar esse rastreamento, deixe o valor como vazio. |
| xperfTrace | x | Opção para habilitar o rastreamento do XPerf. Os valores válidos são **x** ou valor vazio. Se você não deseja capturar esse rastreamento, deixe o valor como vazio. |
| storPortTrace | s | Opção para habilitar o rastreamento do StorPort. Os valores válidos são **s** ou valor vazio. Se você não deseja capturar esse rastreamento, deixe o valor como vazio. |
| srNumber | 123452016365929 | O número do tíquete de suporte, se disponível. Deixe o valor como vazio se você não o tiver. |
| requestTimeUtc | 2017-09-28T22:08:53.736Z | Data e hora atual em Utc. Caso esteja usando o portal para instalar essa extensão, você não precisará fornecer esse valor. |
| resourceId | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName} | Identificador exclusivo de uma VM. |
| storageAccountName | mystorageaccount | O nome da conta de armazenamento para armazenar os logs de diagnóstico e os resultados. |
| storageAccountKey | lDuVvxuZB28NNP…hAiRF3voADxLBTcc== | A chave da conta de armazenamento. |

## <a name="install-the-extension"></a>Instalar a extensão

Siga estas instruções para instalar a extensão em máquinas virtuais do Windows:

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione a máquina virtual em que você deseja instalar essa extensão.

    ![Captura de tela do portal do Azure, com máquinas virtuais realçadas](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Selecione a folha **Extensões** e depois **Adicionar**.

    ![Captura de tela da folha Extensões, com a opção Adicionar realçada](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Selecione **Diagnóstico de Desempenho do Azure**, leia os termos e condições e selecione **Criar**.

    ![Captura de tela da tela Novo recurso, com a opção Diagnóstico de Desempenho do Azure realçada](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Forneça os valores de parâmetro para a instalação e selecione **OK** para instalar a extensão. Para obter mais informações sobre os cenários com suporte, consulte [Como usar o PerfInsights](how-to-use-perfinsights.md#supported-troubleshooting-scenarios). 

    ![Captura de tela da Caixa de diálogo Instalar extensão](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. Quando a instalação for concluída com êxito, você verá uma mensagem indicando que esse status.

    ![Captura de tela da mensagem de provisionamento bem-sucedido](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > Executa-se a extensão quando o provisionamento é realizado com êxito. Demora dois minutos ou menos para ser concluído no cenário básico. Para outros cenários, ela é executada pela duração especificada durante a instalação.

## <a name="remove-the-extension"></a>Remover a extensão

Para remover a extensão de uma máquina virtual, siga estas etapas:

1. Faça logon no [Portal do Azure](https://portal.azure.com), selecione a máquina virtual em que você deseja remover a extensão e, em seguida, selecione a folha **Extensões**. 
2. Clique no (**...**) da entrada de Extensão de Diagnóstico de Desempenho na lista e selecione **Desinstalar**.

    ![Captura de tela da folha Extensões, com a opção Desinstalar realçada](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > Também pode selecionar a entrada de extensão e selecionar a opção **Desinstalar**.

## <a name="template-deployment"></a>Implantação de modelo

As extensões de máquina virtual do Azure podem ser implantadas com modelos do Azure Resource Manager. O esquema JSON detalhado na seção anterior pode ser usado em um modelo do Azure Resource Manager. Isso executará a extensão da VM de Diagnóstico de Desempenho do Azure durante uma implantação de modelo do Azure Resource Manager. Aqui está um exemplo de modelo:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "defaultValue": "yourVMName"
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus"
    },
    "storageAccountName": {
      "type": "securestring",
      "defaultValue": "yourStorageAccount"
    },
    "storageAccountKey": {
      "type": "securestring",
      "defaultValue": "yourStorageAccountKey"
    },
    "performanceScenario": {
      "type": "string",
      "defaultValue": "basic"
    },
    "srNumber": {
      "type": "string",
      "defaultValue": ""
    },
  "traceDurationInSeconds": {
    "type": "int",
    "defaultValue": 300
  },
    "perfCounterTrace": {
      "type": "string",
      "defaultValue": "p"
    },
    "networkTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "xperfTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "storPortTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "requestTimeUtc": {
      "type": "string",
      "defaultValue": "10/2/2017 11:06:00 PM"
    }       
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "storageAccountName": "[parameters('storageAccountName')]",
          "performanceScenario": "[parameters('performanceScenario')]",
          "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
          "perfCounterTrace": "[parameters('perfCounterTrace')]",
          "networkTrace": "[parameters('networkTrace')]",
          "xperfTrace": "[parameters('xperfTrace')]",
          "storPortTrace": "[parameters('storPortTrace')]",
          "srNumber": "[parameters('srNumber')]",
          "requestTimeUtc":  "[parameters('requestTimeUtc')]",
          "resourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
  ]
}
```

## <a name="powershell-deployment"></a>Implantação do PowerShell

O comando `Set-AzVMExtension` pode ser usado para implantar a extensão da máquina virtual do Diagnóstico de Desempenho do Azure em uma máquina virtual existente.

PowerShell

```
$PublicSettings = @{ "storageAccountName"="mystorageaccount";"performanceScenario"="basic";"traceDurationInSeconds"=300;"perfCounterTrace"="p";"networkTrace"="";"xperfTrace"="";"storPortTrace"="";"srNumber"="";"requestTimeUtc"="2017-09-28T22:08:53.736Z";"resourceId"="VMResourceId" }
$ProtectedSettings = @{"storageAccountKey"="mystoragekey" }

Set-AzVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS
```

## <a name="information-on-the-data-captured"></a>Informações sobre os dados capturados
A ferramenta PerfInsights coleta vários logs, configurações, dados de diagnóstico, etc., dependendo do cenário selecionado. Para saber mais, confira a [Documentação de PerfInsights](./how-to-use-perfinsights.md).

## <a name="view-and-share-the-results"></a>Exibir e compartilhar os resultados

A saída da extensão pode ser encontrada em um arquivo zip carregado na conta de armazenamento especificada durante a instalação e é compartilhada por 30 dias usando [Assinaturas de Acesso Compartilhado (SAS)](../../storage/common/storage-sas-overview.md). Este arquivo zip contém logs de diagnóstico e de um relatório com os resultados e recomendações. Um link SAS para o arquivo zip de saída pode ser encontrado dentro de um arquivo de texto chamado *zipfilename* _saslink.txt na pasta **C:\Packages\Plugins\Microsoft.Azure.performance.Diagnostics.AzurePerformanceDiagnostics \\ \<version>**. Qualquer pessoa que tenha esse link pode baixar o arquivo zip.

Para ajudar o engenheiro de suporte que está trabalhando no seu tíquete de suporte, a Microsoft pode usar esse link das SAS para baixar os dados de diagnóstico.

Para exibir o relatório, extraia o arquivo .zip e abra o arquivo **PerfInsights Report.html**.

Você também poderá baixar o arquivo zip diretamente no portal, selecionando a extensão.

![Captura de tela do status detalhado do diagnóstico de desempenho](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> O link das SAS exibido no portal do pode não funcionar às vezes. Isso pode ser causado por uma URL malformada durante as operações de codificação e decodificação. A solução alternativa é obter o link diretamente do arquivo *_saslink.txt da VM.

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

- O status de implantação de extensão (na área de notificação) pode mostrar "Implantação em andamento", mesmo que a extensão tenha sido provisionada com êxito.

    Esse problema pode ser ignorado com segurança enquanto o status da extensão indicar que a extensão está provisionada com êxito.
- Você pode abordar alguns problemas durante a instalação usando os logs de extensão. A saída de execução da extensão é registrada nos arquivos localizados no seguinte diretório:

    `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\<version>`

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione **obter suporte**. Para obter informações sobre como usar o suporte do Azure, leia as [perguntas frequentes sobre suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).