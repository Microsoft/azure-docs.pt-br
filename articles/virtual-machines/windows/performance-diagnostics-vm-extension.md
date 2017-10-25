---
title: "Extensão de VM de Diagnóstico de Desempenho do Azure para Windows | Microsoft Docs"
description: "Apresenta a Extensão de VM de Diagnóstico de Desempenho do Azure para Windows."
services: virtual-machines-windows'
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/29/2017
ms.author: genli
ms.openlocfilehash: b521b4cae29578798247921331d98fc5077ad266
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Extensão de VM de Diagnóstico de Desempenho do Azure para Windows

## <a name="summary"></a>Resumo
A Extensão de VM de Diagnóstico de Desempenho do Azure ajuda a coletar dados de diagnóstico de desempenho de VMs do Windows, executa a análise e fornece um relatório de resultados e recomendações para identificar e resolver problemas de desempenho na máquina virtual. Essa extensão instala uma ferramenta de solução de problemas chamada [PerfInsights](http://aka.ms/perfinsights).

## <a name="prerequisites"></a>Pré-requisitos
### <a name="operating-systems"></a>Sistemas operacionais
Esta extensão pode ser instalada no Windows Server 2008 R2, 2012, 2012 R2, 2016, nos sistemas operacionais Windows 8.1 e Windows 10.

## <a name="extension-schema"></a>Esquema de Extensão
O JSON a seguir mostra o esquema para a Extensão de Diagnóstico de Desempenho do Azure. Essa extensão requer o nome e a chave de uma conta de armazenamento para armazenar a saída de diagnóstico e o relatório. Esses valores são confidenciais e devem ser armazenados dentro de configuração protegida. Os dados de configuração protegidos pela extensão da VM do Azure são criptografados, sendo descriptografados apenas na máquina virtual de destino. Observe que storageAccountName e storageAccountKey diferenciam maiúsculas de minúsculas. Outros parâmetros necessários são listados na seção a seguir.

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
            "performanceScenario": "[parameters('performanceScenario')]",
                  "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
                  "diagnosticsTrace": "[parameters('diagnosticsTrace')]",
                  "perfCounterTrace": "[parameters('perfCounterTrace')]",
                  "networkTrace": "[parameters('networkTrace')]",
                  "xperfTrace": "[parameters('xperfTrace')]",
                  "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
          "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]"        
            }
      }
    }
