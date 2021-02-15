---
title: Alertas de segurança para ambientes no Azure DevTest Labs
description: Este artigo mostra como exibir alertas de segurança para um ambiente no DevTest Labs e tomar uma ação apropriada.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: c9f7cf9fe7ab0e3f573470228ee1962aa92ccaef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91308683"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Alertas de segurança para ambientes no Azure DevTest Labs
Como um usuário de laboratório, agora você pode exibir alertas da central de segurança do Azure para seus ambientes de laboratório. A Central de segurança coleta, analisa e integra automaticamente os dados de registro de seus recursos do Azure, da rede e das soluções de parceiros conectados, como firewall e soluções de proteção de ponto de extremidade, a fim de detectar ameaças reais e reduzir os falsos positivos. Uma lista priorizada de alertas de segurança é exibida na Central de Segurança, junto com as informações necessárias para investigar rapidamente o problema, e recomendações sobre como corrigir um ataque. [Saiba mais sobre alertas de segurança na central de segurança do Azure](../security-center//security-center-alerts-overview.md).  


## <a name="prerequisites"></a>Pré-requisitos
No momento, você pode exibir alertas de segurança somente para ambientes de PaaS (plataforma como serviço) implantados em seu laboratório. Para testar ou usar esse recurso, [implante um ambiente em seu laboratório](devtest-lab-create-environment-from-arm.md). 

## <a name="view-security-alerts-for-an-environment"></a>Exibir alertas de segurança para um ambiente

1. No home page para seu laboratório, selecione **alertas de segurança** no menu à esquerda. Você deve ver o número de alertas de segurança (alta, média e baixa). Saiba mais sobre [como os alertas são classificados](../security-center/security-center-alerts-overview.md#how-are-alerts-classified).

    ![Alertas de segurança-visão geral](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Clique com o botão direito do mouse em três pontos (...) na última coluna e selecione **exibir alertas de segurança**. 

    ![Captura de tela que mostra a página alertas de segurança com "exibir alertas de segurança" selecionado.](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. Você verá mais detalhes sobre as recomendações de alertas e do assistente. Saiba mais sobre como [gerenciar e responder a alertas de segurança na central de segurança do Azure](../security-center/security-center-managing-and-responding-alerts.md).

    ![Exibir alertas de segurança](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre ambientes, consulte os seguintes artigos:

- [Criar ambientes de várias VMs e recursos de PaaS com modelos de Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurar e usar ambientes públicos](devtest-lab-configure-use-public-environments.md)
