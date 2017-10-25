---
title: "Webhooks de registro de contêiner do Azure | Microsoft Docs"
description: "Webhooks de registro de contêiner do Azure"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acr, azure-container-registry
keywords: "Docker, Contêineres, ACR"
ms.assetid: 
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: nepeters
ms.openlocfilehash: 35f2b940a36ca3176cf850afd97e62af0dd797ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-azure-container-registry-webhooks---azure-portal"></a>Usar webhooks de registro de contêiner do Azure – Portal do Azure

Um registro de contêiner do Azure armazena e gerencia imagens de contêiner privadas do Docker, de forma semelhante a como o Docker Hub armazena imagens públicas do Docker. Você usar webhooks para disparar eventos quando certas ações ocorrem em um dos repositórios do registro. Webhooks podem responder a eventos em nível de registro ou eles podem ter o escopo reduzido para a marca de um repositório específico. 

Para obter mais informações e conceitos, consulte a [visão geral](./container-registry-intro.md).

## <a name="prerequisites"></a>Pré-requisitos 

- Registro gerenciado de contêiner do Azure – crie um registro de contêiner gerenciado em sua assinatura do Azure. Por exemplo, use o Portal do Azure ou a CLI do Azure 2.0. 
- CLI do Docker – para configurar o computador local como um host do Docker e acessar os comandos da CLI do Docker, instale o Mecanismo do Docker. 

## <a name="create-webhook-azure-portal"></a>Criar webhook no portal do Azure

1. Faça logon no portal do Azure e navegue até o registro no qual você deseja criar webhooks. 

2. Na folha do contêiner, selecione a guia "Webhooks". 

3. Selecione "Adicionar" na barra de ferramentas da folha do webhook. 

4. Complete o formulário *Criar Webhook* com as seguintes informações:

| Valor | Descrição |
|---|---|
| Nome | O nome que você deseja dar ao webhook. Ele pode conter apenas letras minúsculas e números e entre 5 e 50 caracteres. |
| URI de serviço | O URI para que o webhook deveria enviar notificações POST. |
| Cabeçalhos personalizados | Cabeçalhos que você deseja passar junto com a solicitação POST. Eles devem estar no formato "chave: valor". |
| Ações de gatilho | Ações que disparam o webhook. Atualmente, webhooks podem ser acionados por ações de exclusão e/ou de push para uma imagem. |
| Status | O status do webhook depois que ele é criado. Ele é habilitado por padrão. |
| Escopo | O escopo no qual o webhook funciona. Por padrão, o escopo é para todos os eventos no registro. Pode ser especificado para um repositório ou uma marca usando o formato "repositório: marca". |

Formulário de webhook de exemplo:

![INTERFACE DO USUÁRIO DE DC/SO](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook-azure-cli"></a>Criar webhook na CLI do Azure

Para criar um webhook usando a CLI do Azure, use o comando [az acr webhook create](/cli/azure/acr/webhook#create).

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Testar webhook

### <a name="azure-portal"></a>Portal do Azure

Antes de usar o webhook em ações de exclusão e de push na imagem de contêiner, ele pode ser testado usando o botão **Executar ping**. Quando usado, o Executar ping envia uma solicitação POST genérica para o ponto de extremidade especificado e registra a resposta. Isso é útil para verificar se o webhook foi configurado corretamente.

1. Selecione o webhook que você deseja testar. 
2. Na barra de ferramentas superior, selecione a ação "Executar ping". 
3. Verifique a solicitação e a resposta.

### <a name="azure-cli"></a>CLI do Azure

Para testar um webhook ACR com a CLI do Azure, use o comando [az acr webhook ping](/cli/azure/acr/webhook#ping).

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Para ver os resultados, use o comando [az acr webhook list-events](/cli/azure/acr/webhook#list-events). 

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Excluir webhook

### <a name="azure-portal"></a>Portal do Azure

Cada webhook pode ser excluído selecionando-se o webhook e, em seguida, o botão Excluir no Portal do Azure.

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```