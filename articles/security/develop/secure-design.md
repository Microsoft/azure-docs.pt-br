---
title: Projetar aplicativos seguros no Microsoft Azure
description: Este artigo discute as práticas recomendadas a serem considerados durante as fases de design e o requisito do seu projeto de aplicativo web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 12b9793cabb261368c437bd2ae2dbb39cf078bef
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653278"
---
# <a name="design-secure-applications-on-azure"></a>Design de aplicativos seguros no Azure
Neste artigo, apresentamos as atividades de segurança e controles a serem considerados ao projetar aplicativos para a nuvem. Treinamento de recursos junto com perguntas de segurança e conceitos para considerar durante os requisitos e design fases da Microsoft [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) são abordados. O objetivo é ajudá-lo a definir as atividades e serviços do Azure que você pode usar para criar um aplicativo mais seguro.

As seguintes fases SDL são abordadas neste artigo:

- Treinamento
- Requisitos
- Design

## <a name="training"></a>Treinamento
Antes de começar a desenvolver seu aplicativo de nuvem, levar tempo para compreender a segurança e privacidade no Azure. Ao realizar esta etapa, você pode reduzir o número e a gravidade das vulnerabilidades exploráveis em seu aplicativo. Você estará mais preparado para responder de forma adequada para o panorama de ameaças mutável.

