---
title: Expirar dados no Azure Cosmos DB com Vida Útil
description: Com a TTL, o Microsoft Azure Cosmos DB fornece a capacidade de limpar documentos automaticamente do sistema após determinado período.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 27540c3dfce73788e01f0f8ab0892c733f153fdf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60728994"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>A vida útil (TTL) no Azure Cosmos DB 

Com o **tempo de vida** ou TTL, o Azure Cosmos DB fornece a capacidade de excluir itens automaticamente de um contêiner após um período de tempo determinado. Por padrão, é possível definir a Vida Útil no nível do contêiner e substituir o valor em uma base por item. Após definir a Vida Útil em um nível de item ou contêiner, o Azure Cosmos DB removerá automaticamente esses itens após o período de tempo, desde a hora em que foram modificados pela última vez. O valor de Vida Útil é configurado em segundos. Quando você configura o TTL, o sistema excluirá automaticamente os itens expirados com base no valor de TTL, sem a necessidade de uma operação de exclusão explicitamente é emitida pelo aplicativo cliente.

## <a name="time-to-live-for-containers-and-items"></a>Vida Útil para contêineres e itens

O valor tempo de vida é definido em segundos, e ele será interpretado como um delta do momento em que um item foi modificado pela última vez. É possível definir a Vida Útil em um contêiner ou um item dentro do contêiner:

1. **Vida Útil em um contêiner** (definir usando `DefaultTimeToLive`):

   - Se ausentes (ou definidos como nulo), os itens não serão expirados automaticamente.

   - Se estiverem presentes e o valor for definido como "-1", ele é igual ao infinito e itens não expirarão por padrão.

   - Se estiverem presentes e o valor for definido como um número *"n"* – itens expiram *"n"* segundos após a hora de modificação de seu último.

2. **Vida Útil em um item** (definir usando `ttl`):

   - Essa Propriedade será aplicável somente se `DefaultTimeToLive` estiver presente e não estiver definido como nulo para o contêiner pai.

   - Se presente, substituirá o valor `DefaultTimeToLive` do contêiner pai.

## <a name="time-to-live-configurations"></a>Configurações de Vida Útil

* Se o TTL é definido como *"n"* em um contêiner, em seguida, os itens nesse contêiner expirarão após *n* segundos.  Se houver itens no mesmo contêiner que tem seu próprio tempo para live, defina como -1 (indicando que não ocorra) ou se alguns itens tem substituído o tempo de vida de configuração com um número diferente, esses itens expiram com base em seu próprio valor TTL configurado. 

* Se a TTL não estiver definida em um contêiner, o tempo de vida de um item nesse contêiner não terá efeito. 

* Se a TTL em um contêiner for definida como -1, um item nesse contêiner que tenha a vida útil definida como n expirará após n segundos, e os itens restantes não expirarão. 

A exclusão de itens com base na TTL é gratuita. Não haverá custos adicionais (isto é, nenhuma RU adicional será consumida) quando o item for excluído como resultado da expiração de TTL.

## <a name="next-steps"></a>Próximas etapas

Saiba como configurar a vida útil nos seguintes artigos:

* [Como configurar a Vida Útil](how-to-time-to-live.md)
