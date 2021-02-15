---
title: Quatro etapas para uma base de identidade forte – Azure AD
description: Este tópico descreve quatro etapas que os clientes de identidade híbrida podem tomar para criar uma base de identidade forte.
services: active-directory
author: martincoetzer
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2019
ms.subservice: hybrid
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 795f5ede382e561ee810e54e1f8897c5d806e8b8
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412367"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>Quatro etapas para uma base de identidade forte com Azure Active Directory

O gerenciamento de acesso a aplicativos e dados não pode mais depender das estratégias de limite de segurança de rede tradicionais, como redes de perímetro e firewalls, devido à rápida movimentação de aplicativos para a nuvem. Agora, as organizações devem confiar em sua solução de identidade para controlar quem e o que tem acesso aos aplicativos e dados da organização. Mais organizações estão permitindo que os funcionários tragam seus próprios dispositivos para trabalhar e usar seus dispositivos em qualquer lugar em que possam se conectar à Internet. Garantir que esses dispositivos estejam em conformidade e seguro se tornou uma consideração importante na solução de identidade que uma organização opta por implementar. No local de trabalho digital de hoje, [a identidade é o plano de controle principal](https://www.microsoft.com/security/technology/identity-access-management?rtc=1) de qualquer organização que se move para a nuvem.

Ao adotar uma solução de identidade híbrida do Azure Active Directory (Azure AD), as organizações têm acesso a recursos premium que desbloqueiam a produtividade por meio de recursos de automação, delegação, autoatendimento e logon único. Ele permite que os seus trabalhadores acessem os recursos da empresa em qualquer lugar onde precisem realizar seu trabalho, permitindo que sua equipe de ti administre esse acesso, garantindo que as pessoas certas tenham o acesso certo aos recursos certos para estabelecer uma produtividade segura.

Com base em nossas aprendeções, esta lista de verificação das práticas recomendadas ajudará você a implantar rapidamente as ações recomendadas para criar uma base de identidade *forte* em sua organização:

* Conectar-se a aplicativos facilmente
* Estabelecer uma identidade para cada usuário automaticamente
* Capacite seus usuários com segurança
* Operacionalize suas ideias

## <a name="step-1---connect-to-apps-easily"></a>Etapa 1-conectar-se a aplicativos facilmente

Conectando seus aplicativos com o Azure AD, você pode melhorar a produtividade e a segurança do usuário final habilitando o SSO (logon único) e o provisionamento do usuário. Ao gerenciar seus aplicativos em um único lugar, o Azure AD, você pode minimizar a sobrecarga administrativa e obter um único ponto de controle para suas políticas de segurança e conformidade.

Esta seção aborda as opções para gerenciar o acesso do usuário a aplicativos, permitindo acesso remoto seguro a aplicativos internos e os benefícios de migrar seus aplicativos para o Azure AD.

### <a name="make-apps-available-to-your-users-seamlessly"></a>Tornar os aplicativos disponíveis para os usuários diretamente

O Azure AD permite que os administradores [adicionem aplicativos](../manage-apps/add-application-portal.md) à galeria de aplicativos empresariais no [portal do Azure](https://portal.azure.com/). Adicionar aplicativos à galeria de aplicativos empresariais facilita a configuração de aplicativos para usar o Azure AD como seu provedor de identidade. Ele também permite que você gerencie o acesso do usuário ao aplicativo com políticas de acesso condicional e configure o logon único (SSO) para aplicativos para que os usuários não precisem inserir suas senhas repetidamente e sejam automaticamente conectados a aplicativos locais e baseados em nuvem.

Depois que os aplicativos são adicionados à galeria do Azure AD, os usuários podem ver os aplicativos atribuídos a eles e pesquisar e solicitar outros aplicativos, conforme necessário. O Azure AD fornece [vários métodos](../manage-apps/end-user-experiences.md) para que os usuários acessem seus aplicativos:

* Painel de acesso/meus aplicativos
* Inicializador de aplicativos do Microsoft 365
* Logon direto a aplicativos federados
* Links diretos logon

Para saber mais sobre o acesso do usuário aos aplicativos, consulte **etapa 3 – Capacite seus usuários** neste artigo.

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>Migrar aplicativos do Serviços de Federação do Active Directory (AD FS) para o Azure AD

Migrar a configuração de logon único do Serviços de Federação do Active Directory (AD FS) (ADFS) para o Azure AD permite recursos adicionais de segurança, uma capacidade de gerenciamento mais consistente e colaboração. Para obter resultados ideais, recomendamos que você migre seus aplicativos do AD FS para o Azure AD. Trazer a autenticação e a autorização de seu aplicativo para o Azure AD oferece os seguintes benefícios:

* Gerenciar custos
* Gerenciando riscos
* Aumentando a produtividade
* Lidando com a conformidade e a governança

Para saber mais, confira o White Paper [migrando seus aplicativos para Azure Active Directory](https://aka.ms/migrateapps/whitepaper) .

### <a name="enable-secure-remote-access-to-apps"></a>Habilitar acesso remoto seguro a aplicativos

O [Azure proxy de aplicativo do AD](../manage-apps/what-is-application-proxy.md) fornece uma solução simples para que as organizações publiquem aplicativos locais na nuvem para usuários remotos que precisam acessar aplicativos internos de maneira segura. Após um logon único no Azure AD, os usuários podem acessar aplicativos locais e na nuvem por meio de URLs externas ou um portal de aplicativo interno.

O Proxy de Aplicativo do AD do Azure oferece os seguintes benefícios:

* Extensão do AD do Azure para recursos locais
  * Proteção e segurança em escala de nuvem
  * Recursos como o acesso condicional e a autenticação multifator que são fáceis de habilitar
* Nenhum componente na rede de perímetro, como VPN e soluções de proxy reverso tradicionais
* Nenhuma conexão de entrada necessária
* SSO (logon único) entre dispositivos, recursos e aplicativos na nuvem e no local
* Capacita os usuários finais a serem produtivos a qualquer momento e em qualquer lugar

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>Descobrir sombra com ti com Microsoft Cloud App Security

Nas empresas modernas, os departamentos de TI geralmente não estão cientes de todos os aplicativos de nuvem que são usados pelos usuários enquanto realizam seus trabalhos. Quando os administradores de ti são solicitados a quantos aplicativos de nuvem eles acham que seus funcionários usam, em média, dizem 30 ou 40. Na realidade, a média é de mais de 1.000 aplicativos separados sendo usados por funcionários em sua organização. 80% dos funcionários usam aplicativos não aprovados que ninguém analisou e que podem não estar em conformidade com suas políticas de segurança e conformidade.

O [Microsoft Cloud app Security](/cloud-app-security/what-is-cloud-app-security) (MCAS) pode ajudá-lo a identificar aplicativos úteis que são populares com usuários que podem ser aprovados e adicionados à galeria de aplicativos empresariais para que os usuários se beneficiem de recursos como o SSO e o acesso condicional.

<em>" **Cloud app Security** nos ajuda a garantir que nossas pessoas estejam usando adequadamente nossos aplicativos de nuvem e SaaS, de maneiras que dão suporte às políticas de segurança fundamentais que ajudam a proteger a Accenture".</em> --- [John Blasi, diretor de gerenciamento, segurança de informações, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

Além de detectar a ti sombra, o MCAS também pode determinar o nível de risco dos aplicativos, impedir o acesso não autorizado a dados corporativos, possível perda de dados e outros riscos de segurança inerentes aos aplicativos.

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>Etapa 2 – estabelecer uma identidade para cada usuário automaticamente

Colocar diretórios locais e baseados em nuvem juntos em uma solução de identidade híbrida do Azure AD permitirá que você reutilize seu investimento local Active Directory existente ao provisionar suas identidades existentes na nuvem. A solução sincroniza identidades locais com o Azure AD, enquanto mantém o Active Directory local em execução com qualquer solução de governança existente como a principal fonte de verdade para identidades. A solução de identidade híbrida do Azure AD da Microsoft abrange recursos locais e baseados em nuvem, criando uma identidade de usuário comum para autenticação e autorização para todos os recursos, independentemente de sua localização.

A integração de seus diretórios locais ao Azure AD torna seus usuários mais produtivos e impede que os usuários usem várias contas entre aplicativos e serviços fornecendo uma identidade comum para o acesso a recursos de nuvem e locais. O uso de várias contas é um ponto problemático para os usuários finais e a ti. De uma perspectiva do usuário final, ter várias contas significa ter que se lembrar de várias senhas. Para evitar isso, muitos usuários reutilizam a mesma senha para cada conta, o que é inadequado de uma perspectiva de segurança. De uma perspectiva de ti, a reutilização geralmente leva a mais redefinições de senha e custos de assistência técnica junto com as reclamações do usuário final.

Azure AD Connect é a ferramenta usada para sincronizar suas identidades locais com o Azure AD, que pode ser usada para acessar aplicativos de nuvem. Depois que as identidades estiverem no Azure AD, elas poderão ser provisionadas para aplicativos SaaS como Salesforce ou concur.

Nesta seção, listamos as recomendações para fornecer alta disponibilidade, autenticação moderna para a nuvem e reduzir seu espaço local.

> [!NOTE]
> Se você quiser saber mais sobre Azure AD Connect, confira [o que é Azure ad Connect sincronização?](./how-to-connect-sync-whatis.md)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Configurar um servidor de preparo para Azure AD Connect e mantê-lo atualizado

Azure AD Connect desempenha um papel fundamental no processo de provisionamento. Se o servidor de sincronização ficar offline por algum motivo, as alterações no local não serão atualizadas na nuvem e causarão problemas de acesso aos usuários. É importante definir uma estratégia de failover que permita que os administradores retomem a sincronização rapidamente depois que o servidor de sincronização ficar offline.

Para fornecer alta disponibilidade no caso de seu servidor de Azure AD Connect primário ficar offline, é recomendável que você implante um [servidor de preparo](./how-to-connect-sync-staging-server.md) separado para Azure ad Connect. A implantação de um servidor permite que o administrador "promova" o servidor de preparo para a produção por meio de uma opção de configuração simples. Ter um servidor em espera configurado no modo de preparo também permite que você teste e implante novas alterações de configuração e introduza um novo servidor se estiver encerrando o antigo.

> [!TIP]
> O Azure AD Connect é atualizado regularmente. Portanto, é altamente recomendável que você mantenha o servidor de preparo atualizado para aproveitar as melhorias de desempenho, correções de bugs e novos recursos fornecidos por cada nova versão.

### <a name="enable-cloud-authentication"></a>Habilitar autenticação na nuvem

As organizações com Active Directory local devem estender seu diretório para o AD do Azure usando Azure AD Connect e configurar o método de autenticação apropriado. [Escolher o método de autenticação correto](./choose-ad-authn.md) para sua organização é a primeira etapa em sua jornada de mover aplicativos para a nuvem. Trata-se de um componente crítico, pois controla o acesso a todos os dados e recursos da nuvem.

O método mais simples e recomendado para habilitar a autenticação de nuvem para objetos de diretório locais no Azure AD é habilitar a PHS ( [sincronização de hash de senha](./how-to-connect-password-hash-synchronization.md) ). Como alternativa, algumas organizações podem considerar a habilitação da [autenticação de passagem](./how-to-connect-pta-quick-start.md) (PTA).

Independentemente de você escolher PHS ou PTA, não se esqueça de habilitar o [logon único contínuo](./how-to-connect-sso.md) para permitir que os usuários acessem os aplicativos de nuvem sem inserir constantemente seu nome de usuário e senha no aplicativo ao usar o Windows 7 e 8 dispositivos em sua rede corporativa. Sem o logon único, os usuários devem se lembrar de senhas específicas do aplicativo e entrar em cada aplicativo. Da mesma forma, a equipe de ti precisa criar e atualizar contas de usuário para cada aplicativo, como Microsoft 365, Box e Salesforce. Os usuários precisam se lembrar de suas senhas, além de gastar tempo para entrar em cada aplicativo. Fornecer um mecanismo de logon único padronizado para toda a empresa é crucial para melhor experiência do usuário, redução de risco, capacidade de relatório e governança.

Para as organizações que já usam AD FS ou outro provedor de autenticação local, mudar para o Azure AD como seu provedor de identidade pode reduzir a complexidade e melhorar a disponibilidade. A menos que você tenha casos de uso específicos para usar a Federação, é recomendável migrar da autenticação federada para o PHS e o SSO contínuo ou PTA e o SSO contínuo para aproveitar os benefícios de um espaço local reduzido e a flexibilidade oferecida pela nuvem com experiências de usuário aprimoradas. Para obter mais informações, consulte [migrar da Federação para a sincronização de hash de senha para Azure Active Directory](./plan-migrate-adfs-password-hash-sync.md).

### <a name="enable-automatic-deprovisioning-of-accounts"></a>Habilitar o desprovisionamento automático de contas

A habilitação do provisionamento e desprovisionamento automatizados para seus aplicativos é a melhor estratégia para governar o ciclo de vida de identidades em vários sistemas. O Azure AD dá suporte ao [provisionamento automatizado e baseado em políticas e ao desprovisionamento](../app-provisioning/configure-automatic-user-provisioning-portal.md) de contas de usuário para uma variedade de aplicativos SaaS populares, como ServiceNow e Salesforce, e outros que implementam o [protocolo scim 2,0](../app-provisioning/use-scim-to-provision-users-and-groups.md). Ao contrário das soluções de provisionamento tradicionais, que exigem código personalizado ou carregamento manual de arquivos CSV, o serviço de provisionamento é hospedado na nuvem e apresenta conectores previamente integrados que podem ser configurados e gerenciados usando o portal do Azure. Um dos principais benefícios do desprovisionamento automático é que ele ajuda a proteger sua organização removendo instantaneamente as identidades dos usuários dos principais aplicativos SaaS quando eles deixam a organização.

Para saber mais sobre o provisionamento de contas de usuário automático e como ele funciona, confira [Automatizar o provisionamento e o desprovisionamento de usuário para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="step-3---empower-your-users-securely"></a>Etapa 3: Capacite seus usuários com segurança

No local de trabalho digital de hoje, é importante balancear a segurança com a produtividade. No entanto, os usuários finais geralmente reforçam as medidas de segurança que reduzem a produtividade e o acesso aos aplicativos na nuvem. Para ajudar a resolver isso, o Azure AD fornece recursos de autoatendimento que permitem que os usuários permaneçam produtivos e, ao mesmo tempo, minimizam a sobrecarga administrativa.

Esta seção lista as recomendações para remover o conflito de sua organização, capacitando seus usuários enquanto você ainda está atento.

### <a name="enable-self-service-password-reset-for-all-users"></a>Habilitar Self-Service redefinição de senha para todos os usuários

A SSPR ( [redefinição de senha de autoatendimento](../authentication/tutorial-enable-sspr.md) ) do Azure oferece um meio simples para os administradores de ti permitirem que os usuários redefinam e desbloqueiem suas senhas ou contas sem intervenção do administrador. O sistema inclui relatórios detalhados que acompanham quando os usuários acessam o sistema junto com as notificações para alertá-lo para uso indevido ou um abuso.

Por padrão, o Azure Active Directory desbloqueia contas quando ele executa uma redefinição de senha. No entanto, ao habilitar [a integração de Azure ad Connect local](../authentication/concept-sspr-howitworks.md#on-premises-integration), você também tem a opção de separar essas duas operações, o que permite que os usuários desbloqueiem sua conta sem precisar redefinir a senha.

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>Verifique se todos os usuários estão registrados para MFA e SSPR

O Azure fornece relatórios que podem ser usados por você e sua organização para garantir que os usuários sejam registrados para MFA e SSPR. Os usuários que não tiverem registrado talvez precisem ser instruídos no processo.

O [relatório de entradas](../authentication/howto-mfa-reporting.md) de MFA inclui informações sobre o uso de MFA e fornece uma visão sobre como a MFA está funcionando em sua organização. Ter acesso à atividade de entrada (e auditorias e detecções de riscos) para o Azure AD é crucial para solução de problemas, análise de uso e investigações forenses.

Da mesma forma, o [relatório de gerenciamento de senhas de autoatendimento](../authentication/howto-sspr-reporting.md) pode ser usado para determinar quem tem (ou não) registrado para SSPR.

### <a name="self-service-app-management"></a>Gerenciamento de aplicativo de autoatendimento

Antes que os usuários possam detectar aplicativos automaticamente no painel de acesso, você precisa habilitar o [acesso de aplicativo de autoatendimento](../manage-apps/access-panel-manage-self-service-access.md) a todos os aplicativos que você deseja permitir que os usuários autodescubram e solicitem acesso ao. O acesso ao aplicativo de autoatendimento é uma ótima maneira de permitir que os usuários autodescubram aplicativos e, opcionalmente, permitem que o grupo de negócios aprove o acesso a esses aplicativos. Você pode permitir que o grupo de negócios gerencie as credenciais atribuídas a esses usuários para a [senha Single-Sign nos aplicativos](../manage-apps/troubleshoot-password-based-sso.md#automatically-capture-sign-in-fields-for-an-app) diretamente dos respectivos painéis de acesso.

### <a name="self-service-group-management"></a>Gerenciamento de grupo de autoatendimento

A atribuição de usuários a aplicativos é melhor mapeada ao usar grupos, pois eles permitem uma grande flexibilidade e capacidade de gerenciar em escala:

* Baseado em atributo usando a associação dinâmica de grupo
* Delegação aos proprietários do aplicativo

O Azure AD fornece a capacidade de gerenciar o acesso a recursos usando grupos de segurança e grupos de Microsoft 365. Esses grupos podem ser gerenciados por um proprietário de grupo que pode aprovar ou negar solicitações de associação e delegar o controle de associação de grupo. Conhecido como [Gerenciamento de grupo de autoatendimento](../enterprise-users/groups-self-service-management.md), esse recurso poupa tempo, permitindo que os proprietários de grupos que não são atribuídos a uma função administrativa criem e gerenciem grupos sem precisar contar com os administradores para lidar com suas solicitações.

## <a name="step-4---operationalize-your-insights"></a>Etapa 4-colocar suas ideias em operação

A auditoria e o registro em log de eventos relacionados à segurança e alertas relacionados são componentes essenciais de uma estratégia eficiente para garantir que os usuários permaneçam produtivos e sua organização seja segura. Os logs e relatórios de segurança podem ajudar a responder a perguntas como:

* Você está usando o que está pagando?
* Há alguma coisa suspeita ou mal-intencionada acontecendo em meu locatário?
* Quem foi impactado durante um incidente de segurança?

Os logs e os relatórios de segurança fornecem um registro eletrônico das atividades suspeitas e ajudam a detectar padrões que podem indicar uma tentativa de penetração externa ou uma penetração externa com êxito da rede, bem como ataques internos. Você pode usar a auditoria para monitorar as atividades do usuário, a conformidade regulatória de documentos, realizar análise forense e muito mais. Os alertas fornecem notificações de eventos de segurança.

### <a name="assign-least-privileged-admin-roles-for-operations"></a>Atribuir funções de administrador com privilégios mínimos para operações

Como você imagina sobre sua abordagem às operações, há alguns níveis de administração a serem considerados. O primeiro nível coloca a carga da administração no (s) administrador (es) global (is). Sempre usando a função de administrador global, pode ser apropriado para empresas menores. Mas, para organizações maiores com equipe de suporte técnico e administradores responsáveis por tarefas específicas, a atribuição da função de administrador global pode ser um risco de segurança, uma vez que ela fornece aos indivíduos a capacidade de gerenciar tarefas que estão acima e além do que elas devem ser capazes de fazer.

Nesse caso, você deve considerar o próximo nível de administração. Usando o Azure AD, você pode designar usuários finais como "administradores limitados" que podem gerenciar tarefas em funções com menos privilégios. Por exemplo, você pode atribuir a equipe de suporte técnico a função de [leitor de segurança](../roles/permissions-reference.md#security-reader) para fornecer a capacidade de gerenciar recursos relacionados à segurança com acesso somente leitura. Ou talvez faça sentido atribuir a função de [administrador de autenticação](../roles/permissions-reference.md#authentication-administrator) a indivíduos para dar a eles a capacidade de redefinir credenciais de não senha ou ler e configurar a integridade do serviço do Azure.

Para saber mais, confira [permissões de função de administrador no Azure Active Directory](../roles/permissions-reference.md).

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>Monitorar componentes híbridos (Azure AD Connect sincronização AD FS) usando Azure AD Connect Health

Azure AD Connect e AD FS são componentes críticos que podem interromper o gerenciamento do ciclo de vida e a autenticação e, finalmente, levar a interrupções. Portanto, você deve implantar Azure AD Connect Health para monitoramento e relatórios desses componentes.

Para saber mais, acesse [monitorar AD FS usando Azure ad Connect Health](./how-to-connect-health-adfs.md).

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Usar Azure Monitor para coletar logs de dados para análise

O [Azure monitor](../../azure-monitor/overview.md) é um portal de monitoramento unificado para todos os logs do Azure AD, que fornece informações aprofundadas, análises avançadas e aprendizado inteligente de máquina. Com o Azure Monitor, você pode consumir logs e métricas no portal e via APIs para obter mais visibilidade sobre o estado e o desempenho de seus recursos. Ele permite um único painel de experiência de vidro no portal, permitindo, ao mesmo tempo, uma ampla gama de integrações de produtos por meio de APIs e opções de exportação de dados que dão suporte a sistemas SIEM de terceiros tradicionais. Azure Monitor também oferece a capacidade de configurar regras de alerta para ser notificado ou tomar ações automatizadas sobre problemas que afetam seus recursos.

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>Crie painéis personalizados para sua liderança e seu dia a dia

As organizações que não têm uma solução SIEM podem baixar o [pacote de conteúdo do Power bi](../reports-monitoring/howto-use-azure-monitor-workbooks.md) para o Azure AD. O pacote de conteúdo do Power BI contém relatórios pré-criados para ajudá-lo a entender como os usuários adotam e usam os recursos do Azure AD, o que permite obter informações sobre todas as atividades em seu diretório. Você também pode criar seu próprio [painel personalizado](/power-bi/service-dashboards) e compartilhá-lo com sua equipe de liderança para relatar as atividades do dia a dia. Os painéis são uma ótima maneira de monitorar seus negócios e ver todas as métricas mais importantes em um relance. As visualizações em um dashboard podem vir de um conjunto de dados subjacente, ou de muitos, e de um relatório subjacente, ou de muitos. Um dashboard combina dados locais e na nuvem, proporcionando uma exibição consolidada independentemente do local em que os dados residem.

![Power BI painel personalizado](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>Entenda seus drivers de chamada de suporte

Ao implementar uma solução de identidade híbrida conforme descrito neste artigo, você deve observar uma redução em suas chamadas de suporte. Problemas comuns, como senhas esquecidas e bloqueios de conta, são mitigados pela implementação da redefinição de senha de autoatendimento do Azure, enquanto a habilitação do acesso ao aplicativo de autoatendimento permite que os usuários autodescubram e solicitem acesso a aplicativos sem depender de sua equipe de ti.

Se você não observar uma redução nas chamadas de suporte, recomendamos que você analise seus drivers de chamada de suporte em uma tentativa de confirmar se o acesso ao aplicativo SSPR ou de autoatendimento foi configurado corretamente ou se há outros novos problemas que podem ser tratados sistematicamente.

*"Em nossa jornada de transformação digital, precisávamos de um provedor de gerenciamento de identidades e acesso confiável para facilitar a integração direta e segura entre nós, parceiros e provedores de serviços de nuvem, para um ecossistema eficaz; O Azure AD foi a melhor opção nos oferecendo os recursos necessários e a visibilidade que nos permitia detectar e responder a riscos. "* --- [Yazan Almasri, diretor global de segurança de informações, ARAMEX](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>Monitorar o uso de aplicativos para orientar o insights

Além de descobrir a ti de sombra, monitorar o uso do aplicativo em sua organização usando [Microsoft Cloud app Security](/cloud-app-security/what-is-cloud-app-security) pode ajudar sua organização à medida que você se move para aproveitar ao máximo a promessa de aplicativos em nuvem. Ele pode ajudar a manter você no controle de seus ativos por meio de visibilidade aprimorada da atividade e aumentar a proteção de dados críticos em aplicativos de nuvem. Monitorar o uso do aplicativo em sua organização usando o MCAS pode ajudá-lo a responder às seguintes perguntas:

* Quais aplicativos não aprovados são funcionários usando para armazenar dados?
* Onde e quando os dados confidenciais são armazenados na nuvem?
* Quem está acessando dados confidenciais na nuvem?

*"Com Cloud App Security, podemos identificar rapidamente anomalias e tomar medidas."* --- [Eric LePenske, gerente sênior, segurança da informação, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>Resumo

Há muitos aspectos para implementar uma solução de identidade híbrida, mas esta lista de verificação em quatro etapas ajudará você a realizar rapidamente uma infraestrutura de identidade que permitirá que os usuários sejam mais produtivos e seguros.

* Conectar-se a aplicativos facilmente
* Estabelecer uma identidade para cada usuário automaticamente
* Capacite seus usuários com segurança
* Operacionalize suas ideias

Esperamos que este documento seja um roteiro útil para estabelecer uma base de identidade forte para sua organização.

## <a name="identity-checklist"></a>Lista de verificação de identidade

Recomendamos que você imprima a seguinte lista de verificação para referência ao começar sua jornada para uma base de identidade mais sólida em sua organização.

### <a name="today"></a>Hoje

|Pronto?|Item|
|:-|:-|
||SSPR (redefinição de senha de autoatendimento) do piloto para um grupo|
||Monitorar componentes híbridos usando o Azure AD Connect Health|
||Atribuir funções de administrador com privilégios mínimos para a operação|
||Descobrir sombra com ti com Microsoft Cloud App Security|
||Usar Azure Monitor para coletar logs de dados para análise|

### <a name="next-two-weeks"></a>Próximas duas semanas

|Pronto?|Item|
|:-|:-|
||Disponibilizar um aplicativo para seus usuários|
||Provisionamento piloto do Azure AD para um aplicativo SaaS de sua escolha|
||Configurar um servidor de preparo para Azure AD Connect e mantê-lo atualizado|
||Iniciar a migração de aplicativos do ADFS para o Azure AD|
||Crie painéis personalizados para sua liderança e seu dia a dia|

### <a name="next-month"></a>Próximo mês

|Pronto?|Item|
|:-|:-|
||Monitorar o uso de aplicativos para orientar o insights|
||Acesso remoto seguro ao piloto para aplicativos|
||Verifique se todos os usuários estão registrados para MFA e SSPR|
||Habilitar autenticação na nuvem|

### <a name="next-three-months"></a>Próximos três meses

|Pronto?|Item|
|:-|:-|
||Habilitar o gerenciamento de aplicativo de autoatendimento|
||Habilitar o gerenciamento de grupo de autoatendimento|
||Monitorar o uso de aplicativos para orientar o insights|
||Entenda seus drivers de chamada de suporte|

## <a name="next-steps"></a>Próximas etapas

Saiba como você pode aumentar sua postura segura usando os recursos de Azure Active Directory e esta lista de verificação de cinco [etapas – cinco passos para proteger sua infraestrutura de identidade](../../security/fundamentals/steps-secure-identity.md).

Saiba como os recursos de identidade no Azure AD podem ajudá-lo a acelerar sua transição para o gerenciamento regido na nuvem, fornecendo as soluções e os recursos que permitem às organizações adotar e mover rapidamente mais de seu gerenciamento de identidades de sistemas locais tradicionais para o AD do Azure- [como o Azure ad fornece gerenciamento regido de nuvem para cargas de trabalho locais](./cloud-governed-management-for-on-premises.md).