---
title: Fazer solicitações com o Postman
titleSuffix: Azure Digital Twins
description: Saiba como configurar e usar o postmaster para testar as APIs do gêmeos digital do Azure.
ms.author: baanders
author: baanders
ms.service: digital-twins
services: digital-twins
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: d4a6e25578cd26b10b34f74a9f859d4957cc553b
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783805"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>Como usar o postmaster para enviar solicitações para as APIs do Azure digital gêmeos

O [Postman](https://www.getpostman.com/) é uma ferramenta de testes REST que fornece importantes funcionalidades de solicitação HTTP em uma GUI baseada em plug-in e desktop. Você pode usá-lo para criar solicitações HTTP e enviá-las para as [APIs REST do Azure digital gêmeos](how-to-use-apis-sdks.md).

Este artigo descreve como configurar o [cliente REST do postmaster](https://www.getpostman.com/) para interagir com as APIs do gêmeos digital do Azure, por meio das seguintes etapas:

1. Use o CLI do Azure para [**obter um token de portador**](#get-bearer-token) que será usado para fazer solicitações de API no postmaster.
1. Configure uma [**coleção do postmaster**](#about-postman-collections) e configure o cliente REST do postmaster para usar o token de portador para autenticar. Ao configurar a coleção, você pode escolher uma destas opções:
    1. [**Importe**](#import-collection-of-azure-digital-twins-apis) uma coleção pré-criada de solicitações da API do gêmeos digital do Azure.
    1. [**Crie**](#create-your-own-collection) sua própria coleção do zero.
1. [**Adicione solicitações**](#add-an-individual-request) à sua coleção configurada e envie-as para as APIs do Azure digital gêmeos.

O Azure digital gêmeos tem dois conjuntos de APIs com os quais você pode trabalhar: **plano de dados** e **plano de controle**. Para obter mais informações sobre a diferença entre esses conjuntos de API, consulte [*como usar as APIs e SDKs do gêmeos digital do Azure*](how-to-use-apis-sdks.md). Este artigo contém informações para ambos os conjuntos de API.

## <a name="prerequisites"></a>Pré-requisitos

Para continuar usando o postmaster para acessar as APIs do gêmeos digital do Azure, você precisa configurar uma instância do gêmeos digital do Azure e baixar o postmaster. O restante desta seção orientará você nessas etapas.

### <a name="set-up-azure-digital-twins-instance"></a>Configurar instância dos Gêmeos Digitais do Azure

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>Baixar o postmaster

Em seguida, baixe a versão da área de trabalho do cliente do postmaster. Navegue até [*www.getpostman.com/apps*](https://www.getpostman.com/apps) e siga os prompts para baixar o aplicativo.

## <a name="get-bearer-token"></a>Obter token de portador

Agora que você configurou o postmaster e sua instância do gêmeos digital do Azure, você precisará obter um token de portador que as solicitações do postmaster podem usar para autorizar nas APIs do gêmeos digital do Azure.

Há várias maneiras possíveis de obter esse token. Este artigo usa o [CLI do Azure](/cli/azure/install-azure-cli) para entrar em sua conta do Azure e obter um token dessa maneira.

Se você tiver um CLI do Azure [instalado localmente](/cli/azure/install-azure-cli), poderá iniciar um prompt de comando em seu computador para executar os comandos a seguir.
Caso contrário, você pode abrir uma janela de [Azure cloud Shell](https://shell.azure.com) no navegador e executar os comandos.

1. Primeiro, verifique se você está conectado ao Azure com as credenciais apropriadas, executando este comando:

    ```azurecli-interactive
    az login
    ```

2. Em seguida, use o comando [AZ Account Get-Access-token](/cli/azure/account#az_account_get_access_token) para obter um token de portador com acesso ao serviço de gêmeos digital do Azure. Neste comando, você passará a ID do recurso para o ponto de extremidade do serviço gêmeos digital do Azure, para obter um token de acesso que possa acessar os recursos do gêmeos digital do Azure. 

    O contexto necessário para o token depende de qual conjunto de APIs você está usando, portanto, use as guias abaixo para selecionar entre as APIs plano de [dados](how-to-use-apis-sdks.md#overview-data-plane-apis) e [plano de controle](how-to-use-apis-sdks.md#overview-control-plane-apis) .

    # <a name="data-plane"></a>[Plano de dados](#tab/data-plane)
    
    Para obter um token a ser usado com as APIs do **plano de dados** , use o seguinte valor estático para o contexto do token: `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Esta é a ID de recurso para o ponto de extremidade do serviço gêmeos do Azure digital.
    
    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```
    
    # <a name="control-plane"></a>[Painel de controle](#tab/control-plane)
    
    Para obter um token a ser usado com as APIs do **plano de controle** , use o seguinte valor para o contexto do token: `https://management.azure.com/` .
    
    ```azurecli-interactive
    az account get-access-token --resource https://management.azure.com/
    ```
    ---


3. Copie o valor de `accessToken` no resultado e salve-o para usar na próxima seção. Esse é o **valor do token** que você fornecerá ao postmaster para autorizar suas solicitações.

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="Captura de tela do console mostrando o resultado do comando AZ Account Get-Access-token. O campo accessToken e seu valor de exemplo são realçados.":::

>[!TIP]
>Esse token é válido por pelo menos cinco minutos e um máximo de 60 minutos. Se você ficar sem tempo alocado para o token atual, poderá repetir as etapas nesta seção para obter um novo.

Em seguida, você configurará o postmaster para usar esse token para fazer solicitações de API para o gêmeos digital do Azure.

## <a name="about-postman-collections"></a>Sobre as coleções do postmaster

As solicitações no postmaster são salvas em **coleções** (grupos de solicitações). Ao criar uma coleção para agrupar suas solicitações, você pode aplicar configurações comuns a várias solicitações de uma só vez. Isso pode simplificar muito a autorização se você planeja criar mais de uma solicitação em relação às APIs do Azure digital gêmeos, pois você só precisa configurar esses detalhes uma vez para toda a coleção.

Ao trabalhar com o Azure digital gêmeos, você pode começar importando uma [coleção pré-criada de todas as solicitações do gêmeos digital do Azure](#import-collection-of-azure-digital-twins-apis). Talvez você queira fazer isso se estiver explorando as APIs e quiser configurar rapidamente um projeto com exemplos de solicitação.

Como alternativa, você também pode optar por começar do zero, [criando sua própria coleção vazia](#create-your-own-collection) e preenchendo-a com solicitações individuais que chamam apenas as APIs de que você precisa. 

As seções a seguir descrevem esses dois processos. O restante do artigo ocorre em seu aplicativo local do postmaster, então vá em frente e abra o aplicativo de postagem no computador agora.

## <a name="import-collection-of-azure-digital-twins-apis"></a>Importar a coleção de APIs de gêmeos digital do Azure

Uma maneira rápida de começar a usar o Azure digital gêmeos no postmaster é importar uma coleção pré-criada de solicitações para as APIs do gêmeos digital do Azure.

### <a name="download-the-collection-file"></a>Baixar o arquivo de coleção

A primeira etapa na importação do conjunto de API é baixar uma coleção. Escolha a guia abaixo para escolher o plano de dados ou o plano de controle para ver as opções de coleção criadas previamente.

# <a name="data-plane"></a>[Plano de dados](#tab/data-plane)

Atualmente, há duas coleções do plano de dados do Azure digital gêmeos disponíveis para sua escolha:
* [**Coleção do Azure digital gêmeos postmaster**](https://github.com/microsoft/azure-digital-twins-postman-samples): essa coleção fornece uma experiência de introdução simples para o gêmeos digital do Azure no postmaster. As solicitações incluem dados de exemplo, para que você possa executá-los com edições mínimas necessárias. Escolha esta coleção se desejar um conjunto fácil de solicitações de API de chave contendo informações de exemplo.
    - Para localizar a coleção, navegue até o link do repositório e abra o arquivo chamado *postman_collection.jsem*.
* **[Azure digital gêmeos data plano Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)**: esse repositório contém o arquivo Swagger completo para o conjunto de API gêmeos do Azure digital, que pode ser baixado e importado para o postmaster como uma coleção. Isso fornecerá um conjunto abrangente de todas as solicitações de API, mas com corpos de dados vazios em vez de dados de exemplo. Escolha esta coleção se você quiser ter acesso a todas as chamadas à API e preencher todos os dados por conta própria.
    - Para localizar a coleção, navegue até o link do repositório e escolha a pasta para a versão de especificação mais recente. A partir daqui, abra o arquivo chamado *digitaltwins.jsem*.

# <a name="control-plane"></a>[Painel de controle](#tab/control-plane)

A coleção disponível atualmente para o plano de controle é o [**Swagger do plano de controle gêmeos digital do Azure**](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Esse repositório contém o arquivo Swagger completo para o conjunto de API do Azure digital gêmeos, que pode ser baixado e importado para o postmaster como uma coleção. Isso fornecerá um conjunto abrangente de todas as solicitações de API.

Para localizar a coleção, navegue até o link do repositório e escolha a pasta para a versão de especificação mais recente. A partir daqui, abra o arquivo chamado *digitaltwins.jsem*.

---

Veja como baixar sua coleção escolhida em seu computador para que você possa importá-la para o postmaster.
1. Use os links acima para abrir o arquivo de coleção no GitHub em seu navegador.
1. Selecione o botão **RAW** para abrir o texto bruto do arquivo.
    :::image type="content" source="media/how-to-use-postman/swagger-raw.png" alt-text="Captura de tela do plano de dados digitaltwins.jsno arquivo no GitHub. Há um realce ao contrário do botão RAW." lightbox="media/how-to-use-postman/swagger-raw.png":::
1. Copie o texto da janela e cole-o em um novo arquivo em seu computador.
1. Salve o arquivo com uma extensão *. JSON* (o nome do arquivo pode ser o que você deseja, desde que você possa se lembrar dele para localizar o arquivo mais tarde).

### <a name="import-the-collection"></a>Importar a coleção

Em seguida, importe a coleção para o postmaster.

1. Na janela principal do postmaster, selecione o botão **importar** .
    :::image type="content" source="media/how-to-use-postman/postman-import-collection.png" alt-text="Captura de tela de uma janela do postmaster aberta recentemente. O botão ' importar ' é realçado." lightbox="media/how-to-use-postman/postman-import-collection.png":::

1. Na janela importar a seguir, selecione **carregar arquivos** e navegue até o arquivo de coleção em seu computador que você criou anteriormente. Selecione Abrir.
1. Selecione o botão **importar** para confirmar.

    :::image type="content" source="media/how-to-use-postman/postman-import-collection-2.png" alt-text="Captura de tela da janela ' importar ' do postmaster, mostrando o arquivo a ser importado como uma coleção e o botão de importação.":::

A coleção recém importada agora pode ser vista em sua exibição principal do postmaster, na guia coleções.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-imported.png" alt-text="Captura de tela da janela principal do postmaster. A coleção recém importada é realçada na guia ' coleções '." lightbox="media/how-to-use-postman/postman-post-collection-imported.png":::

Em seguida, continue na próxima seção para adicionar um token de portador à coleção para autorização e conectá-lo à instância do gêmeos digital do Azure.

### <a name="configure-authorization"></a>Configurar a autorização

Em seguida, edite a coleção que você criou para configurar alguns detalhes de acesso. Realce a coleção que você criou e selecione o ícone **Exibir mais ações** para efetuar pull de um menu. Selecione **Editar**.

:::image type="content" source="media/how-to-use-postman/postman-edit-collection.png" alt-text="Captura de tela do postmaster. O ícone ' Exibir mais ações ' da coleção importada está realçado e ' Editar ' está realçado nas opções." lightbox="media/how-to-use-postman/postman-edit-collection.png":::

Siga estas etapas para adicionar um token de portador à coleção para autorização. É aqui que você usará o **valor de token** que você coletou na seção [obter token de portador](#get-bearer-token) para usá-lo para todas as solicitações de API em sua coleção.

1. Na caixa de diálogo Editar da coleção, verifique se você está na guia **autorização** . 

    :::image type="content" source="media/how-to-use-postman/postman-authorization-imported.png" alt-text="Captura de tela da caixa de diálogo Editar da coleção importada no postmaster, mostrando a guia &quot;autorização&quot;." lightbox="media/how-to-use-postman/postman-authorization-imported.png":::

1. Defina o tipo como **OAuth 2,0**, Cole o token de acesso na caixa token de acesso e selecione **salvar**.

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-imported.png" alt-text="Captura de tela da caixa de diálogo de edição do postmaster para a coleção importada, na guia ' autorização '. O tipo é ' OAuth 2,0 ' e a caixa de token de acesso está realçada." lightbox="media/how-to-use-postman/postman-paste-token-imported.png":::

### <a name="additional-configuration"></a>Configuração adicional

# <a name="data-plane"></a>[Plano de dados](#tab/data-plane)

Se você estiver fazendo uma coleta de [plano de dados](how-to-use-apis-sdks.md#overview-data-plane-apis) , ajude a coleção a conectar-se facilmente aos recursos de gêmeos digitais do Azure definindo algumas **variáveis** fornecidas com as coleções. Quando muitas solicitações em uma coleção exigem o mesmo valor (como o nome do host da instância de gêmeos digital do Azure), você pode armazenar o valor em uma variável que se aplica a cada solicitação na coleção. As coleções que podem ser baixadas para o gêmeos digital do Azure vêm com variáveis criadas previamente que você pode definir no nível da coleção.

1. Ainda na caixa de diálogo Editar para sua coleção, vá para a guia **variáveis** .

1. Use o nome do **host** da instância na seção [*pré-requisitos*](#prerequisites) para definir o campo valor atual da variável relevante. Selecione **Salvar**.

    :::image type="content" source="media/how-to-use-postman/postman-variables-imported.png" alt-text="Captura de tela da caixa de diálogo Editar da coleção importada no postmaster, mostrando a guia ' variáveis '. O campo ' valor atual ' é realçado." lightbox="media/how-to-use-postman/postman-variables-imported.png":::

1. Se sua coleção tiver variáveis adicionais, preencha e salve esses valores também.

Quando tiver concluído as etapas acima, você terminará de configurar a coleção. Você pode fechar a guia edição para a coleção, se desejar.

# <a name="control-plane"></a>[Painel de controle](#tab/control-plane)

Se você estiver fazendo uma coleta de [plano de controle](how-to-use-apis-sdks.md#overview-control-plane-apis) , terá feito tudo o que precisa para configurar a coleção. Você pode fechar a guia edição da coleção, se desejar, e prosseguir para a próxima seção.

--- 

### <a name="explore-requests"></a>Explorar solicitações

Em seguida, explore as solicitações na coleção de API do Azure digital gêmeos. Você pode expandir a coleção para exibir as solicitações criadas previamente (classificadas por categoria de operação). 

Solicitações diferentes exigem informações diferentes sobre sua instância e seus dados. Para ver todas as informações necessárias para criar uma solicitação específica, procure os detalhes da solicitação na [documentação de referência da API REST do Azure digital gêmeos](/rest/api/azure-digitaltwins/).

Você pode editar os detalhes de uma solicitação na coleção de postmaster usando estas etapas:

1. Selecione-o na lista para extrair seus detalhes editáveis. 

1. Preencha valores para as variáveis listadas na guia **params** em **variáveis de caminho**.

    :::image type="content" source="media/how-to-use-postman/postman-request-details-imported.png" alt-text="Captura de tela do postmaster. A coleção é expandida para mostrar uma solicitação. A seção ' variáveis de caminho ' é realçada nos detalhes da solicitação." lightbox="media/how-to-use-postman/postman-request-details-imported.png":::

1. Forneça os **cabeçalhos** necessários ou os detalhes do **corpo** nas respectivas guias.

Depois que todos os detalhes necessários forem fornecidos, você poderá executar a solicitação com o botão **Enviar** .

Você também pode adicionar suas próprias solicitações à coleção, usando o processo descrito na seção [*Adicionar uma solicitação individual*](#add-an-individual-request) abaixo.

## <a name="create-your-own-collection"></a>Criar sua própria coleção

Em vez de importar a coleção existente de todas as APIs do Azure digital gêmeos, você também pode criar sua própria coleção do zero. Em seguida, você pode preenchê-lo com solicitações individuais usando a [documentação de referência da API REST do Azure digital gêmeos](/rest/api/azure-digitaltwins/).

### <a name="create-a-postman-collection"></a>Criar uma coleção de Postman

1. Para criar uma coleção, selecione o botão **novo** na janela principal do postmaster.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Captura de tela da janela principal do postmaster. O botão ' novo ' é realçado." lightbox="media/how-to-use-postman/postman-new.png":::

    Escolha um tipo de **coleção**.

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-2.png" alt-text="Captura de tela da caixa de diálogo ' criar novo ' no postmaster. A opção ' Collection ' é realçada.":::

1. Isso abrirá uma guia para preencher os detalhes da nova coleção. Selecione o ícone de edição ao lado do nome padrão da coleção (**nova coleção**) para substituí-lo por sua própria escolha de nome. 

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-3.png" alt-text="Captura de tela da caixa de diálogo Editar da nova coleção no postmaster. O ícone de edição ao lado do nome ' nova coleção ' é realçado." lightbox="media/how-to-use-postman/postman-new-collection-3.png":::

Em seguida, continue na próxima seção para adicionar um token de portador à coleção para autorização.

### <a name="configure-authorization"></a>Configurar a autorização

Siga estas etapas para adicionar um token de portador à coleção para autorização. É aqui que você usará o **valor de token** que você coletou na seção [obter token de portador](#get-bearer-token) para usá-lo para todas as solicitações de API em sua coleção.

1. Ainda na caixa de diálogo Editar para sua nova coleção, vá para a guia **autorização** .

    :::image type="content" source="media/how-to-use-postman/postman-authorization-custom.png" alt-text="Captura de tela da caixa de diálogo Editar da nova coleção no postmaster, mostrando a guia &quot;autorização&quot;." lightbox="media/how-to-use-postman/postman-authorization-custom.png":::

1. Defina o tipo como **OAuth 2,0**, Cole o token de acesso na caixa token de acesso e selecione **salvar**.

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-custom.png" alt-text="Captura de tela da caixa de diálogo de edição do postmaster para a nova coleção, na guia ' autorização '. O tipo é ' OAuth 2,0 ' e a caixa de token de acesso está realçada." lightbox="media/how-to-use-postman/postman-paste-token-custom.png":::

Quando tiver concluído as etapas acima, você terminará de configurar a coleção. Você pode fechar a guia Editar para a nova coleção, se desejar.

A nova coleção pode ser vista em sua exibição principal do postmaster, na guia coleções.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-custom.png" alt-text="Captura de tela da janela principal do postmaster. A coleção personalizada criada recentemente é realçada na guia ' coleções '." lightbox="media/how-to-use-postman/postman-post-collection-custom.png":::

## <a name="add-an-individual-request"></a>Adicionar uma solicitação individual

Agora que sua coleção está configurada, você pode adicionar suas próprias solicitações às APIs do Azure digital.

1. Para criar uma solicitação, use o botão **novo** novamente.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Captura de tela da janela principal do postmaster. O botão ' novo ' é realçado." lightbox="media/how-to-use-postman/postman-new.png":::

    Escolha um tipo de **solicitação**.

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="Captura de tela da caixa de diálogo ' criar novo ' no postmaster. A opção ' solicitação ' é realçada.":::

1. Essa ação abre a janela salvar solicitação, na qual você pode inserir um nome para a solicitação, fornecer uma descrição opcional e escolher a coleção da qual ela faz parte. Preencha os detalhes e salve a solicitação na coleção que você criou anteriormente.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="Captura de tela da janela ' salvar solicitação ' no postmaster, mostrando os campos descritos. O botão ' salvar no Azure digital gêmeos Collection ' é realçado.":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

Agora você pode exibir sua solicitação na coleção e selecioná-la para extrair seus detalhes editáveis.

:::image type="content" source="media/how-to-use-postman/postman-request-details-custom.png" alt-text="Captura de tela do postmaster. A coleção de gêmeos digital do Azure é expandida para mostrar os detalhes da solicitação." lightbox="media/how-to-use-postman/postman-request-details-custom.png":::

### <a name="set-request-details"></a>Definir detalhes da solicitação

Para fazer uma solicitação de postmaster para uma das APIs do Azure digital gêmeos, você precisará da URL da API e das informações sobre quais detalhes ela exige. Você pode encontrar essas informações na [documentação de referência da API REST do Azure digital gêmeos](/rest/api/azure-digitaltwins/).

Para continuar com uma consulta de exemplo, este artigo usará a API de consulta (e sua [documentação de referência](/rest/api/digital-twins/dataplane/query/querytwins)) para consultar todos os gêmeos digitais em uma instância.

1. Obtenha a URL de solicitação e o tipo da documentação de referência. Para a API de consulta, essa é *a `https://digitaltwins-hostname/query?api-version=2020-10-31` postagem* no momento.
1. No postmaster, defina o tipo para a solicitação e insira a URL da solicitação, preenchendo espaços reservados na URL, conforme necessário. É aqui que você usará o nome do **host** da sua instância na seção [*pré-requisitos*](#prerequisites) .
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="Captura de tela dos detalhes da nova solicitação no postmaster. A URL de consulta da documentação de referência foi preenchida na caixa URL de solicitação." lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. Verifique se os parâmetros mostrados para a solicitação na guia **params** correspondem aos descritos na documentação de referência. Para essa solicitação no postmaster, o `api-version` parâmetro foi preenchido automaticamente quando a URL de solicitação foi inserida na etapa anterior. Para a API de consulta, esse é o único parâmetro necessário, portanto, essa etapa é feita.
1. Na guia **autorização** , defina o tipo para **herdar autenticação do pai**. Isso indica que essa solicitação usará a autorização que você configurou anteriormente para toda a coleção.
1. Verifique se os cabeçalhos mostrados para a solicitação na guia **cabeçalhos** correspondem aos descritos na documentação de referência. Para essa solicitação, vários cabeçalhos foram preenchidos automaticamente. Para a API de consulta, nenhuma das opções de cabeçalho é necessária, portanto, essa etapa é feita.
1. Verifique se o corpo mostrado para a solicitação na guia **corpo** corresponde às necessidades descritas na documentação de referência. Para a API de consulta, um corpo JSON é necessário para fornecer o texto da consulta. Veja um exemplo de corpo para essa solicitação que consulta todos os gêmeos digitais na instância:

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="Captura de tela dos detalhes da nova solicitação no postmaster, na guia corpo. Ele contém um corpo JSON bruto com uma consulta de ' SELECT * FROM DIGITALTWINS '." lightbox="media/how-to-use-postman/postman-request-body.png":::

   Para obter mais informações sobre como criar consultas do Azure digital gêmeos, consulte [*como consultar o grafo de entrelaçamento*](how-to-query-graph.md).

1. Verifique a documentação de referência para todos os outros campos que podem ser necessários para o tipo de solicitação. Para a API de consulta, todos os requisitos agora foram atendidos na solicitação do postmaster, portanto, essa etapa é feita.
1. Use o botão **Enviar** para enviar a solicitação concluída.
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="Captura de tela do postmaster mostrando os detalhes da nova solicitação. O botão enviar é realçado." lightbox="media/how-to-use-postman/postman-request-send.png":::

Depois de enviar a solicitação, os detalhes da resposta serão exibidos na janela do postmaster abaixo da solicitação. Você pode exibir o código de status da resposta e qualquer texto do corpo.

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="Captura de tela da solicitação enviada no postmaster. Abaixo dos detalhes da solicitação, a resposta é mostrada. O status é 200 OK e o corpo mostra os resultados da consulta." lightbox="media/how-to-use-postman/postman-request-response.png":::

Você também pode comparar a resposta aos dados de resposta esperados fornecidos na documentação de referência para verificar o resultado ou saber mais sobre os erros que surgirem.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as APIs do digital gêmeos, leia [*como usar as APIs e SDKs do gêmeos digital do Azure*](how-to-use-apis-sdks.md)ou exiba a [documentação de referência para as APIs REST](/rest/api/azure-digitaltwins/).