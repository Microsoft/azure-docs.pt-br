---
title: Usar o Visualizador de Erros de Desenho do Azure Mapas
description: Neste artigo, você aprenderá como Visualizar avisos e erros retornados pela API de conversão do criador (versão prévia).
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: db88e347e12783205ea8c31fed0bb374fccb4736
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96903574"
---
# <a name="using-the-azure-maps-drawing-error-visualizer-with-creator-preview"></a>Usando o Visualizador de erros de desenho do Azure Maps com o criador (versão prévia)

> [!IMPORTANT]
> Os serviços do Criador do Azure Mapas estão em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


O Visualizador de Erros de Desenho é um aplicativo Web autônomo que exibe [avisos e erros de pacote de desenho](drawing-conversion-error-codes.md) detectados durante o processo de conversão. O aplicativo Web Visualizador de Erros consiste em uma página estática que você pode usar sem se conectar à Internet.  Você pode usar o Visualizador de Erros para corrigir erros e avisos de acordo com os [Requisitos de pacote de desenho](drawing-requirements.md). A [API de conversão do Azure Mapas](/rest/api/maps/conversion) só retornará uma resposta com um link para o Visualizador de Erros quando um erro for detectado.

## <a name="prerequisites"></a>Pré-requisitos

Para poder baixar o Visualizador de Erros de Desenho, você precisará:

1. [Criar uma conta do Azure Mapas](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtenha uma chave de assinatura primária](quick-demo-map-app.md#get-the-primary-key-for-your-account), também conhecida como a chave primária ou a chave de assinatura.
3. [Criar um recurso de Criador (versão prévia)](how-to-manage-creator.md)

Este tutorial usa o aplicativo [Postman](https://www.postman.com/), mas você pode escolher um ambiente de desenvolvimento de API diferente.

## <a name="download"></a>Baixar

1. Carregue seu pacote de desenho no serviço Azure Maps Creator (versão prévia) para obter um `udid` para o pacote carregado. Para obter as etapas sobre como carregar um pacote, consulte [Carregar um pacote de desenho](tutorial-creator-indoor-maps.md#upload-a-drawing-package).

2. Agora que o pacote do Drawing foi carregado, usaremos `udid` para o pacote carregado converter o pacote em dados do mapa. Para obter as etapas sobre como converter um pacote, consulte [Converter um pacote de desenho](tutorial-creator-indoor-maps.md#convert-a-drawing-package).

    >[!NOTE]
    >Se o processo de conversão for executado com sucesso, você não receberá um link para a ferramenta Visualizador de Erros.

3. Na guia de resposta **Cabeçalhos** no aplicativo Postmaster, procure a propriedade `diagnosticPackageLocation`, retornada pela API de Conversão. A resposta deve se assemelhar ao JSON a seguir:

    ```json
    {
        "operationId": "77dc9262-d3b8-4e32-b65d-74d785b53504",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Failed",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {
            "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
        }
    }
    ```

4. Baixe o Visualizador de Erros do Pacote de Desenho fazendo uma solicitação `HTTP-GET` na URL `diagnosticPackageLocation`.

## <a name="setup"></a>Instalação

Dentro do pacote compactado baixado no link `diagnosticPackageLocation`, você encontrará dois arquivos.

* _VisualizationTool. zip:_ Contém o código-fonte, a mídia e a página da Web para o Visualizador de Erros de Desenho.
* _ConversionWarningsAndErrors.json_: Contém uma lista formatada de avisos, erros e detalhes adicionais que são usados pelo Visualizador de Erro de Desenho.

Descompacte a pasta _VisualizationTool.zip_. Ela contém os seguintes arquivos:

* pasta de _ativos_: contém imagens e arquivos de mídia
* pasta _estático_: código-fonte
* arquivo _index. html_: o aplicativo Web.

Abra o arquivo _index.html_ usando qualquer um dos navegadores abaixo, com o respectivo número de versão. Você pode usar uma versão diferente, se a versão oferecer um comportamento igualmente compatível como a versão listada.

* Microsoft Edge 80
* Safari 13
* Chrome 80
* Firefox 74

## <a name="using-the-drawing-error-visualizer-tool"></a>Usando a ferramenta Visualizador de Erros de Desenho do Azure Mapas

Depois de iniciar a ferramenta Visualizador de Erros de Desenho, você verá a página de carregamento. A página de carregamento contém uma caixa de arrastar e soltar. A caixa de arrastar e soltar também funciona como um botão que inicia uma caixa de diálogo do Explorador.

:::image type="content" source="./media/drawing-errors-visualizer/start-page.png" alt-text="Aplicativo Visualizador de Erros de Desenho — Página Inicial":::

O arquivo _ConversionWarningsAndErrors.json_ foi colocado na raiz do diretório baixado. Para carregar o _ConversionWarningsAndErrors.json_ você pode arrastar e soltar o arquivo na caixa ou clicar na caixa, localizar o arquivo na caixa de diálogo do Explorador de Arquivos e, em seguida, carregar o arquivo.

:::image type="content" source="./media/drawing-errors-visualizer/loading-data.gif" alt-text="Aplicativo Visualizador de Erros de Desenho — arrastar e soltar para carregar dados":::

Depois que o arquivo _ConversionWarningsAndErrors.json_ for carregado, você verá uma lista de erros e avisos do pacote de desenho. Cada erro ou aviso é especificado pela camada, nível e uma mensagem detalhada. Para exibir informações detalhadas sobre um erro ou aviso, clique no link **detalhes** . Uma seção inmanejável será exibida abaixo da lista. Agora você pode navegar por cada erro para saber mais detalhes sobre como resolver o erro.

:::image type="content" source="./media/drawing-errors-visualizer/errors.png" alt-text="Aplicativo Visualizador de Erros de Desenho — erros e avisos":::

## <a name="next-steps"></a>Próximas etapas

Depois que o [pacote de desenho atender aos requisitos](drawing-requirements.md), você poderá usar o [serviço do Conjunto de dados do Azure Mapas](/rest/api/maps/conversion) para converter o pacote de desenho em um conjunto de dados. Em seguida, você pode usar o módulo Web do Azure Mapas Interno para desenvolver seu aplicativo. Para saber mais, leia os seguintes artigos:

> [!div class="nextstepaction"]
> [Códigos de erro de conversão de desenho](drawing-conversion-error-codes.md)

> [!div class="nextstepaction"]
> [Criador (visualização) para mapas de interno](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Usar o módulo do Azure Mapas Interno](how-to-use-indoor-module.md)

> [!div class="nextstepaction"]
> [Implementar estilo dinâmico do mapa interno](indoor-map-dynamic-styling.md)