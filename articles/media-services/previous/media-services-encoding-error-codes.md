---
title: Códigos de erro de codificação dos Serviços de Mídia do Azure | Microsoft Docs
description: Este tópico lista os códigos de erro que podem ser retornados caso um erro tenha sido encontrado durante a execução de tarefas de codificação.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ce4e939f-5aee-41f9-859d-e4429815e9f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 7e6848fb49dd63fa67a639d09754a28dd5953a32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013475"
---
# <a name="encoding-error-codes"></a>Códigos de erro de codificação

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

A tabela a seguir lista os códigos de erro que podem ser retornados caso um erro tenha sido encontrado durante a execução de tarefas de codificação.  Para obter detalhes do erro em seu código do .NET, use a classe [ErrorDetails](/previous-versions/azure/jj126075(v=azure.100)) . Para obter detalhes do erro em seu código REST, use a API REST [ErrorDetail](/rest/api/media/operations/errordetail) .

| ErrorDetail.Code | Causas possíveis para erro |
| --- | --- |
| Unknown |Erro desconhecido ao executar a tarefa |
| ErrorDownloadingInputAssetMalformedContent |Categoria de erros que abrange erros ao baixar um ativo de entrada, como nomes de arquivo inválidos, arquivo com comprimento zero, formatos incorretos e assim por diante. |
| ErrorDownloadingInputAssetServiceFailure |Categoria de erros que aborda problemas no lado do serviço - por exemplo, erros de rede ou armazenamento durante o download. |
| ErrorParsingConfiguration |Categoria de erros em que a tarefa \<see cref="MediaTask.PrivateData"/> (configuração) não é válida, por exemplo, a configuração não é uma predefinição de sistema válida ou contém XML inválido. |
| ErrorExecutingTaskMalformedContent |Categoria de erros durante a execução da tarefa em que os problemas de conteúdo nos arquivos de mídia de entrada causam falha. |
| ErrorExecutingTaskUnsupportedFormat |Categoria de erros em que o processador de mídia não pode processar os arquivos fornecidos - não há suporte para o formato de mídia ou ele não coincide com a Configuração. Por exemplo, tentando produzir uma saída somente de áudio com base em um ativo que tenha apenas vídeo |
| ErrorProcessingTask |Categoria de outros erros que o processador de mídia encontra durante o processamento da tarefa e que não estão relacionados ao conteúdo. |
| ErrorUploadingOutputAsset |Categoria de erros ao carregar o ativo de saída |
| ErrorCancelingTask |Categoria de erros para cobrir falhas durante a tentativa de cancelar a Tarefa |
| TransientError |Categoria de erros para abordar problemas transitórios (por exemplo, problemas temporários de rede com o Armazenamento do Azure) |

Para obter ajuda da equipe dos **Serviços de Mídia** , abra um [tíquete de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Artigos relacionados
* [Executar tarefas avançadas de codificação ao personalizar predefinições do Codificador de Mídia Padrão](media-services-custom-mes-presets-with-dotnet.md)
* [Cotas e limitações](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
