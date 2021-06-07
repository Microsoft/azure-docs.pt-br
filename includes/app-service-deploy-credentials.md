---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/20/2020
ms.author: cephalin
ms.openlocfilehash: c3fa57dd162fbbfbf0d46f73bffc78f279ef2968
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "83649143"
---
* **Credenciais de nível de usuário**: um conjunto de credenciais para toda a conta do Azure. Ele pode ser usado para implantar no Serviço de Aplicativo para qualquer aplicativo e em qualquer assinatura que a conta do Azure tem permissão para acessar. É o conjunto padrão que aparece na GUI do portal (como a **Visão geral** e **Propriedades** da página de recursos [do aplicativo](../articles/azure-resource-manager/management/manage-resources-portal.md#manage-resources)). Quando um usuário recebe acesso ao aplicativo por meio de RBAC (Controle de Acesso Baseado na Função) ou permissões coadmin, esse usuário pode usar suas próprias credenciais em nível de usuário até que o acesso seja revogado. Não compartilhe essas credenciais com outros usuários do Azure.

* **Credenciais de nível de aplicativo**: um conjunto de credenciais para cada aplicativo. Podem ser usadas para implantar nesse aplicativo somente. As credenciais de cada aplicativo são geradas automaticamente na criação do aplicativo. Eles não podem ser configurados manualmente, mas podem ser redefinidos a qualquer momento. Para que um usuário tenha acesso às credenciais no nível do aplicativo via (RBAC), esse usuário deve ser colaborador ou superior no aplicativo (incluindo a função interna de colaborador do site). Os leitores não têm permissão para publicar e não pode acessar essas credenciais.