```

### <a name="property-values"></a>Valores de propriedade

|   **Nome**   |**Valor/Exemplo**|       **Descrição**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|Versão da API
|publicador|Microsoft.Azure.Performance.Diagnostics|Namespace do publicador para a extensão
|type|AzurePerformanceDiagnostics|Tipo da extensão de VM
|typeHandlerVersion|1.0|Versão do manipulador de extensão
|performanceScenario|básico|Cenário de desempenho para o qual capturar os dados. Os valores válidos são: **basic**, **vmslow**, **azurefiles** e **custom**.
|traceDurationInSeconds|300|Duração dos Rastreamentos se qualquer uma das opções de rastreamento estiver selecionada.
|DiagnosticsTrace|d|Opção para habilitar o rastreamento de diagnóstico. Os valores válidos são **d** ou valor vazio. Se você não deseja capturar esse rastreamento, deixe o valor como vazio.
|perfCounterTrace|p|Opção para habilitar o rastreamento do contador de desempenho. Os valores válidos são **p** ou valor vazio. Se você não deseja capturar esse rastreamento, deixe o valor como vazio.
|networkTrace|n|Opção para habilitar o rastreamento do Netmon. Os valores válidos são **n** ou valor vazio. Se você não deseja capturar esse rastreamento, deixe o valor como vazio.
|xperfTrace|x|Opção para habilitar o rastreamento do XPerf. Os valores válidos são **x** ou valor vazio. Se você não deseja capturar esse rastreamento, deixe o valor como vazio.
|storPortTrace|s|Opção para habilitar o rastreamento do StorPort. Os valores válidos são s ou valor vazio. Se você não deseja capturar esse rastreamento, deixe o valor como vazio.
|srNumber|123452016365929|O número do tíquete de suporte, se disponível. Deixe como vazio se você não o tem.
|requestTimeUtc|9/2/2017 23:06|Data e hora atual em Utc. Você não precisará fornecer esse valor se você estiver usando o portal para instalar esta extensão.
|storageAccountName|mystorageaccount|Nome da conta de armazenamento para armazenar os logs de diagnóstico e os resultados.
|storageAccountKey|lDuVvxuZB28NNP…hAiRF3voADxLBTcc==|A chave para a conta de armazenamento.

## <a name="install-the-extension"></a>Instalar a extensão

Siga estas etapas para instalar a extensão de VM em máquinas virtuais do Windows:

1. Faça logon no [portal do Azure](http://portal.azure.com).
2. Selecione a máquina virtual em que você deseja instalar essa extensão.

    ![Selecione a máquina virtual](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Selecione a folha **Extensões** e clique no botão **Adicionar**.

    ![Selecionar extensões](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Selecione a extensão de diagnóstico de desempenho do Azure, leia os termos e condições e clique no botão **Criar**.

    ![Criar uma Extensão de Diagnóstico de Desempenho do Azure para Windows](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Forneça os valores de parâmetro para a instalação e clique em **OK** para instalar a extensão. Você pode encontrar mais informações sobre os cenários de solução de problemas com suporte [aqui](how-to-use-perfInsights.md#supported-troubleshooting-scenarios). 

    ![Instalar a extensão](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. Depois que a instalação for bem-sucedida, você verá uma mensagem indicando que o provisionamento foi bem-sucedido.

    ![Mensagem de provisionamento bem-sucedido](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > A execução da extensão será iniciada depois que o provisionamento for bem-sucedido e levará alguns minutos ou menos para concluir a execução no cenário básico. Para outros cenários, ela será executada pela duração especificada durante a instalação.

## <a name="remove-the-extension"></a>Remover a extensão
Para remover a extensão de uma máquina virtual, siga estas etapas:

1. Faça logon no [Portal do Azure](http://portal.azure.com), selecione a máquina virtual em que você deseja remover a extensão e, em seguida, selecione a folha Extensões. 
2. Clique no (**...** ) da Entrada de Extensão de Diagnóstico de Desempenho na lista e selecione desinstalar.

    ![Desinstalar a extensão](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > Também pode ser interessante selecionar a entrada de extensão e selecionar a opção Desinstalar.

## <a name="template-deployment"></a>Implantação de Modelo
Extensões de VM do Azure podem ser implantadas com modelos do Azure Resource Manager. O esquema JSON detalhado na seção anterior pode ser usado em um modelo do Azure Resource Manager para executar a Extensão de Diagnóstico de Desempenho do Azure durante uma implantação de modelo do Azure Resource Manager. Aqui está um modelo de exemplo que pode ser usado com a implantação de modelo:

````
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
      "type": "securestring"
      "defaultValue": "yourStorageAccount"
    },
    "storageAccountKey": {
      "type": "securestring"
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
    "diagnosticsTrace": {
      "type": "string",
      "defaultValue": "d"
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
            "performanceScenario": "[parameters('performanceScenario')]",
                  "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
                  "diagnosticsTrace": "[parameters('diagnosticsTrace')]",
                  "perfCounterTrace": "[parameters('perfCounterTrace')]",
                  "networkTrace": "[parameters('networkTrace')]",
                  "xperfTrace": "[parameters('xperfTrace')]",
                  "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
          "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]"        
            }
      }
    }
  ]
}
````

## <a name="powershell-deployment"></a>Implantação do PowerShell
O comando `Set-AzureRmVMExtension` pode ser usado para implantar a extensão da máquina virtual do Diagnóstico de Desempenho do Azure em uma máquina virtual existente. Antes de executar o comando, as configurações públicas e privadas precisam ser armazenadas em uma tabela de hash do PowerShell.

PowerShell

````
$PublicSettings = @{ "performanceScenario":"basic","traceDurationInSeconds":300,"diagnosticsTrace":"d","perfCounterTrace":"p","networkTrace":"","xperfTrace":"","storPortTrace":"","srNumber":"","requestTimeUtc":"2017-09-28T22:08:53.736Z" }
$ProtectedSettings = @{"storageAccountName":"mystorageaccount","storageAccountKey":"mystoragekey"}

Set-AzureRmVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
````

## <a name="information-on-the-data-captured"></a>Informações sobre os dados capturados
A ferramenta PerfInsights coleta vários logs, configurações, dados de diagnóstico, etc., dependendo do cenário selecionado. Para obter mais informações sobre os dados coletados por cenário, visite [Documentação do PerfInsights](http://aka.ms/perfinsights).

## <a name="view-and-share-the-results"></a>Exibir e compartilhar os resultados

A saída da extensão é armazenada dentro de uma pasta chamada log_collection na unidade temporária (geralmente D:\log_collection) por padrão. Nesta pasta, você pode ver o arquivo zip que contém os logs de diagnóstico e de um relatório com as descobertas e recomendações.

O arquivo zip criado também é carregado na conta de armazenamento fornecida durante a instalação e é compartilhado por 30 dias usando [Assinaturas de Acesso Compartilhado (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md). Um arquivo de texto chamado *zipfilename*_saslink.txt também é criado na pasta log_collection. Esse arquivo contém o link SAS criado para baixar o arquivo zip. Qualquer pessoa que tenha esse link poderá baixar o arquivo zip.

A Microsoft pode usar esse link SAS para baixar os dados de diagnóstico para uma investigação adicional pelo engenheiro de suporte que está trabalhando em seu tíquete de suporte.

Para exibir o relatório, basta extrair o arquivo zip e abrir o arquivo **PerfInsights Report.html**.

Você também poderá baixar o arquivo zip diretamente do portal, selecionando a extensão.

![Exibir o status detalhado](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> O link SAS exibido no portal pode não funcionar às vezes, devido a malformação de URL (causada por caracteres especiais) durante a operação de codificação e decodificação. A solução alternativa é obter o link diretamente do arquivo *_saslink.txt da VM.

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte
### <a name="troubleshoot"></a>Solucionar problemas

- O status de implantação de extensão (na área de notificação) pode mostrar "Implantação em andamento", mesmo que a extensão tenha sido provisionada com êxito.

    Esse problema pode ser ignorado com segurança enquanto o status da extensão indicar que a extensão está provisionada com êxito.
- Alguns problemas durante a instalação podem ser solucionados usando os logs de extensão. A saída de execução da extensão é registrada nos arquivos localizados no seguinte diretório:

        C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics

### <a name="support"></a>Suporte

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).