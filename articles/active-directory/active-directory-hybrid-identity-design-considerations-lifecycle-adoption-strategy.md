---
title: "Considerações sobre design da identidade híbrida do Azure Active Directory - determinar uma estratégia de adoção de ciclo de vida de identidade híbrida | Microsoft Docs"
description: "Auxilia na definição de tarefas de gerenciamento de identidade híbrida de acordo com as opções disponíveis para cada fase do ciclo de vida."
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: 420b6046-bd9b-4fce-83b0-72625878ae71
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 18b40486a66d8e092a8af299460145989a1ab99d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="determine-hybrid-identity-lifecycle-adoption-strategy"></a>Determinar uma estratégia de adoção para o ciclo de vida da identidade híbrida
Nesta tarefa, você define a estratégia de gerenciamento de identidade para sua solução de identidade híbrida, para atender aos requisitos de negócios definidos na seção [Determinar as tarefas de gerenciamento de identidade híbrida](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md).

Para definir as tarefas de gerenciamento de identidade híbrida de acordo com o ciclo de vida da identidade de ponta a ponta apresentado anteriormente nesta etapa, considere as opções disponíveis para cada fase do ciclo de vida.

## <a name="access-management-and-provisioning"></a>Provisionamento e gerenciamento de acesso
Com uma solução de gerenciamento de acesso de conta válida, você pode controlar de forma precisa os usuários que têm acesso às informações da organização.

O controle de acesso é uma função essencial de um sistema centralizado e de ponto único de provisionamento. Além de proteger informações confidenciais, os controles de acesso revelam as contas existentes cujas autorizações não são aprovadas ou não são mais necessárias. Para controlar as contas obsoletas, o sistema de provisionamento vincula as informações da conta às informações autoritativas sobre os proprietários das contas. As informações autoritativas de identidade do usuário normalmente são mantidas em bancos de dados e em diretórios de recursos humanos.

As contas de empresas de TI sofisticadas incluem centenas de parâmetros que definem as autoridades. Esses detalhes podem ser controlados pelo sistema de provisionamento. Os novos usuários podem ser identificados pelos dados fornecidos a partir da fonte autorizada. O recurso de aprovação de solicitação de acesso dá início aos processos de aprovação (ou de rejeição) do provisionamento de recursos para eles.

