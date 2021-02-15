---
title: Alterar a SKU de um Azure AD Domain Services | Microsoft Docs
description: Saiba como a camada de SKU para um domínio Azure AD Domain Services gerenciado se os requisitos de negócios forem alterados
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 320bd87aa78d26cee44c48f27365febd1dd426ff
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620283"
---
# <a name="change-the-sku-for-an-existing-azure-active-directory-domain-services-managed-domain"></a>Alterar a SKU de um domínio gerenciado Azure Active Directory Domain Services existente

No Azure Active Directory Domain Services (AD DS do Azure), o desempenho e os recursos disponíveis são baseados no tipo de SKU. Essas diferenças de recursos incluem a frequência de backup ou o número máximo de relações de confiança de floresta de saída unidirecionais.

Você seleciona uma SKU ao criar o domínio gerenciado e pode alternar os SKUs para cima ou para baixo conforme suas necessidades de negócios mudam depois que o domínio gerenciado tiver sido implantado. As alterações nos requisitos de negócios podem incluir a necessidade de backups mais frequentes ou a criação de relações de confiança de floresta adicionais. Para obter mais informações sobre os limites e os preços das diferentes SKUs, consulte [conceitos do azure AD DS SKU][concepts-sku] e páginas de [preços do Azure AD DS][pricing] .

Este artigo mostra como alterar a SKU de um domínio gerenciado AD DS do Azure existente usando o portal do Azure.

## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você precisará dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, conclua o tutorial para [criar e configurar um domínio gerenciado][create-azure-ad-ds-instance].

## <a name="sku-change-limitations"></a>Limitações de alteração de SKU

Você pode alterar os SKUs para cima ou para baixo depois que o domínio gerenciado tiver sido implantado. No entanto, se você usar uma floresta de recursos e tiver criado relações de confiança de uma floresta de saída unidirecional do Azure AD DS para um ambiente de AD DS local, haverá algumas limitações para a operação de alteração de SKU. As SKUs *Premium* e *Enterprise* definem um limite no número de relações de confiança que você pode criar. Não é possível alterar para uma SKU com um limite máximo inferior do que você configurou no momento.

Por exemplo:

* Se você tiver criado duas relações de confiança de floresta no SKU *Premium* , não poderá mudar para a SKU *Standard* . O SKU *Standard* não dá suporte a relações de confiança de floresta.
* Ou, se você tiver criado sete relações de confiança no SKU *Premium* , não poderá mudar para o SKU *corporativo* . O SKU *empresarial* dá suporte a um máximo de cinco relações de confiança.

Para obter mais informações sobre esses limites, consulte [recursos e limites de SKU AD DS do Azure][concepts-sku].

## <a name="select-a-new-sku"></a>Selecione um novo SKU

Para alterar a SKU de um domínio gerenciado usando o portal do Azure, conclua as seguintes etapas:

1. Na parte superior da portal do Azure, procure e selecione **Azure AD Domain Services**. Escolha o domínio gerenciado na lista, como *aaddscontoso.com*.
1. No menu no lado esquerdo da página AD DS do Azure, selecione **configurações > SKU**.

    ![Selecione a opção de menu SKU para seu domínio gerenciado AD DS do Azure no portal do Azure](media/change-sku/overview-change-sku.png)

1. No menu suspenso, selecione a SKU que você deseja para o domínio gerenciado. Se você tiver uma floresta de recursos, não poderá selecionar SKU *padrão* , pois as relações de confiança de floresta só estarão disponíveis no SKU *corporativo* ou superior.

    Escolha o SKU desejado no menu suspenso e, em seguida, selecione **salvar**.

    ![Escolha a SKU necessária no menu suspenso na portal do Azure](media/change-sku/change-sku-selection.png)

Pode levar um minuto ou dois para alterar o tipo de SKU.

## <a name="next-steps"></a>Próximas etapas

Se você tiver uma floresta de recursos e quiser criar relações de confiança adicionais após a alteração de SKU, consulte [criar uma relação de confiança de floresta de saída para um domínio local no Azure AD DS][create-trust].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
