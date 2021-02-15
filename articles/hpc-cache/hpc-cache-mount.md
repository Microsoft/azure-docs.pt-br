---
title: Montar um cache HPC do Azure
description: Como conectar clientes a um serviço de cache do Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 7f1d8d34d6351fc344fdb101ac8e9a96678df9d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91651421"
---
# <a name="mount-the-azure-hpc-cache"></a>Montar o Azure HPC Cache

Depois que o cache é criado, os clientes NFS podem acessá-lo com um `mount` comando simples. O comando conecta um caminho de destino de armazenamento específico no cache do Azure HPC a um diretório local no computador cliente.

O comando Mount é composto por esses elementos:

* Um dos endereços de montagem do cache (listado na página Visão geral do cache)
* Um caminho de namespace virtual que você definiu para o destino de armazenamento (listado na página namespace de cache)
* O caminho local a ser usado no cliente
* Parâmetros de comando que otimizam o sucesso desse tipo de montagem de NFS

A página **instruções de montagem** do seu cache coleta as informações e as opções recomendadas para você e cria um comando de montagem de protótipo que você pode copiar. Leia [usar o utilitário de instruções de montagem](#use-the-mount-instructions-utility) para obter detalhes.

## <a name="prepare-clients"></a>Preparar clientes

Verifique se os clientes são capazes de montar o cache HPC do Azure seguindo as diretrizes nesta seção.

### <a name="provide-network-access"></a>Fornecer acesso à rede

Os computadores cliente devem ter acesso de rede à rede virtual do cache e à sub-rede privada.

Por exemplo, crie VMs de cliente na mesma rede virtual ou use um ponto de extremidade, gateway ou outra solução na rede virtual para acesso de fora. (Lembre-se de que nada além do próprio cache deve ser hospedado dentro da sub-rede do cache.)

### <a name="install-utilities"></a>Instalar utilitários

Instale o software do utilitário Linux apropriado para dar suporte ao comando de montagem do NFS:

* Para Red Hat Enterprise Linux ou SuSE: `sudo yum install -y nfs-utils`
* Para Ubuntu ou Debian: `sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>Criar um caminho local

Crie um caminho de diretório local em cada cliente para se conectar ao cache. Crie um caminho para cada caminho de namespace que você deseja montar.

Exemplo: `sudo mkdir -p /mnt/hpc-cache-1/target3`

A página [instruções de montagem](#use-the-mount-instructions-utility) no portal do Azure inclui um comando de protótipo que você pode copiar.

Ao conectar o computador cliente ao cache, você associará esse caminho a um caminho de namespace virtual que representa uma exportação de destino de armazenamento. Crie diretórios para cada um dos caminhos de namespace virtual que o cliente usará.

## <a name="use-the-mount-instructions-utility"></a>Usar o utilitário de instruções de montagem

Você pode usar a página **instruções de montagem** no portal do Azure para criar um comando de montagem copiável. Abra a página na seção **Configurar** do modo de exibição de cache no Portal.

Antes de usar o comando em um cliente, verifique se o cliente atende aos pré-requisitos e tem o software necessário para usar o comando NFS, `mount` conforme descrito acima em [preparar clientes](#prepare-clients).

![captura de tela de uma instância de cache do HPC do Azure no portal, com a página Configurar instruções de montagem > carregada](media/mount-instructions.png)

Siga este procedimento para criar o comando Mount.

1. Personalize o campo **caminho do cliente** . Esse campo fornece um comando de exemplo que você pode usar para criar um caminho local no cliente. O cliente acessa o conteúdo do cache do Azure HPC localmente nesse diretório.

   Clique no campo e edite o comando para conter o nome do diretório desejado. O nome aparece no final da cadeia de caracteres após `sudo mkdir -p`

   ![captura de tela do campo caminho do cliente com cursor posicionado no final](media/mount-edit-client.png)

   Depois de terminar de editar o campo, o comando Mount na parte inferior da página é atualizado com o novo caminho do cliente.

1. Escolha o **endereço de montagem de cache** na lista. Esse menu lista todos os [pontos de montagem de cliente](#find-mount-command-components)do cache.

   Equilibre a carga do cliente em todos os endereços de montagem disponíveis para melhorar o desempenho do cache.

   ![captura de tela do campo endereço de montagem de cache, com seletor mostrando três endereços IP para escolher](media/mount-select-ip.png)

1. Escolha o **caminho do namespace virtual** a ser usado para o cliente. Esses caminhos são vinculados a exportações no sistema de armazenamento de back-end.

   ![Captura de tela que mostra o campo "caminho do namespace virtual", com o seletor aberto.](media/mount-select-target.png)

   Você pode exibir e alterar os caminhos de namespace virtual na página do portal de **namespace** . Leia [Configurar o namespace agregado](add-namespace-paths.md) para ver como.

   Para saber mais sobre o recurso namespace agregado do cache HPC do Azure, leia [planejar o namespace agregado](hpc-cache-namespace.md).

1. O campo de **comando Mount** na etapa três é preenchido automaticamente com um comando de montagem personalizado que usa o endereço de montagem, o caminho do namespace virtual e o caminho do cliente que você definiu nos campos anteriores.

   Clique no símbolo de cópia no lado direito do campo para copiá-lo automaticamente para a área de transferência.

   ![captura de tela do campo de comando de montagem do protótipo, mostrando o texto em foco para o botão "copiar para a área de transferência"](media/mount-command-copy.png)

1. Use o comando de montagem copiado no computador cliente para conectá-lo ao cache do HPC do Azure. Você pode emitir o comando diretamente da linha de comando do cliente ou incluir o comando mount em um modelo ou script de configuração do cliente.

## <a name="understand-mount-command-syntax"></a>Entender a sintaxe do comando de montagem

O comando Mount tem o seguinte formato:

> sudo mount {*Opções*} *cache_mount_address*:/*namespace_path* *local_path*

Exemplo:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount -o hard,proto=tcp,mountproto=tcp,retry=30 10.0.0.28:/blob-demo-0722 hpccache
root@test-client:/tmp#
```

Depois que esse comando for executado com sucesso, o conteúdo da exportação de armazenamento ficará visível no ``hpccache`` diretório do cliente.

### <a name="mount-command-options"></a>Opções de comando de montagem

Para uma montagem de cliente robusta, passe essas configurações e argumentos no comando mount:

> montagem-o Hard, proto = TCP, mountproto = TCP, repetir = 30 $ {CACHE_IP_ADDRESS}:/$ {NAMESPACE_PATH} $ {LOCAL_FILESYSTEM_MOUNT_POINT}

| Configurações de comando de montagem recomendadas | Descrição |
--- | ---
``hard`` | As montagens suaves no cache do HPC do Azure estão associadas a falhas do aplicativo e à possível perda de dados.
``proto=tcp`` | Essa opção é compatível com o tratamento apropriado de erros de rede NFS.
``mountproto=tcp`` | Essa opção é compatível com o tratamento apropriado de erros de rede para operações de montagem.
``retry=<value>`` | Configure ``retry=30`` para evitar falhas transitórias de montagem. Recomenda-se um valor diferente em montagens em primeiro plano.

### <a name="find-mount-command-components"></a>Localizar componentes de comando de montagem

Se você quiser criar um comando de montagem sem usar a página **instruções de montagem** , poderá encontrar os endereços de montagem na página **visão geral** do cache e os caminhos do namespace virtual na página **namespace** .

![captura de tela da página de visão geral da instância do cache HPC do Azure, com uma caixa de realce em volta da lista de endereços de montagem no canto inferior direito](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> Os endereços de montagem de cache correspondem às interfaces de rede dentro da sub-rede do cache. Em um grupo de recursos, essas NICs são listadas com nomes que terminam em `-cluster-nic-` e um número. Não altere ou exclua essas interfaces, ou o cache ficará indisponível.

Os caminhos de namespace virtual são mostrados na página de configurações de **namespace** do cache.

![captura de tela da página de namespace > de configurações do portal com uma caixa de realce ao contrário da primeira coluna da tabela: "caminho do namespace"](media/view-namespace-paths.png)

## <a name="next-steps"></a>Próximas etapas

* Para mover dados para os destinos de armazenamento do cache, leia [popular novo armazenamento de BLOBs do Azure](hpc-cache-ingest.md).
