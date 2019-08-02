---
title: Migrar dos Serviços de Mídia do Azure v2 para v3 | Microsoft Docs
description: Este artigo descreve as alterações introduzidas nos Serviços de Mídia do Azure v3 e mostra as diferenças entre as duas versões. O artigo também fornece um guia para a migração dos Serviços de Mídia v2 para v3.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: serviços de mídia do azure, transmissão, difusão, ao vivo, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: b85b06552dcd0fc419302882f05814adbd454f46
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542557"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Orientação de migração para passar dos Serviços de Mídia v2 para v3

Este artigo descreve as alterações introduzidas no Azure Media Services v3, mostra as diferenças entre as duas versões e fornece as diretrizes de migração.

E se você tiver um serviço de vídeo desenvolvido hoje em dia sobre as [APIs herdadas do Media Services v2](../previous/media-services-overview.md), analise as seguintes diretrizes e considerações antes de migrar para as APIs v3. Há muitos benefícios e novos recursos na API v3 que melhoram a experiência do desenvolvedor e os recursos dos Serviços de Mídia. No entanto, conforme explicado na seção [Problemas conhecidos](#known-issues) deste artigo, também há algumas limitações devido a alterações entre as versões da API. Esta página será mantida à medida que a equipe de Serviços de Mídia fizer melhorias contínuas nas APIs da v3 e abordar as lacunas entre as versões. 

> [!NOTE]
> Atualmente, você não pode usar o portal do Azure para gerenciar recursos da v3. Use a [API REST](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref) ou um dos [SDKs](media-services-apis-overview.md#sdks) com suporte.

## <a name="benefits-of-media-services-v3"></a>Benefícios do Serviços de Mídia do Microsoft Azure v3
  
### <a name="api-is-more-approachable"></a>A API é mais acessível

*  A v3 é baseada em uma superfície de API unificada que expõe a funcionalidade de operações e gerenciamento compilada no Azure Resource Manager. Os modelos do Azure Resource Manager podem ser usados para criar e implantar Transformações, Pontos de Extremidade de Streaming, Eventos ao Vivo, entre outros.
* [Especificação de OpenAPI (anteriormente chamado de Swagger)](https://aka.ms/ams-v3-rest-sdk) documento.
    Expõe o esquema para todos os componentes de serviço, incluindo a codificação baseada em arquivo.
* SDKs disponíveis para [.NET](https://aka.ms/ams-v3-dotnet-ref), .NET Core, [Node.js](https://aka.ms/ams-v3-nodejs-ref), [Python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [Go](https://aka.ms/ams-v3-go-ref), e Ruby.
* [CLI do Azure](https://aka.ms/ams-v3-cli-ref) integração com o suporte a scripts simple.

### <a name="new-features"></a>Novos recursos

* Para processamento de trabalho baseado em arquivo, você pode usar um URL HTTP (S) como entrada.<br/>Você não precisa ter conteúdo já armazenado no Azure, nem precisa criar Ativos.
* Introduz o conceito de [Transformações](transforms-jobs-concept.md) para processamento de trabalho baseado em arquivo. Uma transformação pode ser usada para criar configurações reutilizáveis, criar modelos do Azure Resource Manager e isolar configurações de processamento entre vários clientes ou locatários.
* Um Ativo pode ter vários [Localizadores de Streaming](streaming-locators-concept.md), cada um com diferentes configurações de [Empacotamento Dinâmico](dynamic-packaging-overview.md) e Criptografia Dinâmica.
* [A proteção de conteúdo](content-key-policy-concept.md) suporta recursos de várias chaves.
* Você pode transmitir Eventos ao Vivo com duração de até 24 horas ao usar os Serviços de Mídia para transcodificação de um feed de contribuição de taxa de bits única em um fluxo de saída que tem várias taxas de bits.
* Novo suporte de transmissão ao vivo de baixa latência em Eventos ao Vivo. Para obter mais informações, consulte [latência](live-event-latency.md).
* A Versão Prévia do Evento ao Vivo oferece suporte ao [Empacotamento Dinâmico](dynamic-packaging-overview.md) e à Criptografia Dinâmica. Isso permite proteção de conteúdo na Versão Prévia, bem como empacotamento HLS e DASH.
* A Saída Dinâmica é mais simples de usar do que a entidade Program nas APIs v2. 
* Suporte aprimorado do RTMP (maior estabilidade e mais suporte de codificador de código-fonte).
* Ingestão segura de RTMPS.<br/>Ao criar um Evento ao Vivo, você obtém 4 URLs de ingestão. As 4 URLs de ingestão são quase idênticas, têm o mesmo token de streaming (AppId) e apenas a parte do número da porta é diferente. Duas das URLs são primárias e de backup para RTMPS.   
* Você tem controle de acesso baseado em função (RBAC) sobre suas entidades. 

## <a name="changes-from-v2"></a>Alterações da v2

* Para Ativos criados com a v3, o Media Services suporta apenas a criptografia de [armazenamento do lado do servidor do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).
    * É possível usar APIs v3 com ativos criados com APIs v2 que tinham [criptografia de armazenamento](../previous/media-services-rest-storage-encryption.md) (AES 256) fornecida pelo Serviços de Mídia do Microsoft Azure.
    * Você não pode criar novos Ativos com a criptografia de [armazenamento AES 256 legada](../previous/media-services-rest-storage-encryption.md) usando APIs v3.
* Propriedades do ativo na v3 diferem do v2, consulte [como mapeiam as propriedades](assets-concept.md#map-v3-asset-properties-to-v2).
* Os v3 SDKs agora são desacoplados do Storage SDK, o que lhe dá mais controle sobre a versão do Storage SDK que você deseja usar e evita problemas de versão. 
* Nas APIs da v3, todas as taxas de bits de codificação estão em bits por segundo. Isso é diferente das predefinições do v2 Media Encoder Standard. Por exemplo, o bitrate em v2 seria especificado como 128 (kbps), mas em v3 seria 128000 (bits / segundo). 
* Entidades AssetFiles, AccessPolicies e IngestManifests não existem na v3.
* A propriedade IAsset.ParentAssets não existe na v3.
* ContentKeys não é mais uma entidade; agora é uma propriedade do Localizador de Streaming.
* O suporte para Grade de Eventos substitui o NotificationEndpoints.
* As seguintes entidades foram renomeadas
    * A Saída do Trabalho substitui a Tarefa e, agora faz parte de um Trabalho.
    * O Localizador de Streaming substitui o Localizador.
    * O Evento ao Vivo substitui o Canal.<br/>A cobrança dos Eventos ao Vivo baseia-se em medidores do Canal ao Vivo. Para obter mais informações, confira [cobrança](live-event-states-billing.md) e [preços](https://azure.microsoft.com/pricing/details/media-services/).
    * A Saída Dinâmica substitui o Programa.
* As Saídas ao Vivo começam na criação e terminam quando são excluídas. Os programas funcionaram de maneira diferente nas APIs v2 e tiveram que ser iniciados após a criação.
*  Para obter informações sobre um trabalho, você precisa saber o nome da transformação sob a qual o trabalho foi criado. 

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Falhas de recurso em relação a APIs v2

A API de v3 tem as seguintes falhas de recurso em relação a API v2. Fechar as lacunas está em andamento.

* O [Codificador Premium](../previous/media-services-premium-workflow-encoder-formats.md) e os legados [processadores analíticos de mídia](../previous/media-services-analytics-overview.md) (Visualização do Azure Media Indexer 2, API de Detecção Facial, etc.) não podem ser acessados pela v3.<br/>Os clientes que desejam migrar da visualização do Indexador de Mídia 1 ou 2 podem usar imediatamente a predefinição AudioAnalyzer na API v3.  Essa nova predefinição contém mais recursos que o antigo Indexador de Mídia 1 ou 2. 
* Muitas da [recursos avançados do Media Encoder Standard no v2](../previous/media-services-advanced-encoding-with-mes.md) APIs atualmente não estão disponíveis na v3, tais como:
  
    * A união de ativos
    * Sobreposições
    * Corte
    * Sprites em miniatura
    * Inserir uma faixa de áudio silenciosa quando a entrada não tiver áudio
    * Inserir uma faixa de vídeo de entrada quando não tiver vídeo
* Atualmente, os Eventos ao Vivo com transcodificação não dão suporte à inserção em curso de imagem fixa e à inserção de marcador de anúncio por meio da chamada à API. 

> [!NOTE]
> Por favor, marque este artigo e continue verificando atualizações.
 
## <a name="code-differences"></a>Diferenças de código

A tabela a seguir mostra as diferenças de código entre v2 e v3 para cenários comuns.

|Cenário|API V2|API V3|
|---|---|---|
|Criar um ativo e enviar um arquivo |[exemplo de .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[exemplo de .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Enviar um trabalho|[exemplo de .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[exemplo de .NET V3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Mostra como criar primeiro uma transformação e, em seguida, enviar uma tarefa.|
|Publicar um ativo com criptografia AES |1. Criar ContentKeyAuthorizationPolicyOption<br/>2. Criar ContentKeyAuthorizationPolicy<br/>3. Criar AssetDeliveryPolicy<br/>4. Criar Ativo e carregar conteúdo OU Enviar trabalho e usar ativo de saída<br/>5. Associar AssetDeliveryPolicy com Ativo<br/>6. Criar ContentKey<br/>7. Anexar ContentKey ao Ativo<br/>8. Criar AccessPolicy<br/>9. Criar Localizador<br/><br/>[exemplo de .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Criar Política de Chave de Conteúdo<br/>2. Criar Ativo<br/>3. Carregar conteúdo ou usar Ativo como JobOutput<br/>4. Criar um Localizador de Streaming<br/><br/>[exemplo de .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Obter detalhes do trabalho e gerenciar trabalhos |[Gerenciar trabalhos com v2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Gerenciar trabalhos com v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

## <a name="known-issues"></a>Problemas conhecidos

* Atualmente, você não pode usar o portal do Azure para gerenciar recursos da v3. Use a [API REST](https://aka.ms/ams-v3-rest-sdk), CLI ou um dos SDKs suportados.
* Você precisa para provisionar unidades reservadas de mídia (MRUs) em sua conta para controlar a simultaneidade e o desempenho de seus trabalhos, especialmente os que envolvem a análise de áudio ou de vídeo. Para obter mais informações, consulte [Scaling Media Processing](../previous/media-services-scale-media-processing-overview.md) (Colocação em escala do processamento de mídia). Você pode gerenciar as MRUs usando a [CLI 2.0 para Serviços de Mídia v3](media-reserved-units-cli-how-to.md), o [portal do Azure](../previous/media-services-portal-scale-media-processing.md) ou as [APIs v2](../previous/media-services-dotnet-encoding-units.md). Você precisa provisionar MRUs, se você estiver usando os Serviços de Mídia do Microsoft Azure v2 ou v3 APIs.
* As entidades do Serviços de Mídia do Microsoft Azure criadas com a API v3 não podem ser gerenciadas pela API v2.  
* Não é recomendado gerenciar entidades que foram criadas com APIs v2 por meio das APIs da v3. A seguir, exemplos das diferenças que tornam as entidades em duas versões incompatíveis:   
    * Trabalhos e tarefas criados na v2 não aparecem na v3, pois não estão associados a uma transformação. A recomendação é mudar para v3 Transforms e Jobs. Haverá um período de tempo relativamente curto de necessidade de monitorar as tarefas V2 durante a transição.
    * Os Canais e os Programas criados com a v2 (que são mapeados para os Eventos ao Vivo e as Saídas Dinâmicas na v3) não podem continuar sendo gerenciados com a v3. A recomendação é alternar para os Eventos ao Vivo e as Saídas Dinâmicas da v3 em uma parada de Canal conveniente.<br/>Atualmente, você não pode migrar continuamente executando canais.  

> [!NOTE]
> Esta página será mantida à medida que a equipe de Serviços de Mídia fizer melhorias contínuas nas APIs da v3 e abordar as lacunas entre as versões.

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

Para ver como é fácil começar a codificar e transmitir arquivos de vídeo, confira [Arquivos de transmissão](stream-files-dotnet-quickstart.md). 

