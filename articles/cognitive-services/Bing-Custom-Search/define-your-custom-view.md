---
title: Configure sua experiência de Pesquisa Personalizada do Bing | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Descreve como criar um site e serviços de pesquisa vertical
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: c4cb93e39a67e13646a339af6ac999a8fd96b383
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129631"
---
# <a name="configure-your-bing-custom-search-experience"></a>Configure sua experiência de Pesquisa Personalizada do Bing

Uma instância de Pesquisa Personalizada permite adaptar a experiência de pesquisa para incluir conteúdo apenas de sites com os quais os usuários se importam. Em vez de realizar uma pesquisa em toda a Web, o Bing pesquisa apenas a parte da Web de interesse do usuário. Para criar a exibição personalizada da Web, use o [portal](https://customsearch.ai) da Pesquisa Personalizada do Bing.

O portal permite criar uma instância de pesquisa que especifica as fatias da Web: os domínios, as subpáginas e páginas da Web que você quer que o Bing pesquise, e aqueles que você não quer que ele pesquise. O portal também pode sugerir conteúdo que você talvez queira incluir.

Ao definir fatias da web, use o seguinte:

| Nome da fatia | DESCRIÇÃO                                                                                                                                                                                                                                                                                                |
|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Domínio     | Uma fatia de domínio inclui todo o conteúdo localizado em um domínio da Internet. Por exemplo, `www.microsoft.com`. Omitir `www.` faz com que o Bing também pesquise os subdomínios do domínio. Por exemplo, se você especificar `microsoft.com`, o Bing também retorna os resultados de `support.microsoft.com` ou `technet.microsoft.com`. |
| Subpágina    | Uma fatia de subpágina inclui todo o conteúdo localizado na subpágina e nos caminhos abaixo. É possível especificar no máximo duas subpáginas no caminho. Por exemplo, `www.microsoft.com/en-us/windows/`                                                                                                                       |
| Página da web    | Uma fatia da página da Web pode incluir apenas essa página da Web em uma pesquisa personalizada. Opcionalmente, é possível especificar se quer incluir subpáginas.                                                                                                                                                                                  |

> [!IMPORTANT]
> Todos os domínios, subpáginas e páginas da Web que forem especificados devem ser públicos e indexados pelo Bing. Se você possui um site público que deseja incluir na pesquisa e o Bing não o indexou, consulte a [documentação de Webmasters](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) do Bing para obter detalhes sobre como obter o Bing para indexá-lo. Além disso, consulte a documentação do Webmaster para obter detalhes sobre como fazer com que o Bing atualize o site rastreado se o índice estiver desatualizado.

## <a name="add-slices-of-the-web-to-your-custom-search-instance"></a>Adicionar fatias da web à sua instância de pesquisa personalizada

Quando você cria sua instância de pesquisa personalizada, você pode especificar as fatias da web: domínios, subpáginas e páginas da Web, que você deseja incluir ou bloquear de seus resultados de pesquisa. 

Se você conhecer as fatias que quer incluir na instância de pesquisa personalizada, adicione-as à lista **Ativo** da instância. 

Se você não tiver certeza de quais fatias incluir, você pode enviar consultas de pesquisa ao Bing no painel de **visualização** e selecionar as fatias que desejar. Para fazer isso: 

1. Selecione “Bing” na lista suspensa no painel de visualização e insira uma consulta de pesquisa

2. Clique em **Adicionar site** ao lado do resultado que você quer incluir. Em seguida, clique em OK.

>[!NOTE]
> [!INCLUDE[publish or revert](./includes/publish-revert.md)]

<a name="active-and-blocked-lists"></a>

### <a name="customize-your-search-experience-with-active-and-blocked-lists"></a>Personalizar sua experiência de pesquisa com listas Ativo e Bloqueado 

Você pode acessar a lista de fatias ativas e bloqueadas, clicando nas guias **Ativo** e **Bloqueado** em sua instância de pesquisa personalizada. Fatias adicionadas à lista de ativos serão incluídas em sua pesquisa personalizada. Fatias bloqueadas não serão pesquisadas e não aparecerão nos resultados da pesquisa.

Para especificar as fatias da web que você deseja que o Bing pesquise, clique na guia **Ativo** e adicione uma ou mais URLs. Para editar ou excluir URLs, use as opções na coluna **Controles**. 

Ao adicionar URLs à lista **Ativo**, você pode adicionar URLs únicas ou várias URLs simultaneamente carregando um arquivo de texto usando o ícone de upload.

![A guia Ativo da Pesquisa Personalizada do Bing](media/file-upload-icon.png)

Para carregar um arquivo, crie um arquivo de texto e especifique um único domínio, subpágina ou página da Web por linha. O arquivo será rejeitado se ele não estiver formatado corretamente.

> [!NOTE]
> * Você só pode carregar um arquivo para a lista **Ativo**. Você não pode usá-lo para adicionar fatias à lista **Bloqueado**.  
> * Se a lista **Bloqueado** contiver um domínio, uma subpágina ou página da Web que você especificou no arquivo de upload, ele será removido da lista **Bloqueado** e adicionado à lista **Ativo**.
> * Entradas duplicadas no seu arquivo de upload serão ignoradas pela Pesquisa Personalizada do Bing. 

### <a name="get-website-suggestions-for-your-search-experience"></a>Obtenha sugestões de sites para a sua experiência de pesquisa

Depois de adicionar as fatias da web para a lista **Ativo**, o portal de Pesquisa Personalizada do Bing gerará sugestões de sites e subpáginas na parte inferior da guia. Essas são fatias que a Pesquisa Personalizada do Bing acha que você talvez queira incluir. Clique em **Atualizar** para obter sugestões atualizadas depois de atualizar as configurações da sua instância de pesquisa personalizada. Esta seção só é visível se as sugestões estiverem disponíveis.

## <a name="search-for-images-and-videos"></a>Pesquisar imagens e vídeos

Você pode pesquisar imagens e vídeos da mesma forma que o conteúdo da web usando a [API de Pesquisa de Imagem Personalizada do Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference) ou a [API de Pesquisa de Vídeo Personalizada do Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference). Você pode exibir esses resultados com a [interface do usuário hospedada](hosted-ui.md) ou as APIs. 

Essas APIs são semelhantes às APIs de [Pesquisa de Imagem do Bing](../Bing-Image-Search/overview.md) e [Pesquisa de Vídeo do Bing](../Bing-Video-Search/search-the-web.md) não personalizadas, mas pesquisam toda a web e não exigem o parâmetro de consulta `customConfig`. Consulte esses conjuntos de documentação para obter mais informações sobre como trabalhar com imagens e vídeos. 

## <a name="test-your-search-instance-with-the-preview-pane"></a>Testar sua instância de pesquisa com o painel de visualização

É possível testar a instância de pesquisa usando o painel de visualização no lado direito do portal para enviar consultas de pesquisa e exibir os resultados. 

1. Abaixo da caixa de pesquisa, selecione **Minha instância**. Você pode comparar os resultados de sua experiência de pesquisa com o Bing, selecionando **Bing**. 
2. Selecione um filtro de pesquisa seguro e qual mercado pesquisar (consulte [Parâmetros de Consulta](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters)).
3. Insira uma consulta e pressione Enter ou clique no ícone de pesquisa para exibir os resultados da configuração atual. Você pode alterar seu tipo de pesquisa que você executa clicando em **Web**, **Imagem**, ou **Vídeo** para obter resultados correspondentes. 

<a name="adjustrank"></a>

## <a name="adjust-the-rank-of-specific-search-results"></a>Ajustar a classificação dos resultados de pesquisa específica

O portal permite que você ajuste a classificação da pesquisa de conteúdo de domínios, subpáginas e páginas da Web específicos. Depois de enviar uma consulta de pesquisa no painel de visualização, cada resultado de pesquisa contém uma lista de ajustes que você pode fazer para ele:  

|            |                                                                                                                                                                      |
|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Bloco      | Mova o domínio, a subpágina ou a página da Web para a lista Bloqueado. O Bing excluirá o conteúdo do site selecionado para não aparecer nos resultados da pesquisa.                    |
| Aumentar      | Aumenta o conteúdo do domínio ou da subpágina nos resultados da pesquisa.                                                                                        |
| Rebaixar     | Rebaixa o conteúdo do domínio ou subpágina ao nível abaixo nos resultados da pesquisa. Você seleciona se quer rebaixar o conteúdo do domínio ou da subpágina à qual a página da Web pertence. |
| Fixar no início | Move o domínio, a subpágina ou a página da Web para a lista **Fixado**. Isso força que a página da Web sejam exibida como o primeiro resultado de pesquisa para uma determinada consulta de pesquisa.                   |

A classificação de ajuste não está disponível para pesquisas de imagem e vídeo.

### <a name="boosting-and-demoting-search-results"></a>Aumentar e rebaixar os resultados da pesquisa

É possível aumentar, superaumentar ou rebaixar qualquer domínio ou subpágina na lista **Ativo**. Por padrão, todas as fatias são adicionadas sem ajustes de classificação. As fatias da Web que são Superaumentadas ou Aumentadas apresentam uma classificação superior nos resultados da pesquisa (com classificação superaumentada maior que aumentada). Itens rebaixados são classificados abaixo nos resultados da pesquisa.

É possível superaumentar, aumentar ou rebaixar itens, usando os controles de **Ajuste de Classificação** na lista **Ativo** ou usando os controles Aumentar e Rebaixar no painel de visualização. O serviço adiciona a fatia à lista Ativo e ajusta a classificação adequadamente.

> [!NOTE] 
> Aumentar e o rebaixar domínios e subpáginas são um dos muitos métodos que a Pesquisa Personalizada do Bing usa para determinar a ordem dos resultados da pesquisa. Devido a outros fatores que influenciam a classificação de conteúdo da web diferente, os efeitos de ajustar a classificação podem variar. Use o painel de visualização para testar os efeitos de ajustar a classificação dos resultados da pesquisa. 

Superaumentar, aumentar e rebaixar não estão disponíveis para as pesquisas de imagem e vídeo.

## <a name="pin-slices-to-the-top-of-search-results"></a>Fixar fatias na parte superior dos resultados da pesquisa

O portal também permite que você fixe URLs na parte superior dos resultados da pesquisa de termos de pesquisa específicos, usando a guia **Fixado**. Insira uma URL e uma consulta para especificar a página da Web que será exibida como o resultado principal. Observe que você pode fixar no máximo uma página da Web por consulta de pesquisa, e apenas as páginas da Web indexadas serão exibidas em pesquisas. A fixação de resultados não está disponível para pesquisas de imagem e vídeo.

Você pode fixar uma página da Web na parte superior de duas maneiras:

* Na guia  **Fixado** , insira a URL da página da Web a ser fixada na parte superior e a consulta correspondente.

* No painel **Visualização**, insira uma consulta de pesquisa e clique em pesquisar. Localize a página da Web que você deseja fixar para a sua consulta e, em seguida, clique em  **Fixar no topo**. A página da Web e a consulta serão adicionadas à lista **Fixado**.

### <a name="specify-the-pins-match-condition"></a>Especificar a condição de correspondência da fixação

Por padrão, as páginas da Web só estão fixadas na parte superior dos resultados da pesquisa quando a cadeia de caracteres de consulta do usuário corresponde exatamente a um listado na lista **Fixado**. Você pode alterar este comportamento especificando uma das seguintes condições de correspondência:

> [!NOTE]
> Todas as comparações entre a consulta de pesquisa do usuário e a consulta de pesquisa da fixação diferenciam maiúsculas de minúsculas.

| Value | DESCRIÇÃO                                                                          |
|---------------|----------------------------------------------------------------------------------|
| Começa com | A fixação é uma correspondência se a cadeia de caracteres de consulta do usuário começa com a cadeia de caracteres de consulta da fixação |
| Termina com   | A fixação é uma correspondência se a cadeia de caracteres de consulta do usuário termina com a cadeia de caracteres de consulta da fixação.  |
| Contém:    | A fixação é uma correspondência se a cadeia de caracteres de consulta do usuário contém a cadeia de caracteres de consulta da fixação.   |


Para alterar a condição de correspondência da fixação, clique no ícone de edição da fixação. Na coluna **Condição de correspondência de consulta**, clique na lista suspensa e selecione a nova condição a ser usada. Em seguida, clique no ícone de salvar para salvar as alterações.

### <a name="change-the-order-of-your-pinned-sites"></a>Alterar a ordem dos seus sites fixados

Para alterar a ordem de seus pins, você pode arrastá-los e soltá-los, ou editar o número da ordem, clicando no ícone "Editar" na coluna **Controles** da lista **Fixado**.

Se vários pins satisfazem uma condição de correspondência, a Pesquisa Personalizada do Bing usará a mais alta na lista.

## <a name="view-statistics"></a>Exibir estatísticas

Se você inscreveu-se na Pesquisa Personalizada no nível apropriado (consulte as [Páginas de preços](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), uma guia **Estatísticas** será adicionada às instâncias de produção. A guia Estatísticas mostra detalhes sobre como os pontos de extremidade da Pesquisa Personalizada são usados, incluindo volume da chamada, principais consultas, distribuição geográfica, códigos de resposta e pesquisa segura. É possível filtrar detalhes usando os controles fornecidos.

## <a name="usage-guidelines"></a>Diretrizes de uso

- Para cada instância de pesquisa personalizada, o número máximo de ajustes de classificação que você pode fazer nas fatias **Ativo** e **Bloqueado** é limitado a 400.
- Adicionar uma fatia às guias Ativo ou Bloqueado conta como um ajuste de classificação.
- Aumentar e rebaixar contam como dois ajustes na classificação.
- Para cada instância de pesquisa personalizada, o número máximo de fixações que você pode fazer é limitado a 200.

## <a name="next-steps"></a>Próximos passos

- [Chamar sua pesquisa personalizada](./search-your-custom-view.md)
- [Configurar a experiência de interface do usuário hospedada](./hosted-ui.md)
- [Usar marcadores de decoração para realçar texto](./hit-highlighting.md)
- [Paginar páginas da Web](./page-webpages.md)
