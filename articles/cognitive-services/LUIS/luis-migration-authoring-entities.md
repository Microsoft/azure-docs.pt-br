---
title: Migrar para a entidade de aprendizado de máquina v3
description: A criação v3 fornece um novo tipo de entidade, a entidade de aprendizado de máquina, juntamente com a capacidade de adicionar relações à entidade de aprendizado de máquina e a outras entidades ou recursos do aplicativo.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/08/2020
ms.openlocfilehash: 667226770d25ef1687420b1c13bc71863f987e33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324681"
---
# <a name="migrate-to-v3-authoring-entity"></a>Migrar para a entidade de criação v3

A criação v3 fornece um novo tipo de entidade, a entidade de aprendizado de máquina, juntamente com a capacidade de adicionar relações à entidade de aprendizado de máquina e a outras entidades ou recursos do aplicativo.

## <a name="entities-are-decomposable-in-v3"></a>As entidades são decompostas em v3

As entidades criadas com as APIs de criação v3, usando as [APIs](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview) ou com o portal, permitem que você crie um modelo de entidade em camadas com um pai e filhos. O pai é conhecido como a **entidade de aprendizado de máquina** e os filhos são conhecidos como **subentidades** da entidade aprendida do computador.

Cada subentidade também é uma entidade de aprendizado de máquina, mas com as opções de configuração adicionadas dos recursos.

* Os **recursos necessários** são regras que garantem que uma entidade seja extraída quando corresponder a um recurso. A regra é definida pelo recurso necessário para o modelo:
    * [Entidade predefinida](luis-reference-prebuilt-entities.md)
    * [Entidade de expressão regular](reference-entity-regular-expression.md)
    * [Listar entidade](reference-entity-list.md).

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>Como essas novas relações se comparam à criação v2

A criação v2 forneceu entidades hierárquicas e compostas juntamente com funções e recursos para realizar essa mesma tarefa. Como as entidades, os recursos e as funções não estavam explicitamente relacionados entre si, era difícil entender como o LUIS implicava as relações durante a previsão.

Com v3, a relação é explícita e projetada pelos autores do aplicativo. Isso permite que você, como autor do aplicativo,:

* Veja visualmente como o LUIS está prevendo essas relações, no exemplo declarações
* Teste para essas relações com o [painel de teste interativo](luis-interactive-test.md) ou no ponto de extremidade
* Use essas relações no aplicativo cliente, por meio de um objeto bem estruturado, nomeado e aninhado [. JSON](reference-entity-machine-learned-entity.md)

## <a name="planning"></a>Planejamento

Ao migrar, considere o seguinte em seu plano de migração:

* Faça backup do aplicativo LUIS e realize a migração em um aplicativo separado. Ter um aplicativo V2 e v3 disponível ao mesmo tempo permite que você valide as alterações necessárias e o impacto nos resultados da previsão.
* Capturar métricas de sucesso de previsão atuais
* Capturar informações do painel atual como um instantâneo do status do aplicativo
* Revisar as tentativas, entidades, listas de frases, padrões e testes de lote existentes
* Os elementos a seguir podem ser migrados **sem alteração**:
    * Intenções
    * Entidades
        * Entidade de expressão regular
        * Entidade de lista
    * Recursos
        * Lista de frases
* Os elementos a seguir precisam ser migrados **com as alterações**:
    * Entidades
        * Entidade hierárquica
        * Entidade composta
    * Funções – as funções só podem ser aplicadas a uma entidade de aprendizado de máquina (pai). Funções não podem ser aplicadas a subentidades
    * Testes em lotes e padrões que usam as entidades hierárquicas e compostas

Ao projetar seu plano de migração, deixe tempo para examinar as entidades finais de aprendizado de máquina, depois que todas as entidades hierárquicas e compostas tiverem sido migradas. Embora uma migração direta funcione, depois de fazer a alteração e examinar os resultados do teste do lote e prever o JSON, o JSON mais unificado pode levar você a fazer alterações para que as informações finais entregues ao aplicativo do lado do cliente sejam organizadas de forma diferente. Isso é semelhante à refatoração de código e deve ser tratado com o mesmo processo de revisão que sua organização tem em vigor.

Se você não tiver testes de lote em vigor para seu modelo V2 e migrar os testes de lote para o modelo V3 como parte da migração, não será possível validar como a migração afetará os resultados de previsão do ponto de extremidade.

## <a name="migrating-from-v2-entities"></a>Migrando de entidades v2

Ao começar a mudar para o modelo de criação v3, você deve considerar como mover para a entidade de aprendizado de máquina e suas subentidades e recursos.

A tabela a seguir anota quais entidades precisam migrar de um design de entidade v2 para um v3.

