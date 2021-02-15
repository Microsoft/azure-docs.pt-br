---
title: Migrar o perfil da CDN do Azure da Verizon Standard para Verizon Premium
description: Saiba mais sobre os detalhes da migração de um perfil da Verizon Standard para o Verizon Premium.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/21/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: e58c1177fb3c4d241a3efe1759b3900abbd04ca1
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92778380"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Migre um perfil Azure CDN do Verizon Standard para o Verizon Premium

Quando você cria uma Rede de Distribuição de Conteúdo do Microsoft Azure (CDN) para gerenciar seus pontos de extremidade, o Azure CDN oferece quatro produtos diferentes para você escolher. Para obter informações sofre os produtos diferentes e seus recursos disponíveis, consulte [Comparar recursos de produto Azure CDN](cdn-features.md).

Se você criou um perfil **Azure CDN Standard do Verizon** e está usando para gerenciar seus pontos de extremidade CDN, você tem a opção de atualizar para um perfil do **Azure CDN Premium do  Verizon** . Ao atualizar, seus pontos de extremidade CDN e todos os seus dados serão preservados. 

> [!IMPORTANT]
> Ao atualizar para um perfil **Azure CDN Premium do Verizon** , você não poderá depois converter de volta para um perfil  **Azure CDN Standard do Verizon** .
> 

Para atualizar um perfil **Azure CDN Standard do Verizon** , entre em contato com o [Suporte da Microsoft](https://azure.microsoft.com/support/options/).

## <a name="profile-comparison"></a>Comparação de perfil
**Perfis Azure CDN Premium do Verizon** têm duas diferenças principais dos perfis **Azure CDN Standard do Verizon** :
- Para determinados recursos do Azure CDN como [compressão](cdn-improve-performance.md), [regras de cache](cdn-caching-rules.md), e [filtragem de geografia](cdn-restrict-access-by-country.md), você não pode usar a interface do Azure CDN, você deve usar o portal do Verizon através botão **Gerenciar** .
- API: Ao contrário do Verizon Standard, você não pode usar a API para controlar os recursos que são acessados pelo portal Verizon Premium. No entanto, você pode usar a API para controlar outros recursos comuns, como criar / excluir um ponto de extremidade, limpar / carregar ativos armazenados em cache e ativar / desativar um domínio personalizado.
- Preços: o Verizon Premium tem uma estrutura de preço diferente para transferência de dados diferente do Verizon Standard. Para obter mais informações, consulte [Preços de Rede de Distribuição de Conteúdo](https://azure.microsoft.com/pricing/details/cdn/).

**Perfis Azure CDN Premium do Verizon** têm os seguintes recursos adicionais:
- [Autenticação do Token](cdn-token-auth.md): permite que os usuários obtenham e usem um token para buscar recursos seguros.
- [Mecanismo de regras](./cdn-verizon-premium-rules-engine.md): O mecanismo de regras da CDN do Azure permite que você personalize como as solicitações HTTP são manipuladas.
- Ferramentas de análise avançada:
   - [Análise HTTP detalhada](cdn-advanced-http-reports.md)
   - [Análise de desempenho do Edge](cdn-edge-performance.md)
   - [Análise em tempo real](cdn-real-time-alerts.md)


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o mecanismo de regras, consulte [Referência do mecanismo de regras da CDN do Azure](./cdn-verizon-premium-rules-engine-reference.md).