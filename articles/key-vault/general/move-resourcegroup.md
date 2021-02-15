---
title: Azure Key Vault mover um cofre para um grupo de recursos diferente | Microsoft Docs
description: Orientação sobre como mover um cofre de chaves para um grupo de recursos diferente.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/29/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another resource group.
ms.openlocfilehash: ea152e1d78ab1ea610eee5420394c89524673993
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042375"
---
# <a name="moving-an-azure-key-vault-across-resource-groups"></a>Movendo um Azure Key Vault entre grupos de recursos

## <a name="overview"></a>Visão geral

Mover um cofre de chaves entre grupos de recursos é um recurso de cofre de chaves com suporte. Mover um cofre de chaves entre grupos de recursos não afetará as configurações de política de acesso ou firewall do cofre de chaves. Os aplicativos conectados e as entidades de serviço devem continuar funcionando conforme o esperado.

> [!IMPORTANT]
> **Os cofres de chaves usados para a criptografia de disco não podem ser movidos.**
> Se você estiver usando o Key Vault com criptografia de disco para uma VM, o cofre de chaves não poderá ser movido para um grupo de recursos diferente ou uma assinatura enquanto a criptografia de disco estiver habilitada. Você deve desabilitar a criptografia de disco antes de mover o cofre de chaves para um novo grupo de recursos ou assinatura. 

## <a name="design-considerations"></a>Considerações de criação

Sua organização pode ter implementado Azure Policy com imposição ou exclusões no nível do grupo de recursos. Pode haver um conjunto diferente de atribuições de política no grupo de recursos em que o cofre de chaves existe atualmente e no grupo de recursos em que você está movendo o cofre de chaves. Um conflito nos requisitos da política tem o potencial de quebrar seus aplicativos.

### <a name="example"></a>Exemplo

Você tem um aplicativo conectado ao key Vault que cria certificados que são válidos por dois anos. O grupo de recursos em que você está tentando mover o cofre de chaves tem uma atribuição de política que bloqueia a criação de certificados válidos por mais de um ano. Depois de mover o cofre de chaves para o novo grupo de recursos, a operação para criar um certificado válido por dois anos será bloqueada por uma atribuição de política do Azure.

### <a name="solution"></a>Solução

Certifique-se de ir para a página de Azure Policy na portal do Azure e examine as atribuições de política do seu grupo de recursos atual, bem como o grupo de recursos que você está movendo e verifique se não há nenhuma correspondência.

## <a name="procedure"></a>Procedimento

1. Faça logon no Portal do Azure
2. Navegue até o Key Vault
3. Clique na guia "visão geral"
4. Selecione o botão "mover"
5. Selecione "mover para outro grupo de recursos" nas opções suspensas
6. Selecione o grupo de recursos para o qual você deseja mover o cofre de chaves
7. Reconhecer o aviso sobre a movimentação de recursos
8. Selecione "OK"

Key Vault agora avaliará a validade da movimentação de recursos e alertará você sobre quaisquer erros. Se nenhum erro for encontrado, a movimentação de recursos será concluída. 
