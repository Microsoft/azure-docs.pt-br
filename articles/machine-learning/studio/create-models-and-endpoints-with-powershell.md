---
title: "Criar vários modelos baseados em um teste | Microsoft Docs"
description: "Use o PowerShell para criar vários modelos do Machine Learning e pontos de extremidade de serviço Web com o mesmo algoritmo, mas com conjuntos de dados de treinamento diferentes."
services: machine-learning
documentationcenter: 
author: hning86
manager: jhubbard
editor: cgronlun
ms.assetid: 1076b8eb-5a0d-4ac5-8601-8654d9be229f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: garye;haining
ms.openlocfilehash: cc938fdaa6843f7c9e974d9b88a9b682b4678493
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-many-machine-learning-models-and-web-service-endpoints-from-one-experiment-using-powershell"></a>Criar vários modelos do Machine Learning e pontos de extremidade de serviço Web com base em um teste usando o PowerShell
Este é um problema comum do aprendizado de máquina: você deseja criar vários modelos que têm o mesmo fluxo de trabalho de treinamento e que usam o mesmo algoritmo, mas tem conjuntos de dados de treinamento diferentes como entrada. Este artigo mostra como fazer isso em escala no Azure Machine Learning Studio usando apenas um único teste.

Por exemplo, digamos que você tenha um negócio de franquia mundial de aluguel de bicicletas. Você deseja criar um modelo de regressão para prever a demanda de aluguel com base em dados históricos. Você tem 1.000 locais de aluguel em todo o mundo e coletou um conjunto de dados para cada local que inclui recursos importantes, tais como data, hora, clima e tráfego que são específicos de cada local.

Você poderia treinar seu modelo uma vez usando uma versão mesclada de todos os conjuntos de dados em todos os locais. Mas como cada um de seus locais tem um ambiente exclusivo, uma abordagem melhor seria treinar seu modelo de regressão separadamente, usando o conjunto de dados para cada local. Dessa forma, cada modelo treinado poderia levar em conta os diferentes tamanhos de armazenamento, volume, geografia, população, ambiente de tráfego para bicicletas, *etc.*

Essa poderia ser a melhor abordagem, mas você não quer criar 1.000 testes de treinamento no Azure Machine Learning com cada um deles representando um local exclusivo. Além de essa ser uma tarefa árdua, também parece muito ineficiente, já que cada teste teria todos os mesmos componentes, exceto pelo conjunto de dados de treinamento.

A boa notícia é que podemos fazer isso usando a [API de readaptação do Azure Machine Learning](retrain-models-programmatically.md) e automatizar a tarefa com o [PowerShell do Azure Machine Learning](powershell-module.md).

