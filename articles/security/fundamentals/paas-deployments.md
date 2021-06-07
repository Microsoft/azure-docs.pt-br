---
title: Práticas recomendadas para implantações de PaaS seguras-Microsoft Azure
description: Conheça as práticas recomendadas para projetar, criar e gerenciar aplicativos de nuvem seguros no Azure e entender as vantagens de segurança do PaaS em comparação com outros modelos de serviço de nuvem.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: techlake
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: 77a2a32b9a6358c39a14cfe37eeb44f7cb90af0a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94841981"
---
# <a name="securing-paas-deployments"></a>Proteção de implantações de PaaS

Este artigo fornece informações que ajudam você a:

- Compreender as vantagens de segurança de hospedar aplicativos na nuvem
- Avaliar as vantagens de segurança de PaaS (plataforma como serviço) em comparação com outros modelos de serviço de nuvem
- Alterar o foco de segurança de uma abordagem de segurança de perímetro centrada na rede para uma centrada em identidade
- Implementar as recomendações de práticas recomendadas de segurança de PaaS gerais

O [desenvolvimento de aplicativos seguros no Azure](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/) é um guia Geral das perguntas e controles de segurança que você deve considerar em cada fase do ciclo de vida do desenvolvimento de software ao desenvolver aplicativos para a nuvem.

## <a name="cloud-security-advantages"></a>Vantagens da segurança na nuvem
É importante entender a [divisão de responsabilidade](shared-responsibility.md) entre você e a Microsoft. No local, você possui a pilha inteira, mas conforme muda para a nuvem, algumas responsabilidades são transferidas para a Microsoft.

