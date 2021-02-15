---
title: Guia de referência de operações de gerenciamento de identidade e acesso do Azure Active Directory
description: Este guia de referência de operações descreve as verificações e ações que você deve executar para proteger as operações de gerenciamento de acesso e identidades
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
ms.openlocfilehash: 66bce573be5a31641bdff809b8e9a79b617a703a
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370994"
---
# <a name="azure-active-directory-identity-and-access-management-operations-reference-guide"></a>Guia de referência de operações de gerenciamento de identidade e acesso do Azure Active Directory

Esta seção do [Guia de referência de operações do Azure ad](active-directory-ops-guide-intro.md) descreve as verificações e ações que você deve considerar para proteger e gerenciar o ciclo de vida de identidades e suas atribuições.

> [!NOTE]
> Essas recomendações são atuais a partir da data de publicação, mas podem mudar ao longo do tempo. As organizações devem avaliar continuamente suas práticas de identidade à medida que os produtos e serviços da Microsoft evoluem ao longo do tempo.

## <a name="key-operational-processes"></a>Principais processos operacionais

### <a name="assign-owners-to-key-tasks"></a>Atribuir proprietários a tarefas principais

O gerenciamento de Azure Active Directory requer a execução contínua das principais tarefas operacionais e processos que podem não fazer parte de um projeto de distribuição. Ainda é importante que você configure essas tarefas para manter seu ambiente. As principais tarefas e seus proprietários recomendados incluem:

| Tarefa | Proprietário |
| :- | :- |
| Definir o processo como criar assinaturas do Azure | Varia de acordo com a organização |
| Decidir quem recebe licenças de Enterprise Mobility + Security | Equipe de operações IAM |
| Decidir quem recebe licenças de Microsoft 365 | Equipe de produtividade |
| Decida quem obtém outras licenças, por exemplo, Dynamics, Visual Studio Codespaces | Proprietário do Aplicativo |
| Atribuir licenças | Equipe de operações IAM |
| Solucionar problemas e corrigir erros de atribuição de licença | Equipe de operações IAM |
| Provisionar identidades para aplicativos no Azure AD | Equipe de operações IAM |

Ao examinar sua lista, você pode achar necessário atribuir um proprietário para tarefas que não têm um proprietário ou ajustar a propriedade para tarefas com proprietários que não estão alinhadas com as recomendações acima.

#### <a name="assigning-owners-recommended-reading"></a>Atribuindo a leitura recomendada de proprietários

- [Atribuindo funções de administrador no Azure Active Directory](../roles/permissions-reference.md)
- [Governança no Azure](../../governance/index.yml)

## <a name="on-premises-identity-synchronization"></a>Sincronização de identidade local

### <a name="identify-and-resolve-synchronization-issues"></a>Identificar e resolver problemas de sincronização

