---
title: Otimizar o tempo de consulta usando a estratégia de armazenamento de tabela do sistema no banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve como otimizar o tempo de consulta com a estratégia de armazenamento da tabela do sistema em um banco de dados do Azure para PostgreSQL-servidor único.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 3f1fa0affb821b00d4f5529841533e854e634377
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86116175"
---
# <a name="optimize-query-time-with-the-toast-table-storage-strategy"></a>Otimizar o tempo de consulta com a estratégia de armazenamento de tabela TOAST 
Este artigo descreve como otimizar os tempos de consulta com a estratégia de armazenamento de tabela TOAST (técnica de armazenamento de atributo de tamanho grande).

## <a name="toast-table-storage-strategies"></a>Estratégias de armazenamento de tabelas TOAST
Quatro estratégias diferentes são usadas para armazenar colunas em disco que podem usar o TOAST. Elas representam várias combinações entre a compactação e o armazenamento fora de linha. A estratégia pode ser definida no nível do tipo de dados e no nível da coluna.
- **Sem Formatação** impede a compactação ou o armazenamento fora de linha. Ela desabilita o uso de cabeçalhos de byte único para tipos varlena. Sem Formatação é a única estratégia possível para colunas de tipos de dados que não podem usar TOAST.
- **Estendido** permite compactação e armazenamento fora de linha. Estendido é o padrão para a maioria dos tipos de dados que podem usar TOAST. Ocorre uma tentativa de compactação primeiro. Ocorrerá uma tentativa de armazenamento fora de linha se a linha ainda for muito grande.
- **External** permite armazenamento fora de linha, mas não compactação. Uso de Externa realiza operações de subcadeia de caracteres em texto amplo e em colunas bytea mais rápido. Essa velocidade é fornecida às custas de um aumento no espaço de armazenamento. Essas operações são otimizadas para efetuar fetch apenas das partes necessárias do valor fora de linha quando ele não está compactado.
- **Principal** permite a compactação, mas não o armazenamento fora de linha. O armazenamento fora de linha ainda é realizado para essas colunas, mas apenas como último recurso. Ele ocorre quando não há outra maneira de tornar a linha pequena o suficiente para caber em uma página.

## <a name="use-toast-table-storage-strategies"></a>Usar estratégias de armazenamento de tabela TOAST
Se suas consultas acessarem tipos de dados que podem usar TOAST, considere usar a estratégia Principal, em vez da opção padrão Estendido para reduzir os tempos de consulta. A Principal não descarta o armazenamento fora de linha. Se suas consultas não acessam tipos de dados que podem usar TOAST, pode ser benéfico manter a opção Estendido. Uma parte maior das linhas da tabela principal cabe no cache do buffer compartilhado, que ajuda o desempenho.

Se você tiver uma carga de trabalho que usa um esquema com tabelas amplas e altas contagens de caracteres, considere usar as tabelas TOAST do PostgreSQL. Um exemplo de tabela de clientes tinha mais de 350 colunas com várias colunas que abrangiam 255 caracteres. Após sua conversão na estratégia Principal da tabela TOAST, o tempo de consulta de benchmarks foi reduzido de 4203 segundos para 467 segundos. É uma melhoria de 89%.

## <a name="next-steps"></a>Próximas etapas
Analise sua carga de trabalho quanto às características anteriores. 

Examine a seguinte documentação do PostgreSQL: 
- [Capítulo 68, Armazenamento físico de banco de dados](https://www.postgresql.org/docs/current/storage-toast.html) 