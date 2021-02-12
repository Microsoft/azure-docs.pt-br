---
title: Criar aplicativos seguros no Microsoft Azure
description: Este artigo aborda as práticas recomendadas a serem consideradas durante o requisito e as fases de design do seu projeto de aplicativo Web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 743412b7602e5781911cdf190e41a5ee15bfddd4
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487670"
---
# <a name="design-secure-applications-on-azure"></a>Criar aplicativos seguros no Azure
Neste artigo, apresentamos as atividades e os controles de segurança a serem considerados quando você cria aplicativos para a nuvem. Os recursos de treinamento, juntamente com as perguntas de segurança e os conceitos a serem considerados durante as fases de design e de requisitos do Microsoft [Security Development Lifecycle (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) , são abordados. O objetivo é ajudá-lo a definir atividades e serviços do Azure que você pode usar para criar um aplicativo mais seguro.

As seguintes fases do SDL são abordadas neste artigo:

- Treinamento
- Requisitos
- Design

## <a name="training"></a>Treinamento
Antes de começar a desenvolver seu aplicativo de nuvem, Reserve um tempo para entender a segurança e a privacidade no Azure. Ao fazer essa etapa, você pode reduzir o número e a gravidade de vulnerabilidades exploráveis em seu aplicativo. Você estará mais preparado para reagir adequadamente ao panorama de ameaças em constante mudança.

Use os seguintes recursos durante o estágio de treinamento para se familiarizar com os serviços do Azure que estão disponíveis para desenvolvedores e as práticas recomendadas de segurança no Azure:

  - [Guia do desenvolvedor para o Azure](https://azure.microsoft.com/campaigns/developer-guide/) mostra como começar a usar o Azure. O guia mostra quais serviços você pode usar para executar seus aplicativos, armazenar seus dados, incorporar inteligência, criar aplicativos de IoT e implantar suas soluções de maneira mais eficiente e segura.

  - O [Guia de introdução para os desenvolvedores do Azure](../../guides/developer/azure-developer-guide.md) fornece informações essenciais para os desenvolvedores que procuram começar a usar a plataforma Azure para suas necessidades de desenvolvimento.

  - [SDKs e ferramentas](../../index.yml?pivot=sdkstools) descreve as ferramentas que estão disponíveis no Azure.

  - [Azure DevOps Services](/azure/devops/) fornece ferramentas de colaboração de desenvolvimento. As ferramentas incluem pipelines de alto desempenho, repositórios git gratuitos, quadros Kanban configuráveis e testes de carga abrangentes e baseados em nuvem.
    A [central de recursos DevOps](/azure/devops/learn/) combina nossos recursos para aprendizagem de práticas de DevOps, controle de versão do git, métodos Agile, como trabalhamos com DevOps na Microsoft e como você pode avaliar sua própria progressão de DevOps.

  - [Os 5 principais itens de segurança a serem considerados antes de enviar para produção](/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca) mostram como ajudar a proteger seus aplicativos Web no Azure e proteger seus aplicativos contra os ataques de aplicativos Web mais comuns e perigosos.

  - O [Secure DevOps Kit para Azure](https://azsk.azurewebsites.net/index.html) é uma coleção de scripts, ferramentas, extensões e automaçãos que atendem às necessidades abrangentes de segurança de recursos e de assinatura do Azure das equipes do DevOps que usam ampla automação. O Secure DevOps Kit para Azure pode mostrar como integrar perfeitamente a segurança aos seus fluxos de trabalho nativos do DevOps. O kit aborda ferramentas como SVTs (testes de verificação de segurança), que podem ajudar os desenvolvedores a escrever código seguro e testar a configuração segura de seus aplicativos na nuvem nos estágios de codificação e desenvolvimento antecipado.

  - [As práticas recomendadas de segurança para soluções do Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions) fornecem uma coleção de práticas recomendadas de segurança para usar à medida que você projeta, implanta e gerencia suas soluções de nuvem usando o Azure.

## <a name="requirements"></a>Requisitos
A fase de definição de requisitos é uma etapa crucial para definir o que é seu aplicativo e o que ele fará quando for lançado. A fase de requisitos também é um momento para pensar sobre os controles de segurança que você criará em seu aplicativo. Durante essa fase, você também inicia as etapas que serão executadas em todo o SDL para garantir que você libere e implante um aplicativo seguro.

### <a name="consider-security-and-privacy-issues"></a>Considerar problemas de segurança e privacidade
Essa fase é o melhor momento para considerar problemas de segurança e privacidade fundamentais. Definir níveis aceitáveis de segurança e privacidade no início de um projeto ajuda uma equipe:

- Entenda os riscos associados a problemas de segurança.
- Identificar e corrigir bugs de segurança durante o desenvolvimento.
- Aplique níveis estabelecidos de segurança e privacidade em todo o projeto.

Ao escrever os requisitos para seu aplicativo, considere os controles de segurança que podem ajudar a manter seu aplicativo e dados seguros.

### <a name="ask-security-questions"></a>Fazer perguntas de segurança
Faça perguntas de segurança como:

  - Meu aplicativo contém dados confidenciais?

  - Meu aplicativo coleta ou armazena dados que exigem a adesão a padrões do setor e programas de conformidade como o [FFIEC (Instituto financeiro federal de exames)](/previous-versions/azure/security/blueprints/ffiec-analytics-overview) ou os [padrões de segurança de dados do setor de cartão de pagamento (PCI DSS)](/previous-versions/azure/security/blueprints/pcidss-analytics-overview)?

  - O meu aplicativo coleta ou contém dados confidenciais pessoais ou de clientes que podem ser usados, por conta própria ou por outras informações, para identificar, contatar ou localizar uma única pessoa?

  - Meu aplicativo coleta ou contém dados que podem ser usados para acessar informações médicas, educacionais, financeiras ou de empregos de um indivíduo? Identificar a sensibilidade dos seus dados durante a fase de requisitos ajuda a classificar seus dados e identificar o método de proteção de dados que você usará para seu aplicativo.

  - Onde e como os meus dados são armazenados? Considere como você monitorará os serviços de armazenamento que seu aplicativo usa para quaisquer alterações inesperadas (como tempos de resposta mais lentos). Será possível influenciar o registro em log para coletar dados mais detalhados e analisar um problema em profundidade?

  - Meu aplicativo estará disponível para o público (na Internet) ou apenas internamente? Se seu aplicativo estiver disponível para o público, como você protege os dados que podem ser coletados de forma incorreta? Se seu aplicativo estiver disponível apenas internamente, considere quem em sua organização deve ter acesso ao aplicativo e por quanto tempo eles devem ter acesso.

  - Você entende seu modelo de identidade antes de começar a projetar seu aplicativo? Como você determinará que os usuários são quem dizem que são e o que um usuário está autorizado a fazer?

  - Meu aplicativo executa tarefas confidenciais ou importantes (como a transferência de dinheiro, o desbloqueio de portas ou o fornecimento de medicina)?
    Considere como você irá validar que o usuário que está executando uma tarefa confidencial está autorizado a executar a tarefa e como você autenticará que a pessoa é quem eles dizem. Autorização (AuthZ) é o ato de conceder uma permissão de entidade de segurança autenticada para fazer algo. Autenticação (Authn) é o ato de desafiador para credenciais legítimas.

  - Meu aplicativo executa qualquer atividade de software arriscada, como permitir que os usuários carreguem ou baixem arquivos ou outros dados? Se seu aplicativo executar atividades arriscadas, considere como seu aplicativo protegerá os usuários de lidar com arquivos ou dados mal-intencionados.

### <a name="review-owasp-top-10"></a>Examinar os 10 primeiros OWASP
Considere analisar os [<span class="underline">10 principais riscos de segurança de aplicativo do OWASP</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).
Os 10 principais OWASP abordam os riscos de segurança críticos aos aplicativos Web.
A conscientização desses riscos de segurança pode ajudá-lo a tomar decisões de design e requisito que minimizem esses riscos em seu aplicativo.

Pensar nos controles de segurança para evitar violações é importante.
No entanto, você também desejará supor que ocorrerá [uma violação](/azure/devops/learn/devops-at-microsoft/security-in-devops) . Pressupor que uma violação ajude a responder algumas perguntas importantes sobre segurança com antecedência, para que elas não precisem ser respondidas em uma emergência:

  - Como detectar um ataque?

  - O que fará se houver um ataque ou uma violação?

  - Como vou me recuperar do ataque, como vazamento de dados ou adulteração?

## <a name="design"></a>Design

A fase de design é essencial para estabelecer práticas recomendadas para design e especificações funcionais. Também é essencial para a realização da análise de riscos que ajuda a reduzir problemas de segurança e privacidade em um projeto.

Quando você tem requisitos de segurança em vigor e usa conceitos de design seguro, você pode evitar ou minimizar oportunidades para uma falha de segurança. Uma falha de segurança é uma supervisão no design do aplicativo que pode permitir que um usuário execute ações mal-intencionadas ou inesperadas após o lançamento do aplicativo.

Durante a fase de design, pense também em como você pode aplicar segurança em camadas; um nível de defesa não é necessariamente suficiente. O que acontece se um invasor ultrapassar o WAF (firewall do aplicativo Web)? Você quer outro controle de segurança em vigor para se defender contra esse ataque.

Com isso em mente, discutiremos os seguintes conceitos de design seguro e os controles de segurança que você deve abordar ao projetar aplicativos seguros:

- Use uma biblioteca de codificação segura e uma estrutura de software.
- Verificar se há componentes vulneráveis.
- Use a modelagem de ameaças durante o design do aplicativo.
- Reduza sua superfície de ataque.
- Adote uma política de identidade como o perímetro de segurança primário.
- Exigir nova autenticação para transações importantes.
- Use uma solução de gerenciamento de chaves para proteger chaves, credenciais e outros segredos.
- Proteger dados confidenciais.
- Implemente medidas à prova de falhas.
- Aproveite o tratamento de erros e exceções.
- Use o registro em log e alertas.

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>Usar uma biblioteca de codificação segura e uma estrutura de software

Para o desenvolvimento, use uma biblioteca de codificação segura e uma estrutura de software que tenha segurança incorporada. Os desenvolvedores podem usar recursos existentes e comprovados (criptografia, entrada saneamento, codificação de saída, chaves ou cadeias de conexão e qualquer outra coisa que seja considerada um controle de segurança) em vez de desenvolver controles de segurança do zero. Isso ajuda a proteger contra falhas de implementação e design relacionadas à segurança.

Verifique se você está usando a versão mais recente da estrutura e todos os recursos de segurança disponíveis na estrutura. A Microsoft oferece um [conjunto abrangente de ferramentas de desenvolvimento](https://azure.microsoft.com/product-categories/developer-tools/) para todos os desenvolvedores, trabalhando em qualquer plataforma ou linguagem, para fornecer aplicativos de nuvem. Você pode codificar com a linguagem de sua escolha escolhendo entre vários [SDKs](https://azure.microsoft.com/downloads/).
Você pode aproveitar os IDEs (ambientes de desenvolvimento integrado) completos e editores que têm recursos de depuração avançados e suporte interno do Azure.

A Microsoft oferece uma variedade de [linguagens, estruturas e ferramentas](../../index.yml?panel=sdkstools-all&pivot=sdkstools) que você pode usar para desenvolver aplicativos no Azure. Um exemplo é o [Azure para desenvolvedores .net e .NET Core](/dotnet/azure/). Para cada idioma e estrutura que oferecemos, você encontrará guias de início rápido, tutoriais e referências de API para ajudá-lo a começar rapidamente.

O Azure oferece uma variedade de serviços que você pode usar para hospedar sites e aplicativos Web. Esses serviços permitem desenvolver em sua linguagem favorita, seja .NET, .NET Core, Java, Ruby, Node.js, PHP ou Python.
Os [aplicativos Web do serviço Azure app](../../app-service/overview.md) (aplicativos Web) são um desses serviços.

Os aplicativos Web adicionam o poder do Microsoft Azure ao seu aplicativo. Ele inclui segurança, balanceamento de carga, dimensionamento automático e gerenciamento automatizado. Você também pode aproveitar os recursos do DevOps em aplicativos Web, como gerenciamento de pacotes, ambientes de preparo, domínios personalizados, certificados SSL/TLS e implantação contínua do Azure DevOps, GitHub, Hub do Docker e outras fontes.

O Azure oferece outros serviços que você pode usar para hospedar sites e aplicativos Web. Os Aplicativos Web são a melhor opção para a maioria dos cenários. Para uma arquitetura de micro Service, considere o [Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric).
Se você precisar de mais controle sobre as VMs nas quais seu código é executado, considere as [Máquinas Virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/).
Para obter mais informações sobre como escolher entre esses serviços do Azure, consulte uma [comparação de Azure app serviço, máquinas virtuais, Service Fabric e serviços de nuvem](/azure/architecture/guide/technology-choices/compute-decision-tree).

### <a name="apply-updates-to-components"></a>Aplicar atualizações aos componentes

Para evitar vulnerabilidades, você deve inventariar continuamente os componentes do lado do cliente e do servidor (por exemplo, estruturas e bibliotecas) e suas dependências para atualizações. Novas vulnerabilidades e versões de software atualizadas são lançadas continuamente. Verifique se você tem um plano contínuo para monitorar, fazer triagem e aplicar atualizações ou alterações de configuração às bibliotecas e aos componentes que você usa.

Consulte a página [Abrir projeto de segurança de aplicativo Web (OWASP)](https://www.owasp.org/index.php/Main_Page) em [usando componentes com vulnerabilidades conhecidas](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities) para sugestões de ferramenta. Você também pode assinar alertas por email para vulnerabilidades de segurança relacionadas aos componentes que você usa.

### <a name="use-threat-modeling-during-application-design"></a>Usar a modelagem de ameaças durante o design do aplicativo

A modelagem de ameaças é o processo de identificar possíveis ameaças à segurança para seus negócios e aplicativos e, em seguida, garantir que as atenuações adequadas estejam em vigor. O SDL especifica que as equipes devem se envolver na modelagem de ameaças durante a fase de design, ao resolver possíveis problemas é relativamente fácil e econômico. Usar a modelagem de ameaças na fase de design pode reduzir muito o custo total de desenvolvimento.

Para ajudar a facilitar o processo de modelagem de ameaças, projetamos o [Threat Modeling Tool do SDL](threat-modeling-tool.md) com especialistas não relacionados à segurança em mente. Essa ferramenta facilita a modelagem de ameaças para todos os desenvolvedores, fornecendo uma orientação clara sobre como criar e analisar modelos de ameaça.

Modelando o design do aplicativo e enumerando as ameaças [Stride](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) — falsificação, violação, repúdio, divulgação de informações, negação de serviço e elevação de privilégio — em todos os limites de confiança, comprovou uma maneira eficaz de detectar erros de design no início. A tabela a seguir lista as ameaças STRIDE e fornece algumas atenuações de exemplo que usam os recursos fornecidos pelo Azure. Essas mitigações não funcionarão em todas as situações.

| Ameaça | Propriedade de segurança | Mitigação potencial da plataforma Azure |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Falsificação               | Autenticação        | [Exigir conexões HTTPS](/aspnet/core/security/enforcing-ssl?tabs=visual-studio&view=aspnetcore-2.1). |
| Adulteração              | Integridade             | Validar certificados SSL/TLS. Os aplicativos que usam SSL/TLS devem verificar completamente os certificados X. 509 das entidades a que se conectam. Use Azure Key Vault certificados para [gerenciar seus certificados X509](../../key-vault/general/about-keys-secrets-certificates.md). |
| Repúdio            | Não repúdio       | Habilitar o [monitoramento e diagnóstico](/azure/architecture/best-practices/monitoring) do Azure.|
| Divulgação de informações confidenciais | Confidencialidade       | Criptografe dados confidenciais [em repouso](../fundamentals/encryption-atrest.md) e [em trânsito](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit). |
| Negação de serviço      | Disponibilidade          | Monitore as métricas de desempenho para possíveis condições de negação de serviço. Implementar filtros de conexão. A [proteção contra DDoS do Azure](../../ddos-protection/ddos-protection-overview.md#next-steps), combinada com as práticas recomendadas de design de aplicativo, fornece defesa contra ataques de DDoS.|
| Elevação de privilégio | Autorização         | Use Azure Active Directory <span class="underline"> </span> [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md).|

### <a name="reduce-your-attack-surface"></a>Reduzir a superfície de ataque

Uma superfície de ataque é a soma total de onde podem ocorrer possíveis vulnerabilidades. Neste artigo, nos concentramos na superfície de ataque de um aplicativo.
O foco é proteger um aplicativo contra ataques. Uma maneira simples e rápida de minimizar a superfície de ataque é remover recursos e códigos não utilizados do seu aplicativo. Quanto menor o seu aplicativo, menor a superfície de ataque. Por exemplo, remova:

- Código para recursos que você ainda não lançou.
- Código de suporte à depuração.
- Adaptadores de rede e protocolos que não são usados ou que foram preteridos.
- Máquinas virtuais e outros recursos que você não está usando.

Fazer uma limpeza regular de seus recursos e garantir que você remova o código não utilizado é uma ótima maneira de garantir que haja menos oportunidades para ataques maliciosos.

Uma maneira mais detalhada e aprofundada de reduzir sua superfície de ataque é concluir uma análise da superfície de ataque. Uma análise da superfície de ataque ajuda a mapear as partes de um sistema que precisam ser examinadas e testadas quanto a vulnerabilidades de segurança.

A finalidade de uma análise da superfície de ataque é entender as áreas de risco em um aplicativo para que os desenvolvedores e especialistas em segurança saibam quais partes do aplicativo estão abertas para atacar. Em seguida, você pode encontrar maneiras de minimizar esse potencial, controlar quando e como a superfície de ataque muda e o que isso significa de uma perspectiva de risco.

Uma análise da superfície de ataque ajuda a identificar:

- Funções e partes do sistema você precisa examinar e testar vulnerabilidades de segurança.
- Áreas de código de alto risco que exigem proteção de defesa intensa (partes do sistema que você precisa defender).
- Quando você altera a superfície de ataque e precisa atualizar uma avaliação de ameaça.

Reduzir as oportunidades de invasores de explorar um possível ponto fraco ou uma vulnerabilidade exige que você analise minuciosamente a superfície de ataque geral do aplicativo. Ele também inclui desabilitar ou restringir o acesso aos serviços do sistema, aplicar o princípio de privilégios mínimos e empregar defesas em camadas sempre que possível.

Abordamos a [condução de uma análise da superfície de ataque](secure-develop.md#conduct-attack-surface-review) durante a fase de verificação do SDL.

> [!NOTE]
> **Qual é a diferença entre a modelagem de ameaças e a análise da superfície de ataque?**
A modelagem de ameaças é o processo de identificar possíveis ameaças à segurança para seu aplicativo e garantir que as atenuações adequadas contra as ameaças estejam em vigor. A análise da superfície de ataque identifica áreas de alto risco de código que estão abertas para atacar. Ele envolve encontrar maneiras de defender áreas de alto risco de seu aplicativo e revisar e testar essas áreas de código antes de implantar o aplicativo.

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Adotar uma política de identidade como o perímetro de segurança primário

Quando você projeta aplicativos de nuvem, é importante expandir seu foco de perímetro de segurança de uma abordagem centrada na rede para uma abordagem centrada em identidade. Historicamente, o perímetro de segurança local primário era a rede de uma organização. A maioria dos designs de segurança locais usa a rede como a tabela dinâmica de segurança primária. Para aplicativos de nuvem, você é mais bem atendido ao considerar a identidade como o perímetro de segurança primário.

Coisas que você pode fazer para desenvolver uma abordagem centrada em identidades para o desenvolvimento de aplicativos Web:

- Impor a autenticação multifator para usuários.
- Use plataformas de autorização e autenticação fortes.
- Aplique o princípio de privilégios mínimos.
- Implemente o acesso just-in-time.

#### <a name="enforce-multi-factor-authentication-for-users"></a>Impor a autenticação multifator para usuários

Use a autenticação de dois fatores. A autenticação de dois fatores é o padrão atual para autenticação e autorização porque evita as fraquezas de segurança inerentes aos tipos de senha e de nome de usuário de autenticação. O acesso às interfaces de gerenciamento do Azure (portal do Azure/PowerShell remoto) e aos serviços voltados para o cliente devem ser projetados e configurados para usar a [autenticação multifator do Azure](../../active-directory/authentication/concept-mfa-howitworks.md).

#### <a name="use-strong-authentication-and-authorization-platforms"></a>Usar plataformas de autenticação e autorização fortes

Use os mecanismos de autenticação e autorização fornecidos pela plataforma em vez de código personalizado. Isso ocorre porque o desenvolvimento de código de autenticação personalizado pode ser propenso a erros. O código comercial (por exemplo, da Microsoft) geralmente é revisado extensivamente pela segurança. [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) é a solução do Azure para gerenciamento de identidades e acesso. Essas ferramentas e serviços do Azure AD ajudam com o desenvolvimento seguro:

- A [plataforma Microsoft Identity](../../active-directory/develop/index.yml) é um conjunto de componentes que os desenvolvedores usam para criar aplicativos que conectam usuários com segurança. A plataforma ajuda os desenvolvedores que estão criando aplicativos de locatário único, linha de negócios (LOB) e desenvolvedores que procuram desenvolver aplicativos multilocatários. Além de entrada básica, os aplicativos criados usando a plataforma de identidade da Microsoft podem chamar APIs e APIs personalizadas da Microsoft. A plataforma de identidade da Microsoft dá suporte a protocolos padrão do setor, como OAuth 2,0 e OpenID Connect.

- [Azure Active Directory B2C (Azure ad B2C)](../../active-directory-b2c/index.yml) é um serviço de gerenciamento de identidade que você pode usar para personalizar e controlar como os clientes se inscrevem, entram e gerenciam seus perfis quando usam seus aplicativos. Isso inclui aplicativos desenvolvidos para iOS, Android e .NET, entre outros. Azure AD B2C habilita essas ações enquanto protege as identidades do cliente.

#### <a name="apply-the-principle-of-least-privilege"></a>Aplicar o princípio de privilégios mínimos

O conceito de [privilégios mínimos](https://en.wikipedia.org/wiki/Principle_of_least_privilege) significa fornecer aos usuários o nível preciso de acesso e controle de que eles precisam para realizar seus trabalhos e nada mais.

Um desenvolvedor de software precisa de direitos de administrador de domínio? Um assistente administrativo precisaria de acesso a controles administrativos em seu computador pessoal? A avaliação do acesso ao software não é diferente. Se você usar o [controle de acesso baseado em função do Azure (RBAC do Azure)](../../role-based-access-control/overview.md) para fornecer aos usuários diferentes habilidades e autoridade em seu aplicativo, não dará a todos acesso a tudo. Limitando o acesso ao que é necessário para cada função, você limita o risco de ocorrer um problema de segurança.

Certifique-se de que seu aplicativo impõe [privilégios mínimos](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications) em seus padrões de acesso.

> [!NOTE]
> As regras de privilégios mínimos precisam ser aplicadas ao software e às pessoas que criam o software. Os desenvolvedores de software podem ser um grande risco para a segurança de ti se receberem muito acesso. As consequências podem ser severas se um desenvolvedor tiver uma intenção mal-intencionada ou receber muito acesso. Recomendamos que as regras de privilégios mínimos sejam aplicadas aos desenvolvedores em todo o ciclo de vida do desenvolvimento.

#### <a name="implement-just-in-time-access"></a>Implementar o acesso just-in-time

Implemente o acesso JIT ( *just-in-time* ) para reduzir ainda mais o tempo de exposição dos privilégios. Use [Azure ad Privileged Identity Management](../../active-directory/roles/security-planning.md#stage-3-take-control-of-admin-activity) para:

- Dê aos usuários as permissões de que eles precisam apenas de JIT.
- Atribua funções por uma duração reduzida com confiança de que os privilégios são revogados automaticamente.

### <a name="require-re-authentication-for-important-transactions"></a>Exigir nova autenticação para transações importantes

A [solicitação entre sites forjada](/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1) (também conhecida como *XSRF* ou *CSRF*) é um ataque contra aplicativos hospedados na Web em que um aplicativo Web mal-intencionado influencia a interação entre um navegador cliente e um aplicativo Web que confia nesse navegador. Os ataques de solicitação entre sites forjado são possíveis porque os navegadores da Web enviam alguns tipos de tokens de autenticação automaticamente com cada solicitação para um site.
Essa forma de exploração também é conhecida como um ataque ou *sessão* de *um clique* , pois o ataque aproveita a sessão autenticada anteriormente do usuário.

A melhor maneira de se defender contra esse tipo de ataque é pedir ao usuário algo que apenas o usuário possa fornecer antes de cada transação importante, como uma compra, desativação de conta ou uma alteração de senha. Você pode pedir ao usuário para digitar novamente sua senha, concluir um captcha ou enviar um token secreto que apenas o usuário teria. A abordagem mais comum é o token secreto.

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>Use uma solução de gerenciamento de chaves para proteger chaves, credenciais e outros segredos

Perder chaves e credenciais é um problema comum. A única coisa pior do que perder as chaves e as credenciais é quando um terceiro não autorizado tem acesso a elas. Os invasores podem aproveitar as técnicas automatizadas e manuais para encontrar chaves e segredos armazenados em repositórios de código como o GitHub. Não coloque chaves e segredos nesses repositórios de código públicos ou em qualquer outro servidor.

Sempre coloque suas chaves, certificados, segredos e cadeias de conexão em uma solução de gerenciamento de chaves. Você pode usar uma solução centralizada na qual chaves e segredos são armazenados em HSMs (módulos de segurança de hardware). O Azure fornece um HSM na nuvem com [Azure Key Vault](../../key-vault/general/overview.md).

Key Vault é um *repositório secreto*: é um serviço de nuvem centralizado para armazenar os segredos do aplicativo. Key Vault mantém seus dados confidenciais protegidos, mantendo os segredos do aplicativo em um único local central e fornecendo acesso seguro, controle de permissões e registro em log de acesso.

Os segredos são armazenados em *cofres* individuais. Cada cofre tem suas próprias políticas de configuração e segurança para controlar o acesso. Você obtém seus dados por meio de uma API REST ou por meio de um SDK de cliente que está disponível para a maioria das linguagens de programação.

> [!IMPORTANT]
> O Azure Key Vault foi projetado para armazenar segredos de configuração para aplicativos de servidor. Ele não se destina ao armazenamento de dados que pertencem aos usuários do aplicativo. Isso é refletido em suas características de desempenho, na API e no modelo de custo.
>
> Os dados do usuário devem ser armazenados em outro lugar, como em uma instância do banco do dados SQL do Azure com Transparent Data Encryption (TDE) ou em uma conta de armazenamento que usa o Criptografia do Serviço de Armazenamento do Azure. Os segredos que são usados pelo seu aplicativo para acessar esses armazenamentos de dados podem ser mantidos em Azure Key Vault.

### <a name="protect-sensitive-data"></a>Proteger dados confidenciais

Proteger os dados é uma parte essencial da sua estratégia de segurança.
Classificar seus dados e identificar suas necessidades de proteção de dados ajuda a projetar seu aplicativo com a segurança de dados em mente. Classificar (categorizar) dados armazenados por sensibilidade e impacto nos negócios ajuda os desenvolvedores a determinar os riscos associados aos dados.

Rotule todos os dados aplicáveis como confidenciais ao projetar seus formatos de dados. Certifique-se de que o aplicativo trate os dados aplicáveis como confidenciais. Essas práticas podem ajudá-lo a proteger seus dados confidenciais:

- Usar criptografia.
- Evite segredos de codificação rígida como chaves e senhas.
- Verifique se os controles de acesso e a auditoria estão em vigor.

#### <a name="use-encryption"></a>Usar criptografia

Proteger os dados deve ser uma parte essencial da sua estratégia de segurança.
Se os dados estiverem armazenados em um banco de dado ou se forem movidos entre locais, use a criptografia de [dados em repouso](../fundamentals/encryption-atrest.md) (enquanto no banco de dados) e a criptografia de [dado em trânsito](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit) (de seu caminho para e para o usuário, o Database, uma API ou um ponto de extremidade de serviço). Recomendamos que você sempre use protocolos SSL/TLS para trocar dados. Certifique-se de usar a versão mais recente do TLS para criptografia (atualmente, esta é a versão 1,2).

#### <a name="avoid-hard-coding"></a>Evitar codificação de hardware

Algumas coisas nunca devem ser embutidas em código em seu software. Alguns exemplos são nomes de host ou endereços IP, URLs, endereços de email, nomes de acessações, senhas, chaves de conta de armazenamento e outras chaves de criptografia. Considere a implementação de requisitos em relação ao que pode ou não ser embutido em código em seu código, incluindo nas seções de comentário do seu código.

Quando você coloca comentários em seu código, certifique-se de não salvar nenhuma informação confidencial. Isso inclui seu endereço de email, senhas, cadeias de conexão, informações sobre seu aplicativo que só seriam conhecidas por alguém em sua organização e qualquer outra coisa que possa dar a um invasor uma vantagem em atacar seu aplicativo ou organização.

Basicamente, suponha que tudo em seu projeto de desenvolvimento será um conhecimento público quando for implantado. Evite incluir dados confidenciais de qualquer tipo no projeto.

Discutimos anteriormente [Azure Key Vault](../../key-vault/general/overview.md). Você pode usar Key Vault para armazenar segredos como chaves e senhas em vez de codificá-los embutidamente. Quando você usa Key Vault em combinação com identidades gerenciadas para recursos do Azure, seu aplicativo Web do Azure pode acessar valores de configuração secretas com facilidade e segurança sem armazenar segredos no controle do código-fonte ou na configuração. Para saber mais, confira [gerenciar segredos em seus aplicativos de servidor com o Azure Key Vault](/learn/modules/manage-secrets-with-azure-key-vault/).

### <a name="implement-fail-safe-measures"></a>Implementar medidas de falha segura

Seu aplicativo deve ser capaz de lidar com [erros](/dotnet/standard/exceptions/) que ocorrem durante a execução de maneira consistente. O aplicativo deve capturar todos os erros e falhar ou ser fechado.

Você também deve garantir que os erros sejam registrados com contexto de usuário suficiente para identificar atividades suspeitas ou mal-intencionadas. Os logs devem ser retidos por um tempo suficiente para permitir uma análise forense atrasada. Os logs devem estar em um formato que possa ser facilmente consumido por uma solução de gerenciamento de log. Certifique-se de que os alertas de erros relacionados à segurança sejam disparados. O registro em log e o monitoramento insuficiente permitem que os invasores ataquem mais sistemas e mantenham a persistência.

### <a name="take-advantage-of-error-and-exception-handling"></a>Aproveite o tratamento de erros e exceções

Implementar o tratamento de erros e [exceções](/dotnet/standard/exceptions/best-practices-for-exceptions) corretos é uma parte importante da codificação defensiva. A manipulação de erros e exceções é essencial para tornar um sistema confiável e seguro. Erros na manipulação de erros podem levar a tipos diferentes de vulnerabilidades de segurança, como vazamento de informações para invasores e ajuda de invasores a entender mais sobre sua plataforma e design.

Verifique se:

- Você manipula exceções de maneira centralizada para evitar [blocos try/catch](/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions) duplicados no código.

- Todos os comportamentos inesperados são tratados dentro do aplicativo.

- As mensagens exibidas aos usuários não vazam dados críticos, mas fornecem informações suficientes para explicar o problema.

- As exceções são registradas e fornecem informações suficientes para que as equipes forenses ou de resposta a incidentes investiguem.

O [aplicativo lógico do Azure](../../logic-apps/logic-apps-overview.md) fornece uma experiência de primeira classe para [lidar com erros e exceções](../../logic-apps/logic-apps-exception-handling.md) causadas por sistemas dependentes. Você pode usar aplicativos lógicos para criar fluxos de trabalho para automatizar tarefas e processos que integram aplicativos, dados, sistemas e serviços entre empresas e organizações.

### <a name="use-logging-and-alerting"></a>Usar registro em log e alertas

[Registre](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1) seus problemas de segurança para investigações de segurança e acione alertas sobre problemas para garantir que as pessoas saibam sobre problemas em tempo hábil. Habilite a auditoria e log em todos os componentes. Os logs de auditoria devem capturar o contexto do usuário e identificar todos os eventos importantes.

Verifique se você não registra dados confidenciais que um usuário envia para seu site. Exemplos de dados confidenciais:

- Credenciais de usuário
- Números de seguro social ou outras informações de identificação
- Números de cartão de crédito ou outras informações financeiras
- Informações de integridade
- Chaves privadas ou outros dados que podem ser usados para descriptografar informações criptografadas
- Informações do sistema ou aplicativo que podem ser usadas para atacar com mais eficiência o aplicativo

Certifique-se de que o aplicativo monitore eventos de gerenciamento de usuários, como logons de usuário bem-sucedidos e com falha, redefinições de senha, alterações de senha, bloqueio de conta e registro de usuário. O registro desses eventos ajuda você a detectar e reagir a comportamentos potencialmente suspeitos. Ele também permite que você Reúna dados de operações, como quem está acessando o aplicativo.

## <a name="next-steps"></a>Próximas etapas
Nos artigos a seguir, recomendamos os controles de segurança e as atividades que podem ajudá-lo a desenvolver e implantar aplicativos seguros.

- [Desenvolver aplicativos e segurança](secure-develop.md)
- [Implantar aplicativos seguros](secure-deploy.md)