---
title: "Gestão do acesso de convidado com revisões de acesso do Azure AD | Microsoft Docs"
description: "Gerenciar usuários convidados como membros de um grupo ou atribuídos a um aplicativo com revisões de acesso do Azure Active Directory"
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: b2a76945482e0fe51dc7cc46740cbb8f3f2ff643
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Gerenciar o acesso de convidado com revisões de acesso do Azure AD


Com o Azure Active Directory, você pode habilitar facilmente a colaboração entre limites organizacionais usando o [recurso B2B do Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md). Usuários convidados de outros locatários podem ser [convidado por administradores](active-directory-b2b-admin-add-users.md) ou por [usuários finais](active-directory-b2b-how-it-works.md).  Isso também se aplica às identidades sociais, como Contas da Microsoft.

Você pode assegurar com facilidade que os usuários convidados tenham o acesso apropriado.  Faça isso pedindo que os próprios convidados, ou um tomador de decisão, participem de uma revisão de acesso e reconfirmar (ou "atestar") o acesso de convidado. Revisores podem fornecer a sua entrada na necessidade de cada usuário de acesso contínuo, com base nas sugestões do Azure AD. Quando uma revisão de acesso for concluída, você poderá alterar e remover o acesso de convidados que não precisam mais dela.

> [!NOTE]
> Este documento se concentra na revisão do acesso de usuários convidados. Se você quiser revisar o acesso de todos os usuários, não apenas dos convidados, leia então o guia de [gerenciamento de acesso do usuário com revisões de acesso](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md).  E se você desejar revisar a associação do usuário em funções administrativas, como o administrador global, confira [Como iniciar uma revisão de acesso no Azure AD PIM](active-directory-privileged-identity-management-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Pré-requisitos 

As revisões de acesso estão disponíveis com a edição Premium P2 do Azure Active Directory, inclusa no EMS E5. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).  Cada usuário que interage com esse recurso, para criar uma análise, revisar o acesso ou aplicar uma revisão, deve ter uma licença.  

Se você for pedir aos usuários convidados que revisem seus próprios acessos, leia mais sobre licenciamento de usuário convidado em [Licenciamento de colaboração B2B do Azure AD](active-directory-b2b-licensing.md).

## <a name="creating-and-performing-an-access-review-for-guests"></a>Criar e executar de uma revisão de acesso para convidados

Primeiro, permita que as revisões de acesso apareçam nos painéis de acesso do revisor.  Como administrador global, acesse a [página de revisões de acesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/). 

O Azure AD proporciona vários cenários para revisão de usuários convidados.  

Selecione uma das seguintes:
 - um grupo no Azure Active Directory com um ou mais convidados como membros 
 - ou um aplicativo conectado ao Azure Active Directory com um ou mais usuários convidados atribuídos a ele, e decida se cada convidado revisará seu próprios acessos, ou se um ou mais usuários revisarão o acesso de cada convidado.

 Cada um desses cenários é abordado em uma das seções a seguir.

