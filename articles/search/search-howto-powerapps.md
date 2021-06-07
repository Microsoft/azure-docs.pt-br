---
title: 'Tutorial: Consultar no Power Apps'
titleSuffix: Azure Cognitive Search
description: Diretrizes passo a passo sobre como criar um Power App que se conecta a um índice do Azure Cognitive Search, envia consultas e renderiza resultados.
author: luiscabrer
manager: eladz
ms.author: luisca
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: tutorial
ms.date: 11/17/2020
ms.openlocfilehash: e8c16f02cf6b77fa54d2a19abac48e9914aa99bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008558"
---
# <a name="tutorial-query-a-cognitive-search-index-from-power-apps"></a>Tutorial: Consultar um índice do Cognitive Search por meio do Power Apps

Aproveite o ambiente em método RAD do Power Apps para criar um aplicativo personalizado para seu conteúdo pesquisável no Azure Cognitive Search.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Conectar-se à Pesquisa Cognitiva do Azure
> * Configurar uma solicitação de consulta
> * Visualizar os resultados em um aplicativo de tela

Caso não tenha uma assinatura do Azure, abra uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* [Conta do Power Apps](https://make.powerapps.com)

* [Índice hotels-sample](search-get-started-portal.md) hospedado em seu serviço de pesquisa

* [Chave da API de consulta](search-security-api-keys.md#find-existing-keys)

## <a name="1---create-a-custom-connector"></a>1 – Criar um conector personalizado

Um conector no Power Apps é uma conexão de fonte de dados. Nesta etapa, você criará um conector personalizado para se conectar a um índice de pesquisa na nuvem.

1. [Entre](https://make.powerapps.com) no Power Apps.

1. À esquerda, expanda **Dados** > **Conectores Personalizados**.
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="Menu do conector personalizado" border="true":::

1. Selecione **+ Novo conector personalizado** e, em seguida, selecione **Criar do zero**.

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="Menu Criar do zero" border="true":::

1. Dê um nome ao seu conector personalizado (por exemplo, *AzureSearchQuery*) e, em seguida, clique em **Continuar**.

1. Insira as informações na página Geral:

   * Cor da tela de fundo do ícone (por exemplo, #007ee5)
   * Descrição (por exemplo, "Um conector para o Azure Cognitive Search")
   * No host, será necessário inserir a URL do serviço de pesquisa (como `<yourservicename>.search.windows.net`)
   * Para a URL base, basta inserir "/"

    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="Caixa de diálogo Informações Gerais" border="true":::

1. Na Página Segurança, defina *Chave da API* como o **Tipo de Autenticação**, defina o rótulo do parâmetro e o nome do parâmetro para *api-key*. Para **Localização do parâmetro**, selecione *Cabeçalho* conforme mostrado abaixo.

    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="Opção de tipo de autenticação" border="true":::

1. Na página Definições, selecione **+ Nova Ação** para criar uma ação que consultará o índice. Insira o valor "Consulta" para o resumo e o nome da ID da operação. Insira uma descrição como *"Consulta o índice de pesquisa"* .

    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="Opções da nova ação" border="true":::

1. Role para baixo. Em solicitações, selecione **+ Importar do exemplo** para configurar uma solicitação de consulta para o serviço de pesquisa:

   * Selecione o verbo `GET`

   * Para a URL, insira uma consulta de exemplo para o índice de pesquisa (`search=*` retorna todos os documentos, `$select=` permite que você escolha campos). A versão da API é um campo obrigatório. Uma URL, quando totalmente especificada, pode ter aparência semelhante a esta: `https://mydemo.search.windows.net/indexes/hotels-sample-index/docs?search=*&$select=HotelName,Description,Address/City&api-version=2020-06-30`

   * Para Cabeçalhos, digite `Content-Type`. Você definirá o valor como `application/json` em uma etapa posterior.

     O **Power Apps** usa a sintaxe na URL para extrair parâmetros da consulta: os parâmetros search, select e api-version se tornam configuráveis à medida que você avança no assistente.

       :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="Importar do exemplo" border="true":::

1. Clique em **Importar** para preencher automaticamente a solicitação. Conclua a configuração dos metadados do parâmetro clicando no símbolo **...** ao lado de cada um dos parâmetros. Clique em **Voltar** para retornar à página de solicitação após cada atualização de parâmetro.

   :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="Importar da caixa de diálogo de exemplo" border="true":::

1. Para *pesquisar*: Defina `*` como o **valor padrão**, defina **necessário** como *Falso* e defina **visibilidade** para *nenhuma*. 

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="Metadados de parâmetro de pesquisa" border="true":::

1. Para *selecionar*: Defina `HotelName,Description,Address/City` como o **valor padrão**, defina **necessário** como *Falso* e defina **visibilidade** para *nenhuma*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-4-parameter-metadata-select.png" alt-text="Selecionar metadados de parâmetro" border="true":::

1. Para *api-version*: Defina `2020-06-30` como o **valor padrão**, defina **necessário** como *Verdadeiro* e defina **visibilidade** para *interna*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="Metadados de parâmetro de versão" border="true":::

1. Para *Content-Type*: Defina como `application/json`.

1. Depois de fazer essas alterações, alterne para a exibição do **Editor do Swagger**. Na seção de parâmetros, você deverá ver a seguinte configuração:

    ```JSON
    parameters:
      - {name: search, in: query, required: false, type: string, default: '*'}
      - {name: $select, in: query, required: false, type: string, default: 'HotelName,Description,Address/City'}
      - {name: api-version, in: query, required: true, type: string, default: '2020-06-30',
        x-ms-visibility: internal}
      - {name: Content-Type, in: header, required: false, type: string}
    ```

1. Volte para o assistente e retorne para a etapa **3. Solicitar**. Role para baixo até a seção Resposta. Clique em **"Adicionar resposta padrão"** . Isso é crítico porque ajudará o Power Apps a compreender o esquema da resposta. 

1. Cole uma resposta de exemplo. Um modo fácil de capturar uma resposta de exemplo é por meio do Gerenciador de Pesquisa no portal do Azure. No Gerenciador de Pesquisa, você deve inserir a mesma consulta que inseriu para a solicitação, mas adicionar **$top=2** para restringir os resultados a apenas dois documentos: `search=*&$select=HotelName,Description,Address/City&$top=2`. 

   O Power Apps precisa de apenas de alguns resultados para detectar o esquema. Agora você copiar a resposta a seguir para o assistente, supondo que você esteja usando o índice hotels-sample.

    ```JSON
    {
        "@odata.context": "https://mydemo.search.windows.net/indexes('hotels-sample-index')/$metadata#docs(*)",
        "value": [
            {
                "@search.score": 1,
                "HotelName": "Arcadia Resort & Restaurant",
                "Description": "The largest year-round resort in the area offering more of everything for your vacation – at the best value!  What can you enjoy while at the resort, aside from the mile-long sandy beaches of the lake? Check out our activities sure to excite both young and young-at-heart guests. We have it all, including being named “Property of the Year” and a “Top Ten Resort” by top publications.",
                "Address": {
                    "City": "Seattle"
                }
            },
            {
                "@search.score": 1,
                "HotelName": "Travel Resort",
                "Description": "The Best Gaming Resort in the area.  With elegant rooms & suites, pool, cabanas, spa, brewery & world-class gaming.  This is the best place to play, stay & dine.",
                "Address": {
                    "City": "Albuquerque"
                }
            }
        ]
    }
    ```

    > [!TIP] 
    > Há um limite de caracteres que você pode inserir para a resposta JSON, portanto, talvez seja melhor que você simplifique o JSON antes de colá-lo. O esquema e o formato da resposta são mais importantes do que os valores propriamente ditos. Por exemplo, o campo Descrição pode ser simplificado apenas para a primeira frase.

1. Clique em **Importar** para adicionar a resposta padrão.

1. Clique em **Criar conector** no canto superior direito para salvar a definição.

1. Clique em **Fechar** para fechar o conector.

## <a name="2---test-the-connection"></a>2 – Testar a conexão

Quando o conector é criado pela primeira vez, você precisa reabri-lo na lista de Conectores Personalizados para testá-lo. Posteriormente, se você fizer atualizações adicionais, poderá testar de dentro do assistente.

Você precisará de uma [chave de API de consulta](search-security-api-keys.md#find-existing-keys) para essa tarefa. Cada vez que uma conexão é criada, seja para uma execução de teste ou inclusão em um aplicativo, o conector precisa da chave de API de consulta usada para conexão com o Azure Cognitive Search.

1. Na extrema esquerda, clique em **Conectores Personalizados**.

1. Encontre seu conector na lista (neste tutorial, é "AzureSearchQuery").

1. Selecione o conector, expanda a lista de ações e selecione **Exibir Propriedades**.

    :::image type="content" source="./media/search-howto-powerapps/1-11-1-test-connector.png" alt-text="Exibir propriedades" border="true":::

1. Selecione **Editar** no canto superior direito.

1. Selecione **4. Testar** para abrir a página de teste.

1. Em Operação de Teste, clique em **+ Nova Conexão**.

1. Insira uma chave de API de consulta. Esta é uma consulta do Azure Cognitive Search para acesso somente leitura a um índice. Você pode [encontrar a chave](search-security-api-keys.md#find-existing-keys) no portal do Azure. 

1. Em Operações, clique no botão **Operação de teste**. Se você tiver êxito, verá um status de 200 e, no corpo da resposta, deverá ver o JSON que descreve os resultados da pesquisa.

    :::image type="content" source="./media/search-howto-powerapps/1-11-2-test-connector.png" alt-text="Resposta JSON" border="true":::

## <a name="3---visualize-results"></a>3 – Visualizar os resultados

Nesta etapa, crie um Power App com uma caixa de pesquisa, um botão de pesquisa e uma área de exibição para os resultados. O Power App será conectado ao conector personalizado criado recentemente para obter os dados do Azure Search.

1. À esquerda, expanda **Aplicativos** >  **+ Novo aplicativo** > **Tela**.

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="Criar aplicativo de tela" border="true":::

1. Selecione o tipo de aplicativo. Para este tutorial, crie um **Aplicativo em Branco** com o **Layout para Telefone**. O **Power Apps Studio** será exibido.

1. Uma vez no Studio, selecione a guia **Fontes de Dados** e clique no conector que você acabou de criar. Em nosso caso, ele se chama *AzureSearchQuery*. Clique em **Adicionar uma conexão**.

   Insira a chave de API de consulta.

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="conectar conector" border="true":::

    Agora o *AzureSearchQuery* é uma fonte de dados que está disponível para ser usada em seu aplicativo.

1. Na **guia Inserir**, adicione alguns controles à tela.

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="Inserir controles" border="true":::

1. Insira os seguintes elementos:

   * Um rótulo de texto com o valor "Consulta:"
   * Um elemento de entrada de texto (nomeie-o *txtQuery*, valor padrão: "*")
   * Um botão com o texto "Pesquisar" 
   * Uma galeria vertical (nomeie-a *galleryResults*)

    A tela deverá ter aparência semelhante à seguinte:

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="Layout de controles" border="true":::

1. Para fazer com que o **botão Pesquisar** emita uma consulta, cole a seguinte ação em **OnSelect**:

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Query({search: txtQuery.Text}).value))
    ```

   A captura de tela a seguir mostra a barra de fórmulas para a ação **OnSelect**.

    :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="OnSelect do botão" border="true":::

   Essa ação fará com que o botão atualize uma nova coleção chamada *azResult* com o resultado da consulta de pesquisa, usando o texto na caixa de texto *txtQuery* como o termo de consulta.

   > [!NOTE]
   > Experimente isso se você receber um erro de sintaxe de fórmula "A função 'ClearCollect' tem algumas funções inválidas":
   > 
   > * Primeiro, verifique se a referência do conector está correta. Limpe o nome do conector e comece a digitá-lo. O IntelliSense deverá sugerir o conector e o verbo corretos.
   > 
   > * Se o erro persistir, exclua e recrie o conector. Se houver várias instâncias de um conector, o aplicativo poderá estar usando uma incorreta.
   > 

1. Vincule o controle Galeria Vertical à coleção *azResult* que foi criada quando você concluiu a etapa anterior. 

   Selecione o controle de galeria e execute as ações a seguir no painel de propriedades.

   * Defina **DataSource** como *azResult*.
   * Selecione um **Layout** que funcione para você com base no tipo de dados no índice. Nesse caso, usamos o layout *Título, subtítulo e corpo*.
   * Escolha **Editar Campos** e selecione os campos que deseja visualizar.

    Já que fornecemos um exemplo de resultado quando definimos o conector, o aplicativo está ciente dos campos disponíveis no índice.
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="Campos da galeria" border="true":::   
 
1. Pressione **F5** para visualizar o aplicativo.  

    :::image type="content" source="./media/search-howto-powerapps/2-8-3-final.png" alt-text="Aplicativo final" border="true":::    

<!--     Remember that the fields can be set to calculated values.

    For the example, setting using the *"Image, Title and Subtitle"* layout and specifying the *Image* function as the concatenation of the root path for the data and the file name (for instance, `"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`) will produce the result below.

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="Final app" border="true":::         -->

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você está trabalhando em sua própria assinatura, é uma boa ideia identificar, no final de um projeto, se você ainda precisa dos recursos criados. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Você pode localizar e gerenciar recursos no portal usando o link **Todos os recursos** ou **Grupos de recursos** no painel de navegação à esquerda.

Se você estiver usando um serviço gratuito, estará limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite.

## <a name="next-steps"></a>Próximas etapas

O Power Apps permite o método RAD de aplicativos personalizados. Agora que você sabe como se conectar a um índice de pesquisa, saiba mais sobre como criar uma experiência de visualização rica em um Power App personalizado.

> [!div class="nextstepaction"]
> [Catálogo de aprendizado do Power Apps](/powerapps/learning-catalog/get-started)