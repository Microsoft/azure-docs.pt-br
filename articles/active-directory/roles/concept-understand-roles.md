---
title: Compreender os conceitos de funções do Azure Active Directory
description: Saiba mais sobre as funções internas e personalizadas do Azure Active Directory com o escopo de recursos no Azure Active Directory.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/20/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71291458eec0aec13542d3e0dfaf04a96f391a23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103466109"
---
# <a name="understand-roles-in-azure-active-directory"></a>Compreender funções no Azure Active Directory

Há cerca de 60 funções internas do Azure AD (Azure Active Directory), que são funções com um conjunto fixo de permissões de função. Para complementar as funções internas, o Azure AD também dá suporte a funções personalizadas. Use funções personalizadas para selecionar as permissões de função que você deseja. Por exemplo, você pode criar uma para gerenciar recursos específicos do Azure AD, como aplicativos ou entidades de serviço.

Este artigo explica o que são funções do Azure AD e como elas podem ser usadas.

## <a name="how-azure-ad-roles-are-different-from-other-microsoft-365-roles"></a>Como as funções do Azure AD são diferentes de outras funções do Microsoft 365

Há vários serviços diferentes no Microsoft 365, como o Azure AD e o Intune. Alguns desses serviços têm os próprios sistemas de controle de acesso baseado em função; especificamente:

- AD do Azure
- Exchange
- Intune
- Central de Segurança
- Centro de Conformidade
- Microsoft Cloud App Security
- Comércio

Outros serviços, como o Teams, o SharePoint e a Área de Trabalho Gerenciada, não têm sistemas de controle de acesso baseado em função separados. Eles usam funções do Azure AD para acesso administrativo. O Azure tem o próprio sistema de controle de acesso baseado em função para recursos do Azure, como máquinas virtuais, e esse sistema não é o mesmo que as funções do Azure AD.

