---
title: Usar contêineres de serviços cognitivas do Azure local
titleSuffix: Azure Cognitive Services
description: Saiba como usar contêineres do Docker para usar serviços cognitivas locais.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.topic: article
ms.date: 12/16/2020
ms.author: aahi
keywords: local, Docker, contêiner, kubernetes
ms.openlocfilehash: f5caac0a0da3595075dddd5a273a6883a0cca468
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861810"
---
# <a name="azure-cognitive-services-containers"></a>Contêineres dos Serviços Cognitivos do Azure

Os serviços cognitivas do Azure fornecem vários [contêineres do Docker](https://www.docker.com/what-container) que permitem usar as mesmas APIs que estão disponíveis no Azure, no local. O uso desses contêineres oferece a flexibilidade de trazer serviços cognitivas mais perto de seus dados para fins de conformidade, segurança ou outras razões operacionais. O suporte ao contêiner está disponível atualmente para um subconjunto de serviços cognitivas do Azure.

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

O uso de contêineres é uma abordagem para distribuição de software em que um aplicativo ou serviço, incluindo as dependências e configurações, é empacotado como uma imagem de contêiner. Com pouca ou nenhuma modificação, uma imagem de contêiner pode ser implantada em um host de contêiner. Os contêineres são isolados uns dos outros e do sistema operacional subjacente, com um espaço menor do que uma máquina virtual. Os contêineres poderão ser instanciados a partir de imagens de contêiner para tarefas de curto prazo e removidos, quando não forem mais necessários.

## <a name="features-and-benefits"></a>Características e benefícios

- **Infraestrutura imutável**: habilite as equipes do DevOps para aproveitar um conjunto consistente e confiável de parâmetros de sistema conhecidos, além de ser capaz de se adaptar a alterações. Os contêineres fornecem a flexibilidade para dinamizar em um ecossistema previsível e evitar descompassos de configuração.
- **Controle sobre os dados**: escolha onde seus dados são processados por serviços cognitivas. Isso pode ser essencial se você não puder enviar dados para a nuvem, mas precisar de acesso a API de Serviços Cognitivos. Suporte para consistência em ambientes híbridos – entre dados, gerenciamento, identidade e segurança.
- **Controle sobre atualizações de modelo**: flexibilidade no controle de versão e atualização de modelos implantados em suas soluções.
- **Arquitetura portátil**: habilita a criação de uma arquitetura de aplicativo portátil que pode ser implantada no Azure, no local e na borda. Os contêineres podem ser implantados diretamente no [Serviço de Kubernetes do Azure](../aks/index.yml), nas [Instâncias de Contêiner do Azure](../container-instances/index.yml) ou em um cluster do [Kubernetes](https://kubernetes.io/) implantado no [Azure Stack](/azure-stack/operator). Para obter mais informações, consulte [Implantar Kubernetes no Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Alta taxa de transferência/baixa latência**: forneça aos clientes a capacidade de dimensionar para alta taxa de transferência e requisitos de baixa latência, permitindo que os serviços cognitivas sejam executados fisicamente em seus dados e lógica do aplicativo. Os contêineres não limitam as transações por segundo (TPS) e poderão ser escalados vertical e horizontalmente para lidar com a demanda se você fornecer os recursos de hardware necessários.
- **Escalabilidade**: com a popularidade cada vez maior de contêineres e software de orquestração de contêiner, como kubernetes; a escalabilidade está no Forefront de avanços tecnológicos. Criando uma base de cluster escalonável, o desenvolvimento de aplicativos atende à alta disponibilidade.

## <a name="containers-in-azure-cognitive-services"></a>Contêineres nos Serviços Cognitivos do Azure

Os contêineres de serviços cognitivas do Azure fornecem o seguinte conjunto de contêineres do Docker, cada um contendo um subconjunto de funcionalidades de serviços nos serviços cognitivas do Azure. Você pode encontrar instruções e locais de imagem nas tabelas abaixo. Uma lista de [imagens de contêiner](containers/container-image-tags.md) também está disponível.

### <a name="decision-containers"></a>Contêineres de decisão

| Serviço |  Contêiner | Descrição | Disponibilidade |
|--|--|--|--|
| [Detector de anomalias][ad-containers] | **Detector de anomalias** ([imagem](https://hub.docker.com/_/microsoft-azure-cognitive-services-decision-anomaly-detector))  | A API do Detector de Anomalias permite monitorar e detectar anormalidades em seus dados de série temporal com aprendizado de máquina. | Disponível para o público geral |

### <a name="language-containers"></a>Contêineres de linguagem

| Serviço |  Contêiner | Descrição | Disponibilidade |
|--|--|--|--|
| [LUIS][lu-containers] |  **LUIS** ([imagem](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409)) | Carrega um modelo de reconhecimento vocal treinado ou publicado, também conhecido como um aplicativo LUIS, para um contêiner do Docker e fornece acesso às previsões de consulta dos pontos de extremidade da API do contêiner. Você pode coletar logs de consulta do contêiner e carregá-los novamente no [portal do LUIS](https://www.luis.ai) para aumentar a precisão da previsão do aplicativo. | Disponível para o público geral |
| [Análise de Texto][ta-containers-keyphrase] | **Extração de Frases-Chave** ([imagem](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) | Extrai frases-chave para identificar os principais pontos. Por exemplo, para o texto de entrada "A comida estava deliciosa e a equipe era maravilhosa", a API retorna os principais pontos de discussão: "comida" e "equipe maravilhosa". | Versão Prévia |
| [Análise de Texto][ta-containers-language] |  **Detecção de idioma de texto** ([imagem](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) | Para até 120 idiomas, detecta em qual idioma o texto de entrada está escrito e informa um código de idioma único para cada documento enviado na solicitação. O código de idioma é emparelhado com uma pontuação que indica a intensidade da pontuação. | Versão Prévia |
| [Análise de Texto][ta-containers-sentiment] | **Análise de sentimento v3** ([imagem](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) | Analisa o texto bruto em busca de pistas sobre sentimentos positivos ou negativos. Esta versão da análise de sentimentos retorna rótulos de sentimentos (por exemplo, *positivos* ou *negativos*) para cada documento e frase dentro dele. |  Disponível para o público geral |
| [Análise de Texto][ta-containers-health] |  **Análise de Texto para integridade** | Extraia e rotule informações médicas de texto clínico não estruturado. | Visualização restrita. [Solicitar acesso][request-access]. |

### <a name="speech-containers"></a>Contêineres de fala

> [!NOTE]
> Para usar os contêineres de fala, você precisará concluir um [formulário de solicitação online](https://aka.ms/csgate).

| Serviço |  Contêiner | Descrição | Disponibilidade |
|--|--|--|
| [API do Serviço de Fala][sp-containers-stt] |  **Conversão de fala em texto** ([imagem](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | Transcreve fala em tempo real contínua em texto. | Disponível para o público geral |
| [API do Serviço de Fala][sp-containers-cstt] | **Fala personalizada para texto** ([imagem](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | Transcreve a fala contínua em tempo real em texto usando um modelo personalizado. | Disponível para o público geral |
| [API do Serviço de Fala][sp-containers-tts] | **Conversão de texto em fala** ([imagem](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-text-to-speech)) | Converte o texto em fala que soa natural. | Disponível para o público geral |
| [API do Serviço de Fala][sp-containers-ctts] | **Conversão de texto em fala** ([imagem](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-text-to-speech)) personalizada | Converte o texto em uma fala de som natural usando um modelo personalizado. | Visualização restrita |
| [API do Serviço de Fala][sp-containers-ntts] | **Conversão de texto em fala neural** ([imagem](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-neural-text-to-speech)) | Converte o texto em voz natural usando uma tecnologia de rede neural profunda, permitindo uma fala mais natural sintetizada. | Disponível para o público geral |
| [API do Serviço de Fala][sp-containers-lid] | **Detecção de idioma de fala** ([imagem](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-language-detection)) | Determina o idioma do áudio falado. | Visualização restrita |

### <a name="vision-containers"></a>Contêineres de visão

> [!WARNING]
> Em 11 de junho de 2020, a Microsoft comunicou que não venderá tecnologia de reconhecimento facial a departamentos de polícia nos Estados Unidos até que um sólido regulamento, com base nos direitos humanos, seja aprovado. Desse modo, os clientes não poderão usar os recursos de reconhecimento facial ou a funcionalidade incluída nos Serviços do Azure, como Detecção Facial ou Video Indexer, se forem um departamento de polícia nos Estados Unidos ou permitirem o uso de desses serviços para e pela polícia.

| Serviço |  Contêiner | Descrição | Disponibilidade |
|--|--|--|--|
| [Pesquisa Visual Computacional][cv-containers] | **Ler OCR** ([imagem](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-read)) | O contêiner de OCR de leitura permite extrair texto impresso e manuscrito de imagens e documentos com suporte para formatos de arquivo JPEG, PNG, BMP, PDF e TIFF. Para obter mais informações, consulte a [documentação da API de leitura](./computer-vision/concept-recognizing-text.md). | Visualização restrita. [Solicitar acesso][request-access]. |
| [Análise Espacial][spa-containers] | **Análise espacial** ([imagem](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-spatial-analysis)) | Analisa o vídeo de streaming em tempo real para entender as relações espaciais entre as pessoas, a movimentação e as interações com objetos em ambientes físicos. | Visualização restrita. [Solicitar acesso][request-access]. |
| [Detecção Facial][fa-containers] | **Detecção Facial** | Detecta rostos humanos em imagens e identifica atributos, incluindo referências faciais (como narizes e olhos), sexo, idade e outras características faciais previstas pelo computador. Além de detectar, a Detecção Facial pode verificar se duas faces na mesma imagem ou imagens diferentes são iguais usando uma pontuação de confiança, ou comparar faces em relação a um banco de dados para ver se uma face semelhante ou idêntica já existe. Além disso, também pode organizar rostos semelhantes em grupos, usando traços visuais compartilhados. | Indisponível |
| [Reconhecedor de formulário][fr-containers] | **Reconhecimento de Formulários** | A compreensão de formulário aplica a tecnologia de aprendizado de máquina para identificar e extrair pares de chave-valor e tabelas de formulários. | Indisponível | 


<!--
|[Personalizer](./personalizer/what-is-personalizer.md) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Além disso, há suporte para alguns contêineres na oferta de [recursos de vários serviços](cognitive-services-apis-create-account.md) de serviços cognitivas. Você pode criar um único recurso All-in-One de serviços cognitivas e usar a mesma chave de cobrança entre os serviços com suporte para os seguintes serviços:

* Visual Computacional
* Face
* LUIS
* Análise de Texto

## <a name="prerequisites"></a>Pré-requisitos

É necessário atender aos seguintes pré-requisitos antes de usar contêineres dos Serviços Cognitivos do Azure:

**Mecanismo do Docker**: é necessário ter o Mecanismo do Docker instalado localmente. O Docker fornece pacotes que configuram o ambiente do Docker em [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms) e [Windows](https://docs.docker.com/docker-for-windows/). No Windows, o Docker deve ser configurado para dar suporte a contêineres do Linux. Os contêineres do Docker também podem ser implantados diretamente no [Serviço de Kubernetes do Azure](../aks/index.yml) ou [Instâncias de Contêiner do Azure](../container-instances/index.yml).

O Docker deve ser configurado para permitir que os contêineres conectem-se e enviem dados de cobrança para o Azure.

**Familiaridade com Docker e Registro de Contêiner da Microsoft**: é necessário ter uma compreensão básica de ambos os conceitos de Docker e Registro de Contêiner da Microsoft, como registros, repositórios, contêineres e imagens de contêiner, bem como conhecimento dos comandos básicos `docker`.

Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).

Contêineres individuais podem ter seus próprios requisitos, incluindo requisitos de alocação de memória e servidor.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

## <a name="developer-samples"></a>Exemplos do desenvolvedor

Exemplos de desenvolvedor estão disponíveis em nosso [repositório do GitHub](https://github.com/Azure-Samples/cognitive-services-containers-samples).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as [receitas de contêiner](containers/container-reuse-recipe.md) que você pode usar com os serviços cognitivas.

Instale e explore a funcionalidade fornecida pelos contêineres nos Serviços Cognitivos do Azure:

* [Contêineres de detector de anomalias][ad-containers]
* [Contêineres de Pesquisa Visual Computacional][cv-containers]
* [Contêineres de face][fa-containers]
* [Contêineres do reconhecedor de formulário][fr-containers]
* [Contêineres de Reconhecimento vocal (LUIS)][lu-containers]
* [Contêineres da API do serviço de fala][sp-containers]
* [Contêineres de Análise de Texto][ta-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: face/face-how-to-install-containers.md
[fr-containers]: form-recognizer/form-recognizer-container-howto.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[spa-containers]: ./computer-vision/spatial-analysis-container.md
[sp-containers-lid]: speech-service/speech-container-howto.md?tabs=lid
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[sp-containers-ntts]: speech-service/speech-container-howto.md?tabs=ntts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
[ta-containers-keyphrase]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-containers-language]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-containers-sentiment]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
[ta-containers-health]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health
[request-access]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u