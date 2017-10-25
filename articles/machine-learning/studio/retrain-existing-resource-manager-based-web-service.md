---
title: "Readaptar um serviço Web de previsão existente | Microsoft Docs"
description: "Aprenda como readaptar um modelo e atualizar o serviço Web para usar o modelo recentemente adaptado no Machine Learning do Azure."
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: 
ms.assetid: cc4c26a2-5672-4255-a767-cfd971e46775
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: v-donglo
ms.openlocfilehash: e7663f931594c0626a173562b846f3f9324d8ba3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="retrain-an-existing-predictive-web-service"></a>Readaptar um serviço Web de previsão existente
Este documento descreve o processo de readaptação do cenário a seguir:

* Você tem um experimento de treinamento e um experimento de previsão implantado como um serviço Web operacionalizado.
* Você tem novos dados os quais deseja usar em seu serviço Web de previsão para executar sua pontuação.

> [!NOTE] 
> Para implantar um novo serviço Web, você precisa ter permissões suficientes na assinatura na qual o serviço Web está sendo implantado. Para obter mais informações, consulte [Gerenciar um serviço Web usando o portal de Serviços Web do Azure Machine Learning](manage-new-webservice.md). 

Começando com o Serviço web e experimentos existentes, você precisa executar estas etapas:

1. Atualize o modelo.
   1. Modifique seu experimento de treinamento para permitir entradas e saídas do serviço Web.
   2. Implante o teste de treinamento como um serviço Web de readaptação.
   3. Use o BES (Serviço de Execução em Lote) do teste de treinamento para readaptar o modelo.
2. Use os cmdlets do PowerShell do Azure Machine Learning para atualizar o experimento de previsão.
   1. Entre sua conta do Azure Resource Manager.
   2. Obtenha a definição do serviço Web.
   3. Exporte a definição do Serviço Web como JSON.
   4. Atualize a referência para o blob ilearner no JSON.
   5. Importe o JSON em uma definição de serviço Web.
   6. Atualize o serviço Web com a nova definição de serviço Web.

## <a name="deploy-the-training-experiment"></a>Implantar o teste de treinamento
Para implantar o teste de treinamento como um serviço Web de readaptação, você deve adicionar as entradas e saídas do serviço Web ao modelo. Ao conectar um módulo *Saída do Serviço Web* ao módulo *[Modelo de Treinamento][train-model]* do teste, você habilita o teste de treinamento a fim de produzir um novo modelo de treinamento que pode ser usado em seu teste preditivo. Se você tiver um *Modelo de Avaliação*, também poderá anexar a saída do serviço Web para obter os resultados da avaliação como saída.

Para atualizar seu experimento de treinamento:

1. Conecte um módulo *Entrada do Serviço Web* à sua entrada de dados (por exemplo, um módulo *Limpar Dados Ausentes*). Normalmente, você quer garantir que os dados de entrada sejam processados da mesma forma que os dados de treinamento originais.
2. Conecte um módulo *Saída do Serviço Web* à saída de seu módulo *Modelo de Treinamento*.
3. Se você tiver um módulo *Modelo de Avaliação* e quiser exibir os resultados da avaliação, conecte um módulo *Saída do Serviço Web* à saída de seu módulo *Modelo de Avaliação*.

Execute seu experimento.

Em seguida, você deve implantar o Teste de Treinamento como um serviço Web que produz um modelo treinado e os resultados de avaliação do modelo.  

Na parte inferior da tela do experimento, clique em **Configurar o Serviço Web** e selecione **Implantar Serviço Web [Novo]**. O portal dos Serviços Web do Azure Machine Learning abre a página **Implantar Serviço Web**. Digite um nome para o serviço Web, escolha um plano de pagamento e clique em **Implantar**. Você pode usar o método de Execução em Lotes apenas para criar modelos treinados.

## <a name="retrain-the-model-with-new-data-by-using-bes"></a>Readapte o modelo com novos dados usando BES
Para este exemplo, estamos usando C# para criar o aplicativo de readaptação. Você também pode usar o código de exemplo em Python ou R para realizar essa tarefa.

Para chamar as APIs de Readaptação:

1. Crie um aplicativo de console C# no Visual Studio: **Novo** > **Projeto** > **Visual C#** > **Área de Trabalho Clássica do Windows** > **Aplicativo de Console (.NET Framework)**.
2. Entre no portal de Serviços Web do Machine Learning.
3. Clique no serviço Web com o qual você está trabalhando.
4. Clique em **Consumo**.
5. Na parte inferior da página **Consumir**, na seção **Código de Exemplo**, clique em **Lote**.
6. Copie o código C# de exemplo da execução em lotes e cole-o no arquivo Program.cs. Verifique se o namespace permanece intacto.

