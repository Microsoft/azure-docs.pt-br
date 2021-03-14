---
title: Diretrizes de migração de codificação
description: Este artigo fornece orientação baseada em cenário de codificação que ajudará você a migrar dos serviços de mídia do Azure v2 para v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: e093e22e2f1db3b432fdf328a077e56a8e56d92b
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102612483"
---
# <a name="encoding-scenario-based-migration-guidance"></a>Diretrizes de migração baseadas em cenário de codificação

![logotipo do guia de migração](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![etapas de migração 2](./media/migration-guide/steps-4.svg)

Este artigo fornece orientação baseada em cenário de codificação que ajudará você a migrar dos serviços de mídia do Azure v2 para v3.

## <a name="prerequisites"></a>Pré-requisitos

Antes de alterar o fluxo de trabalho de codificação, é recomendável que você compreenda as diferenças no modo como o armazenamento é gerenciado.  No AMS v3, a API de armazenamento do Azure é usada para gerenciar as contas de armazenamento associadas à sua conta de serviços de mídia.

> [!NOTE]
> Trabalhos e tarefas criados na v2 não aparecem na v3, pois não estão associados a uma transformação. A recomendação é alternar para transformações e trabalhos v3.

## <a name="encoding-workflow-comparison"></a>Comparação de fluxo de trabalho de codificação

Reserve alguns minutos para examinar os fluxogramas abaixo para uma comparação visual dos fluxos de trabalho de codificação para v2 e v3.

### <a name="v2-encoding-workflow"></a>Fluxo de trabalho de codificação v2

Clique na imagem abaixo para ver uma versão maior.

[![Fluxo de trabalho de codificação para v2 ](./media/migration-guide/V2-pretty.svg)](./media/migration-guide/V2-pretty.svg#lightbox)

1. Instalação
    1. Crie um ativo ou use um ativo existente. Se estiver usando um novo ativo, carregue o conteúdo para esse ativo. Se estiver usando um ativo existente, você deve estar codificando arquivos que já existem no ativo.
    2. Obter os valores dos seguintes itens:
        - ID ou objeto do processador de mídia
        - Cadeia de caracteres do codificador (nome) do codificador que você deseja usar
        - ID do ativo do novo ativo ou a ID do ativo do ativo existente
    3. Para o monitoramento, crie uma assinatura de notificação de nível de tarefa ou de trabalho ou um manipulador de eventos de SDK
2. Crie o trabalho que contém a tarefa ou as tarefas. Cada tarefa deve incluir os itens acima e:
    - Uma diretiva que um ativo de saída precisa ser criada.  O ativo de saída é criado pelo sistema.
    - Nome opcional para o ativo de saída
3. Enviar o trabalho.
4. Monitore o trabalho.

### <a name="v3-encoding-workflow"></a>Fluxo de trabalho de codificação v3

[![Fluxo de trabalho de codificação para v3](./media/migration-guide/V3-pretty.svg)](./media/migration-guide/V3-pretty.svg#lightbox)

1. Configuração
    1. Crie um ativo ou use um ativo existente. Se estiver usando um novo ativo, carregue o conteúdo para esse ativo. Se estiver usando um ativo existente, você deve estar codificando arquivos que já existem no ativo. Você não *deve carregar mais conteúdo para esse ativo.*
    1. Criar um ativo de saída.  O ativo de saída é onde os arquivos codificados e os metadados de entrada e saída serão armazenados.
    1. Obter valores para a transformação:
        - Predefinição de codificador padrão
        - Grupo de recursos do AMS
        - Nome da conta AMS
    1. Crie a transformação ou use uma transformação existente.  As transformações são reutilizáveis. Não é necessário criar uma nova transformação cada vez que você deseja enviar um trabalho.
1. Criar um trabalho
    1. Para o trabalho, obtenha os valores para os seguintes itens:
        - Nome da transformação
        - O URI de base para a URL de SAS para seu ativo, o caminho de origem HTTPs do seu compartilhamento de arquivos ou o caminho local dos arquivos. O `JobInputAsset` também pode usar um nome de ativo como uma entrada.
        - Nome (s) de arquivo
        - Ativo (s) de saída
        - Um grupo de recursos
        - Nome da conta AMS  
1. Use a [grade de eventos](monitor-events-portal-how-to.md) para monitorar seu trabalho.
1. Enviar o trabalho.

## <a name="custom-presets-from-v2-to-v3-encoding"></a>Predefinições personalizadas da codificação v2 para v3

Se o seu código v2 chamou o codificador padrão com uma predefinição personalizada, primeiro você precisará criar uma nova transformação com a predefinição de codificador padrão personalizado antes de enviar um trabalho.

As predefinições personalizadas agora são JSON e não são mais baseadas em XML. Recrie sua predefinição em JSON seguindo o esquema predefinido personalizado, conforme definido na documentação do [Swagger (Transform Open API)](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/examples/transforms-create.json) .

## <a name="input-and-output-metadata-files-from-an-encoding-job"></a>Arquivos de metadados de entrada e saída de um trabalho de codificação

Em v2, os arquivos de metadados de entrada e saída XML são gerados como resultado de um trabalho de codificação. Em v3, o formato de metadados mudou de XML para JSON. Para obter mais informações sobre metadados, consulte [metadados de entrada](input-metadata-schema.md) e metadados de [saída](output-metadata-schema.md).

## <a name="premium-encoder-to-v3-standard-encoder-or-partner-based-solutions"></a>Codificador Premium para o codificador Standard V3 ou soluções baseadas em parceiro

A API v2 não dá mais suporte ao codificador Premium. Se você usou anteriormente o codificador Premium baseado em fluxo de trabalho para a codificação HEVC deve migrar para o novo [codificador Standard](media-encoder-standard-formats.md) v3 com suporte à codificação HEVC.

Se você precisar dos recursos avançados de fluxo de trabalho do codificador Premium, você será incentivado a começar a usar uma solução de parceiro de codificação avançada do Azure da [imagine Communications](https://imaginecommunications.com), [Telestream](https://www.telestream.net)ou [Bitmovin](https://bitmovin.com).

## <a name="jobs-with-inputs-that-are-on-https-hosted-urls"></a>Trabalhos com entradas que estão em URLs hospedadas HTTPS

Agora você pode enviar trabalhos em v3 de arquivos armazenados no armazenamento do Azure, armazenados localmente ou em servidores Web externos usando o [suporte à entrada de trabalho http (S)](job-input-from-http-how-to.md).

Se você usou anteriormente os fluxos de trabalho para copiar arquivos de arquivos de blob do Azure para ativos vazios antes de enviar trabalhos, você poderá simplificar o fluxo de trabalho passando uma URL SAS para o arquivo no armazenamento de BLOBs do Azure diretamente para a tarefa.

## <a name="indexer-v1-audio-transcription-to-the-new-audioanalyzer-basic-mode"></a>Transcrição de áudio do indexador v1 para o novo AudioAnalyzer "modo básico"

Para clientes que usam o processador do indexador v1 na API v2, você precisa criar uma transformação que invoca o novo `AudioAnalyzer` no [modo básico](how-to-create-basic-audio-transform.md) antes de enviar um trabalho.

## <a name="encoding-transforms-and-jobs-concepts-tutorials-and-how-to-guides"></a>Conceitos de codificação, transformações e trabalhos, tutoriais e guias de instruções

### <a name="concepts"></a>Conceitos

- [Codificando vídeo e áudio com os serviços de mídia](encoding-concept.md)
- [Codecs e formatos de codificador padrão](media-encoder-standard-formats.md)
- [Codificar com uma escada de taxa de bits gerada automaticamente](autogen-bitrate-ladder.md)
- [Usar a predefinição de codificação com reconhecimento de conteúdo para localizar o valor de taxa de bits ideal para uma determinada resolução](content-aware-encoding.md)
- [Unidades reservadas de mídia](concept-media-reserved-units.md)
- [Metadados de entrada](input-metadata-schema.md)
- [Metadados de saída](output-metadata-schema.md)
- [Empacotamento dinâmico nos serviços de mídia V3: codecs de áudio](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging)

### <a name="tutorials"></a>Tutoriais

- [Tutorial: Codificar um arquivo remoto baseado em URL e transmitir o vídeo – .NET](stream-files-dotnet-quickstart.md)
- [Tutorial: Fazer upload, codificar e transmitir vídeos com os Serviços de Mídia v3](stream-files-tutorial-with-api.md)

### <a name="how-to-guides"></a>Guias de como fazer

- [Criar uma entrada de trabalho de uma URL HTTPS](job-input-from-http-how-to.md)
- [Criar uma entrada de trabalho de um arquivo local](job-input-from-local-file-how-to.md)
- [Criar uma transformação de áudio básica](how-to-create-basic-audio-transform.md)
- Com .NET
  - [Como codificar com uma transformação personalizada-.NET](customize-encoder-presets-how-to.md)
  - [Como criar uma sobreposição com Media Encoder Standard](how-to-create-overlay.md)
  - [Como gerar miniaturas usando o codificador padrão com .NET](media-services-generate-thumbnails-dotnet.md)
- Com a CLI do Azure
  - [Como codificar com um CLI do Azure de transformação personalizado](custom-preset-cli-howto.md)
- Com REST
  - [Como codificar com uma transformação personalizada-REST](custom-preset-rest-howto.md)
  - [Como gerar miniaturas usando o codificador padrão com REST](media-services-generate-thumbnails-rest.md)
- [Subclipe um vídeo ao codificar com os serviços de mídia-.NET](subclip-video-dotnet-howto.md)
- [Subclipe um vídeo ao codificar com os serviços de mídia-REST](subclip-video-rest-howto.md)

## <a name="samples"></a>Exemplos

Você também pode [comparar o código V2 e v3 nos exemplos de código](migrate-v-2-v-3-migration-samples.md).

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
