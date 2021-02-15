---
title: Atualizar para o Azure Defender – Central de Segurança do Azure
description: Este início rápido mostrará como atualizar para o Azure Defender da Central de Segurança a fim de obter mais segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2020
ms.author: memildin
ms.openlocfilehash: d3ed064547b1202aa562fffdfde85a28cbe8514e
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/29/2020
ms.locfileid: "97809540"
---
# <a name="quickstart-set-up-azure-security-center"></a>Início Rápido: Configurar a Central de Segurança do Azure

A Central de Segurança do Azure fornece um gerenciamento de segurança unificado e proteção contra ameaças nas cargas de trabalho da sua nuvem híbrida. Enquanto os recursos gratuitos oferecem segurança limitada somente aos recursos do Azure, ao habilitar o Azure Defender você estende essas funcionalidades para o ambiente local e para outras nuvens. O Azure Defender ajuda a localizar e corrigir vulnerabilidades de segurança, aplicar controles de acesso e de aplicativo para bloquear atividades mal-intencionadas, detectar ameaças usando a análise e a inteligência e responder rapidamente a eventuais ataques. Você pode experimentar o Azure Defender sem custo. Para saber mais, consulte a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

Este início rápido orientará você na habilitação do Azure Defender para aumentar a segurança e na instalação do agente de Log Analytics nos computadores a fim de monitorar as vulnerabilidades e as ameaças de segurança.

Você executará as seguintes etapas:

> [!div class="checklist"]
> * Habilitar a Central de Segurança na sua assinatura do Azure
> * Habilitar o Azure Defender na assinatura do Azure
> * Habilitar a coleta de dados automática

## <a name="prerequisites"></a>Pré-requisitos
Para começar a usar a Central de Segurança, você deve ter uma assinatura do Microsoft Azure. Se você não tiver uma assinatura, pode se inscrever em uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

Para poder habilitar o Azure Defender em uma assinatura, é preciso que lhe seja atribuída a função de Proprietário da Assinatura, de Colaborador da Assinatura ou de Administrador de Segurança.


## <a name="enable-security-center-on-your-azure-subscription"></a>Habilitar a Central de Segurança na sua assinatura do Azure

1. Faça logon no [Portal do Azure](https://azure.microsoft.com/features/azure-portal/).

1. No menu do portal, selecione **Central de Segurança**. 

    A página de visão geral da Central de Segurança será exibida.

    :::image type="content" source="./media/security-center-get-started/overview.png" alt-text="Dashboard de visão geral da Central de Segurança" lightbox="./media/security-center-get-started/overview.png":::

**Central de Segurança – Visão geral** fornece uma exibição unificada sobre a postura de segurança das cargas de trabalho da sua nuvem híbrida, permitindo que você descubra e avalie a segurança de suas cargas de trabalho e identifique e reduza riscos. A Central de Segurança habilita automaticamente e sem custo qualquer uma das assinaturas do Azure não integradas anteriormente por você ou por outro usuário da assinatura.

Você pode visualizar e filtrar a lista de assinaturas selecionando o item de menu **Assinaturas**. A Central de Segurança ajustará a exibição para refletir a postura de segurança das assinaturas selecionadas. 

Em poucos minutos após a inicialização da Central de Segurança pela primeira vez, você poderá ver:

- **Recomendações** de maneiras para melhorar a segurança dos recursos conectados.
- Um inventário dos recursos que agora estão sendo avaliados pela Central de Segurança, juntamente com a postura de segurança de cada um deles.

Para aproveitar ao máximo a Central de Segurança, você precisará concluir as etapas abaixo para habilitar o Azure Defender e instalar o agente do Log Analytics.

> [!TIP]
> Para habilitar a Central de Segurança em todas as assinaturas de um grupo de gerenciamento, confira [Habilitar a Central de Segurança em várias assinaturas do Azure](onboard-management-group.md).

## <a name="enable-azure-defender"></a>Habilitar o Azure Defender

Para poder executar os inícios rápidos e os tutoriais da Central de Segurança, você deve habilitar o Azure Defender. Há uma avaliação gratuita de 30 dias disponível. Para saber mais, consulte a [página de preços](https://azure.microsoft.com/pricing/details/security-center/). 

1. Na barra lateral da Central de Segurança, selecione **Introdução**.

    :::image type="content" source="./media/security-center-get-started/get-started-upgrade-tab.png" alt-text="Guia de atualização da página de introdução"::: 

    A guia **Atualização** lista as assinaturas e os workspaces qualificados para a integração.

1. Na lista **Selecionar os workspaces nos quais habilitar o Azure Defender**, selecione os workspaces a serem atualizados.
   - Se você selecionar assinaturas e workspaces não qualificados para a avaliação, a próxima etapa os atualizará e dará inicio aos encargos.
   - Se você selecionar um workspace qualificado para uma avaliação gratuita, a próxima etapa iniciará uma avaliação.
1. Selecione **Atualizar** para habilitar o Azure Defender.

## <a name="enable-automatic-data-collection"></a>Habilitar a coleta de dados automática
A Central de Segurança coleta dados dos computadores a fim de monitorar as ameaças e as vulnerabilidades de segurança. Os dados são coletados usando o agente do Log Analytics, que lê uma variedade de configurações e logs de eventos relacionados à segurança do computador e copia os dados para o seu workspace visando a análise. Por padrão, a Central de Segurança criará um novo workspace para você.

Quando o provisionamento automático estiver habilitado, a Central de Segurança instalará o agente do Log Analytics em todos os computadores compatíveis, bem como naqueles que forem criados. O provisionamento automático é altamente recomendável.

Para habilitar o provisionamento automático do agente de Log Analytics:

1. No menu da Central de Segurança, selecione **Preço e configurações**.
1. Selecione a assinatura relevante.
1. Na página **Coleta de dados**, defina o **Provisionamento automático** como **Ativado**.
1. Selecione **Salvar**.

    :::image type="content" source="./media/security-center-enable-data-collection/enable-automatic-provisioning.png" alt-text="Habilitar o provisionamento automático do agente do Log Analytics":::

>[!TIP]
> Se for necessário provisionar um workspace, a instalação do agente poderá levar até 25 minutos.

Com o agente implantado em seus computadores, a Central de Segurança pode fornecer recomendações adicionais relacionadas ao status de atualização do sistema, configurações de segurança do SO, proteção de ponto de extremidade, além de gerar alertas de segurança adicionais.

>[!NOTE]
> A configuração do provisionamento automático como **Desativado** não removerá o agente do Log Analytics das VMs do Azure em que o agente já houver sido provisionado. Desabilitar o provisionamento automático limita o monitoramento de segurança dos seus recursos.



## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você habilitou o Azure Defender e provisionou o agente do Log Analytics para unificação do gerenciamento de segurança e da proteção contra ameaças nas cargas de trabalho de nuvem híbrida. Para saber mais sobre como usar a Central de Segurança, continue para o guia de início rápido para a integração de computadores com Windows locais e em outras nuvens.

> [!div class="nextstepaction"]
> [Início Rápido: integrar computadores não Azure](quickstart-onboard-machines.md)

Deseja otimizar e reduzir seus gastos com a nuvem?

> [!div class="nextstepaction"]
> [Comece a analisar os custos com o Gerenciamento de Custos](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png