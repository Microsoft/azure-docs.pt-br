---
title: Referência de importação e exportação de dados-QnA Maker
description: Use esta referência de importação e exportação para obter os melhores resultados para o backup, o armazenamento e a substituição da base de dados de conhecimento.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: d73fd6c7f49c10c8aca1060e91fc69b0e581738b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97707496"
---
# <a name="import-and-export-data-reference"></a>Importar e exportar referência de dados

Examine esta referência de importação e exportação para obter os melhores resultados para o backup, o armazenamento e a substituição da base de dados de conhecimento.

## <a name="import-and-export-knowledge-base"></a>Importar e exportar base de dados de conhecimento

Os **arquivos TSV e XLS**, das bases de dados de conhecimento exportadas, só podem ser usados pela importação dos arquivos da página **configurações** no portal de QnA Maker. Eles não podem ser usados como fontes de dados durante a criação da base de conhecimento ou no recurso **+ Adicionar arquivo** ou **+ Adicionar URL** na página **configurações** . 

Quando você importa a base de dados de conhecimento por meio desses **arquivos TSV e XLS**, os pares de QnA são adicionados à fonte editorial e não às fontes das quais os QnAs foram extraídos na base de dados de conhecimento exportada. 
