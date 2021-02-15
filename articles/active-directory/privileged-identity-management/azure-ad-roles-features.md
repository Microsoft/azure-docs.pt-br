---
title: Recursos de função do Azure AD no Privileged Identity Management | Microsoft Docs
description: Como gerenciar funções do Azure AD para Privileged Identity Management de atribuição (PIM)
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
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371504"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>Recursos de gerenciamento para funções do Azure AD no Privileged Identity Management

A experiência de gerenciamento das funções do Azure AD no Privileged Identity Management foi atualizada para unificar a forma como as funções do Azure AD e as funções de recurso do Azure são gerenciadas. Anteriormente, Privileged Identity Management para as funções de recursos do Azure tinham alguns recursos principais que não estavam disponíveis para as funções do Azure AD.

Com a atualização sendo distribuída no momento, estamos mesclando as duas em uma única experiência de gerenciamento e, nela, você obtém a mesma funcionalidade para as funções do Azure AD como as funções de recursos do Azure. Este artigo informa sobre os recursos atualizados e quaisquer requisitos.

## <a name="time-bound-assignments"></a>Atribuições de limite de tempo

Anteriormente, havia dois Estados possíveis para as atribuições de função: *elegíveis* e *permanentes*. Agora você também pode definir uma hora de início e de término para cada tipo de atribuição. Essa adição oferece quatro Estados possíveis em que você pode fazer uma atribuição:

- Qualificado permanentemente
- Ativo permanentemente
- Qualificado, com datas de início e de término especificadas para atribuição
- Ativo, com datas de início e de término especificadas para atribuição

Em muitos casos, mesmo que você não queira que os usuários tenham funções qualificadas de atribuição e ativação todas as vezes, você ainda pode proteger sua organização do Azure AD definindo um tempo de expiração para atribuições. Por exemplo, se você tiver alguns usuários temporários qualificados, considere definir uma expiração para removê-los automaticamente da atribuição de função quando seu trabalho for concluído.

## <a name="new-role-settings"></a>Novas configurações de função

Também estamos adicionando novas configurações para as funções do Azure AD.

- **Anteriormente**, você podia definir apenas as configurações de ativação por função. Ou seja, as configurações de ativação, como requisitos de autenticação multifator e requisitos de incidente/solicitação de tíquete, foram aplicadas a todos os usuários qualificados para uma função especificada.
- **Agora**, você pode configurar se um usuário individual precisa executar a autenticação multifator antes de poder ativar uma função. Além disso, você pode ter controle avançado sobre seus emails de Privileged Identity Management relacionados a funções específicas.

## <a name="extend-and-renew-assignments"></a>Estender e renovar atribuições

Assim que você descobrir a atribuição de tempo limite, a primeira pergunta que você pode fazer é o que acontece se uma função tiver expirado? Nesta nova versão, fornecemos duas opções para esse cenário:

- **Estender**: quando uma atribuição de função se aproxima de sua expiração, o usuário pode usar Privileged Identity Management para solicitar uma extensão para essa atribuição de função
- **Renovar**: quando uma atribuição de função tiver expirado, o usuário poderá usar Privileged Identity Management para solicitar uma renovação para essa atribuição de função

As duas ações iniciadas pelo usuário exigem uma aprovação de um Administrador global ou de um Administrador de funções com privilégios. Os administradores não precisarão mais se preocupar com o gerenciamento dessas expirações. Eles só precisam aguardar as solicitações de extensão ou renovação e aprová-las se a solicitação for válida.

## <a name="api-changes"></a>Alterações de API

Quando os clientes tiverem a versão atualizada distribuída para sua organização do Azure AD, a API do Graph existente deixará de funcionar. Você deve fazer a transição para usar o [API do Graph para funções de recurso do Azure](/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta). Para gerenciar funções do Azure AD usando essa API, troque `/azureResources` com `/aadroles` na assinatura e use a ID de diretório para o `resourceId` .

Nós experimentamos o nosso melhor para entrar em contato com todos os clientes que estão usando a API anterior para que eles saibam sobre essa alteração antes do tempo. Se a sua organização do Azure AD foi movida para a nova versão e você ainda depende da API antiga, entre em contato com a equipe em pim_preview@microsoft.com .

## <a name="powershell-change"></a>Alteração do PowerShell

Para clientes que estão usando o módulo Privileged Identity Management PowerShell para funções do Azure AD, o PowerShell deixará de funcionar com a atualização. No lugar dos cmdlets anteriores, você deve usar os cmdlets Privileged Identity Management dentro do módulo PowerShell de visualização do Azure AD. Instale o módulo do PowerShell do Azure AD do [Galeria do PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Agora você pode [ler a documentação e os exemplos de operações do PIM neste módulo do PowerShell](powershell-for-azure-ad-roles.md).

## <a name="next-steps"></a>Próximas etapas

- [Atribuir uma função personalizada do Azure AD](azure-ad-custom-roles-assign.md)
- [Remover ou atualizar uma atribuição de função personalizada do Azure AD](azure-ad-custom-roles-update-remove.md)
- [Configurar uma atribuição de função personalizada do Azure AD](azure-ad-custom-roles-configure.md)
- [Definições de função no Azure AD](../roles/permissions-reference.md)