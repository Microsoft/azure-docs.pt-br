---
title: "Gerenciando nomes de domínio personalizados no Azure Active Directory | Microsoft Docs"
description: "Conceitos de gerenciamento e instruções para gerenciar um domínio personalizado no Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: cf3523bd-9ee0-439e-963d-ccea038867b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: curtand
ms.custom: oldportal;it-pro;
robots: NOINDEX
ms.openlocfilehash: 06e2dd05bdf21074ed060057cbc61e93f304399a
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Gerenciando nomes de domínio personalizados no Azure Active Directory
Um nome de domínio pode ser um identificador importante para muitos recursos de diretório, como parte de:

* Um nome de usuário ou endereço de email de um usuário
* O endereço de um grupo
* A URI da ID do aplicativo de um aplicativo

Um recurso no Azure AD (Azure Active Directory) pode incluir um nome de domínio já verificado como pertencente ao diretório que contém o recurso. Somente um administrador global pode executar tarefas de gerenciamento de domínio no Azure AD.

> [!IMPORTANT]
> A Microsoft recomenda que você gerencie o Azure AD usando o [Centro de administração do AD do Azure](https://aad.portal.azure.com) no portal do Azure em vez de usar o portal clássico do Azure mencionado neste artigo. Para saber como gerenciar seus nomes de domínio no centro de administração do Azure AD, consulte [Gerenciando nomes de domínio personalizados no Azure Active Directory](active-directory-domains-manage-azure-portal.md).

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Definir o nome de domínio primário para o diretório do Azure AD
Quando o diretório é criado, o nome de domínio inicial, como 'contoso.onmicrosoft.com', também é o nome de domínio primário para seu diretório. O domínio primário é o nome de domínio padrão para um novo usuário ao criar um novo usuário no [portal clássico do Azure](https://manage.windowsazure.com/), ou outros portais, como o portal de administração do Office 365. Isso simplifica o processo de criação de novos usuários no portal por um administrador.

Para alterar o nome de domínio primário para seu diretório:

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com/) com uma conta de usuário que seja um administrador global do diretório do Azure AD.
2. Selecione **Active Directory** na barra de navegação esquerda.
3. Abra seu diretório.
4. Selecione a guia **Domínios** .
5. Selecione o botão **Alterar primário** na barra de comandos.
6. Selecione o domínio que você deseja que seja o novo domínio primário para seu diretório.

Você pode alterar o nome de domínio primário para o seu diretório para qualquer domínio personalizado verificado não federado. Alterar o domínio primário para o seu diretório não alterará os nomes de usuário para usuários existentes.

## <a name="add-custom-domain-names-to-your-azure-ad"></a>Adicionar nomes de domínio personalizados ao Azure AD
Você pode adicionar até 900 nomes de domínio personalizados para cada diretório do Azure AD. O processo para [adicionar um nome de domínio personalizado adicional](active-directory-add-domain.md) é o mesmo para o primeiro nome de domínio personalizado.

## <a name="add-subdomains-of-a-custom-domain"></a>Adicionar subdomínios de um domínio personalizado
Se você quiser adicionar um nome de domínio de terceiro nível como 'europe.contoso.com' ao seu diretório, deverá primeiro adicionar e verificar o domínio de segundo nível, como contoso.com. O subdomínio será verificado automaticamente pelo Azure AD. Para conferir que o subdomínio que você acabou de adicionar foi verificado, atualize no navegador a página que lista os domínios em seu diretório.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>O que fazer se você alterar o registrador DNS para o nome de domínio personalizado
Se você alterar o registrador DNS para seu nome de domínio personalizado, poderá continuar usando seu nome de domínio personalizado com o Azure AD sem interrupção e sem outras tarefas de configuração adicionais. Se você usa o nome de domínio personalizado com o Office 365, o Intune ou outros serviços que dependem de nomes de domínio personalizado no Azure AD, consulte a documentação desses serviços.

## <a name="delete-a-custom-domain-name"></a>Excluir um nome de domínio personalizado
Você poderá excluir um nome de domínio do Azure AD se sua organização não usar esse nome de domínio ou se for preciso usar o nome de domínio com outro Azure AD.

Para excluir um nome de domínio personalizado, primeiro você deve assegurar que nenhum recurso em seu diretório dependa do nome de domínio. Você não poderá excluir um nome de domínio do diretório se:

* Qualquer usuário tiver um nome de usuário, endereço de email ou endereço de proxy que inclua o nome de domínio.
* Qualquer grupo tiver um endereço de email ou endereço de proxy que inclua o nome de domínio.
* Qualquer aplicativo no Azure AD tiver um URI da ID do aplicativo que inclua o nome de domínio.

Você deve alterar ou excluir qualquer recurso desse tipo em seu diretório do Azure AD antes que possa excluir o nome de domínio personalizado.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Usar o PowerShell ou o Graph API para gerenciar nomes de domínio
A maioria das tarefas de gerenciamento para nomes de domínio no Azure Active Directory também pode ser concluída usando o Microsoft PowerShell ou de forma programática, usando a API do Graph do Azure AD.

* [Como usar o PowerShell para gerenciar nomes de domínio no Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Como usar a API do Graph para gerenciar nomes de domínio no Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre nomes de domínio no Azure AD](active-directory-add-domain-concepts.md)
* [Gerenciar nomes de domínio personalizados](active-directory-add-manage-domain-names.md)

