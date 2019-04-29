---
title: Pré-carregar ativos em um ponto de extremidade da CDN do Azure | Microsoft Docs
description: Saiba como pré-carregar o conteúdo armazenado em cache em um ponto de extremidade da CDN do Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: magattus
ms.openlocfilehash: 0d34985c8d83e8adad43aeec36ead939d8b22132
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627367"
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Pré-carregar ativos em um ponto de extremidade da CDN do Azure
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Por padrão, os ativos são armazenados em cache apenas quando são solicitados. Como os servidores de borda ainda não armazenaram o conteúdo em cache e precisam encaminhar a solicitação para o servidor de origem, a primeira solicitação de cada região pode levar mais tempo que as solicitações posteriores. Para evitar essa latência na primeira ocorrência, pré-carregue seus ativos. Além de fornecer uma experiência mais adequada ao cliente, o pré-carregamento de seus ativos em cache pode reduzir o tráfego de rede no servidor de origem.

> [!NOTE]
> O pré-carregamento de ativos é útil para grandes eventos ou para conteúdo disponibilizado simultaneamente a muitos usuários, como o lançamento de um novo filme ou uma atualização de software.
> 
> 

Esse tutorial orienta você carregando previamente o conteúdo armazenado em cache em todos os nós de borda da CDN do Azure.

## <a name="to-pre-load-assets"></a>Para pré-carregar ativos
1. No [Portal do Azure](https://portal.azure.com), navegue até o perfil da CDN que contém o ponto de extremidade que você deseja pré-carregar. O painel do perfil é aberto.
    
2. Clique no ponto de extremidade na lista. O painel do ponto de extremidade é aberto.
3. No painel do ponto de extremidade da CDN, selecione **Carregar**.
   
    ![Painel do ponto de extremidade do CDN](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    O painel **Carregar** é aberto.
   
    ![Painel de carregamento da CDN](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. Em **Caminho do conteúdo**, insira o caminho completo de cada ativo que você deseja carregar (por exemplo, `/pictures/kitten.png`).
   
   > [!TIP]
   > Após você começar a inserir texto, mais caixas de texto de **Caminho do conteúdo** serão mostradas para permitir que você crie uma lista com vários ativos. Para excluir ativos da lista, selecione o botão de reticências (...) e selecione **Excluir**.
   > 
   > Cada caminho de conteúdo deve ser uma URL relativa que se encaixa nas seguintes [expressões regulares](/dotnet/standard/base-types/regular-expression-language-quick-reference):  
   > - Carregar um único caminho de arquivo: `^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$`  
   > - Carregar um único arquivo com uma cadeia de caracteres de consulta: `^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$` 
   > 
   > Como cada ativo precisa ter seu próprio caminho, não há nenhuma funcionalidade de curinga para pré-carregar ativos.
   > 
   > 
   
    ![Botão Carregar](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. Quando terminar de inserir os caminhos de conteúdo, selecione **Carregar**.
   

> [!NOTE]
> Há um limite de 10 solicitações de carga por minuto para cada perfil de CDN e 50 caminhos simultâneos podem ser processados ao mesmo tempo. Cada caminho tem um limite de comprimento de 1024 caracteres.
> 
> 

## <a name="see-also"></a>Consulte também
* [Limpar um ponto de extremidade da CDN do Azure](cdn-purge-endpoint.md)
* [Referência da API REST da CDN do Azure: Pré-carregar o conteúdo em um ponto de extremidade](https://docs.microsoft.com/rest/api/cdn/endpoints/loadcontent)
* [Referência da API REST da CDN do Azure: Limpar o conteúdo de um ponto de extremidade](https://docs.microsoft.com/rest/api/cdn/endpoints/purgecontent)

