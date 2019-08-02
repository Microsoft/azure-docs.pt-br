---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/23/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 14de5283197778464993b80b312a690662e066ab
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443764"
---
O armazenamento do Azure oferece vários tipos de contas de armazenamento. Cada tipo é compatível com recursos diferentes e tem um modelo de preços próprio. Considere essas diferenças antes de criar uma conta de armazenamento para determinar o melhor tipo de conta para seus aplicativos. Os tipos de conta de armazenamento são:

- **Contas para uso geral v2**: tipo de conta de armazenamento básico para blobs, arquivos, filas e tabelas. Recomendado para a maioria dos cenários que usam o Armazenamento do Azure.
- **Contas para uso geral v1**: tipo de conta herdada para blobs, arquivos, filas e tabelas. Use contas de uso geral v2 em vez desta opção, quando possível.
- **Contas de armazenamento de blob de bloco**: Contas de armazenamento somente blob com características de desempenho premium. Recomendado para cenários com taxas de transações alta, usando os objetos menores ou exigir que a latência de armazenamento consistentemente baixo.
- **Contas de armazenamento FileStorage**: Contas de armazenamento somente para arquivos com características de desempenho premium. Recomendado para enterprise ou aplicativos de dimensionamento de alto desempenho.
- **Contas de armazenamento de blobs**: contas de armazenamento somente de blobs. Use contas de uso geral v2 em vez desta opção, quando possível.

A tabela a seguir descreve os tipos de conta de armazenamento e suas funcionalidades:

| Tipo de conta de armazenamento | Serviços com suporte                       | Níveis de desempenho compatíveis      | Camadas de acesso compatíveis         | Opções de replicação               | Modelo de implantação<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Criptografia<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Uso geral V2   | Blob, arquivo, fila, tabela e disco       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Frequente, esporádico, arquivo morto<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Gerenciador de Recursos             | Criptografado              |
| Uso geral V1   | Blob, arquivo, fila, tabela e disco       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | N/D                            | LRS, GRS, RA-GRS                  | Resource Manager, Clássico    | Criptografado              |
| Armazenamento de blobs de bloco   | Blob (somente blobs de blocos e blobs de acréscimo) | Premium                       | N/D                            | LRS                               | Gerenciador de Recursos             | Criptografado              |
| FileStorage   | Somente arquivos | Premium                       | N/D                            | LRS                               | Gerenciador de Recursos             | Criptografado              |
| Armazenamento de blob         | Blob (somente blobs de blocos e blobs de acréscimo) | Standard                      | Frequente, esporádico, arquivo morto<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Gerenciador de Recursos             | Criptografado              |

<div id="deployment-model"><sup>1</sup>É recomendável usar o modelo de implantação do Azure Resource Manager. Ainda é possível criar contas de armazenamento que usam o modelo de implantação clássico em alguns locais, e as contas clássicas existentes ainda têm suporte. Para obter mais informações, consulte <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager vs implantação clássica: compreenda os modelos de implantação e o estado de seus recursos</a>.</div>

<div id="encryption"><sup>2</sup>Todas as contas de armazenamento são criptografadas usando a SSE (Criptografia do Serviço de Armazenamento) para dados em repouso. Para obter mais informações, consulte <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">Criptografia de serviço do Armazenamento do Azure para dados em repouso</a>.</div>

<div id="archive"><sup>3</sup>A camada de acesso aos arquivos está disponível somente no nível de blob individual, e não no nível da conta de armazenamento. Somente os blobs de bloco e de acréscimo podem ser arquivados. Para obter mais informações, consulte <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">Armazenamento de Blobs do Azure: camadas de armazenamento de Arquivos, Frequente e Esporádico</a>.</div>

<div id="zone-redundant-storage"><sup>4</sup>O ZRS (armazenamento com redundância de zona) só está disponível para as contas de armazenamento padrão para uso geral v2. Para obter mais informações sobre o ZRS, consulte <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs">Armazenamento com redundância de zona (ZRS): aplicativos de Armazenamento do Azure altamente disponíveis</a>. Para saber mais sobre outras opções de replicação, confira <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Replicação do Armazenamento do Azure</a>.</div>

<div id="premium-performance"><sup>5</sup>desempenho premium para uso geral v2 e contas de uso geral v1 está disponível para blobs de página e de disco somente.</div>
