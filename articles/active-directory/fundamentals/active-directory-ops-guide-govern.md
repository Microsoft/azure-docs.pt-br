---
title: Guia de referência de operações de governança de Azure Active Directory
description: Este guia de referência de operações descreve as verificações e ações que você deve seguir para proteger o gerenciamento de governança
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: ba3ca140abe36a31ffa03422420ea537bbe4f39a
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935743"
---
# <a name="azure-active-directory-governance-operations-reference-guide"></a>Guia de referência de operações de governança de Azure Active Directory

Esta seção do [Guia de referência de operações do Azure ad](active-directory-ops-guide-intro.md) descreve as verificações e as ações que você deve seguir para avaliar e atestar o acesso concedido a identidades sem privilégios e privilegiadas, auditoria e controle de alterações no ambiente.

> [!NOTE]
> Essas recomendações são atuais a partir da data de publicação, mas podem mudar ao longo do tempo. As organizações devem avaliar continuamente suas práticas de governança à medida que os produtos e serviços da Microsoft evoluem ao longo do tempo.

## <a name="key-operational-processes"></a>Principais processos operacionais

### <a name="assign-owners-to-key-tasks"></a>Atribuir proprietários a tarefas principais

O gerenciamento de Azure Active Directory requer a execução contínua das principais tarefas e processos operacionais, que podem não fazer parte de um projeto de distribuição. Ainda é importante que você configure essas tarefas para otimizar seu ambiente. As principais tarefas e seus proprietários recomendados incluem:

| Tarefa | Proprietário |
| :- | :- |
| Arquivar logs de auditoria do Azure AD no sistema SIEM | Equipe de operações do batalha |
| Descobrir aplicativos que são gerenciados fora de conformidade | Equipe de operações IAM |
| Examine regularmente o acesso a aplicativos | Equipe de arquitetura do batalha |
| Examine regularmente o acesso a identidades externas | Equipe de arquitetura do batalha |
| Examine regularmente quem tem funções com privilégios | Equipe de arquitetura do batalha |
| Definir Gates de segurança para ativar funções com privilégios | Equipe de arquitetura do batalha |
| Examinar regularmente as concessões de consentimento | Equipe de arquitetura do batalha |
| Criar catálogos e acessar pacotes para aplicativos e recursos com base em funcionários da organização | Proprietários do aplicativo |
| Definir políticas de segurança para atribuir usuários a pacotes do Access | Equipe do batalha + proprietários do aplicativo |
| Se as políticas incluírem fluxos de trabalho de aprovação, examine regularmente as aprovações de fluxo de trabalho | Proprietários do aplicativo |
| Examinar exceções em políticas de segurança, como políticas de acesso condicional, usando revisões de acesso | Equipe de operações do batalha |

Ao examinar sua lista, você pode achar necessário atribuir um proprietário para tarefas que não têm um proprietário ou ajustar a propriedade para tarefas com proprietários que não estão alinhadas com as recomendações acima.

#### <a name="owner-recommended-reading"></a>Leitura recomendada pelo proprietário

- [Atribuindo funções de administrador no Azure Active Directory](../roles/permissions-reference.md)
- [Governança no Azure](../../governance/index.yml)

### <a name="configuration-changes-testing"></a>Testes de alterações de configuração

Há alterações que exigem considerações especiais durante o teste, desde técnicas simples, como a distribuição de um subconjunto de destino de usuários para a implantação de uma alteração em um locatário de teste paralelo. Se você ainda não implementou uma estratégia de teste, defina uma abordagem de teste com base nas diretrizes na tabela abaixo:

| Cenário| Recomendação |
|-|-|
|Alterando o tipo de autenticação de federado para PHS/PTA ou vice-versa| Use a [distribuição em etapas](../hybrid/how-to-connect-staged-rollout.md) para testar o impacto da alteração do tipo de autenticação.|
|Distribuir uma nova política de acesso condicional (AC) ou política de proteção de identidade|Crie uma nova política de acesso condicional e atribua a usuários de teste.|
|Integração de um ambiente de teste de um aplicativo|Adicione o aplicativo a um ambiente de produção, oculte-o no painel myapps e atribua-o aos usuários de teste durante a fase de controle de qualidade (QA).|
|Alteração de regras de sincronização|Execute as alterações em um Azure AD Connect de teste com a mesma configuração que está atualmente em produção, também conhecida como modo de preparo, e analise os resultados de CSExport. Se for satisfeita, troque para produção quando estiver pronto.|
|Alteração de identidade visual|Teste em um locatário de teste separado.|
|Distribuindo um novo recurso|Se o recurso der suporte a distribuição para um conjunto de usuários de destino, identifique os usuários piloto e compile. Por exemplo, a redefinição de senha de autoatendimento e a autenticação multifator podem ter como destino usuários ou grupos específicos.|
|Fazer a transferência de um aplicativo de um provedor de identidade local (IdP), por exemplo, Active Directory, para o Azure AD|Se o aplicativo oferecer suporte a várias configurações de IdP, por exemplo, Salesforce, configure e teste o Azure AD durante uma janela de alteração (caso o aplicativo apresente a página HRD). Se o aplicativo não oferecer suporte a vários IdPs, agende o teste durante uma janela de controle de alterações e o tempo de inatividade do programa.|
|Atualizar regras de grupo dinâmico|Crie um grupo dinâmico paralelo com a nova regra. Compare com o resultado calculado, por exemplo, execute o PowerShell com a mesma condição.<br>Se o teste for aprovado, troque os locais em que o grupo antigo foi usado (se possível).|
|Migrar licenças de produtos|Consulte [alterar a licença de um único usuário em um grupo licenciado no Azure Active Directory](../enterprise-users/licensing-groups-change-licenses.md).|
|Alterar AD FS regras como autorização, emissão, MFA|Use a declaração de grupo para o subconjunto de usuários de destino.|
|Alteração AD FS experiência de autenticação ou alterações semelhantes em todo o farm|Crie um farm paralelo com o mesmo nome de host, implemente alterações de configuração, teste de clientes usando arquivo de HOSTs, regras de roteamento de NLB ou roteamento semelhante.<br>Se a plataforma de destino não oferecer suporte a arquivos de HOSTs (por exemplo, dispositivos móveis), altere o controle.|

## <a name="access-reviews"></a>Análises de acesso

### <a name="access-reviews-to-applications"></a>Revisões de acesso a aplicativos

Ao longo do tempo, os usuários podem acumular o acesso aos recursos à medida que eles se movem em diferentes equipes e cargos. É importante que os proprietários de recursos revisem o acesso a aplicativos regularmente e removam os privilégios que não são mais necessários durante todo o ciclo de vida dos usuários. As [revisões de acesso](../governance/access-reviews-overview.md) do Azure ad permitem que as organizações gerenciem com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. Os proprietários de recursos devem revisar o acesso dos usuários regularmente para garantir que apenas as pessoas certas tenham acesso contínuo. Idealmente, você deve considerar o uso de revisões de acesso do Azure AD para essa tarefa.

![Página inicial das revisões de acesso](./media/active-directory-ops-guide/active-directory-ops-img15.png)

> [!NOTE]
> Cada usuário que interage com as revisões de acesso deve ter uma licença paga de Azure AD Premium P2.

### <a name="access-reviews-to-external-identities"></a>Revisões de acesso a identidades externas

É crucial manter o acesso a identidades externas restritas apenas aos recursos que são necessários, durante o tempo necessário. Estabeleça um processo de revisão de acesso automatizado regular para todas as identidades externas e acesso ao aplicativo usando as [revisões de acesso](../governance/access-reviews-overview.md)do Azure AD. Se um processo já existir no local, considere o uso das revisões de acesso do Azure AD. Depois que um aplicativo for desativado ou não mais usado, remova todas as identidades externas que tinham acesso ao aplicativo.

> [!NOTE]
> Cada usuário que interage com as revisões de acesso deve ter uma licença paga de Azure AD Premium P2.

## <a name="privileged-account-management"></a>Gerenciamento de contas com privilégios

### <a name="privileged-account-usage"></a>Uso de conta privilegiada

Os hackers geralmente visam contas de administrador e outros elementos de acesso privilegiado para obter acesso rápido a dados e sistemas confidenciais. Como os usuários com funções privilegiadas tendem a se acumular ao longo do tempo, é importante revisar e gerenciar o acesso de administrador regularmente e fornecer acesso privilegiado just-in-time ao Azure AD e aos recursos do Azure.

