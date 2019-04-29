---
title: Atualizar para a camada Standard da Central de Segurança para segurança aprimorada | Microsoft Docs
description: Este artigo fornece informações sobre os preços para a Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/17/2019
ms.author: monhaber
ms.openlocfilehash: 27acda2496adea39321e498868aebcc2f824df3c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60905715"
---
# <a name="upgrade-to-security-centers-standard-tier-for-enhanced-security"></a>Atualizar para a camada Standard da Central de Segurança para segurança aprimorada
A Central de Segurança do Azure fornece gerenciamento de segurança unificado e proteção avançada contra ameaças para cargas de trabalho em execução no Azure, localmente e em outras nuvens. Ela proporciona visibilidade e controle sobre cargas de trabalho de nuvem híbrida, defesas ativas que reduzem a exposição a ameaças e detecção inteligente para ajudá-lo a acompanhar o ritmo veloz da evolução dos ataques cibernéticos.

## <a name="pricing-tiers"></a>Tipos de preço
A Central de Segurança é oferecida em duas camadas:

- A camada **Gratuita** é habilitada automaticamente em todas as assinaturas do Azure e oferece política de segurança, avaliação de segurança contínua e recomendações de práticas de segurança para ajudar a proteger seus recursos do Azure.
- A camada **Standard** estende os recursos da Camada gratuita para cargas de trabalho em execução em outras nuvens públicas e privadas, fornecendo gerenciamento unificado de segurança e proteção contra ameaças em suas cargas de trabalho de nuvem híbrida. A camada Standard também adiciona recursos avançados de detecção de ameaças, que usam análise comportamental interna e aprendizado de máquina para identificar ataques e explorações de dia zero, controles de acesso e de aplicativos a fim de reduzir a exposição a ataques de rede e malware, e muito mais. Você pode experimentar gratuitamente a camada Standard. Central de segurança Standard oferece suporte ao Azure recursos, incluindo máquinas virtuais, VM conjuntos de dimensionamento, o serviço de aplicativo, servidores SQL e contas de armazenamento. Se você tivera Central de Segurança do Azure Standard pode recusar o suporte com base no tipo de recurso. 


Para obter mais informações, confira a [página de preços](https://azure.microsoft.com/pricing/details/security-center/) da Central de Segurança.

## <a name="try-standard-free-for-30-days"></a>Experimentar a camada Standard gratuitamente por 30 dias
A camada Standard é oferecida gratuitamente nos 30 primeiros dias. Ao fim dos 30 dias, se você optar por continuar usando o serviço, começaremos a cobrar automaticamente pelo uso.

Você pode atualizar uma assinatura inteira do Azure para a camada Standard, que será herdada por todos os recursos na assinatura.

Para obter a camada Standard:

1. Selecione **Política de Segurança** no menu principal da **Central de Segurança**.
2. Selecione a assinatura que você deseja atualizar para o Padrão.
3. Na folha **Política de segurança**, selecione **Camada de preços**.
4. Selecione **Standard** para atualizar.
5. Clique em **Salvar**.

(Os preços na imagem por exemplo são apenas para fins de). ![Preços da Central de segurança](./media/security-center-pricing/get-standard.png)

> [!NOTE]
> Para habilitar todos os recursos da Central de segurança, você deve aplicar o padrão de tipo de preço para a assinatura que contém as máquinas virtuais aplicáveis. A configuração de preços para um workspace não habilita o acesso Just-In-Time à VM, os controles de aplicativo adaptáveis e as detecções de rede para os recursos do Azure.
>
>

## <a name="why-upgrade-to-standard"></a>Por que atualizar para Padrão?
A Central de Segurança oferece maior segurança e proteção contra ameaças para suas cargas de trabalho de nuvem híbrida, incluindo:

- **Segurança híbrida** – Obtenha uma exibição unificada sobre a segurança em todas as suas cargas de trabalho locais e na nuvem. Aplique políticas de segurança e avalie continuamente a segurança de suas cargas de trabalho de nuvem híbrida a fim de garantir a conformidade com padrões de segurança. Colete, pesquise e analise dados de segurança de uma variedade de fontes, inclusive firewalls e outras soluções de parceiros.
- **Detecção avançada de ameaças** – Use análises avançadas e o Grafo de segurança inteligente da Microsoft para obter uma vantagem sobre ataques cibernéticos em evolução.  Aproveite a análise comportamental interna e o aprendizado de máquina para identificar ataques e explorações de dia zero. Monitore redes, computadores e serviços de nuvem contra ataques recebidos e atividade pós-violação. Simplifique a investigação com ferramentas interativas e inteligência contextual contra ameaças.
- **Controles de acesso e de aplicativo** – Bloqueie malwares e outros aplicativos indesejados aplicando recomendações de lista de permissões adaptada para suas cargas de trabalho específicas e baseada em aprendizado de máquina. Reduza a superfície de ataque da rede com acesso controlado Just-In-Time às portas de gerenciamento em VMs do Azure, reduzindo drasticamente a exposição à ataques de força bruta e outros ataques de rede.


## <a name="next-steps"></a>Próximas etapas
Neste artigo, foram apresentados os preços da Central de Segurança. Para saber mais sobre a segurança aprimorada e a proteção avançada da camada Standard, consulte:

- [Detecção avançada de ameaças](security-center-threat-report.md)
- [Controle de acesso Just-In-Time à VM](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-pricing/get-standard.png