Adicione o pacote NuGet Microsoft.AspNet.WebApi.Client como especificado nos comentários. Para adicionar a referência a Microsoft.WindowsAzure.Storage.dll, você precisará primeiro instalar a [biblioteca de cliente para os serviços de Armazenamento do Azure](https://www.nuget.org/packages/WindowsAzure.Storage).

A captura de tela a seguir mostra a página **Consumir** no portal de Serviços Web do Azure Machine Learning.

![Página Consumir][1]

### <a name="update-the-apikey-declaration"></a>Atualizar a declaração da apikey
Localize a declaração da **apikey**:

    const string apiKey = "abc123"; // Replace this with the API key for the web service

Na seção **Informações básicas de consumo** da página **Consumir**, localize a chave primária e copie-a para a declaração da **apikey**.

### <a name="update-the-azure-storage-information"></a>Atualize as informações do Armazenamento do Azure
O código de exemplo de BES carrega um arquivo de uma unidade local (por exemplo "C:\temp\CensusIpnput.csv") para o armazenamento do Azure, processa e grava os resultados de volta para o armazenamento do Azure.  

Para atualizar as informações do Armazenamento do Azure, recupere o nome da conta de armazenamento, a chave e as informações de contêiner de sua conta de armazenamento do Portal Clássico do Azure e, em seguida, atualize o correspondente. Depois de executar o experimento, o fluxo de trabalho resultante deverá ser semelhante ao seguinte:

![Fluxo de trabalho resultante após a execução][4]valores ng no código.

1. Entre no portal clássico do Azure.
2. Na coluna de navegação à esquerda, clique em **Armazenamento**.
3. Na lista de contas de armazenamento, selecione uma para armazenar o modelo recuperado.
4. Na parte inferior da página, clique em **Gerenciar Chaves de Acesso**.
5. Copie e salve a **Chave de Acesso Primária** e feche a caixa de diálogo.
6. Na parte superior da página, clique em **Contêineres**.
7. Selecione um contêiner existente ou crie um novo e salve o nome.

Localize as declarações *StorageAccountName*, *StorageAccountKey* e *StorageContainerName* e atualize os valores salvos no portal clássico.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Você também deve garantir que o arquivo de entrada esteja disponível no local especificado no código.

### <a name="specify-the-output-location"></a>Especificar o local de saída
Ao especificar o local de saída no Conteúdo de Solicitação, a extensão do arquivo especificada em *RelativeLocation* deve ser definida como `ilearner`. Veja os exemplos a seguir:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Veja a seguir um exemplo de saída de readaptação: ![Saída de readaptação][6]

## <a name="evaluate-the-retraining-results"></a>Avaliar os resultados da readaptação
Quando você executa o aplicativo, a saída inclui URL e os tokens de assinaturas de acesso compartilhados necessários para acessar os resultados da avaliação.

Você pode ver os resultados do desempenho do modelo readaptado ao combinar *BaseLocation*, *RelativeLocation* e *SasBlobToken* dos resultados de saída para *output2* (como mostrado na imagem de readaptação anterior) e colando a URL completa na barra de endereço do navegador.  

Examine os resultados para determinar se o modelo treinado recentemente executa bem o suficiente para substituir o existente.

Copie *BaseLocation*, *RelativeLocation* e *SasBlobToken* dos resultados de saída.

## <a name="retrain-the-web-service"></a>Readaptar o serviço Web
Quando você readapta um novo serviço Web, também atualiza a definição do serviço Web de previsão para fazer referenciar ao novo modelo treinado. A definição do serviço Web é uma representação interna do modelo treinado do serviço Web e não pode ser modificada diretamente. Verifique se você está recuperando a definição do serviço Web para seu experimento de previsão, e não seu teste de treinamento.

## <a name="sign-in-to-azure-resource-manager"></a>Entre no Azure Resource Manager
Primeiro, você deve entrar em sua conta do Azure no ambiente do PowerShell usando o cmdlet [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx).

## <a name="get-the-web-service-definition-object"></a>Obter o objeto de definição do serviço Web
Em seguida, obtenha o objeto de definição do serviço Web chamando o cmdlet [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx).

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Para determinar o nome do grupo de recursos de um serviço Web existente, execute o cmdlet Get-AzureRmMlWebService sem parâmetros para exibir os serviços Web em sua assinatura. Localize o serviço Web e examine sua ID de serviço da Web. O nome do grupo de recursos é o quarto elemento na ID, logo após o elemento *resourceGroups* . No exemplo a seguir, o nome do grupo de recursos é Default-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Como alternativa, para determinar o nome do grupo de recursos de um serviço Web existente, faça logon no portal de Serviços Web do Azure Machine Learning. Selecione o serviço Web. O nome do grupo de recursos é o quinto elemento da URL do serviço Web, logo após o elemento *resourceGroups* . No exemplo a seguir, o nome do grupo de recursos é Default-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-object-as-json"></a>Exportar o objeto de Definição do Serviço Web como JSON
Para modificar a definição para o modelo treinado usar o modelo treinado recentemente, primeiro você deve usar o cmdlet [Export-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) para exportá-lo para um arquivo no formato JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob"></a>Atualizar a referência ao blob ilearner
Nos ativos, localize o [modelo treinado] e atualize o valor *uri* no nó *locationInfo* com o URI do blob ilearner. O URI é gerado combinando *BaseLocation* e *RelativeLocation* na saída da chamada de readaptação BES.

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

## <a name="import-the-json-into-a-web-service-definition-object"></a>Importe o JSON em um objeto de Definição do Serviço Web
Você deve usar o cmdlet [Import-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) para converter o arquivo JSON modificado novamente em um objeto de Definição de Serviço Web que você pode usar para atualizar o teste de previsão.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service"></a>Atualizar o serviço Web
Finalmente, use o cmdlet [Update-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) para atualizar o teste de previsão.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

[1]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-consume-page.png
[4]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE04.png
[6]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE06.png

<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
