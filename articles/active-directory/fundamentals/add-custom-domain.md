---
title: Adicionar seu domínio personalizado – Azure Active Directory | Microsoft Docs
description: Instruções sobre como adicionar um domínio personalizado usando o Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: elkuzmen
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28847ece3c08e93d14d381b35fc93fb16dad95a1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95973891"
---
# <a name="add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>Adicionar um nome de domínio personalizado usando o portal do Azure Active Directory

Cada novo locatário do Azure AD vem com um nome de domínio inicial, *\<domainname> . onmicrosoft.com*. Você não pode alterar ou excluir o nome de domínio inicial, mas pode adicionar os nomes de sua organização. Adicionar nomes de domínio personalizados ajuda a criar nomes de usuário que são familiares para seus usuários, como *alain \@ contoso.com*.

## <a name="before-you-begin"></a>Antes de começar

Para poder adicionar um nome de domínio personalizado, crie seu nome de domínio com um registrador de domínio. Para um registrador de domínios reconhecido, consulte [Registradores reconhecidos pelo ICANN](https://www.icann.org/registrar-reports/accredited-list.html).

## <a name="create-your-directory-in-azure-ad"></a>Criar seu diretório no Azure AD

Depois de obter seu nome de domínio, é possível criar seu primeiro diretório do Azure AD. Entre no portal do Azure do seu diretório, usando uma conta com a função de **proprietário** para a assinatura.

Para criar seu novo diretório, siga as etapas em [Criar um novo locatário para sua organização](active-directory-access-create-new-tenant.md#create-a-new-tenant-for-your-organization).

>[!IMPORTANT]
>A pessoa que cria o locatário torna-se automaticamente o administrador global desse locatário. O administrador global pode adicionar mais administradores ao locatário.

Para obter mais informações sobre as funções de assinatura, consulte [funções do Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles).

>[!TIP]
> Se você planeja federar seu AD do Windows Server local com o Azure AD, você precisa selecionar o **plano para configurar esse domínio para logon único com o meu Active Directory local** ao executar a ferramenta de Azure ad Connect para sincronizar seus diretórios.
>
> Você também precisa registrar o mesmo nome de domínio selecionado para federação com seu diretório local na etapa **domínio do Azure AD** no assistente. Para ver como essa configuração se parece, consulte [verificar o domínio do Azure ad selecionado para Federação](../hybrid/how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation). Se você não tiver a ferramenta Azure AD Connect, poderá [baixá-la aqui](https://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-your-custom-domain-name-to-azure-ad"></a>Adicionar seu nome de domínio personalizado ao Azure AD

Depois de criar seu diretório, você pode adicionar seu nome de domínio personalizado.

1. Entre no [portal do Azure](https://portal.azure.com/) usando uma conta de administrador Global para o diretório.

1. Procure e selecione *Azure Active Directory* em qualquer página. Em seguida, selecione **nomes de domínio personalizados**  >  **Adicionar domínio personalizado**.

    ![Página nomes de domínio personalizados, com Adicionar domínio personalizado mostrado](media/add-custom-domain/add-custom-domain.png)

1. Em **nome de domínio personalizado**, insira o novo nome da sua organização, neste exemplo, *contoso.com*. Selecione **Adicionar domínio**.

    ![Página nomes de domínio personalizados, com a página Adicionar domínio personalizado](media/add-custom-domain/add-custom-domain-blade.png)

    >[!IMPORTANT]
    >Você deve incluir *. com*, *.net* ou qualquer outra extensão de nível superior para que isso funcione corretamente.

    O domínio não verificado é adicionado. A página **contoso.com** aparece mostrando as informações de DNS. Salve essas informações. Você precisará dela mais tarde para criar um registro TXT para configurar o DNS.

    ![Página da Contoso com informações sobre entrada de DNS](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-dns-information-to-the-domain-registrar"></a>Adicionar suas informações de DNS ao registrador de domínios

Depois de adicionar seu nome de domínio personalizado ao Azure AD, você deve retornar ao seu registrador de domínios e adicionar as informações de DNS do Azure AD do seu arquivo TXT copiado. A criação desse registro TXT para seu domínio verifica a propriedade do seu nome de domínio.

Volte para seu registrador de domínio e crie um novo registro TXT para seu domínio com base nas informações de DNS copiadas. Defina o tempo de vida (TTL) como 3600 segundos (60 minutos) e salve o registro.

>[!IMPORTANT]
>Você pode registrar quantos nomes de domínio desejar. No entanto, cada domínio obtém seu próprio registro TXT do Azure AD. Tenha cuidado ao inserir as informações do arquivo TXT no registrador de domínios. Se você inserir as informações erradas ou duplicadas por engano, terá que aguardar até que o TTL expire (60 minutos) antes de tentar novamente.

## <a name="verify-your-custom-domain-name"></a>Verificar seu nome de domínio personalizado

Depois de registrar seu nome de domínio personalizado, verifique se ele é válido no Azure AD. A propagação do seu registrador de domínio para o Azure AD pode ser instantânea ou pode levar alguns dias, dependendo do seu registrador de domínio.

Para verificar seu nome de domínio personalizado, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com/) usando uma conta de administrador Global para o diretório.

1. Procure e selecione *Azure Active Directory* em qualquer página e, em seguida, selecione **nomes de domínio personalizados**.

1. Em **nomes de domínio personalizados**, selecione o nome de domínio personalizado. Neste exemplo, selecione **contoso.com**.

    ![Página Fabrikam – nomes de domínio personalizado, com contoso realçado](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

1. Na página **contoso.com** , selecione **verificar** para certificar-se de que seu domínio personalizado está registrado corretamente e é válido para o Azure AD.

    ![Página da Contoso com informações sobre entrada de DNS e o botão Verificar](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

Depois de verificar seu nome de domínio personalizado, você pode excluir seu arquivo TXT ou MX de verificação.

## <a name="common-verification-issues"></a>Problemas comuns de verificação

Se o Azure AD não puder verificar um nome de domínio personalizado, tente as sugestões a seguir:

- **Aguarde pelo menos uma hora e tente novamente**. Os registros DNS precisam ser propagados para que o Azure AD possa verificar o domínio. Esse processo pode levar uma hora ou mais.

- **Verifique se o registro DNS está correto.** Volte para o site de registrador de nomes de domínio. Verifique se a entrada está lá e se ela corresponde às informações de entrada de DNS fornecidas pelo Azure AD.

  Se você não puder atualizar o registro no site do registrador, compartilhe a entrada com alguém que tenha permissões para adicionar a entrada e verifique se ela está correta.

- **Verifique se o nome de domínio não está em uso em outro diretório.** Um nome de domínio só pode ser verificado em um diretório. Se o seu nome de domínio estiver atualmente verificado em outro diretório, ele também não poderá ser verificado no novo diretório. Para corrigir esse problema de duplicação, você deve excluir o nome de domínio do diretório antigo. Para obter mais informações sobre a exclusão de nomes de domínio, consulte [Gerenciar nomes de domínio personalizado](../enterprise-users/domains-manage.md).

- **Confira se você não tem nenhum locatário do Power BI não gerenciado.** Se os usuários tiverem ativado o Power BI por meio de inscrição de autoatendimento e criado um locatário não gerenciado para sua organização, você deverá assumir o gerenciamento como administrador interno ou externo, usando o PowerShell. Para obter mais informações, confira [Controlar um diretório não gerenciado como administrador no Azure Active Directory](../enterprise-users/domains-admin-takeover.md).

## <a name="next-steps"></a>Próximas etapas

- Adicione outro administrador global ao seu diretório. Para obter mais informações, consulte [Como atribuir funções e administradores](active-directory-users-assign-role-azure-portal.md).

- Adicione usuários ao seu domínio. Para obter mais informações, consulte [como adicionar ou excluir usuários](add-users-azure-active-directory.md).

- Gerencie suas informações de nome de domínio no Azure AD. Para obter mais informações, confira [Gerenciamento de nomes de domínio personalizado](../enterprise-users/domains-manage.md).

- Se você tiver versões locais do Windows Server que deseja usar junto com o Azure Active Directory, consulte [Integrar seus diretórios locais ao Azure Active Directory](../hybrid/whatis-hybrid-identity.md).