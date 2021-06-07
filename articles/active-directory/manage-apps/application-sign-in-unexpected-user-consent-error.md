---
title: Erro inesperado ao executar o consentimento para um aplicativo | Microsoft Docs
description: Discute os erros que podem ocorrer durante o processo de consentimento para um aplicativo e o que é possível fazer
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f829672f88ea848e4611000b54d9cc200bc166d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259970"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Erro inesperado ao executar o consentimento para um aplicativo

Este artigo discute os erros que podem ocorrer durante o processo de consentimento para um aplicativo. Se você estiver solucionando problemas de prompts de consentimento inesperado que não contenham mensagens de erro, consulte [ Cenários de autenticação do Azure AD ](../develop/authentication-vs-authorization.md).

Muitos aplicativos que se integram com o Azure Active Directory exigem permissões para acessar outros recursos para serem executados. Quando esses recursos também são integrados com o Azure Active Directory, as permissões para acessá-los são solicitadas usando a estrutura de consentimento comum. Um prompt de consentimento é exibido, o que geralmente ocorre na primeira vez que um aplicativo é usado, mas também pode ocorrer em um uso subseqüente do aplicativo.

Determinadas condições devem ser verdadeiras para que um usuário conceda as permissões exigidas por um aplicativo. Se essas condições não forem atendidas, os seguintes erros podem ocorrer.

## <a name="requesting-not-authorized-permissions-error"></a>Solicitação de erro de permissão não autorizada
* O **AADSTS90093:** &lt;clientAppDisplayName&gt; está solicitando uma ou mais permissões que você não está autorizado a conceder. Contate um administrador que pode consentir pedido em seu nome.
* **AADSTS90094:** &lt;clientAppDisplayName&gt; precisa de permissão para acessar recursos em sua organização que apenas um administrador pode conceder. Peça a um administrador para conceder permissão ao aplicativo antes de poder usá-lo.

Esse erro ocorre quando um usuário que não é um administrador global tenta usar um aplicativo que está solicitando permissões que apenas um administrador pode conceder. Esse erro pode ser resolvido por um administrador concedendo acesso ao aplicativo em nome de sua organização.

Esse erro também pode ocorrer quando um usuário é impedido de consentir em um aplicativo devido à Microsoft detectar que a solicitação de permissões é arriscada. Nesse caso, um evento de auditoria também será registrado com uma categoria de "ApplicationManagement", o tipo de atividade de "consentimento para o aplicativo" e o motivo do status de "aplicativo arriscado detectado".

Outro cenário no qual esse erro pode ocorrer é quando a atribuição de usuário é necessária para o aplicativo, mas nenhum consentimento de administrador foi fornecido. Nesse caso, o administrador deve primeiro fornecer o consentimento do administrador.   

## <a name="policy-prevents-granting-permissions-error"></a>Política impede concessão de permissões de erro
* **AADSTS90093:** Um administrador do &lt;NomeExibiçãoLocatário&gt; definiu uma política que impede que você conceda ao &lt;nome do aplicativo&gt; as permissões solicitadas. Contate um administrador de &lt;Nome Exibiçãolocatário&gt; que pode conceder permissões para esse aplicativo em seu nome.

Esse erro ocorre quando um administrador global desativa a capacidade dos usuários de consentirem com os aplicativos e, em seguida, um usuário não administrador tenta usar um aplicativo que exige consentimento. Esse erro pode ser resolvido por um administrador concedendo acesso ao aplicativo em nome de sua organização.

## <a name="intermittent-problem-error"></a>Erro de problema intermitente
* **AADSTS90090:** Parece que o processo de login encontrou um problema intermitente registrando as permissões que você tentou conceder a &lt; clientAppDisplayName &gt;. tente novamente mais tarde.

Esse erro indica que ocorreu um erro de serviço intermitente. Ele pode ser resolvido tentando consentir ao aplicativo novamente.

## <a name="resource-not-available-error"></a>Erro de recurso não disponível
* **AADSTS65005:** O aplicativo &lt;NomeExibiçãoAplicativoCliente&gt; solicitou permissões para acessar um recurso&lt;NomeExibiçãoAplicativoRecurso&gt; que não está disponível. 

