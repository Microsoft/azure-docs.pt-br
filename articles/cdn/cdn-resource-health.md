---
title: Monitorar a integridade dos recursos da CDN do Azure | Microsoft Docs
description: Saiba como monitorar a Azure CDN Resource Health usando o Azure Resource Health.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: bf23bd89-35b2-4aca-ac7f-68ee02953f31
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 6710f5e5b873f751ad21068acdc15d38574f8378
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593450"
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>Monitorar a integridade dos recursos da CDN do Azure
  
A Azure CDN Resource Health é um subconjunto de [Azure Resource Health](../resource-health/resource-health-overview.md).  Você pode usar a Azure  Resource Health para monitorar a integridade dos recursos CDN e receber as diretrizes de ações para solucionar problemas.

>[!IMPORTANT] 
>A integridade de recursos do Azure CDN considera no momento somente a integridade da distribuição de CDN global e recursos de API.  A integridade de recursos do Azure CDN não verifica a pontos de extremidade de CDN individuais.
>
>Os sinais que alimentam a Azure CDN Resource Health podem ter atraso de até 15 minutos.

## <a name="how-to-find-azure-cdn-resource-health"></a>Como localizar a Azure CDN Resource Health

1. No [Portal do Azure](https://portal.azure.com), navegue para seu perfil CDN.

2. Clique no botão **Configurações** .

    ![Botão Configurações](./media/cdn-resource-health/cdn-profile-settings.png)

3. Em *Suporte + solução de problemas*, clique em **Integridade de recursos**.

    ![Integridade de recursos de CDN](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>Você também pode encontrar recursos de CDN listados no bloco *Integridade de recursos* na folha *Ajuda + suporte*.  Você pode ir rapidamente até *Ajuda + suporte* clicando no **?** circulado no canto superior direito do portal.
>
> ![Ajuda + suporte](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Mensagens específicas do Azure CDN

Os estados de integridade de recursos do Azure CDN podem ser encontrados abaixo.

|Mensagem | Ação recomendada |
|---|---|
|Você pode ter parado, removido ou configurado de forma errada um ou mais dos seus pontos de extremidade de CDN | Você pode ter parado, removido ou configurado de forma errada um ou mais dos seus pontos de extremidade de CDN.|
|Infelizmente, o serviço de gerenciamento de CDN está indisponível no momento | Regresse aqui para ver atualizações de estado; se o problema persistir após o tempo de resolução esperado, contate o suporte.|
|Infelizmente, os pontos de extremidade de CDN podem ser afetados por problemas existentes com alguns dos nossos provedores de CDN | Regresse aqui para ver atualizações de estado; use a ferramenta de solução de problemas para saber como testar a origem e o ponto de extremidade de CDN. Se o problema persistir após o tempo de resolução esperado, contate o suporte. |
|Infelizmente, as alterações de configuração de ponto de extremidade de CDN estão sofrendo atrasos de propagação | Regresse aqui para ver atualizações de estado; se as alterações de configuração não forem totalmente propagadas no tempo esperado, contate o suporte.|
|Infelizmente, estamos tendo problemas ao carregar o portal suplementar | Regresse aqui para ver atualizações de estado; se o problema persistir após o tempo de resolução esperado, contate o suporte.|
Infelizmente, estamos tendo problemas com alguns dos nossos provedores de CDN | Regresse aqui para ver atualizações de estado; se o problema persistir após o tempo de resolução esperado, contate o suporte. |

## <a name="next-steps"></a>Próximas etapas

- [Acesse a visão geral do Azure Resource Health](../resource-health/resource-health-overview.md)
- [Solucione problemas com a compactação de arquivo de CDN](./cdn-troubleshoot-compression.md)
- [Solucione problemas com erros 404](./cdn-troubleshoot-endpoint.md)