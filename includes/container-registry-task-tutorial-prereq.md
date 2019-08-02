---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/02/2019
ms.author: danlep
ms.openlocfilehash: 40cc1856a5e943ca5596e7d11712febadd30e3ec
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133787"
---
## <a name="prerequisites"></a>Pré-requisitos

### <a name="get-sample-code"></a>Obter código de exemplo

Este tutorial presume que você já tenha concluído as tarefas no [tutorial anterior](../articles/container-registry/container-registry-tutorial-quick-task.md) e tenha criado o fork para e clonado o repositório de exemplo. Se você ainda não tiver feito isso, conclua as etapas na seção [Pré-requisitos](../articles/container-registry/container-registry-tutorial-quick-task.md#prerequisites) do tutorial anterior antes de continuar.

### <a name="container-registry"></a>Registro de contêiner

Você deve ter um registro de contêiner do Azure em sua assinatura do Azure para concluir este tutorial. Se você precisar de um registro, confira o [tutorial anterior](../articles/container-registry/container-registry-tutorial-quick-task.md) ou o [Início Rápido: Criar um registro de contêiner usando a CLI do Azure](../articles/container-registry/container-registry-get-started-azure-cli.md).

## <a name="create-a-github-personal-access-token"></a>Criar um token de acesso pessoal do GitHub

Para disparar uma tarefa em uma confirmação de um repositório Git, as Tarefas do ACR precisam de um PAT (token de acesso pessoal) para acessar o repositório. Se você ainda não tiver um PAT, siga estas etapas para gerar um no GitHub:

1. Navegue até a página de criação do PAT no GitHub em https://github.com/settings/tokens/new
1. Digite uma breve **descrição** para o token, por exemplo, “Demonstração de tarefas do ACR”
1. Selecione os escopos do ACR para acessar o repositório. Para acessar um repositório público como neste tutorial, em **repositório**, habilite **repo:status** e **public_repo**

   ![Captura de tela da página de geração do Token de Acesso Pessoal no GitHub][build-task-01-new-token]

   > [!NOTE]
   > Para gerar um PAT e acessar um repositório *privado*, selecione o escopo completo de controle do **repositório**.

1. Selecione o botão **Gerar token** (você pode ser solicitado a confirmar sua senha)
1. Copie e salve o token gerado em um **local seguro** (use esse token quando você definir uma tarefa na seção a seguir)

   ![Captura de tela do Token de Acesso Pessoal no GitHub][build-task-02-generated-token]

<!-- Images -->
[build-task-01-new-token]: ./media/container-registry-task-tutorial-prereq/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-task-tutorial-prereq/build-task-02-generated-token.png