Se não houver nenhum processo em sua organização para gerenciar contas privilegiadas, ou se você tiver administradores que usam suas contas de usuário regulares para gerenciar serviços e recursos, você deverá começar imediatamente a usar contas separadas, por exemplo, uma para atividades normais do dia a dia; o outro para acesso privilegiado e configurado com MFA. Melhor ainda, se sua organização tiver uma assinatura Azure AD Premium P2, você deverá implantar imediatamente [Azure ad Privileged Identity Management](../privileged-identity-management/pim-configure.md#license-requirements) (PIM). No mesmo token, você também deve examinar essas contas com privilégios e [atribuir funções menos privilegiadas](../roles/security-planning.md) , se aplicável.

Outro aspecto do gerenciamento de contas privilegiadas que deve ser implementado é a definição de [revisões de acesso](../governance/access-reviews-overview.md) para essas contas, seja manual ou [automatizada por meio do PIM](../privileged-identity-management/pim-how-to-perform-security-review.md).

#### <a name="privileged-account-management-recommended-reading"></a>Leitura recomendada de gerenciamento de contas privilegiadas

- [Funções no Azure AD Privileged Identity Management](../privileged-identity-management/pim-roles.md)

### <a name="emergency-access-accounts"></a>Contas de acesso de emergência

As organizações devem criar [contas de emergência](../roles/security-emergency-access.md) para se preparar para gerenciar o Azure ad para casos como interrupções de autenticação como:

- Componentes de interrupção das infraestruturas de autenticação (AD FS, AD local, serviço de MFA)
- Rotatividade de equipe administrativa

Para evitar que seja bloqueado inadvertidamente do seu locatário porque você não pode entrar ou ativar uma conta de usuário individual existente como administrador, você deve criar duas ou mais contas de emergência e garantir que elas sejam implementadas e alinhadas com [as práticas recomendadas da Microsoft](../roles/security-planning.md) e os [procedimentos de ruptura](../roles/security-planning.md#break-glass-what-to-do-in-an-emergency).

### <a name="privileged-access-to-azure-ea-portal"></a>Acesso privilegiado ao portal de EA do Azure

O [portal do azure Enterprise Agreement (Azure ea)](https://azure.microsoft.com/blog/create-enterprise-subscription-experience-in-azure-portal-public-preview/) permite que você crie assinaturas do Azure em um Enterprise Agreement mestre, que é uma função avançada na empresa. É comum inicializar a criação desse portal antes mesmo de colocar o Azure AD, portanto, é necessário usar as identidades do Azure AD para bloqueá-lo, remover contas pessoais do portal, garantir que a delegação adequada esteja em vigor e reduzir o risco de bloqueio.

Para ser claro, se o nível de autorização do portal de EA estiver definido atualmente como "modo misto", você deverá remover todas as [contas da Microsoft](https://support.skype.com/en/faq/FA12059/what-is-a-microsoft-account) de todo o acesso privilegiado no portal de ea e configurar o portal de ea para usar apenas as contas do Azure AD. Se as funções delegadas do portal de EA não estiverem configuradas, você também deverá encontrar e implementar funções delegadas para departamentos e contas.

#### <a name="privileged-access-recommended-reading"></a>Leitura recomendada de acesso privilegiado

- [Permissões da função de administrador no Azure Active Directory](../roles/permissions-reference.md)

## <a name="entitlement-management"></a>Gerenciamento de direitos

O [Gerenciamento de direitos (em)](../governance/entitlement-management-overview.md) permite que os proprietários de aplicativos agrupem recursos e os atribua a pessoas específicas na organização (tanto interna quanto externa). O em permite a inscrição e a delegação de autoatendimento para proprietários de negócios, mantendo as políticas de governança para conceder acesso, definir durações de acesso e permitir fluxos de trabalho de aprovação. 

> [!NOTE]
> O gerenciamento de direitos do Azure AD requer licenças Azure AD Premium P2.

## <a name="summary"></a>Resumo

Há oito aspectos para um controle de identidade seguro. Esta lista ajudará você a identificar as ações que você deve seguir para avaliar e atestar o acesso concedido a identidades sem privilégios e privilegiadas, auditoria e alterações de controle no ambiente.

- Atribuir proprietários a tarefas principais.
- Implemente uma estratégia de teste.
- Use as revisões de acesso do Azure AD para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função.
- Estabeleça um processo de revisão de acesso automatizado regular para todos os tipos de identidades externas e acesso ao aplicativo.
- Estabeleça um processo de análise de acesso para revisar e gerenciar o acesso de administrador regularmente e fornecer acesso privilegiado just-in-time ao Azure AD e aos recursos do Azure.
- Provisione contas de emergência para estar preparado para gerenciar o Azure AD para interrupções inesperadas.
- Bloquear o acesso ao portal de EA do Azure.
- Implemente o gerenciamento de direitos para fornecer acesso controlado a uma coleção de recursos.

## <a name="next-steps"></a>Próximas etapas

Introdução às [verificações e ações operacionais do Azure ad](active-directory-ops-guide-ops.md).