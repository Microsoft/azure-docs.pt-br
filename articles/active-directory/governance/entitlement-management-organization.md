---
title: Adicionar uma organização conectada no gerenciamento de direitos do Azure AD-Azure Active Directory
description: Saiba como permitir que as pessoas de fora da sua organização solicitem pacotes de acesso para que você possa colaborar em projetos.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/11/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f08c25749bbd21e3624dee898d9a8c97fd74164
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2021
ms.locfileid: "98059374"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Adicionar uma organização conectada no gerenciamento de direitos do Azure AD

Com o gerenciamento de direitos do Azure Active Directory (AD do Azure), você pode colaborar com pessoas de fora da sua organização. Se você colabora com frequência com usuários em um diretório ou domínio externo do Azure AD, você pode adicioná-los como uma organização conectada. Este artigo descreve como adicionar uma organização conectada para que você possa permitir que usuários fora da sua organização solicitem recursos em seu diretório.

## <a name="what-is-a-connected-organization"></a>O que é uma organização conectada?

Uma organização conectada é outra organização com a qual você tem uma relação.  Para que os usuários na organização possam acessar seus recursos, como seus sites ou aplicativos do SharePoint Online, você precisará de uma representação dos usuários dessa organização nesse diretório.  Como, na maioria dos casos, os usuários nessa organização ainda não estão em seu diretório do Azure AD, você pode usar o gerenciamento de direitos para colocá-los em seu diretório do Azure AD, conforme necessário.  

Há três maneiras de o gerenciamento de direitos permitir que você especifique os usuários que formam uma organização conectada.  Poderia ser

* usuários em outro diretório do AD do Azure,
* usuários em outro diretório não Azure AD que foi configurado para Federação direta ou
* usuários em outro diretório não Azure AD, cujos endereços de email têm o mesmo nome de domínio em comum.

Por exemplo, suponha que você trabalhe no Banco Woodgrove e queira colaborar com duas organizações externas. Essas duas organizações têm configurações diferentes:

- O design gráfico Institute usa o Azure AD e seus usuários têm um nome principal de usuário que termina com *graphicdesigninstitute.com*.
- A contoso ainda não usa o Azure AD. Os usuários da Contoso têm um nome principal de usuário que termina com *contoso.com*.

Nesse caso, você pode configurar duas organizações conectadas. Você cria uma organização conectada para o design gráfico Institute e outra para a contoso. Se você adicionar as duas organizações conectadas a uma política, os usuários de cada organização com um nome principal de usuário que corresponda à política poderão solicitar pacotes de acesso. Os usuários com um nome UPN que tenha um domínio de *graphicdesigninstitute.com* corresponderão à organização conectada ao design gráfico e terão permissão para enviar solicitações. Os usuários com um nome principal de usuário que tem um domínio de *contoso.com* corresponderão à organização conectada à contoso e também teriam permissão para solicitar pacotes. E, como o design gráfico Institute usa o Azure AD, qualquer usuário com um nome principal que corresponda a um [domínio verificado](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) que é adicionado ao seu locatário, como *graphicdesigninstitute. example*, também seria capaz de solicitar pacotes de acesso usando a mesma política.

![Exemplo de organização conectada](./media/entitlement-management-organization/connected-organization-example.png)

A forma como os usuários do diretório do Azure AD ou do domínio se autenticam depende do tipo de autenticação. Os tipos de autenticação para organizações conectadas são:

- Azure AD
- [Federação direta](../external-identities/direct-federation.md)
- [Senha de uso único](../external-identities/one-time-passcode.md) (domínio)

Para ver uma demonstração de como adicionar uma organização conectada, Assista ao vídeo a seguir:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Adicionar uma organização conectada

Para adicionar um diretório ou domínio do Azure AD externo como uma organização conectada, siga as instruções nesta seção.

**Função de pré-requisito**: administrador *global* ou *administrador de usuário*

1. Na portal do Azure, selecione **Azure Active Directory** e, em seguida, selecione **governança de identidade**.

