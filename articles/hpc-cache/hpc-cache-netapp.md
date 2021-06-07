---
title: Usar o cache do HPC do Azure e Azure NetApp Files
description: Como usar o cache HPC do Azure para melhorar o acesso aos dados armazenados com o Azure NetApp Files
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: e955ddc14bb2b0a7abc0dc815c6955247568876b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86497005"
---
# <a name="use-azure-hpc-cache-with-azure-netapp-files"></a>Usar o cache HPC do Azure com Azure NetApp Files

Você pode usar [Azure NetApp files](https://azure.microsoft.com/services/netapp/) como um destino de armazenamento para o cache do Azure HPC. Este artigo explica como os dois serviços podem trabalhar juntos e fornece dicas para configurá-los.

Azure NetApp Files combina o sistema operacional ONTAP com a escalabilidade e a velocidade de Microsoft Azure. Essa combinação permite que os usuários mudem fluxos de trabalho estabelecidos para a nuvem sem reescrever código.

Adicionar um componente de cache do HPC do Azure pode melhorar o acesso ao arquivo apresentando vários Azure NetApp Files volumes em um namespace agregado. Ele pode fornecer o cache de borda para volumes localizados em uma região de serviço diferente. Ele também pode melhorar o desempenho sob demanda para volumes que foram criados em níveis de serviço de nível inferior para economizar custos.

## <a name="overview"></a>Visão geral

Para usar um sistema de Azure NetApp Files como armazenamento de back-end com o cache do HPC do Azure, siga este processo.

1. Crie o sistema Azure NetApp Files e os volumes de acordo com as diretrizes em [planejar o sistema, abaixo](#plan-your-azure-netapp-files-system).
1. Crie o cache do HPC do Azure na região onde você precisa de acesso ao arquivo. (Use as instruções em [criar um cache do HPC do Azure](hpc-cache-create.md).)
1. [Defina os destinos de armazenamento](#create-storage-targets-in-the-cache) no cache que apontam para os volumes de Azure NetApp files. Crie um destino de armazenamento de cache para cada endereço IP exclusivo usado para acessar os volumes.
1. Faça com que os clientes [montem o cache HPC do Azure](#mount-storage-targets) em vez de montar Azure NetApp files volumes diretamente.

## <a name="plan-your-azure-netapp-files-system"></a>Planeje seu sistema de Azure NetApp Files

Ao planejar seu sistema de Azure NetApp Files, preste atenção aos itens desta seção para garantir que você possa integrá-lo suavemente com o cache do HPC do Azure.

Leia também a [documentação do Azure NetApp files](../azure-netapp-files/index.yml) antes de criar volumes para uso com o cache do HPC do Azure.

### <a name="nfs-client-access-only"></a>Somente acesso de cliente NFS

O cache HPC do Azure atualmente dá suporte apenas ao acesso de NFS. Ele não pode ser usado com volumes de bits de modo POSIX ou ACL SMB.

### <a name="exclusive-subnet-for-azure-netapp-files"></a>Sub-rede exclusiva para Azure NetApp Files

Azure NetApp Files usa uma única sub-rede delegada para seus volumes. Nenhum outro recurso pode usar essa sub-rede. Além disso, apenas uma sub-rede em uma rede virtual pode ser usada para Azure NetApp Files. Saiba mais em [diretrizes para Azure NetApp files planejamento de rede](../azure-netapp-files/azure-netapp-files-network-topologies.md).

### <a name="delegated-subnet-size"></a>Tamanho de sub-rede delegado

Use o tamanho mínimo para a sub-rede delegada ao criar um sistema de Azure NetApp Files para uso com o cache do HPC do Azure.

O tamanho mínimo, que é especificado com a máscara de rede/28, fornece 16 endereços IP. Na prática, Azure NetApp Files usa apenas três desses endereços IP disponíveis para acesso ao volume. Isso significa que você só precisa criar três destinos de armazenamento em seu cache do Azure HPC para abranger todos os volumes.

Se a sub-rede delegada for muito grande, é possível que os volumes de Azure NetApp Files usem mais endereços IP do que uma única instância de cache do Azure HPC pode manipular. Um único cache pode ter no máximo 10 destinos de armazenamento.

O exemplo de início rápido na documentação Azure NetApp Files usa 10.7.0.0/16 para a sub-rede delegada, que fornece uma sub-rede muito grande.

### <a name="capacity-pool-service-level"></a>Nível de serviço do pool de capacidade

Ao escolher o nível de serviço para seu pool de capacidade, considere o fluxo de trabalho. Se você gravar dados com frequência no volume Azure NetApp Files, o desempenho do cache poderá ser restrito se o tempo de write-back for lento. Escolha um alto nível de serviço para volumes que terão gravações frequentes.

Volumes com níveis de serviço baixo também podem mostrar algum atraso no início de uma tarefa enquanto o cache preenche previamente o conteúdo. Depois que o cache estiver em execução com um bom conjunto de arquivos de trabalho, o atraso deverá se tornar não perceptível.

É importante planejar o nível de serviço do pool de capacidade Antes do tempo, pois ele não pode ser alterado após a criação. Um novo volume precisa ser criado em um pool de capacidade diferente e os dados copiados.

Observe que você pode alterar a cota de armazenamento de um volume e o tamanho do pool de capacidade sem interromper o acesso.

## <a name="create-storage-targets-in-the-cache"></a>Criar destinos de armazenamento no cache

Depois que o sistema de Azure NetApp Files estiver configurado e o cache do HPC do Azure for criado, defina os destinos de armazenamento no cache que apontam para os volumes do sistema de arquivos.

Crie um destino de armazenamento para cada endereço IP usado por seus volumes de Azure NetApp Files. O endereço IP é listado na página de instruções de montagem do volume.

Se vários volumes compartilharem o mesmo endereço IP, você poderá usar um destino de armazenamento para todos eles.  

Siga as [instruções de montagem na documentação do Azure NetApp files](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) para localizar os endereços IP a serem usados.

Você também pode encontrar endereços IP com o CLI do Azure:

```azurecli
az netappfiles volume list -g ${RESOURCE_GROUP} --account-name ${ANF_ACCOUNT} --pool-name ${POOL} --query "[].mountTargets[].ipAddress" | grep -Ee '[0-9]+[.][0-9]+[.][0-9]+[.][0-9]+' | tr -d '"' | tr -d , | sort | uniq
```

Os nomes de exportação no sistema Azure NetApp Files têm um único componente de caminho. Não tente criar um destino de armazenamento para a exportação de raiz ``/`` no Azure NetApp files, pois essa exportação não fornece acesso ao arquivo.

Não há restrições especiais em caminhos de namespace virtual para esses destinos de armazenamento.

## <a name="mount-storage-targets"></a>Montar destinos de armazenamento

Os computadores cliente devem montar o cache em vez de montar os volumes de Azure NetApp Files diretamente. Siga as instruções em [montar o cache do HPC do Azure](hpc-cache-mount.md).

## <a name="next-steps"></a>Próximas etapas

* Leia mais sobre como configurar e usar [Azure NetApp files](../azure-netapp-files/index.yml)
* Para obter ajuda sobre como planejar e configurar seu sistema de cache HPC do Azure para usar Azure NetApp Files, [contate o suporte](hpc-cache-support-ticket.md).
