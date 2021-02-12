---
title: Gerenciar watchlists no Azure Sentinel usando a API REST | Microsoft Docs
description: Este artigo descreve como o Azure Sentinel watchlists pode ser gerenciado usando a API REST Log Analytics ' para criar, modificar e excluir watchlists e seus itens.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2021
ms.author: yelevin
ms.openlocfilehash: ea571f9b033ba82709a13c6d32649f3228ee04b1
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98798362"
---
# <a name="manage-watchlists-in-azure-sentinel-using-rest-api"></a>Gerenciar watchlists no Azure Sentinel usando a API REST

> [!IMPORTANT]
> O recurso watchlists está atualmente em **Visualização**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

O Azure Sentinel, que está sendo criado em parte no Azure Monitor Log Analytics, permite que você use Log Analytics API REST para gerenciar watchlists. Este documento mostra como criar, modificar e excluir watchlists e seus itens usando a API REST.  O Watchlists criado dessa maneira será exibido na interface do usuário do Azure Sentinel.

## <a name="common-uri-parameters"></a>Parâmetros de URI comuns

Estes são os parâmetros de URI comuns para todos os comandos da API Watchlist:

| Nome | Em | Obrigatório | Type | Descrição |
|-|-|-|-|-|
| **SubscriptionId** | path | yes | GUID | a ID da assinatura do Azure |
| **ResourceGroupName** | path | yes | string | o nome do grupo de recursos na assinatura |
| **WorkspaceName** | path | yes | string | o nome do espaço de trabalho Log Analytics |
| **{watchlistAlias}** | path | sim* | string | o nome de uma determinada forma de inspeção<br>\* Não é necessário ao recuperar todos os watchlists |
| **{watchlistItemId}** | path | Sim * * | GUID | a ID do item para criar, adicionar ou excluir da Watchlist<br>\*\* Necessário somente para comandos de item de Watchlist |
| **{API-Version}** | Consulta | yes | string | a versão do protocolo usado para fazer esta solicitação. A partir de 29 de outubro de 2020, a versão da API Watchlist é *2019-01-01-Preview* |
| **{bearerToken}** | autorização | yes | token | o token de autorização de portador |
|  

## <a name="retrieve-all-watchlists"></a>Recuperar todos os watchlists

Esse comando recupera todos os watchlists associados a um espaço de trabalho, sem seus itens.

### <a name="request-uri"></a>URI da solicitação
(O URI é uma única linha, dividida para facilitar a leitura)

| Método | URI da solicitação |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists?api-version={{api-version}}` |
|

### <a name="responses"></a>Respostas

| Código de status | Corpo da resposta | Descrição |
|-|-|-|
| 200/OK | Lista de watchlists existentes ou vazia, se nenhuma lista de observação for encontrada |  |
| 400/solicitação inadequada |  | Sintaxe de solicitação malformada, parâmetro de solicitação inválido... |
|

## <a name="look-up-a-watchlist-by-name"></a>Pesquisar uma inspeção por nome

Este comando recupera uma inspeção de observação específica associada a um espaço de trabalho, sem seus itens.

### <a name="request-uri"></a>URI da solicitação
(O URI é uma única linha, dividida para facilitar a leitura)

| Método | URI da solicitação |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Respostas

| Código de status | Corpo da resposta | Descrição |
|-|-|-|
| 200/OK | A inspeção de observação solicitada |  |
| 400/solicitação inadequada |  | Sintaxe de solicitação malformada, parâmetro de solicitação inválido... |
| 404/não encontrado |  | Nenhum observador encontrado com o nome solicitado |
|

## <a name="create-a-watchlist"></a>Criar uma inspeção

Este comando cria uma Watchlist e adiciona itens a ela. Ele usa duas chamadas para esse ponto de extremidade para criar a Watchlist e seus itens: o primeiro criará o Watchlist (pai) e o segundo adicionará os itens.

### <a name="request-uri"></a>URI da solicitação
(O URI é uma única linha, dividida para facilitar a leitura)

| Método | URI da solicitação |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>Corpo da solicitação

Aqui está um exemplo do corpo da solicitação de uma solicitação de criação de Watchlist:

```json
{
    "properties": {
        "displayName": "High Value Assets Watchlist",
        "source": "Local file",
        "provider": "Microsoft",
        "numberOfLinesToSkip":"1",
        "rawContent": "metadata line\nheader1, header2\nval1, val2",
        "contentType": "text/csv"
    }
}
```

### <a name="responses"></a>Respostas

| Código de status | Corpo da resposta | Descrição |
|-|-|-|
| 200/OK | A Watchlist criada pela solicitação, sem itens |  |
| 400/solicitação inadequada |  | Sintaxe de solicitação malformada, parâmetro de solicitação inválido... |
| 409/conflito |  | Falha na operação, existe uma Watchlist com o mesmo alias |
|

## <a name="delete-a-watchlist"></a>Excluir uma inspeção

Este comando exclui uma Watchlist e seus itens.

### <a name="request-uri"></a>URI da solicitação
(O URI é uma única linha, dividida para facilitar a leitura)

| Método | URI da solicitação |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Respostas

| Código de status | Corpo da resposta | Descrição |
|-|-|-|
| 200/OK | Corpo de resposta vazio |  |
| 204/nenhum conteúdo | Corpo de resposta vazio | Nada excluído |
| 400/solicitação inadequada |  | Sintaxe de solicitação malformada, parâmetro de solicitação inválido... |
|

## <a name="add-or-update-a-watchlist-item"></a>Adicionar ou atualizar um item Watchlist

Quando esse comando é executado em um *watchlisItemId* existente (um GUID), ele atualizará o item com os dados fornecidos no corpo da solicitação. Caso contrário, um novo item será criado.

### <a name="request-uri"></a>URI da solicitação
(O URI é uma única linha, dividida para facilitar a leitura)

| Método | URI da solicitação |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>Corpo da solicitação

Aqui está um exemplo do corpo da solicitação de uma solicitação de adição/atualização do item Watchlist:

```json
{
    "properties": {
      "itemsKeyValue": {
           "Gateway subnet": "10.0.255.224/27",
           "Web Tier": "10.0.1.0/24",
           "Business tier": "10.0.2.0/24",
           "Private DMZ in": "10.0.0.0/27",
           "Public DMZ out": "10.0.0.96/27"
      }
}
}
```

### <a name="responses"></a>Respostas

| Código de status | Corpo da resposta | Descrição |
|-|-|-|
| 200/OK | O item Watchlist criado ou atualizado pela solicitação |  |
| 400/solicitação inadequada |  | Sintaxe de solicitação malformada, parâmetro de solicitação inválido... |
| 409/conflito |  | Falha na operação, existe uma Watchlist com o mesmo alias |
|

## <a name="delete-a-watchlist-item"></a>Excluir um item de inspeção

Este comando exclui um item Watchlist existente.

### <a name="request-uri"></a>URI da solicitação
(O URI é uma única linha, dividida para facilitar a leitura)

| Método | URI da solicitação |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Respostas

| Código de status | Corpo da resposta | Descrição |
|-|-|-|
| 200/OK | Corpo de resposta vazio |  |
| 204/nenhum conteúdo | Corpo de resposta vazio | Nada excluído |
| 400/solicitação inadequada |  | Sintaxe de solicitação malformada, parâmetro de solicitação inválido... |
|

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a gerenciar o watchlists e seus itens no Azure Sentinel usando a API de Log Analytics. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba mais sobre o [watchlists](watchlists.md)
- Explorar outros usos da [API do Azure Sentinel](/rest/api/securityinsights/)