1. No painel esquerdo, selecione **organizações conectadas** e, em seguida, selecione **Adicionar organização conectada**.

    ![O botão "Adicionar organização conectada"](./media/entitlement-management-organization/connected-organization.png)

1. Selecione a guia **noções básicas** e insira um nome de exibição e uma descrição para a organização.

    ![O painel básico "Adicionar organização conectada"](./media/entitlement-management-organization/organization-basics.png)

1. O estado será definido automaticamente como **configurado** quando você criar uma nova organização conectada. Para obter mais informações sobre as propriedades de estado, consulte [Propriedades de estado das organizações conectadas](#state-properties-of-connected-organizations)

1. Selecione a guia **diretório + domínio** e, em seguida, selecione **Adicionar diretório + domínio**.

    O painel **selecionar diretórios + domínios** é aberto.

1. Na caixa de pesquisa, insira um nome de domínio para pesquisar o diretório ou domínio do Azure AD. Certifique-se de inserir o nome de domínio inteiro.

1. Verifique se o nome da organização e o tipo de autenticação estão corretos. A forma como os usuários entram depende do tipo de autenticação.

    ![O painel "selecionar diretórios + domínios"](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Selecione **Adicionar** para adicionar o diretório ou domínio do Azure AD. No momento, você pode adicionar apenas um diretório ou domínio do Azure AD por organização conectada.

    > [!NOTE]
    > Todos os usuários do diretório ou domínio do Azure AD poderão solicitar esse pacote de acesso. Isso inclui os usuários no Azure AD de todos os subdomínios associados ao diretório, a menos que esses domínios estejam bloqueados pela lista de permitidos ou negações B2B (Business to Business) do Azure AD. Para obter mais informações, consulte [Permitir ou bloquear convites para usuários B2B de organizações específicas](../external-identities/allow-deny-list.md).

1. Depois de adicionar o diretório ou domínio do Azure AD, selecione **selecionar**.

    A organização aparece na lista.

    ![O painel "diretório + domínio"](./media/entitlement-management-organization/organization-directory-domain.png)

1. Selecione a guia **patrocinadores** e, em seguida, adicione patrocinadores opcionais para esta organização conectada.

    Os patrocinadores são usuários internos ou externos que já estão em seu diretório que são o ponto de contato para a relação com essa organização conectada. Os patrocinadores internos são usuários Membros em seu diretório. Os patrocinadores externos são usuários convidados da organização conectada que foram convidados anteriormente e já estão em seu diretório. Os patrocinadores podem ser utilizados como aprovadores quando os usuários nesta organização conectada solicitam acesso a esse pacote de acesso. Para obter informações sobre como convidar um usuário convidado para seu diretório, consulte [adicionar Azure Active Directory usuários de colaboração B2B no portal do Azure](../external-identities/add-users-administrator.md).

    Quando você seleciona **Adicionar/remover**, um painel é aberto no qual você pode escolher patrocinadores internos ou externos. O painel exibe uma lista não filtrada de usuários e grupos em seu diretório.

    ![O painel de patrocinadores](./media/entitlement-management-organization/organization-sponsors.png)

1. Selecione a guia **revisar + criar** , examine as configurações da organização e, em seguida, selecione **criar**.

    ![O painel "revisar + criar"](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>Atualizar uma organização conectada 

Se a organização conectada mudar para um domínio diferente, o nome da organização for alterado ou você quiser alterar os patrocinadores, você poderá atualizar a organização conectada seguindo as instruções nesta seção.

**Função de pré-requisito**: administrador *global* ou *administrador de usuário*

1. Na portal do Azure, selecione **Azure Active Directory** e, em seguida, selecione **governança de identidade**.

1. No painel esquerdo, selecione **organizações conectadas** e, em seguida, selecione a organização conectada para abri-la.

1. No painel Visão geral da organização conectada, selecione **Editar** para alterar o nome da organização, a descrição ou o estado.  

1. No painel **diretório + domínio** , selecione **Atualizar diretório + domínio** para alterar para um diretório ou domínio diferente.

1. No painel de **patrocinadores** , selecione **Adicionar patrocinadores internos** ou **Adicionar patrocinadores externos** para adicionar um usuário como um patrocinador. Para remover um patrocinador, selecione o patrocinador e, no painel direito, selecione **excluir**.


## <a name="delete-a-connected-organization"></a>Excluir uma organização conectada

Se você não tiver mais uma relação com um diretório ou domínio do Azure AD externo, poderá excluir a organização conectada.

**Função de pré-requisito**: administrador *global* ou *administrador de usuário*

1. Na portal do Azure, selecione **Azure Active Directory** e, em seguida, selecione **governança de identidade**.

1. No painel esquerdo, selecione **organizações conectadas** e, em seguida, selecione a organização conectada para abri-la.

1. No painel Visão geral da organização conectada, selecione **excluir** para excluí-la.

    ![O botão excluir da organização conectada](./media/entitlement-management-organization/organization-delete.png)

## <a name="managing-a-connected-organization-programmatically"></a>Gerenciando uma organização conectada programaticamente

Você também pode criar, listar, atualizar e excluir organizações conectadas usando Microsoft Graph. Um usuário em uma função apropriada com um aplicativo que tem a permissão delegada `EntitlementManagement.ReadWrite.All` pode chamar a API para gerenciar objetos [connectedOrganization](/graph/api/resources/connectedorganization?view=graph-rest-beta) e definir os patrocinadores para eles.

## <a name="state-properties-of-connected-organizations"></a>Propriedades de estado de organizações conectadas

Há dois tipos diferentes de propriedades de estado para organizações conectadas no gerenciamento de direitos do Azure AD atualmente, configuradas e propostas: 

- Uma organização conectada configurada é uma organização conectada totalmente funcional que permite que os usuários dentro dessa organização acessem pacotes do Access. Quando um administrador cria uma nova organização conectada no portal do Azure, ele estará no estado **configurado** por padrão, pois o administrador criou e deseja usar essa organização conectada. Além disso, quando uma organização conectada é criada programaticamente por meio da API, o estado padrão deve ser **configurado** , a menos que seja definido como outro Estado explicitamente. 

    As organizações conectadas configuradas aparecerão nos seletores das organizações conectadas e estarão no escopo de qualquer política direcionada a "todas" organizações conectadas.

- Uma organização conectada proposta é uma organização conectada que foi criada automaticamente, mas não teve um administrador para criar ou aprovar a organização. Quando um usuário se inscreve em um pacote de acesso fora de uma organização conectada configurada, todas as organizações conectadas criadas automaticamente estarão no estado **proposto** , uma vez que nenhum administrador na configuração de locatário dessa parceria. 
    
    As organizações conectadas propostas não estão no escopo da configuração "todas as organizações conectadas configuradas" em qualquer política, mas podem ser usadas em políticas somente para políticas voltadas para organizações específicas. 

Somente os usuários de organizações conectadas configuradas podem solicitar pacotes de acesso disponíveis para usuários de todas as organizações configuradas. Os usuários de organizações conectadas propostas têm uma experiência como se não há nenhuma organização conectada para esse domínio; Só pode ver e solicitar pacotes de acesso com escopo para sua organização específica ou com escopo para qualquer usuário.

> [!NOTE]
> Como parte da distribuição desse novo recurso, todas as organizações conectadas criadas antes de 09/09/20 foram consideradas **configuradas**. Se você tiver um pacote de acesso que permitia que usuários de qualquer organização se inscrevam, examine sua lista de organizações conectadas que foram criadas antes dessa data para garantir que nenhuma seja classificada de forma **incorreta conforme configurada**.  Um administrador pode atualizar a propriedade **State** conforme apropriado. Para obter diretrizes, consulte [atualizar uma organização conectada](#update-a-connected-organization).

## <a name="next-steps"></a>Próximas etapas

- [Administrar o acesso de usuários externos](./entitlement-management-external-users.md)
- [Controlar o acesso para usuários que não estão em seu diretório](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
