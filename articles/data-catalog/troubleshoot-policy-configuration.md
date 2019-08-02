---
title: Como solucionar problemas de catálogo de dados do Azure
description: Este artigo descreve as preocupações comuns de solução de problemas para os recursos do catálogo de dados do Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 06/13/2019
ms.openlocfilehash: ed74e90e5e8ed55b75968f51cb50e6a1b4cdd75d
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203500"
---
# <a name="troubleshooting-azure-data-catalog"></a>Solução de problemas de catálogo de dados do Azure

Este artigo descreve as preocupações comuns de solução de problemas para os recursos do catálogo de dados do Azure. 

## <a name="functionality-limitations"></a>Limitações de funcionalidade

Ao usar o catálogo de dados do Azure, a seguinte funcionalidade é limitada:

- Contas com o tipo **função convidado** não têm suporte. Você não pode adicionar contas de convidados como usuários do catálogo de dados do Azure e os usuários convidados não podem usar o portal em www.azuredatacatalog.com.

- Não há suporte para a criação de recursos do catálogo de dados do Azure usando comandos de modelos do Azure Resource Manager ou o Azure PowerShell.

- O recurso de catálogo de dados do Azure não pode ser movido entre locatários do Azure.

## <a name="azure-active-directory-policy-configuration"></a>Configuração de política do Azure Active Directory

Pode haver uma situação em que você pode entrar no portal do Catálogo de Dados do Azure, mas ao tentar entrar na ferramenta de registro de fonte de dados, poderá receber uma mensagem de erro impedindo a entrada. Esse erro pode ocorrer quando você estiver na rede da empresa ou quando você estiver se conectando de fora da rede da empresa.

A ferramenta de registro usa a *Autenticação de Formulários* para validar logons de usuário no Azure Active Directory. Para entrar com êxito, um administrador do Azure Active Directory deve habilitar a autenticação de formulários na *política de autenticação global*.

Com a política de autenticação global, você pode habilitar a autenticação separadamente para conexões de intranet e extranet, conforme mostrado na imagem a seguir. Erros de entrada podem ocorrer se a autenticação de formulários não está habilitada para a rede do qual você está se conectando.

 ![Política de Autenticação Global do AzureActive Directory](./media/troubleshoot-policy-configuration/global-auth-policy.png)

Para saber mais, confira [Configurando políticas de autenticação](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)).

## <a name="next-steps"></a>Próximas etapas

* [Criar um catálogo de dados do Azure](data-catalog-get-started.md)
