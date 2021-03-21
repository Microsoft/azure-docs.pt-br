---
title: Escolha uma solução do Azure para transferência de dados periódica| Microsoft Docs
description: Saiba como escolher uma solução do Azure para transferência de dados durante a transferência de dados periodicamente.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: a15ebd43861e2116ddbb2d9055b289645962e203
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96573911"
---
# <a name="solutions-for-periodic-data-transfer"></a>Soluções para transferência de dados periódica
 
Este artigo fornece uma visão geral da transferência de dados de soluções quando você estiver transferindo dados periodicamente. A transferência de dados periódicos pela rede pode ser categorizada como recorrentes em intervalos regulares ou movimentação de dados contínua. O artigo também descreve as opções de transferência de dados recomendadas e a respectiva matriz das principais funcionalidades para esse cenário.

Para entender uma visão geral de todas as opções de transferência de dados disponíveis, vá para [Escolher uma solução de transferência de dados do Azure](storage-choose-data-transfer-solution.md).

## <a name="recommended-options"></a>Opções recomendadas

As opções recomendadas para a transferência de dados periódica se enquadram em duas categorias, dependendo se a transferência for recorrente ou contínua.

- **Ferramentas de script/programação** – para transferência de dados que ocorre em intervalos regulares, use o script e ferramentas programáticas como o AzCopy e as API REST do Armazenamento do Microsoft Azure. Essas ferramentas são destinadas a desenvolvedores e profissionais de TI.

    - **AzCopy** – use essa ferramenta de linha de comando para copiar dados facilmente de e para Blobs do Azure, Arquivos e armazenamento de Tabela com um desempenho ideal. O AzCopy dá suporte à simultaneidade e ao paralelismo e à capacidade de retomar as operações de cópia quando elas forem interrompidas.
    - **SDKs/APIs REST do Armazenamento do Azure** – ao criar um aplicativo, você pode desenvolver o aplicativo com relação a APIs REST do Armazenamento do Microsoft Azure e usar os SDKs de cliente do Azure oferecidos em vários idiomas. As APIs REST também pode aproveitar a biblioteca de movimentação de dados do Armazenamento do Microsoft Azure desenvolvido especialmente para a cópia de alto desempenho de dados para e do Azure.

- **Ferramentas de ingestão de dados contínuas** – para a ingestão de dados contínua, em andamento, você pode selecionar um dispositivo de transferência online Data Box ou Azure Data Factory. Essas ferramentas são configuradas por profissionais de TI e podem automatizar a transferência de dados de forma transparente.

    - **Azure Data Factory** – o Data Factory deve ser usado para expandir uma operação de transferência e se houver necessidade de funcionalidades de monitoramento de nível empresarial e de orquestração. Use o Azure Data Factory para configurar um pipeline de nuvem para regularmente transferir arquivos entre vários serviços do Azure, no local ou uma combinação dos dois. O Azure Data Factory permite que você orquestre fluxos de trabalho controlados por dados que ingerem dados de armazenamentos de dados diferentes e automatizam a movimentação e transformação de dados.
    - **Família de Azure data box para transferências online** – Data Box Edge e gateway do data box são dispositivos de rede online que podem mover dados para dentro e fora do Azure. O Azure Data Box Edge usa a inteligência artificial (AI)-habilitado a computação de borda pré-processar os dados antes do carregamento. O Data Box Gateway é uma versão virtual do dispositivo com os mesmos recursos de transferência de dados.


## <a name="comparison-of-key-capabilities"></a>Comparação das principais funcionalidades

A tabela a seguir resume as diferenças nas principais funcionalidades.

### <a name="scriptedprogrammatic-network-data-transfer"></a>Transferência de dados de rede/programáticas com script

| Recurso                  | AzCopy                                 | APIs REST do Armazenamento do Microsoft Azure       |
|-----------------------------|----------------------------------------|-------------------------------|
| Fator forma                 | Ferramenta de linha de comando da Microsoft       | Os clientes desenvolvem em relação ao armazenamento <br> APIs REST usando bibliotecas de cliente do Azure |
| Configuração única inicial     | Minimal                                | Esforço moderado, variáveis, desenvolvimento    |
| Formato de Dados                 | Blobs do Azure, Azure Files, Tabelas do Azure | Blobs do Azure, Azure Files, Tabelas do Azure   |
| Desempenho                 | Já otimizado                      | Otimize à medida que desenvolve                  |
| Preços                     | Gratuito, são cobrados encargos de saída de dados      | Gratuito, são cobrados encargos de saída de dados        |

### <a name="continuous-data-ingestion-over-network"></a>Ingestão de dados contínua pela rede

| Recurso                                       | Data Box Gateway | Data Box Edge   | Fábrica de dados do Azure        |
|----------------------------------|-----------------------------------------|--------------------------|---------------------------|
| Fator forma                                   | Dispositivo virtual             | Dispositivo físico          | Serviço no portal do Azure, agente local                                                            |
| Hardware                                      | Seu hipervisor            | Fornecido pela Microsoft    | NA                                                            |
| Esforço de configuração inicial                          | Baixo (< 30 min.)            | Moderado (~ acoplar horas) | Grande (~dias)                                                 |
| Formato de Dados                                   | Blobs do Azure, Azure Files   | Blobs do Azure, Azure Files | [Dá suporte a mais de 70 conectores de dados para os formatos e armazenamentos de dados](../../data-factory/copy-activity-overview.md#supported-data-stores-and-formats)|
| Pré-processamento de dados                           | Não                         | Sim, via computação de borda    | Sim                                                           |
| Cache local<br>(para armazenar dados locais)    | Sim                        | Sim                      | Não                                                            |
| Transferência de outras nuvens                    | Não                         | Não                       | Sim                                                           |
| Preços                                       | [Preços](https://azure.microsoft.com/pricing/details/storage/databox/gateway/)                    | [Preços](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                  | [Preços](https://azure.microsoft.com/pricing/details/data-factory/)                                                       |

## <a name="next-steps"></a>Próximas etapas

- [Transferir dados com AzCopy](./storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).
- [Mais informações sobre transferência de dados com APIs REST do Armazenamento](/dotnet/api/overview/azure/storage).
- Entenda como:
    - [Transferir dados com o Data Box Gateway](../../databox-gateway/data-box-gateway-deploy-add-shares.md).
    - [Transforme dados com data Box Edge antes de enviar para o Azure](../../databox-online/azure-stack-edge-deploy-configure-compute.md).
- [Saiba como transferir dados com o Azure Data Factory](../../data-factory/tutorial-bulk-copy-portal.md).