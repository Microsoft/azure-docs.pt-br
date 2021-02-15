---
title: Parâmetros de recurso de pastas de trabalho Azure Monitor
description: Saiba como usar parâmetros de recurso para permitir a separação de recursos em pastas de trabalho. Use os parâmetros de recurso para definir o escopo do qual obter os dados.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 27e98ae51cbea654ca0f06979b289a497a5aedf3
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143516"
---
# <a name="workbook-resource-parameters"></a>Parâmetros de recurso da pasta de trabalho

Os parâmetros de recurso permitem a separação de recursos em pastas de trabalho. Isso é útil ao definir o escopo do qual obter os dados. Um exemplo é permitir que os usuários selecionem o conjunto de VMs, que os gráficos mais tarde usarão ao apresentar os dados.

Os valores dos seletores de recursos podem vir do contexto da pasta de trabalho, da lista estática ou de consultas do grafo de recursos do Azure.

## <a name="creating-a-resource-parameter-workbook-resources"></a>Criando um parâmetro de recurso (recursos de pasta de trabalho)
1. Comece com uma pasta de trabalho vazia no modo de edição.
2. Escolha _adicionar parâmetros_ nos links na pasta de trabalho.
3. Clique no botão azul _Adicionar parâmetro_ .
4. No novo painel de parâmetros que aparece, digite:
    1. Nome do parâmetro: `Applications`
    2. Tipo de parâmetro: `Resource picker`
    3. Necessário: `checked`
    4. Permitir várias seleções: `checked`
5. Obter dados de: `Workbook Resources`
6. Incluir somente tipos de recurso: `Application Insights`
7. Escolha ' salvar ' na barra de ferramentas para criar o parâmetro.

![Imagem mostrando a criação de um parâmetro de recurso usando recursos de pasta de trabalho](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>Criando um parâmetro de recurso (grafo de recursos do Azure)
1. Comece com uma pasta de trabalho vazia no modo de edição.
2. Escolha _adicionar parâmetros_ nos links na pasta de trabalho.
3. Clique no botão azul _Adicionar parâmetro_ .
4. No novo painel de parâmetros que aparece, digite:
    1. Nome do parâmetro: `Applications`
    2. Tipo de parâmetro: `Resource picker`
    3. Necessário: `checked`
    4. Permitir várias seleções: `checked`
5. Obter dados de: `Query`
    1. Tipo de consulta: `Azure Resource Graph`
    2. Assinaturas `Use default subscriptions`
    3. No controle de consulta, adicione este trecho
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. Escolha ' salvar ' na barra de ferramentas para criar o parâmetro.

![Imagem mostrando a criação de um parâmetro de recurso usando o grafo de recursos do Azure](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> O grafo de recursos do Azure ainda não está disponível em todas as nuvens. Certifique-se de que haja suporte na sua nuvem de destino se você escolher essa abordagem.

[Documentação do Azure Resource Graph](../../governance/resource-graph/overview.md)

## <a name="creating-a-resource-parameter--json-list"></a>Criando um parâmetro de recurso (lista JSON)
1. Comece com uma pasta de trabalho vazia no modo de edição.
2. Escolha _adicionar parâmetros_ nos links na pasta de trabalho.
3. Clique no botão azul _Adicionar parâmetro_ .
4. No novo painel de parâmetros que aparece, digite:
    1. Nome do parâmetro: `Applications`
    2. Tipo de parâmetro: `Resource picker`
    3. Necessário: `checked`
    4. Permitir várias seleções: `checked`
5. Obter dados de: `JSON`
    1. No controle de conteúdo, adicione este trecho de JSON
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. Pressione o botão de _atualização_ azul.
6. Opcionalmente, defina `Include only resource types` como _Application insights_
7. Escolha ' salvar ' na barra de ferramentas para criar o parâmetro.

## <a name="referencing-a-resource-parameter"></a>Referenciando um parâmetro de recurso
1. Adicione um controle de consulta à pasta de trabalho e selecione um recurso de Application Insights.
2. Use a lista suspensa _Application insights_ para associar o parâmetro ao controle. Fazer isso define o escopo da consulta para os recursos retornados pelo parâmetro em tempo de execução.
4. No controle KQL, adicione este trecho
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | order by Requests desc
    ```
5. Execute a consulta para ver os resultados. 

![Imagem mostrando um parâmetro de recurso referenciado em um controle de consulta](./media/workbooks-resources/resource-reference.png)

> Essa abordagem pode ser usada para associar recursos a outros controles, como métricas.

## <a name="resource-parameter-options"></a>Opções de parâmetro de recurso
| Parâmetro | Explicação | Exemplo |
| ------------- |:-------------|:-------------|
| `{Applications}` | A ID de recurso selecionada | _/subscriptions/<subid>/resourceGroups/<recurso-grupo>/Providers/<tipo de recurso>/acmeauthentication_ |
| `{Applications:label}` | O rótulo do recurso selecionado | `acmefrontend` |
| `{Applications:value}` | O valor do recurso selecionado | _'/subscriptions/<subid>/resourceGroups/<recurso-grupo>/Providers/<tipo de recurso>/acmeauthentication '_ |
| `{Applications:name}` | O nome do recurso selecionado | `acmefrontend` |
| `{Applications:resourceGroup}` | O grupo de recursos do recurso selecionado | `acmegroup` |
| `{Applications:resourceType}` | O tipo do recurso selecionado | _Microsoft. insights/Components_ |
| `{Applications:subscription}` | A assinatura do recurso selecionado |  |
| `{Applications:grid}` | Uma grade que mostra as propriedades do recurso. Útil para renderizar em um bloco de texto durante a depuração  |  |

## <a name="next-steps"></a>Próximas etapas

* [Comece a aprender mais](./workbooks-overview.md#visualizations) sobre pastas de trabalho muitas opções de visualizações ricas.
* [Controle](workbooks-access-control.md) e compartilhe o acesso aos recursos da pasta de trabalho.