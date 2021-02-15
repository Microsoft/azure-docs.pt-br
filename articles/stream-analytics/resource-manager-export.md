---
title: Exportar um modelo do Azure Resource Manager de um trabalho do Azure Stream Analytics
description: Este artigo descreve como exportar um modelo de Azure Resource Manager para seu trabalho de Azure Stream Analytics.
services: stream-analytics
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: aa17d83dcc14675db5ff6aa4597314baffbffdbb
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015412"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>Exportar um modelo do Azure Resource Manager de um trabalho do Azure Stream Analytics

Os [modelos de Azure Resource Manager](../azure-resource-manager/templates/overview.md) permitem implementar a infraestrutura como código. O modelo é um arquivo JavaScript Object Notation (JSON) que define a infraestrutura e a configuração de seus recursos. Você especifica os recursos a serem implantados e as propriedades desses recursos.

Você pode reimplantar um trabalho de Azure Stream Analytics exportando o modelo de Azure Resource Manager.

## <a name="open-a-job-in-vs-code"></a>Abrir um trabalho no VS Code

Antes de exportar um modelo, você deve primeiro abrir um trabalho de Stream Analytics existente no Visual Studio Code. 

Para exportar um trabalho para um projeto local, localize o trabalho que você deseja exportar no **Stream Analytics Explorer** no portal do Azure. Na página **consulta** , selecione **abrir no Visual Studio**. Em seguida, selecione **Visual Studio Code**.

![Abrir Stream Analytics trabalho no Visual Studio Code](./media/resource-manager-export/open-job-vs-code.png)

Para obter mais informações sobre como usar Visual Studio Code para gerenciar trabalhos de Stream Analytics, consulte o guia de [início rápido do Visual Studio Code](quick-create-visual-studio-code.md).

## <a name="compile-the-script"></a>Compilar o script 

A próxima etapa é compilar o script de trabalho para um modelo de Azure Resource Manager. Antes de compilar o script, verifique se seu trabalho tem pelo menos uma entrada e uma saída configurada. Se nenhuma entrada ou saída estiver configurada, você precisará configurar a entrada e a saída primeiro.

1. Em Visual Studio Code, navegue até o arquivo *Transformation. asaql* do seu trabalho.

   ![Arquivo Transformation. asaql no Visual Studio Code](./media/resource-manager-export/transformation-asaql.png)

1. Clique com o botão direito do mouse no arquivo *Transformation. asaql* e selecione **asa: Compilar script** no menu.

1. Observe que uma pasta de **implantação** aparece em seu espaço de trabalho Stream Analytics trabalho.

1. Explore o *JobTemplate.jsno* arquivo, que é o modelo de gerenciamento de recursos do Azure usado para implantar o.

## <a name="complete-the-parameters-file"></a>Concluir o arquivo de parâmetros

Em seguida, conclua o arquivo de parâmetros do modelo do Azure Resource Management.

1. Abra o *JobTemplate.parameters.jsno* arquivo localizado na pasta **implantar** do espaço de trabalho Stream Analytics trabalho no Visual Studio Code.

1. Observe que as chaves de entrada e saída são nulas. Substitua os valores nulos pelas teclas de acesso reais para os recursos de entrada e saída.

1. Salve o arquivo de parâmetros.

## <a name="deploy-using-templates"></a>Implantar usando modelos

Você está pronto para implantar seu trabalho de Azure Stream Analytics usando os modelos de Azure Resource Manager gerados na seção anterior.

Em uma janela do PowerShell, execute o comando a seguir. Certifique-se de reaplce o *ResourceGroupName*, o *modelofile* e o *TemplateParameterFile* com o nome do grupo de recursos real, e os caminhos de arquivo completos para o *JobTemplate.js* e *JobTemplate.parameters.jsem* arquivos na **pasta implantar** do espaço de trabalho de trabalho.

Se você não tiver Azure PowerShell configurado, siga as etapas em [instalar o módulo Azure PowerShell](/powershell/azure/install-Az-ps).

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>Próximas etapas

* [Testar Azure Stream Analytics trabalhos localmente com a entrada ao vivo usando Visual Studio Code](visual-studio-code-local-run-live-input.md)

* [Explorar Azure Stream Analytics trabalhos com Visual Studio Code (versão prévia)](visual-studio-code-explore-jobs.md)