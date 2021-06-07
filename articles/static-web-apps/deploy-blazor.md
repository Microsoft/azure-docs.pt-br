---
title: 'Tutorial: Criando um aplicativo Web estático com o Blazor em Aplicativos Web Estáticos do Azure'
description: Saiba como criar um site dos Aplicativos Web Estáticos do Azure com o Blazor.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: cshoe
ms.openlocfilehash: 0086f7f68fd05d6925d19c7ab457fbc125e36be4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96350221"
---
# <a name="tutorial-building-a-static-web-app-with-blazor-in-azure-static-web-apps"></a>Tutorial: Criando um aplicativo Web estático com o Blazor em Aplicativos Web Estáticos do Azure

Os Aplicativos Web Estáticos do Azure publicam um site em um ambiente de produção por meio da criação de aplicativos por meio de um repositório GitHub. Neste tutorial, você implantará um aplicativo Web nos Aplicativos Web Estáticos do Azure usando o portal do Azure.

Se você não tiver uma assinatura do Azure, [crie uma conta de avaliação gratuita](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Pré-requisitos

- [GitHub](https://github.com)
- Conta do [Azure](https://portal.azure.com)

## <a name="application-overview"></a>Visão geral do aplicativo

Os Aplicativos Web Estáticos do Azure permitem que você crie aplicativos Web estáticos com suporte em um back-end sem servidor. O tutorial a seguir demonstra como implantar um aplicativo Web Blazor C# que retorna dados meteorológicos.

:::image type="content" source="./media/deploy-blazor/blazor-app-complete.png" alt-text="Aplicativo Blazor completo":::

O aplicativo em destaque neste tutorial é composto por três projetos diferentes do Visual Studio:

- **Api**: O aplicativo Azure Functions em C# que implementa o ponto de extremidade de API que fornece informações de clima para o aplicativo estático. **WeatherForecastFunction** retorna uma matriz de `WeatherForecast` objetos.

- **Cliente**: O projeto de assembly da Web do Blazor de front-end. Uma [rota de fallback](#fallback-route) é implementada para garantir que todas as rotas recebam o arquivo _index.html_.

- **Compartilhado**: Contém classes comuns referenciadas por projetos de API e de cliente que permitem o fluxo de dados do ponto de extremidade de API para o aplicativo Web de front-end. A classe [`WeatherForecast`](https://github.com/staticwebdev/blazor-starter/blob/main/Shared/WeatherForecast.cs) é compartilhada entre ambos os aplicativos.

Juntos, esses projetos compõem as partes necessárias para criar um aplicativo de assembly da Web Blazor em execução no navegador com suporte de um back-end de API.

## <a name="fallback-route"></a>Rota de fallback

O aplicativo expõe URLs como _/counter_ e _/fetchdata_ que são mapeados para rotas específicas do aplicativo. Como esse aplicativo é implementado como um aplicativo de página única, cada rota recebe o arquivo _index.html_. Para garantir que a solicitação para qualquer caminho retorne _index.html_ uma [rota de fallback](./routes.md#fallback-routes) é implementada no arquivo _routes.json_ encontrado na pasta _wwwroot_ do projeto do Cliente.

```json
{
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

A configuração acima garante que as solicitações para qualquer rota no aplicativo retornem a página _index.html_.

## <a name="create-a-repository"></a>Criar um repositório

Este artigo usa um repositório de modelos do GitHub para facilitar a introdução. O modelo apresenta um aplicativo inicial implantado em Aplicativos Web Estáticos do Azure.

1. Verifique se você está conectado ao GitHub e procure a seguinte localização para criar um repositório:
    - [https://github.com/staticwebdev/blazor-starter/generate](https://github.com/login?return_to=/staticwebdev/blazor-starter/generate)
1. Dê ao seu repositório o nome **my-first-static-blazor-app**

## <a name="create-a-static-web-app"></a>Criar um aplicativo Web estático

Agora que o repositório foi criado, crie um aplicativo Web estático no portal do Azure.

1. Navegue até o [portal do Azure](https://portal.azure.com)
1. Selecione **Criar um Recurso**
1. Pesquise por **Aplicativos Web Estáticos**
1. Selecione **Aplicativos Web Estáticos (Versão Prévia)**
1. Escolha **Criar**

Na seção _Informações Básicas_, comece configurando seu novo aplicativo e vinculando-o a um repositório GitHub.

:::image type="content" source="media/deploy-blazor/basics.png" alt-text="Guia Básico":::

1. Selecione sua _Assinatura do Azure_.
1. Selecione ou crie um novo _Grupo de Recursos_
1. Dê ao aplicativo o nome **my-first-static-blazor-app**
    - Os caracteres válidos são `a-z` (não diferencia maiúsculas de minúsculas), `0-9` e `-`.
1. Selecione a _Região_ mais próxima de você.
1. Selecione o **SKU** _gratuito_
1. Escolha o botão **Entrar com o GitHub** e autentique-se no GitHub

Depois de entrar no GitHub, insira as informações do repositório.

:::image type="content" source="media/deploy-blazor/repository-details.png" alt-text="Detalhes do repositório":::

1. Selecione sua _Organização_ de preferência.
1. Selecione **my-first-static-blazor-app** na lista suspensa _Repositório_
1. Selecione **principal** na lista suspensa _Ramificação_

    Caso você não veja nenhum repositório, talvez seja necessário autorizar Aplicativos Web Estáticos do Azure no GitHub. Navegue até o repositório do GitHub e vá para **Configurações > Aplicativos > Aplicativos OAuth Autorizados**, selecione **Aplicativos Web Estáticos do Azure** e **Conceder**. Em repositórios corporativos, você precisa ser um proprietário da organização para conceder as permissões.

1. Na seção _Detalhes do Build_, adicione os detalhes da configuração específica do Blazor.

    - Selecione **Blazor** na lista suspensa _Predefinições do Build_ e mantenha todos os valores padrão.

1. Selecione **Examinar + criar**.

    :::image type="content" source="media/deploy-blazor/review-create.png" alt-text="Botão Revisar Criar":::.

1. Selecione **Criar**.

    :::image type="content" source="media/deploy-blazor/create-button.png" alt-text="Botão Criar":::

1. Selecione **Ir para o recurso**.

    :::image type="content" source="media/deploy-blazor/resource-button.png" alt-text="Botão Ir para o recurso":::.

## <a name="view-the-website"></a>Exibir o site

Há dois aspectos na implantação de um aplicativo estático. O primeiro provisiona os recursos subjacentes do Azure que compõem seu aplicativo. O segundo é um fluxo de trabalho do GitHub Actions que cria e publica seu aplicativo.

Antes de navegar até o novo site estático, primeiro a compilação de implantação deve concluir a execução.

A janela de visão geral de Aplicativos Web Estáticos exibe uma série de links que ajudam você a interagir com seu aplicativo Web.

:::image type="content" source="./media/deploy-blazor/overview-window.png" alt-text="Janela de visão geral":::

1. Ao clicar na barra de notificação que indica _Clique aqui para verificar o status das suas execuções do GitHub Actions_, você será levado ao GitHub Actions em execução no repositório. Quando você verificar se o trabalho de implantação foi concluído, poderá navegar para seu site por meio da URL gerada.

2. Depois que o fluxo de trabalho do GitHub Actions for concluído, selecione o link _URL_ para abrir o site na nova guia.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não quiser continuar usando esse aplicativo, poderá excluir a instância do Aplicativo Web Estático do Azure com as seguintes etapas:

1. Abra o [portal do Azure](https://portal.azure.com)
1. Pesquise **my-first-static-blazor-app** na barra de pesquisa superior
1. Selecione o nome do aplicativo
1. Escolha o botão **Excluir**
1. Selecione **Sim** para confirmar a ação de exclusão

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Autenticação e autorização](./authentication-authorization.md)
