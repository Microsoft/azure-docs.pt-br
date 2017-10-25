---
title: Substituir o comportamento HTTP usando o mecanismo de regras da CDN do Azure | Microsoft Docs
description: "O mecanismo de regras permite que você personalize a forma como as solicitações HTTP são manipuladas pela CDN do Azure, como o bloqueio da entrega de certos tipos de conteúdo, definição de uma política de cache e modificação dos cabeçalhos HTTP."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 625a912b-91f2-485d-8991-128cc194ee71
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: abfe283476206b181018d187675b47112dc5ad2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="override-http-behavior-using-the-azure-cdn-rules-engine"></a>Substituir o comportamento HTTP usando o mecanismo de regras da CDN do Azure
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Visão geral
O mecanismo de regras permite personalizar a forma como as solicitações HTTP são manipuladas, como o bloqueio de entrega de alguns tipos de conteúdo, definição de uma política de caching e modificação dos cabeçalhos HTTP.  Este tutorial demonstrará a criação de uma regra que irá alterar o comportamento de caching dos ativos da CDN.  Também há conteúdo de vídeo disponível na seção "[Veja também](#see-also)".

   > [!TIP] 
   > Para obter uma referência para a sintaxe em detalhes, consulte [Referência do Mecanismo de Regras](cdn-rules-engine-reference.md).
   > 


## <a name="tutorial"></a>Tutorial
1. Na folha do perfil CDN, clique no botão **Gerenciar** .
   
    ![botão gerenciar da folha Perfil CDN](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    O portal de gerenciamento da CDN é aberto.
2. Clique na guia **HTTP Grande**, seguida do **Mecanismo de Regras**.
   
    São exibidas opções para uma nova regra.
   
    ![Novas opções de regra CDN](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > A ordem na qual são listadas várias regras afeta como elas são manipuladas. Uma regra subsequente poderá substituir as ações especificadas por uma regra anterior.
   > 
   > 
3. Insira um nome na caixa de texto **Nome/Descrição** .
4. Identifique o tipo de solicitações às quais a regra será aplicada.  Por padrão, a condição de correspondência **Sempre** é selecionada.  Você usará **Sempre** para este tutorial; portanto, deixe essa opção marcada.
   
   ![Condição de correspondência CDN](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!TIP]
   > Há vários tipos de condições de correspondência disponíveis no menu suspenso.  Clicar no ícone de informação azul à esquerda da condição de correspondência explicará a condição atualmente selecionada em detalhes.
   > 
   >  Para obter a lista completa de expressões condicionais em detalhes, consulte [Expressões condicionais do Mecanismo de regras](cdn-rules-engine-reference-match-conditions.md).
   >  
   > Para obter a lista completa de condições de correspondência em detalhes, veja [Condições de correspondência do Mecanismo de regras](cdn-rules-engine-reference-match-conditions.md).
   > 
   > 
5. Clique no botão **+** próximo aos **Recursos** para adicionar um novo recurso.  Na lista suspensa à esquerda, selecione **Force Internal Max-Age**.  Na caixa de texto que aparece, digite **300**.  Mantenha os valores padrão restantes.
   
   ![Recurso CDN](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Assim como ocorre com condições de correspondência, clicando no ícone de informação azul à esquerda do novo recurso para exibir detalhes sobre esse recurso.  No caso de **Force Internal Max-Age**, estamos substituindo os cabeçalhos **Cache-Control** e **Expires** do ativo para controlar quando o nó de borda da CDN atualizará o ativo da origem.  Nosso exemplo de 300 segundos significa que o nó de borda da CDN armazenará em cache o ativo por 5 minutos antes de atualizar o ativo de sua origem.
   > 
   > Para obter a lista completa de recursos em detalhes, veja [Detalhes de recursos do Mecanismo de regras](cdn-rules-engine-reference-features.md).
   > 
   > 
6. Clique no botão **Adicionar** para salvar a nova regra.  A nova regra agora está aguardando aprovação. Depois de aprovado, o status será alterado de **XML Pendente** para **XML Ativo**.
   
   > [!IMPORTANT]
   > Alterações de regras podem levar até 90 minutos para propagar por meio da CDN.
   > 
   > 

## <a name="see-also"></a>Consulte também
* [Visão geral da CDN do Azure](cdn-overview.md)
* [Referência do Mecanismo de Regras](cdn-rules-engine-reference.md)
* [Condições de correspondência do Mecanismo de regras](cdn-rules-engine-reference-match-conditions.md)
* [Expressões condicionais do Mecanismo de regras](cdn-rules-engine-reference-conditional-expressions.md)
* [Recursos do Mecanismo de Regras](cdn-rules-engine-reference-features.md)
* [Substituindo o comportamento HTTP padrão usando o mecanismo de regras](cdn-rules-engine.md)
* [Azure Fridays: novos recursos Premium poderosos do Azure CDN](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (vídeo)