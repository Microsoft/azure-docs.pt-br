---
title: Sobre o controle de acesso dos certificados do Azure Key Vault
description: Visão geral do controle de acesso dos certificados do Azure Key Vault
services: key-vault
author: sebansal
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 10/12/2020
ms.author: sebansal
ms.openlocfilehash: 1308debb34d724f93526b776f19e0cbf1914d945
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92128513"
---
# <a name="certificate-access-control"></a>Controle de acesso de certificado

 O controle de acesso para certificados é gerenciado pelo Key Vault e é fornecido no nível de um Key Vault que atua como o contêiner desses certificados. A política de controle de acesso para certificados é diferente da política de controle de acesso para chaves e segredos no mesmo Key Vault. Os usuários podem criar um ou mais cofres para armazenar certificados, para manter a segmentação e gerenciamento de certificados apropriados do cenário.  

 As seguintes permissões podem ser usadas, por entidade de segurança, na entrada de controle de acesso segredos em um cofre de chaves e refletem com maior exatidão as operações permitidas em um objeto de segredo:  

- Permissões para operações de gerenciamento do certificado
  - **obter** : Obtém a versão atual do certificado ou qualquer versão de um certificado
  - **lista** : Lista os certificados atuais ou versões de um certificado  
  - **atualizar** : Atualizar um certificado
  - **criar** : Criar certificado do Key Vault
  - **importar** : Importar o material do certificado em um certificado de Key Vault
  - **excluir** : Excluir um certificado, sua política e todas as suas versões  
  - **recuperar** : Recuperar um certificado excluído
  - **backup** : Faz backup de um certificado em um cofre de chaves
  - **restaurar** : Restaurar um backup de certificado em um Key Vault
  - **managecontacts** : Gerenciar contatos de certificados do Key Vault  
  - **manageissuers** : Gerenciar as autoridades/emissores de certificado do Key Vault
  - **getissuers** : Obter as autoridades/emissores de um certificado
  - **listissuers** : Lista as autoridades/emissores de um certificado  
  - **setissuers** : Cria ou atualiza emissores de certificado do Key Vault  
  - **deleteissuers** : Exclui autoridades/emissores do certificado do Azure Key Vault  
 
- Permissões para operações com privilégio
  - **limpar** : Limpar (exclui permanentemente) um certificado excluído

Para obter mais informações, veja [Operações de certificado na referência de API REST do Key Vault](/rest/api/keyvault). Para obter informações sobre como estabelecer permissões, confira [Cofres – Atualizar política de acesso](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="troubleshoot"></a>Solucionar problemas
Talvez você veja um erro devido a uma política de acesso ausente. Por exemplo, ```Error type : Access denied or user is unauthorized to create certificate```. Para resolver esse erro, você precisará adicionar a permissão certificados/criar.

## <a name="next-steps"></a>Próximas etapas

- [Sobre o Key Vault](../general/overview.md)
- [Sobre chaves, segredos e certificados](../general/about-keys-secrets-certificates.md)
- [Autenticação, solicitações e respostas](../general/authentication-requests-and-responses.md)
- [Guia do Desenvolvedor do Cofre de Chaves](../general/developers-guide.md)
