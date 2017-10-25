---
title: "CLI do Azure Service Fabric - repositório sfctl | Microsoft Docs"
description: "Descreve os comandos do repositório sfctl da CLI do Service Fabric."
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/22/2017
ms.author: ryanwi
ms.openlocfilehash: 2af6dff4ffcdf295731f2d61b5f9e35af40615e5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-store"></a>repositório sfctl
Execute operações de arquivo de nível básico no repositório de imagens do cluster.

## <a name="commands"></a>Comandos

|Command|Descrição|
| --- | --- |
|    excluir| Exclui o conteúdo do repositório de imagens existente.|
|    root-info| Obtém as informações de conteúdo na raiz do repositório de imagens.|
|    stat  | Obtém as informações de conteúdo do repositório de imagens.|


## <a name="sfctl-store-delete"></a>sfctl store delete
Exclui o conteúdo do repositório de imagens existente.

Exclui o conteúdo do repositório de imagens existente encontrado em determinado caminho relativo do repositório de imagens. Este comando pode ser usado para excluir pacotes de aplicativos carregados quando eles são provisionados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --content-path [Obrigatório]| Caminho relativo para o arquivo ou pasta no repositório de imagens de sua raiz.|
| --timeout -t          | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug               | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h             | Mostrar esta mensagem de ajuda e sair.|
| --output -o           | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão:           json.|
| --query               | Cadeia de caracteres de consulta JMESPath. Para saber mais e obter exemplos, consulte http://jmespath.org/.|
| --verbose             | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-store-stat"></a>sfctl store stat
Obtém as informações de conteúdo do repositório de imagens.

Retorna as informações sobre o conteúdo do repositório de imagens no contentPath especificado em relação à raiz do repositório de imagens.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --content-path [Obrigatório]| Caminho relativo para o arquivo ou pasta no repositório de imagens de sua raiz.|
| --timeout -t          | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug               | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h             | Mostrar esta mensagem de ajuda e sair.|
| --output -o           | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão:           json.|
| --query               | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose             | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="next-steps"></a>Próximas etapas
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).