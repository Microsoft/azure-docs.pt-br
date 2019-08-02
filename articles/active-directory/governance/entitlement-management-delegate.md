---
title: Delegar tarefas de gerenciamento de direitos do AD do Azure (visualização) – Azure Active Directory
description: Saiba mais sobre as funções que você pode atribuir a delegar tarefas de gerenciamento de direitos do Active Directory.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/10/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4c4933847a39a56084894e5bbd40e166e6b73b6
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798628"
---
# <a name="delegate-tasks-in-azure-ad-entitlement-management-preview"></a>Delegar tarefas de gerenciamento de direitos do AD do Azure (visualização)

> [!IMPORTANT]
> No momento, o gerenciamento de direitos do Azure AD (Azure Active Directory) está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Por padrão, os administradores globais e administradores de usuário podem criar e gerenciar todos os aspectos do gerenciamento de direitos do Azure AD. No entanto, os usuários nessas funções podem não saber todos os cenários em que os pacotes de acesso são necessários. Geralmente trata-se de que os usuários dentro de departamentos sabem que precisam colaborar.

Em vez de conceder permissões irrestritas a não-administradores, você pode conceder aos usuários a menos permissões de que precisam para realizar seu trabalho e evitar a criação de conflitantes ou direitos de acesso não autorizado. Este artigo descreve as funções que você pode atribuir para delegar várias tarefas de gerenciamento de direitos.

## <a name="delegate-example-for-departmental-adoption"></a>Exemplo de delegado para a adoção departamental

Para entender como você pode delegar tarefas de gerenciamento de direitos, convém para considerar um exemplo. Suponha que sua organização tem os seguintes cinco usuários:

| User | department | Observações |
| --- | --- | --- |
| Alice | IT | Administrador global |
| Roberto | Pesquisa | Bob também é o proprietário de um grupo de pesquisa |
| Carole | Pesquisa |  |
| Dave | Marketing |  |
| Elisa | Marketing | Elisa também é o proprietário de um aplicativo de Marketing |

A pesquisa e a departamentos de Marketing deseja usar o gerenciamento de direitos para seus usuários. Alice ainda não está pronta para usar o gerenciamento de direitos de outros departamentos. Aqui está uma forma que Alice pode delegar tarefas para a pesquisa e os departamentos de marketing.

1. Alice cria um novo segurança do Azure AD de grupo para os criadores de catálogo e adiciona o Carol, Bob, Dave e Elisa como membros desse grupo.

1. Alice usa as configurações de gerenciamento de direitos para adicionar esse grupo à função de criadores do catálogo.

1. Carol cria uma **Research** do catálogo e adiciona Bob como um coproprietário desse catálogo. Luís adiciona o grupo de pesquisas, que ele é proprietário do catálogo como um recurso, para que possa ser usado em um pacote de acesso para colaboração de pesquisa.

1. Dave cria uma **Marketing** do catálogo e adiciona Elisa como um coproprietário desse catálogo. Elisa adiciona o aplicativo de marketing que ela é a proprietária para o catálogo como um recurso, para que ele pode ser usado em um pacote de acesso para colaboração de marketing.

A pesquisa e os departamentos de marketing podem utilizar o gerenciamento de direitos. Bob, Carol, Dave e Elisa podem criar e gerenciar pacotes de acesso em seus respectivos catálogos.

![Exemplo de delegado de gerenciamento de direitos](./media/entitlement-management-delegate/elm-delegate.png)

## <a name="entitlement-management-roles"></a>Funções de gerenciamento de direitos

Gerenciamento de direitos tem as seguintes funções que são específicas ao gerenciamento de direitos.

| Role | DESCRIÇÃO |
| --- | --- |
| Criador de catálogo | Criar e gerenciar catálogos. Normalmente um administrador de TI que não seja um Administrador Global ou um proprietário de recurso para uma coleção de recursos. A pessoa que cria um catálogo automaticamente se torna o proprietário do catálogo primeiro do catálogo e pode adicionar os proprietários de catálogo adicionais. |
| Proprietário do catálogo | Editar e gerenciar catálogos existentes. Normalmente, um administrador de TI ou proprietários de recursos ou um usuário que o proprietário do catálogo tiver designado. |
| Gerenciador de pacotes de acesso | Editar e gerenciar todos os pacotes de acesso existentes dentro de um catálogo. |

Além disso, um aprovador designado e um solicitante de um pacote de acesso também têm direitos, embora eles não sejam funções.
 
* Aprovador: Autorizado por uma política para aprovar ou negar as solicitações para acessar os pacotes, embora eles não podem alterar as definições de acesso de pacote.
* Solicitante: Autorizado por uma política de um pacote de acesso para solicitar esse pacote de acesso.

A tabela a seguir lista as tarefas que podem ser executadas por essas funções.

