---
title: Quatro etapas para uma base de identidade forte com o Azure Active Directory
description: Este tópico descreve as quatro etapas que os clientes de identidade híbrida podem adotar para criar uma base de identidade forte.
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
ms.openlocfilehash: a20a1a1009949aa2e6de8586040e918ae15c8d39
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655938"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>Quatro etapas para uma base de identidade forte com o Azure Active Directory

Gerenciando o acesso a aplicativos e dados não pode contar com as estratégias de limite de segurança de rede tradicionais, como firewalls e redes de perímetro por causa a movimentação rápida de aplicativos para a nuvem. Agora as organizações devem confiar que sua solução de identidade para controlar quem e o que tem acesso a dados e aplicativos da organização. Mais organizações estão permitindo que os funcionários trazem seus próprios dispositivos para trabalhar e usar seus dispositivos de qualquer lugar que eles possam se conectar à Internet. Garantir que esses dispositivos estejam em conformidade e seguro se tornou uma consideração importante em uma organização opta por implementar a solução de identidade. No espaço de trabalho digital de hoje, [identidade é o plano de controle principal](https://www.microsoft.com/security/technology/identity-access-management?rtc=1) de qualquer organização que mudar para a nuvem.

Em adotar uma solução de identidade híbrida do Azure Active Directory (Azure AD), as organizações obtém acesso a recursos premium que desbloqueie a produtividade por meio de automação, delegação, autoatendimento, únicos e recursos de logon. Ele permite que seus funcionários acessem recursos da empresa de onde quer que eles precisam para realizar seu trabalho, permitindo que sua equipe de TI determinar que o acesso, garantindo que as pessoas certas tenham o acesso correto para os recursos certos para estabelecer a produtividade segura.

Com base em nossas lições aprendidas, esta lista de verificação de práticas recomendadas ajudará você a implantar rapidamente as ações recomendadas para criar uma *forte* foundation de identidade em sua organização:

* Conectar-se facilmente aos aplicativos
* Estabelecer uma identidade para todos os usuários automaticamente
* Capacite seus usuários com segurança
* Colocar suas ideias

## <a name="step-1---connect-to-apps-easily"></a>Etapa 1: conectar-se facilmente aos aplicativos

Ao conectar seus aplicativos com o Azure AD, você pode melhorar a produtividade do usuário final e a segurança ao habilitar o logon único (SSO) e fazer o provisionamento do usuário. Gerenciando seus aplicativos em um único lugar, o Azure AD, você pode minimizar a sobrecarga administrativa e atingir um ponto único de controle para as políticas de segurança e conformidade.

Esta seção aborda as opções para gerenciar o acesso de usuário aos aplicativos, habilitando o acesso remoto seguro a aplicativos internos e os benefícios de migrar seus aplicativos para o Azure AD.

### <a name="make-apps-available-to-your-users-seamlessly"></a>Disponibilizar aplicativos para seus usuários diretamente

Azure AD permite que os administradores [adicionar aplicativos](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) na Galeria de aplicativos empresariais na [portal do Azure](https://portal.azure.com/). Adicionar aplicativos na Galeria de aplicativos empresariais torna mais fácil para você configurar aplicativos para usar o Azure AD como provedor de identidade. Ele também permite gerenciar o acesso de usuário para o aplicativo com políticas de acesso condicional e configurar o logon único (SSO) para aplicativos para que os usuários não precisam digitar suas senhas repetidamente e são assinados automaticamente em ambos os locais e aplicativos baseados em nuvem.

Quando aplicativos são adicionados à Galeria do Azure AD, os usuários poderão ver os aplicativos que são atribuídos a eles e procuram e solicitarem outros aplicativos conforme necessário. O Azure AD fornece [vários métodos](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences) para os usuários acessarem seus aplicativos:

* Painel de acesso / meus aplicativos
* Iniciador de aplicativos do Office 365
* Logon direto a aplicativos federados
* Links diretos logon

Para saber mais sobre o acesso de usuário aos aplicativos, consulte **etapa 3--Capacite seus usuários** neste artigo.

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>Migrar aplicativos dos serviços de Federação do Active Directory para o Azure AD

Migração única configuração de logon do Active Directory Federation Services (ADFS) para o Azure AD permite que recursos adicionais de segurança, capacidade de gerenciamento mais consistente e colaboração. Para obter melhores resultados, recomendamos que você migre seus aplicativos do AD FS para o Azure AD. Trazer seu aplicativo autenticação e autorização do Azure AD oferece os seguintes benefícios:

* Gerenciar custos
* Gerenciamento de riscos
* Aumentando a produtividade
* Governança e conformidade de endereçamento

Para obter mais informações, consulte o [migrando seus aplicativos ao Azure Active Directory](https://aka.ms/migrateapps/whitepaper) white paper.

### <a name="enable-secure-remote-access-to-apps"></a>Habilitar acesso remoto seguro a aplicativos

[O Proxy de aplicativo do Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) fornece uma solução simples para as organizações publicar aplicativos no local para a nuvem para usuários remotos que precisam de acesso a aplicativos internos de forma segura. Após um logon único para o Azure AD, os usuários podem acessar a aplicativos locais e de nuvem por meio de URLs externas ou um portal interno do aplicativo.

O Proxy de aplicativo do Azure AD oferece os seguintes benefícios:

* Extensão do AD do Azure para recursos locais
  * Proteção e segurança em escala de nuvem
  * Recursos como acesso condicional e autenticação multifator que são fáceis de habilitar
* Nenhum componente na rede de perímetro, como VPN e as soluções tradicionais de proxy reverso
* Nenhuma conexão de entrada necessária
* Logon único (SSO) em dispositivos, recursos e aplicativos na nuvem e locais
* Capacita os usuários finais sejam produtivos a qualquer momento e em qualquer lugar

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>Descobrir TI sombra com o Microsoft Cloud App Security

Nas empresas modernas, os departamentos de TI geralmente não estão cientes de todos os aplicativos de nuvem que são usados pelos usuários para fazer seu trabalho. Quando os administradores de TI são solicitados a nuvem quantos aplicativos eles acham que seus funcionários usam, em média, eles digamos, 30 ou 40. Na realidade, a média é de mais de 1.000 aplicativos separados que estão sendo usados pelos funcionários da sua organização. 80% dos funcionários usam aplicativos não sancionados que ninguém tenha revisado e não pode estar em conformidade com as políticas de segurança e conformidade.

[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) pode ajudá-lo a identificar aplicativos úteis que são comuns com usuários que ele pode sancionar e adicionar à Galeria de aplicativos do Enterprise para que os usuários se beneficiam de recursos, como SSO e acesso condicional.

*"* * Cloud App Security** nos ajuda a garantir que as pessoas estão corretamente usando nossos aplicativos de nuvem e SaaS, de maneiras que dão suporte as políticas de segurança fundamentais que ajudam a proteger a Accenture." *--- [John Blasi, gerenciando Accenture diretor, segurança de informações](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

Além de detectar TI sombra, MCAS também pode determinar o nível de risco de aplicativos, impedir acesso não autorizado a dados corporativos, perda de dados e outros riscos de segurança inerentes nos aplicativos.

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>Etapa 2 - estabelecer uma identidade para todos os usuários automaticamente

Integração de diretórios com base em nuvem e locais em uma solução de identidade híbrida do Azure AD permitirá que você reutilize o investimento existente do Active Directory local por meio do provisionamento de suas identidades existentes na nuvem. A solução sincroniza identidades locais com o AD do Azure, enquanto mantém IT o Active Directory local em execução com qualquer solução de governança existente como a principal fonte de verdade para identidades. Solução de identidade do Microsoft Azure AD híbrido abrange locais e recursos baseados em nuvem, criando uma identidade de usuário comum para autenticação e autorização para todos os recursos, independentemente de sua localização.

Integrar seus diretórios locais ao Azure AD torna os usuários mais produtivos e impede que os usuários usando várias contas em aplicativos e serviços, fornecendo uma identidade comum para acessar a nuvem e recursos locais. Usar várias contas é um ponto problemático para os usuários finais e IT semelhante. Da perspectiva do usuário final, tendo vários meios de contas que precise se lembrar de várias senhas. Para evitar isso, muitos usuários reutilizam a mesma senha para cada conta, o que é ruim de uma perspectiva de segurança. Da perspectiva de TI, reutilização geralmente leva a mais redefinições de senha e os custos de assistência técnica, juntamente com as reclamações de usuários finais.

O Azure AD Connect é a ferramenta que é usada para sincronizar suas identidades locais ao Azure AD, que pode ser usado para acessar aplicativos na nuvem. Depois que as identidades estão no Azure AD, eles podem provisionar para aplicativos SaaS, como o Salesforce ou o Concur.

Nesta seção, listamos as recomendações para fornecer alta disponibilidade, a autenticação moderna para a nuvem e a redução da pegada de local.

> [!NOTE]
> Se você quiser saber mais sobre o Azure AD Connect, consulte [o que é o Azure AD Connect Sync?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Configurar um servidor de preparo para o Azure AD Connect e mantê-lo atualizado

O Azure AD Connect desempenha um papel fundamental no processo de provisionamento. Se o servidor de sincronização ficar offline por algum motivo, as alterações para o local não serão atualizadas na nuvem e causam problemas de acesso aos usuários. É importante definir uma estratégia de failover que permite que os administradores retomar a sincronização rapidamente depois que o servidor de sincronização fica offline.

Para fornecer alta disponibilidade no evento do Azure primário AD Connect servidor ficar offline, é recomendável que você implante um separado [servidor de preparo](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-staging-server) para o Azure AD Connect. Implantando um servidor permite que o administrador "elevar" o servidor de preparo para produção por uma opção de configuração simples. Ter um servidor em espera configurado no modo de preparo também permite que você testar e implantar novas alterações de configuração e introduzir um novo servidor, se o encerramento do antigo.

> [!TIP]
> O Azure AD Connect é atualizado regularmente. Portanto, é altamente recomendável que você mantenha o servidor de preparo atual para aproveitar as melhorias de desempenho, correções de bugs e novos recursos que cada nova versão fornece.

### <a name="enable-cloud-authentication"></a>Habilitar a autenticação de nuvem

As organizações com o Active Directory do local devem estender seu diretório AD do Azure usando o Azure AD Connect e configurar o método de autenticação apropriado. [Escolhendo o método de autenticação correto](https://docs.microsoft.com/azure/security/azure-ad-choose-authn) para sua organização é a primeira etapa na sua jornada de migração de aplicativos para a nuvem. Ele é um componente crítico, pois ele controla o acesso a todos os dados de nuvem e recursos.

O método mais simples e recomendado para habilitar a autenticação de nuvem no local para objetos de diretório do AD do Azure é habilitar [sincronização de Hash de senha](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) (PHS). Como alternativa, algumas organizações podem considerar a habilitação [autenticação de passagem](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA).

Se você escolher PHS ou PTA, não se esqueça de habilitar [logon único contínuo](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) para permitir que os usuários acessem aplicativos na nuvem sem constantemente inserindo seu nome de usuário e senha no aplicativo ao usar o Windows 7 e 8 dispositivos em sua empresa rede. Sem logon único, os usuários devem lembrar senhas específicas de aplicativo e entrar em cada aplicativo. Da mesma forma, a equipe de TI precisa para criar e atualizar contas de usuário para cada aplicativo, como o Office 365, Box e Salesforce. Os usuários precisam se lembrar de suas senhas, além de gastar tempo para entrar em cada aplicativo. Fornecendo um padronizado único logon mecanismo para toda a empresa é crucial para melhor experiência do usuário, redução de risco, capacidade de relatar e governança.

Para as organizações que já usam o AD FS ou outro provedor de autenticação local, o movendo para o Azure AD como provedor de identidade pode reduzir a complexidade e aumentar a disponibilidade. A menos que você tenha casos de uso específicos para usar a federação, é recomendável migrar da autenticação federada PHS e SSO contínuo ou PTA e SSO contínuo para aproveitar os benefícios de uma superfície reduzida no local e a flexibilidade que a nuvem oferece com experiências de usuário aprimorada. Para obter mais informações, consulte [migrar da federação para sincronização de hash de senha para o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/plan-migrate-adfs-password-hash-sync).

### <a name="enable-automatic-deprovisioning-of-accounts"></a>Habilitar desprovisionamento automáticos de contas

Habilitar o provisionamento e desprovisionamento de usuários para seus aplicativos automatizado é a melhor estratégia para controlar o ciclo de vida de identidades em vários sistemas. Azure AD dá suporte a [automatizado, baseado em políticas de provisionamento e desprovisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) de contas de usuário a uma variedade de aplicativos SaaS populares, como ServiceNow e o Salesforce e outras pessoas que implementam o [SCIM 2.0 protocolo](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups). Diferentemente das soluções tradicionais de provisionamento, que exige código personalizado ou o carregamento manual de arquivos CSV, o serviço de provisionamento está hospedado na nuvem e recursos pré-integrados conectores que podem ser configurados e gerenciados usando o portal do Azure. Dos principais benefícios de desprovisionamento automático é que ele ajuda a proteger sua organização removendo instantaneamente as identidades de usuários dos principais aplicativos SaaS quando eles saírem da organização.

Para saber mais sobre o provisionamento de contas de usuário automático e como ele funciona, confira [Automatizar o provisionamento e o desprovisionamento de usuário para aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

## <a name="step-3---empower-your-users-securely"></a>Etapa 3 - Capacite seus usuários com segurança

No espaço de trabalho digital de hoje, é importante equilibrar a segurança com a produtividade. No entanto, os usuários finais frequentemente recuado em medidas de segurança que reduzir sua produtividade e o acesso a aplicativos de nuvem. Para ajudar a lidar com isso, o Azure AD fornece recursos de autoatendimento que permitem aos usuários a permanecerem produtivos enquanto minimiza a sobrecarga administrativa.

Esta seção lista as recomendações para remover o atrito de sua organização, capacitando seus usuários permanecendo vigilante.

### <a name="enable-self-service-password-reset-for-all-users"></a>Habilitar o autoatendimento de redefinição de senha para todos os usuários

Do Azure [redefinição de senha de autoatendimento](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) (SSPR) oferece um meio simples para os administradores de TI permitir que os usuários redefinam e desbloquear suas senhas ou contas sem intervenção do administrador. O sistema inclui relatórios detalhados que acompanham quando os usuários acessam o sistema junto com as notificações para alertá-lo para uso indevido ou um abuso.

Por padrão, o Azure Active Directory desbloqueia contas quando ele executa uma redefinição de senha. No entanto, quando você habilita o Azure AD Connect [integração local](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#on-premises-integration), você também tem a opção de separar essas duas operações, que permitem aos usuários desbloquear sua conta sem precisar redefinir a senha.

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>Verifique se que todos os usuários estão registrados para MFA e o SSPR

O Azure fornece relatórios que podem ser usados por você e sua organização para garantir que os usuários são registrados para MFA e o SSPR. Os usuários que ainda não registraram talvez precise ser instruídos sobre o processo.

A MFA [relatório de entradas](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting) inclui informações sobre o uso MFA e fornece informações sobre como a MFA está funcionando em sua organização. Ter acesso ao entrar atividade (e auditorias e eventos de risco) para o Azure AD é essencial para a solução de análise de uso e investigações legais.

Da mesma forma, o [relatório de gerenciamento de senha de autoatendimento](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting) pode ser usado para determinar quem tem (ou não) registrado no SSPR.

### <a name="self-service-app-management"></a>Gerenciamento de aplicativo de autoatendimento

Antes que os usuários podem descobrir por conta própria aplicativos de seu painel de acesso, você precisa habilitar [acesso a aplicativos de autoatendimento](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-manage-self-service-access) a todos os aplicativos que você deseja permitir que os usuários a descobrir por conta própria e solicitar acesso. Acesso de aplicativo de autoatendimento é uma ótima maneira de permitir que os usuários a descobrir por conta própria aplicativos e, opcionalmente, permitir que o grupo de negócios aprovar o acesso a esses aplicativos. Você pode permitir que o grupo de negócios gerenciar as credenciais atribuídas a esses usuários para [senha de logon único em aplicativos](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery#configure-password-sso-for-an-azure-ad-gallery-app) diretamente de seus painéis de acesso.

### <a name="self-service-group-management"></a>Gerenciamento de grupo de autoatendimento

Atribuindo usuários a aplicativos é melhor mapeado ao usar grupos, pois permitem que grande flexibilidade e capacidade de gerenciar em escala:

* Usando a associação dinâmica de grupo com base em atributo
* Delegação para proprietários de aplicativo

O Azure AD fornece a capacidade de gerenciar o acesso aos recursos usando grupos de segurança e Grupos do Office 365. Esses grupos podem ser gerenciados por um proprietário de grupo que pode aprovar ou negar solicitações de associação e delegar o controle de associação de grupo. Conhecido como [gerenciamento de grupo de autoatendimento](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management), esse recurso poupa tempo, permitindo que os proprietários de grupo que não são atribuídos a uma função administrativa para criar e gerenciar grupos sem a necessidade de contar com os administradores para lidar com suas solicitações.

## <a name="step-4---operationalize-your-insights"></a>Etapa 4: colocar suas ideias

Auditoria e log de eventos relacionados à segurança e alertas relacionados são componentes essenciais de uma estratégia eficiente para garantir que os usuários permanecem produtivos e sua organização é segura. Os logs de segurança e relatórios podem ajudar a responder pergunta, como:

* Você está usando está pagando?
* Há algo suspeito ou mal-intencionado ocorrendo em meu locatário?
* Quem foi afetado durante um incidente de segurança?

Os logs e os relatórios de segurança fornecem um registro eletrônico das atividades suspeitas e ajudam a detectar padrões que podem indicar uma tentativa de penetração externa ou uma penetração externa com êxito da rede, bem como ataques internos. Você pode usar a auditoria para monitorar a atividade de usuário, conformidade regulatória de documentos, fazer análise forense e muito mais. Os alertas fornecem notificações de eventos de segurança.

### <a name="assign-least-privileged-admin-roles-for-operations"></a>Atribuir funções de administrador com privilégios mínimos para operações

Pensar sobre sua abordagem de operações, há dois níveis de administração a considerar. O primeiro nível coloca o peso de administração no seu administrador global (es). Sempre usar a função de administrador global, pode ser apropriada para empresas menores. Mas para organizações maiores com a equipe de suporte técnico e administradores responsáveis por tarefas específicas, atribuir a função de administrador global pode ser um risco à segurança, pois ele fornece às pessoas com a capacidade de gerenciar as tarefas que estão acima e além o que eles devem ser capazes de fazer.

Nesse caso, você deve considerar o próximo nível de administração. Usando o Azure AD, você pode designar os usuários finais como "administradores limitados", que podem gerenciar tarefas nas funções com menos privilégios. Por exemplo, você pode atribuir a ajuda a equipe de suporte técnico a [leitor de segurança](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#security-reader) função fornecê-las com a capacidade de gerenciar recursos de segurança com acesso somente leitura. Ou talvez, faz sentido atribuir a [administrador da autenticação](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#authentication-administrator) função aos indivíduos para lhes conceder a capacidade de redefinir credenciais diferente de senha ou ler e configurar o Azure Service Health.

Para obter mais informações, consulte [permissões de função de administrador no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>Monitorar componentes híbrido (Azure AD Connect sync, o AD FS) usando o Azure AD Connect Health

Azure AD Connect e AD FS são componentes essenciais que podem interromper a autenticação e gerenciamento de ciclo de vida e, por fim, levar a interrupções. Portanto, você deve implantar o Azure AD Connect Health para monitoramento e relatórios desses componentes.

Para saber mais, acesse leitura [FS Monitor AD usando o Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs).

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Usar o Azure Monitor para coletar logs de dados para análise

[O Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) é um portal de monitoramento unificado para todos os logs do Azure AD, que fornece insights profundos, análises avançadas e aprendizado de máquina inteligentes. Com o Azure Monitor, você pode consumir as métricas e logs de dentro do portal e por meio de APIs para obter mais visibilidade sobre o estado e o desempenho dos seus recursos. Ele permite que um único painel de vidro a experiência dentro do portal, permitindo a criação de uma ampla gama de integrações de produtos por meio de opções de exportação de dados e as APIs que oferecem suporte a sistemas tradicionais de SIEM de terceiros. O Azure Monitor também oferece a capacidade de configurar regras de alerta para obter notificações ou tomar ações automatizadas nos problemas que afetam seus recursos.

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>Criar painéis personalizados para sua liderança e seu dia a dia

As organizações que não têm uma solução SIEM podem baixar o [pacote de conteúdo do Power BI](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack) para o Azure AD. O conteúdo de pacote contém relatórios pré-criados para ajudá-lo do Power BI entender como os usuários adotam e usam recursos do Azure AD, que permite que você obtenha informações sobre todas as atividades em seu diretório. Você também pode criar seus próprios [painel personalizado](https://docs.microsoft.com/power-bi/service-dashboards) e compartilhar com sua equipe de liderança para relatar as atividades diárias. Dashboards são uma ótima maneira de monitorar seu negócio e ver todas as suas métricas mais importantes em um relance. As visualizações em um dashboard podem vir de um conjunto de dados subjacente ou muitas e de um relatório subjacente ou muitas. Um painel combina locais e dados, fornecendo uma exibição consolidada independentemente de onde os dados residem na nuvem.

![Painel personalizado do Power BI](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>Entender os drivers de chamada de suporte

Quando você implementa uma solução de identidade híbrida, conforme descrito neste artigo, você deve observar, por fim, uma redução em suas chamadas de suporte. Problemas comuns, como senhas esquecidas e bloqueios de conta são atenuados com a implementação de redefinição de senha de autoatendimento do Azure, permitindo o acesso a aplicativos de autoatendimento permite aos usuários descobrir por conta própria e solicitar acesso a aplicativos sem depender sua equipe de TI.

Se você não observar uma redução nas chamadas de suporte, é recomendável que você analise seus drivers de chamada de suporte em uma tentativa de confirmar se SSPR ou acesso de aplicativo de autoatendimento foi configurado corretamente ou se houver outros novos problemas que podem ser sistematicamente resolvido.

*"Em nossa jornada de transformação digital, precisávamos de uma identidade confiável e o provedor de gerenciamento de acesso para facilitar a integração contínua, embora segura entre nós, parceiros e provedores de serviço de nuvem, para um ecossistema em vigor; Azure AD foi a melhor opção, oferecendo-nos recursos necessários e visibilidade que permitiu-nos detectar e responder a riscos."* --- [Aramex Yazan Almasri, diretor Global de segurança de informações](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>Monitorar o uso de aplicativos para conduzir insights

Além da descoberta de TI de sombra, monitoramento de uso do aplicativo em sua organização usando [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) pode ajudar sua organização, ao mudar para aproveitar ao máximo a promessa de aplicativos de nuvem. Ele pode ajudar a mantê-lo no controle de seus ativos com maior visibilidade da atividade e aumentar a proteção de dados críticos em aplicativos de nuvem. Monitorando o uso do aplicativo em sua organização usando MCAS pode ajudá-lo a responder às seguintes perguntas:

* Quais aplicativos não sancionados funcionários estão usando para armazenar dados em?
* Onde e quando é dados confidenciais que estão sendo armazenados na nuvem?
* Quem está acessando os dados confidenciais na nuvem?

*"Com o Cloud App Security, podemos rapidamente identificar anomalias e agir."* --- [Sênior Eric LePenske Gerenciador, Information Security, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>Resumo

Há muitos aspectos para implementar uma solução de identidade híbrida, mas esta lista de verificação de quatro etapas ajudarão você a executar rapidamente uma infraestrutura de identidade que permitirá que os usuários sejam mais produtivos e seguros.

* Conectar-se facilmente aos aplicativos
* Estabelecer uma identidade para todos os usuários automaticamente
* Capacite seus usuários com segurança
* Colocar suas ideias

Esperamos que este documento é um roteiro úteis para o estabelecimento de uma base de identidade forte para a sua organização.

## <a name="identity-checklist"></a>Lista de verificação de identidade

É recomendável que você imprima a lista de verificação a seguir para referência, conforme você começa sua jornada para uma base de identidade mais sólida em sua organização.

### <a name="today"></a>Hoje

|Pronto?|item|
|:-|:-|
||Piloto Self - Service senha SSPR (redefinição) para um grupo|
||Monitorar componentes híbrida usando o Azure AD Connect Health|
||Atribuir funções de administrador com privilégios mínimos para a operação|
||Descobrir TI sombra com o Microsoft Cloud App Security|
||Usar o Azure Monitor para coletar logs de dados para análise|

### <a name="next-two-weeks"></a>Próximas duas semanas

|Pronto?|item|
|:-|:-|
||Disponibilizar um aplicativo para seus usuários|
||Criar um piloto de provisionamento do Azure AD para um aplicativo SaaS de escolha|
||Configurar um servidor de preparo para o Azure AD Connect e mantê-lo atualizado|
||Iniciar a migração de aplicativos do AD FS para o Azure AD|
||Criar painéis personalizados para sua liderança e seu dia a dia|

### <a name="next-month"></a>No próximo mês

|Pronto?|item|
|:-|:-|
||Monitorar o uso de aplicativos para conduzir insights|
||Piloto acesso remoto seguro aos aplicativos|
||Verifique se que todos os usuários estão registrados para MFA e o SSPR|
||Habilitar a autenticação de nuvem|

### <a name="next-three-months"></a>Próximos três meses

|Pronto?|item|
|:-|:-|
||Habilitar o gerenciamento de aplicativo de autoatendimento|
||Habilitar o gerenciamento de grupo de autoatendimento|
||Monitorar o uso de aplicativos para conduzir insights|
||Entender os drivers de chamada de suporte|

## <a name="next-steps"></a>Próximas etapas

Saiba como você pode aumentar a sua situação de segurança usando os recursos do Azure Active Directory e essa lista de verificação de cinco etapas - [cinco etapas para proteger sua infraestrutura de identidade](https://aka.ms/securitysteps).

Saiba como os recursos de identidade no Azure AD podem ajudar você a Acelere sua transição para o gerenciamento controlado na nuvem, fornecendo as soluções e os recursos que permitem que as empresas a adotar rapidamente e mover mais de seu gerenciamento de identidade do tradicional em sistemas locais para o Azure AD - [como o Azure AD oferece regido gerenciamento de nuvem para cargas de trabalho local](https://aka.ms/cloudgoverned).
