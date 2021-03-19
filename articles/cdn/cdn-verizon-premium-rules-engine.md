---
title: Substituir o comportamento do HTTP com a CDN do Azure – mecanismo de regras da Verizon Premium
description: O mecanismo de regras permite que você personalize como as solicitações HTTP são processadas pela CDN do Azure na Verizon Premium, como o bloqueio da distribuição de certos tipos de conteúdo, definição de uma política de cache e modificação dos cabeçalhos HTTP.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: a49912bc2275e478d657f06587c4ddc830210d3a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87040216"
---
# <a name="override-http-behavior-using-the-azure-cdn-from-verizon-premium-rules-engine"></a>Substituir o comportamento do HTTP usando a CDN do Azure por meio do mecanismo de regras da Verizon Premium

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Visão geral

O mecanismo de regras da CDN do Azure permite que você personalize como as solicitações HTTP são manipuladas. Por exemplo, bloqueando a entrega de determinados tipos de conteúdo, definindo uma política de cache ou modificando um cabeçalho HTTP. Este tutorial demonstra como criar uma regra que altera o comportamento de armazenamento em cache dos ativos da CDN. Para obter mais informações sobre a sintaxe do mecanismo de regras, consulte [Referência do mecanismo de regras da CDN do Azure](cdn-verizon-premium-rules-engine-reference.md).

## <a name="access"></a>Acesso

Para acessar o mecanismo de regras, será necessário primeiro selecionar **Gerenciar** na parte superior da página do **Perfil CDN profile** para acessar a página de gerenciamento da CDN do Azure. Dependendo se o ponto de extremidade está otimizado para DSA (aceleração de site dinâmico), então, você acessa o mecanismo de regras com o conjunto de regras apropriadas para o tipo de ponto de extremidade:

- Pontos de extremidade otimizados para entrega Web geral ou outras otimizações não DSA:
    
    Selecione a guia **HTTP Grande** e, em seguida, selecione **Mecanismo de Regras**.

    ![Mecanismo de regras para HTTP](./media/cdn-rules-engine/cdn-http-rules-engine.png)

- Pontos de extremidade otimizados para DSA:
    
    Selecione a guia **ADN** e, em seguida, selecione **Mecanismo de Regras**.
    
    O ADN é um termo usado pela Verizon para especificar o conteúdo de DSA. Todas as regras criadas aqui são ignoradas pelos pontos de extremidade do seu perfil que não estejam otimizados para DSA.

    ![Mecanismo de regras para DSA](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)

## <a name="tutorial"></a>Tutorial

1. Na página **Perfil CDN**, selecione **Gerenciar**.
   
    ![Botão Gerenciar perfil da CDN](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    O portal de gerenciamento da CDN é aberto.

2. Selecione a guia **HTTP Grande** e, em seguida, selecione **Mecanismo de Regras**.
   
    As opções para uma nova regra são exibidas.
   
    ![Novas opções de regra CDN](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > A ordem na qual são listadas várias regras afeta como elas são manipuladas. Uma regra subsequente poderá substituir as ações especificadas por uma regra anterior. Por exemplo, se você tiver uma regra que permita o acesso a um recurso com base em uma propriedade de solicitação e uma regra que negue acesso a todas as solicitações, a segunda regra substituirá a primeira. As regras substituirão as regras anteriores somente se interagirem com as mesmas propriedades.
   >

3. Insira um nome na caixa de texto **Nome/Descrição** .

4. Identifique o tipo de solicitação à qual a regra se aplica. Use a condição de correspondência padrão, **Sempre**.
   
   ![Condição de correspondência de regra de CDN](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!NOTE]
   > As várias condições de correspondência estão disponíveis na lista suspensa. Para obter informações sobre a condição de correspondência atualmente selecionada, selecione o ícone informativo azul à esquerda.
   >
   >  Para obter uma lista detalhada de expressões condicionais, consulte [Expressões condicionais do mecanismo de regras](cdn-verizon-premium-rules-engine-reference-match-conditions.md).
   >  
   > Para obter uma lista detalhada das condições de correspondência, consulte [Condições de correspondência do mecanismo de regras](cdn-verizon-premium-rules-engine-reference-match-conditions.md).
   >
   >

5. Para adicionar um novo recurso, selecione o botão **+** próximo aos **Recursos**.  Na lista suspensa à esquerda, selecione **Force Internal Max-Age**.  Na caixa de texto que aparece, digite **300**. Não altere os valores padrão restantes.
   
   ![Recurso de regra da CDN](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Vários recursos estão disponíveis na lista suspensa. Para obter informações sobre o recurso atualmente selecionado, selecione o ícone informativo azul à esquerda.
   >
   > Para **Force Internal Max-Age**, os cabeçalhos `Cache-Control` e `Expires` do ativo são substituídos para controlar quando o ponto de extremidade de borda da CDN atualiza o ativo da origem. Neste exemplo, o ponto de extremidade de borda da CDN armazena em cache o ativo por 300 segundos, ou 5 minutos, antes de atualizar o ativo de sua origem.
   >
   > Para obter uma lista detalhada de recursos, consulte [Recursos do mecanismo de regras](cdn-verizon-premium-rules-engine-reference-features.md).
   >
   >

6. Selecione **Adicionar** para salvar a nova regra.  A nova regra agora está aguardando aprovação. Após ter sido aprovado, o status altera de **XML pendente** para **XML ativo**.
   
   > [!IMPORTANT]
   > As alterações nas regras podem demorar até 10 minutos para serem propagadas pela CDN do Azure.
   >
   >

## <a name="see-also"></a>Confira também

- [Visão geral da CDN do Azure](cdn-overview.md)
- [Referência do mecanismo de regras](cdn-verizon-premium-rules-engine-reference.md)
- [Condições de correspondência do mecanismo de regras](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Expressões condicionais do mecanismo de regras](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Recursos do mecanismo de regras](cdn-verizon-premium-rules-engine-reference-features.md)
- [Azure Fridays: novos recursos Premium avançados da CDN do Azure](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (vídeo)