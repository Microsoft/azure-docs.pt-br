---
title: Registrar-se usando as informações do Parceiro CSP com o Cloudyn no Azure
description: Saiba os detalhes sobre o processo de registro usado por parceiros para integrar os clientes deles ao portal do Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 10/23/2020
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: 95d8a425314ad1c968915085b35ca89aa3f62711
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92543314"
---
# <a name="register-with-the-csp-partner-program-and-view-cost-data"></a>Registrar-se mo programa de Parceiro CSP e exibir dados de custo

Como um parceiro CSP e um usuário Cloudyn registrado, você pode ver e analisar seus gastos com a nuvem no Cloudyn. [O Gerenciamento de Custos do Azure está disponível nativamente para os parceiros diretos](../costs/get-started-partners.md) que integraram os clientes a um Contrato de Cliente da Microsoft e compraram um plano do Azure.

O registro fornece acesso ao portal do Cloudyn. Este guia de início rápido fornece detalhes do processo de registro necessários para criar uma assinatura de avaliação do Cloudyn e entrar no portal do Cloudyn.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="configure-indirect-csp-access-in-cloudyn"></a>Configurar acesso indireto de CSP em Cloudyn

Por padrão, a API do Partner Center só está acessível a CSPs diretos. No entanto, um provedor de CSP direto pode configurar o acesso para seus clientes ou parceiros de CSP indireto usando grupos de entidades em Cloudyn.

Para habilitar o acesso para parceiros ou clientes CSP indiretos, conclua estas etapas para segmentar dados de CSP indireto usando grupos de entidades Cloudyn. Em seguida, atribua as permissões de usuário apropriadas para os grupos de entidades.

1. Crie um grupo de entidades com as informações em [Criar entidades](tutorial-user-access.md#create-and-manage-entities).
2. Siga as etapas em [Atribuir assinaturas a Entidades de Custo](https://www.youtube.com/watch?v=d9uTWSdoQYo). Associe a conta do cliente de CSP indireto e suas assinaturas do Azure para a entidade que você criou anteriormente.
3. Siga as etapas em [Criar um usuário com acesso de administrador](tutorial-user-access.md#create-a-user-with-admin-access) para criar uma conta de usuário com acesso de Administrador. Em seguida, verifique se a conta de usuário tem acesso de administrador às entidades específicas criadas anteriormente para a conta indireta.

Parceiros de CSP indiretos entram no portal Cloudyn usando as contas que você criou para eles.


[!INCLUDE [cost-management-create-account-view-data](../../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você usou as informações do CSP para se registrar no Cloudyn. Você também entrou no portal do Cloudyn e começou a exibir os dados de custo. Para saber mais sobre a Cloudyn, continue o tutorial para o Cloudyn.

> [!div class="nextstepaction"]
> [Examinar o uso e os custos](tutorial-review-usage.md)