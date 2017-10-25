---
title: Exemplos do Armazenamento do Azure | Microsoft Docs
description: "Exiba, baixe e execute exemplos de código e aplicativos para o Armazenamento do Azure. Descubra exemplos de introdução a blobs, filas, tabelas e arquivos usando as bibliotecas do cliente de armazenamento do Java."
services: storage
documentationcenter: na
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: 
ms.service: storage
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 01/12/2017
ms.author: seguler
ms.openlocfilehash: fd27e1ac9a773e7b0f5245aa74acdb0521cd098c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-storage-samples-using-java"></a>Exemplos de armazenamento do Azure usando Java

## <a name="java-sample-index"></a>Índice de exemplos de Java

A tabela a seguir fornece uma visão geral do nosso repositório de exemplos e os cenários abordados em cada exemplo. Clique nos links para exibir o código de exemplo correspondente no GitHub.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Ponto de extremidade</th><th style="font-size:110%">Cenário</th><th style="font-size:110%">Exemplo de código</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>Blob</b></td>
<td>Acrescentar blob</td> 
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao Serviço Blob do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Blob de blocos</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao Serviço Blob do Azure em Java</a></td>
</tr> 
<tr> 
<td>Criptografia do cliente</td>
<td><a href="https://github.com/Azure-Samples/storage-java-client-side-encryption">Introdução à criptografia no lado do cliente do Azure em Java</a></td>
</tr> 
<tr> 
<td>Copiar blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao Serviço Blob do Azure em Java</a></td>
</tr> 
<tr> 
<td>Create Container</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao Serviço Blob do Azure em Java</a></td>
</tr> 
<tr> 
<td>Delete Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao Serviço Blob do Azure em Java</a></td>
</tr> 
<tr> 
<td>Delete Container</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao Serviço Blob do Azure em Java</a></td>
</tr> 
<tr> 
<td>Metadados/propriedades/estatísticas de blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Introdução ao Serviço Blob do Azure em Java</a></td>
</tr> 
<tr> 
<td>ACL/metadados/propriedades de contêiner</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Introdução ao Serviço Blob do Azure em Java</a></td>
</tr> 
<tr> 
<td>Get Page Ranges</td>
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-test/src/com/microsoft/azure/storage/blob/CloudPageBlobTests.java">Exemplo para testes de blob de páginas</a></td>
</tr> 
<tr> 
<td>Blob/contêiner de concessão</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao Serviço Blob do Azure em Java</a></td>
</tr> 
<tr> 
<td>Blob/contêiner de lista</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao Serviço Blob do Azure em Java</a></td>
</tr> 
<tr> 
<td>Blob de páginas</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao Serviço Blob do Azure em Java</a></td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-test/src/com/microsoft/azure/storage/blob/SasTests.java">Exemplo para testes de SAS</a></td>
</tr>   
<tr> 
<td>Propriedades do serviço</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Introdução ao Serviço Blob do Azure em Java</a></td>
</tr>           
<tr> 
<td>Blob de instantâneo</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao Serviço Blob do Azure em Java</a></td>
</tr> 
<tr> 
<td rowspan="9"><b>Arquivo</b></td>
<td>Criar compartilhamentos/diretórios/arquivos</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Introdução ao Serviço Arquivo do Azure em Java</a></td> 
</tr>
<tr> 
<td>Excluir compartilhamentos/diretórios/arquivos</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Introdução ao Serviço Arquivo do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Propriedades/metadados de diretório</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Introdução ao Serviço Arquivo do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Baixar arquivos</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Introdução ao Serviço Arquivo do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Metadados/propriedades/métricas de arquivo</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Introdução ao Serviço Arquivo do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Propriedades do serviço de arquivo</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Introdução ao Serviço Arquivo do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Listar diretórios e arquivos</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Introdução ao Serviço Arquivo do Azure em Java</a></td> 
</tr>
<tr> 
<td>Listar compartilhamentos</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Introdução ao Serviço Arquivo do Azure em Java</a></td> 
</tr>
<tr> 
<td>Compartilhar metadados/propriedades/estatísticas</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Introdução ao Serviço Arquivo do Azure em Java</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>Fila</b></td>
<td>Adicionar mensagem</td> 
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-samples/src/com/microsoft/azure/storage/queue/gettingstarted/QueueBasics.java">Amostras da Biblioteca do Cliente Java de Armazenamento</a></td> 
</tr> 
<tr> 
<td>Criptografia do cliente</td> 
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-samples/src/com/microsoft/azure/storage/encryption/queue/gettingstarted/QueueGettingStarted.java">Amostras da Biblioteca do Cliente Java de Armazenamento</a></td> 
</tr> 
<tr> 
<td>Criar filas</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Introdução ao Serviço Fila do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Excluir mensagem/fila</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Introdução ao Serviço Fila do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Espiar mensagem</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Introdução ao Serviço Fila do Azure em Java</a></td> 
</tr> 
<tr> 
<td>ACL/metadados/estatísticas de fila</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Introdução ao Serviço Fila do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Propriedades do serviço de fila</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Introdução ao Serviço Fila do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Atualização de mensagem</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Introdução ao Serviço Fila do Azure em Java</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>Tabela</b></td>
<td>Criar tabela</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableBasics.java">Introdução ao Serviço Tabela do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Excluir entidade/tabela</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableBasics.java">Introdução ao Serviço Tabela do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Inserir/mesclar/substituir entidade</td> 
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-samples/src/com/microsoft/azure/storage/table/gettingtstarted/TableBasics.java">Amostras da Biblioteca do Cliente Java de Armazenamento</a></td> 
</tr> 
<tr> 
<td>Query Entities</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableBasics.java">Introdução ao Serviço Tabela do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Consultar tabelas</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableBasics.java">Introdução ao Serviço Tabela do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Propriedades/ACL de tabela</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableAdvanced.java">Introdução ao Serviço Tabela do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Update Entity</td> 
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-samples/src/com/microsoft/azure/storage/table/gettingtstarted/TableBasics.java">Amostras da Biblioteca do Cliente Java de Armazenamento</a></td> 
</tr> 
</tbody> 
</table>
<br/>

## <a name="azure-code-samples-library"></a>Biblioteca de Exemplos de Código do Azure

Para visualizar a biblioteca completa de exemplos, vá para a biblioteca [Exemplos de código do Azure](https://azure.microsoft.com/resources/samples/?service=storage), que contém exemplos para o Armazenamento do Azure que podem ser baixados e executados localmente. A Biblioteca de Exemplo de Código fornece exemplo de código em formato .zip. Como alternativa, você pode navegar e clonar o repositório GitHub para cada exemplo.

[!INCLUDE [storage-java-samples-include](../../../includes/storage-java-samples-include.md)]

## <a name="getting-started-guides"></a>Guias de introdução

Confira os guias a seguir se você estiver procurando por instruções sobre como instalar e começar a usar as Bibliotecas de cliente de armazenamento do Azure.

* [Introdução ao Serviço Blob do Azure em Java](../blobs/storage-java-how-to-use-blob-storage.md)
* [Introdução ao Serviço Fila do Azure em Java](../storage-java-how-to-use-queue-storage.md)
* [Introdução ao Serviço Tabela do Azure em Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [Introdução ao Serviço Arquivo do Azure em Java](../storage-java-how-to-use-file-storage.md)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre exemplos para outras linguagens:

* .NET: [Exemplos de armazenamento do Azure usando .NET](../storage-samples-dotnet.md)
* Todas as outras linguagens: [Exemplos de Armazenamento do Azure](../storage-samples.md)
