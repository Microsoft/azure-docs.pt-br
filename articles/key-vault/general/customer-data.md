---
title: Recursos de dados de cliente do Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Saiba mais sobre os dados do cliente, que Azure Key Vault recebem durante a criação ou atualização de cofres, chaves, segredos, certificados e contas de armazenamento gerenciadas.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: reference
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 8806ff41edabcd3c0875d3c02360dc9a275e3878
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96460816"
---
# <a name="azure-key-vault-customer-data-features"></a>Recursos de dados de cliente do Azure Key Vault

Azure Key Vault recebe dados do cliente durante a criação ou atualização de cofres, pools HSM gerenciados, chaves, segredos, certificados e contas de armazenamento gerenciado. Esses dados de cliente estarão diretamente visíveis no portal do Azure e por meio da API REST. Os dados do cliente podem ser editados ou excluídos por atualizar ou excluir o objeto que contém os dados.

Os logs de acesso do sistema são gerados quando um usuário ou aplicativo acessa o Cofre de Chaves. Os logs detalhados de acesso detalhados estão disponíveis para clientes que usam o Azure Insights.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identificando os dados do cliente

As informações a seguir identificam os dados do cliente dentro do Azure Key Vault:

- As políticas de acesso para o Azure Key Vault contêm IDs de objeto que representam os usuários, grupos ou aplicativos
- Os assuntos do certificado podem incluir endereços de email ou outros identificadores do usuário ou organizacionais
- Contatos de certificado podem conter endereços de email do usuário, nomes ou números de telefone
- Emissores de certificado podem conter endereços de email, nomes, números de telefone, credenciais de conta e detalhes organizacionais
- Marcas arbitrárias podem ser aplicadas a objetos no Azure Key Vault. Esses objetos incluem cofres, chaves, segredos, certificados e contas de armazenamento. As marcas usadas podem conter dados pessoais
- Os logs de acesso do Azure Key Vault contêm IDs de objeto, [UPNs](../../active-directory/hybrid/plan-connect-userprincipalname.md)e endereços IP para cada chamada à API REST
- Os logs de diagnóstico do Azure Key Vault podem conter IDs de objeto e endereços de IP para as chamadas de API REST

## <a name="deleting-customer-data"></a>Excluindo os dados do cliente

As mesmas APIs REST, experiência do Portal e SDKs usados para criar cofres, chaves, segredos, certificados e contas de armazenamento gerenciado, também são capazes de atualizar e excluir esses objetos.

A exclusão reversível permite recuperar dados excluídos por 90 dias após a exclusão. Ao usar a exclusão reversível, os dados podem ser excluídos permanentemente antes do período de retenção de 90 dias expiram executando uma operação de limpeza. Se o cofre ou a assinatura tiver sido configurado para operações de limpeza de bloco, não é possível excluir permanentemente os dados até que o período de retenção agendado tenha passado.

## <a name="exporting-customer-data"></a>Exportando os dados do cliente

As mesmas APIs REST, experiência de portal e SDKs usadas para criar cofres, chaves, segredos, certificados e contas de armazenamento gerenciadas também permitem visualizar e exportar esses objetos.

O logging de acesso do Azure Key Vault é um recurso opcional que pode ser ativado para gerar os logs para cada chamada API REST. Esses logs serão transferidos para uma conta de armazenamento em sua assinatura em que você aplica a política de retenção que atenda aos requisitos da sua organização.

Os logs de diagnóstico do Azure Key Vault que contêm dados pessoais podem ser recuperados fazendo uma solicitação de exportação no portal User Privacy. Essa solicitação deve ser feita pelo administrador do locatário.

## <a name="next-steps"></a>Próximas etapas

- [Logs do Cofre da Chave do Azure](logging.md)

- [Visão geral de exclusão reversível do Azure Key Vault](./key-vault-recovery.md)

- [Operações de chave do Azure Key Vault](/rest/api/keyvault/key-operations)

- [Operações de segredo do Azure Key Vault](/rest/api/keyvault/secret-operations)

- [Certificados e políticas do Azure Key Vault](/rest/api/keyvault/certificates-and-policies)

- [Chaves de conta de Armazenamento do Azure Key Vault](/rest/api/keyvault/storage-account-key-operations)