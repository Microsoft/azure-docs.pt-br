---
title: Configurar uma interface do usuário hospedada para API de Pesquisa Personalizada do Bing | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Use este artigo para configurar e integrar uma interface do usuário hospedada para Pesquisa Personalizada do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: ae073e10331f07d9863da1d4ed97533f95b87c86
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405055"
---
# <a name="configure-your-hosted-ui-experience"></a>Configurar a experiência de interface do usuário hospedada

A Pesquisa Personalizada do Bing oferece uma interface de usuário hospedada que pode ser facilmente integrada aos seus aplicativos e páginas da Web como um trecho de código JavaScript. Usando o portal de Pesquisa Personalizada do Bing, configure o layout, a cor e as opções de pesquisa da interface do usuário.



## <a name="configure-the-custom-hosted-ui"></a>Configure a interface de usuário hospedada e personalizada

Para configurar uma interface do usuário hospedada para os aplicativos Web, siga essas etapas. Ao fazer alterações, o painel à direita lhe fornecerá uma visualização da sua interface do usuário. Os resultados da pesquisa exibidos não são os resultados reais da instância.

1. Entre no [portal](https://customsearch.ai) da Pesquisa Personalizada do Bing.  
  
2. Selecione a sua instância de Pesquisa Personalizada do Bing.

3. Clique na guia **Interface do Usuário Hospedada**.  
  
4. Selecione um layout.

    |  |  |
    |---------|---------|
    |Barra de pesquisa e resultados (padrão)    | Exibe uma caixa de pesquisa com os resultados da pesquisa abaixo dela.         |
    |Somente os resultados     | Exibe apenas os resultados da pesquisa sem uma caixa de pesquisa. Ao usar esse layout, forneça a consulta de pesquisa (`&q=<query string>`). Adicione o parâmetro de consulta para a URL solicitada no trecho de JavaScript ou o link de ponto de extremidade de HTML.        |
    |Pop-over     | Fornece uma caixa de pesquisa e exibe os resultados da pesquisa em uma sobreposição deslizante.        |
    
5. Selecione um tema de cor. Personalize as cores de acordo com seu aplicativo clicando em **Personalizar tema**. Para alterar uma cor, insira o valor RGB HEX da cor (por exemplo, `#366eb8`) ou clique na caixa de texto correspondente.

   Você pode visualizar as alterações no lado direito do portal. Ao clicar em **Redefinir para padrão**, suas escolhas voltarão para as cores padrão no tema selecionado.

   > [!NOTE]
   > Considere a acessibilidade ao escolher as cores.

6. Em **Configurações adicionais**, forneça os valores conforme apropriado para o seu aplicativo. Essas configurações são opcionais. Para ver o efeito da aplicação ou remoção delas, veja o painel de visualização à direita. Há três opções de configuração disponíveis:  

7. Insira a chave de assinatura da pesquisa ou escolha uma na lista suspensa. A lista suspensa é preenchida com chaves de assinaturas da sua conta do Azure. Confira [Conta da API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).  

8. Se você habilitou a sugestão automática, insira a chave de assinatura de sugestão automática ou escolha uma na lista suspensa. A lista suspensa é preenchida com chaves de assinaturas da sua conta do Azure. A Sugestão Automática Personalizada exige um preço de assinatura específico, consulte o [preço](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Consumir interface do usuário personalizada

Para consumir a interface de usuário hospedada: 

- Inclua o script em sua página da Web  
  
  ```html
  <html>
      <body>
          <script type="text/javascript" 
              id="bcs_js_snippet"
              src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
          </script>
      </body>    
  </html>
  ```

- Outra opção é usar a seguinte URL em um navegador da Web.   
  
  `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`  
  
  > [!NOTE]
  > Adicione os seguintes parâmetros de consulta à URL conforme necessário. Para obter informações sobre esses parâmetros, consulte a referência [API de Pesquisa Personalizada](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters).
  >
  > - q
  > - mkt
  > - safesearch
  > - setlang

  > [!IMPORTANT]
  > A página não pode exibir a política de privacidade ou outros avisos e termos. A adequação para o seu uso pode variar.  

Para obter informações adicionais, incluindo a ID de Configuração Personalizada, acesse **Pontos de extremidade** na guia **Produção**.

## <a name="configuration-options"></a>Opções de configuração

Configure o comportamento de sua interface do usuário hospedada ao clicar em **Configurações adicionais**e fornecer os valores. Essas configurações são opcionais. Para ver o efeito da aplicação ou remoção delas, veja o painel de visualização à direita. 

### <a name="web-search-configurations"></a>Configurações de pesquisa da Web

|  |  |
|---------|---------|
|Resultados da Web ativados    | Determina se a pesquisa na Web está habilitada (você verá uma guia da Web na parte superior da página)        |
|Sugestão Automática ativada     | Determina se a sugestão automática personalizada está habilitada (consulte [preço](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) para ver o custo adicional).        |
|Resultados por página da Web    | Número de resultados da pesquisa na Web a exibir de cada vez (o máximo é de 50 resultados por página).        |
|Legenda da imagem   | Determina se as imagens são exibidas com os resultados da pesquisa.|


As configurações a seguir serão mostradas se você clicar em **Mostrar configurações avançadas**:


|  | |
|---------|---------|
|Realçar palavras     | Determina se os resultados são exibidos com os termos de pesquisa em negrito.         |
|Destino do link    |  Determina se a página da Web será aberta em uma nova guia do navegador (em branco) ou na mesma guia do navegador (própria) quando o usuário clicar em um resultado de pesquisa.        |

### <a name="image-search-configurations"></a>Configurações de pesquisa de imagem

| | |
|---------|---------|
|Resultados de imagem ativados     | Determina se a pesquisa de imagem está habilitada (você verá uma guia imagens na parte superior da página).            |
|Resultados de imagens por página     | Número de resultados da pesquisa de imagem a exibir de cada vez (o máximo é de 150 resultados por página).          |

A configuração a seguir será mostrada se você clicar em **Mostrar configurações avançadas**.  
  
| | |
|---------|---------|
| Habilitar filtros     | Adiciona filtros que o usuário pode usar para filtrar as imagens que o Bing retorna. Por exemplo, o usuário pode filtrar os resultados somente para GIFs animados.|

### <a name="video-search-configurations"></a>Configurações de pesquisa de vídeo

|  | |
|---------|---------|
|Resultados de vídeo ativados     | Determina se a pesquisa de vídeo está habilitada (você verá uma guia de vídeos na parte superior da página).           |
|Resultados de vídeo por página   | Número de resultados da pesquisa de imagem a exibir de cada vez (o máximo é de 150 resultados por página).        |

A configuração a seguir será mostrada se você clicar em **Mostrar configurações avançadas**.  
  
|  | |
|---------|---------|
|Habilitar filtros    | Adiciona filtros que o usuário pode usar para filtrar as imagens que o Bing retorna. Por exemplo, o usuário pode filtrar os resultados para vídeos com uma determinada resolução ou vídeos descobertos nas últimas 24 horas.          |

### <a name="miscellaneous-configurations"></a>Configurações diversas


| |  |
|---------|---------|
|Título da página   | O texto exibido na área de título da página de resultados da pesquisa (não para o layout de pop-over).        |
|Tema da barra de ferramentas    | Determina a cor da tela de fundo da área de título da página de resultados da pesquisa. |

As configurações a seguir serão mostradas se você clicar em **Mostrar configurações avançadas**.  

|Coluna1  |Coluna2  |
|---------|---------|
|Espaço reservado para texto de caixa de pesquisa   | Texto exibido na caixa de pesquisa antes da entrada.        |
|URL do link de título    |Destino para o link do título.         |
|URL do logotipo     | Imagem exibida ao lado do título.         |
|Ícone favorito    | Ícone exibido na barra de título do navegador.          |

As configurações a seguir serão aplicáveis somente se você consumir a interface do usuário hospedada pelo ponto de extremidade HTML (não serão aplicáveis se você usar o snippet de JavaScript).

- Título da página
- Tema da barra de ferramentas
- URL do link de título
- URL do logotipo
- URL do Favicon  

## <a name="next-steps"></a>Próximas etapas

- [Usar marcadores de decoração para realçar texto](./hit-highlighting.md)
- [Paginar páginas da Web](./page-webpages.md)
