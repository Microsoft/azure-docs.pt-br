---
title: Atualizar para o Azure Defender – Central de Segurança do Azure
description: Este início rápido mostrará como atualizar para o Azure Defender da Central de Segurança a fim de obter mais segurança.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 2f37a59d5db3883754b602b2b2525e07b57206b7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102099345"
---
# <a name="quickstart-set-up-azure-security-center"></a>Início Rápido: Configurar a Central de Segurança do Azure

A Central de Segurança do Azure fornece gerenciamento de segurança unificado e proteção contra ameaças nas cargas de trabalho híbridas e de várias nuvens. Enquanto os recursos gratuitos oferecem segurança limitada somente aos recursos do Azure, ao habilitar o Azure Defender você estende essas funcionalidades para o ambiente local e para outras nuvens. O Azure Defender ajuda a localizar e corrigir vulnerabilidades de segurança, aplicar controles de acesso e de aplicativo para bloquear atividades mal-intencionadas, detectar ameaças usando a análise e a inteligência e responder rapidamente a eventuais ataques. Você pode experimentar o Azure Defender sem custo. Para saber mais, consulte a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

Esta seção do guia de início rápido orientará você em todas as etapas recomendadas para habilitar a Central de Segurança do Azure e o Azure Defender. Ao concluir todas as etapas do guia de início rápido, você terá:

> [!div class="checklist"]
> * A Central de Segurança habilitada nas suas assinaturas do Azure
> * O Azure Defender habilitado nas assinaturas do Azure
> * A coleta automática de dados configurada
> * Notificações por email configuradas para alertas de segurança
> * Os seus computadores híbridos e de várias nuvens conectados ao Azure

## <a name="prerequisites"></a>Pré-requisitos
Para começar a usar a Central de Segurança, você deve ter uma assinatura do Microsoft Azure. Se você não tiver uma assinatura, pode se inscrever em uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

Para poder habilitar o Azure Defender em uma assinatura, é preciso que lhe seja atribuída a função de Proprietário da Assinatura, de Colaborador da Assinatura ou de Administrador de Segurança.

## <a name="enable-security-center-on-your-azure-subscription"></a>Habilitar a Central de Segurança na sua assinatura do Azure

> [!TIP]
> Para habilitar a Central de Segurança em todas as assinaturas de um grupo de gerenciamento, confira [Habilitar a Central de Segurança em várias assinaturas do Azure](onboard-management-group.md).

1. Faça logon no [Portal do Azure](https://azure.microsoft.com/features/azure-portal/).

1. No menu do portal, selecione **Central de Segurança**. 

    A página de visão geral da Central de Segurança será exibida.

    :::image type="content" source="./media/security-center-get-started/overview.png" alt-text="Dashboard de visão geral da Central de Segurança" lightbox="./media/security-center-get-started/overview.png":::

    **Central de Segurança – Visão geral** fornece uma exibição unificada sobre a postura de segurança das cargas de trabalho da sua nuvem híbrida, permitindo que você descubra e avalie a segurança de suas cargas de trabalho e identifique e reduza riscos. A Central de Segurança habilita automaticamente e sem custo qualquer uma das assinaturas do Azure não integradas anteriormente por você ou por outro usuário da assinatura.

Você pode visualizar e filtrar a lista de assinaturas selecionando o item de menu **Assinaturas**. A Central de Segurança ajustará a exibição para refletir a postura de segurança das assinaturas selecionadas. 

Em poucos minutos após a inicialização da Central de Segurança pela primeira vez, você poderá ver:

- **Recomendações** de maneiras para melhorar a segurança dos recursos conectados.
- Um inventário dos recursos que agora estão sendo avaliados pela Central de Segurança, juntamente com a postura de segurança de cada um deles.

Para aproveitar ao máximo a Central de Segurança, continue com as próximas etapas da seção do guia de início rápido.



## <a name="next-steps"></a>Próximas etapas
Neste guia de início rápido, você habilitou a Central de Segurança do Azure. A próxima etapa é habilitar o Azure Defender para gerenciamento de segurança unificada e proteção contra ameaças nas suas cargas de trabalho de nuvem híbrida.

> [!div class="nextstepaction"]
> [Guia de início rápido: Habilitar o Azure Defender](enable-azure-defender.md)