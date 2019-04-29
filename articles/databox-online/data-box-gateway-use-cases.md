---
title: Casos de uso do Gateway de caixa de dados do Microsoft Azure | Microsoft Docs
description: Descreve os casos de uso para o Gateway de caixa de dados do Azure, uma solução de armazenamento de solução de virtualização que permite transferir dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.topic: article
ms.date: 03/2/2019
ms.author: alkohli
ms.openlocfilehash: 37ec1d05d07f33343b9ff21380a277d00b242b7c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754111"
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

Use o Data Box Gateway quando desejar manter seus dados por um longo período na nuvem. Use a camada **Arquivo Morto** do armazenamento para a retenção de longo prazo.

A camada de Arquivo Morto é otimizada para armazenar dados raramente acessados por pelo menos 180 dias. A camada de **Arquivo Morto** oferece os custos de armazenamento mais baixos, mas tem os custos de acesso mais altos. Para saber mais, acesse [Camada de acesso ao Arquivo Morto](/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier).

### <a name="move-data-to-archive-tier"></a>Mover dados para a camada de Arquivo Morto

Antes de começar, certifique-se de que o Data Box Gateway esteja em execução. Siga as etapas detalhadas no [Tutorial: Prepare-se para implantar o Azure Data Box Gateway](data-box-gateway-deploy-prep.md) e continue avançando para o próximo tutorial até que você tenha um dispositivo operacional.

- Use o Data Box Gateway para carregar os dados no Azure por meio do procedimento de transferência usual, conforme descrito no artigo [Transferir dados com o Data Box Gateway](data-box-gateway-deploy-add-shares.md).
- Depois que os dados forem carregados, será necessário movê-los para a camada de Arquivo Morto. É possível definir a camada de blob de duas maneiras: Script do Azure PowerShell e ou uma política de Gerenciamento de ciclo de vida de armazenamento do Azure.  
    - Se usar o Azure PowerShell, siga estas [etapas](/azure/databox/data-box-how-to-set-data-tier#use-azure-powershell-to-set-the-blob-tier) para mover os dados para a camada de Arquivo Morto.
    - Se usar o Gerenciamento de Ciclo de Vida de Armazenamento do Azure, siga estas etapas para mover os dados para a camada de Arquivo Morto.
        - [Registre-se](/azure/storage/common/storage-lifecycle-management-concepts#register-for-preview) na versão prévia do serviço de Gerenciamento do Ciclo de Vida de Blobs para usar a camada de Arquivo Morto.
        - Use a seguinte política para [Arquivar dados em ingestão](/azure/storage/blobs/storage-lifecycle-management-concepts#archive-data-at-ingest).
- Depois que os blobs são marcados como Arquivo Morto, eles não podem mais ser modificados pelo gateway, a menos que sejam movidos para a camada frequente ou para a camada esporádica. Se o arquivo estiver no armazenamento local, nenhuma alteração feita na cópia local (incluindo exclusões) será carregada na camada de Arquivo Morto.
- Para ler os dados no Armazenamento de Arquivo Morto, eles devem ser reidratados alterando a camada de blob para frequente ou esporádica. [Atualizar o compartilhamento](data-box-gateway-manage-shares.md#refresh-shares) no gateway não reidrata o blob.

Para obter mais informações, saiba mais sobre como [gerenciar o ciclo de vida do armazenamento de blobs do Azure](/azure/storage/common/storage-lifecycle-management-concepts).

## <a name="initial-bulk-transfer-followed-by-incremental-transfer"></a>Transferência inicial em massa seguida por transferência incremental

Use o Data Box e o Data Box Gateway juntos quando quiser fazer um carregamento em massa de uma grande quantidade de dados seguida por transferências incrementais. Use o Data Box para a transferência em massa em modo offline (propagação inicial) e o Data Box Gateway para as transferências incrementais (alimentação constante) pela rede.

### <a name="seed-the-data-with-data-box"></a>Propagar os dados com o Data Box

Siga estas etapas para copiar os dados para o Data Box e carregá-los no Armazenamento do Azure.

1. [Solicite o Data Box](/azure/databox/data-box-deploy-ordered).
2. [Configure o Data Box](/azure/databox/data-box-deploy-set-up).
3. [Copie os dados para o Data Box por SMB](/azure/databox/data-box-deploy-copy-data).
4. [Devolva o Data Box, verifique os dados carregados no Azure](/azure/databox/data-box-deploy-picked-up).
5. Depois que o carregamento de dados no Azure for concluído, todos os dados devem estar nos contêineres de armazenamento do Azure. Na conta de armazenamento do Data Box, vá para o contêiner de Blob (e Arquivo) para certificar-se de que todos os dados foram copiados. Anote o nome do contêiner, pois você irá usá-lo posteriormente. Por exemplo, na seguinte captura de tela, o contêiner `databox` será usado para a transferência incremental.

    ![Contêiner com os dados no Data Box](media/data-box-gateway-use-cases/data-container1.png)

Essa transferência em massa conclui a fase de semeadura inicial.

### <a name="ongoing-feed-with-data-box-gateway"></a>Alimentação em andamento com o Data Box Gateway

Siga estas etapas para a ingestão em andamento pelo Data Box Gateway.

1. Crie um compartilhamento de nuvem no Data Box Gateway. Esse compartilhamento carrega automaticamente todos os dados para a conta de Armazenamento do Azure. Vá para **Compartilhamentos** em seu recurso do Data Box Gateway e clique em **+ Adicionar compartilhamento**.

    ![Clique em +Adicionar compartilhamento](media/data-box-gateway-use-cases/add-share1.png)

2. Verifique se esse compartilhamento é direcionado ao contêiner que contém os dados propagados. Em **Selecionar contêiner de blob**, escolha **Usar existente** e navegue até o contêiner para onde os dados do Data Box foram transferidos.

    ![Configurações de compartilhamento](media/data-box-gateway-use-cases/share-settings-select-existing-container1.png)

3. Depois que o compartilhamento for criado, atualize o compartilhamento. Esta operação atualiza o compartilhamento local com o conteúdo do Azure.

    ![Atualizar compartilhamento](media/data-box-gateway-use-cases/refresh-share1.png)

    Quando o compartilhamento estiver sincronizado, o Data Box Gateway carregará as alterações incrementais se os arquivos foram modificados no cliente.

## <a name="next-steps"></a>Próximas etapas

- Examine os [requisitos do sistema do Data Box Gateway](data-box-gateway-system-requirements.md).
- Entenda os [limites do Data Box Gateway](data-box-gateway-limits.md).
- Implante o [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) no portal do Azure.