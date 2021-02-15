---
title: Gerenciar cluster do Avere vFXT – Azure
description: Como gerenciar o cluster do Avere – adicionar ou remover nós, reiniciar, parar ou destruir o cluster vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 4135bfe528c33a2beaeb21438181deb5b19ad12e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85505487"
---
# <a name="manage-the-avere-vfxt-cluster"></a>Gerenciar cluster do Avere vFXT

Em algum momento do ciclo de vida de seu avere vFXT para o cluster do Azure, talvez seja necessário adicionar nós de cluster ou iniciar ou reinicializar o cluster. Quando seu projeto for concluído, você precisará saber como parar o cluster e removê-lo permanentemente.

Este artigo explica como adicionar ou remover nós de cluster e outras operações básicas de cluster. Se você precisar alterar as configurações de cluster ou monitorar seu trabalho, use o [painel de controle avere](avere-vfxt-cluster-gui.md).

Dependendo da tarefa de gerenciamento, talvez seja necessário usar uma das três ferramentas diferentes: painel de controle avere, script de gerenciamento de cluster de linha de comando vfxt.py e o portal do Azure.

Esta tabela fornece uma visão geral de quais ferramentas podem ser usadas para cada tarefa.

| Ação | Painel de Controle do Avere | vfxt.py  | Portal do Azure |
| --- | --- | --- | --- |
| Adicionar nós de cluster | não | sim | não |
| Remover nós do cluster | sim | não | não |
| Parar um nó de cluster | sim (também pode reiniciar ou reinicializar os serviços) | não | desligar um nó de VM no portal é interpretado como uma falha de nó |
| Iniciar um nó parado | não | não | sim |
| Destruir um único nó de cluster | não | não | sim |
| Reinicializar o cluster |  |  |  |
| Desligar ou parar o cluster com segurança | sim | sim | não |
| Destruir o cluster  | não | sim | Sim, mas não há garantia de integridade de dados |

Instruções detalhadas para cada ferramenta estão incluídas abaixo.

## <a name="about-stopped-instances-in-azure"></a>Sobre as instâncias paradas no Azure

Quando você desliga ou para qualquer VM do Azure, ela para de incorrer em encargos de computação, mas você ainda deve pagar por seu armazenamento. Se você desligar um nó de vFXT ou todo o cluster inteiro vFXT e não pretender reiniciá-lo, deverá usar o portal do Azure para excluir as VMs relacionadas.

No portal do Azure, um nó *parado* (que pode ser reiniciado) mostra o status **parado** no portal do Azure. Um nó *excluído* mostra o status **parado (desalocado)** e não gera mais cobranças de armazenamento ou de computação.

Antes de excluir a VM, todos os dados alterados devem ter sido gravados do cache para o armazenamento de back-end usando as opções do Painel de Controle do Avere ou vfxt.py para interromper ou desligar o cluster.

## <a name="manage-the-cluster-with-avere-control-panel"></a>Gerenciar o cluster com o Painel de Controle do Avere

O Painel de Controle do Avere pode ser usado para estas tarefas:

* Interromper ou reinicializar nós individuais
* Remover um nó do cluster
* Interromper ou reinicializar todo o cluster

O painel de controle avere prioriza a integridade dos dados, portanto, ele tenta gravar quaisquer dados alterados no armazenamento de back-end antes de uma operação possivelmente destrutiva. Isso o torna uma opção mais segura do que a portal do Azure.

Acesse o Painel de Controle do Avere de um navegador da Web. Siga as instruções em [Acessar o cluster vFXT](avere-vfxt-cluster-gui.md) se precisar de ajuda.

### <a name="manage-nodes-with-avere-control-panel"></a>Gerenciar nós com o Painel de Controle do Avere

A página de configurações de **Nós FXT** tem controles para gerenciar nós individuais.

Para desligar, reinicializar ou remover um nó, localize o nó na lista na página **Nós FXT** e clique no botão apropriado na sua coluna **Ações**.

> [!NOTE]
> Endereços IP podem se mover entre nós de cluster quando o número de nós ativos muda.

