---
title: Guia de controle de versão do blob Gerenciador de Armazenamento do Azure | Microsoft Docs
description: Diretrizes de controle de versão do blob para Gerenciador de Armazenamento do Azure
services: storage
author: chuye
ms.service: storage
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: chuye
ms.openlocfilehash: e20733cb4b93fcfac7606895746645727f12d6c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92783820"
---
# <a name="azure-storage-explorer-blob-versioning-guide"></a>Guia de controle de versão do blob Gerenciador de Armazenamento do Azure

Gerenciador de Armazenamento do Microsoft Azure fornece acesso fácil e gerenciamento de versões de BLOB. Este guia ajudará você a entender como o controle de versão do blob funciona no Gerenciador de Armazenamento. Antes de continuar, é recomendável que você leia mais sobre o [controle de versão de blob](../blobs/versioning-overview.md).

## <a name="terminology"></a>Terminologia

Esta seção fornece algumas definições para ajudar a entender seu uso neste artigo.

- Exclusão reversível: um recurso de proteção de dados automática alternativo. Você pode aprender mais sobre a exclusão reversível [aqui](../blobs/soft-delete-blob-overview.md).
- Blob ativo: uma versão de BLOB ou BLOB é criada no estado ativo. Você só pode operar em BLOBs ou em versões de blob no estado ativo.
- Blob excluído de forma reversível: uma versão de BLOB ou BLOB marcada como excluída de forma reversível. Os BLOBs com exclusão reversível são mantidos apenas por seu período de retenção.
- Versão do blob: um blob criado com o controle de versão do blob habilitado. Cada versão de blob é associada a uma ID de versão.
- Versão atual: uma versão de blob marcada como a versão atual.
- Versão anterior: uma versão de BLOB que não é a versão atual.
- Blob de não versão: um blob criado com o controle de versão de blob desabilitado. Um blob de não versão não tem uma ID de versão.

## <a name="view-blob-versions"></a>Exibir versões de BLOB

Gerenciador de Armazenamento dá suporte a quatro exibições diferentes para exibir BLOBs.

| Visualizar | Blobs de não versão ativos | Blobs de não versão excluídos por software | Versões de BLOB |
| ---- | :----------: | :-----------: | :------------------: |
| BLOBs ativos | Sim | Não | Somente a versão atual |
| BLOBs ativos e BLOBs com exclusão reversível | Sim | Sim | Somente a versão atual |
| BLOBs ativos e blobs sem a versão atual | Sim | Não | Versão atual ou versão mais recente ativa |
| Todos os BLOBs e blobs sem a versão atual | Sim | Sim | Versão atual ou versão mais recente |

### <a name="active-blobs"></a>BLOBs ativos

Nessa exibição, Gerenciador de Armazenamento exibe:

- Blobs de não versão ativos
- Versões atuais

### <a name="active-blobs-and-soft-deleted-blobs"></a>BLOBs ativos e BLOBs com exclusão reversível

Nessa exibição, Gerenciador de Armazenamento exibe:

- Blobs de não versão ativos
- Blobs de não versão excluídos por software
- Versões atuais.

### <a name="active-blobs-and-blobs-without-current-version"></a>BLOBs ativos e blobs sem a versão atual

Nessa exibição, Gerenciador de Armazenamento exibe:

- Blobs de não versão ativos
- Versões atuais
- Versões anteriores ativas mais recentes. 

Para BLOBs que não têm uma versão atual, mas têm uma versão anterior ativa, Gerenciador de Armazenamento exibe sua versão anterior mais recente como uma representação desse blob.

### <a name="all-blobs-and-blobs-without-current-version"></a>Todos os BLOBs e blobs sem a versão atual

Nessa exibição, Gerenciador de Armazenamento exibe:

- Blobs de não versão ativos
- Blobs de não versão excluídos por software
- Versões atuais
- Versões anteriores mais recentes. 

Para BLOBs que não têm uma versão atual, Gerenciador de Armazenamento exibe a versão anterior mais recente como uma representação desse blob.

> [!Note]
> Devido à limitação de serviço, Gerenciador de Armazenamento precisa de algum processamento adicional para obter uma exibição hierárquica de seus diretórios virtuais ao listar versões de BLOB. Levará mais tempo para listar os BLOBs nas seguintes exibições:
> 
> - BLOBs ativos e blobs sem a versão atual
> - Todos os BLOBs e blobs sem a versão atual

## <a name="manage-blob-versions"></a>Gerenciar versões de BLOB

### <a name="view-versions-of-a-blob"></a>Exibir versões de um blob

Gerenciador de Armazenamento fornece um comando **gerenciar versões** para exibir todas as versões de um blob. Para exibir as versões de um blob, selecione o blob para o qual você deseja exibir as versões e selecione **gerenciar histórico &rarr; gerenciar versões** na barra de ferramentas ou no menu de contexto.

### <a name="download-blob-versions"></a>Baixar versões de BLOB

Para baixar uma ou mais versões de BLOB, selecione as versões de BLOB que você deseja baixar e selecione **baixar** na barra de ferramentas ou no menu de contexto.

Se você estiver baixando várias versões de um blob, os arquivos baixados terão suas IDs de versão no início dos nomes de arquivo.

### <a name="delete-blob-versions"></a>Excluir versões de BLOB

Para excluir uma ou mais versões de BLOB, selecione as versões de BLOB que você deseja excluir e selecione **excluir** na barra de ferramentas ou no menu de contexto.

As versões de blob estão sujeitas à sua política de exclusão reversível. Se a exclusão reversível estiver habilitada, as versões de blob serão excluídas de forma reversível. Um caso especial é a exclusão de uma versão atual. A exclusão de uma versão atual fará com que ela se torne automaticamente uma versão anterior ativa.

### <a name="promote-blob-version"></a>Promover versão do blob

Você pode restaurar o conteúdo de um blob promovendo uma versão anterior para se tornar a versão atual. Selecione a versão do blob que você deseja promover e selecione **promover versão** na barra de ferramentas ou no menu de contexto.

Os blobs de não versão serão substituídos pela versão do blob promovido. Verifique se você não precisa mais desses dados ou faça backup dos dados por conta própria antes de confirmar a operação. As versões atuais se tornam versões anteriores automaticamente, portanto Gerenciador de Armazenamento não solicitará a confirmação.

### <a name="undelete-blob-version"></a>Restaurar versão do blob

As versões de BLOB não podem ser reexcluídas individualmente. Eles devem ser reexcluídos de uma só vez. Para restaurar todas as versões de blob de um blob, selecione qualquer uma das versões do blob e selecione Cancelar **exclusão selecionado** na barra de ferramentas ou no menu de contexto.

### <a name="change-access-tier-of-blob-versions"></a>Alterar a camada de acesso de versões de BLOB

Cada versão de blob tem sua própria camada de acesso. Para alterar a camada de acesso de versões de BLOB, selecione as versões de BLOB que você deseja alterar a camada de acesso e selecione **alterar camada de acesso...** no menu de contexto.

## <a name="see-also"></a>Consulte Também

* [Controle de versão de BLOB](../blobs/versioning-overview.md)
* [Exclusão reversível para blobs](../blobs/soft-delete-blob-overview.md)