Há [vantagens de segurança para estar na nuvem](shared-responsibility.md#cloud-security-advantages). Em um ambiente local, as organizações provavelmente têm responsabilidades não atendidas e recursos limitados disponíveis para investir em segurança, o que cria um ambiente em que os invasores podem explorar vulnerabilidades em todas as camadas.

As empresas são capazes de aprimorar sua detecção de ameaças e tempos de resposta usando inteligência de nuvem e recursos de segurança baseados em nuvem do provedor.  Ao deslocar responsabilidades para o provedor de nuvem, as organizações podem obter mais cobertura de segurança, que permite que elas realocar recursos de segurança e orçamento para outras prioridades de negócios.

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>Vantagens de segurança de um modelo de serviço de nuvem PaaS
Vejamos as vantagens de segurança de uma implantação de PaaS do Azure em relação ao local.

![Vantagens de segurança do PaaS](./media/paas-deployments/advantages-of-paas.png)

Iniciando na parte inferior da pilha, a infraestrutura física, a Microsoft reduz responsabilidades e riscos comuns. Como o Microsoft Cloud é monitorado continuamente pela Microsoft é difícil de atacar. Não faz sentido para um invasor buscar o Microsoft Cloud como um alvo. A menos que o invasor tenha muito dinheiro e recursos, é provável que ele mude para outro alvo.  

No meio da pilha, não há nenhuma diferença entre uma implantação de PaaS e local. Na camada de aplicativo e na camada de gerenciamento de acesso e de conta, você tem os riscos semelhantes. Na seção de próximas etapas desse artigo, guiaremos você para as práticas recomendadas para eliminar ou minimizar esses riscos.

Na parte superior da pilha, governança de dados e gerenciamento de direitos, você assume um risco que pode ser reduzido pelo gerenciamento de chaves. (O gerenciamento de chaves é abordado nas práticas recomendadas.) Embora o gerenciamento de chaves seja uma responsabilidade adicional, você tem áreas em uma implantação de PaaS que não precisa mais gerenciar para poder mudar os recursos para o gerenciamento de chaves.

A plataforma Azure também fornece uma forte proteção DDoS usando várias tecnologias baseadas em rede. No entanto, todos os tipos de métodos de proteção DDoS baseados em rede tem seus limites em uma base por link e por datacenter. Para ajudar a evitar o impacto de grandes ataques DDoS, você pode aproveitar a funcionalidade de nuvem central do Azure que lhe permite escalar horizontalmente de maneira rápida e automática para proteger contra os ataques DDoS. Entraremos em mais detalhes sobre como você pode fazer isso nos artigos de práticas recomendadas.

## <a name="modernizing-the-defenders-mindset"></a>Modernizando mentalidade do defensor
Juntamente com as implantações de PaaS vem uma mudança na abordagem geral da segurança. Você passa de precisar controlar tudo sozinho com compartilhar a responsabilidade com a Microsoft.

Outra diferença significativa entre as implantações locais tradicionais e de PaaS é uma nova exibição do que define o perímetro de segurança primário. Historicamente, o perímetro de segurança local primário foi sua rede e a maioria dos projetos de segurança local usam a rede como seu pivô de segurança primário. Para implantações de PaaS, você é melhor atendido considerando a identidade como o perímetro de segurança primário.

## <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Adotar uma política de identidade como o perímetro de segurança primário
Uma das cinco características essenciais da computação em nuvem é amplo acesso à rede, que torna o pensamento centrado na rede menos relevante. A meta da maior parte da computação em nuvem é permitir que os usuários acessem recursos independentemente da localização. Para a maioria dos usuários, sua localização será em algum lugar na Internet.

A figura a seguir mostra como o perímetro de segurança evoluiu de um perímetro de rede para um perímetro de identidade. A segurança se torna menos sobre defender a rede e mais sobre como defender seus dados, bem como gerenciar a segurança de seus aplicativos e usuários. A principal diferença é que você deseja avançar a segurança para mais perto do que é importante para sua empresa.

![Identidade como novo perímetro de segurança](./media/paas-deployments/identity-perimeter.png)

Inicialmente, os serviços de PaaS do Azure (por exemplo, funções Web e SQL do Azure) forneciam pouca ou nenhuma defesa de perímetro de rede tradicional. Entendia-se que o objetivo do elemento era ser exposto na Internet (função web) e que a autenticação fornece o novo perímetro (por exemplo, Blob ou SQL do Azure).

As práticas de segurança modernas pressupõem que o adversário violou o perímetro da rede. Portanto, as práticas de defesa modernas passaram para a identidade. As organizações devem estabelecer um perímetro de segurança com base em identidade com forte higiene de autenticação e autorização (práticas recomendadas).

Os princípios e padrões do perímetro de rede estiveram disponíveis por décadas. Por outro lado, o segmento tem relativamente menos experiência com o uso de identidade como o perímetro de segurança primário. Dito isso, acumulamos experiência suficiente para fornecer algumas recomendações gerais comprovadas no campo e que se aplicam a quase todos os serviços de PaaS.

A seguir estão as práticas recomendadas para gerenciar o perímetro de identidade.

**Prática recomendada**: proteger suas chaves e credenciais para proteger a implantação de PaaS.   
**Detalhe**: perder chaves e credenciais é um problema comum. Você pode usar uma solução centralizada na qual as chaves e os segredos podem ser armazenados em HSMs (módulos de segurança de hardware). O [Azure Key Vault](../../key-vault/general/overview.md) protege suas chaves e segredos criptografando chaves de autenticação, chaves de conta de armazenamento, chaves de criptografia de dados, arquivos. pfx e senhas usando chaves que são protegidas por hsms.

**Prática recomendada**: não colocar as credenciais e outros segredos no código-fonte nem no GitHub.   
**Detalhe**: a única coisa pior do que perder as chaves e credenciais é quando uma parte não autorizada obtém acesso a elas. Os invasores podem aproveitar as tecnologias de bot para encontrar chaves e segredos armazenados em repositórios de código, como o GitHub. Não coloque a chave e os segredos nesses repositórios de código público.

**Prática recomendada**: proteger suas interfaces de gerenciamento de VM em serviços híbridos de PaaS e IaaS usando uma interface de gerenciamento que permite gerenciar remotamente essas VMs de modo direto.   
**Detalhe**: os protocolos de gerenciamento remoto, como [SSH](https://en.wikipedia.org/wiki/Secure_Shell), [RDP](https://support.microsoft.com/kb/186607), e a [comunicação remota do PowerShell](/powershell/module/microsoft.powershell.core/enable-psremoting) podem ser usados. Em geral, é recomendado que você não habilite o acesso remoto direto às VMs pela Internet.

Se possível, use abordagens alternativas como redes virtuais privadas em uma rede virtual do Azure. Se abordagens alternativas não estiverem disponíveis, certifique-se de usar frases secretas complexas e autenticação de dois fatores (como a [autenticação multifator do Azure ad](../../active-directory/authentication/concept-mfa-howitworks.md)).

**Prática recomendada**: usar as plataformas fortes de autorização e autenticação.   
**Detalhe**: use identidades federadas no Azure AD em vez de repositórios de usuário personalizados. Quando você usa identidades federadas, aproveita uma abordagem baseada em plataforma e delega o gerenciamento de identidades autorizadas para seus parceiros. Uma abordagem de identidade federada é importante principalmente quando ocorre a demissão de funcionários e essa informação precisa ser refletida em vários sistemas de autorização e identidade.

Use os mecanismos de autenticação e autorização fornecidos pela plataforma em vez de código personalizado. O motivo é que o desenvolvimento de código de autenticação personalizado pode ser propenso a erros. A maioria dos desenvolvedores não é especialista em segurança e provavelmente não está ciente das sutilezas e dos desenvolvimentos mais recentes em autenticação e autorização. O código comercial (por exemplo, da Microsoft) é amplamente revisado quanto à segurança, com frequência.

Use a autenticação de dois fatores. A autenticação de dois fatores é o padrão atual para autenticação e autorização porque evita os pontos fracos de segurança inerentes nos tipos de autenticação de nome de usuário e senha. O acesso às interfaces de gerenciamento do Azure (Portal/PowerShell remoto) e serviços voltados para o cliente deve ser projetado e configurado para usar a [autenticação multifator do Azure ad](../../active-directory/authentication/concept-mfa-howitworks.md).

Use protocolos de autenticação padrão, como OAuth2 e Kerberos. Esses protocolos foram extensivamente revisados em pares e provavelmente são implementados como parte das bibliotecas da plataforma para autenticação e autorização.

## <a name="use-threat-modeling-during-application-design"></a>Usar a modelagem de ameaças durante o design do aplicativo
O Microsoft [Security Development Lifecycle](https://www.microsoft.com/en-us/sdl) especifica que as equipes devem participar de um processo chamado de modelagem de ameaças durante a fase de design. Para facilitar esse processo, a Microsoft criou a [Threat Modeling Tool do SDL](../develop/threat-modeling-tool.md). A modelagem do design do aplicativo e a enumeração de ameaças [STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) em todos os limites de confiança podem capturar erros de design logo no início.

A tabela a seguir lista as ameaças STRIDE e fornece algumas mitigações de exemplo que usam recursos do Azure. Essas mitigações não funcionarão em todas as situações.

| Ameaça | Propriedade de segurança | Possível migrações para a plataforma Azure |
| --- | --- | --- |
| Falsificação | Autenticação | Exigir conexões HTTPS. |
| Adulteração | Integridade | Validar certificados TLS/SSL. |
| Repúdio | Não repúdio | Habilitar o [monitoramento e diagnóstico](/azure/architecture/best-practices/monitoring) do Azure. |
| Divulgação de informações confidenciais | Confidencialidade | Criptografar dados confidenciais em repouso usando [certificados de serviço](/rest/api/appservice/certificates). |
| Negação de serviço | Disponibilidade | Monitorar as métricas de desempenho quanto a possíveis condições de negação de serviço. Implementar filtros de conexão. |
| Elevação de privilégio | Autorização | Usar o [Privileged Identity Management](../../active-directory/privileged-identity-management/subscription-requirements.md). |

## <a name="develop-on-azure-app-service"></a>Desenvolver no Serviço de Aplicativo do Azure
O [Serviço de Aplicativo do Azure](../../app-service/overview.md) é uma oferta de PaaS que permite a você criar aplicativos Web e móveis para qualquer plataforma ou dispositivo e se conectar a dados em qualquer lugar, na nuvem ou local. O Serviço de Aplicativo inclui os recursos móveis e da Web que antes eram fornecidos separadamente, como os Sites do Azure e os Serviços Móveis do Azure. Ele também inclui novos recursos para automatizar processos empresariais e hospedagem de APIs de nuvem. O Serviço de Aplicativo proporciona, como um único serviço integrado, um avançado conjunto de recursos para cenários da Web, móveis e de integração.

A seguir estão as práticas recomendadas para usar o Serviço de Aplicativo.

**Prática recomendada**: [autenticar usando o Azure Active Directory](../../app-service/overview-authentication-authorization.md).   
**Detalhe**: o Serviço de Aplicativo fornece um serviço OAuth 2.0 para seu provedor de identidade. O OAuth 2.0 concentra-se na simplicidade do desenvolvedor cliente, fornecendo fluxos de autorização específicos para aplicativos Web, aplicativos da área de trabalho e celulares. O Azure AD usa o OAuth 2.0 para que você possa autorizar o acesso a aplicativos móveis e da Web.

**Prática recomendada**: restringir o acesso com base na necessidade de saber e nos princípios de segurança de privilégios mínimos.   
**Detalhe**: restringir o acesso é fundamental para as organizações que desejam impor políticas de segurança de acesso a dados. Você pode usar o Azure RBAC para atribuir permissões a usuários, grupos e aplicativos em um determinado escopo. Para saber mais sobre como conceder acesso a aplicativos aos usuários, confira [introdução ao gerenciamento de acesso](../../role-based-access-control/overview.md).

**Prática recomendada**: proteger seus dados.   
**Detalhe**: o Azure Key Vault ajuda a proteger chaves de criptografia e os segredos que os aplicativos e serviços de nuvem usam. Com o Key Vault, você pode criptografar chaves e segredos (como chaves de autenticação, chaves de conta de armazenamento, chaves de criptografia de dados, arquivos .PFX e senhas) usando chaves protegidas por HSMs (módulos de segurança de hardware). Para garantia extra, você pode importar ou gerar chaves em HSMs. Confira [Azure Key Vault](../../key-vault/general/overview.md) para saber mais. Também é possível usar o Key Vault para gerenciar seus certificados TLS com renovação automática.

**Prática recomendada**: restringir os endereços IP de origem de entrada.   
**Detalhe**: o [Ambiente do Serviço de Aplicativo](../../app-service/environment/intro.md) tem um recurso de integração de rede virtual que ajuda a restringir endereços IP de origem de entrada por meio de grupos de segurança de rede. As redes virtuais permitem que você coloque recursos do Azure em uma rede roteável, fora da Internet, cujo acesso você pode controlar. Para obter mais informações, confira [Integrar um aplicativo a uma rede virtual do Azure](../../app-service/web-sites-integrate-with-vnet.md).

**Prática recomendada**: monitorar o estado de segurança de seus ambientes do Serviço de Aplicativo.   
**Detalhe**: use a Central de Segurança do Azure para monitorar seus ambientes do Serviço de Aplicativo. Quando a Central de Segurança identifica possíveis vulnerabilidades de segurança, ela cria [recomendações](../../security-center/asset-inventory.md) que guiam você pelo processo de configuração dos controles necessários.

> [!NOTE]
> O monitoramento do serviço de aplicativo está em visualização e está disponível apenas na [camada Standard](../../security-center/security-center-pricing.md) da central de segurança.
>
>

## <a name="install-a-web-application-firewall"></a>Instalar um firewall do aplicativo Web
Os aplicativos Web cada vez mais são alvos de ataques mal-intencionados que exploram vulnerabilidades conhecidas comuns. Os ataques de injeção de SQL, os ataques de scripts entre sites, entre outros, são comuns entre essas explorações. Pode ser difícil impedir esses ataques no código do aplicativo e isso pode exigir manutenção, aplicação de patches e monitoramento rigorosos em muitas camadas da topologia do aplicativo. Um firewall de aplicativo Web centralizado ajuda a simplificar bastante o gerenciamento de segurança e oferece mais garantia ao administrador do aplicativo contra ameaças ou invasões. Uma solução WAF também pode reagir a uma ameaça de segurança mais rapidamente ao aplicar um patch contra uma vulnerabilidade conhecida em um local central do que a proteção de cada um dos aplicativos Web individuais. Os gateways de aplicativos existentes podem ser facilmente convertidos em um gateway de aplicativo com firewall de aplicativo Web.

O [WAF (firewall do aplicativo Web)](../../web-application-firewall/afds/afds-overview.md) é um recurso do Gateway de Aplicativo que fornece proteção centralizada de seus aplicativos Web contra vulnerabilidades e explorações comuns. O WAF é baseado em regras dos [conjuntos de regras principais do OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 ou 2.2.9.

## <a name="monitor-the-performance-of-your-applications"></a>Monitorar o desempenho de seus aplicativos
O monitoramento é o ato de coletar e analisar dados para determinar o desempenho, a integridade e a disponibilidade do aplicativo. Uma estratégia de monitoramento eficaz ajuda a esclarecer a operação detalhada dos componentes do seu aplicativo. Ela também ajuda a aumentar o tempo de atividade notificando sobre questões críticas para que você possa resolvê-las antes que se tornem problemas. Além disso, ela ajuda a detectar anomalias que possam estar relacionadas à segurança.

Use o [Azure Application Insights](https://azure.microsoft.com/documentation/services/application-insights) para monitorar a disponibilidade, o desempenho e o uso do aplicativo, esteja ele hospedado na nuvem ou localmente. Usando o Application Insights, você pode identificar e diagnosticar erros no aplicativo sem esperar que um usuário os relate. Com as informações coletadas, será possível fazer as escolhas informadas sobre a manutenção e as melhorias do seu aplicativo.

O Application Insights tem ferramentas abrangentes para interagir com os dados que coleta. O Application Insights armazena seus dados em um repositório comum. Ele pode aproveitar a funcionalidade compartilhada, como alertas, painéis e análise profunda com a linguagem de consulta Kusto.

## <a name="perform-security-penetration-testing"></a>Executar teste de penetração de segurança
A validação de defesas de segurança é tão importante quanto testar qualquer outra funcionalidade. Faça um [teste de penetração](pen-testing.md) de uma parte padrão do processo de compilação e implantação. Agende testes de segurança e verificações de vulnerabilidade regulares em aplicativos implantados e monitore em busca de ataques e de portas e pontos de extremidade abertos.

O teste de difusão é um método para localizar falhas de programa (erros de código) fornecendo dados de entrada malformados para interfaces de programa (pontos de entrada) que analisam e consomem esses dados. A [detecção de riscos de segurança da Microsoft](https://www.microsoft.com/en-us/security-risk-detection/) é uma ferramenta baseada em nuvem que você pode usar para procurar bugs e outras vulnerabilidades de segurança em seu software antes de implantá-lo no Azure. A ferramenta foi projetada para detectar vulnerabilidades antes de implantar o software para que você não precise corrigir um bug, lidar com panes ou responder a um ataque após o lançamento do software.


## <a name="next-steps"></a>Próximas etapas
Neste artigo, nos concentramos nas vantagens de segurança de uma implantação de PaaS do Azure e nas práticas recomendadas de segurança para aplicativos em nuvem. Em seguida, aprenda as práticas recomendadas para proteger suas soluções Web e móveis de PaaS usando serviços específicos do Azure. Começaremos com o serviço Azure App, o banco de dados SQL do Azure e o Azure Synapse Analytics e o armazenamento do Azure. Conforme os artigos sobre práticas recomendadas para outros serviços do Azure forem disponibilizados, os links serão fornecidos na lista a seguir:

- [Serviço de Aplicativo do Azure](paas-applications-using-app-services.md)
- [Banco de dados SQL do Azure e análise de Synapse do Azure](paas-applications-using-sql.md)
- [Armazenamento do Azure](paas-applications-using-storage.md)
- Cache Redis do Azure
- Barramento de Serviço do Azure
- Firewalls de aplicativo Web

Consulte [desenvolvendo aplicativos seguros no Azure](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/) para questões de segurança e controles que você deve considerar em cada fase do ciclo de vida do desenvolvimento de software ao desenvolver aplicativos para a nuvem.

Veja [Melhores práticas e padrões de segurança do Azure](best-practices-and-patterns.md) para obter melhores práticas segurança complementares a serem usadas ao projetar, implantar e gerenciar as soluções de nuvem, usando o Azure.

Os seguintes recursos estão disponíveis para fornecer mais informações gerais sobre a segurança do Azure e os serviços da Microsoft relacionados:
* [Blog da equipe de segurança do Azure](/archive/blogs/azuresecurity/) – para obter informações atualizadas sobre as últimas novidades de Segurança do Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – em que vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, podem ser relatadas ou enviadas por email para secure@microsoft.com