---
author: baanders
description: incluir arquivo para a visão geral das etapas na instalação do gêmeos digital do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: c1cfab8c9e68d9e6b0c3b84e8848645a50c24710
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100560784"
---
A configuração completa para uma nova instância de gêmeos digital do Azure consiste em duas partes:
1. **Criando a instância**
2. **Configurando permissões de acesso do usuário**: os usuários do Azure precisam ter a função de *proprietário de dados do gêmeos digital do Azure* na instância do gêmeos digital do Azure para poder gerenciá-la e seus dados. Nesta etapa, você como um proprietário/administrador da assinatura do Azure atribuirá essa função à pessoa que gerenciará sua instância de gêmeos digital do Azure. Isso pode ser você mesmo ou outra pessoa na sua organização.
 
>[!NOTE]
>Essas operações devem ser concluídas por um usuário com o poder de gerenciar os recursos e o acesso do usuário na assinatura do Azure. Embora algumas etapas possam ser concluídas com permissões inferiores, a cooperação de alguém com essas permissões será necessária para configurar completamente uma instância utilizável. Veja mais informações sobre isso na seção [*pré-requisitos: permissões necessárias*](#prerequisites-permission-requirements) abaixo.