|Tipo de entidade de criação v2|Tipo de entidade de criação v3|Exemplo|
|--|--|--|
|Entidade composta|Entidade aprendida do computador|[Saiba Mais](#migrate-v2-composite-entity)|
|Entidade hierárquica|função da entidade de aprendizado de máquina|[Saiba Mais](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>Migrar entidade composta v2

Cada filho da composição v2 deve ser representado com uma subentidade da entidade de aprendizado de máquina v3. Se o filho composto for uma expressão predefinida, regular ou uma entidade de lista, isso deverá ser aplicado como um recurso necessário na subentidade.

Considerações ao planejar a migração de uma entidade composta para uma entidade de aprendizado de máquina:
* Entidades filho não podem ser usadas em padrões
* Entidades filho não são mais compartilhadas
* Entidades filho precisam ser rotuladas se usadas para serem aprendidas sem computador

### <a name="existing-features"></a>Recursos existentes

Qualquer lista de frases usada para impulsionar palavras na entidade composta deve ser aplicada como um recurso à entidade de aprendizado de máquina (pai), à entidade de subentidade (filho) ou à intenção (se a lista de frases se aplicar apenas a uma intenção). Planeje adicionar o recurso à entidade em que ele deve aumentar significativamente. Não adicione o recurso genericamente à entidade de aprendizado de máquina (pai), se ele aumentará significativamente a previsão de uma subentidade (filho).

### <a name="new-features"></a>Novos recursos

Na criação v3, adicione uma etapa de planejamento para avaliar entidades como possíveis recursos para todas as entidades e intenções.

### <a name="example-entity"></a>Entidade de exemplo

Essa entidade é apenas um exemplo. Sua própria migração de entidade pode exigir outras considerações.

Considere uma composição v2 para modificar uma pizza `order` que usa:
* datetimeV2 predefinidos para tempo de entrega
* lista de frases para aumentar determinadas palavras, como pizza, pizza, Crust e ingredientes
* listar entidade para detectar ingredientes como cogumelos, olivas, pepperoni.

Um exemplo de expressão para essa entidade é:

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

A tabela a seguir demonstra a migração:

|Modelos v2|Modelos v3|
|--|--|
|Entidade pai-componente nomeada `Order`|Entidade pai-Machine-Learning chamada `Order`|
|DatetimeV2 predefinido filho|* Migre a entidade predefinida para o novo aplicativo.<br>* Adicione o recurso necessário no pai para datetimeV2 predefinidos.|
|Entidade de lista de filhos para ingredientes|* Migre a entidade de lista para o novo aplicativo.<br>* Em seguida, adicione um recurso necessário no pai para a entidade de lista.|


## <a name="migrate-v2-hierarchical-entity"></a>Migrar entidade hierárquica v2

Na criação v2, uma entidade hierárquica foi fornecida antes das funções existentes em LUIS. Ambos serviam a mesma finalidade de extrair entidades com base no uso de contexto. Se você tiver entidades hierárquicas, poderá considerá-las como entidades simples com funções.

Na criação V3:
* Uma função pode ser aplicada na entidade de aprendizado de máquina (pai).
* Uma função não pode ser aplicada a nenhuma subentidade.

Essa entidade é apenas um exemplo. Sua própria migração de entidade pode exigir outras considerações.

Considere uma entidade hierárquica v2 para modificar uma pizza `order` :
* onde cada filho determina um ingredientes original ou o topo de cima

Um exemplo de expressão para essa entidade é:

`Change the topping from mushrooms to olives`

A tabela a seguir demonstra a migração:

|Modelos v2|Modelos v3|
|--|--|
|Entidade pai-componente nomeada `Order`|Entidade pai-Machine-Learning chamada `Order`|
|Entidade filho-hierárquica com topo de pizza original e final|* Adicionar função a `Order` para cada ingredientes.|

## <a name="api-change-constraint-replaced-with-required-feature"></a>Restrição de alteração de API substituída pelo recurso necessário

Essa alteração foi feita em maio de 2020 na conferência//Build e só se aplica às APIs de criação v3 em que um aplicativo está usando um recurso restrito. Se você estiver migrando da criação v2 para a criação V3 ou não tiver usado recursos restritos v3, pule esta seção.

**Funcionalidade** – capacidade de exigir uma entidade existente como um recurso para outro modelo e apenas extrair esse modelo se a entidade for detectada. A funcionalidade não foi alterada, mas a API e a terminologia foram alteradas.

|Terminologia anterior|Nova terminologia|
|--|--|
|`constrained feature`<br>`constraint`<br>`instanceOf`|`required feature`<br>`isRequired`|

#### <a name="automatic-migration"></a>Migração automática

A partir de **junho de 19 2020**, você não terá permissão para criar restrições programaticamente usando a API de criação anterior que expô essa funcionalidade.

Todos os recursos de restrição existentes serão migrados automaticamente para o sinalizador de recurso necessário. Nenhuma alteração programática é necessária para sua API de previsão e nenhuma alteração resultante na qualidade da precisão da previsão.

#### <a name="luis-portal-changes"></a>Alterações do portal LUIS

O portal de visualização do LUIS referenciou essa funcionalidade como uma **restrição**. O portal LUIS atual designa essa funcionalidade como um **recurso necessário**.

#### <a name="previous-authoring-api"></a>API de criação anterior

Essa funcionalidade foi aplicada na API de criação de visualização **[criar entidade filho](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d86cf3c6a25a45529767d77)** como parte da definição de uma entidade, usando a `instanceOf` Propriedade do filho de uma entidade:

```json
{
    "name" : "dayOfWeek",
    "instanceOf": "datetimeV2",
    "children": [
        {
           "name": "dayNumber",
           "instanceOf": "number",
           "children": []
        }
    ]
}
```

#### <a name="new-authoring-api"></a>Nova API de criação

Essa funcionalidade agora é aplicada com a **[API adicionar relação de recursos de entidade](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d9dc1781e38aaec1c375f26)** usando as `featureName` `isRequired` Propriedades e. O valor da `featureName` propriedade é o nome do modelo.

```json
{
    "featureName": "YOUR-MODEL-NAME-HERE",
    "isRequired" : true
}
```


## <a name="next-steps"></a>Próximas etapas

* [Recursos para desenvolvedores](developer-reference-resource.md)
