---
title: Fontes de dados e tipos de arquivo compatíveis
description: Este artigo fornece detalhes conceituais sobre as fontes de dados e tipos de arquivo com suporte no alcance.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: references_regions
ms.openlocfilehash: 3b19fab33d0c8f53025605fd14fe65f08e660392
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677927"
---
# <a name="supported-data-sources-and-file-types-in-azure-purview"></a>Fontes de dados e tipos de arquivo com suporte no Azure alcance

Este artigo discute as fontes de dados com suporte, os tipos de arquivo e os conceitos de verificação no alcance.

## <a name="supported-data-sources"></a>Fontes de dados com suporte

O Azure alcance dá suporte às seguintes fontes:

| Tipo de repositório | Tipo de autenticação com suporte | Configurar verificações via UX/PowerShell |
| ---------- | ------------------- | ------------------------------ |
| SQL Server local                   | Autenticação do SQL                        | DIRETIVAS                                |
| Azure Synapse Analytics (antigo SQL DW)            | Autenticação do SQL, entidade de serviço, MSI               | DIRETIVAS                             |
| Banco de dados SQL do Azure (DB)                  | Autenticação do SQL, entidade de serviço, MSI               | DIRETIVAS |
| Instância Gerenciada do Banco de Dados SQL do Azure      | Autenticação do SQL, entidade de serviço, MSI               | DIRETIVAS    |
| Armazenamento do Blobs do Azure                       | Chave de conta, entidade de serviço, MSI | DIRETIVAS            |
| Azure Data Explorer                      | Entidade de Serviço                              | DIRETIVAS            |
| Azure Data Lake Storage Gen1 (ADLS Gen1) | Entidade de serviço, MSI                              | DIRETIVAS            |
| Azure Data Lake Storage Gen2 (ADLS Gen2) | Chave de conta, entidade de serviço, MSI            | DIRETIVAS            |
| Azure Cosmos DB                          | Chave de conta                                    | DIRETIVAS            |


> [!Note]
> O Azure Data Lake Storage Gen2 já se encontra disponível ao público em geral. É recomendável que você comece a usá-lo hoje mesmo. Para saber mais, consulte a [página do produto](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/).

## <a name="file-types-supported-for-scanning"></a>Tipos de arquivo com suporte para verificação

Os seguintes tipos de arquivo têm suporte para verificação, para extração de esquema e classificação, quando aplicável:

- Formatos de arquivo estruturados com suporte pela extensão: AVRO, ORC, PARQUET, CSV, JSON, PSV, SSV, TSV, TXT, XML, GZIP
- Formatos de arquivo de documento com suporte pela extensão: DOC, DOCM, DOCX, ponto, ODP, ODS, ODT, PDF, POT, PPS, PPSX, PPT, PPTM, PPTX, XLC, XLS, XLSB, XLSM, XLSX, XLT
- O alcance também dá suporte a extensões de arquivo personalizadas e analisadores personalizados.
 
> [!Note]
> Todo arquivo gzip deve ser mapeado para um único arquivo CSV no. Os arquivos gzip estão sujeitos às regras de classificação personalizada e do sistema. No momento, não há suporte para a verificação de um arquivo gzip mapeado para vários arquivos no ou qualquer tipo de arquivo diferente de CSV. 

## <a name="sampling-within-a-file"></a>Amostragem dentro de um arquivo

Na terminologia do alcance,
- Exame de L1: extrai informações básicas e metadados como nome de arquivo, tamanho e nome totalmente qualificado
- Verificação de L2: extrai o esquema para os tipos de arquivo estruturado e tabelas de banco de dados
- Exame L3: extrai o esquema onde aplicável e sujeita o arquivo de amostra para as regras de classificação personalizada e do sistema

Para todos os formatos de arquivo estruturados, os arquivos de exemplo do alcance scanner são os seguintes:

- Para tipos de arquivo estruturados, ele obtém 128 linhas em cada coluna ou 1 MB, o que for menor.
- Para formatos de arquivo de documento, ele obtém 20 MB de cada arquivo.
    - Se um arquivo de documento for maior que 20 MB, ele não estará sujeito a uma verificação profunda (sujeito à classificação). Nesse caso, o alcance captura apenas metadados básicos, como o nome do arquivo e o nome totalmente qualificado.

## <a name="resource-set-file-sampling"></a>Amostragem do arquivo de conjunto de recursos

Uma pasta ou um grupo de arquivos de partição é detectado como um *conjunto de recursos* no alcance, se ele corresponder a uma política de conjunto de recursos do sistema ou a uma política de conjunto de recursos definida pelo cliente. Se um conjunto de recursos for detectado, alcance obterá uma amostra de cada pasta que ele contém. Saiba mais sobre os conjuntos de recursos [aqui](concept-resource-sets.md).

Amostragem de arquivo para conjuntos de recursos por tipos de arquivo:

- **Arquivos delimitados (CSV, PSV, SSV, TSV)** -1 em 100 arquivos são amostrados (verificação L3) em uma pasta ou grupo de arquivos de partição que são considerados um ' conjunto de recursos '
- **Data Lake tipos de arquivo (parquet, Avro, Orc)** -1 em 18446744073709551615 (tempo máximo) são amostrados (exame L3) em uma pasta ou grupo de arquivos de partição que são considerados um *conjunto de recursos*
- **Outros tipos de arquivo estruturados (JSON, XML, txt)** -1 em 100 arquivos são amostrados (verificação L3) em uma pasta ou grupo de arquivos de partição que são considerados um ' conjunto de recursos '
- **Objetos SQL e entidades CosmosDB** -cada arquivo é examinado em L3.
- **Tipos de arquivo de documento** -cada arquivo é examinado em L3. Padrões de conjunto de recursos não se aplicam a esses tipos de arquivo.

## <a name="scan-regions"></a>Verificar regiões
A seguir está uma lista de todas as regiões da fonte de dados do Azure (data center) em que o scanner alcance é executado. Se sua fonte de dados do Azure estiver em uma região fora dessa lista, o verificador será executado na região de sua instância do alcance.
 
### <a name="purview-scanner-regions"></a>Regiões do scanner alcance

- Leste dos EUA
- EastUs2 
- SouthCentralUS
- Oeste dos EUA
- WestUs2
- SoutheastAsia
- WestEurope
- NorthEurope
- UkSouth
- AustraliaEast
- Canadá Central
- BrazilSouth
- CentralIndia
- JapanEast
- SouthAfricaNorth
- FranceCentral

## <a name="classification"></a>classificação

Todas as regras de classificação do sistema 105 se aplicam a formatos de arquivo estruturados. Somente as regras de classificação do MCE se aplicam aos tipos de arquivo de documento (não os padrões de Regex nativos de verificação de dados, a detecção baseada em filtro de flor). Para obter mais informações sobre as classificações com suporte, consulte [classificações com suporte no Azure alcance](supported-classifications.md).

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: executar o kit de início e verificar dados](tutorial-scan-data.md)
- [Gerenciar fontes de dados no Azure alcance (versão prévia)](manage-data-sources.md)