---
title: Introdução à análise espacial Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Este documento explica os conceitos básicos e os recursos de um contêiner de análise espacial Pesquisa Visual Computacional.
services: cognitive-services
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: f90e4e5e187977f0ee77a565ff9143902ea3a10d
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736828"
---
# <a name="introduction-to-computer-vision-spatial-analysis"></a>Introdução à análise espacial Pesquisa Visual Computacional

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

## <a name="example-use-cases-for-spatial-analysis"></a>Casos de uso de exemplo para análise espacial

Veja a seguir os casos de uso de exemplo que tínhamos em mente à medida que projetamos e testamos a análise espacial.

**Conformidade do Distancing social** – um espaço do Office tem várias câmeras que usam análise espacial para monitorar a conformidade do Distancing social medindo a distância entre as pessoas. O Gerenciador de instalações pode usar calor mostrando estatísticas agregadas de conformidade de distancing social ao longo do tempo para ajustar o espaço de trabalho e facilitar a distancing social.

**Análise do comprador** – uma loja de supermercado usa câmeras apontadas em exibições de produto para medir o impacto das alterações de mercadorias no tráfego de armazenamento. O sistema permite que o Gerenciador de loja identifique quais novos produtos impulsionam a maioria das alterações no envolvimento.

**Gerenciamento de fila** -as câmeras apontadas em filas de check-out fornecem alertas aos gerentes quando o tempo de espera fica muito longo, permitindo que eles abram mais linhas. Os dados históricos sobre o abandono da fila fornecem informações sobre o comportamento do consumidor.

**Conformidade de máscara facial** – lojas de varejo podem usar câmeras que apontam para os Fronts da loja para verificar se os clientes que estão acompanhando a loja estão aproveitando máscaras de face para manter a conformidade com a segurança e analisar estatísticas agregadas para obter informações sobre tendências de uso de máscara. 

**Criando análise de & de ocupação** – um edifício de escritório usa câmeras focadas em entrada para espaços-chave para medir o footfall e como as pessoas usam o local de trabalho. As informações permitem que o gerente de construção ajuste o serviço e o layout para melhor atender occupants.

**Detecção mínima da equipe** -em uma data center, as câmeras monitoram a atividade em relação aos servidores. Quando os funcionários estão corrigindo fisicamente o equipamento confidencial, duas pessoas sempre precisam estar presentes durante o reparo por motivos de segurança. As câmeras são usadas para verificar se essa diretriz é seguida.

**Otimização do local de trabalho** – em um restaurante casual rápido, as câmeras na cozinha são usadas para gerar informações agregadas sobre o fluxo de trabalho dos funcionários. Isso é usado por gerentes para melhorar processos e treinamento para a equipe.

## <a name="considerations-when-choosing-a-use-case"></a>Considerações ao escolher um caso de uso

**Evite alertas de segurança críticos** -a análise espacial não foi projetada para alertas críticos em tempo real de segurança. Ele não deve ser confiado em cenários quando são necessários alertas em tempo real para disparar a intervenção para evitar ferimentos, como desligar uma parte de máquinas pesadas quando uma pessoa está presente. Ele pode ser usado para redução de riscos usando estatísticas e intervenção para reduzir o comportamento arriscado, como pessoas que inserem uma área restrita/proibida.

**Evite usar para decisões relacionadas a empregos** -a análise espacial fornece métricas de probabilística sobre o local e a movimentação de pessoas dentro de um espaço. Embora esses dados possam ser úteis para a melhoria do processo agregado, os dados não são um bom indicador de desempenho de trabalho individual e não devem ser usados para tomar decisões relacionadas a empregos.

**Evite usar para decisões relacionadas à assistência médica** -a análise espacial fornece probabilística e dados parciais relacionados aos movimentos das pessoas. Os dados não são adequados para tomar decisões relacionadas à integridade.

**Evite usar em espaços protegidos** – proteger a privacidade dos indivíduos, avaliando locais e posições da câmera, ajustando os ângulos e a região dos interesses para que eles não sobreusem áreas protegidas como banheiros.

**Considere atentamente o uso em escolas ou em instalações idosas** – a análise espacial não foi altamente testada com dados que contêm menores sob a idade de 18 ou adultos em idade 65. Recomendamos que os clientes avaliem minuciosamente as taxas de erro para seu cenário em ambientes em que essas idades predominate.

**Considere cuidadosamente usar em espaços públicos** – avaliar locais e posições da câmera, ajustar ângulos e região de interesses para minimizar a coleta de espaços públicos. A iluminação e o clima em espaços públicos como ruas e parques afetarão significativamente o desempenho do sistema de análise espacial e é extremamente difícil fornecer uma divulgação efetiva em espaços públicos.

## <a name="spatial-analysis-gating-for-public-preview"></a>Análise espacial se retenção da visualização pública

Para garantir que a análise espacial seja usada para cenários para os quais foi desenvolvida, estamos disponibilizando essa tecnologia para os clientes por meio de um processo de aplicativo. Para obter acesso à análise espacial, você precisará começar preenchendo nosso formulário de ingestão online. [Inicie seu aplicativo aqui](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u).

O acesso à visualização pública de análise espacial está sujeito à exclusiva critério da Microsoft com base em nossos critérios de qualificação, processo de habilitação e disponibilidade para dar suporte a um número limitado de clientes durante essa visualização restrita. Na visualização pública, estamos procurando clientes que têm uma relação significativa com a Microsoft, estão interessados em trabalhar conosco nos casos de uso recomendados e em cenários adicionais que estão em manutenção com nossos compromissos de ia responsáveis.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Características e limitações para análise espacial](/legal/cognitive-services/computer-vision/accuracy-and-limitations?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext)