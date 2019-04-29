---
title: Opções de transferência de dados do Azure para grandes conjuntos de dados com largura de banda de rede ou nenhuma | Microsoft Docs
description: Saiba como escolher uma solução do Azure para transferência de dados quando você tem uma largura de banda de rede limitada ou ausente em seu ambiente e você planeja transferir grandes conjuntos de dados.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 4c4ac9489b9613b2eeaf26a3df9f4cbc664a1026
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730683"
---
# <a name="data-transfer-for-large-datasets-with-low-or-no-network-bandwidth"></a>Transferência de dados para conjuntos de dados grandes com largura de banda de rede baixa ou ausente
 
Este artigo apresenta uma visão geral das soluções de transferência de dados quando você tem largura de banda da rede limitada ou ausente em seu ambiente e planeja transferir conjuntos de dados grandes. O artigo também descreve as opções de transferência de dados recomendadas e a respectiva matriz das principais funcionalidades para esse cenário.

Para entender uma visão geral de todas as opções de transferência de dados disponíveis, vá para [Escolher uma solução de transferência de dados do Azure](storage-choose-data-transfer-solution.md).

## <a name="offline-transfer-or-network-transfer"></a>Transferência offline ou transferência de rede

Conjuntos de dados grandes implicam que você tem de alguns TBs a alguns PBs de dados. Você tem largura de banda de rede limitada a ausente, sua rede é lenta ou não é confiável. Além disso:

- Você está limitado pelos custos de transferência pela rede de seus ISPs (Provedores de Serviços de Internet).
- As políticas organizacionais ou de segurança não permitem conexões de saída ao lidar com os dados confidenciais.

Em todas as instâncias acima, use um dispositivo físico para executar uma transferência de dados em massa única. Escolha entre dispositivos Data Box Disk, Data Box, Data Box Heavy fornecidos pela Microsoft ou Importação/Exportação usando seus próprios discos.

Para confirmar se um dispositivo físico é a opção correta, use a tabela a seguir. Ela mostra o tempo projetado para transferência de dados de rede, para várias larguras de banda disponíveis (supondo 90% da utilização). Se a transferência pela rede estiver projetada para ser muito lenta, você deverá usar um dispositivo físico.  

![Transferência pela rede ou transferência offline](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

## <a name="recommended-options"></a>Opções recomendadas

As opções disponíveis neste cenário são dispositivos para transferência offline do Azure Data Box ou Importação/Exportação do Azure.

- **Família Azure Data Box para transferências offline** – use dispositivos do Data Box fornecidos pela Microsoft para mover grandes quantidades de dados para o Azure quando você estiver limitado por tempo, disponibilidade de rede ou custos. Copie dados locais usando ferramentas como o Robocopy. Dependendo do tamanho dos dados destinados à transferência, você pode escolher entre Data Box Disk, Data Box ou Data Box Heavy.
- **Importação/Exportação do Azure** – use o serviço de Importação/Exportação do Azure enviando suas próprias unidades de disco para importar com segurança grandes quantidades de dados para o Armazenamento de Blobs do Azure e Azure Files. Este serviço também pode ser usado para transferir dados do armazenamento de Blobs do Azure para as unidades de disco e enviar para seu site local.

## <a name="comparison-of-key-capabilities"></a>Comparação das principais funcionalidades

A tabela a seguir resume as diferenças nas principais funcionalidades.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy              |    Importar/exportar                       |
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


## <a name="next-steps"></a>Próximas etapas

- Entenda como

    - [Transferir dados com o Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Transferir dados com o Data Box](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
    - [Transferir dados com Importação/Exportação](/azure/storage/common/storage-import-export-data-to-blobs).
