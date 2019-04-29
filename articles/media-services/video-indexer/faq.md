---
title: Perguntas frequentes sobre o Video Indexer - Azure
titlesuffix: Azure Media Services
description: Obtenha respostas às perguntas frequentes sobre o Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: e85beea4250b5ac5f4defd0b918786079d3dbe3e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553684"
---
# <a name="frequently-asked-questions"></a>Perguntas frequentes

Este artigo responde perguntas frequentes sobre o Video Indexer.

## <a name="general-questions"></a>Perguntas gerais

### <a name="what-is-video-indexer"></a>O que é o Video Indexer?

O Video Indexer é um serviço de inteligência artificial que faz parte dos Serviços de Mídia do Microsoft Azure. O Video Indexer fornece uma orquestração de vários modelos de machine learning que permitem extrair facilmente um insight profundo de um vídeo. Para fornecer insights avançados e precisos, o Video Indexer utiliza vários canais de vídeo: áudio, fala e visual. Os insights do Video Indexer podem ser utilizados de diferentes formas, por exemplo, para melhorar a detectabilidade e a acessibilidade do conteúdo, criar novas oportunidades de monetização ou produzir novas experiências que usam os insights. O Video Indexer oferece uma interface baseada na Web para teste, configuração e personalização de modelos em sua conta. Os desenvolvedores podem usar uma API baseada em REST para integrar o Video Indexer ao sistema de produção. 

### <a name="what-can-i-do-with-video-indexer"></a>O que posso fazer com o Video Indexer?

Algumas das operações que podem ser executadas pelo Video Indexer em arquivos de mídia incluem:

* Identificar e extrair a fala e identificar os locutores.
* Identificar e extrair o texto na tela em um vídeo.
* Detectar objetos em um arquivo de vídeo.
* Identificar marcas (por exemplo: Microsoft) de faixas de áudio e de texto na tela em um vídeo.
* Detectar e reconhecer rostos de um banco de dados de celebridades e um banco de dados de rostos definido pelo usuário.
* Extrair os tópicos discutidos, mas não necessariamente mencionados no conteúdo de áudio e vídeo.
* Criar legendas ocultas ou subtítulos com base na faixa de áudio.

Para obter mais informações e mais recursos do Video Indexer, confira [Visão geral](video-indexer-overview.md).

### <a name="how-do-i-get-started-with-video-indexer"></a>Como fazer para começar a utilizar o Video Indexer?

