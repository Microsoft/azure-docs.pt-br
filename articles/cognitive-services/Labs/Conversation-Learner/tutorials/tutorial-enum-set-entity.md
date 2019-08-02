---
title: Quando usar entidades ENUM e ações de ENTIDADES definido com um modelo de aprendiz de conversa – serviços Cognitivos do Azure | Microsoft Docs
titleSuffix: Azure
description: Saiba quando é apropriado usar entidades ENUM e ações de ENTIDADES definido com um modelo de aprendiz de conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nolachar
ms.openlocfilehash: ed18d30a0c3f5d51cb3a07b8948863cdda16c1ae
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67845959"
---
# <a name="when-to-use-enum-entities-and-set-entity-actions"></a>Quando usar entidades ENUM e ações de ENTIDADES definido

Este tutorial explicará quando você deve usar as entidades ENUM (enumeração) e ações SET_ENTITY.

## <a name="video"></a>Vídeo

[![Visualização de Tutorial do conjunto de entidade](https://aka.ms/cl_Tutorial_v3_SetEntity_Preview)](https://aka.ms/cl_Tutorial_v3_SetEntity)



## <a name="what-is-covered"></a>O que é coberto

Este tutorial apresenta dois novos recursos. Um novo tipo de entidade chamada ENUM (abreviação de enumeração) e um novo tipo de ação chamado SET_ENTITY, que pode fazer referência a um desses valores de enumeração e, como o nome implica, define a entidade para esse valor. Conforme você aprenderá abaixo, esses novos recursos são usados juntos e explicaremos o que são e como usá-los abaixo. Antes de entrarmos em detalhes, é importante entender qual é o problema em que esses recursos ajudam a resolver.

![enum_entity_type.png](../media/tutorial-enum-set-entity/enum_entity_type.png)
![action_set_entity_type.png](../media/tutorial-enum-set-entity/action_set_entity_type.png)

## <a name="problem"></a>Problema

Há casos em conversas em que o significado das palavras depende do contexto.  Normalmente as palavras-chave rotuladas são aprendidas e extraídos usando um serviço de reconhecimento vocal, mas nesses casos esses sistemas talvez não consiga aprenderá a usar os exemplos de rótulos.

Imagine que você overhear parte de uma conversa entre as pessoas próximas e apenas ouve a palavra "Yes". Você não saberia o que "Sim" concordando em ou confirmando, pois você não respondeu à pergunta solicitado antes dele. A pergunta feita antes de é o contexto, que fornece um significado para a resposta. Da mesma forma, desde "Sim" é uma resposta comuns a várias perguntas diferentes, ele não pode ser aprendido, fornecendo exemplos de como você faria com [personalizado treinado](04-introduction-to-entities.md) entidades porque, em seguida, ele seria aprender rotular cada "Sim" como essa entidade.

### <a name="example"></a>Exemplo

Além disso, vamos esclarecer com o exemplo a seguir:

Bot: Você gosta de serviços Cognitivos do Azure?
Usuário: Sim Bot: Você gosta de sorvete?
Usuário: Sim

Nos tutoriais anteriores, analisamos [personalizado treinado](04-introduction-to-entities.md) entidades e seu pensamento inicial seria criar uma entidade chamada "likesCogServices" e rotular o primeiro "Sim" como essa entidade.  No entanto, o sistema teria também rotular a segunda "Sim". Quando tentamos corrigir o rótulo da segunda "Sim" para "likesIceCream", podemos, em seguida, criaria um conflito de duas entradas mesmos "Sim" coisas diferentes do significado e ficavam presas.

É nesses casos em que você precisa usar ações de SET_ENTITY e entidades ENUM.

## <a name="when-to-use-enums-or-setentity-actions"></a>Quando usar ENUMs ou ações SET_ENTITY

Use essas regras abaixo para saber quando usar entidades ENUM e ações SET_ENTITY:

- Detecção ou configuração de entidade é dependente de contexto
- Número de valores possíveis é fixo (Sim e não seria dois valores)

Em outras palavras, usá-los para quaisquer prompts de finalização, como as perguntas de confirmação que sempre resultam em Sim ou não.

> [!NOTE]
> No momento, temos a limitação dos valores de até 5 por entidade de enum. Cada valor usa um dos slots no limite de 64 atual. Consulte [cl valores e limites](../cl-values-and-boundaries.md)

Exemplo: Bot: Seu pedido está correto?
Usuário: Sim

Quando os valores possíveis da entidade são abertas e não corrigido, você precisará usar um recurso alternativo, como [esperado entidade](05-expected-entity.md).

Exemplo: Bot: Qual é o seu nome?
Usuário: Matt Bot: O que é a sua cor favorita?
Usuário: Silver

Essas solicitações são consideradas em aberto porque eles poderiam ser respondidos com valores arbitrários.

## <a name="what"></a>O que

### <a name="enum-entities"></a>Entidades ENUM

Entidades ENUM são criadas como outras entidades. Semelhante a "programação" entidades, você não é possível rotular palavras como essas entidades. Em vez disso, devem ser definidas por meio de código ou ações SET_ENTITY.

![enum_entity_creation.png](../media/tutorial-enum-set-entity/enum_entity_creation.png)

### <a name="set-entity-actions"></a>Definir as ações de entidade

Conforme mencionado acima, ações de "Conjunto de entidades" simplesmente definir uma entidade para um valor de enum conhecidos. Você pode obter os mesmos resultados, criando uma ação de retorno de chamada de API e usando o Gerenciador de memória para definir a entidade como um valor. Por exemplo `memory.Set(entityName, entityValue)`. Necessidade de escrever esse código e criar essas ações se tornaria entediante e difícil de gerenciar - modo de aprendiz de conversa tem ações especiais para facilitar esse trabalho e gerar essas ações automaticamente quando eles são usados. Ter como ações independentes preserva a capacidade de composição sem sendo juntamente com outras ações ou código no seu bot.

- Defina as ações de entidade só podem ser criadas para se referir a um valor de uma entidade de enum, portanto você deve criar uma entidade de enum pela primeira vez.
- Defina as ações de entidade são também "não-await", pois eles têm nenhuma saída visível e precisam ser acompanhado por uma ação de "espera" que o usuário pode ver.
- Defina as ações de entidade são imutáveis que significa que você não pode editá-los após a criação.

![action_set_entity_creation.png](../media/tutorial-enum-set-entity/action_set_entity_creation.png)

### <a name="automatic-action-generation"></a>Geração automática de ação

Se existe uma entidade de enum em seu modelo, o Aprendiz de conversa criar ações para cada um dos possíveis valores de espaço reservado e disponibilizá-las selecionar durante o treinamento. Após a seleção, a ação seria criada automaticamente para você.

Por exemplo, se eu criar uma entidade de enum com os valores "Sim" e "Não":

![enum_entity_order_confirmation.png](../media/tutorial-enum-set-entity/enum_entity_order_confirmation.png)

Mesmo sem explicitamente criar ações para essa nova enum, você deverá ver duas novas ações disponíveis durante o treinamento:

![action_set_entity_sample.png](../media/tutorial-enum-set-entity/action_set_entity_sample.png)


## <a name="create-a-bot-using-these-new-features"></a>Crie um Bot usando esses novos recursos

### <a name="requirements"></a>Requisitos

Este tutorial exige que o bot de tutorial geral esteja em execução

    npm run tutorial-general

Criaremos um bot para simular a ordenação de fast-food. Ele terá valores discretos para tamanhos de bebidas e frita (pequeno/médio/grande) e confirmação de perguntas com Sim / não respostas. Ambas essas entidades satisfazem as duas regras acima de ser dependente de contexto respostas e valores fixos.

### <a name="create-the-model"></a>Criar o modelo

1. Na IU da web, clique em "Importar"
2. Selecione o tutorial chamado "Tutorial-Enum-conjunto-Entity"

Isso fará com que você navegue para a página de gerenciamento de modelo.
Observe que o modelo já contém algumas entidades de enum e definir ações de entidades.

### <a name="create-the-first-dialog"></a>Criar o primeiro diálogo

1. No painel de navegação à esquerda, clique em "Caixas de diálogo de treinamento", clique no botão "Nova com a caixa de diálogo de treinamento".
2. Como o tipo de usuário em "Olá, eu gostaria de ter solicitar um refrigerante e frita.".
3. Clique no botão de "Pontuação ações"

   > O usuário não tiver especificado tamanhos de bebida ou frita, portanto, precisamos peça.

4. Selecione a ação de resposta: "Bebida qual tamanho você gostaria?"
5. Como o usuário digite, "grande"
6. Clique no botão de "Pontuação ações"
7. Selecione a ação SET_ENTITY - "drinkSize: GRANDE"
8. Selecione a ação de resposta: "Qual tamanho frita deseja?"
9. Como o tipo de uso em, "Hum, tornar aquelas de uma mídia.
10. Clique no botão de "Pontuação ações"
11. Selecione a ação SET_ENTITY - "friesSize: MÉDIA"
12. Selecione a ação de resposta: "Você deseja qualquer Condimentos?"
13. Como o tipo de uso em "Sim"
14. Clique no botão de "Pontuação ações"
15. Selecione a ação SET_ENTITY - "condimentsConfirmation: SIM"
16. Selecione a ação de resposta: "Okey, tenho um pedido para uma bebida grande e MÉDIAS frita. Isso está correto?"
17. Como o tipo de uso em "Sim"
18. Clique no botão de "Pontuação ações"
19. Selecione a ação SET_ENTITY - "orderConfirmation: SIM"
20. Selecione a ação de resposta: "Okey, o número do pedido é 58. Aguarde até ali."
21. Clique em "Salvar" para fechar a caixa de diálogo

Você acabou de criar sua primeira caixa de diálogo usando ações de SET_ENTITY e entidades ENUM. Você pode fazer muitas combinações de mais do usuário, especificando informações parciais ou fazer experimentos com outros tipos de perguntas de finalização.

> [!NOTE]
> Durante o treinamento você verá os espaços reservados para as ações de SET_ENTITY como
>
> ![action_set_entity_training.png](../media/tutorial-enum-set-entity/action_set_entity_training.png)
>
> mas, quando criar logon caixas de diálogo ou usando implantado bots os usuários não verão esses.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Entradas alternativas](./10-alternative-inputs.md)
