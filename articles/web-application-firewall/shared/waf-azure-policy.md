---
title: Firewall e Azure Policy do aplicativo Web do Azure
description: O WAF (firewall do aplicativo Web) do Azure combinado com Azure Policy pode ajudar a reforçar os padrões organizacionais e a avaliar a conformidade em escala para recursos do WAF
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: rimansdo
ms.openlocfilehash: 7798d7e960286d4f8aa971eb2eb0b03d24bd6360
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589450"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Firewall e Azure Policy do aplicativo Web do Azure

O WAF (firewall do aplicativo Web) do Azure combinado com Azure Policy pode ajudar a reforçar os padrões organizacionais e a avaliar a conformidade em escala para recursos do WAF. A política do Azure é uma ferramenta de governança que fornece uma exibição agregada para avaliar o estado geral do ambiente, com a capacidade de fazer uma busca detalhada na granularidade por recurso, por política. O Azure Policy também ajuda a trazer seus recursos para conformidade por meio da correção em massa de recursos existentes e correção automática para novos recursos.

## <a name="azure-policy-for-web-application-firewall"></a>Azure Policy para o Firewall do aplicativo Web

Há várias definições de Azure Policy internas para gerenciar recursos de WAF. Uma análise das definições de política e suas funcionalidades são as seguintes:

1. O **WAF (firewall do aplicativo Web) deve ser habilitado para o serviço de porta frontal do Azure**: os serviços de porta frontal do Azure são avaliados se houver um WAF presente na criação de recursos. A política tem três efeitos: auditoria, negar e desabilitar. Trilhas de auditoria quando um serviço de porta frontal do Azure não tem um WAF e permite que os usuários vejam o que o serviço de porta frontal do Azure não está em conformidade. Deny impede que qualquer serviço de porta frontal do Azure seja criado se um WAF não estiver anexado. Desabilitado desativa esta política.

2. O **Firewall do aplicativo Web (WAF) deve ser habilitado para o gateway de aplicativo**: os gateways de aplicativo serão avaliados se houver um WAF presente na criação de recursos. A política tem três efeitos: auditoria, negar e desabilitar. Trilhas de auditoria quando um gateway de aplicativo não tem um WAF e permite que os usuários vejam qual gateway de aplicativo não está em conformidade. Deny impede que qualquer gateway de aplicativo seja criado se um WAF não estiver anexado. Desabilitado desativa esta política.

3. **O WAF (firewall do aplicativo Web) deve usar o modo especificado para o serviço de porta frontal do Azure**: exige que o uso do modo ' detecção ' ou ' prevenção ' esteja ativo em todas as políticas de firewall do aplicativo Web para o serviço de porta de recepção do Azure. A política tem três efeitos: auditoria, negar e desabilitar. Trilhas de auditoria quando um WAF não se ajusta ao modo especificado. Deny impede que qualquer WAF seja criada se não estiver no modo correto. Desabilitado desativa esta política.

4. **O WAF (firewall do aplicativo Web) deve usar o modo especificado para o gateway de aplicativo**: exige que o uso do modo ' detecção ' ou ' prevenção ' esteja ativo em todas as políticas de firewall do aplicativo Web para o gateway de aplicativo. A política tem três efeitos: auditoria, negar e desabilitar. Trilhas de auditoria quando um WAF não se ajusta ao modo especificado. Deny impede que qualquer WAF seja criada se não estiver no modo correto. Desabilitado desativa esta política.

## <a name="launch-an-azure-policy"></a>Iniciar um Azure Policy

1.  Na home page do Azure, digite política na barra de pesquisa e clique no ícone de Azure Policy

2.  No serviço Azure Policy, em **criação**, selecione **atribuições**.

:::image type="content" source="../media/waf-azure-policy/policy-home.png" alt-text="Guia atribuições em Azure Policy":::

3.  Na página atribuições, selecione o ícone **atribuir política** na parte superior.

:::image type="content" source="../media/waf-azure-policy/assign-policy.png" alt-text="Guia noções básicas na página atribuir política":::

4.  Na guia elementos básicos da página atribuir política, atualize os seguintes campos:
    1.  **Escopo**: Selecione quais assinaturas e grupos de recursos do Azure devem ser afetados pela definição de política.
    2.  **Exclusões**: selecione todos os recursos do escopo a serem excluídos da atribuição de política.
    3.  **Definição de política**: selecione a definição de política a ser aplicada ao escopo com exclusões. Digite "Firewall do aplicativo Web" na barra de pesquisa para escolher o Azure Policy de firewall do aplicativo Web relevante.

:::image type="content" source="../media/waf-azure-policy/policy-listing.png" alt-text="Captura de tela que mostra a guia ' definições de política ' na página ' definições disponíveis '.":::

5.  Selecione a guia **parâmetros** e atualize os parâmetros de atribuição de política. Para esclarecer ainda mais o que o parâmetro faz, passe o mouse sobre o ícone de informações ao lado do nome do parâmetro para obter mais esclarecimentos.

6.  Selecione **revisar + criar** para finalizar sua atribuição de política. A atribuição de política leva aproximadamente 15 minutos até estar ativa para novos recursos.
