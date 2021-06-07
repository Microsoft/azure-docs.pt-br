---
title: Opções para transferência de dados para o Azure usando um dispositivo | Microsoft Docs
description: Saiba como escolher o dispositivo certo para a transferência de dados local para o Azure entre Data Box Edge, Sincronização de Arquivos do Azure e StorSimple 8000 Series.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 4fc92146d6f076287fe37f64734bb07e8250792b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98882205"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>Comparar opções de transferência de dados do StorSimple com a Sincronização de Arquivos do Azure e Data Box Edge 

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]
 
Este documento fornece uma visão geral das opções de transferência de dados locais para o Azure, comparando: Data Box Edge vs. Sincronização de Arquivos do Azure vs. StorSimple 8000 Series.

- **[Edge da caixa de dados](../databox-online/azure-stack-edge-overview.md)** - O Data Box Edge é um dispositivo de rede local que move dados para dentro e fora do Azure e tem computação Edge habilitada para IA para pré-processar o upload de dataduring. O Data Box Gateway é uma versão virtual do dispositivo com os mesmos recursos de transferência de dados.
- **[Sincronização de arquivos do Azure](../storage/files/storage-sync-files-deployment-guide.md)** - A Sincronização de Arquivos do Azure pode ser usado para centralizar os compartilhamentos de arquivos da organização em arquivos do Azure, mantendo a flexibilidade, o desempenho e a compatibilidade de um servidor de arquivos local. A Sincronização de arquivos do Azure transforma o Windows Server em um cache rápido do compartilhamento de arquivos do Azure. A disponibilidade geral da Sincronização de Arquivos do Azure foi anunciada anteriormente em 2018.
- **[StorSimple](./storsimple-overview.md)** - O StorSimple é um dispositivo híbrido que ajuda as empresas a consolidar sua infraestrutura de armazenamento para armazenamento primário, proteção de dados, arquivamento e recuperação de desastres em uma única solução, integrando-se perfeitamente ao armazenamento do Azure. O ciclo de vida do produto para o StorSimple pode ser encontrado [aqui](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).

## <a name="comparison-summary"></a>Resumo de comparação

|                           |StorSimple 8000   |Sincronização de Arquivos do Azure   |Data Box Edge           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|**Visão geral**     |Armazenamento híbrido em camadas e arquivamento|Armazenamento do servidor de arquivos gerais com a sincronização de vários sites e em camadas de nuvem.  |Solução de armazenamento para pré-processar os dados e enviá-lo pela rede para o Azure.        |
|**Cenários**    |Servidor de arquivos, arquivamento, destino de backup |Servidor de arquivos, arquivamento (multi-site)   |Transferência de dados, pré-processamento de dados, incluindo inferência de ML, IoT, arquivamento    |
|**Azure Data Box Edge** |Não disponível |Não disponível |Suporta a execução de contêineres usando o Azure IoT Edge    |
|**Fator forma**  |Dispositivo físico   |Agente instalado no Windows Server |Dispositivo físico   |
|**Hardware**     |Dispositivo físico fornecido pela Microsoft como parte do serviço | Fornecida pelo cliente |Dispositivo físico fornecido pela Microsoft como parte do serviço  |
|**Formato dos dados**  |Formato personalizado   |Arquivos         |Os BLOBs ou arquivos    |
|**Suporte de protocolo** |iSCSI          |SMB, NFS    | NFS ou SMB      |
|**Preços**      |[StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Sincronização de Arquivos do Azure](https://azure.microsoft.com/pricing/details/storage/files/)  |[Data Box Edge](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [Azure Data Box Edge](../databox-online/azure-stack-edge-overview.md) e [Azure Data Box Gateway](../databox-gateway/data-box-gateway-overview.md)
- Saiba mais sobre a [Sincronização de arquivos do Azure](../storage/files/storage-sync-files-deployment-guide.md)