Leia [Cluster > Nós FXT](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html#gui-fxt-nodes>) no guia de configurações do cluster do Avere para obter mais informações.

### <a name="stop-or-reboot-the-cluster-with-avere-control-panel"></a>Parar ou reiniciar o cluster com o Painel de Controle do Avere

A página de configurações **Manutenção do Sistema** tem comandos para reiniciar os serviços de cluster, reinicializar o cluster ou desligar o cluster com segurança. Leia [Administração > Manutenção do Sistema](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_system_maintenance.html#gui-system-maintenance>) (no guia de configurações de cluster do Avere) para obter detalhes.

Quando um cluster começa a desligar, ele posta mensagens de estado na guia **painel** . Após alguns instantes, as mensagens param e, eventualmente, a sessão do painel de controle avere para de responder, o que significa que o cluster foi desligado.

## <a name="manage-the-cluster-with-vfxtpy"></a>Gerenciar o cluster com vfxt.py

vfxt.py é uma ferramenta de linha de comando para a criação e gerenciamento de cluster.

O vfxt.py vem pré-instalado na VM do controlador de cluster. Se desejar instalá-lo em outro sistema, confira a documentação em <https://github.com/Azure/AvereSDK>.

O script vfxt.py pode ser usado para estas tarefas de gerenciamento de cluster:

* Adicionar novos nós a um cluster
* Interromper ou iniciar um cluster  
* Destruir um cluster

Como o Painel de Controle do Avere, operações vfxt.py tentam garantir que os dados alterados sejam armazenados permanentemente no armazenamento de back-end antes de desligar ou destruir o cluster ou nó. Isso o torna uma opção mais segura do que a portal do Azure.

Um guia completo de uso do vfxt.py está disponível no GitHub: [Gerenciamento de clusters na nuvem com vfxt.py](https://github.com/azure/averesdk/blob/master/docs/README.md)

### <a name="add-cluster-nodes-with-vfxtpy"></a>Adicionar nós de cluster com vfxt.py

Um exemplo de script de comando para adicionar nós de cluster está incluído no controlador de cluster. Localize ``./add-nodes`` no controlador e abra-o em um editor para personalizá-lo com suas informações de cluster.

O cluster deve estar em execução para usar esse comando.

Forneça os seguintes valores:

* Nome do grupo de recursos para o cluster e também para recursos de rede e armazenamento se eles não estiverem no mesmo grupo de recursos que o cluster
* Localização do cluster
* Sub-rede e rede de cluster
* Função de acesso de nó de cluster (use a função interna [operador avere](../role-based-access-control/built-in-roles.md#avere-operator))
* Endereço IP de gerenciamento de cluster e senha administrativa
* Número de nós a serem adicionados (1, 2 ou 3)
* Valores de tamanho de tipo e cache da instância de nó

Se você não estiver usando o protótipo, precisará criar um comando como o mostrado a seguir, incluindo todas as informações descritas acima.

```bash
   vfxt.py --cloud-type azure --from-environment \
   --resource-group GROUP_NAME \
   [--network-resource-group GROUP_NAME --storage-resource-group GROUP_NAME]  \
   --location LOCATION --azure-network NETWORK_NAME --azure-subnet SUBNET_NAME \
   --add-nodes --nodes NODE_COUNT \
   --management-address CLUSTER_IP --admin-password ADMIN_PASSWORD \
   --instance-type TYPE --node-cache-size SIZE \
   --azure-role "Avere Operator" \
   --log ~/vfxt.log
```

Para obter mais informações, leia [Adicionar nós a um cluster](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#add-nodes-to-a-cluster) no guia de uso do vfxt.py.

### <a name="stop-a-cluster-with-vfxtpy"></a>Parar um cluster com vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --stop --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET
```

### <a name="start-a-stopped-cluster-with-vfxtpy"></a>Iniciar um cluster parado com vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --start --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --instances INSTANCE1_ID INSTANCE2_ID INSTANCE3_ID ...
```

Porque o cluster for interrompido, você deve passar identificadores de instância para especificar os nós de cluster. Leia [Como especificar qual cluster modificar](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#specifying-which-cluster-to-modify) no guia de uso de vfxt.py para saber mais.

### <a name="destroy-a-cluster-with-vfxtpy"></a>Destruir um cluster com vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --destroy --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --management-address ADMIN_IP
```

A opção ``--quick-destroy`` pode ser usada se você não quiser salvar os dados alterados do cache de cluster.

Leia o [guia de uso vfxt.py](<https://github.com/Azure/AvereSDK/blob/master/docs/README.md>) para obter informações adicionais.

## <a name="manage-cluster-vms-from-the-azure-portal"></a>Gerenciar VMs do cluster do portal do Azure

O portal do Azure pode ser usado para destruir VMs do cluster individualmente, mas a integridade dos dados não será garantida se o cluster não for desligado corretamente primeiro.

O portal do Azure pode ser usado para estas tarefas de gerenciamento de cluster:

* Iniciar um nó do vFXT parado
* Parar um nó vFXT individual (o cluster interpreta isso como uma falha de nó)
* Destruir um cluster vFXT *se* não for necessário garantir que os dados alterados no cache do cluster sejam gravados no arquivista central
* Remover permanentemente nós de vFXT e outros recursos de cluster após eles terem sido desligados com segurança

### <a name="restart-vfxt-instances-from-the-azure-portal"></a>Reiniciar instâncias do vFXT do portal do Azure

Se você precisar reiniciar um nó parado, deverá usar o portal do Azure. Selecione **máquinas virtuais** no menu à esquerda e, em seguida, clique no nome da VM na lista para abrir a página de visão geral.

Clique no botão **Iniciar** na parte superior da página de visão geral para reativar a VM.

![Tela de portal do Azure mostrando a opção para iniciar uma VM parada](media/avere-vfxt-start-stopped-incurring-annot.png)

### <a name="delete-cluster-nodes"></a>Excluir nós de cluster

Se você quiser excluir um nó do cluster vFXT, mas manter o restante do cluster, primeiro [remova o nó do cluster](#manage-nodes-with-avere-control-panel) com o Painel de Controle do Avere.

> [!CAUTION]
> Se você excluir um nó sem primeiro removê-lo do cluster vFXT, os dados poderão ser perdidos.

Para destruir permanentemente uma ou mais instâncias usadas como nó do vFXT, use o portal do Azure.
Selecione **máquinas virtuais** no menu à esquerda e, em seguida, clique no nome da VM na lista para abrir a página de visão geral.

Clique no botão **Excluir** na parte superior da página de visão geral para destruir permanentemente a VM.

Você pode usar esse método para remover permanentemente nós de cluster após eles terem sido desligados com segurança.

### <a name="destroy-the-cluster-from-the-azure-portal"></a>Destruir o cluster do portal do Azure

> [!NOTE]
> Se você quiser que as alterações de cliente restantes no cache sejam gravadas no armazenamento de back-end, use a opção `--destroy` do vfxt.py ou o Painel de Controle do Avere para desligar o cluster corretamente antes de remover as instâncias de nó no portal do Azure.

Você pode destruir as instâncias de nó excluindo-as permanentemente no portal do Azure. Você pode excluí-las uma de cada vez como descrito acima ou usar a página **Máquinas Virtuais** para encontrar todas as VMs do cluster, selecioná-las com as caixas de seleção e clicar no botão **Excluir** para remover todas elas em uma única ação.

![Lista de VMs no portal, filtrada pelo termo "cluster", com três dos quatro verificados e realçados](media/avere-vfxt-multi-vm-delete.png)

### <a name="delete-additional-cluster-resources-from-the-azure-portal"></a>Excluir recursos de cluster adicionais do portal do Azure

Se você tiver criado recursos adicionais especificamente para o cluster vFXT, talvez queira removê-los como parte da subdivisão do cluster. Não destrua elementos que contêm dados necessários ou quaisquer itens compartilhados com outros projetos.

Além de excluir os nós de cluster, considere remover estes componentes:

* A VM do cluster de controlador
* Discos de dados associados a nós de cluster
* Interfaces de rede e IPs públicos associados a componentes de cluster
* Redes virtuais
* Contêineres de armazenamento e contas de armazenamento (**somente** se eles não contiverem dados importantes)
* Conjunto de disponibilidade

![O portal do Azure lista "todos os recursos" mostrando recursos criados para um cluster de teste](media/avere-vfxt-all-resources-list.png)

### <a name="delete-a-clusters-resource-group-from-the-azure-portal"></a>Excluir grupo de recursos do cluster do portal do Azure

Se você tiver criado um grupo de recursos especificamente para hospedar o cluster, poderá destruir todos os recursos relacionados ao cluster destruindo o grupo de recursos.

> [!Caution]
> Somente destrua o grupo de recursos se tiver certeza de que nada do valor reside no grupo. Por exemplo, verifique se que você moveu todos os dados necessários de qualquer contêiner de armazenamento no grupo de recursos.  

Para excluir um grupo de recursos, clique em **Grupos de recursos** no menu esquerdo do portal e filtre a lista de grupos de recursos para encontrar aquele que você criou para o cluster vFXT. Selecione o grupo de recursos e clique nos três pontos à direita do painel. Selecione **Excluir grupo de recursos**. O portal pedirá que você confirme a exclusão, que é irreversível.

![Grupo de recursos mostrando a ação "Excluir grupo de recursos"](media/avere-vfxt-delete-resource-group.png)
