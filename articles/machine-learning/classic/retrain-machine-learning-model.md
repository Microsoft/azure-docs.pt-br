---
title: 'ML Studio (clássico): readaptação de um serviço Web-Azure'
description: Saiba como atualizar um serviço Web para usar um modelo de aprendizado de máquina treinado recentemente no Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-csharp
ms.date: 02/14/2019
ms.openlocfilehash: a4fe9e54e5e03a8dbf2a727b22f784c36d6c65f9
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517579"
---
# <a name="retrain-and-deploy-a-machine-learning-model"></a>Treinar novamente e implantar um modelo de machine learning

**APLICA-SE A:**  ![Aplica-se a.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (clássico) ![Não se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


O novo treinamento é uma maneira de garantir que os modelos de machine learning fiquem precisos e baseados nos dados disponíveis mais relevantes. Este artigo mostra como treinar novamente e implantar um modelo de aprendizado de máquina como um novo serviço Web no Studio (clássico). Se você estiver buscando readaptar um serviço Web clássico, [exiba este artigo de instruções.](retrain-classic-web-service.md)

Este artigo pressupõe que você já tem um serviço Web preditivo implantado. Se você ainda não tiver um serviço Web de previsão, [saiba como implantar um serviço Web Studio (clássico) aqui.](deploy-a-machine-learning-web-service.md)

Você seguirá estas etapas para treinar novamente e implantar um novo serviço Web de aprendizado de máquina:

1. Implantar um **serviço Web do novo treinamento**
1. Treinar um novo modelo usando o **serviço Web do novo treinamento**
1. Atualizar o **teste preditivo** existente para usar o novo modelo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="deploy-the-retraining-web-service"></a>Implantar o serviço Web de readaptação

Um serviço Web de novo treinamento permite você treinar seu modelo novamente com um novo conjunto de parâmetros, como novos dados, bem como salvá-lo para uso posterior. Quando você conecta uma **Saída do Serviço Web** em um **Modelo de treinamento**, o teste de treinamento gera um novo modelo para uso.

Use as etapas a seguir para implantar um serviço Web de novo treinamento:

1. Conecte um módulo **Entrada do Serviço Web** à sua entrada de dados. Normalmente, você quer garantir que os dados de entrada sejam processados da mesma forma que os dados de treinamento originais.
1. Conecte um módulo **Saída do Serviço Web** à saída do seu **Modelo de Treinamento**.
1. Se você tiver um módulo **Modelo de Avaliação**, será possível conectar um módulo **Saída do Serviço Web** à saída dos resultados da avaliação
1. Execute seu experimento.

    Depois de executar o experimento, o fluxo de trabalho resultante deve ser semelhante à seguinte imagem:

    ![Fluxo de trabalho resultante](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE04.png)

    Agora, implante o teste de treinamento como um serviço Web de novo treinamento que gera um modelo treinado e os resultados de avaliação do modelo.

1. Na parte inferior da tela do experimento, clique em **Configurar serviço Web**
1. Selecione **Implantar Serviço Web [Novo]**. O portal dos Serviços Web do Azure Machine Learning abre a página **Implantar Serviço Web**.
1. Digite um nome para seu serviço Web e escolha um plano de pagamento.
1. Selecione **Implantar**.

## <a name="retrain-the-model"></a>Treinar o modelo novamente

Para este exemplo, estamos usando C# para criar o aplicativo de readaptação. Você também pode usar o código de exemplo em Python ou R para realizar essa tarefa.

Use as etapas a seguir para chamar as APIs de novo treinamento:

1. Criar um aplicativo de console em C# no Visual Studio: **novo**  >  **projeto**  >  **Visual C#**  >  **Windows Classic desktop**  >  **console app (.NET Framework)**.
1. Entre no portal de Serviços Web do Machine Learning.
1. Clique no serviço Web com o qual você está trabalhando.
1. Clique em **Consumo**.
1. Na parte inferior da página **Consumir**, na seção **Código de Exemplo**, clique em **Lote**.
1. Copie o código C# de exemplo da execução em lotes e cole-o no arquivo Program.cs. Verifique se o namespace permanece intacto.

Adicione o pacote NuGet Microsoft.AspNet.WebApi.Client como especificado nos comentários. Para adicionar a referência a Microsoft.WindowsAzure.Storage.dll, é necessário instalar a [biblioteca de clientes para os serviços de Armazenamento do Azure](https://www.nuget.org/packages/WindowsAzure.Storage).

A captura de tela a seguir mostra a página **Consumir** no portal de Serviços Web do Azure Machine Learning.

![Página Consumir](media/retrain-machine-learning/machine-learning-retrain-models-consume-page.png)

### <a name="update-the-apikey-declaration"></a>Atualizar a declaração da apikey

Localize a Declaração **apiKey** :

```csharp
const string apiKey = "abc123"; // Replace this with the API key for the web service
```

Na seção **informações básicas de consumo** da página **consumir** , localize a chave primária e copie-a para a Declaração **apiKey** .

### <a name="update-the-azure-storage-information"></a>Atualize as informações do Armazenamento do Azure

O código de exemplo de BES carrega um arquivo de uma unidade local (por exemplo, "C:\temp\CensusInput.csv") para o armazenamento do Azure, processa-o e grava os resultados de volta no armazenamento do Azure.

1. Entrar no portal do Azure
1. Na coluna de navegação esquerda, clique em **Mais serviços**, pesquise **Contas de armazenamento** e selecione.
1. Na lista de contas de armazenamento, selecione uma para armazenar o modelo recuperado.
1. Na coluna de navegação à esquerda, clique em **Chaves de acesso**.
1. Copie e salve a **Chave de Acesso Primária**.
1. Na coluna de navegação à esquerda, clique em **BLOBs**.
1. Selecione um contêiner existente ou crie um novo e salve o nome.

Localize as declarações *StorageAccountName*, *StorageAccountKey* e *StorageContainerName* e atualize os valores salvos no portal.

```csharp
const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name
```

Você também deve garantir que o arquivo de entrada esteja disponível no local especificado no código.

### <a name="specify-the-output-location"></a>Especificar o local de saída

Ao especificar o local de saída no Conteúdo de Solicitação, a extensão do arquivo especificada em *RelativeLocation* deve ser definida como `ilearner`.

```csharp
Outputs = new Dictionary<string, AzureBlobDataReference>() {
    {
        "output1",
        new AzureBlobDataReference()
        {
            ConnectionString = storageConnectionString,
            RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
        }
    },
```

Confira um exemplo de saída de novo treinamento:

![Saída da readaptação](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE06.png)

### <a name="evaluate-the-retraining-results"></a>Avaliar os resultados da readaptação

Quando você executa o aplicativo, a saída inclui URL e os tokens de assinaturas de acesso compartilhados necessários para acessar os resultados da avaliação.

É possível ver os resultados do desempenho do modelo treinado novamente combinando *BaseLocation*, *RelativeLocation* e *SasBlobToken* dos resultados de saída para *output2* e colando a URL completa na barra de endereço do navegador.

Examine os resultados para determinar se o modelo recém-treinado tem desempenho melhor do que o existente.

Salve *BaseLocation*, *RelativeLocation* e *SasBlobToken* dos resultados de saída.

## <a name="update-the-predictive-experiment"></a>Atualizar o teste preditivo

### <a name="sign-in-to-azure-resource-manager"></a>Entre no Azure Resource Manager

Primeiro, entre em sua conta do Azure de dentro do ambiente do PowerShell usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

### <a name="get-the-web-service-definition-object"></a>Obter o objeto de definição do serviço Web

Em seguida, obtenha o objeto de definição de serviço Web chamando o cmdlet [Get-AzMlWebService](/powershell/module/az.machinelearning/get-azmlwebservice) .

```azurepowershell
$wsd = Get-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'
```

Para determinar o nome do grupo de recursos de um serviço Web existente, execute o cmdlet Get-AzMlWebService sem parâmetros para exibir os serviços Web em sua assinatura. Localize o serviço Web e examine sua ID de serviço da Web. O nome do grupo de recursos é o quarto elemento na ID, logo após o elemento *resourceGroups* . No exemplo a seguir, o nome do grupo de recursos é Default-MachineLearning-SouthCentralUS.

```azurepowershell
Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
Name : RetrainSamplePre.2016.8.17.0.3.51.237
Location : South Central US
Type : Microsoft.MachineLearning/webServices
Tags : {}
```

Como alternativa, para determinar o nome do grupo de recursos de um serviço Web existente, faça logon no portal de Serviços Web do Azure Machine Learning. Selecione o serviço Web. O nome do grupo de recursos é o quinto elemento da URL do serviço Web, logo após o elemento *resourceGroups* . No exemplo a seguir, o nome do grupo de recursos é Default-MachineLearning-SouthCentralUS.

`https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237`

### <a name="export-the-web-service-definition-object-as-json"></a>Exportar o objeto de Definição do Serviço Web como JSON

Para modificar a definição do modelo treinado para usar o modelo treinado recentemente, você deve primeiro usar o cmdlet [Export-AzMlWebService](/powershell/module/az.machinelearning/export-azmlwebservice) para exportá-lo para um arquivo de formato JSON.

```azurepowershell
Export-AzMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"
```

### <a name="update-the-reference-to-the-ilearner-blob"></a>Atualizar a referência ao blob ilearner

Nos ativos, localize o [modelo treinado] e atualize o valor *uri* no nó *locationInfo* com o URI do blob ilearner. O URI é gerado combinando *BaseLocation* e *RelativeLocation* na saída da chamada de readaptação BES.

```json
"asset3": {
    "name": "Retrain Sample [trained model]",
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
```

### <a name="import-the-json-into-a-web-service-definition-object"></a>Importe o JSON em um objeto de Definição do Serviço Web

Use o cmdlet [Import-AzMlWebService](/powershell/module/az.machinelearning/import-azmlwebservice) para converter o arquivo JSON modificado de volta em um objeto de definição de serviço Web que você pode usar para atualizar o experimento do predicativo.

```azurepowershell
$wsd = Import-AzMlWebService -InputFile "C:\temp\mlservice_export.json"
```

### <a name="update-the-web-service"></a>Atualizar o serviço Web

Por fim, use o cmdlet [Update-AzMlWebService](/powershell/module/az.machinelearning/update-azmlwebservice) para atualizar o experimento de previsão.

```azurepowershell
Update-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como gerenciar serviços Web ou acompanhar várias execuções de testes, confira os seguintes artigos:

* [Explorar o portal dos Serviços Web](manage-new-webservice.md)
* [Gerenciar iterações de teste](manage-experiment-iterations.md)