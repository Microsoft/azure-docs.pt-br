---
title: Planejar uma implantação de Proxy de Aplicativo do Azure Active Directory
description: Um guia de ponta a ponta para planejar a implantação do proxy de aplicativo em sua organização
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/31/2020
ms.author: kenwith
ms.openlocfilehash: 6da003612d98d107390c9f20a5172786c30665a6
ms.sourcegitcommit: dae6b628a8d57540263a1f2f1cdb10721ed1470d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "105709745"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Planejar uma implantação do Proxy de Aplicativo do Azure AD

O proxy de aplicativo Azure Active Directory (AD do Azure) é uma solução de acesso remoto segura e econômica para aplicativos locais. Ele fornece um caminho de transição imediato para as organizações de "nuvem First" para gerenciar o acesso a aplicativos locais herdados que ainda não são capazes de usar protocolos modernos. Para obter informações introdutórias adicionais, consulte [o que é o proxy de aplicativo](./application-proxy.md).

O Proxy de Aplicativo é recomendado para conceder aos usuários remotos o acesso a recursos internos. O proxy de aplicativo substitui a necessidade de uma VPN ou proxy reverso para esses casos de uso de acesso remoto. Ele não se destina a usuários que estão na rede corporativa. Esses usuários que usam o proxy de aplicativo para acesso à intranet podem apresentar problemas de desempenho indesejáveis.

Este artigo inclui os recursos necessários para planejar, operar e gerenciar Proxy de Aplicativo do AD do Azure.

## <a name="plan-your-implementation"></a>Planejar sua implementação

A seção a seguir fornece uma visão geral dos principais elementos de planejamento que irão configurar para uma experiência de implantação eficiente.

### <a name="prerequisites"></a>Pré-requisitos

Você precisa atender aos seguintes pré-requisitos antes de iniciar a implementação. Você pode ver mais informações sobre como configurar seu ambiente, incluindo esses pré-requisitos, neste [tutorial](application-proxy-add-on-premises-application.md).

* **Conectores**: conectores são agentes leves que você pode implantar em:
   * Hardware físico local
   * Uma VM hospedada em qualquer solução de hipervisor
   * Uma VM hospedada no Azure para habilitar a conexão de saída com o serviço de proxy de aplicativo.

