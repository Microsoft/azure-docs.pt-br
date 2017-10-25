---
title: Erro inesperado ao executar o consentimento para um aplicativo | Microsoft Docs
description: "Discute os erros que podem ocorrer durante o processo de consentimento para um aplicativo e o que é possível fazer"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: fd500fdd4c8642bad96dcf71eebcf1fad461a35f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Erro inesperado ao executar o consentimento para um aplicativo

Este artigo discute os erros que podem ocorrer durante o processo de consentimento para um aplicativo. Se você estiver procurando Solucionar Problemas de solicitações de consentimentos inesperados que não contêm mensagens de erro, consulte [Cenários de Autenticação do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios).

Muitos aplicativos que se integram com o Azure Active Directory exigem permissões para acessar outros recursos para serem executados. Quando esses recursos também são integrados com o Azure Active Directory, as permissões para acessá-los são solicitadas usando a estrutura de consentimento comum. 

Isso resulta em uma solicitação de consentimento exibida que geralmente ocorre na primeira vez que um aplicativo é usado, mas também, pode ocorrer em um subsequente uso do aplicativo.

Determinadas condições devem ser verdadeiras para que um usuário conceda as permissões exigidas por um aplicativo. Se essas condições não forem atendidas, vários erros podem ocorrer. Estão incluídos:

## <a name="requesting-not-authorized-permissions-error"></a>Solicitação de erro de permissão não autorizada
* O **AADSTS90093:** &lt;clientAppDisplayName&gt; está solicitando uma ou mais permissões que você não está autorizado a conceder. Contate um administrador que pode consentir pedido em seu nome.

Esse erro ocorre quando um usuário que não é um administrador de empresa tenta usar um aplicativo que está solicitando permissões, as quais somente um administrador pode conceder. Esse erro pode ser resolvido por um administrador concedendo acesso ao aplicativo em nome de sua organização.

## <a name="policy-prevents-granting-permissions-error"></a>Política impede concessão de permissões de erro
* **AADSTS90093:** Um administrador do &lt;NomeExibiçãoLocatário&gt; definiu uma política que impede que você conceda ao &lt;nome do aplicativo&gt; as permissões solicitadas. Contate um administrador de &lt;Nome Exibiçãolocatário&gt; que pode conceder permissões para esse aplicativo em seu nome.

Esse erro ocorre quando um administrador da empresa desativa a capacidade de consentimento dos usuários para aplicativos e, em seguida, um usuário não administrador tenta usar um aplicativo que exige consentimento. Esse erro pode ser resolvido por um administrador concedendo acesso ao aplicativo em nome de sua organização.

## <a name="intermittent-problem-error"></a>Erro de problema intermitente
* **AADSTS90090:** Parece que encontramos um problema intermitente ao registrar as permissões que você tentou conceder a &lt;NomeExibiçãoAplicativoCliente&gt;. tente novamente mais tarde.

Esse erro indica que ocorreu um erro de serviço intermitente. Ele pode ser resolvido tentando consentir ao aplicativo novamente.

## <a name="resource-not-available-error"></a>Erro de recurso não disponível
* **AADSTS65005:** O aplicativo &lt;NomeExibiçãoAplicativoCliente&gt; solicitou permissões para acessar um recurso&lt;NomeExibiçãoAplicativoRecurso&gt; que não está disponível. 

Contate o desenvolvedor do aplicativo.

##  <a name="resource-not-available-in-tenant-error"></a>Recurso não disponível no erro do locatário
* **AADSTS65005:** &lt;NomeExibiçãoAplicativoCliente&gt; está solicitando acesso a um recurso &lt;NomeExibiçãoAplicativoRecurso&gt; que não está disponível em sua organização &lt;NomeExibiçãoLocatário&gt;. 

Certifique-se de que esse recurso está disponível ou contate o administrador de &lt;NomeExibiçãoLocatário&gt;.

## <a name="permissions-mismatch-error"></a>Erro de incompatibilidade de permissões
* **AADSTS65005:** O aplicativo solicitou consentimento para acessar o recurso &lt;NomeExibiçãoAplicativoRecurso&gt;. A solicitação falhou porque não corresponde como o aplicativo configurado previamente durante o registro do aplicativo. Contate o fornecedor do aplicativo.**

Esses erros ocorrem quando o aplicativo que um usuário está tentando consentir está solicitando permissões para acessar um aplicativo de recurso que não pode ser localizado no diretório da organização (locatário). Isso pode ocorrer por vários motivos:

-   O desenvolvedor do aplicativo cliente configurou seu aplicativo incorretamente, fazendo com que solicite acesso a um recurso inválido. Nesse caso, o desenvolvedor do aplicativo deve atualizar a configuração do aplicativo cliente para resolver esse problema.

-   Uma Entidade de Serviço que representa o aplicativo de recurso de destino não existe na organização ou existiu no passado mas foi removido. Para resolver esse problema, uma Entidade de Serviço para o aplicativo de recurso deve ser provisionada na organização para que o aplicativo cliente possa solicitar-lhe permissões. Isso pode acontecer de várias maneiras, dependendo do tipo de aplicativo, incluindo:

    -   Adquirir uma assinatura para o aplicativo de recursos (aplicativos publicados pela Microsoft)

    -   Consentimento para a aplicação de recursos

    -   Conceder as permissões de aplicação através do Azure Portal

    -   Adicionar o aplicativo a partir da Galeria do Aplicativo Azure AD

## <a name="next-steps"></a>Próximas etapas 

[Aplicativos, permissões e consentimento no Azure Active Directory (ponto de extremidade v1)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)<br>

[Escopos, permissões e consentimento no Azure Active Directory (ponto de extremidade v2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


