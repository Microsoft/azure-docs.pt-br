---
title: 'Início Rápido: Pesquisa de Mapa Interativo com o Azure Mapas'
description: 'Início Rápido: Saiba como criar mapas interativos e pesquisáveis. Veja como criar uma conta do Azure Mapas, obter uma chave primária e usar o SDK da Web para configurar aplicativos de mapa'
author: anastasia-ms
ms.author: v-stharr
ms.date: 7/10/2020
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 24a834c87fe34d90dec5961bb3f8d376c6e5e62d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373208"
---
# <a name="quickstart-create-an-interactive-search-map-with-azure-maps"></a>Início Rápido: Criar um mapa de pesquisa interativo com o Azure Mapas

Este artigo mostra como usar o Azure Mapas para criar um mapa que fornece aos usuários uma experiência de pesquisa interativa. Ele apresenta e explica as seguintes etapas básicas:

* Criar sua conta do Azure Mapas.
* Obter a chave primária a ser usada no aplicativo Web de demonstração.
* Baixe e abra o aplicativo de mapa de demonstração.

Este guia de início rápido usa o SDK da Web do Azure Mapas, no entanto, os serviços do Azure Mapas podem ser usados com qualquer controle de mapeamento. [Aqui](open-source-projects.md#third-part-map-control-plugins) estão alguns controles de mapa de software livre populares para os quais a equipe do Azure Mapas criou plug-ins.

## <a name="prerequisites"></a>Pré-requisitos

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Entre no [portal do Azure](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-azure-maps-account"></a>Criar uma conta dos Mapas do Azure

Crie uma conta do Azure Mapas seguindo as etapas abaixo:

1. No canto superior esquerdo do [portal do Azure](https://portal.azure.com), clique em **Criar um recurso**.
2. Na caixa *Pesquisar no Marketplace*, digite **Azure Mapas**.
3. Em *Resultados*, selecione **Azure Mapas**. Clique no botão **Criar** que aparece abaixo do mapa.
4. Na página **Criar Conta dos Mapas**, insira os seguintes valores:
    * A *Assinatura* que você deseja usar para a conta.
    * O nome do *Grupo de recursos* para a conta. Você pode optar por *Criar novo* ou *Usar existente* em relação ao grupo de recursos.
    * O *Nome* da sua nova conta.
    * A *Camada de preços* para essa conta.
    * Leia a *Política de Privacidade* e de *Licença* e marque a caixa de seleção para aceitar os termos.
    * Selecione o botão **Criar**.

    :::image type="content" source="./media/quick-demo-map-app/create-account.png" alt-text="Criar a conta do Mapas no portal":::

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Obter a chave primária de sua conta

Depois de criar com êxito sua conta dos Mapas, recupere a chave primária que permite consultar as APIs dos Mapas.

1. Abra a sua conta dos Mapas no portal.
2. Na seção de configurações, selecione **Autenticação**.
3. Copie a **Chave Primária** para sua área de transferência. Salve-a localmente para usar depois neste tutorial.

>[!NOTE]
> Se você usar a chave de assinatura em vez da chave primária, seu mapa não será renderizado corretamente. Além disso, para fins de segurança, é recomendável que você faça a rotação entre as chaves primária e secundária. Para fazer a rotação das chaves, atualize seu aplicativo para usar a chave secundária, implante, em seguida, pressione o botão ciclo/atualizar ao lado da chave primária para gerar uma nova chave primária. A chave primária antiga será desabilitada. Para obter mais informações sobre a rotação de chaves, confira [Configurar o Azure Key Vault com a rotação de chaves e auditoria](../key-vault/secrets/tutorial-rotation-dual.md)

:::image type="content" source="./media/quick-demo-map-app/get-key.png" alt-text="Obter a chave primária do Azure Mapas no portal do Azure":::

## <a name="download-the-demo-application"></a>Baixar aplicativo de demonstração

1. Acesse [interactiveSearch.html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html). Copie o conteúdo do arquivo.
2. Salve o conteúdo deste arquivo localmente como **AzureMapDemo.html**. Abra-o em um editor de texto.
3. Pesquise pela cadeia de caracteres `<Your Azure Maps Key>`. Substitua-o pelo valor **Chave primária** da seção anterior.

## <a name="open-the-demo-application"></a>Abrir o aplicativo de demonstração

1. Abra o arquivo **AzureMapDemo.html** em um navegador de sua escolha.
2. Observe o mapa mostrado da Cidade de Los Angeles. Amplie e reduza para ver como o mapa renderiza automaticamente com mais ou menos informações, dependendo do nível de zoom.
3. Altere o centro padrão do mapa. No arquivo **AzureMapDemo.html**, procure a variável chamada **center**. Substitua o par de valores de latitude e longitude dessa variável pelos novos valores **[-74,0060; 40,7128]** . Salve o arquivo e atualize seu navegador.
4. Teste a experiência de pesquisa interativa. Na caixa de pesquisa no canto superior esquerdo do aplicativo Web da demonstração, pesquise **restaurantes**.
5. Mova o mouse sobre a lista de endereços e locais que aparecem abaixo da caixa de pesquisa. Observe como o marcador correspondente no mapa mostra informações sobre esse local. Para preservar a privacidade de empresas particulares, são mostrados endereços e nomes fictícios.

    :::image type="content" source="./media/quick-demo-map-app/interactive-search.png" alt-text="Aplicativo Web de pesquisa no mapa interativo":::


## <a name="clean-up-resources"></a>Limpar os recursos

>[!WARNING]
>Os tutorais listados na seção [Próximas Etapas](#next-steps) detalham como usar e configurar o Azure Mapas com sua conta. Se você planeja continuar com os tutoriais, não limpe os recursos criados neste início rápido.

Se você não planeja continuar com os tutorais, siga estas etapas para limpar os recursos:

1. Feche o navegador que está executando o aplicativo Web **AzureMapDemo.html**.
2. Navegue até a página do portal do Azure. Selecione **Todos os recursos** na página principal do portal. Ou clique no ícone de menu no canto superior esquerdo. Selecione **Todos os recursos**.
3. Clique na sua conta do Azure Mapas. Na parte superior da página, clique em **Excluir**.

Para obter mais exemplos de código e uma experiência interativa de codificação, consulte estes guias:

[Localizar um endereço usando o serviço de pesquisa do Azure Mapas](how-to-search-for-address.md)

[Usar o Controle de Mapeamento do Azure Mapas](how-to-use-map-control.md)

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou sua conta do Azure Mapas e um aplicativo de demonstração. Confira os seguintes tutoriais para saber mais sobre o Azure Mapas:

> [!div class="nextstepaction"]
> [Pesquisar pontos de interesse próximos usando Azure Mapas](tutorial-search-location.md)