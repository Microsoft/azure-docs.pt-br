---
title: Início Rápido – Criar um hub de notificação do Azure usando a CLI do Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a criar um hub de notificação do Azure usando a CLI do Azure.
services: notification-hubs
author: dbradish-microsoft
manager: barbkess
editor: sethmanheim
ms.service: notification-hubs
ms.devlang: azurecli
ms.workload: mobile
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: dbradish
ms.reviewer: thsomasu
ms.lastreviewed: 03/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: d8400eb051c09fac4cb88863ad2fac12d2ca0a1b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789853"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Criar um hub de notificação do Azure usando a CLI do Azure

Os Hubs de Notificação do Azure fornecem um mecanismo de push expansível e fácil de usar que permite que você envie notificações para qualquer plataforma (iOS, Android, Windows, Kindle, Baidu etc.) de qualquer back-end (nuvem ou local). Para obter mais informações sobre o serviço, confira [O que são os Hubs de Notificação do Azure?](notification-hubs-push-notification-overview.md).

Neste início rápido, você criará um hub de notificação usando a CLI do Azure. A primeira seção fornece as etapas usadas para criar um namespace dos Hubs de Notificação. A segunda seção fornece as etapas para criar um hub de notificação em um namespace existente. Além disso, você aprenderá a criar uma política de acesso personalizada.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> Os Hubs de Notificação exigem a versão 2.0.67 ou posterior da CLI do Azure. Execute [az version](/cli/azure/reference-index#az_version) para localizar a versão e as bibliotecas dependentes que estão instaladas. Para fazer a atualização para a versão mais recente, execute [az upgrade](/cli/azure/reference-index#az_upgrade).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Os Hubs de Notificação do Azure, assim como todos os recursos do Azure, precisam ser implantados em um grupo de recursos.  Os grupos de recursos facilitam organizar e gerenciar os recursos relacionados ao Azure.  Confira [O que é o Azure Resource Manager](../azure-resource-manager/management/overview.md) para saber mais sobre grupos de recursos.

Para este início rápido, crie um grupo de recursos chamado **spnhubrg** no local **eastus** com o comando [az group create](/cli/azure/group#az_group_create) a seguir.

```azurecli
az group create --name spnhubrg --location eastus
```

## <a name="create-a-notification-hubs-namespace"></a>Criar um namespace dos Hubs de Notificação

1. Crie um namespace para seus hubs de notificação.

   Um namespace contém um ou mais hubs e o nome deve ser exclusivo em todas as assinaturas do Azure e deve conter pelo menos seis caracteres de comprimento. Para verificar a disponibilidade de um nome, use o comando [az notification-hub namespace check-availability](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability).

   ```azurecli
   az notification-hub namespace check-availability --name spnhubns
   ```

   A CLI do Azure responderá à sua solicitação de disponibilidade exibindo a seguinte saída no console:

   ```shell
   {
   "id": "/subscriptions/yourSubscriptionID/providers/Microsoft.NotificationHubs/checkNamespaceAvailability",
   "isAvailable": true,
   "location": null,
   "name": "spnhubns",
   "properties": false,
   "sku": null,
   "tags": null,
   "type": "Microsoft.NotificationHubs/namespaces/checkNamespaceAvailability"
   }
   ```

   Observe a segunda linha da resposta da CLI do Azure, `"isAvailable": true`. Essa linha indicará `false` se o nome desejado que você especificou para o namespace não estiver disponível. Depois de confirmar a disponibilidade do nome, execute o comando [az notification-hub namespace create](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) para criar seu namespace.  

   ```azurecli
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

   Se o `--name` fornecido ao comando `az notification-hub namespace create` não estiver disponível ou não atender às [regras de nomenclatura e restrições para os recursos do Azure](../azure-resource-manager/management/resource-name-rules.md), a CLI do Azure responderá com a seguinte saída no console:

   ```shell
   #the name is not available
   The specified name is not available. For more information visit https://aka.ms/eventhubsarmexceptions.

   #the name is invalid
   The specified service namespace is invalid.
   ```

   Se o primeiro nome que você tentou não for bem-sucedido, selecione outro nome para o novo namespace e execute o comando `az notification-hub namespace create` novamente.

   > [!NOTE]
   > Desta etapa em diante, você precisará substituir o valor do parâmetro `--namespace` em cada comando da CLI do Azure copiado deste guia de início rápido.

2. Obter uma lista de namespaces.

   Para ver os detalhes sobre o seu novo namespace, use o comando [az notification-hub namespace list](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-list). O parâmetro `--resource-group` é opcional se você quiser ver todos os namespaces de uma assinatura.

   ```azurecli
   az notification-hub namespace list --resource-group spnhubrg
   ```

## <a name="create-notification-hubs"></a>Criar hubs de notificação

1. Criar seu primeiro hub de notificação.

   Agora, um ou mais hubs de notificação poderão ser criados no novo namespace. Execute o comando [az notification-hub create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) para criar um hub de notificação.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Criar um segundo hub de notificação.

   Vários hubs de notificação podem ser criados em um namespace. Para criar um segundo hub de notificação no mesmo namespace, execute o comando `az notification-hub create` novamente usando outro nome de hub.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

3. Obtenha uma lista de hubs de notificação.

   A CLI do Azure retorna uma mensagem de sucesso ou de erro a cada comando executado; no entanto, ser capaz de consultar uma lista de hubs de notificação é tranquilizador. O comando [az notification-hub list](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-list) foi projetado para essa finalidade.

   ```azurecli
   az notification-hub list --resource-group spnhubrg --namespace-name spnhubns --output table
   ```

## <a name="work-with-access-policies"></a>Trabalhar com políticas de acesso

1. Os Hubs de Notificação do Azure usam [segurança de assinatura de acesso compartilhado](./notification-hubs-push-notification-security.md) por meio do uso de políticas de acesso. Duas políticas são criadas automaticamente quando você cria um hub de notificação. As cadeias de conexão dessas políticas são necessárias para configurar as notificações por push. O comando [az notification-hub authorization-rule list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) fornece uma lista de nomes de políticas e os respectivos grupos de recursos.

   ```azurecli
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > Não use a política _DefaultFullSharedAccessSignature_ no aplicativo. Essa política só deve ser usada no back-end. Use somente `Listen` políticas de acesso no aplicativo cliente.

2. Caso deseje criar regras de autorização adicionais com nomes significativos, crie e personalize uma política de acesso própria usando o comando [az notification-hub authorization-rule create](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create). O parâmetro `--rights` é uma lista delimitada por espaço com as permissões que você deseja atribuir.

   ```azurecli
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Manage Send
   ```

3. Há dois conjuntos de chaves e cadeias de conexão para cada política de acesso. Você precisará delas mais tarde para [configurar um hub de notificação](./configure-notification-hub-portal-pns-settings.md). Para listar as chaves e as cadeias de conexão para uma política de acesso dos Hubs de Notificação, use o comando [az notification-hub authorization-rule list-keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys).

   ```azurecli
   # query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output table
   ```

   ```azurecli
   # query the keys and connection strings for a custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > Um [namespace dos Hubs de Notificação](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) e um [hub de notificação](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) têm políticas de acesso separadas. Verifique se você está usando a referência da CLI do Azure correta ao consultar as chaves e as cadeias de conexão.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando o grupo de recursos e todos os recursos relacionados não forem mais necessários, use o comando [az group delete](/cli/azure/group) para removê-los:

```azurecli
az group delete --name spnhubrg
```

## <a name="next-steps"></a>Próximas etapas

* Neste Início Rápido, você criou um hub de notificação. Para saber como configurar o hub com as configurações do PNS (Sistema de Notificação de Plataforma), confira [Configurar as notificações por push em um hub de notificação](configure-notification-hub-portal-pns-settings.md)

* Descubra as inúmeras funcionalidades para gerenciar hubs de notificação com a CLI do Azure:

  [Lista de referência completa dos Hubs de Notificação](/cli/azure/ext/notification-hub/notification-hub)

  [Lista de referência de namespaces dos Hubs de Notificação](/cli/azure/ext/notification-hub/notification-hub/namespace)

  [Lista de referência de regra de autorização dos Hubs de Notificação](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)

  [Lista de referência de credenciais dos Hubs de Notificação](/cli/azure/ext/notification-hub/notification-hub/credential)