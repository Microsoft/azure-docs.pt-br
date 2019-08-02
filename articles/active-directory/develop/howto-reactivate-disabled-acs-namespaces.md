---
title: Como reativar espaços de nomes desabilitados do Azure Access Control Service (ACS)
description: Saiba como encontrar e habilitar os namespaces do Serviço de Controle de Acesso do Azure (ACS) e solicitar uma extensão para mantê-los habilitados até 4 de fevereiro de 2019.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: ryanwi
ms.reviewer: jlu
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1cb17109d53c6e08c8607959207e367086032f6d
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482705"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>Como: Reativar namespaces desabilitados do Serviço de Controle de Acesso

Em novembro de 2017, anunciamos que o Serviço de Coleta de Auditoria (ACS), um Serviço do Microsoft Azure Active Directory (Azure AD), está sendo desativado em 7 de novembro de 2018.

Desde então, enviamos emails para o email de administrador das assinaturas do ACS sobre a desativação do ACS 12 meses, 9 meses, 6 meses, 3 meses, 1 mês, 2 semanas, 1 semana e 1 dia antes da data de desativação de 7 de novembro 2018.

Em 3 de outubro de 2018, anunciamos (por e-mail e [uma postagem no blog](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)) uma oferta de extensão a clientes que não podem concluir sua migração antes de 7 de novembro de 2018. O anúncio também continha instruções para solicitar a extensão.

## <a name="why-your-namespace-is-disabled"></a>Por que o seu namespace está desabilitado

Se você não aceitou a extensão, começaremos a desabilitar os namespaces do ACS a partir de 7 de novembro de 2018. Você já deve ter solicitado a extensão até 4 de fevereiro de 2019; caso contrário, não é possível habilitar os namespaces por meio do PowerShell.

> [!NOTE]
> Você deve ser um administrador de serviços ou coadministrador da assinatura para executar os comandos do PowerShell e solicitar uma extensão.

## <a name="find-and-enable-your-acs-namespaces"></a>Localizar e ativar seus namespaces do ACS

Você pode usar o ACS PowerShell para listar todos os namespaces do ACS e reativar os que foram desabilitados.

1. Faça o download e instale o PowerShell do ACS:
    1. Vá para a Galeria do PowerShell e baixe [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
    1. Instale o módulo:

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. Obter uma lista de todos os comandos possíveis:

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        Para obter ajuda sobre um comando específico, execute:

        ```powershell
        Get-Help [Command-Name] -Full
        ```
    
        em que `[Command-Name]` é o nome do comando do ACS.
1. Conecte-se ao ACS usando o cmdlet **Connect-AcsAccount**. 

    Talvez seja necessário alterar a política de execução, executando **Set-ExecutionPolicy** antes de poder executar o comando.
1. Liste suas assinaturas do Azure disponíveis usando o cmdlet **Get-AcsSubscription**.
1. Liste seus namespaces do ACS usando o cmdlet **Get-AcsNamespace**.
1. Confirme se os namespaces estão desabilitados confirmando que `State` é `Disabled`.

    [![Confirme se os namespaces estão desativados](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    Você também pode usar `nslookup {your-namespace}.accesscontrol.windows.net` para confirmar se o domínio ainda estiver ativo.

1. Habilite seu (s) namespace (s) ACS usando o **cmdlet Enable-AcsNamespace**.

    Depois de ativar seu namespace, você pode solicitar uma extensão para que o (s) espaço (s) de nomes não sejam desabilitados novamente antes de 4 de fevereiro de 2019. Após essa data, todas as solicitações para o ACS falhará.

## <a name="request-an-extension"></a>Solicitar uma extensão

Estamos recebendo novas solicitações de extensão desde 21 de janeiro de 2019.

Começaremos a desabilitar namespaces para clientes que solicitaram extensões até 4 de fevereiro de 2019. Você ainda pode reativar namespaces por meio do PowerShell, mas eles serão desativados novamente após 48 horas.

Depois de 4 de março de 2019, os clientes não poderão mais reativar os namespaces por meio do PowerShell.

Extensões adicionais não serão mais aprovadas automaticamente. Se você precisar de mais tempo para migrar, entre em contato com o [Suporte do Azure](https://portal.azure.com/#create/Microsoft.Support) para fornecer uma linha do tempo detalhada da migração.

### <a name="to-request-an-extension"></a>Para solicitar uma extensão

1. Entre no portal do Azure e criar uma [nova solicitação de suporte](https://portal.azure.com/#create/Microsoft.Support).
1. Preencha o novo formulário de solicitação de suporte, conforme mostrado no exemplo a seguir.

    | Campo de solicitação de suporte | Value |
    |-----------------------|--------------------|
    | **Tipo de problema** | `Technical` |
    | **Assinatura** | Defina sua assinatura |
    | **Serviço** | `All services` |
    | **Recurso** | `General question/Resource not available` |
    | **Tipo de problema** | `ACS to SAS Migration` |
    | **Assunto** | Descreva o problema |

   ![Mostra um exemplo de uma nova solicitação de suporte técnico](./media/howto-reactivate-disabled-acs-namespaces/new-technical-support-request.png)

<!--

1. Navigate to your ACS namespace's management portal by going to `https://{your-namespace}.accesscontrol.windows.net`.
1. Select the **Read Terms** button to read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/), which will direct you to a page with the updated Terms of Use.

    [![Select the Read Terms button](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. Select **Request Extension** on the banner at the top of the page. The button will only be enabled after you read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/).

    [![Select the Request Extension button](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. After the extension request is registered, the page will refresh with a new banner at the top of the page.

    [![Updated page with refreshed banner](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)
-->

## <a name="help-and-support"></a>Ajuda e suporte

- Se você tiver problemas depois de seguir estas instruções, entre em contato com [suporte do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
- Se você tiver dúvidas ou comentários sobre a desativação do ACS, entre em contato conosco em acsfeedback@microsoft.com.

## <a name="next-steps"></a>Próximas etapas

- Confira as informações sobre a desativação do ACS em [Como migrar do Serviço de Controle de Acesso do Azure](active-directory-acs-migration.md).
