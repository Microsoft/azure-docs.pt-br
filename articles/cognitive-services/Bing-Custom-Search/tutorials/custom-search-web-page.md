---
title: 'Tutorial: Criar uma página da Web de pesquisa personalizada – Pesquisa Personalizada do Bing'
titleSuffix: Azure Cognitive Services
description: Descreve como configurar uma instância de pesquisa personalizada e integrá-la a uma página da Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 3a8d6a831adf24212191a103dcf356bbe74d7962
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405220"
---
# <a name="tutorial-build-a-custom-search-web-page"></a>Tutorial: Compilar uma página da Web de pesquisa personalizada

A Pesquisa Personalizada do Bing permite que você crie experiências de pesquisa personalizada para os tópicos importantes para você. Por exemplo, se você possuir um site de artes marciais que fornece uma experiência de pesquisa, será possível especificar os domínios, sites e as páginas da Web que o Bing pesquisará. Os usuários veem os resultados da pesquisa personalizados ao conteúdo importante para eles, em vez de terem que acessar os resultados gerais da pesquisa que podem conter conteúdo irrelevante. 

Este tutorial demonstra como configurar uma instância de pesquisa personalizada e integrá-la a uma nova página da web.

As tarefas abordadas são:

> [!div class="checklist"]
> - Criar uma instância de pesquisa personalizada
> - Adicionar entradas ativas
> - Adicionar entradas bloqueadas
> - Adicionar entradas fixadas
> - Integrar pesquisa personalizada em uma página da Web

## <a name="prerequisites"></a>Pré-requisitos

