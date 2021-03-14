---
title: Adicionar e verificar nomes de domínio personalizados - Azure Active Directory | Microsoft Docs
description: Conceitos de gerenciamento e instruções para gerenciar um nome de domínio no Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/20/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47c991457925664c82baed1a47d89f77a323ff45
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561527"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Gerenciando nomes de domínio personalizados no Azure Active Directory

Um nome de domínio é uma parte importante do identificador para muitos recursos de Azure Active Directory (Azure AD): faz parte de um nome de usuário ou endereço de email para um usuário, parte do endereço de um grupo e, às vezes, faz parte do URI da ID do aplicativo para um aplicativo. Um recurso no Azure AD pode incluir um nome de domínio pertencente à organização que contém o recurso. Somente um Administrador Global pode gerenciar domínios no Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-organization"></a>Definir o nome de domínio primário para sua organização do Azure AD

Quando sua organização é criada, o nome de domínio inicial, como ' contoso.onmicrosoft.com ', também é o nome de domínio primário. O domínio primário é o nome de domínio padrão para um novo usuário quando você cria um novo usuário. Configurar um nome de domínio primário simplifica o processo de criação de novos usuários no portal por um administrador. Para alterar o nome de domínio primário:

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global da organização.
2. Selecione **Azure Active Directory**.
3. Selecione **Personalizar nomes de domínio**.
  
   ![Abrir a página de gerenciamento de usuário](./media/domains-manage/add-custom-domain.png)
4. Selecione o nome do domínio que gostaria que fosse o domínio primário.
5. Selecione o comando **Tornar primário**. Confirme sua escolha quando solicitado.
  
   ![Tornar primário um nome de domínio](./media/domains-manage/make-primary-domain.png)

Você pode alterar o nome de domínio primário da sua organização para que seja qualquer domínio personalizado verificado que não seja federado. A alteração do domínio primário para a sua organização não alterará o nome de usuário para todos os usuários existentes.

## <a name="add-custom-domain-names-to-your-azure-ad-organization"></a>Adicionar nomes de domínio personalizados a sua organização do Azure Active Directory

É possível adicionar até 900 nomes de domínio gerenciado. Se você estiver configurando todos os seus domínios para federação com Active Directory locais, poderá adicionar até 450 nomes de domínio em cada organização.

## <a name="add-subdomains-of-a-custom-domain"></a>Adicionar subdomínios de um domínio personalizado

Se você quiser adicionar um nome de subdomínio como ' europe.contoso.com ' à sua organização, primeiro adicione e verifique o domínio raiz, como contoso.com. O subdomínio é verificado automaticamente pelo Azure AD. Para ver que o subdomínio que você adicionou foi verificado, atualize a lista de domínios no navegador.

Se você já tiver adicionado um domínio contoso.com a uma organização do Azure AD, também poderá verificar o subdomínio europe.contoso.com em uma organização diferente do Azure AD. Ao adicionar o subdomínio, você será solicitado a adicionar um registro TXT no provedor de hospedagem DNS.



## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>O que fazer se você alterar o registrador DNS para o nome de domínio personalizado

Se você altera os registradores DNS, não há nenhuma tarefa de configuração adicional no Azure AD. Você pode continuar usando o nome de domínio com o Azure AD sem interrupção. Se você usar seu nome de domínio personalizado com Microsoft 365, Intune ou outros serviços que dependem de nomes de domínio personalizados no Azure AD, consulte a documentação para esses serviços.

## <a name="delete-a-custom-domain-name"></a>Excluir um nome de domínio personalizado

Você poderá excluir um nome de domínio do Azure AD se sua organização não usar esse nome de domínio ou se for preciso usar o nome de domínio com outro Azure AD.

Para excluir um nome de domínio personalizado, primeiro você deve garantir que nenhum recurso em sua organização dependa do nome de domínio. Você não poderá excluir um nome de domínio da sua organização se:

* Qualquer usuário tiver um nome de usuário, endereço de email ou endereço proxy que inclua o nome do domínio.
* Qualquer grupo tiver um endereço de email ou endereço de proxy que inclua o nome de domínio.
* Qualquer aplicativo no Azure AD tiver um URI da ID do aplicativo que inclua o nome de domínio.

Você deve alterar ou excluir qualquer recurso desse tipo em sua organização do Azure AD para poder excluir o nome de domínio personalizado.

### <a name="forcedelete-option"></a>Opção ForceDelete

