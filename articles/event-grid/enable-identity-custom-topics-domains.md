---
title: Habilitar identidade gerenciada em domínios e tópicos personalizados da grade de eventos do Azure
description: Este artigo descreve como habilitar a identidade de serviço gerenciada para um tópico ou domínio personalizado da grade de eventos do Azure.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 06fd4d6e472b33496e773596b0f3afc8e70be948
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630339"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-custom-topic-or-domain"></a>Atribuir uma identidade gerenciada pelo sistema a um tópico ou domínio personalizado da grade de eventos 
Este artigo mostra como habilitar uma identidade gerenciada pelo sistema para um tópico personalizado da grade de eventos ou um domínio. Para saber mais sobre identidades gerenciadas, confira [o que são identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="enable-identity-at-the-time-of-creation"></a>Habilitar identidade no momento da criação

### <a name="using-azure-portal"></a>Usando o Portal do Azure
Você pode habilitar a identidade atribuída pelo sistema para um tópico personalizado ou um domínio ao criá-lo no portal do Azure. A imagem a seguir mostra como habilitar uma identidade gerenciada pelo sistema para um tópico personalizado. Basicamente, você seleciona a opção **Habilitar identidade atribuída pelo sistema** na página **Avançado** do assistente de criação de tópico. Você também verá essa opção na página **avançado** do assistente de criação de domínio. 

![Habilitar identidade ao criar um tópico personalizado](./media/managed-service-identity/create-topic-identity.png)

### <a name="using-azure-cli"></a>Usando a CLI do Azure
Você também pode usar o CLI do Azure para criar um tópico ou domínio personalizado com uma identidade atribuída pelo sistema. Use o comando `az eventgrid topic create` com o parâmetro `--identity` definido como `systemassigned`. Se você não especificar um valor para este parâmetro, o valor padrão `noidentity` será usado. 

```azurecli-interactive
# create a custom topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Da mesma forma, você pode usar o comando `az eventgrid domain create` para criar um domínio com uma identidade gerenciada pelo sistema.

## <a name="enable-identity-for-an-existing-custom-topic-or-domain"></a>Habilitar identidade para um tópico ou domínio personalizado existente
Nesta seção, você aprenderá a habilitar uma identidade gerenciada pelo sistema para um domínio ou tópico personalizado existente. 

### <a name="using-azure-portal"></a>Usando o Portal do Azure
O procedimento a seguir mostra como habilitar a identidade gerenciada pelo sistema para um tópico personalizado. As etapas para habilitar uma identidade para um domínio são semelhantes. 

1. Vá para o [Portal do Azure](https://portal.azure.com).
2. Procure **Tópicos da grade de eventos** na barra de pesquisa na parte superior.
3. Selecione o **tópico personalizado** para o qual você deseja habilitar a identidade gerenciada. 
4. Alterne para a guia **Identidade**. 
5. Ative a opção para **habilitar a identidade** . 
1. Selecione **salvar** na barra de ferramentas para salvar a configuração. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="Página de identidade para um tópico personalizado"::: 

Você pode usar etapas semelhantes para habilitar uma identidade para um domínio de grade de eventos.

### <a name="use-the-azure-cli"></a>Usar a CLI do Azure
Use o `az eventgrid topic update` comando com `--identity` definido como `systemassigned` para habilitar a identidade atribuída pelo sistema para um tópico personalizado existente. Se quiser desabilitar a identidade, especifique `noidentity` como o valor. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

O comando para atualizar um domínio existente é semelhante (`az eventgrid domain update`).


## <a name="next-steps"></a>Próximas etapas
Adicione a identidade a uma função apropriada (por exemplo, remetente de dados do barramento de serviço) no destino (por exemplo, uma fila do barramento de serviço). Para obter etapas detalhadas, consulte [Adicionar identidade às funções do Azure em destinos](add-identity-roles.md). 
