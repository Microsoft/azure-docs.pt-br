---
title: Configurar o controle de acesso baseado em função do Azure (RBAC do Azure) para a API do Azure para FHIR
description: Este artigo descreve como configurar o RBAC do Azure para a API do Azure para o plano de dados do FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: matjazl
ms.reviewer: dseven
ms.openlocfilehash: 5cadfad445c76726b1b825b131de4016a57979fa
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93391833"
---
# <a name="configure-azure-rbac-for-fhir"></a>Configurar o RBAC do Azure para FHIR 

Neste artigo, você aprenderá a usar o [Azure RBAC (controle de acesso baseado em função)](../role-based-access-control/index.yml) para atribuir acesso à API do Azure para o plano de dados FHIR. O RBAC do Azure é o método preferencial para atribuir acesso ao plano de dados quando os usuários do plano de dados são gerenciados no locatário Azure Active Directory associado à sua assinatura do Azure. Se você estiver usando um locatário de Azure Active Directory externo, consulte a [referência de atribuição de RBAC local](configure-local-rbac.md).

## <a name="confirm-azure-rbac-mode"></a>Confirmar modo de RBAC do Azure

Para usar o RBAC do Azure, sua API do Azure para FHIR deve ser configurada para usar seu locatário de assinatura do Azure para o plano de dados e não deve haver IDs de objeto de identidade atribuídas. Você pode verificar suas configurações inspecionando a folha de **autenticação** da sua API do Azure para FHIR:

:::image type="content" source="media/rbac/confirm-azure-rbac-mode.png" alt-text="Confirmar modo de RBAC do Azure":::

A **autoridade** deve ser definida para o locatário do Azure Active Directory associado à sua assinatura e não deve haver nenhum GUID na caixa rotulado como **IDs de objeto permitido**. Você também observará que a caixa está desabilitada e um rótulo indica que o RBAC do Azure deve ser usado para atribuir funções de plano de dados.

## <a name="assign-roles"></a>Atribuir funções

Para conceder aos usuários, entidades de serviço ou grupos o acesso ao plano de dados FHIR, clique em **controle de acesso (iam)** e clique em **atribuições de função** e clique em **+ Adicionar** :

:::image type="content" source="media/rbac/add-azure-rbac-role-assignment.png" alt-text="Adicionar atribuição de função do Azure":::

Na seleção de **função** , procure uma das funções internas para o plano de dados FHIR:

:::image type="content" source="media/rbac/built-in-fhir-data-roles.png" alt-text="Funções de dados internas do FHIR":::

Você pode escolher entre:

* Leitor de dados do FHIR: pode ler (e Pesquisar) dados do FHIR.
* Gravador de dados FHIR: pode ler, gravar e excluir de maneira reversível os dados do FHIR.
* Exportador de dados FHIR: pode ler e exportar `$export` dados (operador).
* Colaborador de dados do FHIR: pode executar todas as operações do plano de dados.

Se essas funções não forem suficientes para sua necessidade, você também poderá [criar funções personalizadas](../role-based-access-control/tutorial-custom-role-powershell.md).

Na caixa **selecionar** , procure um usuário, uma entidade de serviço ou um grupo ao qual você deseja atribuir a função.

## <a name="caching-behavior"></a>Comportamento de caching

A API do Azure para FHIR armazenará decisões em cache por até 5 minutos. Se você conceder a um usuário acesso ao servidor FHIR adicionando-os à lista de IDs de objeto permitidos ou removê-los da lista, deverá esperar que ele demore até cinco minutos para que as alterações nas permissões sejam propagadas.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a atribuir funções do Azure para o plano de dados FHIR. Para saber mais sobre configurações adicionais para a API do Azure para FHIR:
 
>[!div class="nextstepaction"]
>[Configurações adicionais para a API do Azure para FHIR](azure-api-for-fhir-additional-settings.md)