A Microsoft recomenda que você tenha uma boa linha de base e compreenda os problemas em seu ambiente local que podem resultar em problemas de sincronização para a nuvem. Como as ferramentas automatizadas, como [IdFix](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) e [Azure ad Connect Health](../hybrid/whatis-azure-ad-connect.md#why-use-azure-ad-connect-health) , podem gerar um alto volume de falsos positivos, recomendamos que você identifique os erros de sincronização que foram deixados não solucionados por mais de 100 dias limpando esses objetos com erro. Erros de sincronização não resolvidos de longo prazo podem gerar incidentes de suporte. A [solução de erros durante a sincronização](../hybrid/tshoot-connect-sync-errors.md) fornece uma visão geral dos diferentes tipos de erros de sincronização, alguns dos possíveis cenários que causam esses erros e possíveis maneiras de corrigir os erros.

### <a name="azure-ad-connect-sync-configuration"></a>Configuração de sincronização de Azure AD Connect

Para habilitar todas as experiências híbridas, a postura de segurança baseada em dispositivo e a integração com o Azure AD, é necessário que você sincronize as contas de usuário que seus funcionários usam para fazer logon em suas áreas de trabalho.

Se você não sincronizar o logon dos usuários da floresta no, deverá alterar a sincronização para vir da floresta apropriada.

#### <a name="synchronization-scope-and-object-filtering"></a>Escopo de sincronização e filtragem de objetos

A remoção de buckets conhecidos de objetos que não precisam ser sincronizados tem os seguintes benefícios operacionais:

- Menos fontes de erros de sincronização
- Ciclos de sincronização mais rápidos
- Menos "lixo" para postergar do local, por exemplo, a poluição da lista de endereços global para contas de serviço locais que não são relevantes na nuvem

> [!NOTE]
> Se você achar que está importando muitos objetos que não estão sendo exportados para a nuvem, filtre por UO ou atributos específicos.

Exemplos de objetos a serem excluídos são:

- Contas de serviço que não são usadas para aplicativos de nuvem
- Grupos que não devem ser usados em cenários de nuvem, como aqueles usados para conceder acesso a recursos
- Usuários ou contatos que são identidades externas que devem ser representadas com a colaboração B2B do Azure AD
- Contas de computador nas quais os funcionários não se destinam a acessar aplicativos de nuvem, por exemplo, servidores

> [!NOTE]
> Se uma única identidade humana tiver várias contas provisionadas de algo como uma migração de domínio herdada, fusão ou aquisição, você deverá sincronizar apenas a conta usada pelo usuário diariamente, por exemplo, o que eles usam para fazer logon em seu computador...

Idealmente, você desejará alcançar um equilíbrio entre a redução do número de objetos a serem sincronizados e a complexidade nas regras. Geralmente, uma combinação entre a [filtragem](../hybrid/how-to-connect-sync-configure-filtering.md) de UO/contêiner e um mapeamento de atributo simples para o atributo cloudFiltered é uma combinação de filtragem efetiva.

> [!IMPORTANT]
> Se você usar a filtragem de grupo na produção, deverá fazer a transição para outra abordagem de filtragem.

#### <a name="sync-failover-or-disaster-recovery"></a>Failover de sincronização ou recuperação de desastre

Azure AD Connect desempenha um papel fundamental no processo de provisionamento. Se o servidor de sincronização ficar offline por algum motivo, as alterações no local não poderão ser atualizadas na nuvem e poderão resultar em problemas de acesso para os usuários. Portanto, é importante definir uma estratégia de failover que permita que os administradores retomem a sincronização rapidamente depois que o servidor de sincronização ficar offline. Essas estratégias podem se enquadrar nas seguintes categorias:

- **Implantar servidores de Azure ad Connect no modo de preparo** – permite que um administrador "promova" o servidor de preparo para produção por uma opção de configuração simples.
- **Usar virtualização** – se o Azure ad Connect for implantado em uma VM (máquina virtual), os administradores poderão aproveitar sua pilha de virtualização para migrar ao vivo ou reimplantar rapidamente a VM e, portanto, retomar a sincronização.

Se sua organização não tiver uma estratégia de recuperação de desastre e failover para sincronização, você não deverá hesitar em implantar Azure AD Connect no modo de preparo. Da mesma forma, se houver uma incompatibilidade entre sua configuração de produção e de preparo, você deverá recriar novamente Azure AD Connect modo de preparo para corresponder à configuração de produção, incluindo versões de software e configurações.

![Uma captura de tela de Azure AD Connect configuração do modo de preparo](./media/active-directory-ops-guide/active-directory-ops-img1.png)

#### <a name="stay-current"></a>Mantenha-se atualizado

O Microsoft Updates Azure AD Connect regularmente. Mantenha-se atualizado para aproveitar as melhorias de desempenho, correções de bugs e novos recursos fornecidos por cada nova versão.

Se sua versão do Azure AD Connect tiver mais de seis meses, você deverá atualizar para a versão mais recente.

#### <a name="source-anchor"></a>Âncora de origem

Usar o **MS-DS-consistencyguid** como a [âncora de origem](../hybrid/plan-connect-design-concepts.md) permite uma migração mais fácil de objetos entre florestas e domínios, que é comum em consolidação/limpeza de domínio do AD, fusões, aquisições e divestitures.

Se, no momento, você estiver usando o **objectGUID** como a âncora de origem, recomendamos que você alterne para usando **MS-DS-ConsistencyGuid**.

#### <a name="custom-rules"></a>Regras personalizadas

Azure AD Connect regras personalizadas fornecem a capacidade de controlar o fluxo de atributos entre objetos locais e objetos de nuvem. No entanto, o uso de regras personalizadas ou o uso reutilizado de regra personalizada pode apresentar os seguintes riscos

- Complexidade da solução de problemas
- Degradação do desempenho ao executar operações complexas entre objetos
- Maior probabilidade de divergência de configuração entre o servidor de produção e o servidor de preparo
- Sobrecarga adicional ao atualizar Azure AD Connect se regras personalizadas forem criadas dentro da precedência maior que 100 (usado por regras internas)

Se você estiver usando regras excessivamente complexas, deverá investigar os motivos da complexidade e encontrar oportunidades de simplificação. Da mesma forma, se você tiver criado regras personalizadas com o valor de precedência acima de 100, corrija as regras para que elas não estejam em risco ou entrem em conflito com o conjunto padrão.

Exemplos de regras personalizadas inutilizadas incluem:

- **Compensar dados sujos no diretório** – nesse caso, é recomendável trabalhar com os proprietários da equipe do AD e limpar os dados no diretório como uma tarefa de correção e ajustar os processos para evitar a reintrodução de dados inválidos.
- **Correção única de usuários individuais** – é comum encontrar regras que sejam exceções de caso especial, geralmente devido a um problema com um usuário específico.
- **"CloudFiltering" muito complicado** – embora a redução do número de objetos seja uma boa prática, há um risco de criar um escopo de sincronização muito complicado usando muitas regras de sincronização. Se houver lógica complexa para incluir/excluir objetos além da filtragem de UO, é recomendável lidar com essa lógica fora da sincronização e rotular os objetos com um atributo "cloudFiltered" simples que pode fluir com uma simples regra de sincronização.

#### <a name="azure-ad-connect-configuration-documenter"></a>Azure AD Connect o Documentador de configuração

O [Documentador de configuração de Azure ad Connect](https://github.com/Microsoft/AADConnectConfigDocumenter) é uma ferramenta que você pode usar para gerar a documentação de uma instalação de Azure ad Connect para permitir uma melhor compreensão da configuração de sincronização, a confiança de segurança para obter as coisas corretas e saber o que foi alterado quando você aplicou uma nova compilação ou configuração de Azure ad Connect ou de regras de sincronização personalizadas ou adicionadas ou atualizadas. Os recursos atuais da ferramenta incluem:

- Documentação da configuração completa de Azure AD Connect sincronização.
- Documentação de quaisquer alterações na configuração de dois servidores de sincronização Azure AD Connect ou alterações de uma determinada linha de base de configuração.
- Geração de um script de implantação do PowerShell para migrar as diferenças ou personalizações de regra de sincronização de um servidor para outro.

## <a name="assignment-to-apps-and-resources"></a>Atribuição a aplicativos e recursos

### <a name="group-based-licensing-for-microsoft-cloud-services"></a>Licenciamento baseado em grupo para serviços de nuvem da Microsoft

Azure Active Directory simplifica o gerenciamento de licenças por meio de [Licenciamento baseado em grupo](./active-directory-licensing-whatis-azure-portal.md) para serviços de nuvem da Microsoft. Dessa forma, IAM fornece a infraestrutura de grupo e o gerenciamento delegado desses grupos para as equipes adequadas nas organizações. Há várias maneiras de configurar a associação de grupos no Azure AD, incluindo:

- **Sincronizados a partir de grupos locais** podem vir de diretórios locais, o que pode ser uma boa opção para organizações que estabeleceram processos de gerenciamento de grupo que podem ser estendidos para atribuir licenças em Microsoft 365.

- Os grupos **baseados em atributo/dinâmicos** podem ser criados na nuvem com base em uma expressão baseada em atributos de usuário, por exemplo, Department é igual a "Sales". O Azure AD mantém os membros do grupo, mantendo-os consistentes com a expressão definida. Usar esse tipo de grupo para atribuição de licença habilita uma atribuição de licença baseada em atributo, que é uma boa opção para organizações que têm alta qualidade de dados em seu diretório.

- **Propriedade delegada** – os grupos podem ser criados na nuvem e podem ser proprietários designados. Dessa forma, você pode capacitar os proprietários de negócios, por exemplo, a equipe de colaboração ou a equipe de BI, a definir quem deve ter acesso.

Se, no momento, você estiver usando um processo manual para atribuir licenças e componentes aos usuários, recomendamos que você implemente o licenciamento baseado em grupo. Se o seu processo atual não monitorar erros de licenciamento ou o que está atribuído versus disponível, você deverá definir melhorias para o processo de endereçamento de erros de licenciamento e monitorar a atribuição de licenciamento.

Outro aspecto do gerenciamento de licenças é a definição dos planos de serviço (componentes da licença) que devem ser habilitados com base nas funções de trabalho na organização. Conceder acesso a planos de serviço que não são necessários pode fazer com que os usuários vejam as ferramentas no portal do Office para as quais não foram treinados ou não devem estar usando o. Ele pode gerar um volume de suporte técnico adicional, provisionamento desnecessário e colocar sua conformidade e governança em risco, por exemplo, ao provisionar o OneDrive for Business para indivíduos que podem não ter permissão para compartilhar conteúdo.

Use as diretrizes a seguir para definir os planos de serviço para os usuários:

- Os administradores devem definir "pacotes" de planos de serviço a serem oferecidos aos usuários com base em sua função, por exemplo, trabalho de anéis branco versus trabalho de chão.
- Crie grupos por cluster e atribua a licença com o plano de serviço.
- Opcionalmente, um atributo pode ser definido para manter os pacotes para os usuários.

> [!IMPORTANT]
> O licenciamento baseado em grupo no Azure AD apresenta o conceito de usuários em um estado de erro de licenciamento. Se você observar quaisquer erros de licenciamento, deverá [identificar e resolver](../enterprise-users/licensing-groups-resolve-problems.md) imediatamente quaisquer problemas de atribuição de licença.

![Uma captura de tela de uma descrição do computador gerada automaticamente](./media/active-directory-ops-guide/active-directory-ops-img2.png)

#### <a name="lifecycle-management"></a>Gerenciamento do ciclo de vida

Se você estiver usando uma ferramenta, como [Microsoft Identity Manager](/microsoft-identity-manager/) ou sistema de terceiros, que se baseia em uma infraestrutura local, recomendamos que você descarregue a atribuição da ferramenta existente, implemente o licenciamento baseado em grupo e defina um gerenciamento de ciclo de vida de grupo com base em [grupos](../enterprise-users/licensing-group-advanced.md#use-group-based-licensing-with-dynamic-groups). Da mesma forma, se o processo existente não considerar novos funcionários ou funcionários que deixam a organização, você deverá implantar o licenciamento baseado em grupo com base em grupos dinâmicos e definir um ciclo de vida de associação de grupo. Por fim, se o licenciamento baseado em grupo for implantado em grupos locais que não têm gerenciamento do ciclo de vida, considere o uso de grupos de nuvem para habilitar funcionalidades como Propriedade delegada ou associação dinâmica baseada em atributo.

### <a name="assignment-of-apps-with-all-users-group"></a>Atribuição de aplicativos com o grupo "todos os usuários"

Os proprietários de recursos podem acreditar que o grupo **todos os usuários** contém apenas **funcionários empresariais** quando eles podem realmente conter funcionários e **convidados** **corporativos** . Como resultado, você deve tomar cuidado especial ao usar o grupo **todos os usuários** para atribuição de aplicativos e conceder acesso a recursos, como conteúdo ou aplicativos do SharePoint.

> [!IMPORTANT]
> Se o grupo **todos os usuários** estiver habilitado e usado para políticas de acesso condicional, aplicativo ou atribuição de recursos, certifique-se de [proteger o grupo](../external-identities/use-dynamic-groups.md) se não quiser que ele inclua usuários convidados. Além disso, você deve corrigir suas atribuições de licenciamento criando e atribuindo a grupos que contêm somente **funcionários da empresa** . Por outro lado, se você achar que o grupo **todos os usuários** está habilitado, mas não está sendo usado para conceder acesso a recursos, certifique-se de que a orientação operacional da sua organização seja usar intencionalmente esse grupo (que inclui funcionários e **convidados** **empresariais** ).

### <a name="automated-user-provisioning-to-apps"></a>Provisionamento automatizado de usuários para aplicativos

O [provisionamento automatizado de usuários](../app-provisioning/user-provisioning.md) para aplicativos é a melhor maneira de criar um provisionamento consistente, desprovisionamento e ciclo de vida de identidades em vários sistemas.

Se atualmente você estiver Provisionando aplicativos de maneira ad hoc ou usando itens como arquivos CSV, JIT ou uma solução local que não atenda ao gerenciamento do ciclo de vida, recomendamos que você [implemente o provisionamento de aplicativos](../app-provisioning/user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) com o Azure ad para aplicativos com suporte e defina um padrão consistente para aplicativos que ainda não têm suporte no Azure AD.

![Serviço de provisionamento do Azure AD](./media/active-directory-ops-guide/active-directory-ops-img3.png)

### <a name="azure-ad-connect-delta-sync-cycle-baseline"></a>Linha de base do ciclo de sincronização Delta Azure AD Connect

É importante entender o volume de alterações em sua organização e certificar-se de que não está demorando muito para ter um tempo de sincronização previsível.

A frequência de [sincronização Delta padrão](../hybrid/how-to-connect-sync-feature-scheduler.md) é de 30 minutos. Se a sincronização Delta estiver demorando mais de 30 minutos de forma consistente ou se houver discrepâncias significativas entre o desempenho de sincronização Delta de preparo e produção, você deverá investigar e examinar os [fatores que influenciam o desempenho do Azure ad Connect](../hybrid/plan-connect-performance-factors.md).

#### <a name="azure-ad-connect-troubleshooting-recommended-reading"></a>Azure AD Connect solução de problemas de leitura recomendada

- [Preparar atributos de diretório para sincronização com Microsoft 365 usando a ferramenta IdFix](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)
- [Azure AD Connect: solucionar erros durante a sincronização](../hybrid/tshoot-connect-sync-errors.md)

## <a name="summary"></a>Resumo

Há cinco aspectos para uma infraestrutura de identidade segura. Essa lista ajudará você a encontrar e executar rapidamente as ações necessárias para proteger e gerenciar o ciclo de vida de identidades e seus direitos em sua organização.

- Atribuir proprietários a tarefas principais.
- Localizar e resolver problemas de sincronização.
- Defina uma estratégia de failover para a recuperação de desastre.
- Simplifique o gerenciamento de licenças e a atribuição de aplicativos.
- Automatize o provisionamento de usuário para aplicativos.

## <a name="next-steps"></a>Próximas etapas

Introdução às [verificações e ações de gerenciamento de autenticação](active-directory-ops-guide-auth.md).