---
title: Diretrizes para design de tabela de armazenamento do Azure | Microsoft Docs
description: Entenda as diretrizes para criar seu serviço de tabela de armazenamento do Azure para dar suporte a operações de leitura e gravação com eficiência.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: f84707e454a8b1f5d5947478fe65108a142a9757
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88236311"
---
# <a name="guidelines-for-table-design"></a>Diretrizes de design da tabela

Criar tabelas para uso com o serviço de tabela de armazenamento do Azure é muito diferente das considerações de design de banco de dados relacional. Este artigo descreve as diretrizes para criar a solução de serviço de Tabela para ser eficiente em leitura e gravação.

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>Criar sua solução do serviço Tabela para ser eficiente em leitura

* ***Design para consulta em aplicativos com alta taxa de leitura.***  Quando você está criando tabelas, pense sobre as consultas (especialmente aquelas sensíveis a latência) que você executará antes de pensar em como atualizará as entidades. Isso normalmente resulta em uma solução eficiente e de alto desempenho.  
* ***Especifique PartitionKey e RowKey em suas consultas.*** *Consultas de ponto* como essas são as consultas de serviço Tabela mais eficientes.  
* ***Considere armazenar cópias duplicadas de entidades.***  O armazenamento de tabela é barato, portanto, considere armazenar a mesma entidade várias vezes (com chaves diferentes) para permitir consultas mais eficientes.  
* ***Considere a desnormalização de seus dados.***  O armazenamento de tabela é barato, então considere desnormalizar seus dados. Por exemplo, armazene entidades resumidas para que consultas a dados agregados só tenham de acessar uma única entidade.  
* ***Use valores de chave composta.*** As únicas chaves que você tem são **PartitionKey** e **RowKey**. Por exemplo, use valores de chave composta para habilitar caminhos alternativo com chave de acesso para entidades.  
* ***Use a projeção de consulta.*** Você pode reduzir a quantidade de dados transferidos pela rede por meio de consultas que selecionam apenas os campos necessários.  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>Criar sua solução do serviço Tabela para ser eficiente em gravação  

* ***Não crie partições ativas.*** Escolha chaves que permitam que você distribua suas solicitações por várias partições em qualquer momento.  
* ***Evite picos no tráfego.***  Suavize o tráfego por um período razoável de tempo e evite picos no tráfego.
* ***Não crie, necessariamente, uma tabela separada para cada tipo de entidade.*** Quando você precisar de transações atômicas nos tipos de entidade, pode armazenar esses vários tipos de entidade na mesma partição, na mesma tabela.
* ***Considere a produtividade máxima que deve ser atingida.***  Você deve estar ciente dos destinos de escalabilidade para o serviço Tabela e garantir que seu design não fará com que você os exceda.  

À medida que você ler este guia, verá exemplos que colocam todos esses princípios em prática. 

## <a name="next-steps"></a>Próximas etapas

- [Padrões de design de tabela](table-storage-design-patterns.md)
- [Design para consulta](table-storage-design-for-query.md)
- [Criptografar dados de tabela](table-storage-design-encrypt-data.md)
- [Design para modificação de dados](table-storage-design-for-modification.md)
