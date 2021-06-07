---
title: Usar contêineres locais dos Serviços Cognitivos do Azure
titleSuffix: Azure Cognitive Services
description: Saiba como usar contêineres do Docker em Serviços Cognitivos no local.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.topic: article
ms.date: 04/16/2021
ms.author: aahi
keywords: on-premises, Docker, container, Kubernetes
ms.openlocfilehash: c40e91d81df448021be74af768bc9d5952b263dd
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588217"
---
# <a name="azure-cognitive-services-containers"></a>Contêineres dos Serviços Cognitivos do Azure

Os Serviços Cognitivos do Azure fornecem vários [contêineres do Docker](https://www.docker.com/what-container) que permitem usar no local as mesmas APIs disponíveis no Azure. O uso desses contêineres oferece a flexibilidade de aproximar os Serviços Cognitivos de seus dados para fins de conformidade, segurança ou por outros motivos operacionais. O suporte para contêineres no momento está disponível para um subconjunto de Serviços Cognitivos do Azure.

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

O uso de contêineres é uma abordagem para distribuição de software em que um aplicativo ou serviço, incluindo as dependências e configurações, é empacotado como uma imagem de contêiner. Com pouca ou nenhuma modificação, uma imagem de contêiner pode ser implantada em um host de contêiner. Os contêineres são isolados uns dos outros e do sistema operacional subjacente, com um espaço menor do que uma máquina virtual. Os contêineres poderão ser instanciados a partir de imagens de contêiner para tarefas de curto prazo e removidos, quando não forem mais necessários.

## <a name="features-and-benefits"></a>Características e benefícios

- **Infraestrutura imutável**: permite que as equipes do DevOps aproveitem um conjunto consistente e confiável de parâmetros de sistema conhecidos e seja capaz de se adaptar a mudanças. Os contêineres fornecem flexibilidade para dinamizar em um ecossistema previsível e evitar descompassos de configuração.
- **Controle sobre os dados**: escolhe em que local seus dados serão processados pelos Serviços Cognitivos. Isso pode ser fundamental caso não seja possível enviar dados à nuvem, porém seja preciso acessar APIs de Serviços Cognitivos. Suporte para consistência em ambientes híbridos – entre dados, gerenciamento, identidade e segurança.
- **Controle sobre atualizações de modelos**: flexibilidade no controle de versão e atualização dos modelos implantados nas soluções.
- **Arquitetura portátil**: habilita a criação de uma arquitetura de aplicativo portátil que possa ser implantada no Azure, no local e na borda. Os contêineres podem ser implantados diretamente no [Serviço de Kubernetes do Azure](../aks/index.yml), nas [Instâncias de Contêiner do Azure](../container-instances/index.yml) ou em um cluster do [Kubernetes](https://kubernetes.io/) implantado no [Azure Stack](/azure-stack/operator). Para obter mais informações, consulte [Implantar Kubernetes no Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Alta taxa de transferência/baixa latência**: fornece aos clientes a capacidade de dimensionamento para obter requisitos de alta taxa de transferência e baixa latência permitindo que os Serviços Cognitivos sejam fisicamente executados próximos aos dados e ao log do aplicativo. Os contêineres não limitam as transações por segundo (TPS) e poderão ser escalados vertical e horizontalmente para lidar com a demanda se você fornecer os recursos de hardware necessários.
- **Escalabilidade**: com a popularidade cada vez maior da conteinerização, bem como de programa de software de orquestração de contêiner, como o Kubernetes, a escalabilidade está na vanguarda dos avanços tecnológicos. Criado com base em um cluster escalável, o desenvolvimento de aplicativos fornece alta disponibilidade.

## <a name="containers-in-azure-cognitive-services"></a>Contêineres nos Serviços Cognitivos do Azure

Os contêineres dos Serviços Cognitivos do Azure fornecem o conjunto abaixo de contêineres do Docker. Além disso, cada um contém um subconjunto de funcionalidades de serviços encontrados nos Serviços Cognitivos do Azure. É possível encontrar instruções e locais de imagem nas tabelas abaixo. Uma lista de [imagens de contêiner](containers/container-image-tags.md) também está disponível.

### <a name="decision-containers"></a>Contêineres de decisão

| Serviço |  Contêiner | Descrição | Disponibilidade |
|--|--|--|--|
| [Detector de Anomalias][ad-containers] | **Detector de Anomalias** ([imagem](https://hub.docker.com/_/microsoft-azure-cognitive-services-decision-anomaly-detector))  | A API do Detector de Anomalias permite monitorar e detectar anormalidades em seus dados de série temporal com aprendizado de máquina. | Disponível para o público geral |

### <a name="language-containers"></a>Contêineres de linguagem

| Serviço |  Contêiner | Descrição | Disponibilidade |
|--|--|--|--|
| [LUIS][lu-containers] |  **LUIS** ([imagem](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409)) | Carrega um modelo de reconhecimento vocal treinado ou publicado, também conhecido como um aplicativo LUIS, para um contêiner do Docker e fornece acesso às previsões de consulta dos pontos de extremidade da API do contêiner. Você pode coletar logs de consulta do contêiner e carregá-los novamente no [portal do LUIS](https://www.luis.ai) para aumentar a precisão da previsão do aplicativo. | Disponível para o público geral |
| [Análise de Texto][ta-containers-keyphrase] | **Extração de Frases-Chave** ([imagem](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) | Extrai frases-chave para identificar os principais pontos. Por exemplo, para o texto de entrada "A comida estava deliciosa e a equipe era maravilhosa", a API retorna os principais pontos de discussão: "comida" e "equipe maravilhosa". | Versão Prévia |
| [Análise de Texto][ta-containers-language] |  **Detecção de Idioma do Texto** ([imagem](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) | Para até 120 idiomas, detecta em qual idioma o texto de entrada está escrito e informa um código de idioma único para cada documento enviado na solicitação. O código de idioma é emparelhado com uma pontuação que indica a intensidade da pontuação. | Disponível para o público geral |
| [Análise de Texto][ta-containers-sentiment] | **Análise de Sentimento v3** ([imagem](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) | Analisa o texto bruto em busca de pistas sobre sentimentos positivos ou negativos. Esta versão da análise de sentimento retorna rótulos de sentimentos (como *positivos* ou *negativos*) para cada documento e frase dentro dela. |  Disponível para o público geral |
| [Análise de Texto][ta-containers-health] |  **Análise de Texto para integridade** | Extraia informações médicas de textos clínicos não estruturados, depois insira um rótulo nelas. | Versão prévia restrita. [Solicitar acesso][request-access]. |

### <a name="speech-containers"></a>Contêineres de fala

> [!NOTE]
> Será preciso preencher um [formulário de solicitação online](https://aka.ms/csgate) para usar contêineres de Fala.

| Serviço |  Contêiner | Descrição | Disponibilidade |
|--|--|--|
| [API do Serviço de Fala][sp-containers-stt] |  **Reconhecimento de fala** ([imagem](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | Transcreve fala em tempo real contínua em texto. | Disponível para o público geral |
| [API do Serviço de Fala][sp-containers-cstt] | **Reconhecimento de fala personalizado** ([imagem](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | Transcreve em tempo real uma fala contínua em texto usando um modelo personalizado. | Disponível para o público geral |
| [API do Serviço de Fala][sp-containers-tts] | **Conversão de texto em fala** ([imagem](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-text-to-speech)) | Converte o texto em fala que soa natural. | Disponível para o público geral |
| [API do Serviço de Fala][sp-containers-ctts] | **Conversão de texto em fala personalizada** ([imagem](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-text-to-speech)) | Converte o texto em uma fala que soa natural usando um modelo personalizado. | Versão prévia restrita |
| [API do Serviço de Fala][sp-containers-ntts] | **Conversão de texto em fala neural** ([imagem](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-neural-text-to-speech)) | Converte o texto em uma fala que soa natural usando uma tecnologia de rede neural profunda, permitindo obter uma fala sintetizada mais natural. | Disponível para o público geral |
| [API do Serviço de Fala][sp-containers-lid] | **Detecção de idioma da fala** ([imagem](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-language-detection)) | Determina o idioma do áudio. | Versão prévia restrita |

### <a name="vision-containers"></a>Contêineres de visão

> [!WARNING]
> Em 11 de junho de 2020, a Microsoft comunicou que não venderá tecnologia de reconhecimento facial a departamentos de polícia nos Estados Unidos até que um sólido regulamento, com base nos direitos humanos, seja aprovado. Desse modo, os clientes não poderão usar os recursos de reconhecimento facial ou a funcionalidade incluída nos Serviços do Azure, como Detecção Facial ou Video Indexer, se forem um departamento de polícia nos Estados Unidos ou permitirem o uso de desses serviços para e pela polícia.

| Serviço |  Contêiner | Descrição | Disponibilidade |
|--|--|--|--|
| [Pesquisa Visual Computacional][cv-containers] | **OCR de Leitura** ([imagem](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-read)) | O contêiner de OCR de Leitura permite extrair um texto impresso e manuscrito de imagens e documentos compatíveis com os formatos de arquivos JPEG, PNG, BMP, PDF e TIFF. Para obter mais informações, confira a [documentação da API de Leitura](./computer-vision/overview-ocr.md). | Versão prévia restrita. [Solicitar acesso][request-access]. |
| [Análise Espacial][spa-containers] | **Análise espacial** ([imagem](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-spatial-analysis)) | Analisa um vídeo de streaming em tempo real para entender relações espaciais entre as pessoas, a movimentação delas e interações com objetos em ambientes físicos. | Versão prévia restrita. [Solicitar acesso][request-access]. |
| [Detecção Facial][fa-containers] | **Detecção Facial** | Detecta rostos humanos em imagens e identifica atributos, incluindo referências faciais (como narizes e olhos), sexo, idade e outras características faciais previstas pelo computador. Além de detectar, a Detecção Facial pode verificar se duas faces na mesma imagem ou imagens diferentes são iguais usando uma pontuação de confiança, ou comparar faces em relação a um banco de dados para ver se uma face semelhante ou idêntica já existe. Além disso, também pode organizar rostos semelhantes em grupos, usando traços visuais compartilhados. | Indisponível |
| [Reconhecimento de formulários][fr-containers] | **Reconhecimento de Formulários** | O Reconhecimento de Formulários aplica a tecnologia de machine learning para identificar e extrair pares chave-valor e tabelas de formulários. | Indisponível | 


<!--
|[Personalizer](./personalizer/what-is-personalizer.md) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Além disso, alguns contêineres são compatíveis com a oferta de [recursos de vários serviços](cognitive-services-apis-create-account.md) dos Serviços Cognitivos. É possível criar um recurso completo de Serviços Cognitivos e usar a mesma chave de cobrança em serviços com suporte para obter os seguintes serviços:

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

Saiba mais sobre as [receitas de contêiner](containers/container-reuse-recipe.md) que podem ser usadas com os Serviços Cognitivos.

Instale e explore a funcionalidade fornecida pelos contêineres nos Serviços Cognitivos do Azure:

* [Contêineres do Detector de Anomalias][ad-containers]
* [Contêineres da Pesquisa Visual Computacional][cv-containers]
* [Contêineres de Detecção Facial][fa-containers]
* [Contêineres de Reconhecimento de Formulários][fr-containers]
* [Contêineres LUIS (Reconhecimento vocal)][lu-containers]
* [Contêineres da API de Serviço de Fala][sp-containers]
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
[request-access]: https://aka.ms/csgate
