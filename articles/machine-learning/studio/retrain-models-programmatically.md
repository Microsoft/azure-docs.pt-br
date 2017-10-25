---
title: "Readaptar os modelos de Machine Learning de forma programática | Microsoft Docs"
description: "Aprenda como readaptar um modelo de forma programática e atualizar o serviço Web para usar o modelo treinado recentemente no Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: raymondlaghaeian
manager: jhubbard
editor: cgronlun
ms.assetid: 7ae4f977-e6bf-4d04-9dde-28a66ce7b664
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: raymondl;garye;v-donglo
ms.openlocfilehash: c56ce659766536772d203d0366ef6b53e544a82b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="retrain-machine-learning-models-programmatically"></a>Readaptar os modelos do Machine Learning de forma programática
Neste passo a passo, você aprenderá como programaticamente readaptar um serviço Web do Machine Learning do Azure usando C# e o serviço de Execução de Lote do Machine Learning.

Depois de você ter readaptado o modelo, as instruções a seguir mostram como atualizar o modelo no seu serviço Web preditivo:

* Se você implantou um serviço Web Clássico no portal de serviços Web do Machine Learning, confira [Readaptar um serviço Web Clássico](retrain-a-classic-web-service.md). 
* Se você implantou um Novo serviço Web, confira [Readaptar um Novo serviço Web usando os cmdlets de Gerenciamento do Machine Learning](retrain-new-web-service-using-powershell.md).

Para obter uma visão geral do processo de readaptação, confira [Readaptar um Modelo do Machine Learning](retrain-machine-learning-model.md).

Se você quiser começar a usar o seu novo serviço Web baseado no Azure Resource Manager existente, confira [Readaptar um serviço Web preditivo existente](retrain-existing-resource-manager-based-web-service.md).

## <a name="create-a-training-experiment"></a>Criar um teste de treinamento
Para este exemplo, você usará "Amostra 5: Treinar, testar, avaliar para classificação binária: conjunto de dados adulto" das amostras do Microsoft Azure Machine Learning. 

Para criar o experimento:

1. Entre no Microsoft Azure Machine Learning Studio. 
2. No canto inferior direito do painel, clique em **Novo**.
3. Nas Amostras da Microsoft, selecione a Amostra 5.
4. Para renomear o teste, na parte superior da tela do experimento, selecione o nome do teste "Amostra 5: Treinar, testar, avaliar para classificação binária: conjunto de dados adulto".
5. Tipo de modelo de censo.
6. Na parte inferior da tela do experimento, clique em **Executar**.
7. Clique em **Configurar o Serviço Web** e selecione **Readaptação do Serviço Web**. 

A seguir, o teste inicial.
   
   ![Experimento inicial.][2]


## <a name="create-a-predictive-experiment-and-publish-as-a-web-service"></a>Criar um Teste de Preditivo e publicar como um serviço Web
Em seguida, crie um Experimento Predicativo.

1. Na parte inferior da tela do experimento, clique em **Configurar o Serviço Web** e selecione **Serviço Web Preditivo**. Isso salva o modelo como um Modelo Treinado e adiciona os módulos de Entrada e Saída do serviço Web. 
2. Clique em **Executar**. 
3. Após o teste em execução ter terminado, clique em **Implantar Serviço Web [Clássico]** ou **Implantar Serviço Web [Novo]**.

> [!NOTE] 
> Para implantar um novo serviço Web, você precisa ter permissões suficientes na assinatura na qual o serviço Web está sendo implantado. Para obter mais informações, consulte [Gerenciar um serviço Web usando o portal de Serviços Web do Azure Machine Learning](manage-new-webservice.md). 

## <a name="deploy-the-training-experiment-as-a-training-web-service"></a>Implantar o Teste de Treinamento como um serviço Web de Treinamento
Para readaptar o modelo treinado, você deve implantar o Teste de Treinamento criado como um Serviço Web de Readaptação. Este serviço Web precisa um módulo *Saída do Serviço Web* conectado ao módulo *[Modelo de Treino][train-model]* para poder produzir novos modelos treinados.

