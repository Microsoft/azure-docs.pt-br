---
title: Gerenciar dados de usuário encontrados em uma investigação da Central de Segurança do Azure
description: " Saiba como gerenciar dados de usuário encontrados no recurso de investigação da Central de Segurança do Azure. "
services: operations-management-suite
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: memildin
ms.openlocfilehash: 88fd707d769c7aed53160a9f22fefd15cce19a4b
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340726"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Gerenciar dados de usuário encontrados em uma investigação da Central de Segurança do Azure
Este artigo fornece informações sobre como gerenciar dados de usuário encontrados no recurso de investigação da Central de Segurança do Azure. Os dados de investigação são armazenados em [logs de Azure monitor](../azure-monitor/log-query/log-query-overview.md) e expostos na central de segurança. O gerenciamento de dados de usuário inclui a capacidade de excluir dados ou exportar dados.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>Pesquisando e identificando dados pessoais
No portal do Azure, você pode usar o recurso de investigação da Central de Segurança para pesquisar dados pessoais. O recurso de investigação está disponível em **alertas de segurança**.

O recurso de investigação mostra todas as entidades, informações de usuário e dados na guia **Entidades**.

## <a name="securing-and-controlling-access-to-personal-information"></a>Protegendo e controlando o acesso a informações pessoais
Um usuário da Central de Segurança com a função de Leitor, Proprietário, Colaborador ou Administrador da Conta pode acessar os dados do cliente dentro da ferramenta.

Confira [Funções internas para o controle de acesso baseado em função do Azure](../role-based-access-control/built-in-roles.md) para saber mais sobre as funções Leitor, Proprietário e Colaborador. Confira [Administradores da assinatura do Azure](../cost-management-billing/manage/add-change-subscription-administrator.md) para saber mais sobre a função Administrador da Conta.

## <a name="deleting-personal-data"></a>Excluindo dados pessoais
Um usuário da Central de Segurança com a função Proprietário, Colaborador ou Administrador da Conta pode excluir informações sobre a investigação.

Para excluir uma investigação, você pode enviar uma solicitação `DELETE` à API REST do Azure Resource Manager:

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

A entrada `incidentName` pode ser encontrada listando todos os incidentes usando uma solicitação `GET`:

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>Exportando dados pessoais
Um usuário da Central de Segurança com a função Proprietário, Colaborador ou Administrador da Conta pode exportar informações sobre a investigação. Para exportar informações de investigação, vá para a guia **Entidades** para copiar e colar as informações relevantes.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o gerenciamento de dados de usuário, confira [Gerenciar dados de usuário localizados na Central de Segurança do Azure](security-center-privacy.md).
Para saber mais sobre como excluir dados privados em logs de Azure Monitor, consulte [como exportar e excluir dados privados](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data).