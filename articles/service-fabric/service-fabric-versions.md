---
title: Atualizar sua versão do cluster de Service Fabric do Azure
description: Saiba mais sobre as versões de cluster no Azure Service Fabric, incluindo um link para as versões mais recentes do blog da equipe do Service Fabric.
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.openlocfilehash: 3e859a04ffb0b885aab0f31e83afad8380cbcc95
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103010194"
---
# <a name="upgrade-your-azure-service-fabric-cluster-version"></a>Atualizar sua versão do cluster de Service Fabric do Azure

Verifique se o cluster está sempre executando uma versão com suporte do Azure Service Fabric. No mínimo 60 dias depois de anunciarmos o lançamento de uma nova versão do Service Fabric, o suporte para a versão anterior termina. Você encontrará anúncios de novas versões no blog da [equipe do Service Fabric](https://azure.microsoft.com/updates/?product=service-fabric).

Para cada versão do Service Fabric Runtime, você pode usar as versões especificadas ou mais antigas dos pacotes SDK/NuGet. As versões mais recentes dos pacotes podem não ser capazes de se destinar a clusters mais antigos. Os clusters mais antigos podem ter alterações de recurso ou protocolo que os ambientes de pacote mais recentes não dão suporte.

Consulte os artigos a seguir para obter detalhes sobre como manter o cluster executando uma versão de Service Fabric com suporte:

- [Atualizar um cluster do Azure Service Fabric](service-fabric-cluster-upgrade.md)
- [Atualizar a versão de Service Fabric que é executada em seu cluster autônomo do Windows Server](service-fabric-cluster-upgrade-windows-server.md)

## <a name="unsupported-versions"></a>Versões sem suporte

### <a name="upgrade-alert-for-versions-between-57-and-6363"></a>Alerta de atualização para versões entre 5,7 e 6.3.63. *

Para melhorar a segurança e a disponibilidade, a infraestrutura do Azure fez uma alteração que pode afetar Service Fabric clientes. Essa alteração afeta todos os clusters Service Fabric executando as versões 5,7 a 6,3.

Uma atualização para o tempo de execução de Service Fabric está disponível para todas as versões de Service Fabric com suporte em todas as regiões. Atualize para uma das versões mais recentes com suporte até 19 de janeiro de 2021, para evitar interrupções de serviço.

Se você tiver um plano de suporte e precisar de ajuda técnica, acesse por meio dos canais de suporte do Azure. Abra uma solicitação de suporte para Service Fabric do Azure e mencione esse contexto no tíquete de suporte.

#### <a name="if-you-dont-upgrade-to-a-supported-version"></a>Se você não atualizar para uma versão com suporte

Os clusters Service Fabric do Azure que são executados em versões do 5,7 para 6.3.63. * ficarão indisponíveis se você não tiver atualizado até 19 de janeiro de 2021.

#### <a name="required-action"></a>Ação necessária

Atualize para uma versão Service Fabric com suporte para evitar tempo de inatividade ou perda de funcionalidade relacionada a essa alteração. Verifique se os clusters estão executando pelo menos as seguintes versões para evitar problemas em seu ambiente.

> [!Note]
> **Todas as versões lançadas do 7,2 incluem as alterações necessárias**.
  
  | SO | Tempo de execução de Service Fabric atual no cluster | CU/versão do patch |
  | --- | --- |--- |
  | Windows | 7,0. * | 7.0.478.9590 |
  | Windows | 7,1. * | 7.1.503.9590 |
  | Windows | 7,2. * | 7,2. * |
  | Ubuntu 16 | 7,0. * | 7.0.472.1  |
  | Linux Ubuntu 16.04 | 7,1. * | 7.1.455.1  |
  | Linux Ubuntu 18, 4 | 7,1. * | 7.1.455.1804 |
  | Linux Ubuntu 16.04 | 7,2. * | 7,2. * |
  | Linux Ubuntu 18, 4 | 7,2. * | 7,2. * |

### <a name="upgrade-alert-for-versions-later-than-63"></a>Alerta de atualização para versões posteriores a 6,3

Para melhorar a segurança e a disponibilidade, a infraestrutura do Azure fez uma alteração que pode afetar Service Fabric clientes. Essa alteração afetará todos os clusters de Service Fabric que usam o [modo de rede aberta para contêineres](./service-fabric-networking-modes.md#set-up-open-networking-mode) e executam versões 6,3 a 7,0 ou versões incompatíveis com suporte posteriores a 7,0. Uma atualização para o tempo de execução de Service Fabric está disponível para todas as versões de Service Fabric com suporte em todas as regiões.

#### <a name="if-you-dont-upgrade-to-a-supported-version"></a>Se você não atualizar para uma versão com suporte

Os clusters Service Fabric do Azure que são executados em versões inalteradas posteriores a 6,3 sofrerão perda de funcionalidade ou interrupções de serviço se não tiverem sido atualizados para uma versão com suporte até 19 de janeiro de 2021.
  
  - **Para clusters que executam uma versão do Service Fabric maior que 6,3 não usando o recurso de rede aberta**, o cluster permanecerá ativo.

 - **Para clusters que executam uma versão do Service Fabric maior que 6,3 e usam o [recurso de rede aberta para contêineres](./service-fabric-networking-modes.md#set-up-open-networking-mode)** , o cluster pode se tornar indisponível e deixará de funcionar, o que pode causar interrupções de serviço para suas cargas de trabalho.
 
 -   **Para clusters que executam [versões do Windows entre 7.0.457 e 7.0.466 (ambas as versões incluídas)](#supported-version-names) e o sistema operacional Windows tem o recurso de contêineres do Windows habilitado. Observação: as versões do Linux 7.0.457, 7.0.464 e 7.0.465 não são afetadas**.
    - **Impacto**: o cluster deixará de funcionar, o que pode causar interrupções de serviço para suas cargas de trabalho.
    
#### <a name="required-action"></a>Ação necessária

Para evitar o tempo de inatividade ou a perda de funcionalidade, verifique se os clusters estão executando uma das seguintes versões.

As versões do Service Fabric na tabela contêm as alterações necessárias para evitar a perda de funcionalidade. Verifique se você está usando uma dessas versões.  

> [!Note]
> **Os clusters do Azure Service Fabric em execução na versão 6,5, precisam executar várias atualizações ao mesmo tempo antes de infrastucuture alterar para evitar a perda de funcionalidade no cluster**. 
>   -   1. Atualize para o 7.0.466. **Os clusters que executam o sistema operacional Windows que tem o recurso de contêineres do Windows habilitado não podem estar nessa versão intermediária. Eles precisam executar a próxima etapa (II) abaixo. ou seja,  Atualize para estar em versão mais segura e em conformidade para evitar interrupções de serviço**
>   -   2. Atualize para as últimas versões de reclamação na versão 7,0 * (7.0.478) ou em qualquer uma das versões mais recentes listadas abaixo.


> [!Note]
> **Todas as versões de lançamento do 7,2 incluem as alterações necessárias**.

 | SO | Tempo de execução de Service Fabric atual no cluster | CU/versão do patch |
  | --- | --- |--- |
  | Windows | 7,0. * | 7.0.478.9590 |
  | Windows | 7,1. * | 7.1.503.9590 |
  | Windows | 7,2. * | 7,2. * |
  | Linux Ubuntu 16.04 | 7,0. * | 7.0.472.1  |
  | Linux Ubuntu 16.04 | 7,1. * | 7.1.455.1  |
  | Linux Ubuntu 18, 4 | 7,1. * | 7.1.455.1804 |
  | Linux Ubuntu 16.04 | 7,2. * | 7,2. * |
  | Linux Ubuntu 18, 4 | 7,2. * | 7,2. * |

## <a name="supported-versions"></a>Versões com suporte

A tabela a seguir lista as versões do Service Fabric e suas datas de término do suporte.

| runtime do Service Fabric no cluster | Permite a atualização diretamente da versão do cluster |SDK compatível ou versão do pacote NuGet | Fim do suporte |
| --- | --- |--- | --- |
| Todas as versões do cluster antes de 5.3.121 | 5.1.158.* |Menor ou igual à versão 2.3 |20 de janeiro de 2017 |
| 5.3.* | 5.1.158.* |Menor ou igual à versão 2.3 |24 de fevereiro de 2017 |
| 5.4.* | 5.1.158.* |Menor ou igual à versão 2.4 |10 de maio de 2017       |
| 5.5.* | 5.4.164.* |Menor ou igual à versão 2.5 |10 de agosto de 2017    |
| 5.6.* | 5.4.164.* |Menor ou igual à versão 2.6 |13 de outubro de 2017   |
| 5.7.* | 5.4.164.* |Menor ou igual à versão 2.7 |15 de dezembro de 2017  |
| 6.0.* | 5.6.205.* |Menor ou igual à versão 2.8 |30 de março de 2018     |
| 6.1.* | 5.7.221.* |Menor ou igual à versão 3.0 |15 de julho de 2018      |
| 6.2.* | 6.0.232.* |Menor ou igual à versão 3.1 |26 de outubro de 2018   |
| 6.3.* | 6.1.480.* |Menor ou igual à versão 3.2 |31 de março de 2019  |
| 6.4.* | 6.2.301.* |Menor ou igual a versão 3.3 |15 de setembro de 2019 |
| 6,5. * | 6.4.617.* |Menor ou igual à versão 3,4 |1º de agosto de 2020 |
| 7.0.466.* | 6.4.664.* |Menor ou igual à versão 4,0|31 de janeiro de 2021  |
| 7.0.466.* | 6,5. * |Menor ou igual à versão 4,0|31 de janeiro de 2021 |
| 7.0.470.* | 7.0.466.* |Menor ou igual à versão 4,0 |31 de janeiro de 2021  |
| 7.0.472.* | 7.0.466.* |Menor ou igual à versão 4,0 |31 de janeiro de 2021  |
| 7.0.478.* | 7.0.466.* |Menor ou igual à versão 4,0 |31 de janeiro de 2021  |
| 7.1.409.* | 7.0.466.* |Menor ou igual à versão 4,1 |31 de julho de 2021 |
| 7.1.417.* | 7.0.466.* |Menor ou igual à versão 4,1 |31 de julho de 2021 |
| 7.1.428.* | 7.0.466.* |Menor ou igual à versão 4,1 |31 de julho de 2021 |
| 7.1.456.* | 7.0.466.* |Menor ou igual à versão 4,1 |31 de julho de 2021 |
| 7.1.458.* | 7.0.466.* |Menor ou igual à versão 4,1 |31 de julho de 2021 |
| 7.1.459.* | 7.0.466.* |Menor ou igual à versão 4,1 |31 de julho de 2021 |
| 7.1.503.* | 7.0.466.* |Menor ou igual à versão 4,1 |31 de julho de 2021 |
| 7.1.510.* | 7.0.466.* |Menor ou igual à versão 4,1 |31 de julho de 2021 |
| 7.2.413.* | 7.0.470.* |Menor ou igual à versão 4,2 |Versão atual, portanto, sem data de término |
| 7.2.432.* | 7.0.470.* |Menor ou igual à versão 4,2 |Versão atual, portanto, sem data de término |
| 7.2.433.* | 7.0.470.* |Menor ou igual à versão 4,2 |Versão atual, portanto, sem data de término |
| 7.2.445.* | 7.0.470.* |Menor ou igual à versão 4,2 |Versão atual, portanto, sem data de término |
| 7.2.452.* | 7.0.470.* |Menor ou igual à versão 4,2 |Versão atual, portanto, sem data de término |
| 7.2.457.* | 7.0.470.* |Menor ou igual à versão 4,2 |Versão atual, portanto, sem data de término |
| 7.2.477.* | 7.0.478.* |Menor ou igual à versão 4,2 |Versão atual, portanto, sem data de término |

## <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis

A tabela a seguir lista os sistemas operacionais com suporte para as versões de Service Fabric com suporte.

| Sistema operacional | Versão de Service Fabric mais antiga com suporte |
| --- | --- |
| Windows Server 2012 R2 | Todas as versões |
| Windows Server 2016 | Todas as versões |
| Windows Server 1709 | 6,0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6,0 |
| Linux Ubuntu 18, 4 | 7.1 |

## <a name="supported-version-names"></a>Nomes de versão com suporte

A tabela a seguir lista os nomes de versão de Service Fabric e seus números de versão correspondentes.

| Nome da versão | Número de versão do Windows | Número de versão do Linux |
| --- | --- | --- |
| RTO DE 5,3 | 5.3.121.9494 | Não Aplicável|
| 5,3 CU1 | 5.3.204.9494 | Não Aplicável|
| 5,3 CU2 | 5.3.301.9590 | Não Aplicável|
| 5,3 CU3 | 5.3.311.9590 | Não Aplicável|
| 5,4 CU2 | 5.4.164.9494 | Não Aplicável|
| 5,5 CU1 | 5.5.216.0    | Não Aplicável|
| 5,5 CU2 | 5.5.219.0 | Não Aplicável|
| 5,5 CU3 | 5.5.227.0 | Não Aplicável|
| 5,5 CU4 | 5.5.232.0 | Não Aplicável|
| RTO DE 5,6 | 5.6.204.9494 | Não Aplicável|
| 5,6 CU2 | 5.6.210.9494 | Não Aplicável|
| 5,6 CU3 | 5.6.220.9494 | Não Aplicável|
| RTO DE 5,7 | 5.7.198.9494 | Não Aplicável|
| 5,7 CU4 | 5.7.221.9494 | Não Aplicável|
| RTO DE 6,0 | 6.0.211.9494 | 6.0.120.1 |
| 6,0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6,0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6,1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6,1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6,1 CU3 | 6.1.472.9494 | Não Aplicável|
| 6,1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| RTO DE 6,2 | 6.2.269.9494 | 6.2.184.1 |
| 6,2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6,2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6,2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| RTO DE 6,3 | 6.3.162.9494 | 6.3.119.1 |
| 6,3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6,3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| RTO DE 6,4 | 6.4.617.9590 | 6.4.625.1 |
| 6,4 CU2 | 6.4.622.9590 | Não Aplicável|
| 6,4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6,4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6,4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6,4 CU6 | 6.4.658.9590 | Não Aplicável|
| 6,4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6,4 CU8 | 6.4.670.9590 | Não Aplicável|
| RTO DE 6,5 | 6.5.639.9590 | 6.5.435.1 |
| 6,5 CU1 | 6.5.641.9590 | 6.5.454.1 |
| 6,5 CU2 | 6.5.658.9590 | 6.5.460.1 |
| 6,5 CU3 | 6.5.664.9590 | 6.5.466.1 |
| 6,5 CU5 | 6.5.676.9590 | 6.5.467.1 |
| RTO DE 7,0 | 7.0.457.9590 | 7.0.457.1 |
| 7,0 CU2 | 7.0.464.9590 | 7.0.464.1 |
| 7,0 CU3 | 7.0.466.9590 | 7.0.465.1 |
| 7,0 CU4 | 7.0.470.9590 | 7.0.469.1 |
| 7,0 CU6 | 7.0.472.9590 | 7.0.471.1 |
| 7,0 CU9 | 7.0.478.9590 | 7.0.472.1 |
| RTO DE 7,1 | 7.1.409.9590 | 7.1.410.1 |
| 7,1 CU1 | 7.1.417.9590 | 7.1.418.1 |
| 7,1 CU2 | 7.1.428.9590 | 7.1.428.1 |
| 7,1 CU3 | 7.1.456.9590 | 7.1.452.1 |
| 7,1 CU5 | 7.1.458.9590 | 7.1.454.1 |
| 7,1 CU6 | 7.1.459.9590 | 7.1.455.1 |
| 7,1 CU8 | 7.1.503.9590 | 7.1.508.1 |
| 7,1 CU10 | 7.1.510.9590 | NA |
| RTO DE 7,2 | 7.2.413.9590 | NA |
| 7,2 CU2 | 7.2.432.9590 | 7.2.431.1 |
| 7,2 CU3 | 7.2.433.9590 | NA |
| 7,2 CU4 | 7.2.445.9590 | 7.2.447.1 |
| 7,2 CU5 | 7.2.452.9590 | 7.2.454.1 |
| 7,2 CU6 | 7.2.457.9590 | 7.2.456.1 |
| 7,2 CU7 | 7.2.477.9590 | 7.2.476.1 |