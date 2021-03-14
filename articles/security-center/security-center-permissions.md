---
title: Permissões na Central de Segurança do Azure | Microsoft Docs
description: Este artigo explica como a Central de Segurança do Azure usa o controle de acesso baseado em função para atribuir permissões aos usuários e identifica as ações permitidas para cada função.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 01/03/2021
ms.author: memildin
ms.openlocfilehash: dcbb4977e1bfd17f0cbed61abf9ba335615b7799
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443753"
---
# <a name="permissions-in-azure-security-center"></a>Permissões na Central de Segurança do Azure

A Central de Segurança do Azure usa o [RBAC do Azure (controle de acesso baseado em função do Azure)](../role-based-access-control/role-assignments-portal.md), que fornece [funções internas](../role-based-access-control/built-in-roles.md) que podem ser atribuídas a usuários, grupos e serviços no Azure.

A Central de Segurança avalia a configuração de seus recursos para identificar problemas de segurança e vulnerabilidades. Na Central de Segurança, você vê apenas as informações relacionadas a um recurso quando for atribuído à função de Proprietário, Colaborador ou Leitor da assinatura ou do grupo de recursos ao qual o recurso pertence.

Além dessas funções, há duas funções específicas da Central de Segurança:

* **Leitor de Segurança**: um usuário que pertence a essa função tem direitos de exibição para a Central de Segurança. O usuário pode exibir as recomendações, alertas, uma política de segurança e estados de segurança, mas não pode fazer alterações.
* **Administrador de Segurança**: um usuário que pertence a essa função tem os mesmos direitos que o Leitor de Segurança e também pode atualizar a política de segurança e ignorar alertas e recomendações.

> [!NOTE]
> As funções de segurança Leitor de Segurança e Administrador de Segurança só têm acesso na Central de Segurança. As funções de segurança não têm acesso a outras áreas de serviço do Azure como Armazenamento, Web e Móveis ou Internet das Coisas.
>

## <a name="roles-and-allowed-actions"></a>Funções e ações permitidas

A tabela a seguir exibe as funções e as ações permitidas na Central de Segurança.

| Ação                                                                                                                                        | Leitor de Segurança/ <br> Leitor | Administrador de Segurança | Colaborador do Grupo de Recursos/ <br> Proprietário do Grupo de Recursos | Colaborador da assinatura | Proprietário da assinatura |
|:----------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------:|:--------------:|:------------------------------------------------------:|:------------------------:|:------------------:|
| Editar política de segurança                                                                                                                          | -                             | ✔             | -                                                      | -                        | ✔                 |
| Adicionar/atribuir iniciativas (incluindo padrões de conformidade regulatória)                                                                           | -                             | -              | -                                                      | -                        | ✔                 |
| Habilitar/desabilitar o Azure Defender                                                                                                               | -                             | ✔             | -                                                      | -                        | ✔                 |
| Habilitar/desabilitar o provisionamento automático                                                                                                            | -                             | ✔             | -                                                      | ✔                       | ✔                  |
| Aplicar as recomendações de segurança a um recurso</br> (e usar a [Correção Rápida](security-center-remediate-recommendations.md#quick-fix-remediation)) | -                             | -              | ✔                                                     | ✔                        | ✔                 |
| Ignorar alertas                                                                                                                                | -                             | ✔             | -                                                      | ✔                       | ✔                  |
| Exibir alertas e recomendações                                                                                                               | ✔                            | ✔              | ✔                                                     | ✔                        | ✔                 |

> [!NOTE]
> Recomendamos que você atribua a função menos permissiva necessária para os usuários realizarem suas tarefas. Por exemplo, atribua a função Leitor aos usuários que precisam apenas exibir informações sobre a integridade da segurança de um recurso, mas que não precisam executar nenhuma ação, como aplicar recomendações ou editar políticas.
>
>

## <a name="next-steps"></a>Próximas etapas
Este artigo explicou como a Central de Segurança usa o Azure RBAC para atribuir permissões aos usuários e identificou as ações permitidas para cada função. Agora que você está familiarizado com as atribuições de função necessárias para monitorar o estado de segurança de sua assinatura, editar as políticas de segurança e aplicar recomendações, saiba como:

- [Definir políticas de segurança na Central de Segurança](tutorial-security-policy.md)
- [Gerenciando recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md)
- [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md)
- [Monitoring partner solutions with Azure Security Center](./security-center-partner-integration.md) (Monitorando soluções de parceiros com a Central de Segurança do Azure)