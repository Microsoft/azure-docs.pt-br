---
title: Limites de recursos e objetos do Azure Analysis Services | Microsoft Docs
description: Descreve os limites de recursos e objetos do Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0ffbffc788baaffd4a0532c3918ed82cc3eaf5c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023530"
---
# <a name="analysis-services-resource-and-object-limits"></a>Limites de recurso e objeto do Analysis Services

Este artigo descreve os limites de objeto de recurso e modelo.

## <a name="tier-limits"></a>Limites de camada

### <a name="developer-tier"></a>Camada de Desenvolvedor

Essa camada é recomendada para cenários de avaliação, desenvolvimento e teste. Um único plano inclui a mesma funcionalidade da camada Standard, mas é limitado em capacidade de processamento, QPUs e tamanho de memória. A expansão de réplica de consulta não está disponível para essa camada. Essa camada não oferece um SLA.

|Plano  |QPUs  |Memória (GB)  |
|---------|---------|---------|
|D1    |    20     |    3     |


### <a name="basic-tier"></a>Camada básica

O nível é recomendado para soluções de produção com modelos tabulares menores, concorrência limitada do usuário e requisitos simples de atualização de dados. A expansão de réplica de consulta *não está disponível* para essa camada. Não há suporte para perspectivas, múltiplas partições e recursos de modelo tabular do DirectQuery nessa camada.  

|Plano  |QPUs  |Memória (GB)  |
|---------|---------|---------|
|B1    |    40     |    10     |
|B2    |    80     |    20     |

### <a name="standard-tier"></a>Camada padrão

Essa camada é para aplicativos de produção críticos, que exigem simultaneidade elástica do usuário e têm modelos de dados em crescimento acelerado. Ele dá suporte à atualização de dados avançada para atualizações de modelos de dados quase em tempo real e a todos os recursos de modelagem tabular.

|Plano  |QPUs  |Memória (GB)  |
|---------|---------|---------|
|S1    |    40     |    10     |
|S2    |    100     |    25     |
|S3    |    200     |    50     |
|S4    |    400     |    100     |
|S8*    |    320     |    200     |
|S9*    |    640    |    400     |

\* Não disponível em todas as regiões.  

## <a name="object-limits"></a>Limites do objeto

Esses limites são teóricos. Desempenho será ser diminuído em números mais baixos.

|Object|Tamanho máximo/números|  
|------------|----------------------------|  
|Bancos de dados em uma instância|16.000|  
|Número combinado de tabelas e colunas em um banco de dados|16.000|  
|Linhas em uma tabela|Ilimitado<br /><br /> **Aviso:** Com a restrição de que nenhuma coluna única na tabela pode ter mais de 1.999.999.997 valores distintos.|  
|Hierarquias em uma tabela|15,999|  
|Níveis em uma hierarquia|15,999|  
|Relações|8.000|  
|Colunas de chave na tabela de todos os|15,999|  
|Medidas em tabelas|2 ^ 31-1 = 2.147.483.647|  
|Células retornadas por uma consulta|2 ^ 31-1 = 2.147.483.647|  
|Tamanho do registro de consulta de fonte|64 k|  
|Comprimento de nomes de objeto|512 caracteres|  


