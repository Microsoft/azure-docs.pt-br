---
title: Gerenciar dados de usuário na Central de Segurança do Azure | Microsoft Docs
description: " Saiba como gerenciar dados de usuário na Central de Segurança do Azure. "
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: rkarlin
ms.openlocfilehash: fcec410df631a58b76878a4cb327ca2fb04a2105
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60703465"
---
# <a name="manage-user-data-in-azure-security-center"></a>Gerenciar dados de usuário na Central de Segurança do Azure
Este artigo fornece informações sobre como é possível gerenciar dados de usuário na Central de Segurança do Azure. O gerenciamento de dados de usuário inclui a capacidade de acessar, excluir ou exportar dados.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

Um usuário da Central de Segurança com a função de Leitor, Proprietário, Colaborador ou Administrador da Conta pode acessar os dados do cliente dentro da ferramenta. Confira [Funções internas para o controle de acesso baseado em função do Azure](../role-based-access-control/built-in-roles.md) para saber mais sobre as funções Leitor, Proprietário e Colaborador. Consulte [Administradores da assinatura do Azure](../billing/billing-add-change-azure-subscription-administrator.md) para saber mais sobre a função Administrador da Conta.

## <a name="searching-for-and-identifying-personal-data"></a>Pesquisar e identificar dados pessoais
Um usuário da Central de Segurança pode exibir os dados pessoais por meio do portal do Azure. A Central de Segurança armazena apenas detalhes de contato de segurança, como endereços de email e números de telefone. Consulte [Fornecer detalhes de contato de segurança na Central de Segurança do Azure](security-center-provide-security-contact-details.md) para obter mais informações.

No portal do Azure, um usuário pode exibir as configurações de IP permitidas usando o recurso de acesso à VM just-in-time da Central de Segurança. Consulte [Gerenciar o acesso à máquina virtual usando o just in time](security-center-just-in-time.md) para obter mais informações.

No portal do Azure, um usuário pode exibir alertas de segurança fornecidos pela Central de Segurança, incluindo endereços IP e detalhes do invasor. Consulte [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) para obter mais informações.

## <a name="classifying-personal-data"></a>Classificar dados pessoais
Não é possível classificar os dados pessoais localizados no recurso de contato de segurança da Central de Segurança. Os dados salvos são um endereço de email (ou vários endereços de email) e um número de telefone. [Dados de contato](security-center-provide-security-contact-details.md) são validados pela Central de Segurança.

Não é necessário classificar os endereços IP e os números da porta salvos pelo recurso [just-in-time](security-center-just-in-time.md) da Central de Segurança do Azure.

Somente um usuário com a função de Administrador pode classificar dados pessoais [exibindo alertas](security-center-managing-and-responding-alerts.md) na Central de Segurança.

## <a name="securing-and-controlling-access-to-personal-data"></a>Protegendo e controlando o acesso a informações pessoais
Um usuário da Central de Segurança com a função Leitor, Proprietário, Colaborador ou Administrador da Conta pode acessar [dados de contato de segurança](security-center-provide-security-contact-details.md).

Um usuário da Central de Segurança com a função Leitor, Proprietário, Colaborador ou Administrador da Conta pode acessar políticas de [just-in-time](security-center-just-in-time.md).

Um usuário da Central de Segurança com a função Leitor, Proprietário, Colaborador ou Administrador da Conta pode exibir [alertas](security-center-managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>Atualizar dados pessoais
Um usuário da Central de Segurança atribuído à função Proprietário, Colaborador ou Administrador da Conta pode atualizar [dados de contato de segurança](security-center-provide-security-contact-details.md) por meio do portal do Azure.

Um usuário da Central de Segurança com a função Proprietário, Colaborador ou Administrador da Conta pode atualizar [políticas de just-in time](security-center-just-in-time.md).

Um Administrador de Conta não pode editar incidentes de alerta. Um [incidente de alerta](security-center-managing-and-responding-alerts.md) é considerado dados de segurança e é somente leitura.

## <a name="deleting-personal-data"></a>Excluir dados pessoais
Um usuário da Central de Segurança atribuído à função Proprietário, Colaborador ou Administrador da Conta pode excluir [dados de contato de segurança](security-center-provide-security-contact-details.md) por meio do portal do Azure.

Um usuário da Central de segurança com a função Proprietário, Colaborador ou Administrador da Conta pode excluir [políticas de just-in-time](security-center-just-in-time.md) por meio do portal do Azure.

Um usuário da Central de Segurança não pode excluir incidentes de alerta. Devido às necessidades de segurança, um [incidente de alerta](security-center-managing-and-responding-alerts.md) é considerado somente leitura.

## <a name="exporting-personal-data"></a>Exportar dados pessoais
Um usuário da Central de Segurança com a função Leitor, Proprietário, Colaborador ou Administrador da Conta pode exportar [dados de contato de segurança](security-center-provide-security-contact-details.md):

- Fazendo uma cópia do portal do Azure
- Executando a chamada à API REST do Azure, GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

Um usuário da Central de Segurança com a função Administrador da Conta pode exportar [políticas just-in-time](security-center-just-in-time.md) contendo endereços IP:

- Fazendo uma cópia do portal do Azure
- Executando a chamada à API REST do Azure, GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

Um Administrador da Conta pode exportar os detalhes do alerta:

- Fazendo uma cópia do portal do Azure
- Executando a chamada à API REST do Azure, GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

Consulte [Obter alertas de segurança (Coleção GET)](https://msdn.microsoft.com/library/mt704050.aspx) para mais informações.

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Restringir o uso de dados pessoais para criação de perfil ou marketing sem consentimento
Um usuário da Central de Segurança pode optar por excluir seus [dados de contato de segurança](security-center-provide-security-contact-details.md).

[Dados de just-in-time](security-center-just-in-time.md) são considerados dados não identificáveis e ficam retidos por um período de 30 dias.

[Dados de alerta](security-center-managing-and-responding-alerts.md) são considerados dados de segurança e ficam retidos por um período de dois anos.

## <a name="auditing-and-reporting"></a>Auditoria e relatórios
Logs de auditoria de contato de segurança, just-in-time, e atualizações de alerta são mantidos nos [Logs de Atividades do Azure](../azure-monitor/platform/activity-logs-overview.md).

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o gerenciamento de dados de usuário, consulte [Gerenciar dados de usuário localizados em uma investigação da Central de Segurança do Azure](security-center-investigation-user-data.md).
