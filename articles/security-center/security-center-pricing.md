---
title: "Atualizar para a camada Standard da Central de Segurança para segurança aprimorada | Microsoft Docs"
description: "Este artigo fornece informações sobre os preços para a Central de Segurança do Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: terrylan
ms.openlocfilehash: 14513c37964246397d9f748322975ef40ef3e8ae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-to-security-centers-standard-tier-for-enhanced-security"></a>Atualizar para a camada Standard da Central de Segurança para segurança aprimorada
A Central de Segurança do Azure fornece gerenciamento de segurança unificado e proteção avançada contra ameaças para cargas de trabalho em execução no Azure, localmente e em outras nuvens. Ela proporciona visibilidade e controle sobre cargas de trabalho de nuvem híbrida, defesas ativas que reduzem a exposição a ameaças e detecção inteligente para ajudá-lo a acompanhar o ritmo veloz da evolução dos ataques cibernéticos.

## <a name="pricing-tiers"></a>Tipos de preço
A Central de Segurança é oferecida em duas camadas:

- A camada **Gratuita** é habilitada automaticamente em todas as assinaturas do Azure e oferece política de segurança, avaliação de segurança contínua e recomendações de práticas de segurança para ajudar a proteger seus recursos do Azure.
- A camada **Standard** estende os recursos da Camada gratuita para cargas de trabalho em execução em outras nuvens públicas e privadas, fornecendo gerenciamento unificado de segurança e proteção contra ameaças em suas cargas de trabalho de nuvem híbrida. A camada Standard também adiciona recursos avançados de detecção de ameaças, que usam análise comportamental interna e aprendizado de máquina para identificar ataques e explorações de dia zero, controles de acesso e de aplicativos a fim de reduzir a exposição a ataques de rede e malware, e muito mais. A camada Standard é gratuita pelos 60 primeiros dias.

Para obter mais informações, confira a [página de preços](https://azure.microsoft.com/pricing/details/security-center/) da Central de Segurança.

## <a name="try-standard-free-for-60-days"></a>Experimentar a versão Standard gratuitamente por 60 dias
A camada Standard é oferecida gratuitamente nos 60 primeiros dias. Ao fim dos 60 dias, se você optar por continuar usando o serviço, começaremos a cobrar automaticamente pelo uso.

Você pode atualizar uma assinatura inteira do Azure para a camada Standard, que será herdada por todos os recursos na assinatura ou você pode definir uma política exclusiva para atualizar somente um Grupo de recursos específico.

Para obter a camada Standard:

1. Selecione **Política de Segurança** no menu principal da **Central de Segurança**.
2. Selecione a assinatura que você deseja atualizar para o Padrão.
3. Na folha **Política de segurança**, selecione **Camada de preços**.
4. Selecione **Standard** para atualizar.
5. Clique em **Salvar**.

![Incidente de segurança][1]

> [!NOTE]
> Para habilitar todos os recursos da Central de Segurança, você deve aplicar o tipo de preço Standard à assinatura ou ao grupo de recursos que contém as máquinas virtuais aplicáveis. A configuração de preços para um espaço de trabalho não habilita o acesso Just-In-Time à VM, os controles de aplicativo adaptáveis e as detecções de rede para os recursos do Azure.
>
>

## <a name="why-upgrade-to-standard"></a>Por que atualizar para Padrão?
A Central de Segurança oferece maior segurança e proteção contra ameaças para suas cargas de trabalho de nuvem híbrida, incluindo:

- **Segurança híbrida** – Obtenha uma exibição unificada sobre a segurança em todas as suas cargas de trabalho locais e na nuvem. Aplique políticas de segurança e avalie continuamente a segurança de suas cargas de trabalho de nuvem híbrida a fim de garantir a conformidade com padrões de segurança. Colete, pesquise e analise dados de segurança de uma variedade de fontes, inclusive firewalls e outras soluções de parceiros.
- **Detecção avançada de ameaças** – Use análises avançadas e o Gráfico de segurança inteligente da Microsoft para obter uma vantagem sobre ataques cibernéticos em evolução.  Aproveite a análise comportamental interna e o aprendizado de máquina para identificar ataques e explorações de dia zero. Monitore redes, computadores e serviços de nuvem contra ataques recebidos e atividade pós-violação. Simplifique a investigação com ferramentas interativas e inteligência contextual contra ameaças.
- **Controles de acesso e de aplicativo** – Bloqueie malwares e outros aplicativos indesejados aplicando recomendações de lista de permissões adaptada para suas cargas de trabalho específicas e baseada em aprendizado de máquina. Reduza a superfície de ataque da rede com acesso controlado Just-In-Time às portas de gerenciamento em VMs do Azure, reduzindo drasticamente a exposição à ataques de força bruta e outros ataques de rede.


## <a name="next-steps"></a>Próximas etapas
Neste artigo, foram apresentados os preços da Central de Segurança. Para saber mais sobre a segurança aprimorada e a proteção avançada da camada Standard, consulte:

- [Detecção avançada de ameaças](security-center-threat-report.md)
- [Controle de acesso Just-In-Time à VM](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-pricing/get-standard.png
