---
title: O que é a Análise Espacial?
titleSuffix: Azure Cognitive Services
description: Este documento explica os conceitos básicos e os recursos de um contêiner de Análise Espacial da Pesquisa Visual Computacional.
services: cognitive-services
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.openlocfilehash: 5aa34ce15d96112450a7c15debcd92312c1d9e19
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284535"
---
# <a name="what-is-spatial-analysis"></a>O que é a Análise Espacial?

O serviço de Análise Espacial ajuda as organizações a maximizar o valor dos espaços físicos, compreendendo os movimentos e a presença de pessoas em determinada área. Ele permite ingerir vídeos de câmeras de vigilância ou CCTV, executar operações de IA para extrair insights dos fluxos de vídeo e gerar eventos para que sejam usados por outros sistemas. Com a entrada de um fluxo de câmera, uma operação de IA pode executar tarefas como contar o número de pessoas que estão entrando em um espaço ou medir a conformidade com as diretrizes de máscara facial e distanciamento social.

<!--This documentation contains the following types of articles:
* The [quickstarts](./quickstarts-sdk/analyze-image-client-library.md) are step-by-step instructions that let you make calls to the service and get results in a short period of time. 
* The [how-to guides](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) contain instructions for using the service in more specific or customized ways.
* The [conceptual articles](tbd) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions.-->

## <a name="what-it-does"></a>O que faz

As principais operações da Análise Espacial foram todas criadas em um pipeline que ingere um vídeo, detecta pessoas no vídeo, acompanha-as durante a movimentação delas ao longo do tempo e gera eventos conforme elas interagem com as regiões de interesse.

## <a name="spatial-analysis-features"></a>Recursos da Análise Espacial

| Recurso | Definição |
|------|------------|
| **Detecção de Pessoas** | Este componente responde à pergunta "Onde estão as pessoas desta imagem?" Ele localiza as pessoas em uma imagem e transmite uma caixa delimitadora que indica a localização de cada uma delas para o componente de acompanhamento de pessoas. |
| **Acompanhamento de Pessoas** | Este componente conecta as detecções de pessoas ao longo do tempo conforme elas se movimentam na frente de uma câmera. Ele usa a lógica temporal sobre como as pessoas normalmente se movem e informações básicas sobre a aparência geral delas. Não acompanha as pessoas em várias câmeras. Se uma pessoa sair do campo de visão por mais de um minuto, aproximadamente, e entrar novamente na visão da câmera, o sistema perceberá isso como uma nova pessoa. O Acompanhamento de Pessoas não identifica exclusivamente os indivíduos em várias câmeras. Ele não usa o reconhecimento facial nem o acompanhamento de passos. |
| **Detecção de Máscara Facial** | Este componente detecta a localização do rosto de uma pessoa no campo de visão da câmera e identifica a presença de uma máscara facial. A operação de IA verifica as imagens do vídeo. Quando um rosto é detectado, o serviço fornece uma caixa delimitadora em torno dele. Usando as funcionalidades de detecção de objetos, ele identifica a presença de máscaras faciais dentro da caixa delimitadora. A detecção de máscara facial não envolve a diferenciação entre rostos, a previsão e a classificação de atributos faciais nem a realização de reconhecimento do rosto. |
| **Região de Interesse** | Esta é uma zona ou uma linha definida pelo usuário no quadro de vídeo de entrada. Quando uma pessoa interage com essa região no vídeo, o sistema gera um evento. Por exemplo, para a operação PersonCrossingLine, uma linha é definida no vídeo. Quando uma pessoa cruza essa linha, um evento é gerado. |
| **Evento** | Um evento é a saída principal da Análise Espacial. Cada operação emite um evento específico periodicamente (como uma vez por minuto) ou sempre que um gatilho específico ocorre. O evento inclui informações sobre o que ocorreu no vídeo de entrada, mas não inclui imagens nem vídeos. Por exemplo, a operação PeopleCount pode emitir um evento que contém a contagem atualizada toda vez que a contagem de pessoas é alterada (gatilho) ou uma vez a cada minuto (periodicamente). |

## <a name="get-started"></a>Introdução

### <a name="public-preview-gating"></a>Restrição da versão prévia pública

A fim de garantir que a Análise Espacial seja usada para cenários para os quais foi desenvolvida, estamos disponibilizando essa tecnologia para os clientes por meio de um processo de aplicativo. Para obter acesso à Análise Espacial, você precisará começar preenchendo nosso formulário de entrada online. [Inicie seu aplicativo aqui](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u).

O acesso à versão prévia pública da Análise Espacial está sujeito a critério exclusivo da Microsoft com base em nossos critérios de qualificação, processo de verificação e disponibilidade para dar suporte a um número limitado de clientes durante essa versão prévia restrita. Na versão prévia pública, estamos procurando clientes que tenham uma relação significativa com a Microsoft, estejam interessados em trabalhar conosco nos casos de uso recomendados e em cenários adicionais que mantenham nossos compromissos com o uso responsável de IA.

### <a name="follow-a-quickstart"></a>Siga um guia de início rápido

Depois de receber acesso à Análise Espacial, siga o [guia de início rápido](spatial-analysis-container.md) para configurar o contêiner e começar a analisar o vídeo.

## <a name="responsible-use-of-spatial-analysis-technology"></a>Uso responsável da tecnologia de Análise Espacial

Para saber como usar a tecnologia de Análise Espacial com responsabilidade, confira a [nota de transparência](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext). As notas de transparência da Microsoft têm a finalidade de ajudar você a entender como funciona nossa tecnologia de IA, as escolhas que os proprietários do sistema podem fazer para influenciar o desempenho e o comportamento do sistema e a importância de pensar em todo o sistema, incluindo a tecnologia, as pessoas e o ambiente.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Guia de início rápido: Contêiner de Análise Espacial](spatial-analysis-container.md)