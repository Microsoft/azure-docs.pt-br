---
title: Opções de transferência de dados do Azure para grandes conjuntos de dados com largura de banda de rede moderada a alta | Microsoft Docs
description: Saiba como escolher uma solução do Azure para transferência de dados quando você tem uma largura de banda de rede moderada a alta em seu ambiente e você planeja transferir grandes conjuntos de dados.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 8dd55032c933cdc31b848addfdac991550376dcf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729216"
---
# <a name="data-transfer-for-large-datasets-with-moderate-to-high-network-bandwidth"></a>Transferência de dados para grandes conjuntos de dados com largura de banda de rede moderada a alta
 
Este artigo apresenta uma visão geral das soluções de transferência de dados quando você tem largura de banda da rede moderada a alta em seu ambiente e planeja transferir conjuntos de dados grandes. O artigo também descreve as opções de transferência de dados recomendadas e a respectiva matriz das principais funcionalidades para esse cenário.

Para entender uma visão geral de todas as opções de transferência de dados disponíveis, vá para [Escolher uma solução de transferência de dados do Azure](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Descrição do cenário

Conjuntos de dados grandes referem-se a tamanhos de dados na ordem de TBs a PBs. Largura de banda de rede moderada a alta refere-se a 100 Mbps a 10 Gbps.

## <a name="recommended-options"></a>Opções recomendadas

As opções recomendadas neste cenário dependem de se você tem largura de banda de rede moderada ou alta.

### <a name="moderate-network-bandwidth-100-mbps---1-gbps"></a>Largura de banda da rede moderada (100 Mbps a 1 Gbps)

Com largura de banda da rede moderada, você precisa projetar o tempo de transferência de dados pela rede.

Use a tabela a seguir para estimar o tempo e, com base nisso, escolha entre transferência offline ou transferência pela rede. A tabela mostra o tempo projetado para transferência de dados de rede, para várias larguras de banda de rede disponíveis (supondo 90% da utilização).  

![Transferência pela rede ou transferência offline](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

- Se a transferência pela rede estiver projetada para ser muito lenta, você deverá usar um dispositivo físico. As opções recomendadas neste caso são dispositivos de transferência offline da família do Azure Data Box ou Importação/Exportação do Azure usando seus próprios discos.

    - **Família Azure Data Box para transferências offline** – use dispositivos do Data Box fornecidos pela Microsoft para mover grandes quantidades de dados para o Azure quando você estiver limitado por tempo, disponibilidade de rede ou custos. Copie dados locais usando ferramentas como o Robocopy. Dependendo do tamanho dos dados destinados à transferência, você pode escolher entre Data Box Disk, Data Box ou Data Box Heavy.
    - **Importação/Exportação do Azure** – use o serviço de Importação/Exportação do Azure enviando suas próprias unidades de disco para importar com segurança grandes quantidades de dados para o Armazenamento de Blobs do Azure e Azure Files. Este serviço também pode ser usado para transferir dados do armazenamento de Blobs do Azure para as unidades de disco e enviar para seu site local.

- Se a transferência pela rede estiver projetada para ser razoável, você poderá usar qualquer uma das seguintes ferramentas detalhadas em [Largura de banda de rede alta](#high-network-bandwidth).


### <a name="high-network-bandwidth-1-gbps---100-gbps"></a>Largura de banda de rede de alta (1 Gbps – 100 Gbps)

Se a largura de banda de rede disponível for alta, use uma das ferramentas a seguir.

- **AzCopy** – use essa ferramenta de linha de comando para copiar dados facilmente de e para Blobs do Azure, Arquivos e armazenamento de Tabela com um desempenho ideal. O AzCopy dá suporte à simultaneidade e ao paralelismo e à capacidade de retomar as operações de cópia quando elas forem interrompidas.
- **SDKs/APIs REST do Armazenamento do Azure** – ao criar um aplicativo, você pode desenvolver o aplicativo com relação a APIs REST do Armazenamento do Azure e usar os SDKs de cliente do Azure oferecidos em vários idiomas.
- **Família Azure Data Box para transferências online** – o Data Box Edge e o Data Box Gateway são dispositivos de rede online que podem mover dados para dentro e para fora do Azure. Use o dispositivo físico Data Box Edge quando houver uma necessidade simultânea de ingestão e pré-processamento contínuos de dados antes do upload. O Data Box Gateway é uma versão virtual do dispositivo com os mesmos recursos de transferência de dados. Em cada caso, a transferência de dados é gerenciada pelo dispositivo.
- **Azure Data Factory** – o Data Factory deve ser usado para expandir uma operação de transferência e se houver necessidade de funcionalidades de monitoramento de nível empresarial e de orquestração. Use o Data Factory para transferir regularmente arquivos entre vários serviços do Azure localmente ou usando uma combinação dos dois. com o Data Factory, você pode criar e agendar fluxos de trabalho conduzidos por dados (chamados pipelines) que ingerem dados de armazenamentos de dados diferentes e automatizam a movimentação e a transformação de dados.

## <a name="comparison-of-key-capabilities"></a>Comparação das principais funcionalidades

As tabelas a seguir resumem as diferenças nas principais funcionalidades para as opções recomendadas.

### <a name="moderate-network-bandwidth"></a>Largura de banda da rede moderada

Se estiver usando transferência de dados offline, use a tabela a seguir para entender as diferenças nas principais funcionalidades.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy            |    Importar/exportar                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    Tamanho dos dados                        |    Até 35 TBs                 |    Até 80 TB por dispositivo                       |    Até 800 TB por dispositivo               |    Variável                            |
|    Tipo de dados                        |    Blobs do Azure                  |    Blobs do Azure<br>Arquivos do Azure                    |    Blobs do Azure<br>Arquivos do Azure            |    Blobs do Azure<br>Arquivos do Azure          |
|    Fator forma                      |    5 SSDs por pedido             |    1 X 50 lb dispositivo do tamanho da área de trabalho por pedido    |    1 X ~500 lb. dispositivo grande por pedido    |    Até 10 HDDs/SSDs por pedido        |
|    Tempo de preparação inicial               |    Baixo <br>(15 min)            |    Baixo a moderado <br> (<30 min)               |    Moderado<br>(1 a 2 horas)               |    Moderado a difícil<br>(variável) |
|    Enviar dados para o Azure               |    Sim                          |    sim                                           |    sim                                   |    Sim                                 |
|    Exportar dados do Azure           |    Não                            |    Não                                            |    Não                                     |    Sim                                 |
|    Criptografia                       |    AES 128 bits                  |    AES 256 bits                                   |    AES 256 bits                           |    AES 128 bits                         |
|    Hardware                         |     Fornecido pela Microsoft          |    Fornecido pela Microsoft                            |    Fornecido pela Microsoft                    |    Fornecido pelo cliente                   |
|    interface de rede                |    USB 3.1/SATA                 |    RJ 45, SFP+                                   |    RJ-45, QSFP+                           |    SATA II/SATA III                    |
|    Integração de parceiros              |    Alguns                         |    [Alto](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                          |    [Alto](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                  |    Alguns                                |
|    Remessa                         |    Gerenciado pela Microsoft            |    Gerenciado pela Microsoft                             |    Gerenciado pela Microsoft                     |    Gerenciado pelo cliente                    |
| Use quando os dados são movidos         |Dentro de um limite de comércio|Dentro de um limite de comércio|Dentro de um limite de comércio|Entre fronteiras geográficas, por exemplo, dos EUA para a UE|
|    Preços                          |    [Preços](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Preços](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Preços](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Preços](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


Se estiver usando a transferência de dados online, use a tabela na seção a seguir para largura de banda de rede alta.

### <a name="high-network-bandwidth"></a>Largura de banda da rede alta

|                                     |    Tools AzCopy, <br>Azure PowerShell, <br>CLI do Azure             |    SDKs, APIs REST do Armazenamento do Azure                   |    Gateway de caixa de dados ou a borda da caixa de dados          |    Fábrica de dados do Azure                                            |
|-------------------------------------|------------------------------------|----------------------------------------------|----------------------------------|-----------------------------------------------------------------------|
|    Tipo de dados                  |    Blobs do Azure, Azure Files, Tabelas do Azure    |    Blobs do Azure, Azure Files, Tabelas do Azure    |    Blobs do Azure, Azure Files                           |   Dá suporte a mais de 70 conectores de dados para os formatos e armazenamentos de dados    |
|    Fator forma                |    Ferramentas da linha de comando                        |    Interface programática                    |    A Microsoft fornece um dispositivo virtual <br>ou físico     |    Serviço no portal do Azure                                            |
|    Configuração única inicial     |    Fácil               |    Moderado                       |    Fácil (<30 minutos) para moderar (1 a 2 horas)            |    Amplo                                                          |
|    Pré-processamento de dados              |    Não                                         |    Não                                         |    Sim (com computação de borda)                               |    Sim                                                                |
|    Transferência de outras nuvens       |    Não                                         |    Não                                        |    Não                                                     |    Sim                                                                |
|    Tipo de usuário                        |    Profissional ou desenvolvedor de TI                                       |    Desenvolvimento                                       |    Profissional de TI                                                |    Profissional de TI                                                             |
|    Preços                          |    Gratuito, são cobrados encargos de saída de dados         |    Gratuito, são cobrados encargos de saída de dados         |    [Preços](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                                               |    [Preços](https://azure.microsoft.com/pricing/details/data-factory/)                                                            |

## <a name="next-steps"></a>Próximas etapas

- [Saiba como transferir dados com Importação/Exportação](/azure/storage/common/storage-import-export-data-to-blobs).
- Entenda como

    - [Transferir dados com o Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Transferir dados com o Data Box](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
- [Transferir dados com AzCopy](/azure/storage/common/storage-use-azcopy-v10).
- Entenda como
    - [Transferir dados com o Data Box Gateway](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Transformar dados com o Data Box Edge antes de enviar para o Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Saiba como transferir dados com o Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal).
- Use as APIs REST para transferir dados

    - [In .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [In Java](https://docs.microsoft.com/java/api/overview/azure/storage/client)
