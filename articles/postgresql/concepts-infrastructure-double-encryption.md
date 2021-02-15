---
title: Criptografia dupla de infraestrutura-banco de dados do Azure para PostgreSQL
description: Saiba mais sobre como usar a criptografia dupla de infraestrutura para adicionar uma segunda camada de criptografia com chaves gerenciadas por serviço.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 6/30/2020
ms.openlocfilehash: 83635b732318a4ada76d1d71c1ce419cae8b35e9
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630131"
---
# <a name="azure-database-for-postgresql-infrastructure-double-encryption"></a>Criptografia dupla da infraestrutura do banco de dados do Azure para PostgreSQL

O banco de dados do Azure para PostgreSQL usa a [criptografia de armazenamento de data em repouso](concepts-security.md#at-rest) para dados usando chaves gerenciadas da Microsoft. Os dados, incluindo backups, são criptografados no disco e essa criptografia está sempre ativa e não pode ser desabilitada. A criptografia usa o módulo de criptografia do FIPS 140-2 validado e uma codificação de 256 de bits do AES para a criptografia de armazenamento do Azure.

A criptografia dupla de infraestrutura adiciona uma segunda camada de criptografia usando chaves gerenciadas pelo serviço. Ele usa o módulo de criptografia validado pelo FIPS 140-2, mas com um algoritmo de criptografia diferente. Isso fornece uma camada adicional de proteção para seus dados em repouso. A chave usada na criptografia dupla de infraestrutura também é gerenciada pelo serviço banco de dados do Azure para PostgreSQL. A criptografia dupla de infraestrutura não é habilitada por padrão, pois a camada adicional de criptografia pode ter um impacto no desempenho.

> [!NOTE]
> Este recurso só tem suporte para os tipos de preço "Uso Geral" e "memória otimizada" no banco de dados do Azure para PostgreSQL.

A criptografia da camada de infraestrutura tem a vantagem de ser implementada na camada mais próxima do dispositivo de armazenamento ou dos cabos de rede. O banco de dados do Azure para PostgreSQL implementa as duas camadas de criptografia usando chaves gerenciadas pelo serviço. Embora ainda tecnicamente na camada de serviço, é muito próximo do hardware que armazena os dados em repouso. Opcionalmente, você ainda pode habilitar a criptografia de dados em repouso usando a [chave gerenciada pelo cliente](concepts-data-encryption-postgresql.md) para o servidor PostgreSQL provisionado.  

A implementação nas camadas de infraestrutura também dá suporte a uma diversidade de chaves. A infraestrutura deve estar ciente de diferentes clusters de máquinas e redes. Como tal, chaves diferentes são usadas para minimizar o raio de ataques de infraestrutura e uma variedade de falhas de hardware e rede. 

> [!NOTE]
> Usar a criptografia dupla de infraestrutura terá impacto no desempenho no banco de dados do Azure para o servidor PostgreSQL devido ao processo de criptografia adicional.

## <a name="benefits"></a>Benefícios

A criptografia dupla de infraestrutura para o banco de dados do Azure para PostgreSQL oferece os seguintes benefícios:

1. **Diversidade adicional de implementação de criptografia** – a mudança planejada para a criptografia baseada em hardware irá mais diversificar as implementações fornecendo uma implementação baseada em hardware além da implementação baseada em software.
2. **Erros de implementação** -duas camadas de criptografia na camada de infraestrutura protege contra erros no cache ou no gerenciamento de memória em camadas mais altas que expõem dados em texto sem formatação. Além disso, as duas camadas também garantem erros na implementação da criptografia em geral.

A combinação delas fornece proteção forte contra ameaças comuns e pontos fracos usados para a criptografia de ataques.

## <a name="supported-scenarios-with-infrastructure-double-encryption"></a>Cenários com suporte com criptografia dupla de infraestrutura

Os recursos de criptografia fornecidos pelo banco de dados do Azure para PostgreSQL podem ser usados juntos. Veja abaixo um resumo dos vários cenários que você pode usar:

|  ##   | Criptografia padrão | Criptografia dupla de infraestrutura | Criptografia de dados usando chaves gerenciadas pelo cliente  |
|:------|:------------------:|:--------------------------------:|:--------------------------------------------:|
| 1     | *Sim*              | *Não*                             | *Não*                                         |
| 2     | *Sim*              | *Sim*                            | *Não*                                         |
| 3     | *Sim*              | *Não*                             | *Sim*                                        |
| 4     | *Sim*              | *Sim*                            | *Sim*                                        |
|       |                    |                                  |                                              |

> [!Important]
> - O cenário 2 e 4 terá impacto no desempenho no banco de dados do Azure para o servidor PostgreSQL devido à camada adicional de criptografia de infraestrutura.
> - A configuração da criptografia dupla de infraestrutura para o banco de dados do Azure para PostgreSQL só é permitida durante a criação do servidor. Depois que o servidor for provisionado, você não poderá alterar a criptografia de armazenamento. No entanto, você ainda pode habilitar a criptografia de dados usando chaves gerenciadas pelo cliente para o servidor criado com/sem criptografia dupla de infraestrutura.

## <a name="limitations"></a>Limitações

Para o banco de dados do Azure para PostgreSQL, o suporte para criptografia dupla de infraestrutura usando chave gerenciada por serviço tem as seguintes limitações:

* O suporte para essa funcionalidade é limitado a tipos de preço **uso geral** e com **otimização de memória** .
* Esse recurso só tem suporte em regiões e servidores, que dão suporte a armazenamento de até 16 TB. Para obter a lista de regiões do Azure que dão suporte a armazenamento de até 16 TB, consulte a [documentação de armazenamento](concepts-pricing-tiers.md#storage).

    > [!NOTE]
    > - Todos os **novos** Servidores PostgreSQL criados nas regiões listadas acima também dão suporte à criptografia de dados com as chaves do Gerenciador de clientes. Nesse caso, os servidores criados por meio de PITR (restauração pontual) ou réplicas de leitura não se qualificam como "novo".
    > - Para validar se o servidor provisionado dá suporte a até 16 TB, você pode ir para a folha tipo de preço no portal e ver se o controle deslizante de armazenamento pode ser movido para até 16 TB. Se você só pode mover o controle deslizante até 4 TB, o servidor pode não dar suporte à criptografia com chaves gerenciadas pelo cliente; no entanto, os dados são criptografados usando chaves gerenciadas pelo serviço em todos os momentos. Entre em contato com AskAzureDBforPostgreSQL@service.microsoft.com se você tiver alguma dúvida.

## <a name="next-steps"></a>Próximas etapas

Saiba como [Configurar a criptografia dupla de infraestrutura para o banco de dados do Azure para PostgreSQL](howto-double-encryption.md).
