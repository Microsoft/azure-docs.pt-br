---
title: Criar vários pontos de extremidade para um modelo
titleSuffix: Azure Machine Learning Studio
description: Use o PowerShell para criar vários modelos do Machine Learning e pontos de extremidade de serviço Web com o mesmo algoritmo, mas com conjuntos de dados de treinamento diferentes.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 04/04/2017
ms.openlocfilehash: a191a7adc2c43337b663fc44a8ef40df9d8ffef4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773651"
---
# <a name="use-powershell-to-create-studio-models-and-web-service-endpoints-from-one-experiment"></a>Usar o PowerShell para criar modelos do Studio e pontos de extremidade de serviço Web por meio de um teste

Aqui está um problema comum do aprendizado de máquina: Você deseja criar vários modelos com o mesmo fluxo de trabalho de treinamento e usar o mesmo algoritmo. Mas quer que eles tenham conjuntos de dados de treinamento diferentes como entrada. Este artigo mostra como fazer isso em escala no Azure Machine Learning Studio usando apenas um único teste.

Por exemplo, digamos que você tenha um negócio de franquia mundial de aluguel de bicicletas. Você deseja criar um modelo de regressão para prever a demanda de aluguel com base em dados históricos. Você tem 1.000 locais para locação no mundo todo e coletou um conjunto de dados para cada local. Eles incluem recursos importantes, como data, hora, clima e tráfego, que são específicos a cada local.

Você poderia treinar seu modelo uma vez usando uma versão mesclada de todos os conjuntos de dados em todos os locais. Porém, cada um de seus locais tem um ambiente exclusivo. Portanto, a melhor abordagem seria treinar seu modelo de regressão separadamente, usando o conjunto de dados de cada local. Dessa forma, cada modelo treinado poderia levar em conta os diferentes tamanhos de armazenamento, volume, geografia, população, ambiente de tráfego para bicicletas e mais.

Essa poderia ser a melhor abordagem, mas não convém criar 1.000 testes de treinamento no Azure Machine Learning Studio com cada um deles representando um local exclusivo. Além de ser uma tarefa avassaladora, ela também parece ineficiente, já que cada experimento teria todos os mesmos componentes, exceto o conjunto de dados de treinamento.

A boa notícia é que você pode fazer isso usando a [API de readaptação do Azure Machine Learning Studio](/azure/machine-learning/studio/retrain-machine-learning-model) e automatizar a tarefa com o [PowerShell do Azure Machine Learning Studio](powershell-module.md).

