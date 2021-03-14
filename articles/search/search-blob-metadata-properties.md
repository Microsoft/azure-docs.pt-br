---
title: Propriedades de metadados de conteúdo
titleSuffix: Azure Cognitive Search
description: As propriedades de metadados de documentos podem fornecer conteúdo a campos em um índice de pesquisa ou informações que informam o comportamento de indexação em tempo de execução. Este artigo lista as propriedades de metadados com suporte no Azure Pesquisa Cognitiva.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/22/2021
ms.openlocfilehash: cbb35f596a1d32816d1a73b462bf590d9dde0d52
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668411"
---
# <a name="content-metadata-properties-used-in-azure-cognitive-search"></a>Propriedades de metadados de conteúdo usadas no Azure Pesquisa Cognitiva

O SharePoint Online e o armazenamento de BLOBs do Azure podem conter vários conteúdos, e muitos desses tipos de conteúdo têm propriedades de metadados que podem ser úteis para indexar. Assim como você pode criar campos de pesquisa para propriedades de blob padrão como **`metadata_storage_name`** , você pode criar campos para propriedades de metadados que são específicas para um formato de documento.

## <a name="supported-document-formats"></a>Formatos de documento com suporte

O Pesquisa Cognitiva dá suporte à indexação de BLOB e à indexação de documentos do SharePoint Online para os seguintes formatos de documento:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="properties-by-document-format"></a>Propriedades por formato de documento

A tabela a seguir resume o processamento feito para cada formato de documento e descreve as propriedades de metadados extraídas por um indexador de BLOB e o indexador do SharePoint Online.

| Formato de documento/tipo de conteúdo | Metadados extraídos | Detalhes do processamento |
| --- | --- | --- |
| HTML (texto/HTML) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Remoção da marcação HTML e extração do texto |
| PDF (aplicativo/PDF) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Extração do texto, incluindo documentos incorporados (excluindo imagens) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extração de texto, incluindo documentos incorporados |
| DOC (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extração de texto, incluindo documentos incorporados |
| DOCM (aplicativo/vnd.ms-word.document. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extração de texto, incluindo documentos incorporados |
| XML do WORD (application/vnd. ms-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Remoção da marcação XML e extração do texto |
| WORD 2003 XML (application/vnd. ms-WordML) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |Remoção da marcação XML e extração do texto |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extração de texto, incluindo documentos incorporados |
| XLS (application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extração de texto, incluindo documentos incorporados |
| XLSM (application/vnd. MS-Excel. Sheet. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extração de texto, incluindo documentos incorporados |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extração de texto, incluindo documentos incorporados |
| PPT (application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extração de texto, incluindo documentos incorporados |
| PPTM (application/vnd. ms-PowerPoint. Presentation. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extração de texto, incluindo documentos incorporados |
| MSG (application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Extração de texto, incluindo texto extraído de anexos. `metadata_message_to_email``metadata_message_cc_email`e `metadata_message_bcc_email` são coleções de cadeias de caracteres, o restante dos campos são cadeias.|
| ODT (application/vnd. Oasis. OpenDocument. Text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extração de texto, incluindo documentos incorporados |
| ODS (application/vnd. Oasis. OpenDocument. Spreadsheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extração de texto, incluindo documentos incorporados |
| ODP (application/vnd. Oasis. OpenDocument. Presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |Extração de texto, incluindo documentos incorporados |
| ZIP (application/zip) |`metadata_content_type` |Extração do texto de todos os documentos no arquivo |
| GZ (aplicativo/gzip) |`metadata_content_type` |Extração do texto de todos os documentos no arquivo |
| EPUB (Application/ePub + zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |Extração do texto de todos os documentos no arquivo |
| XML (application/xml) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |Remoção da marcação XML e extração do texto |
| JSON (application/json) |`metadata_content_type`<br/>`metadata_content_encoding` |Extrair texto<br/>OBSERVAÇÃO: se você precisar extrair vários campos de documento de um blob JSON, consulte [Como indexar blobs JSON](search-howto-index-json-blobs.md) para obter detalhes |
| EML (message/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Extração do texto, incluindo anexos |
| RTF (aplicativo/rtf) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | Extrair texto|
| Texto sem formatação (text/plain) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | Extrair texto|

## <a name="see-also"></a>Confira também

* [Indexadores na Pesquisa Cognitiva do Azure](search-indexer-overview.md)
* [Entender os BLOBs usando o ia](search-blob-ai-integration.md)
* [Visão geral da indexação de BLOB](search-blob-storage-integration.md)
* [Indexação do SharePoint Online](search-howto-index-sharepoint-online.md)