---
title: Modelo de cobrança do MAU para identidades externas do Azure AD
description: Saiba mais sobre o modelo de cobrança de usuários ativos mensais (MAU) de identidades externas do Azure AD para a B2B (colaboração do usuário convidado) no Azure AD. Saiba como vincular seu locatário do Azure AD a uma assinatura do Azure.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.workload: identity
ms.collection: M365-identity-device-management
ms.openlocfilehash: 983a803245467145a0b1161a4495e8045759e7ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92442058"
---
# <a name="billing-model-for-azure-ad-external-identities"></a>Modelo de cobrança para identidades externas do Azure AD

O preço das identidades externas do Azure Active Directory (AD do Azure) é baseado em MAU (usuários ativos mensais), que é a contagem de usuários exclusivos com atividade de autenticação em um mês civil. Esse modelo de cobrança aplica-se aos [locatários](../../active-directory-b2c/billing.md)de B2B (colaboração do usuário convidado) do Azure AD e Azure ad B2C. A cobrança do MAU ajuda a reduzir os custos oferecendo uma camada gratuita e preços flexíveis e previsíveis. Neste artigo, saiba mais sobre a cobrança do MAU e vinculando seus locatários do Azure AD a uma assinatura.

> [!IMPORTANT]
> Este artigo não contém detalhes de preços. Para obter as informações mais recentes sobre cobrança e preços de uso, consulte [preços de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-i-need-to-do"></a>O que preciso fazer?

Para aproveitar a cobrança do MAU, seu locatário do Azure AD deve estar vinculado a uma assinatura do Azure.

|Se seu locatário for:  |Você precisa:  |
|---------|---------|
| Um locatário do Azure AD já vinculado a uma assinatura     | Não fazer nada. Ao usar os recursos de identidades externas para colaborar com os usuários convidados, você será cobrado automaticamente usando o modelo MAU.        |
| Um locatário do Azure AD ainda não está vinculado a uma assinatura     | [Vincule seu locatário do Azure ad a uma assinatura](#link-your-azure-ad-tenant-to-a-subscription) para ativar a cobrança do mau.        |
|  |  |

## <a name="about-monthly-active-users-mau-billing"></a>Sobre a cobrança de usuários ativos mensais (MAU)

Em seu locatário do Azure AD, o uso da colaboração do usuário convidado é cobrado com base na contagem de usuários convidados exclusivos com atividade de autenticação em um mês civil. Esse modelo substitui o modelo de cobrança com proporção de 1:5, que permitia até cinco usuários convidados para cada licença do Azure AD Premium no locatário. Quando o locatário estiver vinculado a uma assinatura e você usar recursos de Identidades Externas para colaborar com usuários convidados, você será cobrado automaticamente de acordo com o modelo de cobrança baseado em MAU.
  
O tipo de preço que se aplica a seus usuários convidados baseia-se no tipo de preço mais alto atribuído ao seu locatário do Azure AD. Por exemplo, se o tipo de preço mais alto em seu locatário for Azure AD Premium P1, o tipo de preço premium P1 também se aplicará aos seus usuários convidados. Se o preço mais alto for Azure AD Gratuito, você será solicitado a atualizar para um tipo de preço premium ao tentar usar os recursos premium para usuários convidados.

## <a name="link-your-azure-ad-tenant-to-a-subscription"></a>Vincular seu locatário do Azure AD a uma assinatura

Um locatário do Azure AD deve estar vinculado a uma assinatura do Azure para obter a cobrança e o acesso adequados aos recursos. Se o diretório ainda não tiver uma assinatura à qual você possa se vincular, você terá a oportunidade de adicionar um durante esse processo.

1. Entre no [portal do Azure](https://portal.azure.com/) com uma conta do Azure que tenha sido atribuída pelo menos a função [colaborador](../../role-based-access-control/built-in-roles.md) dentro da assinatura ou um grupo de recursos dentro da assinatura.

2. Selecione o diretório que você deseja vincular: na barra de ferramentas portal do Azure, selecione o **diretório +** ícone de assinatura e, em seguida, selecione o diretório.

    ![Selecione o diretório + ícone de assinatura](media/external-identities-pricing/portal-mau-pick-directory.png)

3. Em **Serviços do Azure**, selecione **Azure Active Directory**.

4. No menu à esquerda, selecione **Identidades Externas**.

5. Em **assinaturas**, selecione **assinaturas vinculadas**.

6. Na lista locatário, marque a caixa de seleção ao lado do locatário e, em seguida, selecione **vincular assinatura**.

    ![Selecionar o locatário e vincular uma assinatura](media/external-identities-pricing/linked-subscriptions.png)

7. No painel vincular uma assinatura, selecione uma **assinatura** e um **grupo de recursos**. Em seguida, selecione **Aplicar**.

   > [!NOTE]
   > Se não houver assinaturas listadas, você poderá [associar uma assinatura ao seu locatário](../fundamentals/active-directory-how-subscriptions-associated-directory.md). Ou então, você pode adicionar uma nova assinatura selecionando o link **se ainda não tiver uma assinatura, você pode criar uma aqui**.

    ![Selecionar uma assinatura e um grupo de recursos](media/external-identities-pricing/link-subscription-resource.png)

Depois de concluir essas etapas, sua assinatura do Azure será cobrada com base nos detalhes do Azure Direct ou Contrato Enterprise, se aplicável.

## <a name="next-steps"></a>Próximas etapas

Para obter as informações de preços mais recentes, consulte [preços de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).