---
title: Atualizações de esquema de 1º de junho de 2016 - Aplicativos Lógicos do Azure | Microsoft Docs
description: Versão de esquema 2016-06-01 atualizada para definições de aplicativo lógico nos Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 349d57e8-f62b-4ec6-a92f-a6e0242d6c0e
ms.topic: article
ms.date: 07/25/2016
ms.openlocfilehash: 0558c309cc22f39c2ed439b7930443ca0adb071e
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385382"
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Atualizações de esquema para Aplicativos Lógicos do Azure - 1º de junho de 2016

O [esquema atualizado](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json) e a versão da API para Aplicativos Lógicos do Azure incluem os principais aprimoramentos que tornam a lógica de aplicativos mais confiável e fácil de usar:

* [Escopos](#scopes) permitem agrupar ou aninhar ações como uma coleção de ações.
* [Condições e loops](#conditions-loops) agora são ações de primeira classe.
* Ordenação mais precisa para executar ações com a propriedade `runAfter`, substituindo `dependsOn`

Para atualizar os aplicativos lógicos do esquema da visualização de 1º de agosto de 2015 para o esquema de 1º de junho de 2016, [confira a seção sobre atualização](#upgrade-your-schema).

<a name="scopes"></a>

## <a name="scopes"></a>Escopos

Esse esquema inclui escopos, que permitem agrupar ações ou aninhar ações umas dentro das outras. Por exemplo, uma condição pode conter outra condição. Saiba mais sobre a [sintaxe de escopo](../logic-apps/logic-apps-loops-and-scopes.md) ou examine este exemplo básico de escopo:

```json
{
   "actions": {
      "Scope": {
         "type": "Scope",
         "actions": {                
            "Http": {
               "inputs": {
                   "method": "GET",
                   "uri": "https://www.bing.com"
               },
               "runAfter": {},
               "type": "Http"
            }
         }
      }
   }
}
```

<a name="conditions-loops"></a>

## <a name="conditions-and-loops-changes"></a>Alterações de condições e loops

Em versões de esquema anteriores, condições e loops eram parâmetros associados a uma única ação. Esse esquema elimina essa limitação, portanto, as condições e os loops agora estão disponíveis como tipos de ação. Saiba mais sobre [ciclos e escopos](../logic-apps/logic-apps-loops-and-scopes.md), [condições](../logic-apps/logic-apps-control-flow-conditional-statement.md), ou analise este exemplo básico que mostra uma ação de condição:

```json
{
   "Condition - If trigger is some trigger": {
      "type": "If",
      "expression": "@equals(triggerBody(), '<trigger-name>')",
      "runAfter": {},
      "actions": {
         "Http_2": {
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com"
            },
            "runAfter": {},
            "type": "Http"
         }
      },
      "else": 
      {
         "Condition - If trigger is another trigger": {}
      }  
   }
}
```

<a name="run-after"></a>

## <a name="runafter-property"></a>Propriedade 'runAfter'

A propriedade `runAfter` substitui `dependsOn`, fornecendo mais precisão quando você especifica a ordem de execução de ações com base no status das ações anteriores. A propriedade `dependsOn` indicou se "a ação foi executada e se teve êxito", com base em se a ação anterior foi executada com êxito, se falhou ou foi ignorada - não no número de vezes que você desejou executar a ação. A propriedade `runAfter` fornece flexibilidade como um objeto que especifica todos os nomes de ação após os quais o objeto é executado. Essa propriedade também define uma matriz de status aceitável como disparadores. Por exemplo, se você quiser que uma ação seja executada após a ação A for executada com êxito, e também após a ação B ter êxito ou falhar, configure esta propriedade `runAfter`:

```json
{
   // Other parts in action definition
   "runAfter": {
      "A": ["Succeeded"],
      "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>Atualizar o esquema

Para atualizar para o [esquema mais recente](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json), você só precisa executar algumas etapas. O processo de atualização inclui a execução do script de atualização, salvando como um novo aplicativo lógico e, se você desejar, possivelmente substituindo o aplicativo lógico anterior.

1. No portal do Azure, abra o aplicativo lógico.

2. Acesse **Visão geral**. Na barra de aplicativo lógico, escolha **Atualizar Esquema**.
   
   ![Escolher o Esquema de Atualização][1]
   
   É retornada a definição atualizada, que você pode copiar e colar em uma definição de recurso, se necessário. 

   > [!IMPORTANT]
   > *Certifique-se de*  escolher **Salvar Como** para que todas as referências de conexão permaneçam válidas no aplicativo lógico atualizado.

3. Na barra de ferramentas da folha de atualização, escolha **Salvar como**.

4. Insira o nome lógico e o status. Para implantar o aplicativo lógico atualizado, escolha **Criar**.

5. Confirme se o aplicativo lógico atualizado funciona conforme o esperado.
   
   > [!NOTE]
   > Se você estiver usando um gatilho manual ou de solicitação, a URL de retorno de chamada será alterada em seu novo aplicativo lógico. Teste a nova URL para verificar se a experiência de ponta a ponta funciona. Para preservar URLs anteriores, você pode clonar sobre o aplicativo lógico existente.

6. *Opcional* Para substituir o aplicativo lógico anterior pela nova versão do esquema, na barra de ferramentas, escolha **Clonar**, ao lado de **Atualizar Esquema**. Essa etapa será necessária apenas se você quiser manter a mesma ID de recurso ou URL de gatilho de solicitação do aplicativo lógico.

## <a name="upgrade-tool-notes"></a>Observações sobre a ferramenta de atualização

### <a name="mapping-conditions"></a>Condições de mapeamento

Na definição atualizada, a ferramenta se esforça para agrupar ações de ramificação verdadeiras e falsas como um escopo. Especificamente, o padrão de designer de `@equals(actions('a').status, 'Skipped')` aparece como uma ação `else`. No entanto, se a ferramenta detectar padrões irreconhecíveis, ela poderá criar condições separadas para as ramificações verdadeiras e falsas. É possível remapear ações após a atualização, se necessário.

#### <a name="foreach-loop-with-condition"></a>loop 'foreach' com condição

No novo esquema, é possível usar a ação de filtro para replicar o padrão que usa um loop **Para cada** com uma condição por item. No entanto, a mudança acontece automaticamente quando você atualiza. A condição se torna uma ação de filtro que aparece antes do loop **ForEach**, retornando apenas uma matriz de itens que corresponde à condição e passando essa matriz para ação **ForEach**. Para obter um exemplo, confira [Loops e escopos](../logic-apps/logic-apps-loops-and-scopes.md).

### <a name="resource-tags"></a>Marcações de recursos

Após a atualização, as marcas de recurso são removidas. Portanto, você deve redefini-las para o fluxo de trabalho atualizado.

## <a name="other-changes"></a>Outras alterações

### <a name="renamed-manual-trigger-to-request-trigger"></a>Renomeado como gatilho 'manual' para disparar a 'solicitação'

O tipo de disparador `manual` foi preterido e foi renomeado como `request` com o tipo `http`. Essa alteração cria mais consistência para o tipo de padrão usado para criar o disparador.

### <a name="new-filter-action"></a>Nova ação 'filter'

Para filtrar uma matriz grande para um conjunto menor de itens, o novo tipo `filter` aceita uma matriz e uma condição, avalia a condição para cada item e retorna uma matriz com os itens que atendem à condição.

### <a name="restrictions-for-foreach-and-until-actions"></a>Restrições às ações 'foreach' e 'until'

Os loops `foreach` e `until` são restritos a uma única ação.

### <a name="new-trackedproperties-for-actions"></a>Novo 'trackedProperties' para ações

Agora as ações podem ter uma propriedade adicional chamada `trackedProperties`, que é irmã das propriedades `runAfter` e `type`. Esse objeto especifica determinadas entradas de ação ou saídas que você deseja incluir na telemetria de Diagnóstico do Azure, emitida como parte de um fluxo de trabalho. Por exemplo:

``` json
{
   "Http": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com"
      },
      "runAfter": {},
      "type": "Http",
      "trackedProperties": {
         "responseCode": "@action().outputs.statusCode",
         "uri": "@action().inputs.uri"
      }
   }
}
```

## <a name="next-steps"></a>Próximas etapas

* [Criar definições de fluxo de trabalho para aplicativos lógicos](../logic-apps/logic-apps-author-definitions.md)
* [Automatizar a implantação do aplicativo lógico](logic-apps-azure-resource-manager-templates-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