Contate o desenvolvedor do aplicativo.

##  <a name="resource-not-available-in-tenant-error"></a>Recurso não disponível no erro do locatário
* **AADSTS65005:** &lt;NomeExibiçãoAplicativoCliente&gt; está solicitando acesso a um recurso &lt;NomeExibiçãoAplicativoRecurso&gt; que não está disponível em sua organização &lt;NomeExibiçãoLocatário&gt;. 

Certifique-se de que esse recurso está disponível ou contate o administrador de &lt;NomeExibiçãoLocatário&gt;.

## <a name="permissions-mismatch-error"></a>Erro de incompatibilidade de permissões
* **AADSTS65005:** O aplicativo solicitou consentimento para acessar o recurso &lt;NomeExibiçãoAplicativoRecurso&gt;. A solicitação falhou porque não corresponde como o aplicativo configurado previamente durante o registro do aplicativo. Contate o fornecedor do aplicativo.**

Esses erros ocorrem quando o aplicativo que um usuário está tentando consentir está solicitando permissões para acessar um aplicativo de recurso que não pode ser localizado no diretório da organização (locatário). Essa situação pode ocorrer por vários motivos:

-   O desenvolvedor do aplicativo cliente configurou seu aplicativo incorretamente, fazendo com que solicite acesso a um recurso inválido. Nesse caso, o desenvolvedor do aplicativo deve atualizar a configuração do aplicativo cliente para resolver esse problema.

-   Uma Entidade de Serviço que representa o aplicativo de recurso de destino não existe na organização ou existiu no passado mas foi removido. Para resolver esse problema, uma Entidade de Serviço para o aplicativo de recurso deve ser provisionada na organização para que o aplicativo cliente possa solicitar-lhe permissões. O Service Principal pode ser provisionado de várias maneiras, dependendo do tipo de aplicativo, incluindo:

    -   Adquirir uma assinatura para o aplicativo de recursos (aplicativos publicados pela Microsoft)

    -   Consentimento para a aplicação de recursos

    -   Conceder as permissões de aplicação através do Azure Portal

    -   Adicionar o aplicativo a partir da Galeria do Aplicativo Azure AD

## <a name="risky-app-error-and-warning"></a>Erro e aviso do aplicativo arriscado
* **AADSTS900941:** O consentimento do administrador é necessário. O aplicativo é considerado arriscado. (AdminConsentRequiredDueToRiskyApp)
* Este aplicativo pode ser arriscado. Se você confiar nesse aplicativo, peça ao administrador para conceder acesso.
* **AADSTS900981:** Foi recebida uma solicitação de consentimento de administrador para um aplicativo arriscado. (AdminConsentRequestRiskyAppWarning)
* Este aplicativo pode ser arriscado. Só continue se você confiar neste aplicativo.

Essas duas mensagens serão exibidas quando a Microsoft determinar que a solicitação de consentimento pode ser arriscada. Entre vários outros fatores, isso pode ocorrer se um [Publicador verificado](../develop/publisher-verification-overview.md) não tiver sido adicionado ao registro do aplicativo. O primeiro código de erro e a mensagem serão mostrados aos usuários finais quando o [fluxo de trabalho de consentimento do administrador](configure-admin-consent-workflow.md) estiver desabilitado. O segundo código e a mensagem serão mostrados aos usuários finais quando o fluxo de trabalho de consentimento do administrador estiver habilitado e para administradores. 

Os usuários finais não poderão conceder consentimento a aplicativos que foram detectados como arriscados. Os administradores são capazes de fazer isso, mas devem avaliar o aplicativo com muita cautela e tomar cuidado. Se o aplicativo parecer suspeito após uma análise adicional, ele poderá ser relatado à Microsoft na tela de consentimento. 

## <a name="next-steps"></a>Próximas etapas 

[Aplicativos, permissões e consentimento no Azure Active Directory (ponto de extremidade v1)](../develop/quickstart-register-app.md)<br>

[Escopos, permissões e consentimento no Azure Active Directory (ponto de extremidade v 2.0)](../develop/v2-permissions-and-consent.md)