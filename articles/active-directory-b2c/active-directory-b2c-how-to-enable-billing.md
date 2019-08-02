---
title: Como vincular uma assinatura do Azure – Azure Active Directory B2C | Microsoft Docs
description: Guia passo a passo para habilitar a cobrança de locatário do Azure AD B2C em uma assinatura do Azure.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 01/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5cc6c8b4947f8c5ebeb27be2dc82f323f84a6902
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835431"
---
# <a name="link-an-azure-subscription-to-an-azure-active-directory-b2c-tenant"></a>Vincular uma assinatura do Azure a um locatário do Azure Active Directory B2C

> [!IMPORTANT]
> Para obter as últimas informações sobre a cobrança de uso e os preços do Azure AD (Azure Active Directory) B2C, confira [Preços do Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

Os encargos de uso para o Azure AD B2C são cobrados de uma assinatura do Azure. Quando um locatário do Azure AD B2C é criado, o administrador de inquilinos precisa vincular explicitamente o locatário do Azure AD B2C a uma assinatura do Azure. Este artigo mostra como fazer isso.

> [!NOTE]
> Uma assinatura vinculada a um locatário do Azure AD B2C pode ser usada para a cobrança de uso do Azure AD B2C ou outros recursos do Azure, incluindo recursos adicionais do Azure AD B2C.  A assinatura não pode ser usada para adicionar outros serviços baseados em licença do Azure ou licenças do Office 365 no locatário do Azure AD B2C.

O link de assinatura é obtido com a criação de um “recurso” do Azure AD B2C na assinatura de destino do Azure. Vários “recursos” do Azure AD B2C podem ser vinculados a uma única assinatura do Azure juntamente com outros recursos do Azure (por exemplo, VMs, armazenamento de dados, Aplicativos Lógicos). Você pode ver todos os recursos na assinatura, indo para o locatário do Azure AD ao qual a assinatura está associada.

Há suporte para as assinaturas do CSP (Provedores de Soluções de Nuvem) do Azure no Azure AD B2C. A funcionalidade está disponível por meio de APIs ou pelo portal do Azure para o Azure AD B2C e todos os recursos do Azure. Os administradores de assinaturas do CSP podem vincular, mover e excluir relações com o Azure AD B2C da mesma forma que isso é feito para todos os recursos do Azure. O gerenciamento do Azure AD B2C usando o controle de acesso baseado em função não é afetado pela associação entre o locatário do Azure AD B2C e uma assinatura do Azure CSP. O controle de acesso baseado em função é obtido usando funções baseadas em locatário, não funções baseadas em assinatura.

Uma assinatura do Azure válida é necessária para continuar.

## <a name="create-an-azure-ad-b2c-tenant"></a>Criar um locatário do Azure AD B2C

Você deve primeiro [criar um locatário do Azure AD B2C](active-directory-b2c-get-started.md) ao qual deseja vincular uma assinatura. Ignore esta etapa se você já criou um locatário do Azure AD B2C.

## <a name="open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Abrir o portal do Azure no locatário do Azure AD que mostra a sua assinatura do Azure

Navegue até o locatário do Azure AD que mostra a sua assinatura do Azure. Abra o [portal do Azure](https://portal.azure.com) e alterne para o locatário do Azure AD que mostra a assinatura do Azure que você deseja usar.

![Alternando para o locatário do Azure AD](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="find-azure-ad-b2c-in-the-azure-marketplace"></a>Localizar Azure AD B2C no Azure Marketplace

Clique no botão **Criar um recurso**. No **pesquisar no Marketplace** , insira `Active Directory B2C`.

![Captura de tela portal com 'B2C do Active Directory' na pesquisa de mercado](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c.png)

Na lista de resultados, selecione **Azure AD B2C**.

![Azure AD B2C selecionado na lista de resultados](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c-result.png)

Os detalhes sobre o Azure AD B2C são mostrados. Para começar a configurar seu novo locatário do Azure Active Directory B2C, clique no botão **Criar**.

Na tela de criação de recursos, selecione **Vincular um Azure AD B2C existente à minha assinatura do Azure**.

## <a name="create-an-azure-ad-b2c-resource-within-the-azure-subscription"></a>Criar um recurso do Azure AD B2C na assinatura do Azure

Na caixa de diálogo de criação de recursos, selecione um locatário do Azure AD B2C na lista suspensa. Aparecerão todos os locatários para os quais você é o administrador global e aqueles que não estão vinculadas a uma assinatura.

O nome do recurso do Azure AD B2C será pré-selecionado para corresponder ao nome de domínio do locatário do Azure AD B2C.

Em Assinatura, selecione uma assinatura ativa do Azure que você administra.

Selecione um Grupo de Recursos e o local do Grupo de Recursos. Aqui, a seleção não tem impacto sobre o local, desempenho ou status de cobrança do locatário do Azure AD B2C.

![A página de criação de recursos do Azure AD B2C no portal do Azure](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Gerenciar seus recursos de locatário do Azure AD B2C

Depois que um recurso do Azure AD B2C é criado com êxito na assinatura do Azure, você verá um novo recurso do tipo "locatário do B2C" adicionado aos seus outros recursos do Azure.

Você pode usar esse recurso para:

- Navegar até a assinatura para revisar as informações de cobrança.
- Ir até seu locatário do Azure AD B2C
- Enviar uma solicitação de suporte
- Mover o recurso de locatário do Azure AD B2C para outra assinatura do Azure ou para outro Grupo de Recursos.

![Página de configurações de recursos do B2C no portal do Azure](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.PNG)

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Alterar o locatário do Azure AD B2C assinatura de cobrança

Os locatários do Azure AD B2C podem ser movidos para outra assinatura, se as assinaturas de origem e de destino existir no mesmo locatário do Active Directory do Azure.

Para saber como mover recursos do Azure como seu locatário do Azure AD B2C para outra assinatura, consulte [mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/resource-group-move-resources.md).

Antes de iniciar a movimentação, certifique-se de ler o artigo inteiro para compreender totalmente as limitações e requisitos para uma movimentação desse tipo. Além de instruções para mover os recursos, ele inclui informações críticas, como uma lista de verificação de pré-mover e como validar a operação de movimentação.

## <a name="known-issues"></a>Problemas Conhecidos

### <a name="self-imposed-restrictions"></a>Restrições autoimpostas

Um usuário pode ter estabelecido uma restrição regional para a criação de recursos do Azure. Essa restrição pode impedir a criação de recursos do Azure AD B2C. Para atenuar, reduza essa restrição.

## <a name="next-steps"></a>Próximas etapas

Quando essas etapas forem concluídas para cada um dos seus locatários do Azure AD B2C, sua assinatura do Azure será cobrada de acordo com os detalhes do Azure Direct ou Enterprise Agreement.

Você pode revisar os detalhes de cobrança e uso dentro da assinatura do Azure selecionada. Você também pode rever os relatórios de uso detalhado do dia a dia usando a [API de Relatórios de Uso](active-directory-b2c-reference-usage-reporting-api.md).
