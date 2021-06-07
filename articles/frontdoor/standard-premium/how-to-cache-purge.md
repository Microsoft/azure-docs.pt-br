---
title: Limpeza de cache no Azure front door Standard/Premium (visualização)
description: Este artigo ajuda você a entender como limpar o cache em uma porta de recepção do Azure Standard/Premium.
services: frontdoor
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: aacbf2ceab8580727b1885bf6533cd74a7c4e60a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101098389"
---
# <a name="cache-purging-in-azure-front-door-standardpremium-preview"></a>Limpeza de cache no Azure front door Standard/Premium (visualização)

> [!Note]
> Esta documentação é específica para o Azure Front Door Standard/Premium (Versão Prévia). Está procurando informações sobre o Azure Front Door? Veja [aqui](../front-door-overview.md).

O Azure front door Standard/Premium armazena ativos em cache até o tempo de vida (TTL) do ativo expirar. Sempre que um cliente solicita um ativo com TTL expirado, o ambiente de porta frontal do Azure recupera uma nova cópia atualizada do ativo para atender à solicitação e, em seguida, armazena o cache atualizado.

A prática recomendada é garantir que os usuários sempre obtenham a cópia mais recente de seus ativos. A maneira de fazer isso é fazer a versão de seus ativos para cada atualização e publicá-los como novas URLs. O padrão de porta frontal do Azure/Premium recuperará imediatamente os novos ativos para as próximas solicitações de cliente. Às vezes, você pode desejar limpar o conteúdo em cache de todos os nós de borda e forçá-los a recuperar novos ativos atualizados. O motivo pelo qual você deseja limpar o conteúdo armazenado em cache é porque você fez novas atualizações em seu aplicativo ou deseja atualizar ativos que contêm informações incorretas.

> [!IMPORTANT]
> O Azure Front Door Standard/Premium (versão prévia) está na fase de versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

Examine o [cache de porta frontal do Azure](concept-caching.md) para entender como funciona o Caching.

## <a name="configure-cache-purge"></a>Configurar limpeza de cache

1. Vá para a página Visão geral do perfil de porta frontal do Azure com os ativos que você deseja limpar e, em seguida, selecione **Limpar cache**.

   :::image type="content" source="../media/how-to-cache-purge/front-door-cache-purge-1.png" alt-text="Captura de tela da limpeza do cache na página Visão geral.":::

1. Selecione o ponto de extremidade e o domínio que você deseja limpar dos nós de borda. *(Você pode selecionar mais de um domínio)*

   :::image type="content" source="../media/how-to-cache-purge/front-door-cache-purge-2.png" alt-text="Captura de tela da página de limpeza do cache.":::

1. Para limpar todos os ativos, selecione **limpar todos os ativos para os domínios selecionados**. Caso contrário, em **caminhos**, insira o caminho de cada ativo que você deseja limpar.

Esses formatos têm suporte nas listas de caminhos a serem limpos:

* **Limpeza de caminho único**: Limpe ativos individuais especificando o caminho completo do ativo (sem o protocolo e o domínio), com a extensão de arquivo, por exemplo,/Pictures/strasbourg.png.
* **Limpeza de domínio raiz**: Limpe a raiz do ponto de extremidade com "/*" no caminho.

As limpezas de cache no padrão de porta do Azure/preium não diferenciam maiúsculas de minúsculas. Além disso, eles são independentes de cadeia de caracteres de consulta, o que significa que a limpeza de uma URL limpará todas as variações de cadeia de caracteres de consulta. 

## <a name="next-steps"></a>Próximas etapas

Saiba como [criar uma porta de front-Standard/Premium](create-front-door-portal.md).
