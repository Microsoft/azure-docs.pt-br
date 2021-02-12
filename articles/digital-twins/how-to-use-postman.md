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
ms.openlocfilehash: 96872a067d2414254aec3f212542dadabdf2bd6f
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791733"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>Como usar o postmaster para enviar solicitações para as APIs do Azure digital gêmeos

O [postmaster](https://www.getpostman.com/) é uma ferramenta de teste REST que fornece funcionalidades de solicitação HTTP de chave em um desktop e GUI baseada em plug-in. Você pode usá-lo para criar solicitações HTTP e enviá-las para as [APIs REST do Azure digital gêmeos](how-to-use-apis-sdks.md).

Este artigo descreve como configurar o [cliente REST do postmaster](https://www.getpostman.com/) para interagir com as APIs do gêmeos digital do Azure, por meio das seguintes etapas:

1. Use o [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) para obter um token de portador que será usado para fazer solicitações de API no postmaster.
1. Configure uma coleção do postmaster e configure o cliente REST do postmaster para usar o token de portador para autenticar.
1. Use o postmaster configurado para criar e enviar uma solicitação para as APIs do Azure digital gêmeos.

## <a name="prerequisites"></a>Pré-requisitos

Para continuar usando o postmaster para acessar as APIs do gêmeos digital do Azure, você precisa configurar uma instância do gêmeos digital do Azure e baixar o postmaster. O restante desta seção orientará você nessas etapas.

### <a name="set-up-azure-digital-twins-instance"></a>Configurar instância dos Gêmeos Digitais do Azure

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>Baixar o postmaster

Em seguida, baixe a versão da área de trabalho do cliente do postmaster. Navegue até [*www.getpostman.com/apps*](https://www.getpostman.com/apps) e siga os prompts para baixar o aplicativo.

## <a name="get-bearer-token"></a>Obter token de portador

Agora que você configurou o postmaster e sua instância do gêmeos digital do Azure, você precisará obter um token de portador que as solicitações do postmaster podem usar para autorizar nas APIs do gêmeos digital do Azure.

Há várias maneiras possíveis de obter esse token. Este artigo usa o [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) para entrar em sua conta do Azure e obter um token dessa maneira.

Se você tiver um CLI do Azure [instalado localmente](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true), poderá iniciar um prompt de comando em seu computador para executar os comandos a seguir.
Caso contrário, você pode abrir uma janela de [Azure cloud Shell](https://shell.azure.com) no navegador e executar os comandos.

1. Primeiro, verifique se você está conectado ao Azure com as credenciais apropriadas, executando este comando:

    ```azurecli-interactive
    az login
    ```

1. Em seguida, use o comando [AZ Account Get-Access-token](/cli/azure/account?preserve-view=true&view=azure-cli-latest#az_account_get_access_token) para obter um token de portador com acesso ao serviço de gêmeos digital do Azure.

    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

1. Copie o valor de `accessToken` no resultado e salve-o para usar na próxima seção. Esse é o **valor do token** que você fornecerá ao postmaster para autenticar suas solicitações.

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="Exibição de uma janela de console local mostrando o resultado do comando AZ Account Get-Access-token. Um dos campos no resultado é chamado de accessToken e seu valor de exemplo--começando com ey--é realçado.":::

>[!TIP]
>Esse token é válido por pelo menos cinco minutos e um máximo de 60 minutos. Se você ficar sem tempo alocado para o token atual, poderá repetir as etapas nesta seção para obter um novo.

## <a name="set-up-postman-collection-and-authorization"></a>Configurar a coleta e a autorização do postmaster

Em seguida, configure o postmaster para fazer solicitações de API.
Essas etapas acontecem no aplicativo local do seu postmaster, portanto, vá em frente e abra o aplicativo do postmaster em seu computador.

### <a name="create-a-postman-collection"></a>Criar uma coleção de Postman

As solicitações no postmaster são salvas em **coleções** (grupos de solicitações). Ao criar uma coleção para agrupar suas solicitações, você pode aplicar configurações comuns a várias solicitações de uma só vez. Isso pode simplificar muito a autorização se você planeja criar mais de uma solicitação em relação às APIs do Azure digital gêmeos, pois você só precisa configurar a autenticação uma vez para toda a coleção.

1. Para criar uma coleção, clique no botão *+ nova coleção* .

    :::image type="content" source="media/how-to-use-postman/postman-new-collection.png" alt-text="Exibição de uma janela do postmaster aberta recentemente. O botão ' nova coleção ' é realçado":::

1. Na janela *criar uma nova coleção* que segue, forneça um **nome** e uma **Descrição** opcional para sua coleção.

Em seguida, continue na próxima seção para adicionar um token de portador à coleção para autorização.

### <a name="add-authorization-token-and-finish-collection"></a>Adicionar token de autorização e concluir coleta

1. Na caixa de diálogo *criar uma nova coleção* , vá para a guia *autorização* . É aqui que você colocará o **valor do token** coletado na seção [obter token de portador](#get-bearer-token) para usá-lo para todas as solicitações de API em sua coleção.

    :::image type="content" source="media/how-to-use-postman/postman-authorization.png" alt-text="A janela &quot;criar uma nova coleção&quot; do postmaster, mostrando a guia &quot;autorização&quot;.":::

1. Defina o *tipo* como _**OAuth 2,0**_ e cole o token de acesso na caixa *token de acesso* .

    :::image type="content" source="media/how-to-use-postman/postman-paste-token.png" alt-text="A janela &quot;criar uma nova coleção&quot; do postmaster, mostrando a guia &quot;autorização&quot;. Um tipo de ' OAuth 2,0 ' está selecionado e a caixa de token de acesso em que o valor do token de acesso pode ser colado é realçada.":::

1. Depois de colar no token de portador, clique em *criar* para concluir a criação de sua coleção.

Agora, sua nova coleção pode ser vista em sua exibição principal do postmaster, em *coleções*.

:::image type="content" source="media/how-to-use-postman/postman-post-collection.png" alt-text="Exibição da janela principal do postmaster. A coleção recém-criada é realçada na guia ' coleções '.":::

## <a name="create-a-request"></a>Criar uma solicitação

Depois de concluir as etapas anteriores, você poderá criar solicitações para as APIs do Azure digital.

1. Para criar uma solicitação, clique no botão *+ novo* .

    :::image type="content" source="media/how-to-use-postman/postman-new-request.png" alt-text="Exibição da janela principal do postmaster. O botão ' novo ' está realçado":::

1. Escolha *solicitação*.

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="Exibição das opções que você pode selecionar para criar algo novo. A opção ' solicitação ' está realçada":::

1. Essa ação abre a janela *salvar solicitação* , na qual você pode inserir um nome para a solicitação, fornecer uma descrição opcional e escolher a coleção da qual ela faz parte. Preencha os detalhes e salve a solicitação na coleção que você criou anteriormente.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="Exibição da janela ' salvar solicitação ', na qual você pode preencher os campos descritos. O botão ' salvar no Azure digital gêmeos Collection ' está realçado":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

Agora você pode exibir sua solicitação na coleção e selecioná-la para extrair seus detalhes editáveis.

:::image type="content" source="media/how-to-use-postman/postman-request-details.png" alt-text="Exibição da janela principal do postmaster. A coleção de gêmeos digital do Azure é expandida e a solicitação ' Query gêmeos ' é realçada. Os detalhes da solicitação são mostrados no centro da página." lightbox="media/how-to-use-postman/postman-request-details.png":::

### <a name="set-request-details"></a>Definir detalhes da solicitação

Para fazer uma solicitação de postmaster para uma das APIs do Azure digital gêmeos, você precisará da URL da API e das informações sobre quais detalhes ela exige. Você pode encontrar essas informações na [documentação de referência da API REST do Azure digital gêmeos](/rest/api/azure-digitaltwins/).

Para continuar com uma consulta de exemplo, este artigo usará a API de consulta (e sua [documentação de referência](/rest/api/digital-twins/dataplane/query/querytwins)) para consultar todos os gêmeos digitais em uma instância.

1. Obtenha a URL de solicitação e o tipo da documentação de referência. Para a API de consulta, essa é *a `https://digitaltwins-hostname/query?api-version=2020-10-31` postagem* no momento.
1. No postmaster, defina o tipo para a solicitação e insira a URL da solicitação, preenchendo espaços reservados na URL, conforme necessário. É aqui que você usará o nome do **host** da sua instância na seção [*pré-requisitos*](#prerequisites) .
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="Nos detalhes da nova solicitação, a URL de consulta da documentação de referência foi preenchida na caixa URL de solicitação." lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. Verifique se os parâmetros mostrados para a solicitação na guia *params* correspondem aos descritos na documentação de referência. Para essa solicitação no postmaster, o `api-version` parâmetro foi preenchido automaticamente quando a URL de solicitação foi inserida na etapa anterior. Para a API de consulta, esse é o único parâmetro necessário, portanto, essa etapa é feita.
1. Na guia *autorização* , defina o *tipo* para *herdar autenticação do pai*. Isso indica que essa solicitação usará a autenticação que você configurou anteriormente para toda a coleção.
1. Verifique se os cabeçalhos mostrados para a solicitação na guia *cabeçalhos* correspondem aos descritos na documentação de referência. Para essa solicitação, vários cabeçalhos foram preenchidos automaticamente. Para a API de consulta, nenhuma das opções de cabeçalho é necessária, portanto, essa etapa é feita.
1. Verifique se o corpo mostrado para a solicitação na guia *corpo* corresponde às necessidades descritas na documentação de referência. Para a API de consulta, um corpo JSON é necessário para fornecer o texto da consulta. Veja um exemplo de corpo para essa solicitação que consulta todos os gêmeos digitais na instância:

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="Nos detalhes da nova solicitação, a guia corpo é mostrada. Ele contém um corpo JSON bruto com uma consulta de ' SELECT * FROM DIGITALTWINS '."lightbox="media/how-to-use-postman/postman-request-body.png":::

   Para obter mais informações sobre como criar consultas do Azure digital gêmeos, consulte [*como consultar o grafo de entrelaçamento*](how-to-query-graph.md).

1. Verifique a documentação de referência para todos os outros campos que podem ser necessários para o tipo de solicitação. Para a API de consulta, todos os requisitos agora foram atendidos na solicitação do postmaster, portanto, essa etapa é feita.
1. Use o botão *Enviar* para enviar a solicitação concluída.
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="Próximo aos detalhes da nova solicitação, o botão enviar é realçado." lightbox="media/how-to-use-postman/postman-request-send.png":::

Depois de enviar a solicitação, os detalhes da resposta serão exibidos na janela do postmaster abaixo da solicitação. Você pode exibir o código de status da resposta e qualquer texto do corpo.

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="Abaixo dos detalhes da solicitação enviada, os detalhes da resposta são realçados. Há um status de 200 OK e o texto do corpo que descreve as gêmeos digitais que foram retornadas pela consulta." lightbox="media/how-to-use-postman/postman-request-response.png":::

Você também pode comparar a resposta aos dados de resposta esperados fornecidos na documentação de referência para verificar o resultado ou saber mais sobre os erros que surgirem.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as APIs do digital gêmeos, leia [*como usar as APIs e SDKs do gêmeos digital do Azure*](how-to-use-apis-sdks.md)ou exiba a [documentação de referência para as APIs REST](/rest/api/azure-digitaltwins/).