- Para acompanhar o tutorial, você precisa de uma chave de assinatura para a API de Pesquisa Personalizada do Bing.  Para obter uma chave, confira [Experimentar os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
- Se você ainda não tem o Visual Studio 2017 ou posterior instalado, poderá baixar e usar o [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) **gratuito**.

## <a name="create-a-custom-search-instance"></a>Criar uma instância de pesquisa personalizada

Para criar uma instância de Pesquisa Personalizada do Bing:

1. Abra um navegador da Internet.  
  
2. Navegue até o [portal](https://customsearch.ai) da pesquisa personalizada.  
  
3. Entre no portal usando uma conta Microsoft (MSA). Se você não tiver uma MSA, clique em **Criar uma conta Microsoft**. Se for a primeira vez que estiver acessando o portal, serão solicitadas permissões para acessar os dados. Clique em **Sim**.  
  
4. Após entrar, clique em **Nova pesquisa personalizada**. Na janela **Criar uma nova instância de pesquisa personalizada**, insira um nome que seja significativo e descreva o tipo de conteúdo que a pesquisa retornará. É possível alterar o nome a qualquer momento.  
  
   ![Captura de tela da caixa Criar uma nova instância de pesquisa personalizada](../media/newCustomSrch.png)  
  
5. Clique em OK, especifique um URL e se deseja incluir subpáginas do URL.  
  
   ![Página de definição de captura de tela de URL](../media/newCustomSrch1-a.png)  


## <a name="add-active-entries"></a>Adicionar entradas ativas

Para incluir resultados de websites ou URLs específicos, adicione-os à guia **Ativo**.

1.  Na página **Configuração**, clique na guia **Ativo** e insira o URL de um ou mais sites que você deseja incluir em sua pesquisa.

    ![Captura de tela da guia ativa do Editor de definição](../media/customSrchEditor.png)

2.  Para confirmar que a sua instância retorna resultados, digite uma consulta no painel de visualização à direita. O Bing retorna apenas resultados para sites públicos indexados.

## <a name="add-blocked-entries"></a>Adicionar entradas bloqueadas

Para excluir resultados de websites ou URLs específicos, adicione-os à guia **Bloqueados**.

1. Sobre o **configuração** , clique no **bloqueado** guia e digite a URL de um ou mais sites que você deseja excluir da sua pesquisa.

    ![Captura de tela da guia Editor de definição bloqueado](../media/blockedCustomSrch.png)


2. Para confirmar que sua instância não retorna resultados de sites bloqueados, insira uma consulta no painel de visualização no lado direito. 

## <a name="add-pinned-entries"></a>Adicionar entradas fixadas

Para fixar uma página da Web específica na parte superior dos resultados da pesquisa, adicione a página da Web e o termo de consulta à guia **Fixado**. A guia **Fixado** contém uma lista de pares de termos de páginas da Web e consultas que especificam a página da Web que aparece como o resultado principal de uma consulta específica. A página da web só é fixada se a cadeia de caracteres de consulta do usuário corresponder à cadeia de caracteres de consulta do pin com base na condição de correspondência do pin. Somente páginas da Web indexadas serão exibidas nas pesquisas. Confira [Definir o modo de exibição personalizado](../define-your-custom-view.md#pin-slices-to-the-top-of-search-results) para obter mais informações.

1. Na página **Configuração**, clique na guia **Fixado** e insira a página da web e o termo de consulta da página da Web que você deseja que sejam retornados como o resultado principal.  
  
2. Por padrão, a cadeia de caracteres de consulta do usuário deve corresponder exatamente à cadeia de caracteres de consulta do seu pin para que o Bing retorne a página como o resultado principal. Para alterar a condição de correspondência, edite o pin (clique no ícone de lápis), clique em exata na **condição de correspondência de consulta** coluna e selecione a condição de correspondência é adequada para seu aplicativo.  
  
    ![Captura de tela da guia fixa do Editor de Definição](../media/pinnedCustomSrch.png)
  
3. Para confirmar que a instância retorna a página da Web especificada como o resultado principal, insira o termo da consulta que você fixou no painel de visualização à direita.

## <a name="configure-hosted-ui"></a>Configurar a interface do usuário hospedada

A Pesquisa personalizada fornece uma interface do usuário hospedada para renderizar a resposta JSON da instância de pesquisa personalizada. Para definir a experiência de interface do usuário:

1. Clique na guia **Interface do Usuário Hospedada**.  
  
2. Selecione um layout.  
  
   ![Captura de tela da interface do usuário hospedadas selecione a etapa de layout](./media/custom-search-hosted-ui-select-layout.png)  
  
3. Selecione um tema de cor.  
  
   ![Captura de tela da UI hospedada selecione o tema de cores](./media/custom-search-hosted-ui-select-color-theme.png)  

   Se você precisar ajustar o tema de cores para integrar-se melhor ao seu aplicativo da web, clique em **Personalizar tema**. Nem todas as configurações de cores se aplicam a todos os temas de layout. Para alterar uma cor, insira o valor RGB HEX da cor (por exemplo, # 366eb8) na caixa de texto correspondente. Ou clique no botão de cor e clique na sombra que funciona para você. Sempre pense em acessibilidade ao selecionar cores.
  
   ![Captura de tela da IU hospedada para personalizar o tema de cores](./media/custom-search-hosted-ui-customize-color-theme.png)  

  
4. Especifique opções de configuração adicionais.  
  
   ![Captura de tela da etapa de configurações adicionais da UI hospedada](./media/custom-search-hosted-ui-additional-configurations.png)  
  
   Para obter configurações avançadas, clique em **Mostrar configurações avançadas**. Isso adiciona configurações como *Vincular destino* a opções de pesquisa na Web, *Ativar filtros* a Opções de imagem e vídeo e *Pesquisar marcador de texto de texto* a opções Diversos.

   ![Captura de tela da etapa de configurações avançadas da UI hospedada](./media/custom-search-hosted-ui-advanced-configurations.png)  
  
5. Selecione suas chaves de assinatura nas listas suspensas. Ou você pode inserir a chave de assinatura manualmente. Para obter informações sobre como obter chaves, consulte [Experimentar serviços cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search-api).  
  
   ![Captura de tela da etapa de configurações adicionais da UI hospedada](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE [publish or revert](../includes/publish-revert.md)]

<a name="consuminghostedui"></a>
## <a name="consuming-hosted-ui"></a>Consumir Interface do Usuário Hospedada

Há duas maneiras de consumir a interface do usuário hospedada.  

- Opção 1: Integre o snippet JavaScript fornecido no aplicativo.
- Opção 2: Use o Ponto de Extremidade de HTML fornecido.

O restante deste tutorial ilustra a **Opção 1: Snippet de JavaScript**.  

## <a name="set-up-your-visual-studio-solution"></a>Configure sua solução do Visual Studio

1. Abra o **Visual Studio** no seu computador.  
  
2. No menu **Arquivo**, selecione **Novo** e depois **Projeto**.  
  
3. Na janela **Novo Projeto**, selecione **Aplicativo Web Visual C#/Web/ASP.NET Core**, nomeie o projeto e clique em **OK**.  
  
   ![Captura de tela da janela novo projeto](./media/custom-search-new-project.png)  
  
4. Na janela, **Aplicativo Web New ASP.NET Core**, selecione **Aplicativo Web** e clique em **OK**.  
  
   ![Captura de tela da janela novo projeto](./media/custom-search-new-webapp.png)  

## <a name="edit-indexcshtml"></a>Editar index.cshtml

1. No **Gerenciador de Soluções**, expanda **Páginas** e clique duas vezes em **index.cshtml** para abrir o arquivo.  
  
   ![Captura de tela do Solution Explorer com páginas expandidas e index.cshtml selecionado](./media/custom-search-visual-studio-webapp-solution-explorer-index.png)  
  
2. Em index.cshtml, exclua tudo a partir da linha 7 e abaixo.  
  
   ```razor
   @page
   @model IndexModel
   @{
      ViewData["Title"] = "Home page";
   }    
   ```  
  
3. Adicione um elemento de quebra de linha e um div para atuar como um contêiner.  
  
   ```html
   @page
   @model IndexModel
   @{
      ViewData["Title"] = "Home page";
   }
   <br />
   <div id="customSearch"></div>
   ```  
  
4. Na página **Hosted UI**, role para baixo até a seção intitulada **Consuming the UI**. Clique no *Endpoints* para acessar o snippet de JavaScript. Você também pode obter o trecho de código clicando **produção** e, em seguida, a guia **hospedado de interface do usuário**.
  
   <!-- Get new screenshot after prod gets new bits
   ![Screenshot of the Hosted UI save button](./media/custom-search-hosted-ui-consuming-ui.png)  
   -->
  
5. Cole o elemento de script no contêiner que você adicionou.  
  
   ``` html
   @page
   @model IndexModel
   @{
      ViewData["Title"] = "Home page";
   }
   <br />
   <div id="customSearch">
      <script type="text/javascript" 
          id="bcs_js_snippet"
          src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
      </script>
   </div>
   ```  
  
6. No **Gerenciador de Soluções**, clicar com o botão direito do mouse em **wwwroot** e clique em **Exibir no Navegador**.  
  
   ![Captura de tela do explorador de soluções selecionando Visualizar no navegador no menu de contexto do wwwroot](./media/custom-search-webapp-view-in-browser.png)  

A nova página da Web de pesquisa personalizada deve ser semelhante a esta:

![Captura de tela da página da Web de pesquisa personalizada](./media/custom-search-webapp-browse-index.png)

Executar uma pesquisa renderiza resultados como este:

![Captura de tela dos resultados da pesquisa personalizada](./media/custom-search-webapp-results.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Chamar ponto de extremidade da Pesquisa Personalizada do Bing (C#)](../call-endpoint-csharp.md)
