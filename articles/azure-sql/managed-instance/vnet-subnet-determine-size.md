---
title: Determinar o intervalo de & de tamanho de sub-rede necessário
titleSuffix: Azure SQL Managed Instance
description: Este tópico descreve como calcular o tamanho da sub-rede em que o Azure SQL Instância Gerenciada será implantado.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 02/22/2019
ms.openlocfilehash: 156a4c74eea24b20c28df88be85cb32c0ebe2981
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012436"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>Determinar o tamanho de sub-rede necessário & intervalo para o SQL Instância Gerenciada do Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

O Azure SQL Instância Gerenciada deve ser implantado em uma [VNet (rede virtual)](../../virtual-network/virtual-networks-overview.md)do Azure.

O número de instâncias gerenciadas que podem ser implantadas na sub-rede de uma VNet depende do tamanho da sub-rede (intervalo de sub-rede).

Quando você cria uma instância gerenciada, o Azure aloca um número de máquinas virtuais dependendo da camada selecionada durante o provisionamento. Como essas máquinas virtuais estão associadas à sua sub-rede, elas exigem endereços IP. Para garantir alta disponibilidade durante operações regulares e manutenção do serviço, o Azure pode alocar máquinas virtuais adicionais. Como resultado, o número de endereços IP necessários em uma sub-rede é maior do que o número de instâncias gerenciadas nessa sub-rede.

Por padrão, uma instância gerenciada precisa de um mínimo de 32 endereços IP em uma sub-rede. Como resultado, você pode usar a máscara de sub-rede mínima de /27 ao definir os intervalos de IP de sub-rede. É recomendável um planejamento cuidadoso de tamanho de sub-rede para suas implantações da instância gerenciada. As entradas que devem ser levadas em consideração durante o planejamento são:

- Número de instâncias gerenciadas, incluindo os seguintes parâmetros de instância:
  - camada de serviço
  - geração de hardware
  - número de vCores
- Planos para escalar verticalmente ou alterar a camada de serviço

> [!IMPORTANT]
> Um tamanho de sub-rede com 16 endereços IP (máscara de sub-rede/28) permitirá a implantação de instância gerenciada dentro dela, mas deve ser usado somente para implantação de instância única usada para avaliação ou em cenários de desenvolvimento/teste, em que as operações de dimensionamento não serão executadas.

## <a name="determine-subnet-size"></a>Determinar o tamanho da sub-rede

Dimensione sua sub-rede de acordo com as necessidades de implantação e dimensionamento de instância futura. Os parâmetros a seguir podem ajudá-lo a formar um cálculo:

- O Azure usa cinco endereços IP na sub-rede para necessidades próprias
- Cada cluster virtual aloca um número adicional de endereços 
- Cada instância gerenciada usa o número de endereços que dependem do tipo de preço e da geração de hardware

> [!IMPORTANT]
> Não é possível alterar o intervalo de endereços de sub-rede, se houver recursos na sub-rede. Também não é possível migrar as instâncias gerenciadas de uma sub-rede para outra. Sempre que possível, considere o uso de sub-redes maiores, em vez de menores, para evitar problemas no futuro.

GP = uso geral; BC = comercialmente crítico; VC = cluster virtual

| **Ger de hardware** | **Tipo de preços** | **Uso do Azure** | **Uso do VC** | **Uso da instância** | **Total** _ |
| --- | --- | --- | --- | --- | --- |
| Gen4 | GP | 5 | 1 | 5 | 11 |
| Gen4 | BC | 5 | 1 | 5 | 11 |
| Gen5 | GP | 5 | 6 | 3 | 14 |
| Gen5 | BC | 5 | 6 | 5 | 16 |

  \_ Total da coluna exibe o número de endereços que seriam tomadas quando uma instância é implantada na sub-rede. Cada instância adicional na sub-rede Adiciona o número de endereços representados com a coluna uso da instância. Os endereços representados com a coluna uso do Azure são compartilhados entre vários clusters virtuais, enquanto os endereços representados com a coluna uso do VC são compartilhados entre instâncias colocadas nesse cluster virtual.

