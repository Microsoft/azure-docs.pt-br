---
title: Limites de recursos e objetos do Azure Analysis Services | Microsoft Docs
description: Este artigo descreve os limites de recursos e objetos do servidor Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c2caab4e449f4299d00fff14b697887ec00f35e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83697499"
---
# <a name="analysis-services-resource-and-object-limits"></a>Limites de recurso e objeto do Analysis Services

Este artigo descreve os limites de objeto de recurso e modelo.

## <a name="tier-limits"></a>Limites de camada

Para obter limites de QPU e memória das camadas de desenvolvedor de nível básico e padrão, consulte a [página de preços do Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="object-limits"></a>Limites do objeto

Esses limites são teóricos. Desempenho será ser diminuído em números mais baixos.

|Objeto|Tamanho máximo/números|  
|------------|----------------------------|  
|Bancos de dados em uma instância|16.000|  
|Número combinado de tabelas e colunas em um banco de dados|16.000|  
|Linhas em uma tabela|Ilimitado<br /><br /> **Aviso:** Com a restrição de que nenhuma coluna única na tabela pode ter mais de 1.999.999.997 valores distintos.|  
|Hierarquias em uma tabela|15,999|  
|Níveis em uma hierarquia|15,999|  
|Relações|8,000|  
|Colunas de chave na tabela de todos os|15,999|  
|Medidas em tabelas|2 ^ 31-1 = 2.147.483.647|  
|Células retornadas por uma consulta|2 ^ 31-1 = 2.147.483.647|  
|Tamanho do registro da consulta de origem|64 k|  
|Comprimento de nomes de objeto|512 caracteres|  


