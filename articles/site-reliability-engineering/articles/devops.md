---
title: 'Perguntas frequentes: SRE e DevOps | Microsoft Docs'
titleSuffix: Azure
description: 'Perguntas frequentes: compreendendo a relação entre SRE e DevOps'
author: dnblankedelman
manager: ScottCa
ms.service: site-reliability-engineering
ms.topic: article
ms.date: 09/14/2020
ms.author: dnb
ms.openlocfilehash: 62872443c386c45d9821053dc901ad8e0055b74f
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92674278"
---
# <a name="frequently-asked-questions-whats-the-relationship-between-sre-and-devops"></a>Perguntas frequentes: Qual é a relação entre SRE e DevOps?

Há um conjunto de perguntas comuns que surgem pela relação entre a engenharia de confiabilidade do site e a DevOps, incluindo "como elas são as mesmas? Como eles são diferentes? Podemos ter ambos em nossa organização? ". Este artigo tenta compartilhar algumas das respostas que foram oferecidas pelas comunidades SRE e DevOps que nos dizem mais perto de entender essa relação.

## <a name="how-are-they-the-same"></a>Como eles são os mesmos?

SRE e DevOps são práticas de operações modernas que foram criadas e desenvolvidas em resposta a desafios que incluíram:

- uma complexidade crescente de nossos ambientes de produção e processos de desenvolvimento
- o aumento da dependência dos negócios ao funcionamento contínuo desses ambientes
- a incapacidade de dimensionar a força de funcionários de maneira linear ao tamanho desses ambientes
- a necessidade de se mover mais rápido enquanto ainda mantém a estabilidade operacional

Ambas as práticas de operações exigem atenção aos assuntos que são cruciais para lidar com esses desafios, como monitoramento/observação, automação, documentação e ferramentas de desenvolvimento de software colaborativo.

Há uma sobreposição considerável em ferramentas e áreas de trabalho entre SRE e DevOps. Como _a pasta de trabalho de confiabilidade do site a_ coloca, "SRE acredita nas mesmas coisas que DevOps, mas por motivos ligeiramente diferentes."

## <a name="three-different-ways-to-compare-the-two-operations-practices"></a>Três maneiras diferentes de comparar as duas práticas de operações

As semelhanças entre SRE e DevOps são claras. Onde se torna realmente interessante como as duas diferem ou Diver. Aqui, oferecemos três maneiras de pensar sobre sua relação como uma maneira de trazer algumas nuances para essa pergunta. Você não pode concordar com essas respostas, mas cada uma delas fornece um bom ponto de partida para discussão.

### <a name="class-sre-implements-interface-devops"></a>"Class SRE implementa a interface DevOps"

_A pasta de trabalho de confiabilidade do site_ (mencionada em nossa [lista de livros de recursos](../resources/books.md)) discute SRE e DevOps em seu primeiro capítulo. Esse capítulo usa a frase "Class SRE Implements interface DevOps" como seu subtítulo. Isso serve para sugerir (usando uma frase destinada a desenvolvedores) que SRE poderia ser considerada uma implementação específica da filosofia DevOps. Como o capítulo destaca, "DevOps é relativamente silencioso sobre como executar operações em um nível detalhado", enquanto o SRE é consideravelmente mais proexplicativo em suas práticas. Assim, uma resposta possível à pergunta de como os dois relacionados é SRE poderia ser considerada uma das muitas implementações possíveis de DevOps.

### <a name="sre-is-to-reliability-as-devops-is-to-delivery"></a>SRE é a confiabilidade que o DevOps é para entrega

Essa comparação é um pouco muddied porque há várias definições para SRE e DevOps, mas ainda é potencialmente útil. Ele começa com a pergunta "se você tivesse que filtrar cada prática de operações em uma ou duas palavras que reflitam sua preocupação principal, o que seria?"

Se usarmos essa definição de SRE do [Hub de engenharia de confiabilidade do site](../index.yml):

> A Engenharia de Confiabilidade do Site é uma disciplina de engenharia dedicada a ajudar uma organização a alcançar de maneira sustentável o nível de confiabilidade apropriado em sistemas, serviços e produtos.

em seguida, seria fácil dizer que a palavra para SRE é "confiabilidade". Ter o direito no meio do nome também oferece uma excelente evidência para essa declaração.

Se usarmos essa definição de DevOps da [central de recursos DevOps do Azure](/azure/devops/learn/):

> O DevOps é a união de pessoas, processos e produtos para habilitar a entrega contínua de valor para nossos usuários finais.

em seguida, um detalhamento semelhante para DevOps poderia ser "Delivery".

Portanto, "SRE é a confiabilidade que o DevOps é para entrega".

### <a name="direction-of-attention"></a>Direção de atenção

Essa resposta está entre aspas ou um pouco desenhada de uma contribuição por Thomas Limoncelli para o livro de _busca SRE_ mencionado em nossa [lista de livros de recursos](../resources/books.md). Ele observa que os engenheiros de DevOps estão amplamente concentrados no pipeline de desenvolvimento de software com responsabilidades de operações de produção ocasionais, enquanto os SREs se concentram em operações de produção com responsabilidades de pipeline SDLC ocasionais.

Mas o mais importante é que ele também desenha um diagrama que começa com o processo de desenvolvimento de software em um lado e as operações de produção funcionam no outro. Os dois são conectados pelo pipeline comum criado para pegar o código de um desenvolvedor, gerenciamos-lo por meio do número desejado de testes e do estágio e, em seguida, mover esse código para produção.

Limoncelli observa que os engenheiros de DevOps começam no ambiente de desenvolvimento e automatizam as etapas para a produção. Depois de concluído, eles voltarão a otimizar os afunilamentos.

SREs, por outro lado, concentre-se nas operações de produção e alcance profundamente o pipeline como um meio de melhorar o resultado final (basicamente trabalhando na direção oposta).

É essa diferença na direção do SRE e do foco do DevOps que pode ajudar a diferenciá-los.

## <a name="coexistence-in-the-same-organization"></a>Coexistência na mesma organização

A pergunta final que gostaríamos de abordar é "você pode ter SRE e DevOps na mesma organização?"

A resposta a essa pergunta é um Emphatic "Sim!".

Esperamos que as respostas anteriores ofereçam uma ideia de como as duas práticas de operações se sobrepõem e, quando não se sobrepõem, como elas podem ser complementares em foco. As organizações com uma prática DevOps estabelecida podem experimentar as práticas de SRE em uma pequena escala (por exemplo, experimentando SLIs e SLOs) sem precisar confirmar a criação de SRE cargos ou equipes. Esse é um padrão de adoção SRE relativamente comum.

## <a name="next-steps"></a>Próximas etapas

Interessado em aprender mais sobre Engenharia de confiabilidade de site ou DevOps? Confira o [Hub de engenharia de confiabilidade do site](../index.yml) e o [centro de recursos do Azure DevOps](/azure/devops/learn/).