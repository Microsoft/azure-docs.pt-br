---
title: Microsoft Azure Gateway do Data Box casos de uso | Microsoft Docs
description: Descreve os casos de uso do Gateway do Azure Data Box, uma solução de armazenamento de dispositivo virtual que permite transferir dados para o Azure,
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: 3bf137f968082e677f45c20947793232b9181220
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98786605"
---
# <a name="use-cases-for-azure-data-box-gateway"></a>Casos de uso do Azure Data Box Gateway

O Azure Data Box Gateway é um dispositivo de gateway de armazenamento na nuvem que reside localmente e envia a imagem, a mídia e outros dados para o Azure. Esse gateway de armazenamento na nuvem é uma máquina virtual provisionada em seu hipervisor. Os dados são gravados nesse dispositivo virtual usando os protocolos NFS e SMB e, em seguida, esse dispositivo os envia para o Azure. Este artigo fornece uma descrição detalhada dos cenários em que você pode implantar esse dispositivo.

Use o Data Box Gateway nos seguintes cenários:

- Para ingerir continuamente grandes quantidades de dados.
- Para o arquivamento de dados na nuvem de maneira segura e eficiente.
- Para a transferência de dados incrementais pela rede depois que a transferência inicial em massa estiver concluída usando o Data Box.

Cada um desses cenários é descrito detalhadamente nas seções subsequentes.


## <a name="continuous-data-ingestion"></a>Ingestão de dados contínua

Uma das principais vantagens do Data Box Gateway é a capacidade de ingerir dados continuamente em um dispositivo para copiá-los na nuvem, independentemente do tamanho dos dados.