O Video Indexer inclui uma oferta de avaliação gratuita que fornece 600 minutos na interface Web e 2.400 minutos por meio da API. Você pode [se conectar à interface Web do Video Indexer](https://www.videoindexer.ai/) e testá-lo por conta própria usando qualquer identidade da Web, sem precisar configurar uma assinatura do Azure. 

Para indexar vídeos e arquivos de áudio em escala, você pode conectar o Video Indexer a uma assinatura paga do Microsoft Azure. Você pode encontrar mais informações de preço na página de [preços](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/).

Você pode encontrar mais informações na [Guia de Introdução](video-indexer-get-started.md).

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>É necessário habilidades de codificação para usar o Video Indexer?

É possível usar a interface Web do Video Indexer para avaliar, configurar e gerenciar sua conta **sem a necessidade de codificação**.  Quando estiver pronto para desenvolver aplicativos mais complexos, use a [API do Video Indexer](https://api-portal.videoindexer.ai/) para integrá-lo em seus próprios aplicativos, sites da Web ou em [fluxos de trabalho personalizados usando as tecnologias sem servidor como os Aplicativos Lógicos do Azure](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/) ou as Azure Functions.

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>É necessário habilidades de aprendizado de máquina para usar o Video Indexer?

Não, o Video Indexer fornece a integração de vários modelos de machine learning em um pipeline. Indexar um arquivo de áudio ou vídeo por meio do Video Indexer recupera um conjunto completo de insights extraído em uma linha do tempo compartilhada, sem a necessidade de ter qualquer habilidade sobre aprendizado de máquina ou conhecimento sobre os algoritmos da parte do cliente.

### <a name="what-media-formats-does-video-indexer-support"></a>Para quais formatos de mídia o Video Indexer oferece suporte?

O Video Indexer dá suporte aos formatos de mídia mais comuns. Consulte a lista [Formatos padrão do Codificador de Mídia do Azure](https://docs.microsoft.com/azure/media-services/latest/media-encoder-standard-formats) para saber mais.

### <a name="how-to-do-i-upload-a-media-into-video-indexer"></a>Como posso carregar uma mídia no Video Indexer?

No portal da Web do Video Indexer, é possível carregar um arquivo de mídia usando a caixa de diálogo correspondente ou direcionando para uma URL que hospeda diretamente o arquivo de origem (consulte o [exemplo](https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4)). Qualquer URL que hospeda o conteúdo de mídia usando um iFrame de conteúdo ou código incorporado não funcionará (consulte o [exemplo](https://www.videoindexer.ai/accounts/7e1282e8-083c-46ab-8c20-84cae3dc289d/videos/5cfa29e152/?t=4.11)). A API do Video Indexer exige que você especifique o arquivo de entrada por meio de uma URL ou uma matriz de bytes. Os carregamentos por meio de uma URL usando a API estão limitados a 10 GB, mas não têm um limite de duração de tempo. Para obter mais informações, consulte este [guia de instruções](https://docs.microsoft.com/azure/media-services/video-indexer/upload-index-videos).

### <a name="how-long-does-it-take-video-indexer-to-extract-insights-from-media"></a>Quanto tempo leva para o Video Indexer extrair insights de uma mídia?

A quantidade de tempo que leva para indexar um arquivo de áudio ou vídeo usando a API do Video Indexer e a interface Web do Video Indexer depende de vários parâmetros, como o tamanho e a qualidade do arquivo, o número de insights encontrados no arquivo, o número de [unidades reservadas](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview) e, ainda, se o [ponto de extremidade de streaming](https://docs.microsoft.com/azure/media-services/previous/media-services-streaming-endpoints-overview) está habilitado ou não. É recomendável que você execute alguns arquivos de teste com seu próprio conteúdo e calcule uma média para ter uma ideia melhor.

### <a name="can-i-create-customized-workflows-to-automate-processes-with-video-indexer"></a>Posso criar fluxos de trabalho personalizados para automatizar processos com o Video Indexer?

Sim, é possível integrar o Video Indexer a tecnologias sem servidor, como os Aplicativos Lógicos, o Flow e as [Azure Functions](https://azure.microsoft.com/services/functions/). Saiba mais sobre os conectores do [Aplicativo Lógico](https://azure.microsoft.com/services/logic-apps/) e do [Flow](https://flow.microsoft.com/en-us/) para o Video Indexer [aqui](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/). 

### <a name="in-which-azure-regions-is-video-indexer-available"></a>Em quais regiões do Azure o Video Indexer está disponível?

Você pode ver quais regiões do Azure o Video Indexer está disponível na página [regiões](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

### <a name="what-is-the-sla-for-video-indexer"></a>O que é o SLA para o Video Indexer?

O SLA do Serviço de Mídia do Azure abrange o Video Indexer e pode ser encontrado na página do [SLA](https://azure.microsoft.com/support/legal/sla/media-services/v1_2/). O SLA aplica-se somente a contas pagas do Video Indexer e não se aplica à avaliação gratuita.

## <a name="privacy-questions"></a>Perguntas sobre privacidade

### <a name="are-video-and-audio-files-indexed-by-video-indexer-stored"></a>Os arquivos de áudio e vídeo indexados pelo Video Indexer são armazenados?

Sim, a menos que você exclua o arquivo do Video Indexer usando o site ou a API do Video Indexer, seus arquivos de áudio e vídeos são armazenados. Na avaliação gratuita, os arquivos de áudio e vídeo que você indexar serão armazenados na região do Azure do Leste dos EUA. Caso contrário, os arquivos de áudio e vídeo são armazenados na conta de armazenamento de sua assinatura do Azure.

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>Posso excluir meus arquivos que são armazenados no Portal do Video Indexer?

Sim, é sempre possível excluir os arquivos de áudio e vídeo, bem como quaisquer metadados e insights extraídos desses arquivos pelo Video Indexer. Quando você exclui um arquivo do Video Indexer, o arquivo e seus metadados e insights são permanentemente removidos do Video Indexer. No entanto, se você implementou sua própria solução de backup no armazenamento do Azure, o arquivo permanece no seu armazenamento do Azure.

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>Posso controlar o acesso do usuário à minha conta do Video Indexer?

Sim, somente os administradores de contas podem convidar e desconvidar pessoas para suas contas, bem como atribuir quem tem privilégios de edição e quem tem acesso somente para leitura.

### <a name="who-has-access-to-my-video-and-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Quem tem acesso aos meus arquivos de áudio e vídeo que foram indexados e/ou armazenados pelo Video Indexer e aos metadados e insights que foram extraídos?

O conteúdo de seus vídeos ou áudios cuja configuração de privacidade seja pública pode ser acessado por qualquer pessoa que tenha o link para esse conteúdo e seus insights. O conteúdo de seus vídeos e áudios cuja configuração de privacidade seja privada pode ser acessado apenas pelos usuários que foram convidados para a conta desse conteúdo. A configuração de privacidade do seu conteúdo também se aplica aos metadados e aos insights que o Video Indexer extrai. A configuração de privacidade é atribuída quando você carrega o arquivo de vídeo ou áudio. É possível também alterar a configuração de privacidade após a indexação.

### <a name="what-access-does-microsoft-have-to-my-video-or-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Que tipo de acesso a Microsoft tem aos meus arquivos de áudio e vídeo que foram indexados e/ou armazenados pelo Video Indexer e aos metadados e insights que foram extraídos?

De acordo com os [Termos de Serviços Online do Azure](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (OST), você é o proprietário de todo o seu conteúdo, e a Microsoft apenas acessa seu conteúdo e os metadados e insights que o Video Indexer extrai de seus arquivos de acordo com os OST e a Política de Privacidade da Microsoft.

### <a name="are-the-custom-models-that-i-build-in-my-video-indexer-account-available-to-other-accounts"></a>Os modelos personalizados que eu crio na minha conta do Video Indexer estão disponíveis para outras contas?

 Não, os modelos personalizados que você cria em sua conta não estão disponíveis para nenhuma outra conta. O Video Indexer atualmente permite que você crie modelos personalizados de [marcas](customize-brands-model-overview.md), [idioma](customize-language-model-overview.md) e [pessoa](customize-person-model-overview.md) em sua conta. Esses modelos só estão disponíveis na conta em que você criou os modelos.
  
### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>O conteúdo indexado pelo Video Indexer é mantido dentro da região do Azure em que estou usando o Video Indexer?

Sim, o conteúdo e seus insights são mantidos na região do Azure, a menos que você tenha uma configuração manual na sua assinatura do Azure que utiliza várias regiões do Azure. 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>Qual é a Política de Privacidade para o Video Indexer?

O Video Indexer é coberto pela [Política de Privacidade da Microsoft](https://privacy.microsoft.com/privacystatement). A política de privacidade explica os dados pessoais que a Microsoft processa, como a Microsoft os processa e para qual finalidade. Para saber mais sobre privacidade, consulte a [Central de Confiabilidade da Microsoft](https://www.microsoft.com/trustcenter).

### <a name="what-certifications-does-video-indexer-have"></a>Quais certificações o Video Indexer tem?

Atualmente, o Video Indexer tem a certificação SOC. Para revisar a certificação do Video Indexer, consulte a [Central de Confiabilidade da Microsoft](https://www.microsoft.com/trustcenter/compliance/complianceofferings?product=Azure).

## <a name="api-questions"></a>Perguntas de API

### <a name="what-apis-does-video-indexer-offer"></a>Quais APIs o Video Indexer oferece?

As APIs do Video Indexer permitem a indexação, a extração de metadados, o gerenciamento de ativos, a tradução, a inserção, a personalização de modelos e muito mais. Para saber mais sobre como usar a API do Video Indexer, acesse o [Portal do Desenvolvedor do Video Indexer](https://api-portal.videoindexer.ai/).

### <a name="what-client-sdks-does-video-indexer-offer"></a>Quais SDKs de cliente o Video Indexer oferece?

Atualmente, não é oferecido nenhum SDK de cliente. A equipe do Video Indexer está trabalhando nos SDKs e planeja fornecê-los em breve.

### <a name="how-do-i-get-started-with-video-indexers-api"></a>Como fazer para começar a utilizar a API do Video Indexer?

Siga o [Tutorial: introdução à API do Video Indexer](video-indexer-use-apis.md).

### <a name="what-is-the-difference-between-the-video-indexer-api-and-the-azure-media-service-v3-api"></a>Qual é a diferença entre a API do Video Indexer e a API v3 do Serviço de Mídia do Azure?

Atualmente, há algumas sobreposições nos recursos oferecidos pela API do Video Indexer e a API v3 do Serviço de Mídia do Azure. É possível saber mais sobre como comparar os dois serviços [aqui](compare-video-indexer-with-media-services-presets.md).

### <a name="what-is-an-api-access-token-and-why-do-i-need-it"></a>O que é um token de acesso de API e por que eu preciso dele?

A API do Video Indexer contém uma API de autorização e uma API de operações. A API de autorizações contém as chamadas que lhe dão o token de acesso. Cada chamada para a API de operações deve estar associada a um token de acesso, correspondendo ao escopo de autorização da chamada.

Os tokens de acesso são necessários para usar as APIs do Video Indexer para fins de segurança. Isso garante que todas as chamadas são provenientes de você ou daqueles que têm permissões de acesso à sua conta. 

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>Qual é a diferença entre o token de acesso de conta, o token de acesso de usuário e o token de acesso de vídeo?

* Nível da conta - os tokens de acesso no nível da conta permitem que você execute operações no nível da conta ou no nível do vídeo. Por exemplo, carregar o vídeo, listar todos os vídeos, obter insights de vídeo.
* Nível de usuário - os tokens de acesso no nível do usuário permitem que você execute operações no nível do usuário. Por exemplo, obter contas associadas.
* Nível de vídeo - os tokens de acesso ao nível de vídeo permitem que você realize operações em um vídeo específico. Por exemplo, obtenha informações sobre vídeos, faça o download de legendas, obtenha widgets etc.

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>Com que frequência preciso obter um novo token de acesso? Quando os tokens de acesso expiram?

Os tokens de acesso expiram a cada hora, portanto, você precisa gerar um novo token de acesso a cada hora. 

## <a name="billing-questions"></a>Perguntas sobre cobrança

### <a name="how-much-does-video-indexer-cost"></a>Quanto custa o Video Indexer?

O Video Indexer usa um modelo de pagamento conforme o uso, com base na duração da entrada de conteúdo que você indexar. Encargos adicionais podem ser aplicáveis para codificação, streaming, armazenamento, uso da rede e unidades reservadas de mídia. Para saber mais, consulte a página de [preços](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/).

### <a name="when-am-i-billed-for-using-video-indexer"></a>Quando sou cobrado para usar o indexador de vídeo?

Ao enviar um vídeo para ser indexado, o usuário definirá a indexação como análise de vídeo, análise de áudio ou ambos. Isso determinará quais SKUs serão cobradas. Se houver um erro de nível crítico durante o processamento, um código de erro retornará como resposta. Neste caso não haverá cobrança.  Um erro crítico pode ocorrer devido a um bug em nosso código ou uma falha crítica em uma dependência interna do serviço. Erros como identificação incorreta ou extração de insight não são considerados críticos e haverá retorno de resposta. Haverá cobrança no caso de uma resposta válida (código sem erro) retornar.
 
### <a name="does-video-indexer-offer-a-free-trial"></a>O Video Indexer oferece uma avaliação gratuita?

Sim, o Video Indexer oferece uma avaliação gratuita que fornece o serviço e a funcionalidade de API completos. Há uma cota de 600 minutos de vídeos para usuários da interface Web e de 2.400 minutos para usuários da API. 

## <a name="next-steps"></a>Próximas etapas

[Visão geral](video-indexer-overview.md)
