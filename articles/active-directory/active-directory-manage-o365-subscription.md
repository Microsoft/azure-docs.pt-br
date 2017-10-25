---
title: "Gerenciar o diretório para sua assinatura do Office 365 no Azure | Microsoft Docs"
description: "Gerenciar um diretório de assinatura do Office 365 usando o Azure Active Directory e o portal clássico do Azure"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 746987b7-2dfd-4b35-819d-37c1b65c5c81
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/10/2017
ms.author: curtand
ms.reviewer: rodejo
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 497beb75d1bd4c0709929948a9f857a034364f44
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-the-directory-for-your-office-365-subscription-in-azure"></a>Gerenciar o diretório para sua assinatura do Office 365 no Azure
Este artigo descreve como gerenciar um diretório criado para uma assinatura do Office 365 usando o portal clássico do Azure. Você deve ser o Administrador de Serviços ou coadministrador de uma assinatura do Azure para entrar no portal clássico do Azure. Se ainda não tiver uma assinatura do Azure, você poderá se inscrever em uma [avaliação gratuita de 30 dias](https://azure.microsoft.com/trial/get-started-active-directory/) hoje mesmo e implantar sua primeira solução de nuvem em menos de cinco minutos, usando este link. Use a conta corporativa ou de estudante que usa para entrar no Office 365.

> [!IMPORTANT]
> A Microsoft recomenda que você gerencie o Azure AD usando o [Centro de administração do AD do Azure](https://aad.portal.azure.com) no portal do Azure em vez de usar o portal clássico do Azure mencionado neste artigo.

Depois de concluir a assinatura do Azure, você pode entrar no portal clássico do Azure e acessar serviços do Azure. Clique na extensão do Active Directory para gerenciar o mesmo diretório que autentica os usuários do Office 365.

Se você já tiver uma assinatura do Azure, o processo para gerenciar um diretório adicional também é simples. Por exemplo, Carlos Lima pode ter uma assinatura do Office 365 para Contoso.com. Ele também tem uma assinatura do Azure na qual se inscreveu usando sua conta da Microsoft, msmith@hotmail.com. Nesse caso, ele gerencia dois diretórios.

| Assinatura | Office 365 | As tabelas |
| --- | --- | --- |
|   Nome de exibição | Contoso | Diretório padrão do Azure AD (Azure Active Directory) |
|   Nome de domínio | contoso.com | msmithhotmail.onmicrosoft.com |

Ele deseja gerenciar as identidades de usuário no diretório Contoso enquanto está conectado ao Azure usando sua conta da Microsoft, para que possa habilitar os recursos do Azure AD, como a autenticação multifator. O diagrama a seguir pode ajudar a ilustrar o processo.

![Diagrama para gerenciar dois diretórios independentes](./media/active-directory-manage-o365-subscription/AAD_O365_03.png)

Nesse caso, os dois diretórios são independentes um do outro.

## <a name="to-manage-two-independent-directories"></a>Para gerenciar dois diretórios independentes
Para que Carlos Lima gerencie os dois diretórios enquanto está conectado ao Azure como msmith@hotmail.com, ele precisa concluir as seguintes etapas:

> [!NOTE]
> Essas etapas só podem ser realizadas quando o usuário está conectado com uma conta da Microsoft. Se o usuário estiver conectado com uma conta corporativa ou de estudante, a opção **Usar diretório existente** não estará disponível. Uma conta corporativa ou de estudante pode ser autenticada apenas por seu diretório inicial (ou seja, o diretório em que a conta corporativa ou de estudante é armazenada e da qual a empresa ou escola é proprietária).
>
>

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com) como msmith@hotmail.com.
2. Clique em **Novo** > **Serviços de Aplicativos** > **Active Directory** > **Diretório** > **Criação Personalizada**.
3. Clique em Usar diretório existente e marque a caixa de seleção **Estou pronto para sair agora** .
4. Entre no portal clássico do Azure como administrador global de Contoso.onmicrosoft.com (por exemplo, msmith@contoso.com).
5. Quando for solicitado a **Usar diretório Contoso com o Azure?**, clique em **Continuar**.
6. Clique em **Sair agora**.
7. Entre no portal clássico do Azure como msmith@hotmail.com. O diretório da Contoso e o diretório padrão aparecem na extensão do Active Directory.

Depois de concluir essas etapas, msmith@hotmail.com é um administrador global no diretório Contoso.

## <a name="to-administer-resources-as-the-global-admin"></a>Para administrar recursos como administrador global
Agora vamos supor que Brenda Fernandes precise administrar sites e recursos de banco de dados que estão associados à assinatura do Azure de msmith@hotmail.com. Para que ela possa fazer isso, antes Carlos Lima precisa concluir estas etapas adicionais:

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com) usando a conta de Administrador de Serviços da assinatura do Azure (neste exemplo, msmith@hotmail.com).
2. Transferir a assinatura para o diretório Contoso: clique em **Configurações** > **Assinaturas** > selecione a assinatura > **Editar Diretório** > selecione **Contoso (Contoso.com)**. Como parte da transferência, qualquer conta corporativa ou de estudante que atua como coadministrador da assinatura é removida.
3. Adicionar Brenda Fernandes como coadministradora da assinatura: clique em **Configurações** > **Administradores** > selecione a assinatura > **Adicionar** > digite **JohnDoe@Contoso.com**.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre a relação entre assinaturas e diretórios, confira [Como uma assinatura é associada a um diretório](active-directory-how-subscriptions-associated-directory.md).
