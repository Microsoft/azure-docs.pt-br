---
title: Recursos de armazenamento de blobs no Azure Data Lake Storage Gen2 | Microsoft Docs
description: Saiba quais recursos de armazenamento de blobs você pode usar com Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: normesta
ms.openlocfilehash: 5c9eb1aa2e88019609f5ab26c2e995e4e43063ca
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640814"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Recursos de armazenamento de blobs no Azure Data Lake Storage Gen2

Os recursos de armazenamento de BLOBs, como [log de diagnóstico](../common/storage-analytics-logging.md), [camadas de acesso](storage-blob-storage-tiers.md)e [políticas de gerenciamento do ciclo de vida do armazenamento de BLOBs](storage-lifecycle-management-concepts.md) , agora funcionam com contas que têm um namespace hierárquico. Portanto, você pode habilitar namespaces hierárquicos em suas contas de Armazenamento de Blobs sem perder o acesso a esses recursos.

Esta tabela lista os recursos de armazenamento de blobs que você pode usar com Azure Data Lake Storage Gen2. Os itens destas tabelas serão alterados ao longo do tempo, pois o suporte continuará a ser expandido. Para saber mais sobre problemas específicos associados ao status de visualização de um recurso, consulte o artigo [Problemas conhecidos](data-lake-storage-known-issues.md).

## <a name="supported-blob-storage-features"></a>Recursos do Armazenamento de Blobs compatíveis

A tabela a seguir mostra como cada recurso de armazenamento de BLOBs tem suporte com Data Lake Storage Gen2. Há uma coluna para as camadas de [desempenho](premium-tier-for-data-lake-storage.md) Standard e Premium. 

