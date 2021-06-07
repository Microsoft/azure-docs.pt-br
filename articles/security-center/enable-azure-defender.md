---
title: Habilitar proteções de carga de trabalho integradas da Central de Segurança do Azure
description: Saiba como habilitar o Azure Defender para estender as proteções da Central de Segurança do Azure para os seus recursos híbridos e de várias nuvens
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 02/24/2021
ms.openlocfilehash: 7124014821c79fa37aa04da8909e3b4ac3bcb4fb
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492487"
---
# <a name="quickstart-enable-azure-defender"></a>Guia de início rápido: Habilitar o Azure Defender

Para saber mais sobre os benefícios do Azure Defender, confira a [Central de Segurança gratuita vs. Azure Defender habilitado](security-center-pricing.md).

## <a name="prerequisites"></a>Pré-requisitos

Para poder executar os inícios rápidos e os tutoriais da Central de Segurança, você deve habilitar o Azure Defender. 

Você pode proteger uma assinatura inteira do Azure com o Azure Defender e as proteções serão herdadas por todos os recursos na assinatura.

Há uma avaliação gratuita de 30 dias disponível. Para obter detalhes de preço na sua moeda preferencial e de acordo com a sua região, confira [Preço da Central de Segurança](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-azure-defender-from-the-azure-portal"></a>Habilitar o Azure Defender por meio do portal do Azure

Para habilitar todos os recursos da Central de Segurança, incluindo as funcionalidades de proteção contra ameaças, habilite o Azure Defender na assinatura que contém as cargas de trabalho aplicáveis. A habilitação dele no nível do workspace não habilita o acesso just-in-time à VM, os controles de aplicativo adaptáveis nem as detecções de rede para os recursos do Azure. Além disso, os únicos planos do Azure Defender disponíveis no nível do workspace são para servidores e para SQL Server em máquinas.

- Habilite o **Azure Defender para contas de armazenamento** no nível da assinatura ou do recurso
- Você pode habilitar o **Azure Defender para SQL** no nível da assinatura ou do recurso
- Você pode habilitar a proteção contra ameaças do **Banco de Dados do Azure para MariaDB/MySQL/PostgreSQL** apenas no nível do recurso

### <a name="to-enable-azure-defender-on-your-subscriptions-and-workspaces"></a>Para habilitar o Azure Defender nas suas assinaturas e workspaces:

- Para habilitar o Azure Defender em uma assinatura:

    1. No menu principal da Central de Segurança, selecione **Preços e configurações**.
    1. Selecione a assinatura ou o workspace que você deseja proteger.
    1. Escolha **Azure Defender ativado** para fazer a atualização.
    1. Clique em **Salvar**.

    > [!TIP]
    > Você observará que cada plano do Azure Defender é cobrado separadamente e pode ser definido de maneira individual como ativado ou desativado. Por exemplo, talvez você queira desligar o Azure Defender para Serviço de Aplicativo em assinaturas que não têm um Plano do Serviço de Aplicativo do Azure associado. 

    :::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="Página de preços da Central de Segurança no portal":::

- Para habilitar o Azure Defender em várias assinaturas ou workspaces:

    1. Na barra lateral da Central de Segurança, selecione **Introdução**.

        A guia **Atualização** lista as assinaturas e os workspaces qualificados para a integração.

        :::image type="content" source="./media/enable-azure-defender/get-started-upgrade-tab.png" alt-text="Guia de atualização da página de introdução"::: 

    1. Na lista **Selecionar assinaturas e workspaces nas quais habilitar o Azure Defender**, selecione as assinaturas e os workspaces a serem atualizados e selecione **Atualizar** para habilitar o Azure Defender.

       - Se você selecionar assinaturas e workspaces não qualificados para a avaliação, a próxima etapa os atualizará e dará inicio aos encargos.
       - Se você selecionar um workspace qualificado para uma avaliação gratuita, a próxima etapa iniciará uma avaliação.

        :::image type="content" source="./media/enable-azure-defender/upgrade-selected-workspaces-and-subscriptions.png" alt-text="Atualizar todos os workspaces e assinaturas selecionados da página de introdução":::


## <a name="next-steps"></a>Próximas etapas

Agora que você habilitou o Azure Defender, habilite a coleta automática de dados pelos agentes e extensões necessários descritos em [agentes de provisionamento automático e extensões da Central de Segurança do Azure](security-center-enable-data-collection.md).