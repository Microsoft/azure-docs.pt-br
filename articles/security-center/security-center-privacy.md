---
title: Gerenciar dados de usuário na Central de Segurança do Azure | Microsoft Docs
description: Saiba como gerenciar os dados do usuário na central de segurança do Azure. O gerenciamento de dados de usuário inclui a capacidade de acessar, excluir ou exportar dados.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: memildin
ms.openlocfilehash: 5b5c78ffec736f29a481aa95426ff663199613b3
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595650"
---
# <a name="manage-user-data-in-azure-security-center"></a>Gerenciar dados de usuário na Central de Segurança do Azure
Este artigo fornece informações sobre como é possível gerenciar dados de usuário na Central de Segurança do Azure. O gerenciamento de dados de usuário inclui a capacidade de acessar, excluir ou exportar dados.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

Um usuário da Central de Segurança com a função de Leitor, Proprietário, Colaborador ou Administrador da Conta pode acessar os dados do cliente dentro da ferramenta. Para saber mais sobre a função de administrador da conta, consulte [funções internas para o controle de acesso baseado em função do Azure](../role-based-access-control/built-in-roles.md) para saber mais sobre as funções leitor, proprietário e colaborador. Consulte [Administradores de assinatura do Azure](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="searching-for-and-identifying-personal-data"></a>Pesquisando e identificando dados pessoais
Um usuário da Central de Segurança pode exibir os dados pessoais por meio do portal do Azure. A Central de Segurança armazena apenas detalhes de contato de segurança, como endereços de email e números de telefone. Para obter mais informações, consulte [fornecer detalhes de contato de segurança na central de segurança do Azure](security-center-provide-security-contact-details.md).

No portal do Azure, um usuário pode exibir as configurações de IP permitidas usando o recurso de acesso à VM just-in-time da central de segurança. Para obter mais informações, consulte [gerenciar o acesso à máquina virtual usando just-in-time](security-center-just-in-time.md).

No portal do Azure, um usuário pode exibir alertas de segurança fornecidos pela Central de Segurança, incluindo endereços IP e detalhes do invasor. Para obter mais informações, consulte [Gerenciando e respondendo a alertas de segurança na central de segurança do Azure](security-center-managing-and-responding-alerts.md).

## <a name="classifying-personal-data"></a>Classificar dados pessoais
Você não precisa classificar os dados pessoais encontrados no recurso de contato de segurança da central de segurança. Os dados salvos são um endereço de email (ou vários endereços de email) e um número de telefone. [Dados de contato](security-center-provide-security-contact-details.md) são validados pela Central de Segurança.

Você não precisa classificar os endereços IP e os números de porta salvos pelo recurso [just-in-time](security-center-just-in-time.md) da central de segurança.

Somente um usuário com a função de Administrador pode classificar dados pessoais [exibindo alertas](security-center-managing-and-responding-alerts.md) na Central de Segurança.

## <a name="securing-and-controlling-access-to-personal-data"></a>Protegendo e controlando o acesso a informações pessoais
Um usuário da Central de Segurança com a função Leitor, Proprietário, Colaborador ou Administrador da Conta pode acessar [dados de contato de segurança](security-center-provide-security-contact-details.md).

Um usuário da central de segurança atribuiu a função de leitor, proprietário, colaborador ou administrador da conta pode acessar suas políticas [just-in-time](security-center-just-in-time.md) .

Um usuário da Central de Segurança com a função Leitor, Proprietário, Colaborador ou Administrador da Conta pode exibir [alertas](security-center-managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>Atualizando dados pessoais
Um usuário da Central de Segurança atribuído à função Proprietário, Colaborador ou Administrador da Conta pode atualizar [dados de contato de segurança](security-center-provide-security-contact-details.md) por meio do portal do Azure.

Um usuário da central de segurança que atribuiu a função de proprietário, colaborador ou administrador da conta pode atualizar suas [políticas just-in-time](security-center-just-in-time.md).

Um administrador de conta não pode editar incidentes de alerta. Um [incidente de alerta](security-center-managing-and-responding-alerts.md) é considerado dados de segurança e é somente leitura.

## <a name="deleting-personal-data"></a>Excluindo dados pessoais
Um usuário da Central de Segurança atribuído à função Proprietário, Colaborador ou Administrador da Conta pode excluir [dados de contato de segurança](security-center-provide-security-contact-details.md) por meio do portal do Azure.

Um usuário da central de segurança que atribuiu a função de proprietário, colaborador ou administrador da conta pode excluir as [políticas just-in-time](security-center-just-in-time.md) por meio do portal do Azure.

Um usuário da central de segurança não pode excluir incidentes de alerta. Por motivos de segurança, um [incidente de alerta](security-center-managing-and-responding-alerts.md) é considerado dados somente leitura.

## <a name="exporting-personal-data"></a>Exportando dados pessoais
Um usuário da Central de Segurança com a função Leitor, Proprietário, Colaborador ou Administrador da Conta pode exportar [dados de contato de segurança](security-center-provide-security-contact-details.md):

- Copiando do portal do Azure
- Executando a chamada à API REST do Azure, GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

Um usuário da central de segurança que atribuiu a função de administrador da conta pode exportar as [políticas just-in-time](security-center-just-in-time.md) que contêm os endereços IP:

- Copiando do portal do Azure
- Executando a chamada à API REST do Azure, GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

Um Administrador da Conta pode exportar os detalhes do alerta:

- Copiando do portal do Azure
- Executando a chamada à API REST do Azure, GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

Para obter mais informações, consulte [obter alertas de segurança (obter coleção)](/previous-versions/azure/reference/mt704050(v=azure.100)).

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Restringir o uso de dados pessoais para criação de perfil ou marketing sem consentimento
Um usuário da Central de Segurança pode optar por excluir seus [dados de contato de segurança](security-center-provide-security-contact-details.md).

[Os dados Just-in-time](security-center-just-in-time.md) são considerados dados não identificáveis e são mantidos por um período de 30 dias.

[Dados de alerta](security-center-managing-and-responding-alerts.md) são considerados dados de segurança e ficam retidos por um período de dois anos.

## <a name="auditing-and-reporting"></a>Auditoria e relatórios
Os logs de auditoria de contato de segurança, just-in-time e atualizações de alertas são mantidos nos [logs de atividades do Azure](../azure-monitor/essentials/platform-logs-overview.md).