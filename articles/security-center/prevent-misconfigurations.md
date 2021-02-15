---
title: Como evitar configurações incorretas com a central de segurança do Azure
description: Saiba como usar as opções ' impor ' e ' negar ' da central de segurança nas páginas de detalhes de recomendações
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 2607ff3a6c4bb7bf301818925990521572777ab9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379175"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>Impedir configurações incorretas com as recomendações de Impor/Negar

Configurações incorretas de segurança são uma das principais causas de incidentes de segurança. Agora a Central de Segurança pode ajudar a *impedir* configurações incorretas de novos recursos, com relação a recomendações específicas. 

Esse recurso pode ajudar a manter suas cargas de trabalho seguras e estabilizar sua classificação de segurança.

A imposição de uma configuração segura, com base em uma recomendação específica, é oferecida em dois modos:

- Usando o efeito **Negar** do Azure Policy, você pode interromper a criação de recursos não íntegros
- Usando a opção **Impor**, você pode aproveitar o efeito **DeployIfNotExist** da política do Azure e corrigir automaticamente os recursos fora de conformidade após a criação

Isso pode ser encontrado na parte superior da página de detalhes do recurso para obter as recomendações de segurança selecionadas (consulte [recomendações com as opções Deny/Force](#recommendations-with-denyenforce-options)).

## <a name="prevent-resource-creation"></a>Impedir a criação de recursos

1. Abra a recomendação que os novos recursos devem satisfazer e selecione o botão **negar** na parte superior da página.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="Página de recomendação com o botão negar realçado":::

    O painel de configuração é aberto listando as opções de escopo. 

1. Defina o escopo selecionando a assinatura ou o grupo de gerenciamento relevante.

    > [!TIP]
    > Você pode usar os três pontos no final da linha para alterar uma única assinatura ou usar as caixas de seleção para selecionar várias assinaturas ou grupos e, em seguida, selecionar **alterar para negar**.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="Definindo o escopo para Azure Policy negar":::


## <a name="enforce-a-secure-configuration"></a>Impor uma configuração segura

1. Abra a recomendação na qual você implantará uma implantação de modelo se novos recursos não atenderem e selecione o botão **impor** na parte superior da página.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="Página de recomendação com o botão impor realçado":::

    O painel de configuração é aberto com todas as opções de configuração de política. 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="Impor opções de configuração":::

1. Defina o escopo, o nome da atribuição e outras opções relevantes.

1. Selecione **Examinar + criar**.

## <a name="recommendations-with-denyenforce-options"></a>Recomendações com opções Deny/Force

Essas recomendações podem ser usadas com a opção **Deny** :

[!INCLUDE [azure-security-center-recommendations-deny](../../includes/asc/recommendations-with-deny.md)]

Essas recomendações podem ser usadas com a opção **impor** :

- A auditoria no SQL Server deve ser habilitada
- O Backup do Azure deve ser habilitado para máquinas virtuais
- O Azure Defender para SQL deverá ser habilitado em servidores SQL
- Os logs de diagnóstico no Azure Stream Analytics devem ser habilitados
- Os logs de diagnóstico em contas do Lote devem ser habilitados
- Os logs de diagnóstico no Data Lake Analytics devem ser habilitados
- Os logs de diagnóstico no Hub de eventos devem ser habilitados
- Os logs de diagnóstico no Key Vault deve estar habilitados
- Os logs de diagnóstico nos Aplicativos Lógicos devem ser habilitados
- Os logs de diagnóstico nos serviços Pesquisa devem ser habilitados
- Os logs de diagnóstico no Barramento de Serviço devem ser habilitados
