---
title: Usar políticas de acesso no cache do HPC do Azure
description: Como criar e aplicar políticas de acesso personalizadas para limitar o acesso do cliente aos destinos de armazenamento no cache do HPC do Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 12/28/2020
ms.author: v-erkel
ms.openlocfilehash: 795b194eb7cd31e633128c22ddffe808b32e07da
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/29/2020
ms.locfileid: "97802398"
---
# <a name="use-client-access-policies"></a>Usar políticas de acesso para cliente

Este artigo explica como criar e aplicar políticas de acesso de cliente personalizadas para seus destinos de armazenamento.

As políticas de acesso para cliente controlam como os clientes são capazes de se conectar às exportações de destino de armazenamento. Você pode controlar coisas como o controle de rede e o acesso de leitura/gravação no host do cliente ou no nível do servidor.

As políticas de acesso são aplicadas a um caminho de namespace, o que significa que você pode usar políticas de acesso diferentes para duas exportações diferentes em um sistema de armazenamento NFS.

Esse recurso é para fluxos de trabalho em que você precisa controlar como diferentes grupos de clientes acessam os destinos de armazenamento.

Se você não precisar de controle refinado sobre o acesso de destino de armazenamento, poderá usar a política padrão ou poderá personalizar a política padrão com regras extras.

## <a name="create-a-client-access-policy"></a>Criar uma política de acesso de cliente

Use a página **políticas de acesso para cliente** no portal do Azure para criar e gerenciar políticas. <!-- is there AZ CLI for this? -->

[![captura de tela da página políticas de acesso do cliente. Várias políticas são definidas e algumas são expandidas para mostrar suas regras](media/policies-overview.png)](media/policies-overview.png#lightbox)

Cada política é composta por regras. As regras são aplicadas aos hosts na ordem do menor escopo (host) para o maior (padrão). A primeira regra que corresponde é aplicada e as regras posteriores são ignoradas.

Para criar uma nova política de acesso, clique no botão **+ Adicionar política de acesso** na parte superior da lista. Dê um nome à nova política de acesso e insira pelo menos uma regra.

![captura de tela de políticas de acesso edite folha com várias regras preenchidas. Clique em OK para salvar a regra.](media/add-policy.png)

O restante desta seção explica os valores que você pode usar em regras.

### <a name="scope"></a>Escopo

O termo de escopo e o filtro de endereço funcionam juntos para definir quais clientes são afetados pela regra.

Use-os para especificar se a regra se aplica a um cliente individual (host), a um intervalo de endereços IP (rede) ou a todos os clientes (padrão).

Selecione o valor de **escopo** apropriado para sua regra:

* **Host** -a regra se aplica a um cliente individual
* **Rede** -a regra se aplica aos clientes em um intervalo de endereços IP
* **Padrão** – a regra se aplica a todos os clientes.

As regras em uma política são avaliadas nessa ordem. Depois que uma solicitação de montagem de cliente corresponde a uma regra, as outras são ignoradas.

### <a name="address-filter"></a>Filtro de endereço

O valor do **filtro de endereço** especifica quais clientes correspondem à regra.

Se você definir o escopo como **host**, poderá especificar apenas um endereço IP no filtro. Para o **padrão** de configuração de escopo, você não pode inserir nenhum endereço IP no campo de **filtro de endereço** porque o escopo padrão corresponde a todos os clientes.

Especifique o endereço IP ou o intervalo de endereços para esta regra. Use a notação CIDR (exemplo: 0.1.0.0/16) para especificar um intervalo de endereços.

### <a name="access-level"></a>Nível de acesso

Defina quais privilégios conceder aos clientes que correspondem ao escopo e ao filtro.

As opções são **leitura/gravação**, **somente leitura** ou **nenhum acesso**.

### <a name="suid"></a>SUID

Marque a caixa **suid** para permitir que os arquivos no armazenamento definam IDs de usuário no acesso.

SUID normalmente é usado para aumentar temporariamente os privilégios de um usuário para que o usuário possa realizar uma tarefa relacionada a esse arquivo.

### <a name="submount-access"></a>Acesso de submontagem

Marque esta caixa para permitir que os clientes especificados montem diretamente os subdiretórios desta exportação.

### <a name="root-squash"></a>Comprimir raiz

Escolha se deseja ou não definir o comprimir raiz para clientes que correspondem a essa regra.

Esse valor permite que você permita o comprimi raiz no nível de exportação de armazenamento. Você também pode [definir o comprimir raiz no nível de cache](configuration.md#configure-root-squash).

Se você ativar o comprimir raiz, também deverá definir o valor do usuário da ID anônima como uma destas opções:

* **-2** (ninguém)
* **65534** (ninguém)
* **-1** (sem acesso)
* **65535** (sem acesso)
* **0** (raiz sem privilégios)

## <a name="update-access-policies"></a>Atualizar políticas de acesso

Você pode editar ou excluir políticas de acesso da tabela na página **políticas de acesso para cliente** .

Clique no nome da política para abri-lo para edição.

Para excluir uma política, marque a caixa de seleção ao lado de seu nome na lista e clique no botão **excluir** na parte superior da lista. Não é possível excluir a política denominada "default".

> [!NOTE]
> Não é possível excluir uma política de acesso que está em uso. Remova a política de qualquer caminho de namespace que a inclua antes de tentar excluí-la.

## <a name="next-steps"></a>Próximas etapas

* Aplique políticas de acesso nos caminhos de namespace para seus destinos de armazenamento. Leia [Configurar o namespace agregado](add-namespace-paths.md) para saber como.
