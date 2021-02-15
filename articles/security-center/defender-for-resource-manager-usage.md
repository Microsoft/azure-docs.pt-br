---
title: Como responder a alertas do Azure defender para o Resource Manager
description: Saiba mais sobre as etapas necessárias para responder a alertas do Azure defender para o Resource Manager
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 54790795aab8aac247e17198159130d7139dd38c
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754565"
---
# <a name="respond-to-azure-defender-for-resource-manager-alerts"></a>Responder aos alertas do Azure defender para o Resource Manager

Ao receber um alerta do Azure defender para o Resource Manager, recomendamos que você investigue e responda ao alerta conforme descrito abaixo. O Azure defender para o Resource Manager protege todos os recursos conectados, portanto, mesmo que você esteja familiarizado com o aplicativo ou o usuário que disparou o alerta, é importante verificar a situação ao redor de cada alerta.  


## <a name="step-1-contact"></a>Etapa 1. Contact

1. Contate o proprietário do recurso para determinar se o comportamento era esperado ou intencional.
1. Se a atividade for esperada, ignore o alerta.
1. Se a atividade for inesperada, trate as contas de usuário relacionadas, as assinaturas e as máquinas virtuais como comprometidas e atenuadas, conforme descrito na etapa a seguir.

## <a name="step-2-immediate-mitigation"></a>Etapa 2. Mitigação imediata 

1. Corrigir contas de usuário comprometidas:
    - Se eles não estiverem familiarizados, exclua-os, pois eles podem ter sido criados por um ator de ameaça
    - Se eles estiverem familiarizados, altere suas credenciais de autenticação
    - Use os logs de atividades do Azure para examinar todas as atividades executadas pelo usuário e identificar os que são suspeitos

1. Corrigir assinaturas comprometidas:
    - Remover Runbooks desconhecidos da conta de automação comprometida
    - Examine as permissões IAM para a assinatura e remova as permissões para qualquer conta de usuário desconhecida
    - Examine todos os recursos do Azure na assinatura e exclua os que não são familiares
    - Examinar e investigar os alertas de segurança para a assinatura na central de segurança do Azure
    - Use os logs de atividades do Azure para examinar todas as atividades executadas na assinatura e identificar as que são suspeitas

1. Corrigir as máquinas virtuais comprometidas
    - Alterar as senhas de todos os usuários
    - Executar uma verificação completa de antimalware no computador
    - Refazer a imagem dos computadores de uma fonte sem malware


## <a name="next-steps"></a>Próximas etapas

Esta página explicou o processo de responder a um alerta do Azure defender para o Resource Manager. Para obter informações relacionadas, consulte as seguintes páginas:

- [Introdução ao Azure defender para o Gerenciador de recursos](defender-for-resource-manager-introduction.md)
- [Suprimir alertas do Azure Defender](alerts-suppression-rules.md)
- [Exportar continuamente os dados da Central de Segurança](continuous-export.md)