> [!NOTE]
> Para fazer com que sua amostra seja executada mais rapidamente, reduza o número de locais de 1.000 para 10. No entanto, os mesmos princípios e procedimentos se aplicam aos 1.000 locais. No entanto, se você quiser treinar com 1.000 conjuntos de dados, convém executar seguintes scripts do PowerShell em paralelo. A forma como isso será feito está além do escopo deste artigo, mas é possível encontrar exemplos de multi-thread do PowerShell na Internet.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Configurar o teste de treinamento
Use o exemplo [teste de treinamento](https://gallery.azure.ai/Experiment/Bike-Rental-Training-Experiment-1) na [Cortana Intelligence Gallery](https://gallery.azure.ai). Abra esse teste em seu workspace do [Azure Machine Learning Studio](https://studio.azureml.net).

> [!NOTE]
> Para acompanhar este exemplo, talvez você queira usar um workspace padrão em vez de um workspace gratuito. Você cria um ponto de extremidade para cada cliente, para um total de 10 pontos de extremidade, e isso exige um workspace padrão, pois um workspace gratuito é limitado a três pontos de extremidade. Se você tiver apenas um workspace gratuito, basta modificar os scripts para permitir apenas x locais.
> 
> 

O teste usa um módulo **Importar Dados** para importar o conjunto de dados de treinamento *customer001.csv* de uma conta de armazenamento do Azure. Vamos supor que você coletou conjuntos de dados de treinamento de todos os locais de aluguel de bicicletas, e os armazenou no mesmo local que o armazenamento de blobs com nomes de arquivo que vão de *rentalloc001.csv* a *rentalloc10.csv*.

![Módulo de leitor importa dados de um blob do Azure](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Observe que um módulo **Saída do Serviço Web** foi adicionado ao módulo **Treinar Modelo**.
Quando esse teste for implantado como um serviço Web, o ponto de extremidade associado a essa saída retornará o modelo treinado no formato de um arquivo .ilearner.

Observe também que você configurou um parâmetro de serviço Web que define a URL usada pelo módulo **Importar Dados**. Isso permite que você use o parâmetro para especificar conjuntos de dados de treinamento individuais para treinar o modelo para cada local.
Há outras maneiras de fazer isso. Você pode usar uma consulta SQL com um parâmetro de serviço Web para obter dados de um Banco de Dados SQL do Azure. Ou você pode usar um módulo de **Entrada de Serviço Web** para transmitir um conjunto de dados para o serviço Web.

![Saídas de um módulo de modelo treinado para o módulo de saída do serviço Web](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Agora, vamos executar esse teste de treinamento usando o valor padrão *rental001.csv* como o conjunto de dados de treinamento. Se você exibir a saída do módulo **Avaliar** (clique na saída e selecione **Visualizar**), verá que obteve um desempenho razoável de *AUC* = 0,91. Neste ponto, você está pronto para implantar um serviço Web com base nesse teste de treinamento.

## <a name="deploy-the-training-and-scoring-web-services"></a>Implantar os serviços Web de treinamento e pontuação
Para implantar o serviço Web de treinamento, clique no botão **Configurar Serviço Web** abaixo da tela do teste e selecione **Implantar Serviço Web**. Chame esse serviço Web de “Treinamento do Aluguel de Bicicletas”.

Agora, você precisa implantar o serviço Web de pontuação.
Para fazer isso, clique em **Configurar Serviço Web** abaixo das telas e selecione **Serviço Web Preditivo**. Isso criará um teste de pontuação.
Será necessário fazer alguns pequenos ajustes para que isso funcione como um serviço Web. Remova a coluna do rótulo "cnt" dos dados de entrada e limite a saída apenas para a ID da instância e para o valor previsto correspondente.

Para poupar você desse trabalho, abra o [teste preditivo](https://gallery.azure.ai/Experiment/Bike-Rental-Predicative-Experiment-1) que já foi preparado na Galeria.

Para implantar o serviço Web, execute o teste preditivo e clique no botão **Implantar Serviço Web** abaixo da tela. Nomeie o serviço Web de pontuação “Pontuação do Aluguel de Bicicletas”.

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>Criar 10 pontos de extremidade de serviço Web idênticos com o PowerShell
Este serviço Web é fornecido com um ponto de extremidade padrão. Mas você não tem interesses no ponto de extremidade padrão, pois ele não pode ser atualizado. O que você precisa fazer é criar 10 pontos de extremidade adicionais, um para cada local. Faça isso com o PowerShell.

Primeiro, configure o ambiente do PowerShell:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and is properly set to point to the valid Workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

Em seguida, executamos o seguinte comando do PowerShell:

    # Create 10 endpoints on the scoring web service.
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpointName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

Agora você já criou 10 pontos de extremidade e todos eles contêm o mesmo modelo treinado em *customer001.csv*. Consulte-os no Portal do Azure.

![Exibir a lista de modelos treinados no portal](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Atualizar os pontos de extremidade para usar conjuntos de dados de treinamento separados usando o PowerShell
A próxima etapa é atualizar os pontos de extremidade com modelos treinados exclusivamente nos dados individuais de cada cliente. Mas, primeiro, você precisa produzir esses modelos com base no serviço Web **Treinamento do Aluguel de Bicicletas**. Vamos voltar ao serviço Web **Treinamento do Aluguel de Bicicletas** . Você precisa chamar seu ponto de extremidade BES 10 vezes com 10 conjuntos de dados de treinamento diferentes para produzir 10 modelos diferentes. Use o cmdlet **InovkeAmlWebServiceBESEndpoint** do PowerShell para fazer isso.

Você também precisará fornecer credenciais para sua conta de armazenamento de blobs em `$configContent`. Ou seja, nos campos `AccountName`, `AccountKey` e `RelativeLocation`. O `AccountName` pode ser um de seus nomes de conta, como mostrado no **Portal do Azure** (guia *Armazenamento*). Quando você clica em uma conta de armazenamento, seu `AccountKey` pode ser encontrado pressionando o botão **Gerenciar Chaves de Acesso** na parte inferior e copiando a *Chave de Acesso Primária*. O `RelativeLocation` é o caminho relativo ao seu armazenamento em que um novo modelo será armazenado. Por exemplo, o caminho `hai/retrain/bike_rental/` no script abaixo aponta para um contêiner chamado `hai`, e `/retrain/bike_rental/` são subpastas. Atualmente, não é possível criar subpastas por meio da interface do usuário do portal, mas há [vários Gerenciadores de Armazenamento do Azure](../../storage/common/storage-explorers.md) que permitem que você faça isso. É recomendável que você crie um novo contêiner no armazenamento para armazenar os novos modelos treinados (arquivos .iLearner) da seguinte maneira: na página do armazenamento, clique no botão **Adicionar** na parte inferior e nomeie-o como `retrain`. Resumindo, as alterações necessárias para o script a seguir referem-se a `AccountName`, `AccountKey` e `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`).

    # Invoke the retraining API 10 times
    # This is the default (and the only) endpoint on the training web service
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

> [!NOTE]
> O ponto de extremidade BES é o único modo com suporte para esta operação. O RRS não pode ser usado para a produção de modelos treinados.
> 
> 

Como você pode ver acima, em vez de criar 10 arquivos json de configuração de trabalho do BES diferentes, crie dinamicamente a cadeia de configuração. Em seguida, alimente-a no parâmetro *jobConfigString* do cmdlet **InvokeAmlWebServceBESEndpoint**. Não é necessário manter uma cópia em disco.

Se tudo correr bem, após alguns instantes, você verá 10 arquivos .iLearner, de *model001.ilearner* a *model010.ilearner*, em sua conta de armazenamento do Azure. Agora você está pronto para atualizar os 10 pontos de extremidade de serviço Web de pontuação com esses modelos usando o cmdlet **Patch-AmlWebServiceEndpoint** do PowerShell. Lembre-se de nodo que você pode aplicar patch apenas nos pontos de extremidade não padrão que você criou de forma programática anteriormente.

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '<my_blob_sas_token>'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }

Isso deverá ser executado rapidamente. Após a conclusão da execução, você terá criado 10 pontos de extremidade de serviço Web de previsão. Cada um contém um modelo treinado exclusivamente no conjunto de dados específico para um local de aluguel, todos de um teste de treinamento única. Para verificar isso, tente chamar esses pontos de extremidade usando o cmdlet **InvokeAmlWebServiceRRSEndpoint**, fornecendo a eles os mesmos dados de entrada. Espere ver resultados de previsão diferentes, já que os modelos são treinados com conjuntos de treinamento diferentes.

## <a name="full-powershell-script"></a>Script completo do PowerShell
Esta é a listagem do código-fonte completo:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and properly set to point to the valid workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

    # Create 10 endpoints on the scoring web service
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpontName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

    # Invoke the retraining API 10 times to produce 10 regression models in .ilearner format
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '?test'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }
