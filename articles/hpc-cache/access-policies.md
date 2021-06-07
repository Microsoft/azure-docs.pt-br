---
title: Usar políticas de acesso no cache do HPC do Azure
description: Como criar e aplicar políticas de acesso personalizadas para limitar o acesso do cliente aos destinos de armazenamento no cache do HPC do Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/11/2021
ms.author: v-erkel
ms.openlocfilehash: eb9e71cc8ec463077e3b12b8738203a4945a2eab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103471786"
---
# <a name="control-client-access"></a>Controlar o acesso do cliente

Este artigo explica como criar e aplicar políticas de acesso de cliente personalizadas para seus destinos de armazenamento.

As políticas de acesso para cliente controlam como os clientes têm permissão para se conectar às exportações de destino de armazenamento. Você pode controlar coisas como o controle de rede e o acesso de leitura/gravação no host do cliente ou no nível do servidor.

As políticas de acesso são aplicadas a um caminho de namespace, o que significa que você pode usar políticas de acesso diferentes para duas exportações diferentes em um sistema de armazenamento NFS.

Esse recurso é para fluxos de trabalho em que você precisa controlar como diferentes grupos de clientes acessam os destinos de armazenamento.

Se você não precisar de controle refinado sobre o acesso de destino de armazenamento, poderá usar a política padrão ou poderá personalizar a política padrão com regras extras. Por exemplo, se você quiser habilitar o comprimir raiz para todos os clientes que se conectam por meio do cache, você pode editar a política denominada **Default** para adicionar a configuração de comprimir raiz.

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

Essa configuração controla como o cache HPC do Azure trata as solicitações do usuário raiz em computadores cliente. Quando o comprimir raiz está habilitado, os usuários raiz de um cliente são mapeados automaticamente para um usuário sem privilégios quando enviam solicitações por meio do cache do HPC do Azure. Ele também impede solicitações de cliente de usar bits de permissão set-UID.

Se o comprimir raiz estiver desabilitado, uma solicitação do usuário raiz do cliente (UID 0) será passada para um sistema de armazenamento NFS de back-end como raiz. Essa configuração pode permitir o acesso impróprio a arquivos.

A configuração de comprimir raiz para solicitações de cliente pode ajudar a compensar a ``no_root_squash`` configuração necessária em sistemas nas que são usados como destinos de armazenamento. (Leia mais sobre os [pré-requisitos de destino de armazenamento NFS](hpc-cache-prerequisites.md#nfs-storage-requirements).) Ele também pode melhorar a segurança quando usado com destinos do armazenamento de BLOBs do Azure.

Se você ativar o comprimir raiz, também deverá definir o valor de usuário da ID anônima. O portal aceita valores inteiros entre 0 e 4294967295. (Os valores antigos-2 e-1 têm suporte para compatibilidade com versões anteriores, mas não são recomendados para novas configurações.)

Esses valores são mapeados para valores de usuário específicos:

* **-2** ou **65534** (ninguém)
* **-1** ou **65535** (sem acesso)
* **0** (raiz sem privilégios)

Seu sistema de armazenamento pode ter outros valores com significados especiais.

## <a name="update-access-policies"></a>Atualizar políticas de acesso

Você pode editar ou excluir políticas de acesso da tabela na página **políticas de acesso para cliente** .

Clique no nome da política para abri-lo para edição.

Para excluir uma política, marque a caixa de seleção ao lado de seu nome na lista e clique no botão **excluir** na parte superior da lista. Não é possível excluir a política denominada "default".

> [!NOTE]
> Não é possível excluir uma política de acesso que está em uso. Remova a política de qualquer caminho de namespace que a inclua antes de tentar excluí-la.

## <a name="next-steps"></a>Próximas etapas

* Aplique políticas de acesso nos caminhos de namespace para seus destinos de armazenamento. Leia [Configurar o namespace agregado](add-namespace-paths.md) para saber como.