> [!NOTE]
> Para fazer com que nossa amostra seja executada mais rapidamente, reduziremos o número de locais de 1.000 para 10. No entanto, os mesmos princípios e procedimentos se aplicam aos 1.000 locais. A única diferença é que, se você deseja treinar com base nos 1.000 conjuntos de dados, provavelmente, é mais conveniente pensar em executar os seguintes scripts do PowerShell em paralelo. A forma como isso será feito está além do escopo deste artigo, mas é possível encontrar exemplos de multi-thread do PowerShell na Internet.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Configurar o teste de treinamento
Vamos usar um [teste de treinamento](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Training-Experiment-1) de exemplo que já criamos na [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com). Abra esse teste em seu espaço de trabalho do [Azure Machine Learning Studio](https://studio.azureml.net) .

> [!NOTE]
> Para acompanhar este exemplo, talvez você queira usar um espaço de trabalho padrão em vez de um espaço de trabalho gratuito. Criaremos um ponto de extremidade para cada cliente – para um total de 10 pontos de extremidade – e isso exigirá um espaço de trabalho padrão, pois um espaço de trabalho gratuito é limitado a 3 pontos de extremidade. Se você tiver apenas um espaço de trabalho gratuito, basta modificar os scripts abaixo para permitir apenas 3 locais.
> 
> 

O teste usa um módulo **Importar Dados** para importar o conjunto de dados de treinamento *customer001.csv* de uma conta de armazenamento do Azure. Vamos supor que coletamos conjuntos de dados de treinamento de todos os locais de aluguel de bicicletas e os armazenamos no mesmo local que o armazenamento de blobs com nomes de arquivo que vão de *rentalloc001.csv* a *rentalloc10.csv*.

![imagem](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Observe que um módulo **Saída do Serviço Web** foi adicionado ao módulo **Treinar Modelo**.
Quando esse teste for implantado como um serviço Web, o ponto de extremidade associado a essa saída retornará o modelo treinado no formato de um arquivo .ilearner.

Observe também que configuramos um parâmetro de serviço Web para a URL usada pelo módulo **Importar Dados** . Isso nos permite usar o parâmetro para especificar conjuntos de dados de treinamento individuais para treinar o modelo para cada local.
Existem outras maneiras pelas quais poderíamos ter feito isso, como usar uma consulta SQL com um parâmetro de serviço Web para obter dados de um banco de dados SQL Azure ou usando apenas um módulo **Entrada do Serviço Web** para transmitir um conjunto de dados para o serviço Web.

![imagem](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Agora, vamos executar esse teste de treinamento usando o valor padrão *rental001.csv* como o conjunto de dados de treinamento. Se você exibir a saída do módulo **Avaliar** (clique na saída e selecione **Visualizar**), será possível ver que obtemos um desempenho razoável de *AUC* = 0,91. Neste ponto, estamos prontos para implantar um serviço Web com base nesse teste de treinamento.

## <a name="deploy-the-training-and-scoring-web-services"></a>Implantar os serviços Web de treinamento e pontuação
Para implantar o serviço Web de treinamento, clique no botão **Configurar Serviço Web** abaixo da tela do teste e selecione **Implantar Serviço Web**. Chame esse serviço Web de “Treinamento do Aluguel de Bicicletas”.

Agora, precisamos implantar o serviço Web de pontuação.
Para fazer isso, podemos clicar em **Configurar Serviço Web** abaixo das telas e selecionar **Serviço Web Preditivo**. Isso criará um teste de pontuação.
Precisaremos fazer alguns pequenos ajustes para fazê-lo funcionar como um serviço Web, como remover a coluna de rótulo “cnt” dos dados de entrada e limitar a saída apenas à ID da instância e ao valor previsto correspondente.

Para poupá-lo desse trabalho, basta abrir o [teste preditivo](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Predicative-Experiment-1) na Galeria que já foi preparado.

Para implantar o serviço Web, execute o teste preditivo e clique no botão **Implantar Serviço Web** abaixo da tela. Nomeie o serviço Web de pontuação “Pontuação do Aluguel de Bicicletas”.

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>Criar 10 pontos de extremidade de serviço Web idênticos com o PowerShell
Este serviço Web é fornecido com um ponto de extremidade padrão. Mas não estamos muito interessados no ponto de extremidade padrão, pois ele não pode ser atualizado. O que precisamos fazer é criar 10 pontos de extremidade adicionais, um para cada local. Faremos isso com o PowerShell.

Primeiro, configuramos nosso ambiente do PowerShell:

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

Agora, já criamos 10 pontos de extremidade e todos eles contêm o mesmo modelo treinado em *customer001.csv*. Você pode exibi-los no Portal de Gerenciamento do Azure.

![imagem](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Atualizar os pontos de extremidade para usar conjuntos de dados de treinamento separados usando o PowerShell
A próxima etapa é atualizar os pontos de extremidade com modelos treinados exclusivamente nos dados individuais de cada cliente. Mas primeiro, precisamos produzir esses modelos com base no serviço Web **Treinamento do Aluguel de Bicicletas** . Vamos voltar ao serviço Web **Treinamento do Aluguel de Bicicletas** . Precisamos chamar seu ponto de extremidade BES 10 vezes com 10 conjuntos de dados de treinamento diferentes para produzir 10 modelos diferentes. Usaremos o cmdlet **InovkeAmlWebServiceBESEndpoint** do PowerShell para fazer isso.

Você também precisará fornecer credenciais para sua conta de armazenamento de blobs em `$configContent`, ou seja, nos campos `AccountName`, `AccountKey` e `RelativeLocation`. O `AccountName` pode ser um de seus nomes de conta, como mostrado no **Portal de Gerenciamento do Azure Clássico** (guia*Armazenamento* ). Quando você clica em uma conta de armazenamento, seu `AccountKey` pode ser encontrado pressionando o botão **Gerenciar Chaves de Acesso** na parte inferior e copiando a *Chave de Acesso Primária*. O `RelativeLocation` é o caminho relativo ao seu armazenamento em que um novo modelo será armazenado. Por exemplo, o caminho `hai/retrain/bike_rental/` no script abaixo aponta para um contêiner chamado `hai`, e `/retrain/bike_rental/` são subpastas. Atualmente, não é possível criar subpastas por meio da interface do usuário do portal, mas há [vários Gerenciadores de Armazenamento do Azure](../../storage/common/storage-explorers.md) que permitem que você faça isso. É recomendável que você crie um novo contêiner no armazenamento para armazenar os novos modelos treinados (arquivos .ilearner) da seguinte maneira: na página do armazenamento, clique no botão **Adicionar** na parte inferior e nomeie-o como `retrain`. Em resumo, as alterações necessárias para o script a seguir referem-se a `AccountName`, `AccountKey` e `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`).

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

Como você pode ver acima, em vez de construir 10 arquivos JSON de configuração de trabalho BES diferentes, criamos dinamicamente a cadeia de caracteres de configuração e a fornecemos ao parâmetro *jobConfigString* do cmdlet **InvokeAmlWebServceBESEndpoint** , já que não é realmente necessário manter uma cópia em disco.

Se tudo correr bem, após alguns instantes, você verá 10 arquivos .ilearner, de *model001.ilearner* a *model010.ilearner*, em sua conta de armazenamento do Azure. Agora estamos prontos para atualizar os 10 pontos de extremidade de serviço Web de pontuação com esses modelos usando o cmdlet **Patch-AmlWebServiceEndpoint** do PowerShell. Lembre-se mais uma vez de que podemos aplicar patch apenas aos pontos de extremidade não padrão que criamos de forma programática anteriormente.

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

Isso deverá ser executado rapidamente. Quando a execução for concluída, teremos criado com êxito 10 pontos de extremidade de serviço Web preditivo, cada um contendo um modelo treinado exclusivamente no conjunto de dados específico a um local de aluguel, tudo com base em um único teste de treinamento. Para verificar isso, é possível tentar chamar esses pontos de extremidade usando o cmdlet **InvokeAmlWebServiceRRSEndpoint**, fornecendo a eles os mesmos dados de entrada. Além disso, você deve esperar ver resultados de previsão diferentes, já que os modelos são treinados com conjuntos de treinamento diferentes.

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