|Recurso de Armazenamento de Blobs |Standard |Premium |Artigos relacionados |
|---------------|-------------------|---|
|Camada de acesso quente|Disponível para o público geral|Sem suporte|[Armazenamento de Blobs do Azure: camadas de acesso frequentes, esporádicas e de arquivo](storage-blob-storage-tiers.md)|
|Camada de acesso frio|Disponível para o público geral|Sem suporte|[Armazenamento de Blobs do Azure: camadas de acesso frequentes, esporádicas e de arquivo](storage-blob-storage-tiers.md)|
|Eventos|Disponível para o público geral|Disponível para o público geral|[Reagindo a eventos de Armazenamento de Blobs](storage-blob-event-overview.md)|
|Métricas (clássico)|Disponível para o público geral|Disponível para o público geral|[Métricas da análise de Armazenamento do Microsoft Azure (clássico)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Métricas no Azure Monitor|Disponível para o público geral|Visualização|[Métricas do Armazenamento do Microsoft Azure no Azure Monitor](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Comandos de PowerShell do armazenamento de blobs|Disponível para o público geral|Disponível para o público geral|[Início Rápido: Carregar, baixar e listar blobs com o PowerShell](storage-quickstart-blobs-powershell.md)|
|Comandos da CLI do Azure para armazenamento de blobs|Disponível para o público geral|Disponível para o público geral|[Início Rápido: Criar, baixar e listar blobs com a CLI do Azure](storage-quickstart-blobs-cli.md)|
|APIs de armazenamento de Blob|Disponível para o público geral|Disponível para o público geral|[Início Rápido: Biblioteca de clientes do Armazenamento de blobs v12 para .NET](storage-quickstart-blobs-dotnet.md)<br>[Início Rápido: Gerenciar blobs com o SDK do Java v12](storage-quickstart-blobs-java.md)<br>[Início Rápido: Gerenciar blobs com o SDK do Python v12](storage-quickstart-blobs-python.md)<br>[Início Rápido: Gerenciar blobs com o SDK do JavaScript v12 no Node.js](storage-quickstart-blobs-nodejs.md)|
|Logs de diagnóstico|Disponível para o público geral|Visualização |[Registro em log da Análise de Armazenamento do Azure](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Camada de acesso aos arquivos|Disponível para o público geral|Sem suporte|[Armazenamento de Blobs do Azure: camadas de acesso frequentes, esporádicas e de arquivo](storage-blob-storage-tiers.md)|
|Políticas de gerenciamento do ciclo de vida (camadas)|Disponível para o público geral|Ainda não compatível|[Gerenciar o ciclo de vida de armazenamento de BLOBs do Azure](storage-lifecycle-management-concepts.md)|
|Políticas de gerenciamento do ciclo de vida (excluir BLOB)|Disponível para o público geral|Disponível para o público geral|[Gerenciar o ciclo de vida de armazenamento de BLOBs do Azure](storage-lifecycle-management-concepts.md)|
|Logon no Azure Monitor|Visualização |Visualização|[Monitorar o Armazenamento do Microsoft Azure](./monitor-blob-storage.md)|
|Instantâneos|Visualização<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|Visualização<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|[Instantâneos de BLOB](snapshots-overview.md)|
|Sites estáticos|Visualização<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|Visualização<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|[Hospedagem de site estático no Armazenamento do Azure](storage-blob-static-website.md)|
|Armazenamento imutável|Visualização<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|Visualização<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|[Armazenar dados de blob comercialmente críticos com armazenamento imutável](storage-blob-immutable-storage.md)|
|Exclusão reversível do contêiner|Visualização|Visualização|[Exclusão reversível para contêineres (visualização)](soft-delete-container-overview.md)|
|Inventário de armazenamento do Azure|Visualização|Visualização|[Usar o inventário de armazenamento do Azure para gerenciar dados de BLOB (versão prévia)](blob-inventory.md)|
|Domínios personalizados|Versão Prévia<div role="complementary" aria-labelledby="preview-form-2"><sup>2</sup></div>|Versão Prévia<div role="complementary" aria-labelledby="preview-form-2"><sup>2</sup></div>|[Mapear um domínio personalizado para um ponto de extremidade do armazenamento de blobs do Azure](storage-custom-domain-name.md)|
|Exclusão reversível de BLOB|Ainda não compatível|Ainda não compatível|[Exclusão reversível para blobs](./soft-delete-blob-overview.md)|
|Blobfuse|Disponível para o público geral|Disponível para o público geral|[Como montar o Armazenamento de Blobs como um sistema de arquivos com blobfuse](storage-how-to-mount-container-linux.md)|
|Acesso público anônimo |Disponível para o público geral|Disponível para o público geral| Consulte [Configurar acesso de leitura público anônimo para contêineres e blobs](anonymous-read-access-configure.md).|
|Failover de conta gerenciada pelo cliente|Ainda não compatível|Ainda não compatível|[Recuperação de desastre e failover de conta](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Chaves fornecidas pelo cliente|Ainda não compatível|Ainda não compatível|[Fornecer uma chave de criptografia em uma solicitação para o armazenamento de BLOBs](encryption-customer-provided-keys.md)|
|Escopos de criptografia|Ainda não compatível|Ainda não compatível|[Criar e gerenciar escopos de criptografia](encryption-scope-manage.md)|
|Feed de alteração|Ainda não compatível|Ainda não compatível|[Suporte ao feed de alterações no armazenamento de blobs do Azure](storage-blob-change-feed.md)|
|Replicação de objeto|Ainda não compatível|Ainda não compatível|[Configurar a replicação de objeto para BLOBs de blocos](object-replication-configure.md)|
|Controle de versão de BLOB|Ainda não compatível|Ainda não compatível|[Habilitar e gerenciar o controle de versão de blob](versioning-enable.md)|

<div id="preview-form"><sup>1</sup> Para usar instantâneos, armazenamento imutável ou sites estáticos com Data Lake Storage Gen2, você precisa inscrever-se na versão prévia ao concluir este <a href=https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VUOUc3NTNQSUdOTjgzVUlVT1pDTzU4WlRKRy4u>formulário</a>.  </div>
<div id="preview-form-2"><sup>2</sup> Um nome de domínio personalizado pode mapear somente para o serviço BLOB ou ponto de extremidade estático do site. Não há suporte para o ponto de extremidade de armazenamento Data Lake. </a> .  </div>

## <a name="see-also"></a>Confira também

- [Problemas conhecidos com o Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Serviços do Azure compatíveis com o Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Plataformas de software livre compatíveis com o Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Acesso de vários protocolos no Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)