* [Pedir que os convidados revisem suas próprias associações em um grupo](#asking-guests-to-review-their-own-membership-in-a-group)
* [Pedir que os responsáveis revisem a associação do convidado em um grupo](#asking-sponsors-to-review-guests-membership-in-a-group)
* [Pedir que os convidados revisem seus próprios acessos a um aplicativo](#asking-guests-to-review-their-own-access-to-an-application)
* [Pedir que os responsáveis revisem o acesso do convidado a um aplicativo](#asking-sponsors-to-review-guests-access-to-an-application) 
* [Pedir que os convidados revisem a própria necessidade de acesso em geral](#asking-guests-to-review-their-need-for-access-in-general)


### <a name="asking-guests-to-review-their-own-membership-in-a-group"></a>Pedir que os convidados revisem suas próprias associações em um grupo

As revisões de acesso podem ser usadas para garantir que os usuários convidados e adicionados a um grupo ainda precisam do acesso.  É uma maneira fácil de pedir que os próprios convidados revisem suas próprias associações nesse grupo.

1. Inicie uma revisão de acesso para o grupo, selecionando a revisão que inclui apenas membros usuários convidados, e que os membros são responsáveis pela própria revisão. Leia mais em [Como criar uma revisão de acesso](active-directory-azure-ad-controls-create-access-review.md).
2. Peça que cada convidado revise a própria associação.  Por padrão, cada convidado que aceita um convite receberá um email do Azure AD com um link para a revisão de acesso.  O Azure AD tem instruções para convidados sobre [como revisar o acesso](active-directory-azure-ad-controls-perform-access-review.md).
3. Depois dos revisores enviarem a entrada, pare a revisão de acesso e aplique as alterações. Leia mais em [Como concluir uma revisão de acesso](active-directory-azure-ad-controls-complete-access-review.md). 
4. Além dos usuários que negaram a necessidade de continuar com o acesso, convém também remover os usuários que não responderam.  Isso porque os usuários que não respondem possivelmente não estão mais recebendo email.
5. Se o grupo não estiver sendo usado para gerenciamento de acesso, convém também remover usuários que não foram selecionados para participar da revisão por não terem aceitado o convite.  Isso pode indicar que o endereço de email do usuário convidado foi um erro de digitação.  No entanto, se um grupo estiver sendo usado como lista de distribuição, talvez alguns usuários convidados não foram selecionados para participar por serem objetos de contato.

### <a name="asking-sponsors-to-review-guests-membership-in-a-group"></a>Pedir que os responsáveis revisem a associação do convidado em um grupo

Você pode pedir a um responsável, como os proprietários de um grupo, que examine a necessidade do convidado de continuar com a associação em um grupo.

1. Inicie uma revisão de acesso para o grupo, selecionando a revisão que inclui apenas membros usuários convidados, e especificando um ou mais revisores. Leia mais em [Como criar uma revisão de acesso](active-directory-azure-ad-controls-create-access-review.md).
2. Solicite que os revisores forneçam a entrada. Por padrão, todos receberão um email do Azure AD com um link para o painel de acesso, onde poderão [executar sua revisão de acesso](active-directory-azure-ad-controls-perform-access-review.md).
3. Depois dos revisores enviarem a entrada, pare a revisão de acesso e aplique as alterações. Leia mais em [Como concluir uma revisão de acesso](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="asking-guests-to-review-their-own-access-to-an-application"></a>Pedir que os convidados revisem seus próprios acessos a um aplicativo

As revisões de acesso podem ser usadas para garantir que os usuários convidados de um aplicativo específico ainda precisam do acesso.  É uma maneira fácil de pedir que os próprios convidados revisem suas próprias necessidades de acesso.

1. Inicie uma revisão de acesso para o aplicativo, selecionando a revisão que inclui apenas convidados, e que os usuários são responsáveis pela própria revisão de acesso. Leia mais em [Como criar uma revisão de acesso](active-directory-azure-ad-controls-create-access-review.md).
2. Peça que cada convidado revise seu próprio acesso ao aplicativo.  Por padrão, cada convidado que aceita um convite receberá um email do Azure AD com um link para a revisão de acesso no painel de acesso de sua organização.  O Azure AD tem instruções para convidados sobre [como revisar o acesso](active-directory-azure-ad-controls-perform-access-review.md).
3. Depois dos revisores enviarem a entrada, pare a revisão de acesso e aplique as alterações. Leia mais em [Como concluir uma revisão de acesso](active-directory-azure-ad-controls-complete-access-review.md).
4. Além dos usuários que negaram suas próprias necessidades de continuação de acesso, convém também remover os usuários convidados que não responderam, pois os usuários que não respondem possivelmente não estão mais recebendo email.  Convém também remover os usuários convidados que não foram selecionados para participar, especialmente se o convidado não tiver sido convidado recentemente, pois sem aceitar o convite, não teria acesso ao aplicativo. 

### <a name="asking-sponsors-to-review-guests-access-to-an-application"></a>Pedir que os responsáveis revisem o acesso do convidado a um aplicativo


Você pode pedir a um responsável, como os proprietários de um aplicativo, que examine a necessidade do convidado de continuar com o acesso ao aplicativo.

1. Inicie uma revisão de acesso para o aplicativo, selecionando a revisão que inclui apenas convidados, e especificando um ou mais usuários como revisores. Leia mais em [Como criar uma revisão de acesso](active-directory-azure-ad-controls-create-access-review.md).
2. Solicite que os revisores forneçam a entrada. Por padrão, todos receberão um email do Azure AD com um link para o painel de acesso, onde poderão [executar sua revisão de acesso](active-directory-azure-ad-controls-perform-access-review.md).
3. Depois dos revisores enviarem a entrada, pare a revisão de acesso e aplique as alterações. Leia mais em [Como concluir uma revisão de acesso](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="asking-guests-to-review-their-need-for-access-in-general"></a>Pedir que os convidados revisem a própria necessidade de acesso em geral

Em algumas organizações, talvez os convidados não estejam cientes de suas associações de grupo.

> [!NOTE]
> Versões anteriores do Portal do Azure não permitiam o acesso administrativo por usuários com UserType definido como Convidado e, em alguns casos, um administrador em seu diretório pode ter alterado o valor de UserType de um convidado para Membro, usando o PowerShell.  Se isso tiver ocorrido em seu diretório, talvez a consulta mencionada acima não inclua todos os usuários convidados que já tiveram direitos de acesso administrativo. Portanto, será necessário alterar o UserType desse convidado, ou incluí-lo manualmente na associação de grupo.

1. Crie um grupo de segurança no Azure AD com os convidados como membros, se um grupo adequado ainda não existir.  Por exemplo, convém criar um grupo com uma associação de convidados mantida manualmente.  Ou, convém criar um grupo dinâmico com um nome como "Convidados da Contoso" para usuários no locatário Contoso que têm o valor do atributo UserType de Convidado.
2. Inicie uma revisão de acesso para esse grupo, selecionando os revisores como os próprios membros. Leia mais em [Como criar uma revisão de acesso](active-directory-azure-ad-controls-create-access-review.md).
3. Peça que cada convidado revise a própria associação.  Por padrão, cada convidado que aceita um convite receberá um email do Azure AD com um link para a revisão de acesso no painel de acesso de sua organização.  O Azure AD tem instruções para convidados sobre [como revisar o acesso](active-directory-azure-ad-controls-perform-access-review.md).
4. Depois dos revisores enviarem a entrada, pare a revisão de acesso. Leia mais em [Como concluir uma revisão de acesso](active-directory-azure-ad-controls-complete-access-review.md).
5. Remova o acesso de convidado para convidados que foram negados, não completaram a revisão ou não tinham aceito o convite anteriormente.   Se alguns dos convidados forem contatos selecionados para participar da revisão, mas não aceitaram anteriormente um convite, convém desabilitar o logon da conta usando o Portal do Azure ou o PowerShell.  Se o convidado não precisar mais de acesso e não for um contato, o objeto de usuário dele poderá ser removido de seu diretório usando o Portal do Azure ou o PowerShell.

## <a name="next-steps"></a>Próximas etapas

- [Como criar uma revisão de acesso para os membros de um grupo ou para o acesso a um aplicativo](active-directory-azure-ad-controls-create-access-review.md)







