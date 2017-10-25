---
title: "Usar dados de Importação/Exportação em serviços Web do Azure Machine Learning | Microsoft Docs"
description: "Saiba como usar os módulos Importar Dados e Exportar Dados para enviar e receber dados de um serviço Web."
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondlaghaeian
editor: 
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.author: v-donglo
ms.openlocfilehash: 40e1dbf4fc1618deec5e7f85fb956f2881e08319
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploying-azure-ml-web-services-that-use-data-import-and-data-export-modules"></a>Implantação de serviços Web do Azure AM que usam módulos Importar Dados e Exportar Dados

Quando você cria um experimento de previsão, normalmente adiciona uma entrada e uma saída de serviço Web. Ao implantar o experimento, os consumidores poderão enviar e receber dados do serviço Web por meio de entradas e saídas. Para alguns aplicativos, os dados do cliente podem estar disponíveis a partir de um feed de dados ou já residirem em uma fonte de dados externa, como o armazenamento de Blobs do Azure. Nesses casos, eles não precisam de dados de leitura e gravação usando saídas e entradas do serviço Web. Em vez disso, eles podem usar o BES (Serviço de execução de lote) para ler dados da fonte de dados usando um módulo Importar Dados e gravar os resultados de pontuação em um local de dados diferente usando um módulo Exportar Dados.

Os módulos Importar Dados e Exportar Dados podem ler e gravar em vários locais de dados, como uma URL da Web via HTTP, uma Consulta de Hive, um banco de dados SQL do Azure, um armazenamento de Tabelas do Azure, um armazenamento de Blobs do Azure, um provedor de Feed de Dados ou um banco de dados SQL local.

Este tópico usa o "Exemplo 5: Treinamento, teste, avaliação para classificação binária: conjunto de dados adulto" e pressupõe que o conjunto de dados já tenha sido carregado em uma tabela SQL do Azure chamada censusdata.

## <a name="create-the-training-experiment"></a>Criar o teste de treinamento
Quando você abre o "Exemplo 5: Treinamento, teste, avaliação para classificação binária: conjunto de dados adulto", ele usa o conjunto de dados do exemplo adulto Classificação binária de receita de censo. E o experimento na tela será semelhante à imagem a seguir:

