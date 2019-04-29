---
title: Exemplos da CLI do Azure – Serviços de Mídia do Azure | Microsoft Docs
description: Exemplos da CLI do Azure para os Serviços de Mídia do Azure
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 03/11/2019
ms.author: juliako
ms.openlocfilehash: dee7f831562dc1f4b2478d13b204aab1d8455e1e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733174"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Exemplos da CLI do Azure para os Serviços de Mídia do Azure

A tabela a seguir inclui links para exemplos da CLI do Azure para os Serviços de Mídia do Azure.

## <a name="examples"></a>Exemplos

|  |  |
|---|---|
|**Escala**||
| [Dimensionar Unidades Reservadas de Mídia](media-reserved-units-cli-how-to.md)|Para os trabalhos de análise de áudio e análise de vídeo acionados pelo Serviços de Mídia do Microsoft Azure v3 ou pelo Video Indexer, é altamente recomendável provisionar sua conta com 10 MRUs do S3. <br/>O script mostra como usar a CLI para dimensionar as Unidades Reservadas de Mídia (MRUs).|
|**Conta**||
| [Criar uma conta dos Serviços de Mídia](create-account-cli-how-to.md) | O script cria uma conta de serviços de mídia do Azure. |
| [Redefinir credenciais de conta](./scripts/cli-reset-account-credentials.md)|Redefine as credenciais de conta e obtém as configurações de app.config de volta.|
|**Ativos**||
| [Criar ativos](./scripts/cli-create-asset.md)|Cria um ativo de Serviços de Mídia para carregar o conteúdo.|
| [Carregar um arquivo](./scripts/cli-upload-file-asset.md)|Carrega um arquivo local para um contêiner de armazenamento.|
| **Transformações** e **Trabalhos**||
| [Criar transformações](./scripts/cli-create-transform.md)|Mostra como criar transformações. Transformações descreve um fluxo de trabalho simples de tarefas para processar seus arquivos de áudio ou vídeos (também conhecidos como “receita”).<br/> Você sempre deve verificar se uma transformação com um nome desejado e "receita" já existe. Em caso afirmativo, reutilize-a. |
| [Codificar com uma transformação personalizada](custom-preset-cli-howto.md) | Mostra como criar um personalizado predefinido para seus requisitos específicos de cenário ou dispositivo de destino.|
| [Criar trabalhos](./scripts/cli-create-jobs.md)|Enviar um trabalho para uma Transformação de codificação simples usando a URL HTTPs.|
| [Criar EventGrid](./scripts/cli-create-event-grid.md)|Cria uma assinatura de Grade de Eventos de nível de conta para Alterações de Estado do Trabalho.|
| **Entregar**||
| [Publicar um ativo](./scripts/cli-publish-asset.md)| Cria um localizador de Streaming e obtém as URLs de Streaming novamente. |
| [Filter](filters-dynamic-manifest-cli-howto.md)| Configura um filtro para um ativo de Vídeo sob Demanda e mostra como usar a CLI para criar [Filtros de Conta](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) e [Filtros de Ativo](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

## <a name="see-also"></a>Consulte também

- [CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
- [Início Rápido: Arquivos de streaming de vídeo - CLI](stream-files-cli-quickstart.md)
