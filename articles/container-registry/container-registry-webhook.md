---
title: Webhooks para responder a ações de registro
description: Saiba como usar webhooks para disparar eventos quando ocorrerem ações de push ou pull em seus repositórios de registro.
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 5374b58ba72727500294a173c26e9a131b29fe34
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101722238"
---
# <a name="using-azure-container-registry-webhooks"></a>Como usar webhooks do Registro de Contêiner do Azure

Um registro de contêiner do Azure armazena e gerencia imagens de contêiner privadas do Docker, de forma semelhante a como o Docker Hub armazena imagens públicas do Docker. Ele também pode hospedar repositórios para [gráficos Helm](container-registry-helm-repos.md) (versão prévia), um formato de empacotamento para implantar aplicativos no Kubernetes. É possível usar webhooks para disparar eventos quando certas ações ocorrem em um dos repositórios do Registro. Webhooks podem responder a eventos no nível do registro ou podem ter o escopo reduzido para a marca de um repositório específico. Com um registro de [replicação geográfica](container-registry-geo-replication.md), você configura cada webhook para responder a eventos em uma réplica regional específica.

O ponto de extremidade de um webhook deve ser acessível publicamente a partir do registro. Você pode configurar solicitações de webhook do registro para autenticar em um ponto de extremidade protegido.

Para obter detalhes sobre solicitações de webhook, consulte [Azure Container Registry webhook schema reference](container-registry-webhook-reference.md) (Referência de esquema de webhook do Registro de Contêiner do Azure).

## <a name="prerequisites"></a>Pré-requisitos

* Registro de Contêiner do Azure – Crie um Registro de contêiner em sua assinatura do Azure. Por exemplo, use o [Portal do Azure](container-registry-get-started-portal.md) ou a [CLI do Azure](container-registry-get-started-azure-cli.md). As [camadas de serviço do Registro de Contêiner do Azure](container-registry-skus.md) têm cotas de webhooks diferentes.
* CLI do Docker – para configurar o computador local como um host do Docker e acessar os comandos da CLI do Docker, instale o [Mecanismo do Docker](https://docs.docker.com/engine/installation/).

## <a name="create-webhook---azure-portal"></a>Criar webhook – portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com).
1. Navegue até o Registro de contêiner no qual você deseja criar um webhook.
1. Em **Serviços**, selecione **Webhooks**.
1. Selecione **Adicionar** na barra de ferramentas do webhook.
1. Preencha o formulário *Criar webhook* com as seguintes informações:

| Valor | Descrição |
|---|---|
| Nome do webhook | O nome que você deseja dar ao webhook. Ele pode conter somente letras e números e deve ter de 5 a 50 caracteres de extensão. |
| Location | Para um registro de [replicação geográfica](container-registry-geo-replication.md), especifique a região do Azure da réplica de registro. 
| URI de serviço | O URI para que o webhook deveria enviar notificações POST. |
| Cabeçalhos personalizados | Cabeçalhos que você deseja passar junto com a solicitação POST. Eles devem estar no formato "chave: valor". |
| Ações de gatilho | Ações que disparam o webhook. As ações incluem push de imagem, exclusão de imagem, push de gráfico de Helm, exclusão de gráfico de Helm e quarentena de imagem. Você pode escolher uma ou mais ações para disparar o webhook. |
| Status | O status do webhook depois que ele é criado. Ele é habilitado por padrão. |
| Escopo | O escopo no qual o webhook funciona. Se não for especificado, o escopo incluirá todos os eventos no registro. Ele pode ser especificado para um repositório ou uma marca usando o formato "repository:tag" ou "repository:*" para todas as marcas em um repositório. |

Formulário de webhook de exemplo:

![Captura de tela que mostra a criação de webhook do ACR U I no portal do Azure.](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Criar webhook – CLI do Azure

Para criar um webhook usando a CLI do Azure, use o comando [az acr webhook create](/cli/azure/acr/webhook#az-acr-webhook-create). O comando a seguir cria um webhook para todos os eventos de exclusão de imagem no registro *mycontainerregistry*:

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Testar webhook

### <a name="azure-portal"></a>Portal do Azure

Antes de usar o webhook, você pode testá-lo com o botão **Ping**. O ping envia uma solicitação POST genérica para o ponto de extremidade especificado e registra a resposta. Usar o recurso de ping pode ajudá-lo a verificar se você configurou corretamente o webhook.

1. Selecione o webhook que você deseja testar.
2. Na barra de ferramentas superior, selecione **Ping**.
3. Verifique a resposta do ponto de extremidade na coluna **STATUS HTTP**.

![Interface do usuário de criação de webhook ACR no portal do Azure](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>CLI do Azure

Para testar um webhook ACR com a CLI do Azure, use o comando [az acr webhook ping](/cli/azure/acr/webhook#az-acr-webhook-ping).

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Para ver os resultados, use o comando [az acr webhook list-events](/cli/azure/acr/webhook).

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Excluir webhook

### <a name="azure-portal"></a>Portal do Azure

Cada webhook pode ser excluído selecionando-se o webhook e, em seguida, o botão **Excluir** no Portal do Azure.

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Próximas etapas

### <a name="webhook-schema-reference"></a>Referência do esquema do Webhook

Para obter detalhes sobre o formato e propriedades das cargas do evento JSON emitidas pelo Registro de Contêiner do Azure, consulte a referência de esquema do webhook:

[Referência de esquema de webhook do Registro de Contêiner do Azure](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Eventos da Grade de Eventos

Além dos eventos do webhook do registro nativo discutidos neste artigo, o Registro de Contêiner do Azure pode emitir eventos para a Grade de Eventos:

[Início Rápido: Enviar eventos de registro de contêiner para a Grade de Eventos](container-registry-event-grid-quickstart.md)
