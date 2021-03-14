---
title: Visão geral da análise espacial
titleSuffix: Azure Cognitive Services
description: Este documento explica os conceitos básicos e os recursos de um contêiner de análise espacial Pesquisa Visual Computacional.
services: cognitive-services
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: ad05dd59c925242baf5c2b0e36c1f51bc4fec5d4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575361"
---
# <a name="overview-of-computer-vision-spatial-analysis"></a>Visão geral de Pesquisa Visual Computacional análise espacial

Pesquisa Visual Computacional análise espacial é um novo recurso dos serviços cognitivas do Azure Pesquisa Visual Computacional que ajuda as organizações a maximizar o valor de seus espaços físicos, compreendendo os movimentos e a presença das pessoas em uma determinada área. Ele permite que você ingerir vídeo de câmeras CCTVs ou de vigilância, executar operações de ia para extrair informações dos fluxos de vídeo e gerar eventos a serem usados por outros sistemas. Com a entrada de um fluxo de câmera, uma operação de ia pode fazer coisas como contar o número de pessoas que estão inserindo um espaço ou medir a conformidade com a máscara de face e as diretrizes de distancing social.

## <a name="the-basics-of-spatial-analysis"></a>Noções básicas da análise espacial

Atualmente, as principais operações de análise espacial são todas criadas em um pipeline que ingere vídeo, detecta pessoas no vídeo, controla as pessoas à medida que elas se movimentam ao longo do tempo e gera eventos à medida que as pessoas interagem com regiões de interesse.

## <a name="spatial-analysis-terms"></a>Termos de análise espacial

| Termo | Definição |
|------|------------|
| Detecção de pessoas | Este componente responde à pergunta "onde estão as pessoas nesta imagem"? Ele localiza seres humanos em uma imagem e passa uma caixa delimitadora que indica o local de cada pessoa para o componente de acompanhamento de pessoas. |
| Acompanhamento de pessoas | Esse componente conecta as detecções de pessoas ao longo do tempo à medida que as pessoas se movimentam na frente de uma câmera. Ele usa lógica temporal sobre como as pessoas normalmente se movem e informações básicas sobre a aparência geral das pessoas para fazer isso. Ele não rastreia as pessoas em várias câmeras. Se uma pessoa existir o campo de exibição de uma câmera por mais de aproximadamente um minuto e, em seguida, entrar novamente no modo de exibição de câmera, o sistema irá perceber isso como uma nova pessoa. O controle de pessoas não identifica exclusivamente indivíduos entre câmeras. Ele não usa o reconhecimento facial ou o acompanhamento de da. |
| Detecção de máscara facial | Esse componente detecta o local da face de uma pessoa no campo de exibição da câmera e identifica a presença de uma máscara de face. Para fazer isso, a operação de ia examina as imagens do vídeo; onde uma face é detectada, o serviço fornece uma caixa delimitadora em todo o rosto. Usando os recursos de detecção de objetos, ele identifica a presença de máscaras de face dentro da caixa delimitadora. A detecção de máscara facial não envolve a distinção de uma face de outra face, a previsão ou a classificação de atributos faciais ou a realização do reconhecimento facial. |
| Região de interesse | Essa é uma zona ou linha definida no vídeo de entrada como parte da configuração. Quando uma pessoa interage com a região do vídeo, o sistema gera um evento. Por exemplo, para a operação PersonCrossingLine, uma linha é definida no vídeo. Quando uma pessoa cruza essa linha, um evento é gerado. |
| Evento | Um evento é a saída principal da análise espacial. Cada operação emite um evento específico periodicamente (por exemplo, uma vez por minuto) ou quando ocorre um gatilho específico. O evento inclui informações sobre o que ocorreu no vídeo de entrada, mas não inclui imagens ou vídeos. Por exemplo, a operação PeopleCount pode emitir um evento que contém a contagem atualizada toda vez que a contagem de pessoas altera (gatilho) ou uma vez a cada minuto (periodicamente). |

## <a name="responsible-use-of-spatial-analysis-technology"></a>Uso responsável da tecnologia de análise espacial

Para saber como usar a tecnologia de análise espacial com responsabilidade, consulte a [Nota de transparência](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext). As notas de transparência da Microsoft têm a finalidade de ajudá-lo a entender como funciona nossa tecnologia ia, as escolhas que os proprietários do sistema podem fazer para influenciar o desempenho e o comportamento do sistema e a importância de pensar em todo o sistema, incluindo a tecnologia, as pessoas e o ambiente.

## <a name="spatial-analysis-gating-for-public-preview"></a>Análise espacial se retenção da visualização pública

Para garantir que a análise espacial seja usada para cenários para os quais foi desenvolvida, estamos disponibilizando essa tecnologia para os clientes por meio de um processo de aplicativo. Para obter acesso à análise espacial, você precisará começar preenchendo nosso formulário de ingestão online. [Inicie seu aplicativo aqui](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u).

O acesso à visualização pública de análise espacial está sujeito à exclusiva critério da Microsoft com base em nossos critérios de qualificação, processo de habilitação e disponibilidade para dar suporte a um número limitado de clientes durante essa visualização restrita. Na visualização pública, estamos procurando clientes que têm uma relação significativa com a Microsoft, estão interessados em trabalhar conosco nos casos de uso recomendados e em cenários adicionais que estão em manutenção com nossos compromissos de ia responsáveis.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução ao contêiner de análise espacial](spatial-analysis-container.md)