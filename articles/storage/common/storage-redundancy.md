---
title: Redundância de dados no Armazenamento do Azure | Microsoft Docs
description: Os dados na sua conta do Armazenamento do Microsoft Azure são replicados para garantir durabilidade e alta disponibilidade. As opções de redundância incluem armazenamento com redundância local (LRS), armazenamento com redundância de zona (ZRS), armazenamento com redundância geográfica (GRS) e armazenamento com redundância geográfica com acesso de leitura (RA-GRS).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/10/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: b4b9e913363938f98999c6a769ba83efbd625b69
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798331"
---
# <a name="azure-storage-redundancy"></a>Redundância do Armazenamento do Azure

Os dados na sua conta de armazenamento do Microsoft Azure sempre são replicados para garantir durabilidade e alta disponibilidade. A redundância do Armazenamento do Azure copia os dados para que eles sejam protegidos contra eventos planejados e não planejados, incluindo falhas de hardware transitórias, interrupções de energia ou rede e desastres naturais de grandes proporções. Você pode escolher replicar os dados no mesmo datacenter, em datacenters zonais na mesma região ou entre regiões separadas geograficamente.

A replicação garante que sua conta de armazenamento atenda ao [SLA (Contrato de Nível de Serviço) do Armazenamento](https://azure.microsoft.com/support/legal/sla/storage/) mesmo diante de falhas. Consulte o SLA para obter informações sobre as garantias do Armazenamento do Azure para disponibilidade e durabilidade.

Regularmente, o armazenamento do Azure verifica a integridade dos dados armazenados usando verificações de redundância cíclica (CRC). Se for detectada corrupção de dados, é reparado usando dados redundantes. O armazenamento do Azure também calcula somas de verificação em todo o tráfego de rede para detectar corrupção de pacotes de dados ao armazenar ou recuperar dados.

## <a name="choosing-a-redundancy-option"></a>Escolher uma opção de redundância

Ao criar uma conta de armazenamento, você deve selecionar uma das seguintes opções de redundância:

* [Armazenamento com redundância local (LRS)](storage-redundancy-lrs.md)
* [Armazenamento com redundância de zona (ZRS)](storage-redundancy-zrs.md)
* [Armazenamento com redundância geográfica (GRS)](storage-redundancy-grs.md)
* [Armazenamento com redundância geográfica com acesso de leitura (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage)

A tabela a seguir fornece uma visão geral rápida do escopo de durabilidade e disponibilidade que cada estratégia de replicação fornecerá a você para um determinado tipo de evento (ou evento de impacto semelhante).

| Cenário                                                                                                 | LRS                             | ZRS                              | GRS                                  | RA-GRS                               |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Indisponibilidade de nó dentro de um datacenter                                                                 | Sim                             | Sim                              | Sim                                  | Sim                                  |
| Um data center inteiro (zonal ou não zonal) fica indisponível                                           | Não                              | Sim                              | Sim                                  | Sim                                  |
| Uma interrupção em toda a região                                                                                     | Não                              | Não                               | Sim                                  | Sim                                  |
| Acesso de leitura aos dados (em uma região remota e replicada geograficamente) no caso de indisponibilidade em toda a região | Não                              | Não                               | Não                                   | Sim                                  |
| Projetado para fornecer \_\_ durabilidade de objetos em um determinado ano                                          | no mínimo 99,999999999% (11 9's) | no mínimo 99,9999999999% (12 9's) | no mínimo 99,99999999999999% (16 9's) | no mínimo 99,99999999999999% (16 9's) |
| Tipos de conta de armazenamento suportados                                                                   | GPv2, GPv1, Blob                | GPv2                             | GPv2, GPv1, Blob                     | GPv2, GPv1, Blob                     |
| SLA de disponibilidade para solicitações de leitura | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,99% (99,9% para a camada de acesso estática) |
| SLA de disponibilidade para solicitações de gravação | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso esporádico) |

Todos os dados da sua conta de armazenamento são replicados, incluindo blobs de blocos e blobs de página, blobs, filas, tabelas e arquivos de acréscimo. Todos os tipos de contas de armazenamento são replicados, embora o ZRS requer uma conta de armazenamento de uso geral v2.

Para obter informações sobre cada opção de redundância de preços, consulte [preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/). 

Para obter informações sobre o armazenamento do Azure garante para durabilidade e disponibilidade, consulte o [SLA do armazenamento do Azure](https://azure.microsoft.com/support/legal/sla/storage/).

> [!NOTE]
> Armazenamento Premium do Azure dá suporte a armazenamento apenas localmente redundante (LRS).

## <a name="changing-replication-strategy"></a>Alterar estratégia de replicação
Você pode alterar a estratégia de replicação da sua conta de armazenamento usando o [portal do Azure](https://portal.azure.com/), [do Azure Powershell](storage-powershell-guide-full.md), [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), ou uma da [cliente do Azure bibliotecas](https://docs.microsoft.com/azure/index#pivot=sdkstools). Alterar o tipo de replicação da sua conta de armazenamento não resulta em tempo de inatividade.

   > [!NOTE]
   > Atualmente, você não pode usar o Portal ou a API para converter sua conta no ZRS. Se você quiser converter a replicação da conta para ZRS, consulte [Armazenamento com redundância de zona (ZRS)](storage-redundancy-zrs.md) para mais detalhes.
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>Há algum custo para alterar a estratégia de replicação da minha conta?
Depende do caminho de conversão. A ordenação da oferta de redundância mais barata para a mais cara é LRS, ZRS, GRS e RA-GRS. Por exemplo, se você escolher *partir de* LRS para qualquer outra, terá encargos adicionais porque você está transitando para um nível de redundância mais sofisticado. Escolher *para* GRS ou RA-GRS acarretará um encargo de largura de banda de saída porque os dados (em sua região primária) estão sendo replicados para a região secundária remota. Esse é um encargo único na configuração inicial. Depois que os dados forem copiados, não haverá mais encargos de conversão. Você será cobrado apenas para replicar qualquer novo ou atualizações em dados existentes. Para obter detalhes sobre taxas de largura de banda, visite a [página Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

Se você converter sua conta de armazenamento do GRS para LRS, não há nenhum custo adicional, mas seus dados replicados serão excluídos do local secundário.

Se você converter sua conta de armazenamento de RA-GRS para GRS ou LRS, essa conta é cobrada como RA-GRS por 30 dias adicionais além da data em que ele foi convertido.

## <a name="see-also"></a>Consulte também

- [Armazenamento com redundância local (LRS): Redundância de dados de baixo custo para o Armazenamento do Microsoft Azure](storage-redundancy-lrs.md)
- [ZRS (armazenamento com redundância de zona): Aplicativos de Armazenamento do Azure altamente disponíveis](storage-redundancy-zrs.md)
- [Armazenamento com redundância geográfica (GRS): Replicação inter-regional para Armazenamento do Microsoft Azure](storage-redundancy-grs.md)
- [Metas de desempenho e escalabilidade do Armazenamento do Azure](storage-scalability-targets.md)
- [Criando aplicativos altamente disponíveis usando o armazenamento de RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
- [Armazenamento do Microsoft Azure redundância acesso de leitura e opções de armazenamento com redundância geográfica](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [SOSP Paper – Armazenamento do Azure: Um serviço de armazenamento em nuvem altamente disponível com coerência forte](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
