---
title: "Pré-carregar ativos em um ponto de extremidade da CDN do Azure | Microsoft Docs"
description: "Saiba como pré-carregar o conteúdo armazenado em cache em um ponto de extremidade da CDN do Azure."
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 1f2dcd9a91bb6e883cbef06373c1acd98bf8d45f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Pré-carregar ativos em um ponto de extremidade da CDN do Azure
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Por padrão, os ativos são armazenados primeiro em cache à medida que são solicitados. Isso significa que a primeira solicitação de cada região pode demorar mais tempo, pois os servidores de borda não terão o conteúdo armazenado em cache e precisarão encaminhar a solicitação para o servidor de origem. O pré-carregamento de conteúdo evita essa latência de primeiro acesso.

Além de fornecer uma experiência mais adequada ao cliente, o pré-carregamento de seus ativos em cache também pode reduzir o tráfego de rede no servidor de origem.

> [!NOTE]
> O pré-carregamento de ativos é útil para grandes eventos ou para conteúdo disponibilizado simultaneamente a um grande número de usuários, como o lançamento de um novo filme ou uma atualização de software.
> 
> 

Esse tutorial orienta você carregando previamente o conteúdo armazenado em cache em todos os nós de borda da CDN do Azure.

## <a name="walkthrough"></a>Passo a passo
1. No [Portal do Azure](https://portal.azure.com), navegue até o perfil da CDN que contém o ponto de extremidade que você deseja pré-carregar.  A folha do perfil é aberta.
2. Clique no ponto de extremidade na lista.  A folha do ponto de extremidade é aberta.
3. Na folha do ponto de extremidade da CDN, clique no botão carregar.
   
    ![Folha do ponto de extremidade da CDN](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    A folha Carregar é aberta.
   
    ![Folha de carregamento da CDN](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. Insira o caminho completo de cada ativo que você deseja carregar (por exemplo, `/pictures/kitten.png`) na caixa de texto **Caminho** .
   
   > [!TIP]
   > Outras caixas de texto **Caminho** serão mostradas depois que você digitar um texto, para permitir que crie uma lista com vários ativos.  Para excluir ativos da lista, clique no botão de reticências (...).
   > 
   > Os caminhos devem ser uma URL relativa que se adapte à seguinte [expressão regular](https://msdn.microsoft.com/library/az24scfc.aspx):  
   > >Carregar um único caminho de arquivo `@"^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$"`;  
   > >Carregar um único arquivo com a cadeia de caracteres de consulta `@"^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$";`  
   > 
   > Cada ativo deve ter seu próprio caminho.  Não há nenhuma funcionalidade de curinga para pré-carregar ativos.
   > 
   > 
   
    ![Botão Carregar](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. Clique no botão **Carregar** .
   
    ![Botão Carregar](./media/cdn-preload-endpoint/cdn-load-button.png)

> [!NOTE]
> Há uma limitação de 10 solicitações de carga por minuto para cada perfil CDN. São permitidos 50 caminhos por solicitação. Cada caminho tem um limite de comprimento de 1024 caracteres.
> 
> 

## <a name="see-also"></a>Consulte também
* [Limpar um ponto de extremidade da CDN do Azure](cdn-purge-endpoint.md)
* [Referência da API REST da CDN do Azure – limpar ou pré-carregar um ponto de extremidade](https://msdn.microsoft.com/library/mt634451.aspx)

