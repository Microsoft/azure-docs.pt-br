---
title: Entender o namespace agregado do cache do HPC do Azure
description: Como planejar o namespace virtual para o cache do Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 1c28f549cf93d77f6aef6bcde6a2225345a79cc9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91612941"
---
# <a name="plan-the-aggregated-namespace"></a>Planejar o namespace agregado

O cache HPC do Azure permite que os clientes acessem uma variedade de sistemas de armazenamento por meio de um namespace virtual que oculta os detalhes do sistema de armazenamento de back-end.

Depois de adicionar um destino de armazenamento, você configura um ou mais caminhos de namespace voltados para o cliente para o destino de armazenamento. Os computadores cliente montam esse caminho de arquivo e podem fazer solicitações de leitura de arquivo para o cache em vez de montar o sistema de armazenamento diretamente.

Como o cache do HPC do Azure gerencia esse sistema de arquivos virtual, você pode alterar o destino de armazenamento sem alterar o caminho voltado para o cliente. Por exemplo, você pode substituir um sistema de armazenamento de hardware pelo armazenamento em nuvem sem a necessidade de reescrever procedimentos do lado do cliente.

## <a name="aggregated-namespace-example"></a>Exemplo de namespace agregado

Planeje o namespace agregado para que os computadores cliente possam acessar convenientemente as informações de que precisam e para que os administradores e engenheiros de fluxo de trabalho possam distinguir facilmente os caminhos.

Por exemplo, considere um sistema em que uma instância de cache do Azure HPC está sendo usada para processar dados armazenados no blob do Azure. A análise requer arquivos de modelo que são armazenados em um datacenter local.

Os dados do modelo são armazenados em um datacenter e as informações necessárias para esse trabalho são armazenadas nesses subdiretórios:

* */goldline/templates/acme2017/sku798*
* */goldline/templates/acme2017/sku980*

O sistema de armazenamento do datacenter expõe essas exportações:

* */*
* */goldline*
* */goldline/templates*

Os dados a serem analisados foram copiados para um contêiner de armazenamento de BLOBs do Azure denominado "SourceCollection" usando o [utilitário CLFSLoad](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload).

Para permitir acesso fácil por meio do cache, considere a criação de destinos de armazenamento com estes caminhos de namespace virtual:

| Sistema de armazenamento de back-end <br/> (Caminho do arquivo NFS ou contêiner de BLOB) | Caminho do namespace virtual |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| SourceCollection                        | /Source               |

Um destino de armazenamento NFS pode ter vários caminhos de namespace virtual, desde que cada um faça referência a um caminho de exportação exclusivo. (Leia [caminhos de namespace NFS](add-namespace-paths.md#nfs-namespace-paths) para saber o número máximo recomendado de caminhos de namespace por destino de armazenamento NFS.)

Como os caminhos de origem do NFS são subdiretórios da mesma exportação, você precisará definir vários caminhos de namespace do mesmo destino de armazenamento.

| Nome de host do destino de armazenamento  | Caminho de exportação do NFS     | Caminho do subdiretório | Caminho do namespace    |
|--------------------------|---------------------|-------------------|-------------------|
| *Endereço IP ou nome do host* | /goldline/templates | acme2017/sku798   | /templates/sku798 |
| *Endereço IP ou nome do host* | /goldline/templates | acme2017/sku980   | /templates/sku980 |

Um aplicativo cliente pode montar o cache e acessar facilmente os caminhos de arquivo de namespace agregados ``/source`` , ``/templates/sku798`` e ``/templates/sku980`` .

Uma abordagem alternativa pode ser criar um caminho virtual como `/templates` os links para o diretório pai, `acme2017` e fazer com que os clientes naveguem para o indivíduo `sku798` e os `sku980` diretórios depois de montar o cache. No entanto, você não pode criar um caminho de namespace que seja um subdiretório de outro caminho de namespace. Portanto, se você criar um caminho para o `acme2017` diretório, também não poderá criar caminhos de namespace para acessar diretamente seus subdiretórios.

A página Configurações de **namespace** do cache do Azure HPC mostra o sistema de arquivos voltado para o cliente e permite adicionar ou editar caminhos. Leia [Configurar o namespace agregado](add-namespace-paths.md) para obter detalhes.

## <a name="next-steps"></a>Próximas etapas

Depois de decidir como configurar seu sistema de arquivos virtual, siga estas etapas para criá-lo:

* [Criar destinos de armazenamento](hpc-cache-add-storage.md) para adicionar seus sistemas de armazenamento de back-end ao cache HPC do Azure
* [Adicionar caminhos de namespace](add-namespace-paths.md) para criar o namespace agregado que os computadores cliente usam para acessar arquivos
