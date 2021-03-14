---
title: Formatos de arquivo com suporte por atividade de cópia no Azure Data Factory
description: Este tópico descreve os formatos de arquivo e os códigos de compactação que são suportados pela atividade de cópia no Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jingwang
ms.openlocfilehash: bb773d01124b99b4837f393b610e00ecbfa510fb
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364606"
---
# <a name="supported-file-formats-and-compression-codecs-by-copy-activity-in-azure-data-factory"></a>Formatos de arquivo com suporte e codecs de compactação por atividade de cópia no Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*Este artigo se aplica aos seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [blob do Azure](connector-azure-blob-storage.md), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [armazenamento de arquivos do Azure](connector-azure-file-storage.md), [sistema de arquivos](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Você pode usar a [atividade de cópia](copy-activity-overview.md) para copiar arquivos no estado em que se encontram entre dois armazenamentos de dados baseados em arquivo; nesse caso, os dados são copiados com eficiência sem nenhuma serialização ou desserialização. 

Além disso, você também pode analisar ou gerar arquivos de um determinado formato. Por exemplo, você pode executar o seguinte:

* Copie dados de um banco de SQL Server e grave em Azure Data Lake Storage Gen2 no formato parquet.
* Copiar arquivos no formato de texto (CSV) de um sistema de arquivos local e gravar no armazenamento de BLOBs do Azure no formato Avro.
* Copie arquivos compactados de um sistema de arquivos local, descompacte-os imediatamente e grave arquivos extraídos em Azure Data Lake Storage Gen2.
* Copie dados no formato de texto compactado gzip (CSV) do armazenamento de BLOBs do Azure e grave-os no banco de dados SQL do Azure.
* Muitas outras atividades que exigem serialização/desserialização ou compactação/descompactação.

## <a name="next-steps"></a>Próximas etapas

Consulte os outros artigos sobre atividade de cópia:

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Desempenho da atividade de cópia](copy-activity-performance.md)