Conforme os dados são gravados no dispositivo de gateway, o dispositivo carrega os dados no Armazenamento do Azure. O dispositivo gerencia automaticamente o armazenamento, removendo os arquivos localmente ao mesmo tempo em que mantém os metadados quando ele atinge um certo limite. O armazenamento de uma cópia local dos metadados permite que o dispositivo de gateway carregue as alterações somente quando o arquivo for atualizado. Os dados carregados no dispositivo de gateway devem estar de acordo com as diretrizes descritas em [Limitações de upload de dados](data-box-gateway-limits.md#data-upload-caveats).

Conforme o dispositivo é preenchido com os dados, ele começa a limitar da taxa de entrada (conforme necessário) para coincidir com a taxa com qual os dados são carregados para a nuvem. Para monitorar a ingestão contínua no dispositivo, são utilizados alertas. Esses alertas são gerados quando a limitação começa e são removidos quando a limitação para.

## <a name="cloud-archival-of-data"></a>Arquivamento de dados na nuvem

Use o Data Box Gateway quando desejar manter seus dados por um longo período na nuvem. Use a camada Arquivo Morto do armazenamento para a retenção de longo prazo.

A camada de arquivo morto é otimizada para armazenar dados raramente acessados por pelo menos 180 dias. A camada de Arquivo Morto oferece os custos de armazenamento mais baixos, mas tem os custos de acesso mais altos. Para saber mais, acesse [Camada de acesso ao Arquivo Morto](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

### <a name="move-data-to-the-archive-tier"></a>Mover dados para a camada de arquivo morto

Antes de começar, certifique-se de que o Data Box Gateway esteja em execução. Siga as etapas detalhadas no [tutorial: preparar para implantar gateway do Azure data Box](data-box-gateway-deploy-prep.md) e continue avançando para o próximo tutorial até que você tenha um dispositivo operacional.

- Use o Data Box Gateway para carregar os dados no Azure por meio do procedimento de transferência usual, conforme descrito no artigo [Transferir dados com o Data Box Gateway](data-box-gateway-deploy-add-shares.md).
- Depois que os dados forem carregados, será necessário movê-los para a camada de Arquivo Morto. Você pode definir a camada de blob de duas maneiras: usando um script Azure PowerShell ou uma política de gerenciamento do ciclo de vida do armazenamento do Azure.  
    - Se estiver usando Azure PowerShell, siga estas [etapas](../databox/data-box-how-to-set-data-tier.md#use-azure-powershell-to-set-the-blob-tier) para mover os dados para a camada de arquivo morto.
    - Se estiver usando o gerenciamento do ciclo de vida do Azure, siga estas etapas para mover os dados para a camada de arquivo morto.
        - [Registre-se](../storage/blobs/storage-lifecycle-management-concepts.md) para a versão prévia do serviço de gerenciamento do ciclo de vida de BLOB para usar a camada de arquivo.
        - Use a seguinte política para [Arquivar dados em ingestão](../storage/blobs/storage-lifecycle-management-concepts.md#archive-data-after-ingest).
- Depois que os BLOBs são marcados como arquivados, eles não podem mais ser modificados pelo gateway, a menos que sejam movidos para a camada quente ou frio. Se o arquivo estiver no armazenamento local, as alterações feitas na cópia local (incluindo exclusões) não serão carregadas na camada de arquivo morto.
- Para ler dados no armazenamento de arquivo morto, você deve reidratar os dados alterando a camada de BLOB para quente ou esporádico. [Atualizar o compartilhamento](data-box-gateway-manage-shares.md#refresh-shares) no gateway não reidrata o blob.

Para obter mais informações, saiba mais sobre como [gerenciar o ciclo de vida do armazenamento de blobs do Azure](../storage/blobs/storage-lifecycle-management-concepts.md).

## <a name="initial-bulk-transfer-followed-by-incremental-transfer"></a>Transferência inicial em massa seguida por transferência incremental

Use o Data Box e o Data Box Gateway juntos quando quiser fazer um carregamento em massa de uma grande quantidade de dados seguida por transferências incrementais. Use o Data Box para a transferência em massa em modo offline (propagação inicial) e o Data Box Gateway para as transferências incrementais (alimentação constante) pela rede.

### <a name="seed-the-data-with-data-box"></a>Propagar os dados com o Data Box

Siga estas etapas para copiar os dados para o Data Box e carregá-los no Armazenamento do Azure.

1. [Solicite o Data Box](../databox/data-box-deploy-ordered.md).
2. [Configure o Data Box](../databox/data-box-deploy-set-up.md).
3. [Copie os dados para o Data Box por SMB](../databox/data-box-deploy-copy-data.md).
4. [Devolva o Data Box, verifique os dados carregados no Azure](../databox/data-box-deploy-picked-up.md).
5. Depois que o carregamento de dados no Azure for concluído, todos os dados devem estar nos contêineres de armazenamento do Azure. Na conta de armazenamento do Data Box, vá para o contêiner de Blob (e Arquivo) para certificar-se de que todos os dados foram copiados. Anote o nome do contêiner, pois você irá usá-lo posteriormente. Por exemplo, na seguinte captura de tela, o contêiner `databox` será usado para a transferência incremental.

    ![Contêiner com os dados no Data Box](media/data-box-gateway-use-cases/data-container.png)

Essa transferência em massa conclui a fase de semeadura inicial.

### <a name="ongoing-feed-with-data-box-gateway"></a>Alimentação em andamento com o Data Box Gateway

Siga estas etapas para a ingestão em andamento pelo Data Box Gateway. 

1. Crie um compartilhamento de nuvem no Data Box Gateway. Esse compartilhamento carrega automaticamente todos os dados para a conta de Armazenamento do Azure. Vá para **Compartilhamentos** em seu recurso do Data Box Gateway e clique em **+ Adicionar compartilhamento**.

    ![Clique em +Adicionar compartilhamento](media/data-box-gateway-use-cases/add-share.png)

2. Verifique se esse compartilhamento é direcionado ao contêiner que contém os dados propagados. Em **Selecionar contêiner de blob**, escolha **Usar existente** e navegue até o contêiner para onde os dados do Data Box foram transferidos.

    ![Configurações de compartilhamento](media/data-box-gateway-use-cases/share-settings-select-existing-container.png)

3. Depois que o compartilhamento for criado, atualize o compartilhamento. Esta operação atualiza o compartilhamento local com o conteúdo do Azure.

    ![Atualizar compartilhamento](media/data-box-gateway-use-cases/refresh-share.png)

    Quando o compartilhamento estiver sincronizado, o Data Box Gateway carregará as alterações incrementais se os arquivos foram modificados no cliente.

## <a name="next-steps"></a>Próximas etapas

- Examine os [requisitos do sistema do Data Box Gateway](data-box-gateway-system-requirements.md).
- Entenda os [limites do Data Box Gateway](data-box-gateway-limits.md).
- Implante o [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) no portal do Azure.