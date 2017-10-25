---
title: "Localizações nomeadas no Azure Active Directory | Microsoft Docs"
description: "Ao configurar localizações nomeadas, você pode evitar que endereços IP que pertencem à sua organização gerem falsos positivos para o tipo de evento de risco Viagem impossível a localizações atípicas."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: da437908509e40386ed23863648bd6956b308186
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="named-locations-in-azure-active-directory"></a>Localizações nomeadas no Azure Active Directory

Com o recurso de localizações nomeadas do Azure Active Directory, você pode rotular os intervalos de endereços IP confiáveis em suas organizações. Em seu ambiente, você pode usar localizações nomeadas no contexto da detecção de [eventos de risco](active-directory-reporting-risk-events.md). O recurso ajuda a reduzir o número de falsos positivos relatados para o tipo de evento de risco *Impossível viajar para localizações atípicas*. 

## <a name="configuration"></a>Configuração

Para configurar uma localização nomeada:

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador global.

2. No painel esquerdo, clique em **Azure Active Directory**.

    ![O link do Azure Active Directory no painel esquerdo](./media/active-directory-named-locations/01.png)

3. Na folha **Azure Active Directory**, na seção **Segurança**, clique em **Acesso condicional**.

    ![O comando Acesso condicional](./media/active-directory-named-locations/05.png)


4. Na folha **Acesso Condicional**, na seção **Gerenciar**, clique em **Localizações nomeadas**.

    ![O comando Localizações nomeadas](./media/active-directory-named-locations/06.png)


5. Na folha **Localizações nomeadas**, clique em **Novo local**.

    ![O comando Novo local](./media/active-directory-named-locations/07.png)


6. Na folha **Novo**, faça o seguinte:

    ![A Nova folha](./media/active-directory-named-locations/56.png)

    a. Na caixa **Nome**, digite um nome para a localização nomeada.

    b. Na caixa **Intervalos de IP**, digite um intervalo de IP. O intervalo de IP precisa estar no formato *CIDR* (Roteamento Entre Domínios Sem Classe).  

    c. Clique em **Criar**.



## <a name="what-you-should-know"></a>O que você deve saber

**Atualizações em massa**: quando você cria ou atualiza localizações nomeadas, para atualizações em massa, você pode carregar ou baixar um arquivo CSV com os intervalos de IP. Um carregamento adiciona os intervalos de IP no arquivo à lista em vez de substituir a lista.

![Os links para carregar e baixar arquivos](./media/active-directory-named-locations/09.png)


**Limitações**: você pode definir um máximo de 60 localizações nomeadas com um intervalo de IP atribuído a cada uma delas. Se você tiver apenas uma localização nomeada configurada, poderá definir até 500 intervalos de IP para ela.


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre eventos de risco, veja [Eventos de risco do Azure Active Directory](active-directory-reporting-risk-events.md).