A operação de atualização normalmente requer redimensionamento de cluster virtual. Em algumas circunstâncias, a operação de atualização exigirá a criação do cluster virtual (para obter mais detalhes, consulte o [artigo operações de gerenciamento](sql-managed-instance-paas-overview.md#management-operations)). No caso da criação do cluster virtual, o número de endereços adicionais necessários é igual ao número de endereços representados pela coluna uso do VC somada com os endereços necessários para as instâncias colocadas nesse cluster virtual (coluna uso da instância).

**Exemplo 1**: você planeja ter uma instância gerenciada de uso geral (hardware Gen4) e uma instância gerenciada crítica para os negócios (hardware Gen5). Isso significa que você precisa de um mínimo de 5 + 1 + 1 * 5 + 6 + 1 * 5 = 22 endereços IP para poder implantar. Como os intervalos de IP são definidos na potência de 2, sua sub-rede requer o intervalo mínimo de IP de 32 (2 ^ 5) para essa implantação.<br><br>
Conforme mencionado acima, em algumas circunstâncias, a operação de atualização exigirá a criação do cluster virtual. Isso significa que, como um exemplo, no caso de uma atualização para a instância gerenciada crítica de negócios Gen5 que requer uma criação de cluster virtual, você precisará ter mais 6 + 5 = 11 endereços IP disponíveis. Como você já está usando 22 dos endereços 32, não há nenhum endereço disponível para esta operação. Portanto, você precisa reservar a sub-rede com a máscara de sub-rede de/26 (endereços 64).

**Exemplo 2**: você planeja ter três Gen5 (hardware de uso geral) e duas instâncias gerenciadas críticas para os negócios (hardware Gen5) colocadas na mesma sub-rede. Isso significa que você precisa de 5 + 6 + 3 * 3 + 2 * 5 = 30 endereços IP. Portanto, você precisa reservar a sub-rede com a máscara de sub-rede de/26. A seleção de uma máscara de sub-rede de/27 faria com que o número restante de endereços fosse 2 (32 – 30), isso impediria operações de atualização para todas as instâncias, uma vez que endereços adicionais são necessários na sub-rede para executar o dimensionamento da instância.

**Exemplo 3**: você planeja ter uma instância gerenciada de uso geral (hardware Gen5) e executar a operação de atualização de vCores de tempos em tempos. Isso significa que você precisa de 5 + 6 + 1 * 3 + 3 = 17 endereços IP. Como os intervalos de IP são definidos em potência de 2, você precisa do intervalo de IP de 32 (2^5) endereços IP. Portanto, é necessário reservar a sub-rede com a máscara de sub-rede de /27.

### <a name="address-requirements-for-update-scenarios"></a>Requisitos de endereço para cenários de atualização

Durante o dimensionamento, as instâncias de operação requerem temporariamente a capacidade de IP adicional que depende do tipo de preço e da geração de hardware

| **Ger de hardware** | **Tipo de preços** | **Cenário** | **Endereços adicionais** _ |
| --- | --- | --- | --- |
| Gen4 | GP ou BC | Dimensionamento de vCores | 5 |
| Gen4 | GP ou BC | Dimensionando o armazenamento | 5 |
| Gen4 | GP ou BC | Alternando de GP para BC ou BC para GP | 5 |
| Gen4 | GP | Alternando para Gen5_ | 9 |
| Gen4 | BC | Alternando para Gen5 * | 11 |
| Gen5 | GP | Dimensionamento de vCores | 3 |
| Gen5 | GP | Dimensionando o armazenamento | 0 |
| Gen5 | GP | Alternando para BC | 5 |
| Gen5 | BC | Dimensionamento de vCores | 5 |
| Gen5 | BC | Dimensionando o armazenamento | 5 |
| Gen5 | BC | Alternando para GP | 3 |

  \* O hardware Gen4 está sendo descontinuado e não está mais disponível para novas implantações. Atualize a geração de hardware de Gen4 para Gen5 para aproveitar os recursos específicos da geração de hardware Gen5.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, consulte [o que é o Azure SQL instância gerenciada?](sql-managed-instance-paas-overview.md).
- Saiba mais sobre [a arquitetura de conectividade para o SQL instância gerenciada](connectivity-architecture-overview.md).
- Consulte como [criar uma VNet na qual você implantará o SQL instância gerenciada](virtual-network-subnet-create-arm-template.md).
- Para problemas de DNS, consulte [configurar um DNS personalizado](custom-dns-configure.md).
