---
title: Autorizar acesso ao Armazenamento do Microsoft Azure | Microsoft Docs
description: Saiba mais sobre as diferentes maneiras de autorizar acesso ao Armazenamento do Microsoft Azure, incluindo o Azure Active Directory, a autenticação de Chave Compartilhada ou assinaturas de acesso compartilhado.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: a0717785f4f9c1c21a18d081d157a6cdc8c12f18
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371191"
---
# <a name="authorizing-access-to-azure-storage"></a>Autorizar acesso ao Armazenamento do Microsoft Azure

Sempre que você acessar os dados na conta de armazenamento, seu cliente fará uma solicitação sobre HTTP/HTTPS para Armazenamento do Microsoft Azure. Toda solicitação para um recurso seguro deve ser autorizada para que o serviço garanta que o cliente tenha as permissões necessárias para acessar os dados.

A tabela a seguir descreve as opções que o armazenamento do Azure oferece para autorizar o acesso a recursos:

|  |Chave compartilhada (chave da conta de armazenamento)  |Assinatura de acesso compartilhado (SAS)  |Active Directory do Azure (Azure AD)  |Acesso de leitura público anônimo  |
|---------|---------|---------|---------|---------|
|Blobs do Azure     |[Com suporte](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Com suporte](storage-dotnet-shared-access-signature-part-1.md)         |[Com suporte](storage-auth-aad.md)         |[Com suporte](../blobs/storage-manage-access-to-resources.md)         |
|Arquivos do Azure (SMB)     |[Com suporte](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |Sem suporte         |[Com suporte, somente com os serviços de domínio do AAD](../files/storage-files-active-directory-overview.md)         |Sem suporte         |
|Arquivos do Azure (REST)     |[Com suporte](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Com suporte](storage-dotnet-shared-access-signature-part-1.md)         |Sem suporte         |Sem suporte         |
|Filas do Azure     |[Com suporte](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Com suporte](storage-dotnet-shared-access-signature-part-1.md)         |[Com suporte](storage-auth-aad.md)         |Sem suporte         |
|Tabelas do Azure     |[Com suporte](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Com suporte](storage-dotnet-shared-access-signature-part-1.md)         |Sem suporte         |Sem suporte         |

Cada opção de autorização é descrita brevemente abaixo:

- **Integração do Azure Active Directory (Azure AD)** para BLOBs e filas. O Azure AD fornece RBAC (controle de acesso baseado em função) para um controle refinado sobre o acesso de um cliente a recursos em uma conta de armazenamento. Para obter mais informações sobre a integração do Azure AD para BLOBs e filas, consulte [Autenticando solicitações para o armazenamento do Azure usando o Azure Active Directory](storage-auth-aad.md).

- **Integração de Azure AD Domain Services (DS) (versão prévia)** para arquivos. Os arquivos do Azure oferecem suporte à autenticação baseada em identidade sobre o protocolo SMB por meio do Azure AD DS. Isso fornece ao RBAC um controle refinado sobre o acesso de um cliente aos recursos em uma conta de armazenamento. Para obter mais informações sobre a integração do AD do Azure para arquivos usando os serviços de domínio, consulte [visão geral do suporte à autenticação do AAD DS (serviço de domínio Azure Active Directory de arquivos do Azure) para acesso SMB (versão prévia)](../files/storage-files-active-directory-overview.md).

- **Autorização de chave compartilhada** para blobs, arquivos, filas e tabelas. Um cliente usando a Chave Compartilhada passa um cabeçalho com cada solicitação assinada usando a chave de acesso da conta de armazenamento. Para obter mais informações, consulte [Autorizar com Chave Compartilhada](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/).
- **Assinaturas de acesso compartilhado** para blobs, arquivos, filas e tabelas. SAS (assinaturas de acesso compartilhado) fornecem acesso delegado limitado a recursos em uma conta de armazenamento. Adicionar restrições no intervalo de tempo para o qual a assinatura é válida ou nas permissões concedidas fornecerá flexibilidade no gerenciamento de acesso. Para saber mais, confira [Usar SAS (Assinaturas de Acesso Compartilhado)](storage-dotnet-shared-access-signature-part-1.md) para saber mais.
- **Acesso de leitura pública anônimo** para contêineres e blobs. A autorização não é necessária. Para obter mais informações, confira [Gerenciar acesso anônimo de leitura aos contêineres e blobs](../blobs/storage-manage-access-to-resources.md).  

Por padrão, todos os recursos no Armazenamento do Microsoft Azure são protegidos e estão disponíveis apenas para o proprietário da conta. Embora seja possível usar qualquer uma das estratégias de autorização descritas acima para conceder aos clientes o acesso a recursos na conta de armazenamento, a Microsoft recomenda o uso do Azure AD quando possível para obter máxima segurança e facilidade de uso. 
