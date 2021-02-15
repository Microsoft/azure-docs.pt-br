---
author: baanders
description: incluir arquivo para a visão geral das etapas na instalação do gêmeos digital do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: bf442da43fd7945bf69fbb889ef0c517b8832809
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92478803"
---
>[!NOTE]
>Essas operações devem ser concluídas por um usuário com o poder de gerenciar os recursos e o acesso do usuário na assinatura do Azure. Embora algumas etapas possam ser concluídas com permissões inferiores, a cooperação de alguém com essas permissões será necessária para configurar completamente uma instância utilizável. Veja mais informações sobre isso na seção [*pré-requisitos: permissões necessárias*](#prerequisites-permission-requirements) abaixo.

A configuração completa para uma nova instância de gêmeos digital do Azure consiste em duas partes:
1. **Criando a instância**
2. **Configurando permissões de acesso do usuário**: os usuários do Azure precisam ter a função de *proprietário de dados do gêmeos digital do Azure* na instância do gêmeos digital do Azure para poder gerenciá-la e seus dados. Nesta etapa, você como um proprietário/administrador da assinatura do Azure atribuirá essa função à pessoa que gerenciará sua instância de gêmeos digital do Azure. Isso pode ser você mesmo ou outra pessoa na sua organização.

[!INCLUDE [digital-twins-role-rename-note.md](digital-twins-role-rename-note.md)]