* Consulte [entender aplicativo Azure ad conectores de proxy](application-proxy-connectors.md) para obter uma visão geral mais detalhada.

     * Os computadores do conector devem [ser habilitados para o TLS 1,2](application-proxy-add-on-premises-application.md) antes da instalação dos conectores.

     * Se possível, implante os conectores na [mesma rede](application-proxy-network-topology.md) e segmento que os servidores de aplicativos Web de back-end. É melhor implantar conectores depois de concluir uma descoberta de aplicativos.
     * Recomendamos que cada grupo de conectores tenha pelo menos dois conectores para fornecer alta disponibilidade e escala. Ter três conectores é ideal caso você precise atender a um computador em qualquer ponto. Examine a [tabela de capacidade do conector](./application-proxy-connectors.md#capacity-planning) para ajudar a decidir em que tipo de computador instalar conectores. Quanto maior a máquina, mais buffer e desempenho do conector serão.

* **Configurações de acesso à rede**: conectores do Azure proxy de aplicativo do AD [se conectam ao Azure via HTTPS (porta TCP 443) e http (porta TCP 80)](application-proxy-add-on-premises-application.md).

   * O tráfego do protocolo TLS de terminação não tem suporte e impedirá que os conectores estabeleçam um canal seguro com seus respectivos pontos de extremidade de proxy Azure App.

   * Evite todas as formas de inspeção embutida em comunicações TLS de saída entre conectores e o Azure. A inspeção interna entre um conector e aplicativos de back-end é possível, mas pode degradar a experiência do usuário e, como tal, não é recomendada.

   * O balanceamento de carga dos conectores em si também não tem suporte, ou até mesmo necessário.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Considerações importantes antes de configurar o Azure Proxy de Aplicativo do AD

Os requisitos principais a seguir devem ser atendidos para configurar e implementar Proxy de Aplicativo do AD do Azure.

*  **Integração do Azure**: antes de implantar o proxy de aplicativo, as identidades de usuário devem ser sincronizadas de um diretório local ou criadas diretamente em seus locatários do Azure AD. A Sincronização de Identidades permite que o Azure AD pré-autentique os usuários antes de permitir acesso a eles aos aplicativos publicados pelo Proxy de Aplicativo e tenha as informações de identificador de usuário necessárias para realizar o SSO (logon único).

* **Requisitos de acesso condicional**: não recomendamos o uso do proxy de aplicativo para acesso à intranet porque isso adiciona latência que afetará os usuários. É recomendável usar o proxy de aplicativo com políticas de acesso condicional e pré-autenticação para acesso remoto da Internet.  Uma abordagem para fornecer acesso condicional para uso na intranet é modernizar os aplicativos para que eles possam se autenticar diretamente com o AAD. Consulte [recursos para migrar aplicativos para o AAD](./migration-resources.md) para obter mais informações.

* **Limites de serviço**: para se proteger contra o superconsumo de recursos por locatários individuais, há limites de limitação definidos por aplicativo e locatário. Para ver esses limites, consulte [restrições e limites de serviço do Azure ad](../enterprise-users/directory-service-limits-restrictions.md). Esses limites de limitação se baseiam em um parâmetro de comparação muito acima do volume de uso típico e fornece um amplo buffer para a maioria das implantações.

* **Certificado público**: se você estiver usando nomes de domínio personalizados, deverá adquirir um certificado TLS/SSL. Dependendo dos requisitos organizacionais, obter um certificado pode levar algum tempo e é recomendável iniciar o processo o mais cedo possível. O proxy de Aplicativo Azure dá suporte a certificados padrão, [curingas](application-proxy-wildcard.md)ou baseados em San. Para obter mais detalhes, consulte [configurar domínios personalizados com o Azure proxy de aplicativo do AD](application-proxy-configure-custom-domain.md).

* **Requisitos de domínio**: o logon único para seus aplicativos publicados usando a delegação restrita de Kerberos (KCD) requer que o servidor que executa o conector e o servidor que executa o aplicativo estejam ingressados no domínio e façam parte do mesmo domínio ou domínios confiantes.
Para obter informações detalhadas sobre o tópico, consulte [KCD para logon único](application-proxy-configure-single-sign-on-with-kcd.md) com o proxy de aplicativo. O serviço do conector é executado no contexto do sistema local e não deve ser configurado para usar uma identidade personalizada.

* **Registros DNS para URLs**

   * Antes de usar domínios personalizados no proxy de aplicativo, você deve criar um registro CNAME no DNS público, permitindo que os clientes resolvam a URL externa definida personalizada para o endereço de proxy de aplicativo predefinido. A falha ao criar um registro CNAME para um aplicativo que usa um domínio personalizado impedirá que os usuários remotos se conectem ao aplicativo. As etapas necessárias para adicionar registros CNAME podem variar do provedor DNS para o provedor e, portanto, saiba como [gerenciar registros DNS e conjuntos de registros usando o portal do Azure](../../dns/dns-operations-recordsets-portal.md).

   * Da mesma forma, os hosts do conector devem ser capazes de resolver a URL interna dos aplicativos que estão sendo publicados.

* **Direitos e funções administrativas**

   * A **instalação do conector** requer direitos de administrador local no Windows Server em que ele está sendo instalado. Ele também requer um mínimo de uma função de *administrador de aplicativos* para autenticar e registrar a instância do conector em seu locatário do Azure AD.

   * A **publicação e a administração de aplicativos** exigem a função de administrador de *aplicativos* . Os administradores de aplicativos podem gerenciar todos os aplicativos no diretório, incluindo registros, configurações de SSO, atribuições de usuário e grupo e licenciamento, configurações de proxy de aplicativo e consentimento. Ela não concede a capacidade de gerenciar o Acesso Condicional. A função de *administrador de aplicativos de nuvem* tem todas as capacidades do administrador do aplicativo, exceto pelo fato de que ele não permite o gerenciamento de configurações de proxy de aplicativo.

* **Licenciamento**: o proxy de aplicativo está disponível por meio de uma assinatura Azure ad Premium. Consulte a [página de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) para obter uma lista completa de opções e recursos de licenciamento.

### <a name="application-discovery"></a>Descoberta de aplicativos

Compile um inventário de todos os aplicativos no escopo que estão sendo publicados por meio do proxy de aplicativo coletando as seguintes informações:

| Information Type| Informações a serem coletadas |
|---|---|
| Tipo de serviço| Por exemplo: SharePoint, SAP, CRM, aplicativo Web personalizado, API |
| Plataforma de aplicativos | Por exemplo: IIS do Windows, Apache no Linux, Tomcat, NGINX |
| Associação do domínio| FQDN (nome de domínio totalmente qualificado) do servidor Web |
| Local do aplicativo | Onde o servidor Web ou o farm está localizado em sua infraestrutura |
| Acesso interno | A URL exata usada ao acessar o aplicativo internamente. <br> Se for um farm, que tipo de balanceamento de carga está em uso? <br> Se o aplicativo desenha conteúdo de fontes diferentes de si mesmo.<br> Determine se o aplicativo opera sobre WebSockets. |
| Acesso externo | A solução de fornecedor que o aplicativo já está exposto para o externamente. <br> A URL que você deseja usar para acesso externo. Se o SharePoint, verifique se os mapeamentos de acesso alternativo estão configurados de acordo com [este guia](/SharePoint/administration/configure-alternate-access-mappings). Caso contrário, será necessário definir URLs externas. |
| Certificado público | Se estiver usando um domínio personalizado, adquira um certificado com um nome de entidade correspondente. se um certificado existir, anote o número de série e o local de onde ele pode ser obtido. |
| Tipo de autenticação| O tipo de autenticação compatível com o suporte do aplicativo, como básica, autenticação de integração do Windows, baseada em formulários, cabeçalho e declarações. <br>Se o aplicativo estiver configurado para ser executado em uma conta de domínio específica, observe o FQDN (nome de domínio totalmente qualificado) da conta de serviço.<br> Se baseado em SAML, o identificador e URLs de resposta. <br> Se baseado em cabeçalho, a solução de fornecedor e o requisito específico para tratamento do tipo de autenticação. |
| Nome do grupo de conectores | O nome lógico do grupo de conectores que será designado para fornecer o canal e o SSO para este aplicativo de back-end. |
| Acesso de usuários/grupos | Os usuários ou grupos de usuários aos quais será concedido acesso externo ao aplicativo. |
| Requisitos adicionais | Observe quaisquer requisitos adicionais de acesso remoto ou de segurança que devem ser fatorados na publicação do aplicativo. |

Você pode baixar esta [planilha de inventário de aplicativos](https://aka.ms/appdiscovery) para inventariar seus aplicativos.

### <a name="define-organizational-requirements"></a>Definir requisitos organizacionais

Veja a seguir as áreas para as quais você deve definir os requisitos de negócios de sua organização. Cada área contém exemplos de requisitos

 **Acesso**

* Usuários remotos com dispositivos ingressados no domínio ou no Azure AD podem acessar aplicativos publicados com segurança com o SSO (logon único) contínuo.

* Os usuários remotos com dispositivos pessoais aprovados podem acessar com segurança os aplicativos publicados, desde que eles estejam registrados no MFA e tenham registrado o aplicativo Microsoft Authenticator em seu celular como um método de autenticação.

**Governança**

* Os administradores podem definir e monitorar o ciclo de vida de atribuições de usuário em aplicativos publicados por meio do proxy de aplicativo.

**Segurança**

* Somente os usuários atribuídos a aplicativos por meio da Associação de grupo ou individualmente podem acessar esses aplicativos.

**Desempenho**

* Não há degradação do desempenho do aplicativo em comparação ao acesso ao aplicativo da rede interna.

**Experiência do Usuário**

* Os usuários estão cientes de como acessar seus aplicativos usando URLs familiares da empresa em qualquer plataforma de dispositivo.

**Auditoria**
* Os administradores podem auditar a atividade de acesso do usuário.


### <a name="best-practices-for-a-pilot"></a>Práticas recomendadas para um piloto

Determine a quantidade de tempo e esforço necessários para encerrar completamente um único aplicativo para acesso remoto com SSO (logon único). Faça isso executando um piloto que considere sua descoberta inicial, publicação e teste geral. O uso de um aplicativo Web baseado no IIS simples já pré-configurado para a autenticação integrada do Windows (IWA) ajudaria a estabelecer uma linha de base, pois essa configuração requer um esforço mínimo para o acesso remoto e o SSO do piloto bem-sucedido.

Os elementos de design a seguir devem aumentar o sucesso da implementação do piloto diretamente em um locatário de produção.

**Gerenciamento de conector**:

* Os conectores desempenham um papel fundamental no fornecimento do canal local para seus aplicativos. O uso do grupo de conectores **padrão** é adequado para o teste piloto inicial de aplicativos publicados antes de testá-los em produção. Os aplicativos testados com êxito podem ser movidos para grupos de conectores de produção.

**Gerenciamento de aplicativos**:

* É mais provável que sua força de obra se lembre de que uma URL externa é familiar e relevante. Evite publicar seu aplicativo usando nossos sufixos msappproxy.net ou onmicrosoft.com predefinidos. Em vez disso, forneça um conhecido domínio verificado de nível superior, prefixado com um nome de host lógico, como *intranet. <customers_domain>. com*.

* Restrinja a visibilidade do ícone do aplicativo piloto a um grupo piloto ocultando seu ícone de inicialização formulário o portal do Azure myapps. Quando estiver pronto para produção, você pode fazer o escopo do aplicativo para seu respectivo público-alvo, seja no mesmo locatário de pré-produção ou publicando também o aplicativo em seu locatário de produção.

**Configurações de logon único**: algumas configurações de SSO têm dependências específicas que podem levar tempo para serem configuradas, portanto evite atrasos de controle de alterações, garantindo que as dependências sejam resolvidas antes do tempo. Isso inclui os hosts do conector de ingresso no domínio para executar o SSO usando a KCD (delegação restrita de Kerberos) e cuidar de outras atividades demoradas. Por exemplo, configurar uma instância de acesso de PING, se precisar de SSO baseado em cabeçalho.

**TLS entre o host do conector e o aplicativo de destino**: a segurança é fundamental, portanto, o TLS entre o host do conector e os aplicativos de destino sempre deve ser usado. Particularmente, se o aplicativo Web estiver configurado para a autenticação baseada em formulários (FBA), como as credenciais do usuário serão efetivamente transmitidas em texto não criptografado.

**Implemente incrementalmente e teste cada etapa**.
Realize testes funcionais básicos depois de publicar um aplicativo para garantir que todos os requisitos de usuário e de negócios sejam atendidos seguindo as instruções abaixo:

1. Teste e valide o acesso geral ao aplicativo Web com pré-autenticação desabilitada.
2. Se houver êxito, habilite a pré-autenticação e atribua usuários e grupos. Teste e valide o acesso.
3. Em seguida, adicione o método SSO para seu aplicativo e teste novamente para validar o acesso.
4. Aplique as políticas de acesso condicional e MFA, conforme necessário. Teste e valide o acesso.

**Ferramentas de solução de problemas**: ao solucionar problemas, sempre comece Validando o acesso ao aplicativo publicado do navegador no host do conector e confirme se o aplicativo funciona conforme o esperado. Quanto mais simples a sua configuração, mais fácil de determinar a causa raiz, considere a possibilidade de tentar reproduzir problemas com uma configuração mínima, como usar apenas um único conector e nenhum SSO. Em alguns casos, as ferramentas de depuração da Web, como o Fiddler da Telerik, podem provar indispensável para solucionar problemas de acesso ou de conteúdo em aplicativos acessados por meio de um proxy. O Fiddler também pode atuar como um proxy para ajudar a rastrear e depurar o tráfego de plataformas móveis, como iOS e Android, e praticamente qualquer coisa que possa ser configurada para rotear por meio de um proxy. Consulte o [Guia de solução de problemas](application-proxy-troubleshoot.md) para obter mais informações.

## <a name="implement-your-solution"></a>Implementar sua solução

### <a name="deploy-application-proxy"></a>Implantar o proxy de aplicativo

As etapas para implantar o proxy de aplicativo são abordadas neste [tutorial para adicionar um aplicativo local para acesso remoto](application-proxy-add-on-premises-application.md). Se a instalação não for bem-sucedida, selecione  **solucionar problemas de proxy de aplicativo**  no portal ou use o guia de solução de problemas [para instalar o conector do agente de proxy de aplicativo](application-proxy-connector-installation-problem.md).

### <a name="publish-applications-via-application-proxy"></a>Publicar aplicativos por meio do proxy de aplicativo

A publicação de aplicativos pressupõe que você tenha atendido todos os pré-requisitos e que você tenha vários conectores mostrados como registrados e ativos na página proxy de aplicativo.

Você também pode publicar aplicativos usando o [PowerShell](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true).

Abaixo estão algumas práticas recomendadas a serem seguidas ao publicar um aplicativo:

* **Usar grupos de conectores**: atribua um grupo de conectores designado para publicar cada aplicativo respectivo. Recomendamos que cada grupo de conectores tenha pelo menos dois conectores para fornecer alta disponibilidade e escala. Ter três conectores é ideal caso você precise atender a um computador em qualquer ponto. Além disso, consulte [publicar aplicativos em redes e locais separados usando grupos de conectores](application-proxy-connector-groups.md) para ver como você também pode usar grupos de conectores para segmentar seus conectores por rede ou local.

* **Definir tempo limite do aplicativo back-end**: essa configuração é útil em cenários em que o aplicativo pode exigir mais de 75 segundos para processar uma transação do cliente. Por exemplo, quando um cliente envia uma consulta a um aplicativo Web que atua como um front-end para um banco de dados. O front-end envia essa consulta para seu servidor de banco de dados back-end e aguarda uma resposta, mas quando recebe uma resposta, o lado do cliente da conversa atinge o tempo limite. Definir o tempo limite como longo fornece de 180 segundos para que as transações mais longas sejam concluídas.

* **Usar tipos de cookie apropriados**

   * **Cookie somente http**: fornece segurança adicional ao fazer com que o proxy de aplicativo inclua o sinalizador HTTPOnly nos cabeçalhos de resposta http Set-cookie. Essa configuração ajuda a reduzir as explorações, como XSS (script entre sites). Deixe isso definido como não para clientes/agentes de usuário que exigem acesso ao cookie de sessão. Por exemplo, o cliente RDP/MTSC conectando-se a um gateway Área de Trabalho Remota publicado por meio do proxy de aplicativo.

   * **Cookie seguro**: quando um cookie é definido com o atributo Secure, o agente do usuário (aplicativo do lado do cliente) incluirá apenas o cookie em solicitações HTTP se a solicitação for transmitida por um canal protegido por TLS. Isso ajuda a reduzir o risco de um cookie ser comprometido em canais de texto não criptografado, portanto, deve ser habilitado.

   * **Cookie persistente**: permite que o cookie de sessão do proxy de aplicativo persista entre os fechamentos do navegador, se for válido, até que expire ou seja excluído. Usado para cenários em que um aplicativo avançado, como o Office, acessa um documento em um aplicativo Web publicado, sem que o usuário seja solicitado a fornecer autenticação novamente. No entanto, habilite com cuidado, pois os cookies persistentes podem deixar um serviço em risco de acesso não autorizado, se não forem usados em conjunto com outros controles de compensação. Essa configuração só deve ser usada para aplicativos mais antigos que não podem compartilhar cookies entre processos. É melhor atualizar seu aplicativo para lidar com cookies de compartilhamento entre processos em vez de usar essa configuração.

* **Converter URLs em cabeçalhos**: habilite isso para cenários em que o DNS interno não pode ser configurado para corresponder ao namespace público da organização (a. k. a DNS dividido). A menos que seu aplicativo exija o cabeçalho de host original na solicitação do cliente, deixe esse valor definido como Sim. A alternativa é fazer com que o conector use o FQDN na URL interna para o roteamento do tráfego real e o FQDN na URL externa, como o cabeçalho do host. Na maioria dos casos, essa alternativa deve permitir que o aplicativo funcione normalmente, quando acessado remotamente, mas seus usuários perdem os benefícios de ter uma correspondência dentro & URL externa.

* **Traduzir URLs no corpo do aplicativo**: Ative a tradução do link do corpo do aplicativo para um aplicativo quando desejar que os links desse aplicativo sejam convertidos em respostas de volta ao cliente. Se habilitada, essa função fornece uma tentativa de melhor esforço na tradução de todos os links internos que o proxy de aplicativo encontra nas respostas HTML e CSS sendo retornadas aos clientes. É útil ao publicar aplicativos que contenham links absolutos ou curtos de NetBIOS embutidos no conteúdo, ou aplicativos com conteúdo que se vincula a outros aplicativos locais.

Para cenários em que um aplicativo publicado é vinculado a outros aplicativos publicados, habilite a tradução de link para cada aplicativo para que você tenha controle sobre a experiência do usuário no nível por aplicativo.

Por exemplo, suponha que você tenha três aplicativos publicados por meio do proxy de aplicativo vinculados entre si: benefícios, despesas e viagens, além de um quarto aplicativo, comentários, que não é publicado por meio do proxy de aplicativo.

![Imagem 1](media/App-proxy-deployment-plan/link-translation.png)

Quando você habilita a conversão de link para o aplicativo benefícios, os links para despesas e viagens são redirecionados para as URLs externas para esses aplicativos, para que os usuários que acessam os aplicativos de fora da rede corporativa possam acessá-los. Os links de despesas e viagens de volta para os benefícios não funcionam porque a conversão de links não foi habilitada para esses dois aplicativos. O link para comentários não é redirecionado porque não há nenhuma URL externa, de modo que os usuários que usam o aplicativo de benefícios não conseguirão acessar o aplicativo de comentários de fora da rede corporativa. Consulte informações detalhadas sobre [conversão de link e outras opções de redirecionamento](application-proxy-configure-hard-coded-link-translation.md).

### <a name="access-your-application"></a>Acesse seu aplicativo

Existem várias opções para gerenciar o acesso aos recursos publicados do proxy de aplicativo, portanto, escolha o mais apropriado para o cenário e as necessidades de escalabilidade em questão. As abordagens comuns incluem: usando grupos locais que estão sendo sincronizados por meio de Azure AD Connect, criando grupos dinâmicos no Azure AD com base em atributos de usuário, usando grupos de autoatendimento que são gerenciados por um proprietário de recurso ou uma combinação de todos eles. Consulte os recursos vinculados para obter os benefícios de cada um.

A maneira mais simples de atribuir acesso de usuários a um aplicativo é entrar nas opções de **usuários e grupos** no painel esquerdo do seu aplicativo publicado e atribuir diretamente grupos ou indivíduos.

![Figura 24](media/App-proxy-deployment-plan/add-user.png)

Você também pode permitir que os usuários acessem por autoatendimento em seu aplicativo atribuindo um grupo que atualmente não é membro e configurando as opções de autoatendimento.

![Imagem 25](media/App-proxy-deployment-plan/allow-access.png)

Se habilitada, os usuários poderão fazer logon no portal do myapps e solicitar o acesso e ser aprovados automaticamente e adicionados ao grupo de autoatendimento já permitido ou precisar de aprovação de um Aprovador designado.

Os usuários convidados também podem ser [convidados a acessar aplicativos internos publicados por meio do proxy de aplicativo por meio do Azure ad B2B](../external-identities/add-users-information-worker.md).

Para aplicativos locais que normalmente são acessíveis anonimamente, não exigindo nenhuma autenticação, você pode preferir desabilitar a opção localizada nas **Propriedades** do aplicativo.

![Imagem 26](media/App-proxy-deployment-plan/assignment-required.png)


Deixar essa opção definida como não permite que os usuários acessem o aplicativo local por meio do proxy de Aplicativo Azure AD sem permissões, portanto, use com cuidado.

Depois que seu aplicativo for publicado, ele deverá ser acessível digitando sua URL externa em um navegador ou por seu ícone em [https://myapps.microsoft.com](https://myapps.microsoft.com/) .

### <a name="enable-pre-authentication"></a>Habilitar pré-autenticação

Verifique se seu aplicativo está acessível por meio do proxy de aplicativo acessando-o por meio da URL externa.

1. Navegue até **Azure Active Directory**  >  **aplicativos empresariais**  >  **todos os aplicativos** e escolha o aplicativo que você deseja gerenciar.

2. Selecione **Proxy de Aplicativo**.

3. No campo **pré-autenticação** , use a lista suspensa para selecionar **Azure Active Directory** e selecione **salvar**.

Com a pré-autenticação habilitada, o Azure AD desafiará os usuários primeiro para autenticação e, se o logon único estiver configurado, o aplicativo de back-end também verificará o usuário antes que o acesso ao aplicativo seja concedido. Alterar o modo de pré-autenticação de passagem para o Azure AD também configura a URL externa com HTTPS, portanto, qualquer aplicativo inicialmente configurado para HTTP agora será protegido com HTTPS.

### <a name="enable-single-sign-on"></a>Habilitar Logon Único

O SSO fornece a melhor experiência de usuário possível e a segurança, pois os usuários só precisam entrar uma vez ao acessar o Azure AD. Depois que um usuário é autenticado previamente, o SSO é executado pelo conector de proxy de aplicativo que se autentica no aplicativo local, em nome do usuário. O aplicativo de back-end processa o logon como se ele fosse o próprio usuário.

Escolher a opção de **passagem** permite que os usuários acessem o aplicativo publicado sem precisar se autenticar no Azure AD.

A execução de SSO só será possível se o Azure AD puder identificar o usuário solicitando acesso a um recurso, de modo que seu aplicativo deve ser configurado para autenticar previamente os usuários com o Azure AD no acesso para que o SSO funcione, caso contrário, as opções de SSO serão desabilitadas.

Leia [logon único para aplicativos no Azure ad](what-is-single-sign-on.md) para ajudá-lo a escolher o método de SSO mais apropriado ao configurar seus aplicativos.

###  <a name="working-with-other-types-of-applications"></a>Trabalhando com outros tipos de aplicativos

O Azure Proxy de Aplicativo do AD também pode dar suporte a aplicativos que foram desenvolvidos para usar a [MSAL (biblioteca de autenticação da Microsoft)](../develop/v2-overview.md). Ele dá suporte a aplicativos cliente nativos consumindo tokens emitidos pelo Azure AD recebidos nas informações de cabeçalho da solicitação do cliente para executar a pré-autenticação em nome dos usuários.

Leia [Publicando aplicativos cliente móveis e nativos](./application-proxy-configure-native-client-application.md) e [aplicativos baseados em declarações](./application-proxy-configure-for-claims-aware-applications.md) para saber mais sobre as configurações disponíveis do proxy de aplicativo.

### <a name="use-conditional-access-to-strengthen-security"></a>Usar o acesso condicional para reforçar a segurança

A segurança do aplicativo requer um conjunto avançado de recursos de segurança que podem proteger e responder a ameaças complexas locais e na nuvem. Os invasores geralmente têm acesso à rede corporativa por meio de credenciais de usuário fracas, padrão ou roubadas.  A segurança orientada por identidade da Microsoft reduz o uso de credenciais roubadas Gerenciando e protegendo as identidades privilegiadas e não privilegiadas.

Os seguintes recursos podem ser usados para dar suporte ao Azure Proxy de Aplicativo do AD:

* Acesso condicional com base no usuário e no local: Mantenha os dados confidenciais protegidos limitando o acesso do usuário com base na localização geográfica ou em um endereço IP com [políticas de acesso condicional com base no local](../conditional-access/location-condition.md).

* Acesso condicional com base no dispositivo: garanta que somente os dispositivos registrados, aprovados e em conformidade possam acessar dados corporativos com [acesso condicional baseado em dispositivo](../conditional-access/require-managed-devices.md).

* Acesso condicional baseado em aplicativo: o trabalho não precisa parar quando um usuário não está na rede corporativa. [Proteja o acesso à nuvem corporativa e aos aplicativos locais](../conditional-access/app-based-conditional-access.md) e mantenha o controle com acesso condicional.

* Acesso condicional com base em risco: Proteja seus dados contra hackers mal-intencionados com uma [política de acesso condicional baseada em risco](https://www.microsoft.com/cloud-platform/conditional-access) que pode ser aplicada a todos os aplicativos e a todos os usuários, seja localmente ou na nuvem.

* Meus aplicativos do Azure AD: com o serviço de proxy de aplicativo implantado e aplicativos publicados com segurança, oferecem aos usuários um hub simples para descobrir e acessar todos os seus aplicativos. Aumente a produtividade com recursos de autoatendimento, como a capacidade de solicitar acesso a novos aplicativos e grupos ou gerenciar o acesso a esses recursos em nome de outros, por meio de [meus aplicativos](https://aka.ms/AccessPanelDPDownload).

## <a name="manage-your-implementation"></a>Gerenciar sua implementação

### <a name="required-roles"></a>Funções necessárias

A Microsoft defende o princípio de conceder o privilégio mínimo possível para executar as tarefas necessárias com o Azure AD. [Examine as diferentes funções do Azure que estão disponíveis](../roles/permissions-reference.md) e escolha a correta para atender às necessidades de cada pessoa. Algumas funções podem precisar ser aplicadas temporariamente e removidas após a conclusão da implantação.

| Função corporativa| Tarefas de negócios| Funções do Azure AD |
|---|---|---|
| Administrador de assistência técnica | Normalmente limitado à qualificação de problemas relatados pelo usuário final e à execução de tarefas limitadas, como alteração de senhas de usuários, invalidação de tokens de atualização e integridade do serviço de monitoramento. | Administrador de assistência técnica |
| Administrador de identidade| Leia relatórios de entrada e logs de auditoria do Azure AD para depurar problemas relacionados ao proxy de aplicativo.| Leitor de segurança |
| Proprietário do aplicativo| Crie e gerencie todos os aspectos de aplicativos empresariais, registros de aplicativos e configurações de proxy de aplicativo.| Administrador do aplicativo |
| Administrador de infraestrutura | Proprietário da substituição do certificado | Administrador do aplicativo |

Minimizar o número de pessoas que têm acesso a informações ou recursos seguros ajudará a reduzir a chance de um ator mal-intencionado obter acesso não autorizado ou um usuário autorizado que afete inadvertidamente um recurso confidencial.

No entanto, os usuários ainda precisam executar operações privilegiadas diárias, impondo as políticas de [Privileged Identity Management](../privileged-identity-management/pim-configure.md) just-in-time (JIT) para fornecer acesso privilegiado sob demanda aos recursos do Azure e o Azure AD é a nossa abordagem recomendada para gerenciar com eficiência o acesso administrativo e a auditoria.

### <a name="reporting-and-monitoring"></a>Relatórios e monitoramento

O Azure AD fornece informações adicionais sobre o uso de aplicativos e a integridade operacional de sua organização por meio de [relatórios e logs de auditoria](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). O proxy de aplicativo também torna muito fácil monitorar conectores do portal do Azure AD e dos logs de eventos do Windows.

#### <a name="application-audit-logs"></a>Logs de auditoria de aplicativo

Esses logs fornecem informações detalhadas sobre logons para aplicativos configurados com o proxy de aplicativo e o dispositivo e o usuário que acessa o aplicativo. [Os logs de auditoria](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) estão localizados no portal do Azure e na [API de auditoria](/graph/api/resources/directoryaudit?view=graph-rest-beta) para exportação. Além disso, os [relatórios de uso e informações](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context) também estão disponíveis para seu aplicativo.

#### <a name="application-proxy-connector-monitoring"></a>Monitoramento do conector de proxy de aplicativo

Os conectores e o serviço cuidam de todas as tarefas de alta disponibilidade. Você pode monitorar o status dos conectores na página proxy de aplicativo no portal do Azure AD. Para obter mais informações sobre conectores manutenção, consulte [entender os conectores de proxy de aplicativo do AD do Azure](./application-proxy-connectors.md#maintenance).

![Exemplo: conectores de Proxy de Aplicativo do AD do Azure](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Logs de eventos do Windows e contadores de desempenho

Os conectores têm logs de administração e de sessão. Os logs de administrador incluem eventos de chave e seus erros. Os logs de sessão incluem todas as transações e seus detalhes de processamento. Os logs e os contadores estão localizados nos logs de eventos do Windows para obter mais informações, consulte [entender os conectores de proxy de aplicativo do AD do Azure](./application-proxy-connectors.md#under-the-hood). Siga este [tutorial para configurar fontes de dados de log de eventos no Azure monitor](../../azure-monitor/agents/data-sources-windows-events.md).

### <a name="troubleshooting-guide-and-steps"></a>Guia de solução de problemas e etapas

Saiba mais sobre problemas comuns e como resolvê-los com nosso guia para [solucionar problemas](application-proxy-troubleshoot.md) de mensagens de erro.

Os artigos a seguir abordam cenários comuns que também podem ser usados para criar guias de solução de problemas para sua organização de suporte.

* [Problema ao exibir a página do aplicativo](application-proxy-page-appearance-broken-problem.md)
* [O carregamento de aplicativos é muito longo](application-proxy-page-load-speed-problem.md)
* [Os links na página do aplicativo não funcionam](application-proxy-page-links-broken-problem.md)
* [Quais portas abrir para meu aplicativo](application-proxy-add-on-premises-application.md)
* [Nenhum conector útil em um grupo de conectores para meu aplicativo](application-proxy-connectivity-no-working-connector.md)
* [Configurar no portal do administrador](application-proxy-config-how-to.md)
* [Configurar logon único para meu aplicativo](application-proxy-config-sso-how-to.md)
* [Problema ao criar um aplicativo no portal do administrador](application-proxy-config-problem.md)
* [Configurar a Delegação Restrita de Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Configurar com PingAccess](./application-proxy-ping-access-publishing-guide.md)
* [Erro Não é possível acessar este aplicativo corporativo](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Problema ao instalar o conector de agente de Application Proxy](application-proxy-connector-installation-problem.md)
* [Problema de entrada](application-sign-in-problem-on-premises-application-proxy.md)