Você pode aplicar **ForceDelete** a um nome de domínio no [Centro de administração do Azure AD](https://aad.portal.azure.com) ou usando a [API do Microsoft Graph](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true). Essas opções usam uma operação assíncrona e atualizam todas as referências de nome de domínio personalizado, assim como "user@contoso.com", para o nome de domínio padrão inicial, tal como "user@contoso.onmicrosoft.com".

Para chamar **ForceDelete** no portal do Azure, você deve garantir que há menos de 1.000 referências ao nome de domínio, e quaisquer referências em que o Exchange é o serviço de provisionamento devem ser atualizadas ou removidas no [Centro de administração do Exchange](https://outlook.office365.com/ecp/). Isso inclui listas distribuídas e grupos de segurança habilitados por email do Exchange. Para obter mais informações, veja [Remover grupos de segurança habilitados por email](/Exchange/recipients/mail-enabled-security-groups#Remove%20mail-enabled%20security%20groups&preserve-view=true). Além disso, a operação **ForceDelete** não será bem-sucedida se qualquer uma das seguintes opções for verdadeira:

* Você comprou um domínio por meio de serviços de assinatura de domínio Microsoft 365
* Você é um parceiro administrando em nome de outra organização do cliente

As ações a seguir são executadas como parte da operação **ForceDelete**:

* Renomeia o nome UPN, o EmailAddress e o ProxyAddress de usuários com referências ao nome de domínio personalizado para o nome de domínio padrão inicial.
* Renomeia o EmailAddress de grupos com referências ao nome de domínio personalizado para o nome de domínio padrão inicial.
* Renomeia os identifierUris de aplicativos com referências ao nome de domínio personalizado para o nome de domínio padrão inicial.

Um erro é retornado quando:

* O número de objetos a ser renomeado é maior que 1.000
* Um dos aplicativos a ser renomeado é um aplicativo multilocatário

### <a name="frequently-asked-questions"></a>Perguntas frequentes

**P: Por que a exclusão do domínio está falhando com um erro afirmando que eu tenho grupos controlados pelo Exchange nesse nome de domínio?** <br>
**R:** Hoje em dia, determinados grupos como grupos de segurança habilitados por email e listas distribuídas são provisionados pelo Exchange e precisem ser limpos manualmente no [EAC (Centro de administração do Exchange)](https://outlook.office365.com/ecp/). Poderá haver ProxyAddresses remanescentes que dependam do nome de domínio personalizado e precisarão ser atualizados manualmente para outro nome de domínio. 

**P: Eu estou conectado como admin\@contoso.com, mas não consigo excluir o nome de domínio "contoso.com".**<br>
**R:** Você não pode referenciar o nome de domínio personalizado que você está tentando excluir em seu nome de conta de usuário. Verifique se a conta de Administrador Global está usando o nome de domínio padrão inicial (.onmicrosoft.com), assim como admin@contoso.onmicrosoft.com. Entre com uma conta de Administrador Global diferente, tal como admin@contoso.onmicrosoft.com, ou com outro nome de domínio personalizado, tal como "fabrikam.com", em que a conta é admin@fabrikam.com.

**P: Eu cliquei no botão Excluir domínio e vejo o status `In Progress` para a operação de exclusão. Quanto tempo demora? O que acontecerá se ela falhar?**<br>
**R:** A operação de exclusão do domínio é uma tarefa assíncrona em segundo plano que renomeia todas as referências para o nome de domínio. Ela deve ser concluída dentro de um ou dois minutos. Se a exclusão do domínio falhar, verifique se você não tem:

* Aplicativos configurados no nome de domínio com o appIdentifierURI
* Qualquer grupo habilitado por email referenciando o nome de domínio personalizado
* Mais de 1.000 referências ao nome de domínio

Se você achar que alguma das condições ainda não foi atendida, limpe as referências manualmente e tente excluir o domínio novamente.

## <a name="use-powershell-or-the-microsoft-graph-api-to-manage-domain-names"></a>Usar o PowerShell ou o API do Microsoft Graph para gerenciar nomes de domínio

A maioria das tarefas de gerenciamento para nomes de domínio no Azure Active Directory também pode ser concluída usando o Microsoft PowerShell ou de forma programática, usando a API do Microsoft Graph.

* [Como usar o PowerShell para gerenciar nomes de domínio no Azure AD](/powershell/module/azuread/#domains&preserve-view=true)
* [Tipo de recurso de domínio](/graph/api/resources/domain)

## <a name="next-steps"></a>Próximas etapas

* [Adicionar nomes de domínio personalizados](../fundamentals/add-custom-domain.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
* [Remover grupos de segurança habilitados por email do Exchange no Centro de administração do Exchange em um nome de domínio personalizado no Azure AD](/Exchange/recipients/mail-enabled-security-groups#Remove%20mail-enabled%20security%20groups&preserve-view=true)
* [Aplicar ForceDelete a um nome de domínio personalizado com a API do Microsoft Graph](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)