![Configuração inicial do experimento.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Para ler os dados da tabela SQL do Azure:

1. Exclua o módulo do conjunto de dados.
2. Na caixa de pesquisa dos componentes, digite importar.
3. Na lista de resultados, adicione um módulo *Importar Dados* para a tela do experimento.
4. Conecte a saída do módulo *Importar Dados* à entrada do módulo *Limpar Dados Ausentes* módulo.
5. No painel de propriedades, selecione **Banco de Dados SQL do Azure** in the **Fonte de Dados** .
6. Nos campos **Nome do servidor de banco de dados**, **Nome do banco de dados**, **Nome de usuário** e **Senha**, insira as informações apropriadas ao seu banco de dados.
7. No campo Consulta de banco de dados, digite a consulta a seguir.
   
     select [age],
   
        [workclass],
        [fnlwgt],
        [education],
        [education-num],
        [marital-status],
        [occupation],
        [relationship],
        [race],
        [sex],
        [capital-gain],
        [capital-loss],
        [hours-per-week],
        [native-country],
        [income]
     from dbo.censusdata;
8. Na parte inferior da tela do experimento, clique em **Executar**.

## <a name="create-the-predictive-experiment"></a>Criar o experimento preditivo
Em seguida, configure o experimento preditivo do qual você implantará o serviço Web.

1. Na parte inferior da tela do experimento, clique em **Configurar o Serviço Web** e selecione **Serviço Web Preditivo [Recomendado]**.
2. Remova os módulos *Entrada do Serviço Web* e *Saída do Serviço Web* do experimento preditivo. 
3. Na caixa de pesquisa dos componentes, digite exportar.
4. Na lista de resultados, adicione um módulo *Exportar Dados* para a tela do experimento.
5. Conecte a saída do módulo *Modelo de Pontuação* à entrada do módulo *Exportar Dados*. 
6. No painel de propriedades, selecione **Banco de Dados SQL do Azure** no menu suspenso de destino dos dados.
7. Nos campos **Nome do servidor de banco de dados**, **Nome do banco de dados**, **Nome da conta de usuário do servidor** e **Senha da conta de usuário do servidor**, insira as informações apropriadas ao seu banco de dados.
8. No campo **Lista separada por vírgulas de colunas a serem salvas** , digite Rótulos de Pontuação.
9. No campo **Nome da tabela de dados**, digite dbo.ScoredLabels. Se a tabela não existir, ela será criado quando o experimento for executado ou o serviço Web for chamado.
10. No campo **Lista separada por vírgulas de colunas de banco de dados** , digite ScoredLabels.

Quando você escreve um aplicativo que chama o serviço Web final, convém especificar uma consulta de entrada diferente ou a tabela de destino em tempo de execução. Para configurar essas entradas e saídas, use o recurso de Parâmetros de Serviço Web para definir o módulo *Importar Dados*, a propriedade *Fonte de dados* e a propriedade de destino de dados do modo *Exportar Dados*.  Para saber mais sobre Parâmetros de Serviço Web, consulte a entrada [Parâmetros de Serviço Web do AzureML](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) no blog Cortana Intelligence and Machine Learning Blog.

Para configurar os Parâmetros de Serviço Web para a consulta de importação e a tabela de destino:

1. No painel de propriedades do módulo *Importar Dados*, clique no ícone na parte superior direita do campo **Consulta de banco de dados** e selecione **Definir como parâmetro de serviço Web**.
2. No painel de propriedades do módulo *Exportar Dados*, clique no ícone na parte superior direita do campo **Nome da tabela de dados** e selecione **Definir como parâmetro de serviço Web**.
3. Na parte inferior do painel de propriedades do módulo *Exportar Dados* , na seção **Parâmetros de Serviço Web** , clique em Consulta de banco de dados e renomeie a consulta.
4. Clique em **Nome da tabela de dados** e troque seu nome para **Tabela**.

Quando terminar, seu experimento deverá ser semelhante à imagem a seguir:

![Aparência final do experimento.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Agora você pode implantar o experimento como um serviço Web.

## <a name="deploy-the-web-service"></a>Implantar o serviço Web
Você pode implantar um serviço Web Clássico ou Novo.

### <a name="deploy-a-classic-web-service"></a>Implantar um Serviço Web Clássico
Para implantar como um serviço Web Clássico e criar um aplicativo para consumi-lo:

1. Na parte inferior da tela do experimento, clique em Executar.
2. Após a conclusão da execução, clique em **Implantar Serviço Web** e selecione **Implantar Serviço Web [Clássico]**.
3. No painel do serviço Web, localize sua chave de API. Copie e salve-a para uso posterior.
4. Na tabela **Ponto de Extremidade Padrão**, clique no link **Execução em Lote** para abrir a Página de Ajuda da API.
5. No Visual Studio, crie um aplicativo de console C#: **Novo** > **Projeto** > **Visual C#** > **Área de Trabalho Clássica do Windows** > **Aplicativo de Console (.NET Framework)**.
6. Na Página de Ajuda da API, encontre a seção **Código de Exemplo** na parte inferior da página.
7. Copie e cole o código de exemplo C# no arquivo Program.cs e remova todas as referências ao armazenamento de blobs.
8. Atualize o valor da variável *apiKey* com a chave de API que você salvou anteriormente.
9. Localize a declaração de solicitação e atualize os valores dos Parâmetros de Serviço Web que são passados para os módulos *Importar Dados* e *Exportar Dados*. Nesse caso, você usa a consulta original, mas definirá um novo nome de tabela.
   
        var request = new BatchExecutionRequest() 
        {           
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable2" },
            }
        };
10. Execute o aplicativo. 

Após a conclusão da execução, uma nova tabela será adicionada ao banco de dados que contém os resultados da pontuação.

### <a name="deploy-a-new-web-service"></a>Implantar um serviço Web Novo

> [!NOTE] 
> Para implantar um novo serviço Web, você precisa ter permissões suficientes na assinatura na qual o serviço Web está sendo implantado. Para saber mais, confira [Gerenciar um serviço Web usando o portal de Serviços Web do Azure Machine Learning](manage-new-webservice.md). 

Para implantar como um serviço Web Novo e criar um aplicativo para consumi-lo:

1. Na parte inferior da tela do experimento, clique em **Executar**.
2. Após a conclusão da execução, clique em **Implantar Serviço Web** e selecione **Implantar Serviço Web [Novo]**.
3. Na página Implantar Experimento, insira um nome para o serviço Web e selecione um plano de preços, depois clique em **Implantar**.
4. Na página **Início rápido**, clique em **Consumir**.
5. Na seção **Código de Exemplo**, clique em **Lote**.
6. No Visual Studio, crie um aplicativo de console C#: **Novo** > **Projeto** > **Visual C#** > **Área de Trabalho Clássica do Windows** > **Aplicativo de Console (.NET Framework)**.
7. Copie e cole o código de exemplo de C# no arquivo Program.cs.
8. Atualize o valor da variável *apiKey* com a **Chave Primária** localizada na seção **Informações básicas de consumo**.
9. Localize a declaração *scoreRequest* e atualize os valores dos Parâmetros de Serviço Web que são passados para os módulos *Importar Dados* e *Exportar Dados*. Nesse caso, você usa a consulta original, mas definirá um novo nome de tabela.
   
        var scoreRequest = new
        {       
            Inputs = new Dictionary<string, StringTable>()
            {
            },
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable3" },
            }
        };
10. Execute o aplicativo. 