| Tarefa | Criador de catálogo | Proprietário do catálogo | Gerenciador de pacotes de acesso | Aprovador |
| --- | :---: | :---: | :---: | :---: |
| [Criar um novo catálogo](entitlement-management-catalog-create.md) | :heavy_check_mark: |  |  |  |
| [Adicionar um recurso a um catálogo](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | | :heavy_check_mark: | | |
| [Editar um catálogo](entitlement-management-catalog-create.md#edit-a-catalog) |  | :heavy_check_mark: |  |  |
| [Excluir um catálogo](entitlement-management-catalog-create.md#delete-a-catalog) |  | :heavy_check_mark: |  |  |
| [Adicionar um proprietário do catálogo ou um Gerenciador de pacotes de acesso a um catálogo](#add-a-catalog-owner-or-an-access-package-manager) |  | :heavy_check_mark: |  |  |
| [Criar um novo pacote de acesso em um catálogo](entitlement-management-access-package-create.md) |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [Gerenciar funções de recurso em um pacote de acesso](entitlement-management-access-package-edit.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Especifique quem pode solicitar um pacote de acesso](entitlement-management-access-package-edit.md#add-a-new-policy) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Atribuir diretamente um usuário a um pacote de acesso](entitlement-management-access-package-edit.md#directly-assign-a-user) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Modo de exibição que tem uma atribuição a um pacote de acesso](entitlement-management-access-package-edit.md#view-who-has-an-assignment) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Exibir solicitações de um pacote acesso](entitlement-management-access-package-edit.md#view-requests) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Exibir erros de entrega da solicitação](entitlement-management-access-package-edit.md#view-a-requests-delivery-errors) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Cancelar uma solicitação pendente](entitlement-management-access-package-edit.md#cancel-a-pending-request) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Ocultar um pacote de acesso](entitlement-management-access-package-edit.md#change-the-hidden-setting) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Excluir um pacote de acesso](entitlement-management-access-package-edit.md#delete) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Aprovar uma solicitação de acesso](entitlement-management-request-approve.md) |  |  |  | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Funções necessárias para adicionar recursos a um catálogo

Um Administrador Global pode adicionar ou remover qualquer grupo (grupos de segurança criados de nuvem ou nuvem criada grupos do Office 365), aplicativo ou site do SharePoint Online em um catálogo. Um usuário administrador pode adicionar ou remover qualquer grupo ou aplicativo em um catálogo.

Para um usuário que não é um Administrador Global ou administrador de usuário, para adicionar grupos, aplicativos ou sites do SharePoint Online a um catálogo, que o usuário deve ter *ambos* o necessários direitos de proprietário do Azure AD directory função e catálogo função de gerenciamento. A tabela a seguir lista as combinações de função que são necessárias para adicionar recursos a um catálogo. Para remover os recursos de um catálogo, você deve ter as mesmas funções.

| Função do diretório do Azure Active Directory | Função de gerenciamento de direitos | Pode adicionar o grupo de segurança | Pode adicionar o grupo do Office 365 | Pode adicionar o aplicativo | Pode adicionar o site do SharePoint Online |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Administrador global](../users-groups-roles/directory-assign-admin-roles.md) | N/D |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Usuário administrador](../users-groups-roles/directory-assign-admin-roles.md) | N/D |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Administrador do Intune](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário do catálogo | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Administrador do Exchange](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário do catálogo |  | :heavy_check_mark: |  |  |
| [Administrador de serviços de equipes](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário do catálogo |  | :heavy_check_mark: |  |  |
| [Administrador do SharePoint](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário do catálogo |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Administrador de aplicativo](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário do catálogo |  |  | :heavy_check_mark: |  |
| [Administrador de aplicativos de nuvem](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário do catálogo |  |  | :heavy_check_mark: |  |
| User | Proprietário do catálogo | Proprietário do grupo somente se | Proprietário do grupo somente se | Somente se o proprietário de aplicativo |  |

## <a name="add-a-catalog-creator"></a>Adicionar um criador de catálogo

Se você quiser delegar a criação do catálogo, você adicionar usuários à função de criador de catálogo.  Você pode adicionar usuários individuais, ou para sua conveniência pode adicionar um grupo cujos membros são capazes de criar catálogos. Siga estas etapas para atribuir um usuário à função de criador de catálogo.

**Função de pré-requisito:** Administrador global ou administrador de usuário

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, na **gerenciamento de direitos** seção, clique em **configurações**.

1. Clique em **Editar**.

1. No **delegar o gerenciamento de direitos** seção, clique em **adicionar criadores de catálogo** para selecionar os usuários ou grupos que serão os membros para esta função de gerenciamento de direitos.

1. Clique em **Selecionar**.

1. Clique em **Salvar**.

## <a name="add-a-catalog-owner-or-an-access-package-manager"></a>Adicionar um proprietário do catálogo ou um Gerenciador de pacotes de acesso

Se você deseja delegar o gerenciamento de um pacote de acesso ou o catálogo no catálogo, você adicionar usuários para o proprietário do catálogo ou funções de acesso do Gerenciador de pacote. Quem cria um catálogo se torna o proprietário do catálogo primeiro. Siga estas etapas para atribuir um usuário para o proprietário do catálogo ou função de Gerenciador de pacote de acesso.

**Função de pré-requisito:** Usuário administrador ou proprietário do catálogo

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **catálogos** e, em seguida, abra o catálogo que você deseja adicionar os administradores.

1. No menu à esquerda, clique em **funções e os administradores**.

1. Clique em **adicionar proprietários** ou **adicionar gerenciadores de pacotes de acesso** para selecionar os membros para essas funções.

1. Clique em **selecionar** para adicionar esses membros.

## <a name="next-steps"></a>Próximas etapas

- [Adicione aprovadores](entitlement-management-access-package-edit.md#policy-request)
- [Adicionar recursos a um catálogo](entitlement-management-catalog-create.md#add-resources-to-a-catalog)
