---
title: "Atualizações de esquema de 1º de junho de 2016 - Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Criar definições de JSON para Aplicativos Lógicos do Azure com a versão de esquema 2016-06-01"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 349d57e8-f62b-4ec6-a92f-a6e0242d6c0e
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/25/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 43df04d6478e44c82c88b17d916cfc9fe4afc03e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Atualizações de esquema para Aplicativos Lógicos do Azure - 1º de junho de 2016

Esse novo esquema e a versão da API para Aplicativos Lógicos do Azure incluem os principais aprimoramentos que tornam a lógica de aplicativos mais confiável e fácil de usar:

* [Escopos](#scopes) permitem agrupar ou aninhar ações como uma coleção de ações.
* [Condições e loops](#conditions-loops) agora são ações de primeira classe.
* Ordenação mais precisa para executar ações com a propriedade `runAfter`, substituindo `dependsOn`

Para atualizar os aplicativos lógicos do esquema da visualização de 1º de agosto de 2015 para o esquema de 1º de junho de 2016, [confira a seção sobre atualização](##upgrade-your-schema).

<a name="scopes"></a>
## <a name="scopes"></a>Escopos

Esse esquema inclui escopos, que permitem agrupar ações ou aninhar ações umas dentro das outras. Por exemplo, uma condição pode conter outra condição. Saiba mais sobre a [sintaxe de escopo](../logic-apps/logic-apps-loops-and-scopes.md) ou examine este exemplo básico de escopo:

```
{
    "actions": {
        "My_Scope": {
            "type": "scope",
            "actions": {                
                "Http": {
                    "inputs": {
                        "method": "GET",
                        "uri": "http://www.bing.com"
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

Em versões de esquema anteriores, condições e loops eram parâmetros associados a uma única ação. Esse esquema remove essa limitação; portanto, condições e loops agora aparecem como tipos de ação. Saiba mais sobre [loops e escopos](../logic-apps/logic-apps-loops-and-scopes.md) ou examine este exemplo básico de uma ação de condição:

```
{
    "If_trigger_is_some-trigger": {
        "type": "If",
        "expression": "@equals(triggerBody(), 'some-trigger')",
        "runAfter": { },
        "actions": {
            "Http_2": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.bing.com"
                },
                "runAfter": {},
                "type": "Http"
            }
        },
        "else": 
        {
            "if_trigger_is_another-trigger": "..."
        }      
    }
}
```

<a name="run-after"></a>
## <a name="runafter-property"></a>Propriedade 'runAfter'

A propriedade `runAfter` substitui `dependsOn`, fornecendo mais precisão quando você especifica a ordem de execução de ações com base no status das ações anteriores.

A propriedade `dependsOn` era sinônimo de "a ação foi executado e foi bem-sucedida", não importa quantas vezes você deseje executar uma ação dependendo de a ação anterior ter sido bem-sucedida, ter falhado ou ter sido ignorada. A propriedade `runAfter` fornece flexibilidade como um objeto que especifica todos os nomes de ação após os quais o objeto é executado. Essa propriedade também define uma matriz de status aceitável como disparadores. Por exemplo, se você quiser realizar a execução após A etapa ter êxito e também após B ter êxito ou falhar, construa esta propriedade `runAfter`:

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>Atualizar o esquema

A atualização para o novo esquema exige apenas algumas etapas. O processo de atualização inclui a execução do script de atualização, salvando como um novo aplicativo lógico e, se você desejar, possivelmente substituindo o aplicativo lógico anterior.

1. No portal do Azure, abra o aplicativo lógico.

2. Acesse **Visão geral**. Na barra de aplicativo lógico, escolha **Atualizar Esquema**.
   
    ![Escolher o Esquema de Atualização][1]
   
    É retornada a definição atualizada, que você pode copiar e colar em uma definição de recurso, se necessário. 
    No entanto, é **altamente recomendável** escolher **Salvar como** para garantir que todas as referências de conexão sejam válidas no aplicativo lógico atualizado.

3. Na barra de ferramentas da folha de atualização, escolha **Salvar como**.

4. Insira o nome lógico e o status. Para implantar o aplicativo lógico atualizado, escolha **Criar**.

5. Confirme se o aplicativo lógico atualizado funciona conforme o esperado.
   
   > [!NOTE]
   > Se você estiver usando um gatilho manual ou de solicitação, a URL de retorno de chamada será alterada em seu novo aplicativo lógico. Teste a nova URL para verificar se a experiência de ponta a ponta funciona. Para preservar URLs anteriores, você pode clonar sobre o aplicativo lógico existente.

6. *Opcional* Para substituir o aplicativo lógico anterior pela nova versão do esquema, na barra de ferramentas, escolha **Clonar**, ao lado de **Atualizar Esquema**. Essa etapa será necessária apenas se você quiser manter a mesma ID de recurso ou URL de gatilho de solicitação do aplicativo lógico.

### <a name="upgrade-tool-notes"></a>Observações sobre a ferramenta de atualização

#### <a name="mapping-conditions"></a>Condições de mapeamento

Na definição atualizada, a ferramenta se esforça para agrupar ações de ramificação verdadeiras e falsas como um escopo. Mais especificamente, o padrão de designer de `@equals(actions('a').status, 'Skipped')` deve aparecer como uma ação `else`. No entanto, se a ferramenta detectar padrões irreconhecíveis, ela poderá criar condições separadas para as ramificações verdadeiras e falsas. É possível remapear ações após a atualização, se necessário.

#### <a name="foreach-loop-with-condition"></a>loop 'foreach' com condição

No novo esquema, você pode usar a ação de filtro para replicar o padrão de um loop `foreach` com uma condição por item, mas essa alteração deve ocorrer automaticamente durante a atualização. A condição se torna uma ação de filtro antes do loop foreach para retornar apenas uma matriz de itens que correspondem à condição, e essa matriz é passada para a ação foreach. Para obter um exemplo, confira [Loops e escopos](../logic-apps/logic-apps-loops-and-scopes.md).

#### <a name="resource-tags"></a>Marcações de recursos

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

```
{                
    "Http": {
        "inputs": {
            "method": "GET",
            "uri": "http://www.bing.com"
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
* [Criar modelos de implantação para aplicativos lógicos](../logic-apps/logic-apps-create-deploy-template.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