| Fase de gerenciamento do ciclo de vida | No local | Nuvem | Híbrido |
| --- | --- | --- | --- |
| Provisionamento e gerenciamento de contas |Usando a função de servidor dos Serviços de Domínio do Active Directory® (AD DS), crie uma infraestrutura escalonável, segura e fácil de gerenciar para o usuário e para o gerenciamento de recursos, e ofereça suporte para aplicativos habilitados por diretório, como o Microsoft® Exchange Server. <br><br> [Você pode provisionar grupos no AD DS por meio de um gerenciador de identidades](https://technet.microsoft.com/library/ff686261.aspx) <br>[ Você pode provisionar usuários no AD DS](https://technet.microsoft.com/library/ff686263.aspx) <br><br> Os administradores podem usar o controle de acesso para gerenciar o acesso de usuários e compartilhar recursos de segurança. No Active Directory, o controle de acesso é administrado no nível do objeto, estabelecendo diferentes níveis de acesso ou permissões para objetos, como Controle total, Gravação, Leitura ou Sem acesso. O controle de acesso no Active Directory determina como os usuários podem usar os objetos do Active Directory. As permissões para objetos no Active Directory são definidas com a configuração mais segura por padrão. |Você precisa criar uma conta para cada usuário que acessará um serviço de nuvem da Microsoft. Você também pode alterar as contas de usuário ou excluí-las quando elas não forem mais necessárias. Por padrão, os usuários não têm permissões de administrador, mas, opcionalmente, você pode atribuí-las. Para obter mais informações, veja [Gerenciando usuários no AD do Azure](active-directory-create-users.md). <br><br> Um dos principais recursos no Active Directory do Azure é a capacidade de gerenciar o acesso aos recursos. Esses recursos podem ser parte do diretório, como no caso de permissões para gerenciar objetos por meio de funções no diretório ou podem ser externos ao diretório, como aplicativos SaaS, serviços do Azure e sites do SharePoint ou recursos locais. <br><br> No centro da solução de gerenciamento de acesso do Active Directory do Azure está o grupo de segurança. O proprietário do recurso (ou o administrador do diretório) pode atribuir um grupo para fornecer um determinado acesso aos recursos que possui. Os membros do grupo receberão o acesso e o proprietário do recurso pode delegar a outra pessoa o direito de gerenciar a lista de membros de um grupo, como um gerente de departamento ou um administrador de assistência técnica<br> <br> O tópico Gerenciando grupos no AD do Azure fornece mais informações sobre o gerenciamento de acesso por meio dos grupos. |Estender as identidades do Active Directory para a nuvem através de sincronização e federação |

## <a name="role-based-access-control"></a>Controle de acesso baseado em função
O controle de acesso baseado em função (RBAC) usa funções e políticas de provisionamento para avaliar, testar e impor processos de negócios e regras para conceder acesso aos usuários. Os principais administradores criam políticas de provisionamento e atribuem usuários a funções, além de definir conjuntos de qualificações de recursos para essas funções. O RBAC amplia a solução de gerenciamento de identidades para usar processos baseados em software e reduzir a interação manual do usuário no processo de provisionamento.
O RBAC do AD do Azure permite à empresa restringir a quantidade de operações que um usuário pode realizar quando obtém acesso ao Portal de Gerenciamento do Azure. Ao usar o RBAC para controlar o acesso ao portal, os administradores de TI podem delegar o acesso usando as seguintes abordagens de gerenciamento de acesso:

* **Atribuição de função baseada em grupo**: você pode atribuir acesso aos grupos do AD do Azure, que podem ser sincronizados no Active Directory local. Isso permite aproveitar os investimentos existentes que sua organização já fez em ferramentas e processos para gerenciar os grupos. Também é possível usar o recurso de gerenciamento de grupos delegado do Azure AD Premium.
* **Aproveitar funções internas do Azure**: você pode usar três funções: Proprietário, Colaborador e Leitor para garantir que os usuários e grupos tenham permissão para realizar apenas as tarefas necessárias para concluir seus respectivos trabalhos.
* **Acesso granular aos recursos**: você pode atribuir funções a usuários e grupos de uma determinada assinatura, grupo de recursos ou recurso individual do Azure, como um site ou banco de dados. Dessa forma, é possível garantir que os usuários tenham acesso a todos os recursos necessários e nenhum acesso a recursos que não precisem gerenciar.

## <a name="provisioning-and-other-customization-options"></a>Provisionamento e outras opções de personalização
A equipe pode usar planos de negócios e requisitos para decidir até que ponto personalizar a solução de identidade. Por exemplo, uma grande empresa pode exigir um plano de distribuição em fases para fluxos de trabalho e adaptadores personalizados com base em uma linha do tempo para o provisionamento incremental de aplicativos que são amplamente usados entre regiões geográficas. Outro plano de personalização pode fornecer dois ou mais aplicativos para provisionar a organização, após a realização de testes bem-sucedidos. A interação do usuário de aplicativo pode ser personalizada e os procedimentos para o provisionamento de recursos podem ser alterados para se ajustar ao provisionamento automatizado.

Você pode desprovisionar para remover um serviço ou componente. Por exemplo, o desprovisionamento de uma conta significa que a conta é excluída de um recurso.

O modelo híbrido de provisionamento de recursos combina solicitações e abordagens baseadas em função, que têm suporte do AD do Azure. Uma empresa pode querer automatizar o acesso com atribuições baseadas em função para um subconjunto de funcionários ou de sistemas gerenciados. A empresa pode também querer lidar com todas as outras solicitações ou exceções de acesso por meio de um modelo baseado em solicitação. Algumas empresas podem começar usando atribuições manuais e evoluir no sentido de um modelo híbrido, visando uma implantação totalmente baseada em função no futuro.

Outras empresas podem considerar impraticável alcançar o provisionamento total baseado em função por razões comerciais e focar em uma abordagem híbrida como objetivo pretendido. Outras empresas podem ainda ficar satisfeitas apenas com o provisionamento baseado em solicitação e não pretender fazer mais investimentos para definir e gerenciar políticas de provisionamento automatizado com base em função.

## <a name="license-management"></a>Gerenciamento de licenças
O gerenciamento de licenças com base em grupo no AD do Azure permite aos administradores atribuir usuários a um grupo de segurança, ao passo que o AD do Azure atribui licenças automaticamente a todos os membros do grupo. Quando um usuário é adicionado ao grupo ou removido posteriormente, a licença será automaticamente atribuída ou removida conforme apropriado.

Use grupos de sincronização do AD local ou gerencie-os no AD do Azure. Com o emparelhamento desses grupos ao Gerenciamento de Grupo de Autoatendimento do Azure AD Premium, você pode facilmente delegar a atribuição de licenças para as pessoas responsáveis pela tomada de decisões. Você vai garantir que os problemas relacionados a conflitos de licenças e dados de localização ausentes sejam classificados automaticamente.

## <a name="self-regulating-user-administration"></a>Administração autoreguladora de usuários
Quando a empresa começa a provisionar recursos para todas as organizações internas, você pode implementar o recurso autorregulador de administração de usuário. Aproveite as vantagens e os benefícios do provisionamento de usuários através dos limites organizacionais. Nesse ambiente, uma alteração no status de um usuário se reflete automaticamente nos direitos de acesso em regiões geográficas e limites da organização. Você pode reduzir os custos de provisionamento e simplificar os processos de aprovação e acesso. A implementação envolve todo o potencial da implementação do controle de acesso baseado em função para o gerenciamento de acesso de ponta a ponta na empresa. Reduza os custos administrativos por meio de procedimentos automatizados para controlar o provisionamento de usuários. Você pode aprimorar a segurança automatizando a aplicação de políticas de segurança, além de simplificar e centralizar o gerenciamento do ciclo de vida do usuário e o provisionamento de recursos para grandes grupos de usuários.

> [!NOTE]
> Para saber mais, veja o tópico Configurando o AD do Azure para o gerenciamento de acesso a aplicativos por autoatendimento
> 
> 

Os Serviços do AD do Azure baseados em licença (baseados em direito) funcionam ativando uma assinatura no locatário de serviço/diretório do AD do Azure. Quando a assinatura está ativa, os recursos de serviço podem ser gerenciados por administradores de serviço/diretório e usados por usuários licenciados. Para saber mais, consulte o tópico Como funciona o licenciamento do AD do Azure?
Integração com outros provedores de terceiros

O Active Directory do Azure fornece logon único e segurança avançada de acesso a aplicativos para milhares de aplicativos SaaS e aplicativos Web locais. Para obter uma lista detalhada da Galeria de Aplicativos do Active Directory do Azure para aplicativos SaaS com suporte, consulte a lista de compatibilidade de federação do Active Directory do Azure: provedores de identidade de terceiros que podem ser usados para implementar o logon único

## <a name="define-synchronization-management"></a>Definir o gerenciamento de sincronização
A integração de seus diretórios locais ao AD do Azure torna os usuários mais produtivos fornecendo uma identidade comum para acesso aos recursos na nuvem e locais. Com essa integração, os usuários e as organizações podem se beneficiar do seguinte:

* As organizações podem fornecer aos usuários uma identidade híbrida comum para serviços baseados em nuvem ou locais, aproveitando o Active Directory do Windows Server e, em seguida, conectando-se ao Active Directory do Azure.
* Os administradores podem fornecer acesso condicional com base no recurso do aplicativo, na identidade de usuário e dispositivo, no local de rede e na autenticação multifator.
* Os usuários podem aproveitar sua identidade comum por meio das contas no AD do Azure para o Office 365, o Intune, os aplicativos SaaS e os aplicativos de terceiros.
* Os desenvolvedores podem criar aplicativos que aproveitam o modelo de identidade comum, integrando aplicativos ao Active Directory local ou o Azure para aplicativos baseados em nuvem.

A imagem a seguir mostra o exemplo de uma exibição de alto nível do processo de sincronização de identidades.

![](./media/hybrid-id-design-considerations/identitysync.png)

Processo de sincronização de identidades

Analise a tabela a seguir para comparar as opções de sincronização:

| Opção de gerenciamento de sincronização | Vantagens | Desvantagens |
| --- | --- | --- |
| Com base em sincronização (através do DirSync ou do AADConnect) |Usuários e grupos sincronizados no local e na nuvem  <br>  **Controle de políticas**: as Políticas de Conta podem ser definidas no Active Directory, que fornece ao administrador a capacidade de gerenciar políticas de senha, estações de trabalho, restrições, controles de bloqueio e muito mais, sem ter que realizar outras tarefas na nuvem.  <br>  **Controle de acesso**: pode restringir o acesso ao serviço de nuvem para que os serviços possam ser acessados através do ambiente corporativo, através de servidores online, ou das duas maneiras. <br>  Menos chamadas de suporte: quando os usuários têm menos senhas para se lembrar, é menos provável esquecê-las. <br>  Segurança: as informações e identidades dos usuários são protegidas porque todos os servidores e serviços usados no logon único são gerenciados e controlados localmente. <br>  Suporte para autenticação forte: use uma autenticação forte (também chamada de autenticação de dois fatores) com o serviço de nuvem. No entanto, se usar esse recurso, você deve usar o logon único. | |
| Com base em federação (através do AD FS) |Habilitado pelo serviço de token de segurança (STS). Quando você configura um STS para fornecer acesso de logon único com um serviço de nuvem da Microsoft, cria ao mesmo tempo uma relação de confiança federada entre o STS local e o domínio federado que você especificou no locatário do AD do Microsoft Azure. <br> Permite aos usuários finais usar o mesmo conjunto de credenciais para obter acesso a vários recursos <br>Os usuários finais não precisam manter vários conjuntos de credenciais. Além disso, os usuários devem fornecer suas credenciais a cada um dos recursos participantes. Cenários com suporte para relações B2B e B2C. |Requer profissionais especializados para implantação e manutenção de servidores dedicados do AD FS local. Há restrições sobre o uso de autenticação forte, caso planeje usar o AD FS para o STS. Para saber mais, consulte o artigo [Configurando opções avançadas do AD FS 2.0](http://go.microsoft.com/fwlink/?linkid=235649). |

> [!NOTE]
> Para saber mais, veja o artigo [Integrando identidades locais com o Active Directory do Azure](active-directory-aadconnect.md).
> 
> 

## <a name="see-also"></a>Consulte também
[Visão geral sobre as considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)

