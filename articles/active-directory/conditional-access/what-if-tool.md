---
title: A ferramenta de What If de acesso condicional-Azure Active Directory
description: Saiba como você pode entender o impacto de suas políticas de acesso condicional em seu ambiente.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3ac799203cade3a907acbe28dee3a8023891db2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93077635"
---
# <a name="troubleshoot-using-the-what-if-tool-in-conditional-access"></a>Solucionar problemas usando a ferramenta What If no Acesso Condicional

O [acesso condicional](./overview.md) é um recurso do Azure Active Directory (AD do Azure) que permite controlar como os usuários autorizados acessam seus aplicativos de nuvem. Como você sabe o que esperar das políticas de acesso condicional em seu ambiente? Para responder a essa pergunta, você pode usar a **ferramenta de What If de acesso condicional** .

Este artigo explica como você pode usar essa ferramenta para testar suas políticas de acesso condicional.

## <a name="what-it-is"></a>O que é

A **ferramenta de política de What If de acesso condicional** permite que você entenda o impacto de suas políticas de acesso condicional em seu ambiente. Em vez de testar as suas políticas executando várias entradas manualmente, essa ferramenta permite que você avalie um logon simulado de um usuário. A simulação calcula o impacto que esse logon tem em suas políticas e gera um relatório de simulação. O relatório não lista apenas as políticas de acesso condicional aplicadas, mas também [as políticas clássicas](policy-migration.md#classic-policies) , se existirem.    

A ferramenta de **What If** fornece uma maneira de determinar rapidamente as políticas que se aplicam a um usuário específico. Você pode usar as informações, por exemplo, se você precisar solucionar um problema.    

## <a name="how-it-works"></a>Como ele funciona

Na **ferramenta de What If de acesso condicional** , primeiro você precisa definir as configurações do cenário de entrada que deseja simular. Essas configurações incluem:

- O usuário que você deseja testar 
- Os aplicativos de nuvem que o usuário tente acessar
- As condições sob as quais o acesso aos aplicativos de nuvem configurados são executadas
     
Como uma próxima etapa, você pode iniciar uma simulação que avalia suas configurações. Somente as políticas que estão habilitadas fazem parte de uma execução de avaliação.

Terminada a avaliação, a ferramenta gera um relatório das políticas afetadas. Para coletar mais informações sobre uma política de acesso condicional, a [pasta de trabalho informações de acesso condicional e relatórios](howto-conditional-access-insights-reporting.md) pode fornecer detalhes adicionais sobre as políticas no modo somente de relatório e as políticas atualmente habilitadas.

## <a name="running-the-tool"></a>A execução da ferramenta

Você pode encontrar a ferramenta **What If** na página **[acesso condicional-políticas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** no portal do Azure.

Para iniciar a ferramenta, na barra de ferramentas na parte superior da lista de políticas, clique em **What If** .

:::image type="content" source="./media/what-if-tool/01.png" alt-text="Captura de tela da página de políticas de acesso condicional no portal do Azure. Na barra de ferramentas, o item What If é realçado." border="false":::

Antes de executar uma avaliação, você deve configurar as definições.

## <a name="settings"></a>Configurações

Esta seção fornece informações sobre as configurações de execução de simulação.

:::image type="content" source="./media/what-if-tool/02.png" alt-text="Captura de tela da página de políticas de acesso condicional no portal do Azure. Na barra de ferramentas, o item What If é realçado." border="false":::

### <a name="user"></a>Usuário

Você só pode selecionar um usuário. Esse é o único campo obrigatório.

### <a name="cloud-apps"></a>Aplicativos na nuvem

O padrão para essa configuração é **Todos os aplicativos de nuvem** . A configuração padrão executa uma avaliação de todas as políticas disponíveis em seu ambiente. Você pode restringir o escopo para políticas que afetem aplicativos de nuvem específicos.

### <a name="ip-address"></a>Endereço IP

O endereço IP é um único endereço IPv4 para imitar a [condição local](location-condition.md). O endereço representa o endereço de internet do dispositivo usado pelo usuário para fazer acesso. Você pode verificar o endereço IP de um dispositivo, por exemplo, navegando até [Qual é o meu endereço IP](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Plataformas de dispositivo

Essa configuração imita a [condição de plataformas de dispositivo](concept-conditional-access-conditions.md#device-platforms) e representa o equivalente a **todas as plataformas (incluindo plataformas sem suporte)** . 

### <a name="client-apps"></a>Aplicativos cliente

Essa configuração imita a [condição de aplicativos do cliente](concept-conditional-access-conditions.md#client-apps).
Por padrão, essa configuração faz com que uma avaliação de todas as políticas com **navegador** ou **aplicativos móveis e clientes de desktop** sejam cada ou ambos selecionados. Ela também detecta políticas que impõem o **Exchange ActiveSync (EAS)** . Você pode reduzir essa configuração selecionando:

- **Navegador** para avaliar todas as políticas com menos **Navegador** selecionado. 
- **Aplicativos móveis e clientes de desktop** avaliando todas as políticas com pelo menos **aplicativos móveis e clientes de desktop** selecionado. 

### <a name="sign-in-risk"></a>Risco de entrada

Essa configuração imita a [condição de risco de logon](concept-conditional-access-conditions.md#sign-in-risk).   

## <a name="evaluation"></a>Avaliação 

Você inicia uma avaliação clicando em **What If** . O resultado da avaliação fornece um relatório que consiste em: 

:::image type="content" source="./media/what-if-tool/03.png" alt-text="Captura de tela da página de políticas de acesso condicional no portal do Azure. Na barra de ferramentas, o item What If é realçado." border="false":::

- Um indicador de se as políticas clássicas existem no seu ambiente
- Políticas que se aplicam ao usuário
- Políticas que não se aplicam ao usuário

Se [políticas clássicas](policy-migration.md#classic-policies) existirem para os aplicativos de nuvem selecionados, um indicador é apresentado a você. Clicando no indicador, você é redirecionado para a página de políticas clássicas. Na página de políticas clássicas, você pode migrar uma política clássica ou simplesmente desabilitá-la. Você pode retornar para o resultado da avaliação fechando essa página.

Na lista de políticas que se aplicam ao usuário selecionado, você também pode encontrar uma lista de [controles de concessão](concept-conditional-access-grant.md) e de [sessão](concept-conditional-access-session.md) que seu usuário deve atender.

Na lista de políticas que não se aplicam ao usuário, você pode também encontrar os motivos pelos quais essas políticas não se aplicam. Para cada política listada, o motivo representa a primeira condição que não foi atendida. Um motivo possível para uma política que não é aplicada é uma política desabilitada por não ter sido melhor avaliada.   

## <a name="next-steps"></a>Próximas etapas

- Mais informações sobre o aplicativo de política de acesso condicional podem ser encontradas usando o modo somente relatório de políticas usando [informações e relatórios de acesso condicional](howto-conditional-access-insights-reporting.md).
- Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, consulte as [políticas comuns de acesso condicional](concept-conditional-access-policy-common.md).