Use os seguintes recursos durante a fase de treinamento para se familiarizar com os serviços do Azure que estão disponíveis para desenvolvedores e com as práticas recomendadas de segurança no Azure:

  - [Guia do desenvolvedor para o Azure](https://azure.microsoft.com/campaigns/developer-guide/) mostra como começar a usar com o Azure. O guia mostra quais serviços você pode usar para executar seus aplicativos, armazene seus dados, incorporar inteligência, criar aplicativos de IoT e implantar suas soluções de forma mais eficiente e segura.

  - [Guia de Introdução para desenvolvedores do Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide) fornece informações essenciais para os desenvolvedores que desejam para começar a usar a plataforma do Azure para as necessidades de seu desenvolvimento.

  - [SDKs e ferramentas](https://docs.microsoft.com/azure/index#pivot=sdkstools) descreve as ferramentas que estão disponíveis no Azure.

  - [Os serviços do Azure DevOps](https://docs.microsoft.com/azure/devops/) fornece ferramentas de colaboração de desenvolvimento. As ferramentas incluem os pipelines de alto desempenho, repositórios Git gratuitos, quadros Kanban configuráveis e teste de carga abrangente de automatizados e baseados em nuvem.
    O [Central de recursos de DevOps](https://docs.microsoft.com/azure/devops/learn/) combina nossos recursos de aprendizagem DevOps práticas, Git controle de versão, agile métodos, como trabalhamos com DevOps na Microsoft e como você pode avaliar sua própria progressão de DevOps.

  - [Top 5 itens de segurança a serem considerados antes de enviar por push para produção](https://docs.microsoft.com/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca) mostra a você como ajudar a proteger seus aplicativos web no Azure e os seus aplicativos contra os ataques mais comuns e perigoso de aplicativo web.

  - [Proteger o Kit de DevOps do Azure](https://azsk.azurewebsites.net/index.html) é uma coleção de scripts, ferramentas, extensões e automações que atende às abrangente do Azure recursos e assinatura segurança necessidades das equipes de DevOps que usam automação abrangente. O Kit de DevOps seguro para o Azure pode mostrar como integrar perfeitamente a segurança em seus fluxos de trabalho de DevOps nativos. O kit aborda ferramentas como testes de verificação de segurança (SVTs), que podem ajudar os desenvolvedores a escrever código seguro e testam a configuração de segurança de seus aplicativos em nuvem nos estágios de desenvolvimento inicial e de codificação.

  - [Práticas recomendadas de segurança para soluções do Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions) fornece uma coleção de segurança de práticas recomendadas para usar como projetar, implantar e gerenciar suas soluções de nuvem usando o Azure.

## <a name="requirements"></a>Requisitos
A fase de definição de requisitos é uma etapa crucial na definição de seu aplicativo é e o que ele fará quando ele é lançado. A fase de requisitos também é um tempo para pensar sobre os controles de segurança que você criará em seu aplicativo. Durante essa fase, você também começar as etapas que você tomará em todo o SDL para garantir que você libera e implanta um aplicativo seguro.

### <a name="consider-security-and-privacy-issues"></a>Considere as questões de segurança e privacidade
Essa fase é o melhor momento para considerar os problemas de privacidade e segurança de base. Definir níveis aceitáveis de segurança e privacidade no início de um projeto, uma equipe ajuda a:

- Entenda os riscos associados com problemas de segurança.
- Identifique e corrija bugs de segurança durante o desenvolvimento.
- Aplica estabelecidos níveis de segurança e privacidade em todo o projeto.

Quando você escreve os requisitos para o seu aplicativo, certifique-se de considerar os controles de segurança que podem ajudar a manter seu aplicativo e dados seguros.

### <a name="ask-security-questions"></a>Faça perguntas de segurança
Faça perguntas de segurança, como:

  - Meu aplicativo contêm dados confidenciais?

  - O meu aplicativo coletar ou armazenar dados que requer que eu aderir aos padrões do setor e programas de conformidade, como o [Federal financeiros instituição exame Council FFIEC ()](https://docs.microsoft.com/azure/security/blueprints/ffiec-analytics-overview) ou o [Payment Card Industry Data Security Standards (PCI DSS)](https://docs.microsoft.com/azure/security/blueprints/pcidss-analytics-overview)?

  - O meu aplicativo coletar ou contêm dados confidenciais de pessoal ou de cliente que podem ser usados, por conta própria ou com outras informações para identificar, contatar ou localize uma única pessoa?

  - Meu aplicativo coletar ou conter dados que podem ser usados para acessar o médico de um indivíduo, educacional, financeiro, ou informações de emprego? Identificando a confidencialidade dos seus dados durante a fase de requisitos ajuda a classificar seus dados e identificar o método de proteção de dados que você usará para seu aplicativo.

  - Onde e como Meus dados são armazenados? Considere como você vai monitorar os serviços de armazenamento que o aplicativo usa para alterações inesperadas (como tempos de resposta mais lentas). Você será capaz de influenciar o registro em log para coletar dados mais detalhados e analisar um problema em camadas?

  - Meu aplicativo estaria disponível ao público (na internet) ou apenas internamente? Se seu aplicativo está disponível ao público, como você protege os dados que podem ser coletados de que está sendo usado da maneira errada? Se seu aplicativo só está disponível internamente, considere quem em sua organização deve ter acesso ao aplicativo e por quanto tempo eles devem ter acesso.

  - Você compreende o modelo de identidade antes de começar a projetar seu aplicativo? Como será possível determinar que os usuários são quem dizem ser e que o usuário está autorizado a fazer?

  - Meu aplicativo realiza tarefas importantes ou confidenciais (por exemplo, transferência de dinheiro, desbloquear portas ou fornecimento Medicina)?
    Considere como você validará que o usuário que está executando uma tarefa confidencial está autorizado a executar a tarefa e como você fará a autenticação se a pessoa é quem diz ser. A autorização (aram) é o ato de conceder uma permissão de entidade de segurança autenticada para fazer algo. A autenticação (AuthN) é o ato de desafiar uma parte para credenciais legítimas.

  - Meu aplicativo realiza todas as atividades de risco de software, como permitir que os usuários carregar ou baixar arquivos ou outros dados? Se seu aplicativo executar as atividades arriscadas, considere como seu aplicativo protegerá os usuários de manipulação de dados ou arquivos mal-intencionados.

### <a name="review-owasp-top-10"></a>10 principais OWASP de revisão
Considere a possibilidade de revisar as [ <span class="underline">riscos de segurança de aplicativo do OWASP Top 10</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).
OWASP Top 10 endereços riscos de segurança críticos para aplicativos da web.
Reconhecimento desses riscos de segurança pode ajudá-lo a tomar decisões de design e o requisito que minimizam esses riscos em seu aplicativo.

Pensar sobre controles de segurança para impedir violações é importante.
No entanto, você também queira [supor uma violação](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/security-in-devops) ocorrerá. Supondo que uma violação ajuda a responder algumas perguntas importantes sobre a segurança com antecedência, para que eles não precisam ser respondidas em caso de emergência:

  - Como detectará um ataque?

  - O que eu fará se não houver um ataque ou violação?

  - Como eu vou recuperar do ataque, como vazamento ou violação de dados?

## <a name="design"></a>Design

A fase de design é fundamental para o estabelecimento de práticas recomendadas para design e as especificações funcionais. Também é essencial para realizar análises de riscos que ajuda a atenuar os problemas de segurança e privacidade em todo o projeto.

Quando você tem requisitos de segurança em vigor e usa os conceitos de design seguro, você pode evitar ou minimizar as oportunidades para uma falha de segurança. Uma falha de segurança é uma falha na criação do aplicativo que pode permitir que um usuário executar ações mal-intencionadas ou inesperadas após o lançamento de seu aplicativo.

Durante a fase de design, também pensar sobre como você pode aplicar a segurança em camadas; um nível de proteção não é necessariamente o suficiente. O que acontece se um invasor obtém após seu firewall do aplicativo web (WAF)? Você deseja que outro controle de segurança em vigor para proteger contra esse ataque.

Com isso em mente, vamos abordar os seguintes conceitos de design seguro e os controles de segurança, que você deve resolver ao projetar aplicativos seguros:

- Use uma biblioteca de codificação segura e uma estrutura de software.
- Verificar se há componentes vulneráveis.
- Use a modelagem de ameaças durante o design do aplicativo.
- Reduza a superfície de ataque.
- Adote uma política de identidade como o perímetro de segurança primário.
- Exigir a reautenticação para transações importantes.
- Use uma solução de gerenciamento de chaves para proteger as chaves, credenciais e outros segredos.
- Proteger dados confidenciais.
- Implementar medidas à prova de falhas.
- Tirar proveito do erro e tratamento de exceção.
- Use alertas e logs.

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>Usar uma biblioteca de codificação segura e uma estrutura de software

Para o desenvolvimento, use uma biblioteca de codificação segura e uma estrutura de software que inseriu a segurança. Os desenvolvedores podem usar uma existente, comprovada de recursos (criptografia, saneamento de entrada, codificação de saída, chaves ou cadeias de caracteres de conexão e qualquer outra coisa que seria considerado um controle de segurança) em vez de desenvolver controles de segurança a partir do zero. Isso ajuda a proteger contra falhas de implementação e de design relacionados à segurança.

Certifique-se de que você está usando a versão mais recente da sua estrutura e todos os recursos de segurança que estão disponíveis no framework. A Microsoft oferece um abrangente [conjunto de ferramentas de desenvolvimento](https://azure.microsoft.com/product-categories/developer-tools/) para todos os desenvolvedores, trabalhando em qualquer plataforma ou linguagem, entregue aplicativos de nuvem. Você pode codificar com a linguagem de sua escolha, escolhendo de várias [SDKs](https://azure.microsoft.com/downloads/).
Você pode tirar proveito dos ambientes completos de desenvolvimento integrado (IDEs) e editores que têm recursos avançados de depuração e suporte interno do Azure.

A Microsoft oferece uma variedade de [linguagens, estruturas e ferramentas](https://docs.microsoft.com/azure/index#pivot=sdkstools&panel=sdkstools-all) que você pode usar para desenvolver aplicativos no Azure. Um exemplo é [do Azure para desenvolvedores de .NET e .NET Core](https://docs.microsoft.com/dotnet/azure/). Para cada idioma e a estrutura que oferecemos, você encontrará guias de início rápido, tutoriais e as referências de API para ajudá-lo a começar rapidamente.

O Azure oferece uma variedade de serviços que você pode usar para hospedar aplicativos web e sites. Esses serviços permitem que você desenvolva em sua linguagem favorita, seja .NET, .NET Core, Java, Ruby, Node. js, PHP ou Python.
[Aplicativos de Web do serviço de aplicativo do Azure](https://docs.microsoft.com/azure/app-service/app-service-web-overview) (aplicativos Web) é um desses serviços.

Os aplicativos Web adiciona o poder do Microsoft Azure para seu aplicativo. Ele inclui segurança, balanceamento de carga, dimensionamento automático e gerenciamento automatizado. Você pode também aproveitar recursos de DevOps em aplicativos Web, como o pacote de gerenciamento, implantação contínua de DevOps do Azure, GitHub, Hub do Docker e outras fontes, domínios personalizados, certificados SSL/TLS e ambientes de preparo.

O Azure oferece outros serviços que você pode usar para hospedar aplicativos web e sites. Os Aplicativos Web são a melhor opção para a maioria dos cenários. Para uma arquitetura de microsserviço, considere [do Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric).
Se você precisar de mais controle sobre as VMs nas quais seu código é executado, considere as [Máquinas Virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/).
Para obter mais informações sobre como escolher entre esses serviços do Azure, consulte um [comparação dos serviços de nuvem, máquinas virtuais, Service Fabric e serviço de aplicativo do Azure](https://docs.microsoft.com/azure/app-service/choose-web-site-cloud-service-vm).

### <a name="apply-updates-to-components"></a>Aplicar atualizações aos componentes

Para evitar vulnerabilidades, você deve continuamente inventariar os componentes do lado do cliente e lado do servidor (por exemplo, estruturas e bibliotecas) e suas dependências para atualizações. Versões de software atualizado e novas vulnerabilidades são liberadas continuamente. Certifique-se de que você tiver um plano em andamento para monitorar, realizar triagem e aplicar atualizações ou alterações na configuração de bibliotecas e componentes que você usar.

Consulte a [Open Web Application Security OWASP (projeto)](https://www.owasp.org/index.php/Main_Page) página [usando componentes com vulnerabilidades conhecidas](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities) para sugestões de ferramenta. Você também pode assinar alertas de email para vulnerabilidades de segurança que estão relacionados a componentes que você usar.

### <a name="use-threat-modeling-during-application-design"></a>Usar a modelagem de ameaças durante o design do aplicativo

A modelagem de ameaças é o processo de identificação de possíveis ameaças de segurança para seus negócios e o aplicativo e, em seguida, garantindo que as atenuações apropriadas estejam em vigor. O SDL Especifica que as equipes devem participar de modelagem de ameaças durante a fase de design, quando a resolução de problemas potenciais é relativamente fácil e econômica. Usando modelagem na fase de design pode reduzir significativamente o custo total de desenvolvimento de ameaça.

Para ajudar a facilitar a processo de modelagem de ameaças, projetamos o [ferramenta de modelagem de ameaças do SDL](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) com especialistas de não-segurança em mente. Essa ferramenta facilita a modelagem de ameaças para todos os desenvolvedores fornecendo orientação clara sobre como criar e analisar os modelos de ameaças.

Modelando o design do aplicativo e enumerando [STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) ameaças — falsificação, violação, repúdio, divulgação de informações confidenciais, negação de serviço e elevação de privilégio — em relação de confiança de todos os limites provou ser uma maneira eficiente para capturar erros de design desde o início. A tabela a seguir lista as ameaças STRIDE e fornece algumas mitigações de exemplo que usam os recursos fornecidos pelo Azure. Essas mitigações não funcionarão em todas as situações.

| Ameaça | Propriedade de segurança | Potencial redução de plataforma do Azure |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Falsificação               | Autenticação        | [Exigir conexões HTTPS](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.1&tabs=visual-studio). |
| Violação              | Integridade             | Valide os certificados SSL/TLS. Aplicativos que usam SSL/TLS devem verificar os certificados X.509 das entidades que eles se conectem. Usar certificados de Cofre de chaves do Azure para [gerenciar seu x509 certificados](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates). |
| Repúdio            | Não repúdio       | Habilitar o [monitoramento e diagnóstico](https://docs.microsoft.com/azure/architecture/best-practices/monitoring) do Azure.|
| Revelação de informações | Confidencialidade       | Criptografar dados confidenciais [em repouso](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest) e [em trânsito](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices#protect-data-in-transit). |
| Negação de Serviço      | Disponibilidade          | Monitorar as métricas de desempenho para possíveis condições de negação de serviço. Implementar filtros de conexão. [A proteção contra DDoS do Azure](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview#next-steps), combinadas com as práticas recomendadas de design de aplicativo, que fornece proteção contra ataques de DDoS.|
| Elevação de privilégio | Autorização         | Usar o Azure Active Directory <span class="underline"> </span> [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure).|

### <a name="reduce-your-attack-surface"></a>Reduzir a superfície de ataque

Uma superfície de ataque é o total de soma de onde as vulnerabilidades potenciais podem ocorrer. Neste artigo, vamos nos concentrar na superfície de ataque do aplicativo.
O foco está em um aplicativo a proteção contra ataques. É uma maneira simples e rápida para minimizar a superfície de ataque remover recursos não utilizados e código de seu aplicativo. O menor seu aplicativo, menor a superfície de ataque. Por exemplo, remova:

- Código para os recursos que você ainda não lançado ainda.
- Depurando código de suporte.
- Interfaces de rede e protocolos que não são usados ou que foram preterido.
- Máquinas virtuais e outros recursos que você não estiver usando.

Fazendo a limpeza regular de seus recursos e garantir que você remova o código não utilizado são excelentes maneiras de garantir que haja menos oportunidades de atores mal-intencionados atacar.

É uma maneira mais detalhada e detalhada para reduzir a superfície de ataque concluir uma análise da superfície de ataque. Uma análise da superfície de ataque ajuda a mapear as partes de um sistema que precisa ser revisado e testado para vulnerabilidades de segurança.

A finalidade de uma análise da superfície de ataque é compreender as áreas de risco em um aplicativo para que os desenvolvedores e especialistas em segurança estejam cientes das quais partes do aplicativo estão abertos a ataques. Em seguida, você pode encontrar maneiras de minimizar esta faixa potencial, quando e como a superfície de ataque é alterado, e o que isso significa que de uma perspectiva de risco.

Uma análise da superfície de ataque ajuda você a identificar:

- Partes do sistema e funções que você precisa examinar e testar vulnerabilidades de segurança.
- Áreas de alto risco de código que exigem proteção de defesa em profundidade (partes do sistema que você precisa para se defender).
- Quando você altera a superfície de ataque e precise atualizar uma avaliação de ameaças.

Diminuindo as oportunidades dos invasores explorar um potencial ponto fraco ou vulnerabilidade exige que você analisar detalhadamente a superfície geral de ataques do seu aplicativo. Ele também inclui desabilitando ou restringindo o acesso aos serviços do sistema, aplicando o princípio de privilégio mínimo, e empregar defesas em camadas sempre que possível.

Discutiremos [conduzir uma revisão da superfície de ataque](secure-develop.md#conduct-attack-surface-review) durante a fase de verificação do SDL.

> [!NOTE]
> **O que é a diferença entre a modelagem de ameaças e análise da superfície de ataque?**
A modelagem de ameaças é o processo de identificar possíveis ameaças de segurança para seu aplicativo e garantir que as atenuações apropriadas contra as ameaças estão em vigor. Análise da superfície de ataque identifica áreas de alto risco de código que estão abertas a ataques. Ela envolve encontrar maneiras de se defender de áreas de alto risco do seu aplicativo e a revisão e teste essas áreas de código antes de implantar o aplicativo.

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Adotar uma política de identidade como o perímetro de segurança primário

Ao projetar aplicativos de nuvem, é importante expandir o foco do perímetro de segurança de uma abordagem centrada em rede para uma abordagem centrada em identidade. Historicamente, o perímetro de segurança local primário foi a rede da organização. A maioria dos projetos de segurança local usam a rede como o pivô de segurança primário. Para aplicativos em nuvem, você é melhor atendido considerando a identidade como o perímetro de segurança primário.

Coisas que você pode fazer para desenvolver uma abordagem centrada em identidade para o desenvolvimento de aplicativos da web:

- Impor a autenticação multifator para usuários.
- Use plataformas de autorização e autenticação fortes.
- Aplique o princípio de privilégios mínimos.
- Implementar o acesso just-in-time.

#### <a name="enforce-multi-factor-authentication-for-users"></a>Impor a autenticação multifator para usuários

Use a autenticação de dois fatores. Autenticação de dois fatores é o padrão atual para autenticação e autorização porque evita as vulnerabilidades de segurança inerentes em tipos de nome de usuário e senha de autenticação. Acesso às interfaces de gerenciamento do Azure (Azure portal/PowerShell remoto) e aos serviços voltados para o cliente deve ser criado e configurado para usar [autenticação multifator do Azure](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication).

#### <a name="use-strong-authentication-and-authorization-platforms"></a>Usar as plataformas forte de autenticação e autorização

Use os mecanismos de autenticação e autorização fornecidos pela plataforma em vez de código personalizado. Isso ocorre porque o desenvolvimento de código de autenticação personalizado pode ser propenso a erros. O código comercial (por exemplo, da Microsoft) frequentemente é extensivamente revisado quanto à segurança. [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) é a solução do Azure para gerenciamento de identidades e acesso. Esses serviços e ferramentas do AD do Azure ajudam com o desenvolvimento seguro:

- [Azure plataforma de identidade do Active Directory (AD do Azure para desenvolvedores)](https://docs.microsoft.com/azure/active-directory/develop/about-microsoft-identity-platform) é um serviço de identidade de nuvem que os desenvolvedores usam para criar aplicativos que se conectar com segurança os usuários. AD do Azure ajuda os desenvolvedores que estão criando aplicativos (LOB) de locatário único, a linha de negócios e os desenvolvedores que desejam para desenvolver aplicativos de multilocatário. Além do básico ao entrar, os aplicativos que são criados usando o Azure AD podem chamar APIs da Microsoft e APIs personalizadas que são criadas na plataforma do Azure AD. Plataforma de identidade do Azure AD dá suporte a protocolos padrão do setor, como OAuth 2.0 e OpenID Connect.

- [Azure B2C de diretório Active Directory (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/) é um serviço de gerenciamento de identidade que você pode usar para personalizar e controlar como os clientes se inscrever, entrar e gerenciam seus perfis ao usarem seus aplicativos. Isso inclui aplicativos que são desenvolvidos para iOS, Android e .NET, entre outros. B2C do AD do Azure habilita essas ações enquanto protege as identidades dos usuários.

#### <a name="apply-the-principle-of-least-privilege"></a>Aplique o princípio de privilégio mínimo

O conceito de [privilégio mínimo](https://en.wikipedia.org/wiki/Principle_of_least_privilege) significa fornecendo aos usuários o nível exato de acesso e controle que precisam para realizar seus trabalhos e nada mais.

Seria um desenvolvedor de software precisa de direitos de administrador de domínio? Um assistente administrativo precisa de acesso para controles administrativos em seus computadores pessoais? Acesso ao software de avaliação não é diferente. Se você usar [controle de acesso baseado em função (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) para fornecer capacidades de diferentes usuários e autoridade em seu aplicativo, você não daria todos acesso a tudo. Limitando o acesso para o que é necessário para cada função, você deve limitar o risco de ocorrer um problema de segurança.

Certifique-se de que seu aplicativo impõe [privilégio mínimo](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications) ao longo de seus padrões de acesso.

> [!NOTE]
>  As regras de privilégio mínimo necessário se aplicam ao software e para as pessoas a criação de software. Os desenvolvedores de software podem ser um grande risco à segurança de TI se eles recebem muito mais acesso. As consequências podem ser graves, se um desenvolvedor tem más intenções ou recebe muito mais acesso. É recomendável que as regras de privilégio mínimo ser aplicadas aos desenvolvedores em todo o ciclo de vida de desenvolvimento.

#### <a name="implement-just-in-time-access"></a>Implementar o acesso just-in-time

Implemente *just-in-time* acesso JIT () para reduzir ainda mais o tempo de exposição de privilégios. Use [do Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#stage-3-build-visibility-and-take-full-control-of-admin-activity) para:

- Dar aos usuários as permissões necessárias apenas JIT.
- Atribua funções por uma duração reduzida com confiança de que os privilégios são revogados automaticamente.

### <a name="require-re-authentication-for-important-transactions"></a>Exigir a reautenticação para transações importantes

[Falsificação de solicitação intersite forjada](https://docs.microsoft.com/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1) (também conhecido como *XSRF* ou *CSRF*) é um ataque contra aplicativos hospedados na web em que um aplicativo da web mal-intencionadas influencia a interação entre um navegador cliente e uma web aplicativo de confiança do navegador. Ataques de falsificação de solicitação intersite forjada são possíveis, pois navegadores da web enviam alguns tipos de tokens de autenticação automaticamente com cada solicitação de um site.
Essa forma de exploração é também conhecido como um *ataque de um clique* ou *sequestro de sessão* porque o ataque aproveita o usuário do autenticado anteriormente sessão.

A melhor maneira de se defender contra esse tipo de ataque é pedir ao usuário algo que apenas o usuário pode fornecer antes de cada transação importantes, como uma compra, a desativação de conta ou uma alteração de senha. Você pode solicitar que o usuário digitar novamente sua senha, conclua um captcha ou enviar um token de segredo que somente o usuário teria. A abordagem mais comum é o token secreto.

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>Usar uma solução de gerenciamento de chaves para proteger as chaves, credenciais e outros segredos

Perder chaves e credenciais é um problema comum. A única coisa pior do que perder as chaves e as credenciais é quando um terceiro não autorizado tem acesso a elas. Os invasores podem tirar proveito das técnicas manuais e automatizados para localizar as chaves e segredos são armazenados em repositórios de código como o GitHub. Não coloque chaves e segredos nesses repositórios de código público ou em qualquer outro servidor.

Sempre coloque suas chaves, certificados, segredos e cadeias de caracteres de conexão em uma solução de gerenciamento de chaves. Você pode usar uma solução centralizada em que as chaves e segredos são armazenados em módulos de segurança de hardware (HSMs). O Azure oferece um HSM na nuvem com o [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis).

O Key Vault é um *repositório secreto*: é um serviço de nuvem centralizado para armazenar segredos do aplicativo. O Cofre de chaves mantém seus dados confidenciais protegidos manter os segredos do aplicativo em um único local central e fornecendo acesso seguro, o controle de permissões e o log de acesso.

Segredos são armazenados em um indivíduo *cofres*. Cada cofre tem sua própria configuração e políticas de segurança para controlar o acesso. Para acessar seus dados por meio de uma API REST ou um SDK está disponível para a maioria das linguagens de programação de cliente.

> [!IMPORTANT]
> O Azure Key Vault foi projetado para armazenar segredos de configuração para aplicativos de servidor. Ele não se destina para armazenar dados que pertencem a usuários do aplicativo. Isso é refletido em suas características de desempenho, a API e o modelo de custo.
>
> Dados de usuário devem ser armazenados em outro lugar, como em uma instância de banco de dados SQL que tem criptografia de dados transparente (TDE) ou em uma conta de armazenamento que usa a criptografia do serviço de armazenamento do Azure. Segredos são usados pelo seu aplicativo para acessar esses armazenamentos de dados podem ser mantidos no Azure Key Vault.

### <a name="protect-sensitive-data"></a>Proteger dados confidenciais

Proteção dos dados é uma parte essencial de sua estratégia de segurança.
Classificar seus dados e identificar a proteção de dados precisam de ajuda a projetar seu aplicativo com base na segurança de dados. Classificação (categorizando) dados armazenados por sensibilidade e impacto nos negócios ajuda os desenvolvedores a determinar os riscos associados a dados.

Rotule todos os dados aplicáveis como confidenciais quando você projeta seus formatos de dados. Certifique-se de que o aplicativo trata os dados aplicáveis como confidenciais. Essas práticas podem ajudar você a proteger seus dados confidenciais:

- Use a criptografia.
- Evite embutir segredos, como senhas e chaves.
- Certifique-se de que os controles de acesso e auditoria estão em vigor.

#### <a name="use-encryption"></a>Usar a criptografia

Proteção de dados deve ser uma parte essencial de sua estratégia de segurança.
Se seus dados são armazenados em um banco de dados, ou se move para frente e para trás entre locais, usar a criptografia de [dados em repouso](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest) (enquanto estiver no banco de dados) e a criptografia de [dados em trânsito](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices#protect-data-in-transit) (em seu caminho de e para o usuário, o banco de dados, uma API ou ponto de extremidade de serviço). É recomendável que você sempre use protocolos SSL/TLS para trocar dados. Certifique-se de que você use a versão mais recente do TLS para criptografia (atualmente, essa é a versão 1.2).

#### <a name="avoid-hard-coding"></a>Evitar a codificação

Algumas coisas nunca devem ser embutido em código no seu software. Alguns exemplos são os nomes de host ou IP endereços, URLs, endereços de email, nomes de usuário, senhas, chaves de conta de armazenamento e outras chaves de criptografia. Considere a implementação de requisitos para o que pode ou não pode ser embutido em código no seu código, incluindo nas seções de comentário do código.

Ao colocar comentários em seu código, certifique-se de que você não salve nenhuma informação confidencial. Isso inclui o endereço de email, senhas, cadeias de caracteres de conexão, informações sobre seu aplicativo deve ser conhecida apenas por alguém em sua organização e qualquer outra coisa que pode fornecer ao invasor uma vantagem atacar seu aplicativo ou organização .

Basicamente, supor que tudo no seu projeto de desenvolvimento será conhecimento público quando ele é implantado. Evite incluir dados confidenciais de qualquer tipo no projeto.

Anteriormente, discutimos [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis). Você pode usar o Cofre de chaves para armazenar segredos, como chaves e senhas, em vez de codificá-los. Quando você usa o Cofre de chaves em combinação com identidades gerenciadas para recursos do Azure, seu aplicativo web do Azure pode acessar valores de configuração secreta com facilidade e segurança sem armazenar os segredos em sua configuração ou o controle de origem. Para obter mais informações, consulte [gerenciar segredos em seus aplicativos de servidor com o Azure Key Vault](https://docs.microsoft.com/learn/modules/manage-secrets-with-azure-key-vault/).

### <a name="implement-fail-safe-measures"></a>Implementar medidas à prova de falhas

Seu aplicativo deve ser capaz de manipular [erros](https://docs.microsoft.com/dotnet/standard/exceptions/) que ocorrem durante a execução de uma maneira consistente. O aplicativo deve capturar todos os erros e a falha seguro ou fechado.

Você também deve garantir que os erros são registrados com o contexto de usuário suficientes para identificar atividades suspeitas ou mal-intencionadas. Os logs devem ser mantidos por um tempo suficiente permitir a análise forense atrasada. Os logs devem estar em um formato que pode ser facilmente consumido por uma solução de gerenciamento de log. Certifique-se de que os alertas para erros relacionados à segurança são disparadas. Insuficiente de log e monitoramento permite que os invasores ainda mais os sistemas de ataque e mantêm a persistência.

### <a name="take-advantage-of-error-and-exception-handling"></a>Tirar proveito do erro e tratamento de exceções

Implementação de corrigir o erro e [tratamento de exceção](https://docs.microsoft.com/dotnet/standard/exceptions/best-practices-for-exceptions) é uma parte importante da codificação de defesa. Erro e tratamento de exceções são essenciais para fazer um sistema seguro e confiável. Erros no tratamento de erros podem levar a diferentes tipos de vulnerabilidades de segurança, como vazamento de informações para os invasores e ajudando os invasores entender mais sobre a plataforma e o design.

Verifique se:

- Tratar exceções de uma maneira centralizada para evitar duplicado [blocos try/catch](https://docs.microsoft.com/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions) no código.

- Todos os comportamentos inesperados são tratados dentro do aplicativo.

- As mensagens que são exibidas aos usuários não vazam dados críticos, mas fornecer informações suficientes para explicar o problema.

- As exceções são registradas e que eles fornecem informações suficientes para que as equipes de resposta de análise forense ou um incidente investigar.

[Aplicativos lógicos do Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) fornece uma experiência de primeira classe para [tratamento de erros e exceções](https://docs.microsoft.com/azure/logic-apps/logic-apps-exception-handling) que são causados por sistemas dependentes. Você pode usar aplicativos lógicos para criar fluxos de trabalho para automatizar tarefas e processos que se integram a aplicativos, dados, sistemas e serviços entre empresas e organizações.

### <a name="use-logging-and-alerting"></a>Registro em log de uso e os alertas

[Log](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1) sua segurança emite para investigações de segurança e disparar alertas sobre problemas para garantir que as pessoas conhecer os problemas de maneira oportuna. Habilite a auditoria e log em todos os componentes. Os logs de auditoria devem capturar o contexto de usuário e identificar todos os eventos importantes.

Verifique se você não registrar quaisquer dados confidenciais que um usuário envia para seu site. Exemplos de dados confidenciais:

- Credenciais do usuário
- Números de previdência social ou outras informações de identificação
- Números de cartão de crédito ou outras informações financeiras
- Informações de integridade
- As chaves privadas ou outros dados que podem ser usados para descriptografar informações criptografadas
- Informações do sistema ou aplicativo que podem ser usadas para atacar com mais eficiência o aplicativo

Certifique-se de que o aplicativo monitora eventos de gerenciamento de usuário, como logons de usuário bem-sucedidas e com falha, redefinições de senha, as alterações de senha, bloqueio de conta e registro do usuário. Registro em log para esses eventos ajuda a detectar e reagir a comportamentos potencialmente suspeitos. Ele também permite coletar dados de operações, como quem está acessando o aplicativo.

## <a name="next-steps"></a>Próximas etapas
Os artigos a seguir, é recomendável que os controles de segurança e as atividades que podem ajudá-lo a desenvolvem e implantar aplicativos seguros.

- [Desenvolver aplicativos seguros](secure-develop.md)
- [Implantar aplicativos seguros](secure-deploy.md)
