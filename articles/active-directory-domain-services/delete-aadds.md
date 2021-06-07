---
title: Excluir Azure Active Directory Domain Services | Microsoft Docs
description: Saiba como desabilitar, ou excluir, um Azure Active Directory Domain Services domínio gerenciado usando o portal do Azure
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: a5126abd6643eba7f63b2bf4ca984bb9892b2d7a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619803"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>Excluir um Azure Active Directory Domain Services domínio gerenciado usando o portal do Azure

Se você não precisar mais de um domínio gerenciado Azure Active Directory Domain Services (Azure AD DS), poderá excluí-lo. Não há nenhuma opção para desativar ou desabilitar temporariamente um domínio gerenciado AD DS do Azure. Excluir o domínio gerenciado não exclui ou, de outra forma, afeta negativamente o locatário do Azure AD.

Este artigo mostra como usar o portal do Azure para excluir um domínio gerenciado.

> [!WARNING]
> **A exclusão é permanente e não pode ser revertida.**
> 
> Quando você exclui um domínio gerenciado, ocorrem as seguintes etapas:
>   * Controladores de domínio para o domínio gerenciado são desprovisionados e removidos da rede virtual.
>   * Os dados em um domínio gerenciado serão excluídos permanentemente. Esses dados incluem UOs personalizadas, GPOs, registros DNS personalizados, entidades de serviço, GMSAs, etc. que você criou.
>   * Os computadores que ingressaram no domínio gerenciado perdem sua relação de confiança com o domínio e precisam ser retirados do domínio.
>       * Você não pode entrar nesses computadores usando credenciais corporativas do AD. Em vez disso, você deve usar as credenciais de administrador local para o computador.

## <a name="delete-the-managed-domain"></a>Excluir o domínio gerenciado

Para excluir um domínio gerenciado, conclua as seguintes etapas:

1. No portal do Azure, pesquise e selecione **Azure AD Domain Services**.
1. Selecione o nome do domínio gerenciado, como *aaddscontoso.com*.
1. Na página **Visão Geral**, selecione **Excluir**. Para confirmar a exclusão, digite o nome de domínio do domínio gerenciado novamente e, em seguida, selecione **excluir**.

Pode levar de 15-20 minutos ou mais para excluir o domínio gerenciado.

## <a name="next-steps"></a>Próximas etapas

Considere [compartilhar comentários][feedback] para os recursos que você gostaria de ver no Azure AD DS.

Se você quiser começar a usar o Azure AD DS novamente, consulte [criar e configurar um domínio gerenciado Azure Active Directory Domain Services][create-instance].

<!-- INTERNAL LINKS -->
[feedback]: https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160593%3fcategory_id%3d160593
[create-instance]: tutorial-create-instance.md