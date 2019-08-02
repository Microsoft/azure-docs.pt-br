---
title: Perguntas frequentes sobre os Serviços de Mídia do Azure v3 | Microsoft Docs
description: Este artigo fornece respostas às perguntas frequentes sobre os Serviços de Mídia do Azure v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 06/21/2019
ms.author: juliako
ms.openlocfilehash: 28b9c8f343437c20e277d2f3ba53767afa45a5c2
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501264"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Perguntas frequentes sobre os serviços de mídia v3

Este artigo fornece respostas às perguntas frequentes sobre os Serviços de Mídia do Azure (AMS) v3.

## <a name="general"></a>Geral

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Quais funções do Azure podem executar ações nos recursos dos serviços de mídia do Azure? 

Consulte [RBAC (controle de acesso baseado em função) para contas de serviços de mídia](rbac-overview.md).

### <a name="how-do-i-configure-media-reserved-units"></a>Como fazer para configurar Unidades Reservadas para Mídia?

Para os trabalhos de análise de áudio e análise de vídeo acionados pelo Serviços de Mídia do Microsoft Azure v3 ou pelo Video Indexer, é altamente recomendável provisionar sua conta com 10 MRUs do S3. Se você precisar de mais de 10 MRUs do S3, abra um ticket de suporte usando o [Portal do Microsoft Azure](https://portal.azure.com/).

Para obter detalhes, confira [Dimensionar o processamento de mídia com a CLI](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Qual é o método recomendado para processar vídeos?

Use [Transformações](https://docs.microsoft.com/rest/api/media/transforms) para configurar tarefas comuns para codificar ou analisar vídeos. Cada **Transformação** descreve uma receita ou um fluxo de trabalho de tarefas para processar os arquivos de áudio ou vídeos. Um [trabalho](https://docs.microsoft.com/rest/api/media/jobs) é a solicitação real aos serviços de mídia para aplicar a **transformação** a um determinado conteúdo de áudio ou vídeo de entrada. Quando a Transformação for criada, você poderá enviar trabalhos usando as APIs dos Serviços de Mídia ou um dos SDKs publicados. Para obter mais informações, consulte [Transformações e Trabalhos](transforms-jobs-concept.md).

### <a name="how-does-pagination-work"></a>Como a paginação funciona?

Ao usar a paginação, você sempre deve usar o próximo link para enumerar a coleção e não depender de um tamanho de página em particular. Para obter detalhes e exemplos, confira [Filtragem, ordenação, paginação](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Quais recursos ainda não estão disponíveis nos serviços de mídia do Azure v3?

Para obter detalhes, confira lacunas de [recursos em relação às APIs v2](migrate-from-v2-to-v3.md#feature-gaps-with-respect-to-v2-apis).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Qual é o processo de mover uma conta de serviços de mídia entre assinaturas?  

Para obter detalhes, consulte [movendo uma conta dos serviços de mídia entre assinaturas](media-services-account-concept.md).

## <a name="live-streaming"></a>Transmissão ao vivo 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Como inserir quebras/vídeos e imagens fixas durante a transmissão ao vivo?

A codificação ativa dos Serviços de Mídia v3 ainda não dá suporte à inserção de vídeo ou de imagens fixas durante a transmissão ao vivo. 

Você pode usar um [codificador ativo local](recommended-on-premises-live-encoders.md) para alternar para o vídeo de origem. Muitos aplicativos fornecem a capacidade de alternar fontes, incluindo o Telestream Wirecast, o Switcher Studio (no iOS), o OBS Studio (aplicativo gratuito) e muito mais.

## <a name="content-protection"></a>Proteção de conteúdo

### <a name="should-i-use-an-aes-128-clear-key-encryption-or-a-drm-system"></a>Devo usar uma criptografia de chave não criptografada AES-128 ou um sistema DRM?

Geralmente, os clientes se perguntam se devem utilizar criptografia AES ou um sistema DRM. A principal diferença entre os dois sistemas é que, com a criptografia AES, a chave de conteúdo é transmitida ao cliente via TLS para que a chave seja criptografada em trânsito, mas sem nenhuma criptografia adicional ("em claro"). Como resultado, a chave usada para descriptografar o conteúdo é acessível ao Player do cliente e pode ser exibida em um rastreamento de rede no cliente em texto sem formatação. Uma criptografia de chave não criptografada AES-128 é adequada para casos de uso em que o visualizador é uma parte confiável (por exemplo, criptografar vídeos corporativos distribuídos em uma empresa para serem exibidos por funcionários).

Os sistemas DRM, como PlayReady, Widevine e FairPlay, fornecem um nível adicional de criptografia na chave usada para descriptografar o conteúdo em comparação com uma chave de limpeza AES-128. A chave de conteúdo é criptografada para uma chave protegida pelo tempo de execução do DRM em um adicional para qualquer criptografia de nível de transporte fornecida pelo TLS. Além disso, a descriptografia é identificada em um ambiente seguro no nível do sistema operacional, no qual é mais difícil para um usuário mal-intencionado atacar. O DRM é recomendado para casos de uso em que o visualizador pode não ser uma parte confiável e você exige o mais alto nível de segurança.

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>Como e onde obter o token do JWT antes de usá-lo para a licença de solicitação ou a chave?

1. Para produção, você precisa ter um STS (serviços de token de segurança) (serviço Web) que emite um token JWT após uma solicitação HTTPS. Para teste, você pode usar o código mostrado no método **GetTokenAsync** definido no [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).
2. Player será necessário fazer uma solicitação, depois que um usuário é autenticado para o STS para esse token e atribuí-lo como o valor do token. Você pode usar o [API do Player de mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/).

* Para obter um exemplo de execução de STS, com a chave simétrica e assimétrica, consulte [https://aka.ms/jwt](https://aka.ms/jwt). 
* Para obter um exemplo de um player com base no Player de mídia do Azure usando tal token JWT, consulte [https://aka.ms/amtest](https://aka.ms/amtest) (expanda o link de "player_settings" para ver a entrada de token).

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>Como você autorizar solicitações para transmitir vídeos com a criptografia AES?

A abordagem correta é aproveitar o STS (serviço de Token seguro):

No STS, dependendo do perfil do usuário, adicione declarações diferentes (como "Usuário Premium", "Usuário Básico", "Usuário de Avaliação Gratuita"). Com diferentes declarações JWT, o usuário pode ver é diferente do conteúdo. É claro que, para conteúdo diferente/ativo, o ContentKeyPolicyRestriction terá o RequiredClaims correspondente.

Use as APIs dos serviços de mídia do Azure para configurar a entrega de licença/chave e criptografar seus ativos (conforme mostrado neste [exemplo](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)).

Para obter mais informações, consulte:

- [Visão geral da proteção de conteúdo](content-protection-overview.md)
- [Criação de um sistema de proteção de conteúdo de DRM múltiplo com controle de acesso](design-multi-drm-system-with-access-control.md)

### <a name="http-or-https"></a>HTTP ou HTTPS?
O aplicativo de player ASP.NET MVC deve ser compatível com o seguinte:

* Autenticação de usuário por meio do Azure AD que está em HTTPS.
* Troca de JWT entre o cliente e o Azure AD, que está em HTTPS.
* Aquisição de licença de DRM pelo cliente, que deve estar em HTTPS se a entrega de licença é fornecida pelos Serviços de Mídia. O pacote de produtos PlayReady não faz com que HTTPS seja obrigatório para a entrega de licença. Se o servidor de licença do PlayReady está fora dos Serviços de Mídia, você pode usar HTTP ou HTTPS.

O aplicativo de player ASP.NET usa HTTPS como uma melhor prática, portanto o Player de Mídia é uma página em HTTPS. No entanto, o HTTP é preferencial para streaming, então é necessário considerar o problema do conteúdo misto.

* O navegador não permite conteúdo misto. Mas plug-ins como os do Silverlight e OSMF, para smooth e DASH, permitem. O conteúdo misto é uma preocupação de segurança devido à ameaça potencial de injeção de JavaScript mal-intencionado, o que pode fazer com que os dados do cliente fiquem em risco. Os navegadores bloqueiam essa funcionalidade por padrão. A única maneira de contornar isso é no lado do servidor (origem), por meio da permissão de todos os domínios (sejam HTTPS ou HTTP). Isso provavelmente também não será uma boa ideia.
* Evite conteúdo misto. O aplicativo de player e o Player de Mídia devem usar HTTP ou HTTPS. Durante a reprodução de conteúdo misto, a tecnologia silverlightSS exige a desmarcação de um aviso de conteúdo misto. A tecnologia flashSS lida com conteúdo misto sem um aviso de conteúdo misto.
* Se o ponto de extremidade de streaming tiver sido criado antes de agosto de 2014, ele não será compatível com HTTPS. Nesse caso, crie e use um novo ponto de extremidade de streaming para HTTPS.

### <a name="what-about-live-streaming"></a>E quanto à Transmissão ao Vivo?

Você pode usar exatamente o mesmo design e a mesma implementação para proteger a transmissão ao vivo nos Serviços de Mídia, tratando o ativo associado a um programa como um ativo de VoD. Para fornecer uma proteção de vários DRM do conteúdo ao vivo, aplique a mesma configuração/processamento ao ativo como se fosse um ativo VOD antes de associar o ativo à saída dinâmica.

### <a name="what-about-license-servers-outside-media-services"></a>E os servidores de licença fora dos Serviços de Mídia?

Muitas vezes os clientes investiram em um farm de servidores de licença em seu próprio data center ou hospedado por provedores de serviço de DRM. Com a Proteção de Conteúdo dos Serviços de Mídia, você pode operar em modo híbrido. O conteúdo pode ser hospedado e protegido dinamicamente nos Serviços de Mídia, enquanto as licenças de DRM são fornecidas pelos servidores fora dos Serviços de Mídia. Nesse caso, considere as seguintes alterações:

* O STS precisa emitir tokens que sejam aceitáveis e possam ser verificados pelo farm de servidores de licença. Por exemplo, os servidores de licença do Widevine fornecidos pela Axinom exigem um JWT específico que contém uma mensagem de autorização. Portanto, você precisa ter um STS para emitir esse JWT. 
* Você não precisa mais configurar o serviço de entrega de licença nos Serviços de Mídia. Você precisa fornecer URLs de aquisição de licença (para o PlayReady, o Widevine e o FairPlay) ao configurar o ContentKeyPolicies.

## <a name="media-services-v2-vs-v3"></a>Serviços de Mídia v2 comparado com v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Eu posso usar o portal do Azure para gerenciar recursos da v3?

Atualmente, você não pode usar o portal do Azure para gerenciar recursos da v3. Use a [API REST](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref) ou um dos [SDKs](media-services-apis-overview.md#sdks) com suporte.

### <a name="is-there-an-assetfile-concept-in-v3"></a>Existe um conceito de AssetFile no v3?

Os AssetFiles foram removidos da API do AMS para separar os Serviços de Mídia das dependências do SDK do Armazenamento. Agora o Armazenamento, não os Serviços de Mídia, mantém as informações que pertencem ao Armazenamento. 

Para obter mais informações, confira [Migrar para os Serviços de Mídia v3](migrate-from-v2-to-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Onde está a criptografia de armazenamento do lado do cliente?

Agora é recomendável usar a criptografia de armazenamento do lado do servidor (que está ativada por padrão). Para obter mais informações, consulte [Criptografia de serviço do Armazenamento do Azure para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="next-steps"></a>Próximas etapas

[Visão geral dos Serviços de Mídia v3](media-services-overview.md)
