---
title: Marcar um aplicativo como “editor verificado” – Plataforma de identidade da Microsoft | Azure | Azure
description: Descreve como marcar um aplicativo como “editor verificado”. Quando um aplicativo é marcado como verificado pelo editor, isso significa que o editor verificou sua identidade usando uma conta do Microsoft Partner Network que concluiu o processo de verificação e associou essa conta do MPN ao registro de aplicativo.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 2b658cdc58777274bb14f9e8069cef2facdb0479
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92363446"
---
# <a name="mark-your-app-as-publisher-verified"></a>Marcar seu aplicativo como “editor verificado”

Quando um registro de aplicativo tem um Publicador verificado, isso significa que o editor do aplicativo [verificou](/partner-center/verification-responses) sua identidade usando sua conta de Microsoft Partner Network (MPN) e associou essa conta MPN ao registro do aplicativo. Este artigo descreve como concluir o processo de [verificação do Publicador](publisher-verification-overview.md) .

## <a name="quickstart"></a>Guia de Início Rápido
Se você já estiver inscrito no Microsoft Partner Network (MPN) e tiver atendido aos [pré-requisitos](publisher-verification-overview.md#requirements), poderá começar imediatamente: 

1. Entre no [portal de registro de aplicativo](https://aka.ms/PublisherVerificationPreview) usando a [autenticação multifator](../fundamentals/concept-fundamentals-mfa-get-started.md)

1. Escolha um aplicativo e clique em **Identidade visual**. 

1. Clique em **Adicionar ID do MPN para verificar o editor** e analise os requisitos listados.

1. Insira sua ID do MPN e clique em **Verificar e salvar**.

Para obter mais detalhes sobre benefícios específicos, requisitos e perguntas frequentes, consulte a [visão geral](publisher-verification-overview.md).


## <a name="mark-your-app-as-publisher-verified"></a>Marcar seu aplicativo como “editor verificado”
Verifique se você atendeu aos [pré-requisitos](publisher-verification-overview.md#requirements) e siga estas etapas para marcar seus aplicativos como “editor verificado”.  

1. Verifique se você está conectado usando a [autenticação multifator](../fundamentals/concept-fundamentals-mfa-get-started.md) a uma conta organizacional (Azure AD) que está autorizada a fazer alterações nos aplicativos que você deseja marcar como Publicador verificado e na conta MPN no Partner Center.

    - No Azure AD, esse usuário deve ser um membro de uma das seguintes [funções](../roles/permissions-reference.md): administrador de aplicativos, administrador de aplicativos de nuvem, administrador global. 

    - No Partner Center, esse usuário deve ter as seguintes [funções](/partner-center/permissions-overview): Administrador de MPN, administrador de contas ou um administrador global (essa é uma função compartilhada controlada no Azure Active Directory). 

1. Navegue até o portal de registro do aplicativo:  

1. Clique em um aplicativo que você deseja marcar como Editor Verificado e abra a folha de identidade visual. 

1. Verifique se o [domínio do Publicador](howto-configure-publisher-domain.md) do aplicativo está definido. 

1. Certifique-se de que o domínio do Publicador ou um [domínio personalizado](../fundamentals/add-custom-domain.md) verificado pelo DNS no locatário corresponda ao domínio do endereço de email usado durante o processo de verificação para sua conta do MPN.

1. Clique em **Adicionar ID do MPN para verificar o editor** próximo à parte inferior da página. 

1. Insira sua **ID do MPN**. Essa ID do MPN deve ser para: 

    - Uma conta do Microsoft Partner Network válida que concluiu o processo de verificação.  

    - A PGA (conta global do parceiro) da sua organização. 

1. Clique em **Verificar e salvar**. 

1. Aguarde o processamento da solicitação. Isso pode levar alguns minutos. 

1. Se a verificação tiver sido bem-sucedida, a janela de verificação do editor será fechada, e você voltará para a folha de identidade visual. Você verá o selo Verificado em azul próximo ao seu **nome de exibição de editor verificado**. 

1. Os usuários que forem solicitados a dar consentimento ao seu aplicativo começarão a ver o selo assim que você tiver finalizado o processo com êxito. Porém, pode levar algum tempo para que isso seja replicado em todo o sistema. 

1. Teste essa funcionalidade. Para isso, entre em seu aplicativo e garanta que o selo Verificado apareça na tela de consentimento. Se você estiver conectado como um usuário que concedeu consentimento ao aplicativo, poderá usar o parâmetro de consulta *prompt=consent* para forçar uma solicitação de consentimento. Esse parâmetro deve ser usado somente para teste e nunca embutido em código nas solicitações do seu aplicativo.

1. Repita esse processo conforme necessário para qualquer outro aplicativo para o qual você deseja que o selo seja exibido. Você pode usar o Microsoft Graph para fazer isso mais rapidamente em massa, e os cmdlets do PowerShell estarão disponíveis em breve. Consulte [Fazer chamadas de Microsoft API Graph](troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls) para obter mais informações. 

É isso! Informe-nos se você tiver comentários sobre o processo, os resultados ou o recurso em geral. 

## <a name="next-steps"></a>Próximas etapas
Se você tiver problemas, leia as [informações de solução de problemas](troubleshoot-publisher-verification.md).
