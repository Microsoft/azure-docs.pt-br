---
title: Comparação de Azure Data Lake Storage Gen1 com o armazenamento de BLOBs
description: Saiba mais sobre as diferenças entre Azure Data Lake Storage Gen1 e o armazenamento de BLOBs do Azure em relação a alguns aspectos principais do processamento de Big Data.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 77ac3c0809c08719d77457c59ef311ad43ef99cd
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108330"
---
# <a name="comparing-azure-data-lake-storage-gen1-and-azure-blob-storage"></a>Comparando o Azure Data Lake Storage Gen1 e o armazenamento de BLOBs do Azure

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)] 

A tabela neste artigo resume as diferenças entre o Azure Data Lake Storage Gen1 e o Armazenamento de Blobs do Azure em alguns aspectos fundamentais do processamento de big data. O Armazenamento de Blobs do Azure é um repositório de objetos escalonável de finalidade geral que foi criado para uma ampla variedade de cenários de armazenamento. O Azure Data Lake Storage Gen1 é um repositório de grande escala otimizado para cargas de trabalho de análise de big data.

| Categoria | Azure Data Lake Storage Gen1 | Armazenamento do Blobs do Azure |
| -------- | ---------------------------- | ------------------ |
| Finalidade |Armazenamento otimizado para cargas de trabalho de análise de big data |Repositório de objeto de uso geral para uma grande variedade de cenários de armazenamento, incluindo análise big data |
| Casos de uso |Análise de streaming interativa em lotes e dados de aprendizado de máquina, como arquivos de log, dados da IoT, fluxos de cliques, grandes conjuntos de dados |Qualquer tipo de dados de texto ou binários, como back-end de aplicativo, dados de backup, armazenamento de mídia para streaming e dados de uso geral. Adicionalmente, suporte total para cargas de trabalho de análise; lote, interativo, análises de streaming e dados do Azure Machine Learning, como arquivos de log, dados IoT, fluxos de cliques, grandes conjuntos de dados |
| Conceitos Principais |A conta do Data Lake Storage Gen1 contém pastas, que, por sua vez, contêm dados armazenados como arquivos |A conta de armazenamento tem contêineres, que, por sua vez, têm dados na forma de blobs |
| Estrutura |Sistema de arquivos hierárquico |Repositório de objetos com namespace simples |
| API |API REST sobre HTTPS |API REST sobre HTTP/HTTPS |
| API no servidor |[API REST compatível com WebHDFS](/rest/api/datalakestore/) |[API REST do Armazenamento de Blobs do Azure](/rest/api/storageservices/Blob-Service-REST-API) |
| Cliente do sistema de arquivos Hadoop |Sim |Sim |
| Operações de dados – autenticação |Com base em [Identidades do Azure Active Directory](../active-directory/develop/authentication-vs-authorization.md) |Com base em segredos compartilhados – [Chaves de Acesso de Conta](../storage/common/storage-account-keys-manage.md) e [Chaves de Assinatura de Acesso Compartilhado](../storage/common/storage-sas-overview.md). |
| Operações de dados – protocolo de autenticação |[OpenID Connect](https://openid.net/connect/). As chamadas devem conter um JWT (token Web JSON) válido emitido por Azure Active Directory.|Message Authentication Code com base em hash (HMAC). As chamadas devem conter um hash SHA-256 codificado na Base64 em uma parte da solicitação HTTP. |
| Operações de dados – autorização |ACLs (Listas de Controle de Acesso) POSIX.  ACLs baseadas em identidades do Azure Active Directory podem ser definidas no arquivo e nível de pasta. |Para autorização no nível de conta — use [Chaves de Acesso da Conta](../storage/common/storage-account-keys-manage.md)<br>Para autorização de conta, contêiner ou blob — use [Chaves de Assinatura de Acesso Compartilhado](../storage/common/storage-sas-overview.md) |
| Operações de dados – auditoria |Disponível. Consulte [aqui](data-lake-store-diagnostic-logs.md) para obter informações. |Disponível |
| Dados de criptografia em repouso |<ul><li>Transparente, lado do servidor</li> <ul><li>Com chaves gerenciadas pelo serviço</li><li>Com chaves gerenciados pelo cliente no Cofre de Chaves do Azure</li></ul></ul> |<ul><li>Transparente, lado do servidor</li> <ul><li>Com chaves gerenciadas pelo serviço</li><li>Com chaves gerenciadas pelo cliente no Azure KeyVault (versão prévia)</li></ul><li>Criptografia do cliente</li></ul> |
| Operações de gerenciamento (por exemplo, criar conta) |[Controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/overview.md) para gerenciamento de conta |[Controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/overview.md) para gerenciamento de conta |
| SDKs de desenvolvedor |.NET, Java, Python, Node.js |.NET, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS |
| Desempenho de Carga de Trabalho de Análise |Desempenho otimizado para cargas de trabalho de análise paralela. Alta taxa de transferência e IOPS. |Desempenho otimizado para cargas de trabalho de análise paralela. |
| Limites de tamanho |Não há limites para tamanhos de conta, tamanhos de arquivo ou número de arquivos |Para limites específicos, consulte [metas de escalabilidade para contas de armazenamento Standard](../storage/common/scalability-targets-standard-account.md) e [escalabilidade e metas de desempenho para o armazenamento de BLOBs](../storage/blobs/scalability-targets.md). Limites de contas maiores disponíveis contactando [Suporte do Azure](https://azure.microsoft.com/support/faq/) |
| Redundância geográfica |Com redundância local (várias cópias de dados em uma região do Azure) |LRS (redundância local), ZRS (redundância de zona), GRS (redundância global), RA-GRS (redundância global com acesso de leitura). Consulte [aqui](../storage/common/storage-redundancy.md) para obter mais informações |
| Estado do serviço |Disponível para o público geral |Disponível para o público geral |
| Disponibilidade regional |Veja [aqui](https://azure.microsoft.com/regions/#services) |Disponível em todas as regiões do Azure |
| Preço |Consulte os [preços](https://azure.microsoft.com/pricing/details/data-lake-store/) |Consulte os [preços](https://azure.microsoft.com/pricing/details/storage/) |