---
title: Carregar arquivos em uma conta dos Serviços de Mídia do Azure do Azure StorSimple | Microsoft Docs
description: Este artigo fornece uma breve visão geral do Gerenciador de Dados do Azure StorSimple. O artigo também fornece links para tutoriais que mostram como extrair dados do StorSimple e carregá-los como ativos em uma conta dos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 1dd09328-262b-43ef-8099-73241b49a925
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: d1d43f11c1a90456b24f02a5ec43982d5fdc3de7
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98694513"
---
# <a name="upload-files-into-an-azure-media-services-account-from-azure-storsimple"></a>Carregar arquivos em uma conta dos Serviços de Mídia do Azure do Azure StorSimple 

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

> [!NOTE]
> Não estão sendo adicionados novos recursos ou funcionalidades aos Serviços de Mídia v2. <br/>Confira a versão mais recente, [Serviços de Mídia v3](../latest/index.yml). Além disso, consulte s [diretrizes de migração da v2 para a v3](../latest/migrate-v-2-v-3-migration-introduction.md)
>
> 
> O Gerenciador de Dados do Azure StorSimple está em visualização privada. 
> 

## <a name="overview"></a>Visão geral

Nos serviços de mídia, você pode carregar seus arquivos digitais em um ativo. O ativo pode conter vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e arquivos de legenda codificada (e os metadados sobre esses arquivos). Depois que os arquivos são carregados, seu conteúdo é armazenado com segurança na nuvem para processamento e streaming adicionais.

O [Azure StorSimple](../../storsimple/index.yml) usa o armazenamento em nuvem como uma extensão da solução local e dispõe os dados em camadas automaticamente no armazenamento local e no armazenamento em nuvem. O dispositivo StorSimple deduplica e compacta os dados antes de enviá-los para a nuvem, tornando-o muito eficiente para o envio de arquivos grandes para a nuvem. O serviço [Gerenciador de Dados do StorSimple](../../storsimple/storsimple-data-manager-overview.md) fornece APIs que permitem extrair dados do StorSimple e apresentá-los como ativos do AMS.

## <a name="get-started"></a>Introdução

1. [Crie uma conta dos Serviços de Mídia](media-services-portal-create-account.md) para a qual você deseja transferir os ativos.
2. Inscreva-se para a visualização do Gerenciador de Dados, conforme descrito no artigo [Gerenciador de Dados do StorSimple](../../storsimple/storsimple-data-manager-overview.md).
3. Crie uma conta do Gerenciador de Dados do StorSimple.
4. Crie um trabalho de transformação de dados que, quando executado, extrai dados de um dispositivo StorSimple e os transfere para uma conta do AMS como ativos. 

    Quando um trabalho começa a ser executado, uma fila de armazenamento é criada. Essa fila é populada com mensagens sobre blobs transformados à medida que elas estejam prontas. O nome dessa fila é igual ao nome da definição do trabalho. Use essa fila para determinar quando como ativo está pronto e para chamar sua operação desejada dos Serviços de Mídia para execução. Por exemplo, use esta fila para disparar uma função do Azure que contém o código necessário dos Serviços de Mídia.

## <a name="see-also"></a>Consulte também

[Usar o SDK do .NET para disparar trabalhos no Gerenciador de Dados](../../storsimple/storsimple-data-manager-dotnet-jobs.md)

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Próximas etapas

Agora você pode codificar seus ativos carregados. Para saber mais, veja [Codificar ativos](media-services-portal-encode.md).
