---
title: Recursos de função do Azure AD Privileged Identity Management | Microsoft Docs
description: Como gerenciar funções do Azure AD para atribuição de PIM (Privileged Identity Management)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 07/10/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4478c9c286c06d5d6c5593195a0e93abd286b8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92371504"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>Funcionalidades de gerenciamento para funções do Azure AD no Privileged Identity Management

A experiência de gerenciamento das funções do Azure AD no Privileged Identity Management foi atualizada para unificar a forma como as funções do Azure AD e as funções de recurso do Azure são gerenciadas. Anteriormente, o Privileged Identity Management para as funções de recursos do Azure tinham alguns recursos principais que não estavam disponíveis para as funções do Azure AD.

Com a atualização realizada no momento, estamos mesclando as duas em uma única experiência de gerenciamento e com ela você obtém a mesma funcionalidade para as funções do Azure AD que já tem para as funções de recursos do Azure. Este artigo informa sobre os recursos atualizados e seus requisitos.

## <a name="time-bound-assignments"></a>Atribuições de limite de tempo

Anteriormente, havia dois estados possíveis para as atribuições de função: *qualificado* e *permanente*. Agora você também pode definir uma hora de início e de término para cada tipo de atribuição. Essa adição oferece quatro estados possíveis em que você pode fazer uma atribuição:

- Permanentemente qualificado
- Permanentemente ativo
- Qualificado, com datas de início e de término especificadas para a atribuição
- Ativo, com datas de início e de término especificadas para a atribuição

Em muitos casos, mesmo que você não queira que os usuários sempre tenham funções de atribuição qualificada e ativa, você ainda pode proteger sua organização do Azure AD definindo um tempo de expiração para as atribuições. Por exemplo, se você tiver alguns usuários temporários qualificados, considere definir um tempo de expiração para removê-los automaticamente da atribuição de função ao concluir o trabalho.

## <a name="new-role-settings"></a>Configurações de nova função

Também estamos adicionando novas configurações para as funções do Azure AD.

- **Anteriormente**, você podia definir apenas as configurações de ativação por função. Ou seja, as configurações de ativação, como requisitos de autenticação multifator e requisitos de incidente/solicitação de tíquete, foram aplicadas a todos os usuários qualificados para uma função especificada.
- **Agora**, você pode configurar se um usuário individual precisa executar a autenticação multifator antes de ativar uma função. Além disso, você pode ter controle avançado sobre seus emails de Privileged Identity Management relacionados à funções específicas.

## <a name="extend-and-renew-assignments"></a>Estender e renovar atribuições

Assim que você decidir qual é a atribuição de tempo limite, a primeira pergunta que você pode fazer é o que acontece se uma função tiver expirado? Nesta nova versão, fornecemos duas opções para esse cenário:

- **Estender**: quando uma atribuição de função se aproxima da expiração, o usuário pode usar o Privileged Identity Management para solicitar uma extensão de tempo para essa atribuição de função
- **Renovar**: quando uma atribuição de função expirar, o usuário pode usar o Privileged Identity Management para solicitar uma renovação dessa atribuição de função

As duas ações iniciadas pelo usuário exigem uma aprovação de um Administrador global ou de um Administrador de funções com privilégios. Os administradores não precisarão mais se preocupar com o gerenciamento dessas expirações. Eles só precisam esperar pelas solicitações de renovação e aprová-las se forem válidas.

## <a name="api-changes"></a>Alterações de API

Quando os clientes tiverem a versão atualizada distribuída para sua organização do Azure AD, a API do Graph existente deixará de funcionar. Você deve fazer a transição para usar o [API do Graph para funções de recurso do Azure](/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta). Para gerenciar funções do Azure AD usando essa API, troque `/azureResources` com `/aadroles` na assinatura e use a ID de diretório para o `resourceId`.

Nos esforçamos ao máximo para entrar em contato com todos os clientes que estão usando a API anterior para informá-los sobre essa alteração com antecedência. Se a sua organização do Azure AD foi atualizada para a nova versão e você ainda depende da API antiga, fale com nossa equipe em pim_preview@microsoft.com.

## <a name="powershell-change"></a>Alteração do PowerShell

Para os clientes que estão usando o módulo Privileged Identity Management do PowerShell para funções do Azure AD, o PowerShell deixará de funcionar com a atualização. No lugar dos cmdlets anteriores, você deve usar os cmdlets do Privileged Identity Management dentro do módulo de visualização do PowerShell do Azure AD. Instale o módulo do PowerShell do Azure AD a partir da [Galeria do PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Agora você pode [ler a documentação e os exemplos de operações do PIM neste módulo do PowerShell](powershell-for-azure-ad-roles.md).

## <a name="next-steps"></a>Próximas etapas

- [Atribuir uma função personalizada do Azure AD](azure-ad-custom-roles-assign.md)
- [Remover ou atualizar uma atribuição de função personalizada do Azure AD](azure-ad-custom-roles-update-remove.md)
- [Configurar uma atribuição de função personalizada do Azure AD](azure-ad-custom-roles-configure.md)
- [Definições de função no Azure AD](../roles/permissions-reference.md)