1. Para voltar para o teste de treinamento, clique no ícone de Experimentos no painel esquerdo, em seguida, clique no experimento chamado Modelo de Censo.  
2. Na caixa de pesquisa Pesquisar Itens do Experimento, digite o serviço Web. 
3. Arraste um módulo *Entrada do Serviço Web* para a tela do experimento e conecte sua saída ao módulo *Limpar Dados Ausentes*.  Isso garante que os dados de readaptação são processados da mesma forma que os dados de treinamento original.
4. Arraste os dois módulos *Saída do Serviço Web* para as telas do experimento. Conecte a saída do módulo *Modelo de Treinamento* a um e a saída do módulo *Modelo de Avaliação* ao outro. A saída do serviço Web para o **Modelo de Treino** fornecerá o novo modelo treinado. A saída anexada ao **Modelo de Avaliação** retorna a saída do módulo, que são os resultados de desempenho.
5. Clique em **Executar**. 

Em seguida, você deve implantar o Teste de Treinamento como um serviço Web que produz um modelo treinado e os resultados de avaliação do modelo. Para fazer isso, o próximo conjunto de ações dependem de você estar trabalhando com um serviço Web Clássico ou um Novo serviço Web.  

**Serviço Web Clássico**

Na parte inferior da tela do experimento, clique em **Configurar o Serviço Web** e selecione **Implantar Serviço Web [Clássico]**. O **Painel** do Serviço Web é exibido com a Chave de API e a página de ajuda da API para a Execução em Lotes. Apenas o método de Execução em Lotes pode ser usado para criar Modelos Treinados.

**Novo Serviço Web**

Na parte inferior da tela do experimento, clique em **Configurar o Serviço Web** e selecione **Implantar Serviço Web [Novo]**. O portal dos Serviços Web de Machine Learning do Azure abre a página Implantar Serviço Web. Digite um nome para o serviço Web, escolha um plano de pagamento clique em **Implantar**. Apenas o método Execução em Lotes pode ser usado para criar os Modelos Treinados

Em ambos os casos, após o teste terminar a execução, o fluxo de trabalho resultante fica como a seguir:

![Fluxo de trabalho resultante após a execução.][4]



## <a name="retrain-the-model-with-new-data-using-bes"></a>Você quer readaptar o modelo com novos dados
Para este exemplo, você está usando C# para criar o aplicativo de readaptação. Você também pode usar o código de exemplo do Python ou R para realizar essa tarefa.

Para chamar as APIs de Readaptação:

1. Crie um aplicativo de console C# no Visual Studio: **Novo** > **Projeto** > **Visual C#** > **Área de Trabalho Clássica do Windows** > **Aplicativo de Console (.NET Framework)**.
2. Entre no portal do Serviço Web de Machine Learning.
3. Se você estiver trabalhando com um serviço Web Clássico, clique em **Serviços Web Clássicos**.
   1. Clique no serviço Web com o qual você está trabalhando.
   2. Clique no ponto de extremidade padrão.
   3. Clique em **Consumo**.
   4. Na parte inferior da página **Consumir**, na seção **Código de Exemplo**, clique em **Lote**.
   5. Continue na etapa 5 deste procedimento.
4. Se você estiver trabalhando com um Novo serviço Web, clique em **Serviços Web**.
   1. Clique no serviço Web com o qual você está trabalhando.
   2. Clique em **Consumo**.
   3. Na parte inferior da página Consumir, na seção **Código de Exemplo**, clique em **Lote**.
5. Copie o código C# de exemplo da execução em lotes e cole-o no arquivo Program.cs, verificando se o namespace permanece intacto.

