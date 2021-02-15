---
title: Gerenciar vários locatários no Azure Sentinel como um provedor de serviços de segurança gerenciado | Microsoft Docs
description: Como integrar e gerenciar vários locatários no Azure Sentinel como um MSSP (provedor de serviços de segurança gerenciado) usando o Azure Lighthouse.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2020
ms.author: yelevin
ms.openlocfilehash: 011ddb883c028a954a8b0683c220bf6341eddb66
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578132"
---
# <a name="manage-multiple-tenants-in-azure-sentinel-as-an-mssp"></a>Gerenciar vários locatários no Azure Sentinel como um MSSP

Se você for um MSSP (provedor de serviços de segurança gerenciado) e estiver usando o [Azure Lighthouse](../lighthouse/overview.md) para oferecer serviços de SOC (central de operações de segurança) para seus clientes, você poderá gerenciar os recursos do Azure Sentinel para seus clientes diretamente do seu próprio locatário do Azure, sem precisar se conectar ao locatário do cliente. 

## <a name="prerequisites"></a>Pré-requisitos

- [Integração do Azure Lighthouse](../lighthouse/how-to/onboard-customer.md)

- Para que isso funcione corretamente, seu locatário (o locatário MSSP) deve ter os provedores de recursos do Azure Sentinel registrados em pelo menos uma assinatura. Além disso, cada um dos locatários de seus clientes deve ter os provedores de recursos registrados. Se você registrou o Azure Sentinel em seu locatário e seus clientes em seus próprios, você está pronto para começar. Para verificar o registro, execute as seguintes etapas:

    1. Selecione **assinaturas** no portal do Azure e, em seguida, selecione uma assinatura relevante no menu.

    1. No menu de navegação da tela de assinatura, em **configurações**, selecione **provedores de recursos**.

    1. Do ** *nome da assinatura* | Provedores de recursos** tela, procure e selecione *Microsoft. OperationalInsights* e *Microsoft. SecurityInsights*e verifique a coluna **status** . Se o status do provedor for não *registrado*, selecione **registrar**.
    
        :::image type="content" source="media/multiple-tenants-service-providers/check-resource-provider.png" alt-text="Verificar provedores de recursos":::

## <a name="how-to-access-azure-sentinel-in-managed-tenants"></a>Como acessar o Azure Sentinel em locatários gerenciados

1. Em **diretório + assinatura**, selecione os diretórios delegados (diretório = locatário) e as assinaturas onde os espaços de trabalho do Azure Sentinel do seu cliente estão localizados.

    :::image type="content" source="media/multiple-tenants-service-providers/directory-subscription.png" alt-text="Verificar provedores de recursos":::

1. Abra o Azure Sentinel. Você verá todos os espaços de trabalho nas assinaturas selecionadas e poderá trabalhar com eles diretamente, como qualquer espaço de trabalho em seu próprio locatário.

> [!NOTE]
> Você não poderá implantar conectores no Azure Sentinel de dentro de um espaço de trabalho gerenciado. Para implantar um conector, você deve entrar diretamente no locatário no qual deseja implantar um conector e autenticar-se nele com as permissões necessárias.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a gerenciar vários locatários do Azure Sentinel diretamente. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).

