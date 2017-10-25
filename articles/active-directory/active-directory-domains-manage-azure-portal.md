---
title: "Gerenciando nomes de domínio personalizados no Azure Active Directory | Microsoft Docs"
description: "Conceitos de gerenciamento e instruções para gerenciar um nome de domínio no Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 5063cd0a-dba2-4ba9-aa65-b8117490d73a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.openlocfilehash: 1e58af1f8d26b03c07b27d69f13868bccaaa33aa
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Gerenciando nomes de domínio personalizados no Azure Active Directory
Um nome de domínio é uma parte importante do identificador para muitos recursos de diretório: ele é parte de um nome de usuário ou endereço de email para um usuário, parte do endereço para um grupo e pode ser parte do URI da ID do aplicativo para um aplicativo. Um recurso no Azure AD (Azure Active Directory) pode incluir um nome de domínio já verificado como pertencente ao diretório que contém o recurso. Somente um administrador global pode executar tarefas de gerenciamento de domínio no Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Definir o nome de domínio primário para o diretório do Azure AD
Quando o diretório é criado, o nome de domínio inicial, como 'contoso.onmicrosoft.com', também é o nome de domínio primário. O domínio primário é o nome de domínio padrão para um novo usuário quando você cria um novo usuário. Configurar um nome de domínio primário simplifica o processo de criação de novos usuários no portal por um administrador. Para alterar o nome de domínio primário:

1. Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Selecione **Azure Active Directory**.
   
   ![Abrir o gerenciamento de usuários](./media/active-directory-domains-add-azure-portal/user-management.png)
3. Selecione **Nomes de domínio**.
4. Selecione o nome do domínio que você gostaria de transformar no domínio primário.
5. Selecione o comando **Tornar primário**. Confirme sua escolha quando solicitado.
   
   ![Tornar primário um nome de domínio](./media/active-directory-domains-manage-azure-portal/make-primary.png)

Você pode alterar o nome de domínio primário para o seu diretório para qualquer domínio personalizado verificado não federado. Alterar o domínio primário para o seu diretório não alterará os nomes de usuário para usuários existentes.

## <a name="add-custom-domain-names-to-your-azure-ad"></a>Adicionar nomes de domínio personalizados ao Azure AD
> É possível adicionar até um máximo de 900 nomes de domínio gerenciado. Se você pretende configurar todos os domínios para federação com Active Directory local, será possível adicionar até um máximo de 450 nomes de domínio em cada diretório. Para mais informações, consulte [Nomes de domínio federados e gerenciados](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain-concepts#federated-and-managed-domain-names).

## <a name="add-subdomains-of-a-custom-domain"></a>Adicionar subdomínios de um domínio personalizado
Se você quiser adicionar um nome de domínio de terceiro nível como 'europe.contoso.com' ao seu diretório, deverá primeiro adicionar e verificar o domínio de segundo nível, como contoso.com. O subdomínio será verificado automaticamente pelo Azure AD. Para conferir se o subdomínio que você acabou de adicionar foi verificado, atualize no navegador a página que lista os domínios.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>O que fazer se você alterar o registrador DNS para o nome de domínio personalizado
Se você alterar o registrador DNS para seu nome de domínio personalizado, poderá continuar usando seu nome de domínio personalizado com o Azure AD sem interrupção e sem outras tarefas de configuração adicionais. Se você usa o nome de domínio personalizado com o Office 365, o Intune ou outros serviços que dependem de nomes de domínio personalizado no Azure AD, consulte a documentação desses serviços.

## <a name="delete-a-custom-domain-name"></a>Excluir um nome de domínio personalizado
Você poderá excluir um nome de domínio do Azure AD se sua organização não usar esse nome de domínio ou se for preciso usar o nome de domínio com outro Azure AD.

Para excluir um nome de domínio personalizado, primeiro você deve assegurar que nenhum recurso em seu diretório dependa do nome de domínio. Você não poderá excluir um nome de domínio do diretório se:

* Qualquer usuário tiver um nome de usuário, endereço de email ou endereço proxy que inclua o nome do domínio.
* Qualquer grupo tiver um endereço de email ou endereço de proxy que inclua o nome de domínio.
* Qualquer aplicativo no Azure AD tiver um URI da ID do aplicativo que inclua o nome de domínio.

Você deve alterar ou excluir qualquer recurso desse tipo em seu diretório do Azure AD antes que possa excluir o nome de domínio personalizado.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Usar o PowerShell ou o Graph API para gerenciar nomes de domínio
A maioria das tarefas de gerenciamento para nomes de domínio no Azure Active Directory também pode ser concluída usando o Microsoft PowerShell ou de forma programática, usando a API do Graph do Azure AD.

* [Como usar o PowerShell para gerenciar nomes de domínio no Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Como usar a API do Graph para gerenciar nomes de domínio no Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Próximas etapas
* [Adicionar nomes de domínio personalizados](add-custom-domain.md)

