---
title: Criptografia dupla de infraestrutura-banco de dados do Azure para MySQL
description: Saiba mais sobre como usar a criptografia dupla de infraestrutura para adicionar uma segunda camada de criptografia com chaves gerenciadas de serviço.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 6/30/2020
ms.openlocfilehash: e9182a2a0b88f85af5305f5794fec2ffe7935701
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631726"
---
# <a name="azure-database-for-mysql-infrastructure-double-encryption"></a>Criptografia dupla da infraestrutura do banco de dados do Azure para MySQL

O banco de dados do Azure para MySQL usa a [criptografia de armazenamento de data em repouso](concepts-security.md#at-rest) para dados usando chaves gerenciadas da Microsoft. Os dados, incluindo backups, são criptografados no disco e essa criptografia está sempre ativa e não pode ser desabilitada. A criptografia usa o módulo de criptografia do FIPS 140-2 validado e uma codificação de 256 de bits do AES para a criptografia de armazenamento do Azure.

A criptografia dupla de infraestrutura adiciona uma segunda camada de criptografia usando chaves gerenciadas pelo serviço. Ele usa o módulo de criptografia validado pelo FIPS 140-2, mas com um algoritmo de criptografia diferente. Isso fornece uma camada adicional de proteção para seus dados em repouso. A chave usada em criptografia dupla de infraestrutura também é gerenciada pelo serviço de banco de dados do Azure para MySQL. A criptografia dupla de infraestrutura não é habilitada por padrão, pois a camada adicional de criptografia pode ter um impacto no desempenho.

> [!NOTE]
> Este recurso só tem suporte para os tipos de preço "Uso Geral" e "memória otimizada" no banco de dados do Azure para MySQL.

A criptografia da camada de infraestrutura tem a vantagem de ser implementada na camada mais próxima do dispositivo de armazenamento ou dos cabos de rede. O banco de dados do Azure para MySQL implementa as duas camadas de criptografia usando chaves gerenciadas pelo serviço. Embora ainda tecnicamente na camada de serviço, é muito próximo do hardware que armazena os dados em repouso. Opcionalmente, você ainda pode habilitar a criptografia de dados em repouso usando a [chave gerenciada pelo cliente](concepts-data-encryption-mysql.md) para o servidor MySQL provisionado. 

A implementação nas camadas de infraestrutura também dá suporte a uma diversidade de chaves. A infraestrutura deve estar ciente de diferentes clusters de máquinas e redes. Como tal, chaves diferentes são usadas para minimizar o raio de ataques de infraestrutura e uma variedade de falhas de hardware e rede. 

> [!NOTE]
> Usar a criptografia dupla de infraestrutura terá impacto no desempenho no banco de dados do Azure para o servidor MySQL devido ao processo de criptografia adicional.

## <a name="benefits"></a>Benefícios

A criptografia dupla de infraestrutura para o banco de dados do Azure para MySQL oferece os seguintes benefícios:

1. **Diversidade adicional de implementação de criptografia** – a mudança planejada para a criptografia baseada em hardware irá mais diversificar as implementações fornecendo uma implementação baseada em hardware além da implementação baseada em software.
2. **Erros de implementação** -duas camadas de criptografia na camada de infraestrutura protege contra erros no cache ou no gerenciamento de memória em camadas mais altas que expõem dados em texto sem formatação. Além disso, as duas camadas também garantem erros na implementação da criptografia em geral.

A combinação delas fornece proteção forte contra ameaças comuns e pontos fracos usados para a criptografia de ataques.

## <a name="supported-scenarios-with-infrastructure-double-encryption"></a>Cenários com suporte com criptografia dupla de infraestrutura

Os recursos de criptografia fornecidos pelo banco de dados do Azure para MySQL podem ser usados juntos. Veja abaixo um resumo dos vários cenários que você pode usar:

|  ##   | Criptografia padrão | Criptografia dupla de infraestrutura | Criptografia de dados usando chaves gerenciadas pelo cliente  |
|:------|:------------------:|:--------------------------------:|:--------------------------------------------:|
| 1     | *Sim*              | *Não*                             | *Não*                                         |
| 2     | *Sim*              | *Sim*                            | *Não*                                         |
| 3     | *Sim*              | *Não*                             | *Sim*                                        |
| 4     | *Sim*              | *Sim*                            | *Sim*                                        |
|       |                    |                                  |                                              |

> [!Important]
> - O cenário 2 e 4 podem introduzir uma queda de 5-10% na taxa de transferência com base no tipo de carga de trabalho para o servidor do banco de dados do Azure para MySQL devido à camada adicional de criptografia de infraestrutura.
> - A configuração da criptografia dupla de infraestrutura para o banco de dados do Azure para MySQL só é permitida durante a criação do servidor. Depois que o servidor for provisionado, você não poderá alterar a criptografia de armazenamento. No entanto, você ainda pode habilitar a criptografia de dados usando chaves gerenciadas pelo cliente para o servidor criado com/sem criptografia dupla de infraestrutura.

## <a name="limitations"></a>Limitações

Para o banco de dados do Azure para MySQL, o suporte para criptografia dupla de infraestrutura usando chave gerenciada por serviço tem as seguintes limitações:

* O suporte para essa funcionalidade é limitado a tipos de preço **uso geral** e com **otimização de memória** .
* Esse recurso só tem suporte em regiões e servidores, que dão suporte a armazenamento de até 16 TB. Para obter a lista de regiões do Azure que dão suporte a armazenamento de até 16 TB, consulte a [documentação de armazenamento](concepts-pricing-tiers.md#storage).

    > [!NOTE]
    > - Todos os **novos** servidores MySQL criados nas regiões listadas acima também dão suporte à criptografia de dados com as chaves do Gerenciador de clientes. Nesse caso, os servidores criados por meio de PITR (restauração pontual) ou réplicas de leitura não se qualificam como "novo".
    > - Para validar se o servidor provisionado dá suporte a até 16 TB, você pode ir para a folha tipo de preço no portal e ver se o controle deslizante de armazenamento pode ser movido para até 16 TB. Se você só pode mover o controle deslizante até 4 TB, o servidor pode não dar suporte à criptografia com chaves gerenciadas pelo cliente; no entanto, os dados são criptografados usando chaves gerenciadas pelo serviço em todos os momentos. Entre em contato com AskAzureDBforMySQL@service.microsoft.com se você tiver alguma dúvida.

## <a name="next-steps"></a>Próximas etapas

Saiba como [Configurar a criptografia dupla de infraestrutura para o banco de dados do Azure para MySQL](howto-double-encryption.md).
