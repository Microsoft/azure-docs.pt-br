---
title: Montar clientes no cluster do Microsoft Azure FXT Edge Filer
description: Como máquinas de cliente NFS podem montar o cache de armazenamento híbrido do Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 5471bf4041275d5988414def99dd2130f51fbb80
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67828029"
---
# <a name="tutorial-mount-the-cluster"></a>Tutorial: Montar o cluster

Este tutorial ensina a montar clientes NFS no cluster do Azure FXT Edge Filer. Os clientes montam os caminhos de namespace virtual atribuídos por você durante a adição do armazenamento back-end. 

Este tutorial ensina: 

> [!div class="checklist"]
> * Estratégias para fazer o balanceamento de carga de clientes entre o intervalo de endereços IP voltados para o cliente
> * Como construir um caminho de montagem de uma junção de endereço IP voltado para o cliente e o namespace
> * Quais argumentos usar em um comando de montagem

Este tutorial demora cerca de 45 minutos para ser concluído.

## <a name="steps-to-mount-the-cluster"></a>Etapas para montar o cluster

Execute estas etapas para conectar computadores cliente ao seu cluster do Azure FXT Edge Filer.

1. Decida como balancear a carga de tráfego do cliente entre seus nós de cluster. Leia [Balancear a carga do cliente](#balance-client-load) a seguir, para saber mais detalhes. 
1. Identifique o endereço IP do cluster e o caminho de junção para montagem.
1. Determine o caminho voltado para o cliente da montagem.
1. Emita o [comando de montagem](#use-recommended-mount-command-options) com os argumentos adequados.

## <a name="balance-client-load"></a>Balancear a carga do cliente

Para ajudar a balancear as solicitações do cliente entre todos os nós do cluster, monte os clientes no intervalo completo dos endereços IP voltados para o cliente. Há várias maneiras de automatizar essa tarefa.

Para saber mais sobre o balanceamento de carga de DNS round robin para o cluster, leia [Configurar o DNS para o cluster do Azure FXT Edge Filer](fxt-configure-network.md#configure-dns-for-load-balancing). Para usar esse método, você precisa manter um servidor DNS, o que não é explicado neste artigo.

Para instalações menores, é mais simples usar um script para atribuir endereços IP por todo o intervalo em tempo de montagem do cliente. 

Outros métodos de balanceamento de carga podem ser adequados para sistemas grandes ou complexos. Consulte um representante da Microsoft ou abra uma [solicitação de suporte](fxt-support-ticket.md) para receber ajuda. No momento, o Azure Load Balancer *não tem suporte* com o Azure FXT Edge Filer.

## <a name="create-the-mount-command"></a>Criar o comando de montagem 

No cliente, o comando ``mount`` mapeia o servidor virtual (vserver) no cluster do Azure FXT Edge Filer até um caminho no sistema de arquivos local. 

O formato é ``mount <FXT cluster path> <local path> {options}``

Existem três elementos no comando de montagem: 

* caminho do cluster – uma combinação de endereço IP e o caminho de junção do namespace descrito abaixo
* Caminho local – o caminho no cliente 
* opções de comando de montagem – (listadas em [Usar opções de comando de montagem recomendadas](#use-recommended-mount-command-options))

### <a name="create-the-cluster-path"></a>Criar o caminho do cluster

O caminho do cluster é uma combinação do *endereço IP* do vserver com o caminho até uma *junção de namespace*. A junção de namespace é um caminho virtual definido durante a [adição do sistema de armazenamento](fxt-add-storage.md#create-a-junction).

Por exemplo, se você tiver usado ``/fxt/files`` como caminho do namespace, os clientes montarão *endereço_IP*:/fxt/files no ponto de montagem local. 

![Caixa de diálogo "Adicionar nova junção" com /avere/files no campo de caminho do namespace](media/fxt-mount/fxt-junction-example.png)

O endereço IP é um dos endereços IP voltados para o cliente definidos para o vserver. É possível encontrar o intervalo dos IPs voltados para o cliente em dois locais no Painel de Controle do cluster:

* Tabela **VServers** (guia Painel) – 

  ![A guia Painel do Painel de Controle, com a guia VServer selecionada na tabela de dados abaixo do gráfico, e a seção do endereço IP circulada](media/fxt-mount/fxt-ip-addresses-dashboard.png)

* Página de configurações **Rede voltada para o cliente** – 

  ![Página de configuração Configurações > VServer > Rede voltada para o cliente com um círculo ao redor da seção de Intervalo de Endereços da tabela para um determinado vserver](media/fxt-mount/fxt-ip-addresses-settings.png)

Combine o endereço IP e o caminho do namespace para formar o caminho do cluster para o comando de montagem. 

Exemplo de comando de montagem do cliente: ``mount 10.0.0.12:/sd-access /mnt/fxt {options}``

### <a name="create-the-local-path"></a>Criar o caminho local

Você decide o caminho local para o comando de montagem. Você pode definir qualquer estrutura de caminho que quiser como parte do namespace virtual. Crie um namespace e um caminho local conveniente para o fluxo de trabalho do cliente. 

Para saber mais sobre o namespace voltado ao cliente, leia a [visão geral do namespace](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html) do Guia de configuração do cluster.

Além dos caminhos, inclua as [opções do comando de montagem](#use-recommended-mount-command-options) descritas abaixo ao montar cada cliente.

### <a name="use-recommended-mount-command-options"></a>Usar as opções de comando de montagem recomendadas

Para garantir uma montagem de cliente sem dificuldades, passe esses argumentos e configurações em seu comando de montagem: 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Configurações necessárias | |
--- | --- 
``hard`` | As montagens flexíveis do cluster do Azure FXT Edge Filer estão associadas às falhas no aplicativo e à possível perda de dados. 
``proto=netid`` | Essa opção é compatível com o tratamento apropriado de erros de rede NFS.
``mountproto=netid`` | Essa opção é compatível com o tratamento apropriado de erros de rede para operações de montagem.
``retry=n`` | Configure ``retry=30`` para evitar falhas transitórias de montagem. Recomenda-se um valor diferente em montagens em primeiro plano.

| Configurações preferenciais  | |
--- | --- 
``nointr``            | Se os seus clientes usarem kernels de sistema operacional mais antigos (anteriores a abril de 2008) que dão suporte a essa opção, use-os. A opção "intr" é o padrão.

## <a name="next-steps"></a>Próximas etapas

Depois de montar os clientes, teste seu fluxo de trabalho e comece a usar seu cluster.

Se você precisar mover dados para um novo arquivista central, aproveite a estrutura de cache usando a ingestão de dados em paralelo. Algumas estratégias estão descritas em [Mover dados para um cluster vFXT](https://docs.microsoft.com/azure/avere-vfxt/avere-vfxt-data-ingest). O Avere vFXT para Azure é um produto baseado em nuvem que usa uma tecnologia de armazenamento em cache bem semelhante ao Azure FXT Edge Filer.

Leia [Monitorar o status de hardware do Azure FXT Edge Filer](fxt-monitor.md) se você precisar solucionar problemas de hardware. 
