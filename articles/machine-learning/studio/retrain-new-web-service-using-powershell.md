---
title: "Treinar novamente um novo serviço Web do Azure Machine Learning com o PowerShell | Microsoft Docs"
description: "Saiba como readaptar um modelo de forma programática e atualizar o serviço Web para usar o modelo treinado recentemente no Machine Learning do Azure usando os cmdlets do PowerShell de Gerenciamento do Machine Learning."
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondlaghaeian
editor: 
ms.assetid: 3953a398-6174-4d2d-8bbd-e55cf1639415
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.author: v-donglo
ms.openlocfilehash: 77bc78e7ed27f1566e5e5f6a3539c93c9aa73e2d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="retrain-a-new-resource-manager-based-web-service-using-the-machine-learning-management-powershell-cmdlets"></a>Readaptar um novo serviço Web baseado no Resource Manager usando os cmdlets do PowerShell de Gerenciamento do Machine Learning
Quando você readapta um novo serviço Web, também atualiza a definição do serviço Web de previsão para fazer referenciar ao novo modelo treinado.  

## <a name="prerequisites"></a>Pré-requisitos
Você deve ter configurado um teste de treinamento e um experimento de previsão, como mostrado em [Readaptar os modelos do Machine Learning de forma programática](retrain-models-programmatically.md). 

> [!IMPORTANT]
> O experimento de previsão deve ser implantado como um serviço Web do Machine Learning do Azure Resource Manager (novo). Para implantar um novo serviço Web, você precisa ter permissões suficientes na assinatura na qual o serviço Web está sendo implantado. Para saber mais, confira [Gerenciar um serviço Web usando o portal de Serviços Web do Azure Machine Learning](manage-new-webservice.md). 

Para obter mais informações sobre como implantar os serviços Web, veja [Implantar um serviço Web do Azure Machine Learning](publish-a-machine-learning-web-service.md).

Esse processo exige que você tenha instalado os Cmdlets do Machine Learning do Azure. Para obter informações sobre como instalar os cmdlets do Machine Learning, consulte a referência [Cmdlets do Machine Learning do Azure](https://msdn.microsoft.com/library/azure/mt767952.aspx) no MSDN.

Copiar as informações a seguir da saída da readaptação:

* BaseLocation
* RelativeLocation

As etapas são:

1. Entre sua conta do Azure Resource Manager.
2. Obter a definição do serviço Web
3. Exportar a Definição do Serviço Web como JSON
4. Atualize a referência para o blob ilearner no JSON.
5. Importar o JSON para uma Definição do Serviço Web
6. Atualizar o serviço Web com a nova Definição do Serviço Web

## <a name="sign-in-to-your-azure-resource-manager-account"></a>Entrar em sua conta do Azure Resource Manager
Primeiro, você deve entrar em sua conta do Azure no ambiente do PowerShell usando o cmdlet [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) .

## <a name="get-the-web-service-definition"></a>Obter a definição do serviço Web
Em seguida, obtenha o Serviço Web chamando o cmdlet [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) . A definição do serviço Web é uma representação interna do modelo treinado do serviço Web e não pode ser modificada diretamente. Verifique se você está recuperando a definição do serviço Web para seu experimento de previsão, e não seu teste de treinamento.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Para determinar o nome do grupo de recursos de um serviço Web existente, execute o cmdlet Get-AzureRmMlWebService sem parâmetros para exibir os serviços Web em sua assinatura. Localize o serviço Web e examine sua ID de serviço da Web. O nome do grupo de recursos é o quarto elemento na ID, logo após o elemento *resourceGroups* . No exemplo a seguir, o nome do grupo de recursos é Default-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Como alternativa, para determinar o nome do grupo de recursos de um serviço Web existente, faça logon no portal de serviços Web do Microsoft Azure Machine Learning. Selecione o serviço Web. O nome do grupo de recursos é o quinto elemento da URL do serviço Web, logo após o elemento *resourceGroups* . No exemplo a seguir, o nome do grupo de recursos é Default-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-as-json"></a>Exportar a Definição do Serviço Web como JSON
Para modificar a definição para o modelo treinado usar o Modelo Treinado recentemente, primeiro você deve usar o cmdlet [Export-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) para exportá-lo para um arquivo no formato JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob-in-the-json"></a>Atualize a referência para o blob ilearner no JSON.
Nos ativos, localize o [modelo treinado] e atualize o valor *uri* no nó *locationInfo* com o URI do blob ilearner. O URI é gerado combinando *BaseLocation* e *RelativeLocation* na saída da chamada de readaptação BES. Isso atualiza o caminho para referenciar o novo modelo treinado.

     "asset3": {
        "name": "Retrain Samp.le [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

## <a name="import-the-json-into-a-web-service-definition"></a>Importar o JSON para uma Definição do Serviço Web
É necessário usar o cmdlet [Import-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) para converter o arquivo JSON modificado novamente para uma Definição do Serviço Web que você pode usar para atualizá-lo.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service-with-new-web-service-definition"></a>Atualizar o serviço Web com a nova Definição do Serviço Web
Finalmente, use o cmdlet [Update-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) para atualizar a Definição do Serviço Web.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'  -ServiceUpdates $wsd

## <a name="summary"></a>Resumo
Usando os cmdlets de gerenciamento do PowerShell de Machine Learning, você pode atualizar o modelo treinado de um Serviço Web de previsão habilitando cenários, como:

* Readaptação de modelo periódico com novos dados.
* A distribuição de um modelo para os clientes com o objetivo de permitir que eles recuperem o modelo usando seus próprios dados.

