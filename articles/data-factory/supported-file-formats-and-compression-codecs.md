---
title: Formatos de arquivo com suporte por atividade Copy no Azure Data Factory
description: Este tópico descreve os formatos de arquivo e os códigos de compactação com suporte pela atividade Copy no Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jingwang
ms.openlocfilehash: bb773d01124b99b4837f393b610e00ecbfa510fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100364606"
---
# <a name="supported-file-formats-and-compression-codecs-by-copy-activity-in-azure-data-factory"></a>Formatos de arquivo e códigos de compactação com suporte pela atividade Copy no Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*Este artigo aplica-se aos seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [Blob do Azure](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Sistema de Arquivos](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md) e [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Você pode usar a [atividade Copy](copy-activity-overview.md) para copiar arquivos no estado em que se encontram entre dois armazenamentos de dados baseados em arquivo, caso em que os dados são copiados de maneira eficiente sem nenhuma serialização/desserialização. 

Além disso, você pode analisar ou gerar arquivos de um determinado formato. Por exemplo, você pode fazer o seguinte:

* Copiar dados de um banco de dados do SQL Server e gravá-los no Azure Data Lake Storage Gen2 no formato Parquet.
* Copiar arquivos no formato de texto (CSV) do sistema de arquivos local e gravá-los no Armazenamento de Blobs do Azure no formato Avro.
* Copiar arquivos compactados de um sistema de arquivos local, descompactá-los imediatamente e gravar os arquivos extraídos no Azure Data Lake Storage Gen2.
* Copiar dados em formato de texto (CSV) compactado por Gzip do Armazenamento de Blobs do Azure e gravá-los no Banco de Dados SQL do Azure.
* Muitas outras atividades que exigem serialização/desserialização ou compactação/descompactação.

## <a name="next-steps"></a>Próximas etapas

Consulte os outros artigos sobre atividade de cópia:

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Desempenho da atividade de cópia](copy-activity-performance.md)