Adicione o pacote do Nuget Microsoft.AspNet.WebApi.Client como especificado nos comentários. Para adicionar a referência a Microsoft.WindowsAzure.Storage.dll, você precisará primeiro instalar a biblioteca de cliente para os serviços de armazenamento do Microsoft Azure. Para obter mais informações, consulte [Serviços de Armazenamento do Windows](https://www.nuget.org/packages/WindowsAzure.Storage).

### <a name="update-the-apikey-declaration"></a>Atualizar a declaração da apikey
Localize a declaração da **apikey** .

    const string apiKey = "abc123"; // Replace this with the API key for the web service

Na seção **Informações básicas de consumo** da página **Consumir**, localize a chave primária e copie-a para a declaração da **apikey**.

### <a name="update-the-azure-storage-information"></a>Atualize as informações do Armazenamento do Azure
O código de exemplo de BES carrega um arquivo de uma unidade local (por exemplo "C:\temp\CensusIpnput.csv") para o armazenamento do Azure, processa e grava os resultados de volta para o armazenamento do Azure.  

Para realizar essa tarefa, você deve recuperar as informações de nome da Conta de armazenamento, da chave e do contêiner para sua Conta de armazenamento no portal clássico do Azure e os valores correspondentes de atualização no código. 

1. Entre no portal clássico do Azure.
2. Na coluna de navegação à esquerda, clique em **Armazenamento**.
3. Na lista de contas de armazenamento, selecione uma para armazenar o modelo recuperado.
4. Na parte inferior da página, clique em **Gerenciar Chaves de Acesso**.
5. Copie e salve a **Chave de Acesso Primária** e feche a caixa de diálogo. 
6. Na parte superior da página, clique em **Contêineres**.
7. Selecione um contêiner existente ou crie um novo e salve o nome.

Localize as declarações *StorageAccountName*, *StorageAccountKey* e *StorageContainerName*, e atualize os valores salvos no portal no Azure.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name

Você também deve garantir que o arquivo de entrada está disponível no local especificado no código. 

### <a name="specify-the-output-location"></a>Especificar o local de saída
Ao especificar o local de saída no Conteúdo de Solicitação, a extensão do arquivo especificado em *RelativeLocation* deve ser definido como ilearner. 

Veja os exemplos a seguir:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you would like to use for your output file, and valid file extension (usually .csv for scoring results, or .ilearner for trained models)*/
            }
        },

> [!NOTE]
> Os nomes de seus locais de saída podem ser diferentes neste passo a passo com base na ordem em que você adicionou os módulos de saída do serviço Web. Como você configura esse Teste de Treinamento com duas saídas, os resultados incluem informações de local de armazenamento para ambos.  
> 
> 

![Saída da readaptação][6]

Diagrama 4:Saída da readaptação.

## <a name="evaluate-the-retraining-results"></a>Avaliar os resultados da readaptação
Quando você executa o aplicativo, a saída inclui o token SAS e a URL necessários para acessar os resultados da avaliação.

Você pode ver os resultados do desempenho do modelo readaptado ao combinar *BaseLocation*, *RelativeLocation* e *SasBlobToken* dos resultados de saída para *output2* (como mostrado na imagem de readaptação anterior) e colando a URL completa na barra de endereço do navegador.  

Examine os resultados para determinar se o modelo treinado recentemente executa bem o suficiente para substituir o existente.

Copie *BaseLocation*, *RelativeLocation* e *SasBlobToken* dos resultados de saída e use-os durante o processo de readaptação.

## <a name="next-steps"></a>Próximas etapas
Se você tiver implantado o serviço Web de previsão clicando em **implantar o serviço Web [clássico]**, consulte [treinar novamente um serviço web clássico](retrain-a-classic-web-service.md).

Se você tiver implantado um Novo serviço Web clicando em **Implantar o Serviço Web [Novo]**, consulte [treinar novamente um serviço web novo usando os cmdlets de gerenciamento do Machine Learning](retrain-new-web-service-using-powershell.md).

<!-- Retrain a New web service using the Machine Learning Management REST API -->


[1]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png
[7]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE07.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