Quando dizemos sistema de controle de acesso baseado em função separado, isso significa que há um armazenamento de dados diferente no qual as definições e atribuições de função são armazenadas. Da mesma forma, há um ponto de decisão de política diferente no qual as verificações de acesso acontecem. Para obter mais informações, confira [Funções para serviços do Microsoft 365 no Azure AD](m365-workload-docs.md) e [Funções clássicas de administrador da assinatura, funções do Azure e funções do Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="why-some-azure-ad-roles-are-for-other-services"></a>Por que algumas funções do Azure AD são feitas para outros serviços

O Microsoft 365 tem vários sistemas de controle de acesso baseado em função que foram desenvolvidos independentemente ao longo do tempo, cada um com o próprio portal de serviço. Para facilitar o gerenciamento de identidades no Microsoft 365 do portal do Azure AD, adicionamos algumas funções internas específicas do serviço, cada uma delas permite acesso administrativo a um serviço do Microsoft 365. Um exemplo dessa adição é a função Administrador do Exchange no Azure AD. Essa função é equivalente ao [grupo de funções de Gerenciamento da Organização](/exchange/organization-management-exchange-2013-help) no sistema de controle de acesso baseado em função do Exchange e pode gerenciar todos os aspectos do Exchange. Da mesma forma, adicionamos a função Administrador do Intune, Administrador do Teams, Administrador do SharePoint e assim por diante. As funções específicas do serviço são uma categoria de funções internas do Azure AD que serão mostradas na seção a seguir.

## <a name="categories-of-azure-ad-roles"></a>Categorias de funções do Azure AD

As funções internas do Azure AD podem ser usadas em locais diferentes, que se enquadram nas três categorias amplas a seguir.

- **Usuários em funções específicas do Azure AD**: Essas funções concedem permissões para gerenciar recursos somente no Azure AD. Por exemplo, Administrador de Usuários, Administrador de Aplicativos, Administrador de Grupos concedem permissões para gerenciar recursos que residem no Azure AD.
- **Funções específicas do serviço**: Para os principais serviços do Microsoft 365 (não Azure AD), criamos funções específicas de serviço que concedem permissões para gerenciar todos os recursos no serviço.  Por exemplo, as funções Administrador do Exchange, Administrador do Intune, Administrador do SharePoint e Administrador do Teams podem gerenciar recursos com os respectivos serviços. O Administrador do Exchange pode gerenciar caixas de correio, o Administrador do Intune pode gerenciar políticas de dispositivo, o Administrador do SharePoint pode gerenciar conjuntos de sites, o Administrador do Teams pode gerenciar a qualidade da chamada e assim por diante.
- **Funções entre serviços**: Há algumas funções que abrangem serviços. Temos duas funções globais: Administrador global e Leitor Global. Todos os serviços do Microsoft 365 têm essas duas funções. Além disso, há algumas funções relacionadas à segurança, como o Administrador de Segurança e o Leitor de Segurança que permitem acesso entre vários serviços de segurança no Microsoft 365. Por exemplo, usando funções do Administrador de Segurança no Azure AD, você pode gerenciar a Central de Segurança do Microsoft 365, a Proteção Avançada contra Ameaças do Microsoft Defender e o Microsoft Cloud App Security. Da mesma forma, na função Administrador de Conformidade, você pode gerenciar configurações relacionadas à Conformidade no Centro de Conformidade do Microsoft 365, no Exchange e assim por diante.

![As três categorias de funções internas do Azure AD](./media/concept-understand-roles/role-overlap-diagram.png)

A tabela a seguir é auxilia o entendimento dessas categorias de função. As categorias são nomeadas arbitrariamente e não se destinam a implicar outras funcionalidades além das [permissões de função documentadas](permissions-reference.md).

Categoria | Função
---- | ----
Usuários em funções específicas do Azure AD | Administrador de aplicativos<br>Desenvolvedor de aplicativos<br>Administrador de Autenticação<br>Administrador de Conjunto de Chaves do IEF B2C<br>Administrador de Política do IEF B2C<br>Administrador de Aplicativos de Nuvem<br>Administrador de Dispositivo de Nuvem<br>Administrador de Acesso Condicional<br>Administradores de Dispositivo<br>Leitores de Diretório<br>Contas de sincronização de diretório<br>Gravadores de diretório<br>Administrador de fluxo do usuário de ID externa<br>Administrador de atributo de fluxo do usuário de ID externa<br>Administrador do Provedor de Identidade Externa<br>Administrador de Grupos<br>Emissor do Convite ao Convidado<br>Administrador de assistência técnica<br>Administrador de Identidade Híbrida<br>Administrador de Licenças<br>Suporte de camada 1 do parceiro<br>Suporte de camada 2 do parceiro<br>Administrador de senha<br>Administrador de Autenticação Privilegiada<br>Administrador de função com privilégios<br>Leitor de Relatórios<br>Administrador da conta de usuário
Funções entre serviços | Administrador Global<br>Administrador de conformidade<br>Administrador de dados de conformidade<br>Leitor global<br>Administrador de Segurança<br>Operador de segurança<br>Leitor de segurança<br>Administrador de suporte a serviço
Funções específicas do serviço | Administrador do Azure DevOps<br>Administrador da Proteção de Informações do Azure<br>Administrador de cobrança<br>Administrador de serviços do CRM<br>Aprovador de acesso do cofre do cliente<br>Administrador de Análise de Área de Trabalho<br>Administrador de serviços do Exchange<br>Administrador do Insights<br>Líder de negócios do Insights<br>Administrador de serviços do Intune<br>Administrador do Kaizala<br>Administrador de serviços do Lync<br>Leitor de Privacidade do Centro de Mensagens<br>Leitor do Centro de Mensagens<br>Usuário moderno do Commerce<br>Administrador de Rede<br>Administrador de Aplicativos do Office<br>Administrador de serviços do Power BI<br>Administrador do Power Platform<br>Administrador de Impressora<br>Técnico de Impressora<br>Administrador de Pesquisas<br>Editor de Pesquisa<br>Administrador de serviços do SharePoint<br>Administrador de Comunicações do Teams<br>Engenheiro de Suporte de Comunicações do Teams<br>Especialista em Suporte de Comunicações do Teams<br>Administrador de dispositivos do Teams<br>Administrador de Serviços do Teams

## <a name="next-steps"></a>Próximas etapas

- [Visão geral do controle de acesso baseado em função do Azure AD](custom-overview.md)
- Criar atribuições de função usando [o portal do Azure, o Azure AD PowerShell e a API do Graph](custom-create.md)
- [Listar atribuições de função](view-assignments.md)
