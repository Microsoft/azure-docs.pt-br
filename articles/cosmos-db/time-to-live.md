---
title: Expirar dados no Azure Cosmos DB com Vida Útil
description: Com a TTL, o Microsoft Azure Cosmos DB fornece a capacidade de limpar documentos automaticamente do sistema após determinado período.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 7a29e9446a8c3b703c2ec3140711f44f3c81535f
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467582"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Vida útil (TTL) no Azure Cosmos DB 

Com **a** vida útil ou TTL, Azure Cosmos DB fornece a capacidade de excluir itens automaticamente de um contêiner após um determinado período de tempo. Por padrão, é possível definir a Vida Útil no nível do contêiner e substituir o valor em uma base por item. Após definir a Vida Útil em um nível de item ou contêiner, o Azure Cosmos DB removerá automaticamente esses itens após o período de tempo, desde a hora em que foram modificados pela última vez. O valor de Vida Útil é configurado em segundos. Quando você configurar o TTL, o sistema excluirá automaticamente os itens expirados com base no valor TTL, sem a necessidade de uma operação de exclusão emitida explicitamente pelo aplicativo cliente.

## <a name="time-to-live-for-containers-and-items"></a>Vida Útil para contêineres e itens

O valor de vida útil é definido em segundos e é interpretado como um Delta desde o momento em que um item foi modificado pela última vez. É possível definir a Vida Útil em um contêiner ou um item dentro do contêiner:

1. **Vida Útil em um contêiner** (definir usando `DefaultTimeToLive`):

   - Se ausentes (ou definidos como nulo), os itens não serão expirados automaticamente.

   - Se presente e o valor for definido como "-1", ele será igual ao infinito e os itens não expirarão por padrão.

   - Se presente e o valor for definido como algum número *"n"* – os itens expirarão em *"n"* segundos após a hora da última modificação.

2. **Vida Útil em um item** (definir usando `ttl`):

   - Essa Propriedade será aplicável somente se `DefaultTimeToLive` estiver presente e não estiver definido como nulo para o contêiner pai.

   - Se presente, substituirá o valor `DefaultTimeToLive` do contêiner pai.

## <a name="time-to-live-configurations"></a>Configurações de Vida Útil

* Se TTL for definido como *"n"* em um contêiner, os itens nesse contêiner expirarão após *n* segundos.  Se houver itens no mesmo contêiner que tenham sua própria vida útil, defina como-1 (indicando que eles não expiram) ou se alguns itens tiverem substituído a configuração de vida útil com um número diferente, esses itens expirarão com base em seu próprio valor de TTL configurado. 

* Se a TTL não estiver definida em um contêiner, o tempo de vida de um item nesse contêiner não terá efeito. 

* Se a TTL em um contêiner for definida como -1, um item nesse contêiner que tenha a vida útil definida como n expirará após n segundos, e os itens restantes não expirarão. 

A exclusão de itens com base na TTL é gratuita. Não haverá custos adicionais (isto é, nenhuma RU adicional será consumida) quando o item for excluído como resultado da expiração de TTL.

## <a name="examples"></a>Exemplos

Esta seção mostra alguns exemplos com valores de vida útil diferentes atribuídos a contêineres e itens:

### <a name="example-1"></a>Exemplo 1

TTL no contêiner é definido como nulo (DefaultTimeToLive = NULL)

|TTL no item| Resultado|
|---|---|
|TTL = nulo|    O TTL está desabilitado. O item nunca expirará (padrão).|
|ttl = -1   |O TTL está desabilitado. O item nunca expirará.|
|ttl = 2000 |O TTL está desabilitado. O item nunca expirará.|


### <a name="example-2"></a>Exemplo 2

TTL no contêiner é definido como-1 (DefaultTimeToLive =-1)

|TTL no item| Resultado|
|---|---|
|TTL = nulo |O TTL está habilitado. O item nunca expirará (padrão).|
|ttl = -1   |O TTL está habilitado. O item nunca expirará.|
|ttl = 2000 |O TTL está habilitado. O item expirará após 2000 segundos.|


### <a name="example-3"></a>Exemplo 3:

TTL no contêiner é definido como 1000 (DefaultTimeToLive = 1000)

|TTL no item| Resultado|
|---|---|
|TTL = nulo|    O TTL está habilitado. O item expirará após 1000 segundos (padrão).|
|ttl = -1   |O TTL está habilitado. O item nunca expirará.|
|ttl = 2000 |O TTL está habilitado. O item expirará após 2000 segundos.|

## <a name="next-steps"></a>Próximas etapas

Saiba como configurar a vida útil nos seguintes artigos:

* [Como configurar a Vida Útil](how-to-time-to-live.md)
