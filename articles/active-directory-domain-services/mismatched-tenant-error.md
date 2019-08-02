---
title: Resolver erros de diretório incompatíveis no Azure AD Domain Services | Microsoft Docs
description: Compreenda e resolva erros de diretórios incompatíveis para domínios gerenciados existentes do Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 676efa155c85ab371ec41c49ad0c15eb2bd5a24a
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68233999"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Resolva erros de diretórios incompatíveis para domínios gerenciados existentes do Azure AD Domain Services
Você tem um domínio gerenciado dos Azure AD Domain Services. Quando navega para o portal do Azure e exibe o domínio gerenciado, você vê a seguinte mensagem de erro:

![Erro de diretórios incompatíveis](./media/getting-started/mismatched-tenant-error.png)

Você não pode administrar esse domínio gerenciado até que o erro seja resolvido.


## <a name="whats-causing-this-error"></a>O que está causando o erro?
Este erro é causado quando seu domínio gerenciado e a rede virtual em que ele está habilitado pertencem a dois locatários diferentes do Azure AD. Por exemplo, você tem um domínio gerenciado chamado “contoso.com” e ele foi habilitado para o locatário do Azure AD da Contoso. No entanto, a rede virtual do Azure em que o domínio gerenciado foi habilitado pertence à Fabrikam – outro locatário do Azure AD.

O novo portal do Azure (e, especificamente, a extensão do Azure AD Domain Services) foi criado com base no Azure Resource Manager. No ambiente do Azure Resource Manager moderno, certas restrições são impostas para fornecer maior segurança e RBAC (controle de acesso baseado em função) aos recursos. Habilitar o Azure AD Domain Services para um locatário do Azure AD é uma operação sensível, pois ela faz com que os hashes de credenciais sejam sincronizados com o domínio gerenciado. Esta operação requer que você seja um administrador de locatários do diretório. Além disso, você precisa ter privilégios administrativos na rede virtual em que habilitar o domínio gerenciado. Para as verificações de RBAC funcionarem de forma consistente, o domínio gerenciado e a rede virtual devem pertencer ao mesmo locatário do Azure AD.

Em resumo, você não pode habilitar um domínio gerenciado para um locatário do Azure AD “contoso.com” em uma rede virtual que pertence a uma assinatura do Azure pertencente a outro locatário do Azure AD, como “fabrikam.com”. 

**Configuração válida**: Neste cenário de implantação, o domínio gerenciado Contoso está habilitado para o locatário Contoso do Azure AD. O domínio gerenciado é exposto em uma rede virtual pertencente a uma assinatura do Azure que pertence ao locatário Contoso do Azure AD. Portanto, tanto o domínio gerenciado quanto a rede virtual pertencem ao mesmo locatário do Azure AD. Essa configuração é válida e tem suporte completo.

![Configuração de locatário válida](./media/getting-started/valid-tenant-config.png)

**Configuração de locatários incompatíveis**: Neste cenário de implantação, o domínio gerenciado Contoso está habilitado para o locatário Contoso do Azure AD. No entanto, o domínio gerenciado é exposto em uma rede virtual pertencente a uma assinatura do Azure que pertence ao locatário Fabrikam do Azure AD. Portanto, o domínio gerenciado e a rede virtual pertencem a locatários diferentes do Azure AD. Essa configuração tem locatários incompatíveis e não tem suporte. A rede virtual precisa ser movida para o mesmo locatário do Azure AD (ou seja, a Contoso) que o domínio gerenciado. Confira a seção [Resolução](#resolution) para obter detalhes.

![Configuração de locatários incompatíveis](./media/getting-started/mismatched-tenant-config.png)

Portanto, quando o domínio gerenciado e a rede virtual em que ele está habilitado pertencem a dois locatários diferentes do Azure AD, você vê este erro.

As regras a seguir se aplicam no ambiente do Resource Manager:
- Um diretório do Azure AD pode ter várias assinaturas do Azure.
- Uma assinatura do Azure pode ter vários recursos, como redes virtuais.
- Um único domínio gerenciado do Azure AD Domain Services fica habilitado para um diretório do Azure AD.
- Um domínio gerenciado do Azure AD Domain Services pode ser habilitado em uma rede virtual pertencente a qualquer uma das assinaturas do Azure no mesmo locatário do Azure AD.


## <a name="resolution"></a>Resolução
Você tem duas opções para resolver o erro de diretórios incompatíveis. Você pode:

- Clicar no botão **Excluir** para excluir o domínio gerenciado existente. Crie o domínio novamente usando o [portal do Azure](https://portal.azure.com), de modo que o domínio gerenciado e a rede virtual em que ele está disponível pertençam ao diretório do Azure AD. Adicione todos os computadores anteriormente adicionados no domínio excluído para o domínio gerenciado recém-criado.

- Mova a assinatura do Azure que contém a rede virtual para o diretório do Azure AD a que o seu domínio gerenciado pertence. Siga as etapas no artigo [Transferir a propriedade de uma assinatura do Azure para outra conta](../billing/billing-subscription-transfer.md).


## <a name="related-content"></a>Conteúdo relacionado
* [Serviços de Domínio do Azure AD - guia de Introdução](create-instance.md)
* [Guia de solução de problemas – Azure AD Domain Services](troubleshoot.md)
