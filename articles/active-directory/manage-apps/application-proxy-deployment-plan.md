---
title: Planejar uma implantação de Proxy de aplicativo do Azure Active Directory
description: Um guia de ponta a ponta para planejar a implantação do proxy de aplicativo dentro da sua organização
services: active-directory
documentationcenter: azure
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: baselden
ms.reviewer: ''
ms.openlocfilehash: 7d40c0604f0947abe8d536eafe87545790476a98
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625540"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Planejar uma implantação do Proxy de aplicativo do Azure AD

Proxy de aplicativo do Azure Active Directory (Azure AD) é uma solução de acesso remoto seguro e econômico para aplicativos locais. Ele fornece um caminho de transição imediata para organizações de "Nuvem primeiro" gerenciar o acesso ao herdados aplicativos locais que ainda não estão capaz de usar protocolos modernos. Para obter informações introdutórias adicionais, consulte [o que é o Proxy de aplicativo](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy).

O Proxy de aplicativo é recomendado para dando a usuários remotos acesso aos recursos internos. O Proxy de aplicativo substitui a necessidade de uma VPN ou o proxy reverso para esses casos de uso do acesso remoto. Ele não é destinado a usuários que estão na rede corporativa. Esses usuários que usam o Proxy de aplicativo para acesso à intranet poderão enfrentar problemas de desempenho indesejável.

Este artigo inclui os recursos que você precisa para planejar, operar e gerenciar Proxy de aplicativo do Azure AD. 

## <a name="plan-your-implementation"></a>Planejar sua implementação

A seção a seguir fornece uma visão geral da chave de elementos que irá prepará-lo uma experiência de implantação eficiente de planejamento. 

### <a name="prerequisites"></a>Pré-requisitos

Você precisa cumprir os seguintes pré-requisitos antes de iniciar sua implementação. Você pode ver mais informações sobre como configurar seu ambiente, incluindo estes pré-requisitos desta [tutorial](application-proxy-add-on-premises-application.md).

* **Conectores**: Conectores são agentes leves que podem ser implantados em:
   * O hardware físico no local
   * Uma VM hospedada dentro de qualquer solução de hipervisor
   * Uma VM hospedada no Azure para habilitar a conexão de saída para o serviço de Proxy de aplicativo.

* Ver [entender o Proxy de aplicativo do Azure AD conectores](application-proxy-connectors.md) para obter uma visão mais detalhada.

     * Conector máquinas devem [ser habilitado para o TLS 1.2](application-proxy-add-on-premises-application.md) antes de instalar os conectores.

     * Se possível, implantar conectores na [mesma rede](application-proxy-network-topology.md) e segmento como os servidores de aplicativos web de back-end. É aconselhável implantar conectores depois de concluir uma descoberta de aplicativos.
     * É recomendável que cada grupo de conectores tem pelo menos dois conectores para fornecer alta disponibilidade e escala. É ideal ter três conectores no caso de você precisará de uma máquina em qualquer ponto de serviço. Examine os [tabela de capacidade do conector](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning) para ajudar com a decisão de qual tipo de máquina para instalar os conectores no. O conector será maior no computador do buffer de mais e alto desempenho.

* **Configurações de acesso de rede**: Conectores de Proxy de aplicativo do AD do Azure [conectar-se ao Azure por meio de HTTPS (porta TCP 443) e HTTP (porta TCP 80)](application-proxy-add-on-premises-application.md). 

   * Conector de terminação tráfego TLS não é suportado e impedirá a conectores de estabelecer um canal seguro com seus respectivos pontos de extremidade de Proxy de aplicativo do Azure.

   * Evite todas as formas de inspeção de embutido em comunicações de saída TLS entre conectores e o Azure. Inspeção interna entre um aplicativo conector e o back-end é possível, mas pode degradar a experiência do usuário e como tal, não é recomendada.

   * Balanceamento de carga dos conectores em si também não é suportado ou até mesmo necessárias.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Considerações importantes antes de configurar o Proxy de aplicativo do Azure AD

Os seguintes requisitos principais devem ser atendidos para configurar e implementar o Proxy de aplicativo do Azure AD.

*  **Integração do Azure**: Antes de implantar o proxy de aplicativo, as identidades de usuário devem ser sincronizadas de um diretório local ou criadas diretamente em seus locatários do AD do Azure. A Sincronização de Identidades permite que o Azure AD pré-autentique os usuários antes de permitir acesso a eles aos aplicativos publicados pelo Proxy de Aplicativo e tenha as informações de identificador de usuário necessárias para realizar o SSO (logon único).

* **Requisitos de acesso condicionais**: Não recomendamos o uso de Proxy de aplicativo para acesso à intranet porque isso adiciona latência que terá impacto sobre os usuários. É recomendável usar o Proxy de aplicativo com pré-autenticação e políticas de acesso condicional para acesso remoto da internet.  Uma abordagem para fornecer acesso condicional para o uso da intranet é modernizar os aplicativos para que eles possam diretly autenticar com o AAD. Consulte a [recursos para migrar aplicativos para o AAD](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) para obter mais informações. 

* **Limites de serviço**: Para se proteger contra consumo excessivo de recursos por locatários individuais lá são os limites de limitação definida por aplicativo e de locatário. Para ver esses limites se referem [restrições e limites de serviço do AD do Azure](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions). Esses limites de limitação se baseiam em um parâmetro de comparação muito acima de volume de uso típico e fornece buffer suficiente para a maioria das implantações.

* **Certificado público**: Se você estiver usando nomes de domínio personalizado, você deve adquirir um certificado público emitido por uma autoridade de certificado confiável de não-Microsoft. Dependendo dos seus requisitos organizacionais, obtendo um certificado pode levar algum tempo e é recomendável começar o processo mais cedo possível. O Proxy de aplicativo do Azure oferece suporte ao padrão, [curinga](application-proxy-wildcard.md), ou certificados baseados em SAN.

* **Requisitos de domínio**: Logon único para seus aplicativos publicados usando a delegação restrita de Kerberos (KCD) requer que o servidor que executa o conector e o servidor que executa o aplicativo são ingressados no domínio e parte do mesmo domínio ou em domínios confiáveis.
Para obter informações detalhadas sobre o tópico, consulte [KCD para logon único](application-proxy-configure-single-sign-on-with-kcd.md) com Proxy de aplicativo. O serviço do conector é executado no contexto do sistema local e não deve ser configurado para usar uma identidade personalizada.

* **Registros DNS para URLs**

   * Antes de usar domínios personalizados no Proxy de aplicativo, você deve criar um registro CNAME no DNS público, permitindo que os clientes resolver a URL externa definido personalizado para o endereço de Proxy de aplicativo predefinido. Falha ao criar um registro CNAME para um aplicativo que usa um domínio personalizado impedirá que usuários remotos se conectem ao aplicativo. As etapas necessárias para adicionar registros CNAME podem variar de provedor para provedor DNS por isso, Aprenda como [gerenciar registros DNS e conjuntos de registros usando o portal do Azure](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal).

   * Da mesma forma, os hosts de conector devem ser capazes de resolver a URL interna de aplicativos que estão sendo publicados.

* **Funções e direitos administrativos**

   * **Instalação do conector** requer direitos de administrador local no servidor do Windows que está sendo instalado. Ele também requer um mínimo de um *administrador do aplicativo* função para autenticar e registrar a instância do conector para seu locatário do AD do Azure. 

   * **Administração e publicação de aplicativos** exigem o *administrador de aplicativos* função. Os administradores de aplicativos podem gerenciar todos os aplicativos no diretório, incluindo registros, configurações de SSO, usuário e as atribuições de grupo e licenciamento, as configurações de Proxy de aplicativo e consentimento. Ela não concede a capacidade de gerenciar o Acesso Condicional. O *administrador de aplicativos de nuvem* função tem todas as habilidades do administrador do aplicativo, exceto que ele não permite o gerenciamento das configurações de Proxy de aplicativo.

* **Licenciamento**: O Proxy de aplicativo está disponível por meio da assinatura do Azure AD Basic. Consulte a [página de preços do Active Directory do Azure](https://azure.microsoft.com/pricing/details/active-directory/) para obter uma lista completa de recursos e opções de licenciamento.  

### <a name="application-discovery"></a>Descoberta de aplicativo

Compile um inventário de todos os aplicativos dentro do escopo que estão sendo publicados por meio do Proxy de aplicativo por meio da coleta as seguintes informações:

| Tipo de informação| Informações a serem coletadas |
|---|---|
| Tipo de serviço| Por exemplo: SharePoint, SAP, CRM, API, aplicativo Web personalizado |
| Plataforma de aplicativos | Por exemplo:  Windows IIS, Apache no Linux, o Tomcat, o NGINX |
| Associação de domínio| Nome de domínio totalmente qualificado do servidor Web (FQDN) |
| Local do aplicativo | Onde o servidor web ou farm está localizado em sua infraestrutura |
| Acesso interno | A URL exata usada ao acessar o aplicativo internamente. <br> Se um farm, o tipo de balanceamento de carga está em uso? <br> Se o aplicativo desenha o conteúdo de fontes diferentes em si.<br> Determine se o aplicativo opera por meio de WebSockets. |
| Acesso externo | A solução de fornecedor que o aplicativo já está exposto externamente para. <br> A URL que você deseja usar para acesso externo. Se o SharePoint, certifique-se de mapeamentos alternativos de acesso são configurados por [neste guia](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings). Caso contrário, você precisará definir URLs externas. |
| Certificado público | Se usar um domínio personalizado, adquira um certificado com um nome de entidade correspondente. Se houver um certificado, observe o número de série e o local de onde ele pode ser obtido. |
| Tipo de autenticação| O tipo de autenticação compatíveis com o suporte do aplicativo, como básica, autenticação de integração do Windows, baseada em formulários, baseada em cabeçalho e declarações. <br>Se o aplicativo é configurado para ser executado sob uma conta de domínio específico, observe o nome de domínio totalmente qualificado (FQDN) da conta de serviço.<br> Se baseado em SAML, as URLs de resposta e o identificador. <br> Se baseada em cabeçalho, digite a solução de fornecedor e um requisito específico para lidar com a autenticação. |
| Nome do grupo de conector | O nome lógico para o grupo de conectores que será designado para fornecer o canal e o SSO para esse aplicativo de back-end. |
| Acesso de usuários/grupos | Os usuários ou grupos de usuário que serão concedidos acesso externo ao aplicativo. |
| Requisitos adicionais | Observe que qualquer acesso remoto adicional ou requisitos de segurança que devem ser fatorados na publicação do aplicativo. |

Você pode baixá-lo [planilha de inventário de aplicativo](https://aka.ms/appdiscovery) para inventariar seus aplicativos.

### <a name="define-organizational-requirements"></a>Definir requisitos organizacionais

A seguir estão as áreas para as quais você deve definir os requisitos de negócios da sua organização. Cada área contém exemplos de requisitos

 **Access**

* Os usuários remotos com ingressado no domínio ou usuários de dispositivos ingressados no Azure AD podem acessar aplicativos publicados com segurança com logon único contínuo-(SSO).

* Usuários remotos com dispositivos pessoais aprovados podem acessar com segurança os aplicativos publicados, desde que eles são registrados no MFA e registraram o aplicativo Microsoft Authenticator em seu telefone celular como método de autenticação.

**Governança** 

* Os administradores podem definir e monitorar o ciclo de vida de atribuições de usuário para aplicativos publicados pelo Proxy de aplicativo.

**Segurança**

* Somente os usuários atribuídos a aplicativos por meio da associação de grupo ou individualmente podem acessar esses aplicativos.

**Desempenho**

* Não há nenhuma degradação de desempenho do aplicativo comparado a acessar o aplicativo da rede interna.

**Experiência do usuário**

* Os usuários estejam cientes de como acessar seus aplicativos por meio de URLs de empresa familiar em qualquer plataforma de dispositivo.

**Auditoria**
* Os administradores são capazes de fazer auditoria da atividade de acesso do usuário.


### <a name="best-practices-for-a-pilot"></a>Práticas recomendadas para um piloto

Determine a quantidade de tempo e esforço necessários para um único aplicativo para acesso remoto com logon único (SSO) de comissão totalmente. Executar um piloto que considera sua descoberta inicial, a publicação e o teste geral para fazer isso. Usando um aplicativo web simples baseado no IIS que já está pré-configurado para autenticação integrada do Windows (IWA) ajuda a estabelecer uma linha de base, como esta instalação requer um mínimo de esforço para SSO e acesso remoto-piloto com êxito.

Os seguintes elementos de design devem aumentar o sucesso da sua implementação piloto diretamente em um locatário de produção.  

**Gerenciamento de conector**:  

* Conectores desempenham um papel fundamental no fornecimento do canal no local de seus aplicativos. Usando o **padrão** grupo de conectores é adequado para inicial teste piloto de aplicativos publicados antes de programação-los em produção. Aplicativos testados com êxito, em seguida, podem ser movidos para grupos de conectores de produção.

**Gerenciamento de aplicativos**:

* Sua força de trabalho é mais provável se lembrar de que uma URL externa é familiares e relevantes. Evite publicar seu aplicativo usando nosso sufixos msappproxy.net ou onmicrosoft.com predefinidos. Em vez disso, fornecer um familiar verificado domínio de nível superior, prefixado com um nome de host lógico, como *.com da intranet. < customers_domain >* .

* Restringir a visibilidade do ícone do aplicativo piloto em um grupo piloto, ocultando seu formato de ícone iniciar o portal do MyApps do Azure. Quando estiver pronto para produção você pode definir o escopo do aplicativo para seu respectivo público-alvo, no mesmo locatário pré-produção ou também publicar o aplicativo em seu locatário de produção.

**Configurações de logon único**: Algumas configurações de SSO têm dependências específicas que podem levar tempo para configurar, portanto, evite alterar controle atrasos garantindo que as dependências são resolvidos antecipadamente. Isso inclui hosts de conector para executar o SSO usando a delegação restrita de Kerberos (KCD) e tomando o cuidado de outras atividades demoradas de ingresso no domínio. Por exemplo, configurando uma instância de acesso de PING, se precisar de SSO baseada em cabeçalho.

**SSL entre o Host do conector e o aplicativo de destino**: A segurança é fundamental, para que o TLS entre os aplicativos de host e de destino do conector sempre deve ser usados. Especialmente se o aplicativo web está configurado para autenticação baseada em formulários (FBA), enquanto as credenciais do usuário, em seguida, são transmitidas com eficiência em texto não criptografado.

**Implementar de forma incremental e teste de cada etapa**. Conduza um teste funcional básico depois de publicar um aplicativo para garantir que todos os requisitos de negócios e de usuário são atendidos, seguindo as instruções abaixo:

1. Testar e validar o acesso geral para o aplicativo web com a pré-autenticação desabilitada.
2. Se for bem-sucedido, habilite pré-autenticação e atribua usuários e grupos. Testar e validar o acesso.
3. Em seguida, adicione o método SSO para seu aplicativo e teste novamente para validar o acesso.
4. Aplica o acesso condicional e políticas MFA conforme necessário. Testar e validar o acesso.

**Solucionando problemas de ferramentas**: Ao solucionar problemas, sempre comece validar acesso para o aplicativo publicado do navegador no host do conector e confirmar que o aplicativo funciona conforme o esperado. Mais simples do que o seu programa de instalação mais fácil para determinar a causa raiz, portanto, considere tentar reproduzir problemas com uma configuração mínima como o uso de apenas um único conector e nenhum SSO. Em alguns casos, a web, ferramentas de depuração como Fiddler da Telerik pode provar indispensável para solucionar problemas de acesso ou o conteúdo em aplicativos acessados por meio de um proxy. Fiddler também pode atuar como um proxy para ajudar a rastrear e depurar o tráfego para plataformas móveis como iOS e Android, e praticamente qualquer coisa que possa ser configurado para rotear por meio de um proxy. Consulte a [guia de solução](application-proxy-troubleshoot.md) para obter mais informações.

## <a name="implement-your-solution"></a>Implementar sua solução

### <a name="deploy-application-proxy"></a>Implantar o Proxy de aplicativo

As etapas para implantar o Proxy de aplicativo são abordadas neste [para adicionar um aplicativo local para acesso remoto](application-proxy-add-on-premises-application.md). Se a instalação não for bem-sucedida, selecione **solucionar problemas de Proxy de aplicativo** no portal ou use o guia de solução de problemas [para problemas de instalação do conector de agente de Proxy de aplicativo](application-proxy-connector-installation-problem.md).

### <a name="publish-applications-via-application-proxy"></a>Publicar aplicativos por meio do Proxy de aplicativo

Publicando aplicativos pressupõe que você ter atendido todos os pré-requisitos e que você tenha vários conectores mostrando como registrado e o Active Directory na página do Proxy de aplicativo.

Você também pode publicar aplicativos usando [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview).

Abaixo estão algumas práticas recomendadas a seguir ao publicar um aplicativo:

* **Usar grupos de conector**: Atribua um grupo de conectores que foram designado para a publicação de cada aplicativo respectivo. É recomendável que cada grupo de conectores tem pelo menos dois conectores para fornecer alta disponibilidade e escala. É ideal ter três conectores no caso de você precisará de uma máquina em qualquer ponto de serviço. Além disso, consulte [publicar aplicativos em redes separadas e locais usando grupos de conectores](application-proxy-connector-groups.md) para ver como você também pode usar grupos de conectores para segmentar seus conectores pela rede ou local.

* **Definir tempo limite de aplicativo de back-end**: Essa configuração é útil em cenários em que o aplicativo pode exigir mais de 75 segundos processar uma transação do cliente. Por exemplo, quando um cliente envia uma consulta para um aplicativo web que atua como um front-end para um banco de dados. O front-end envia essa consulta para seu servidor de banco de dados de back-end e aguarda uma resposta, mas, quando que ele recebe uma resposta, o lado do cliente da conversa expire. Definir o tempo limite ao longo fornece 180 segundos para transações mais tempo para ser concluída.

* **Usar tipos de Cookie apropriado**

   * **Cookie HTTP-Only**: Fornece segurança adicional fazendo com que o Proxy de aplicativo incluir o sinalizador HTTPOnly em cabeçalhos de resposta HTTP set-cookie. Essa configuração ajuda a mitigar as explorações como intersite XSS (script). Deixe este conjunto como não para agentes de clientes/usuários que precisam de acesso para o cookie de sessão. Por exemplo, o cliente RDP/MTSC conectado a um Gateway de área de trabalho remota publicado por meio do Proxy de aplicativo.

   * **Proteger o Cookie**: Quando um cookie é definido com o atributo Secure, o agente do usuário (aplicativo do lado do cliente) incluirá apenas o cookie em solicitações HTTP se a solicitação é transmitida por um canal protegido de TLS. Isso ajuda a reduzir o risco de um cookie a ser comprometido por canais de texto não criptografado, portanto, deve ser habilitado.

   * **Cookie persistente**: Permite que o cookie de sessão do Proxy de aplicativo persistam entre fechamentos de navegador por permanecendo válidos até que ela expira ou é excluída. Usado para cenários em que um aplicativo avançado, como o office acessa um documento dentro de um aplicativo web publicado, sem que o usuário que está sendo solicitado novamente para autenticação. Habilitar com cuidado, no entanto, como cookies persistentes pode, por fim, deixar um serviço correm o risco de acesso não autorizado, se não usado em conjunto com outros controles de compensação. Essa configuração deve ser usada somente para aplicativos mais antigos que não é possível compartilhar cookies entre processos. É melhor atualizar seu aplicativo para manipular o compartilhamento de cookies entre processos em vez de usar essa configuração.

* **Converter URLs nos cabeçalhos**: Habilite esta opção para cenários em que o DNS interno não pode ser configurado para corresponder ao namespace de público da organização (também conhecidas como DNS dividido). A menos que seu aplicativo requer o cabeçalho de host original na solicitação do cliente, deixe esse valor é definido como Sim. A alternativa é fazer com que o conector usar o FQDN na URL interna para roteamento de tráfego real e o FQDN na URL externa, como o cabeçalho de host. Na maioria dos casos essa alternativa deve permitir que o aplicativo funcione como de costume, quando acessados remotamente, mas os usuários perderem os benefícios de ter uma correspondência dentro e fora da URL.

* **Traduzir URLs no corpo do aplicativo**: Ative a translação de link do corpo do aplicativo para um aplicativo quando desejar que os links do aplicativo a ser traduzido em respostas de volta ao cliente. Se habilitada, essa função fornece uma tentativa de melhor esforço converter todos os links internos que o Proxy de aplicativo localiza em HTML e CSS respostas sendo retornadas aos clientes. É útil quando a publicação de aplicativos que contêm absoluto embutido em código ou links de nome curto do NetBIOS no conteúdo ou aplicativos com o conteúdo que contém links para outros aplicativos locais.

Para cenários em que um aplicativo publicado é vinculado a outra de aplicativos publicados, habilite a translação de link para cada aplicativo para que você tenha controle sobre a experiência do usuário no nível por aplicativo.

Por exemplo, suponha que você tem três aplicativos publicados por meio do Proxy de Aplicativo que estão vinculados entre si: Benefícios, despesas e viagem, além de um quarto aplicativo, comentários, que não não publicado por meio do Proxy de aplicativo.

![Figura 1](media/App-proxy-deployment-plan/link-translation.png)

Quando você habilita a translação de link para o aplicativo benefícios, os links para despesas e viagem são redirecionados para as URLs externas para esses aplicativos, para que os usuários que acessam os aplicativos de fora da rede corporativa podem acessá-los. Links de despesas e viagem para benefícios não funcionam porque a conversão de link ainda não foi habilitada para esses dois aplicativos. O link para comentários não é redirecionado porque não há nenhuma URL externa, para que os usuários usando o aplicativo de benefícios não será capazes de acessar o aplicativo de comentários de fora da rede corporativa. Obtenha informações detalhadas sobre [vincular tradução e outras opções de redirecionamento](application-proxy-configure-hard-coded-link-translation.md).

### <a name="access-your-application"></a>Acessar seu aplicativo

Existem várias opções para gerenciar o acesso ao Proxy de aplicativo recursos publicados, portanto, escolha o mais apropriado para suas necessidades de determinado cenário e a escalabilidade. Abordagens comuns incluem: usando grupos locais que estão sendo sincronizados por meio do Azure AD Connect, criação de grupos dinâmicos no AD do Azure com base em atributos de usuário, usando grupos de autoatendimento que são gerenciados pelo proprietário do recurso ou uma combinação de tudo isso. Consulte os recursos vinculados para os benefícios de cada um.

A maneira mais simples de atribuir acesso de usuários a um aplicativo está indo para o **usuários e grupos** opções do painel esquerdo do seu aplicativo publicado e atribuindo diretamente a grupos ou indivíduos.

![Figura 24](media/App-proxy-deployment-plan/add-user.png)

Você também pode permitir que os usuários para acesso de autoatendimento para seu aplicativo ao atribuir um grupo que não são atualmente um membro da e configurando as opções de autoatendimento.

![Figura 25](media/App-proxy-deployment-plan/allow-access.png)

Se habilitada, os usuários, em seguida, ser capazes de fazer logon para o acesso de solicitação e de portal MyApps e ser automaticamente aprovado e adicionado ao grupo de autoatendimento já permitido ou precisam de aprovação de um aprovador designado.

Usuários convidados também podem ser [convidado para acessar a aplicativos internos publicados por meio do Proxy de aplicativo por meio do Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker).

Para aplicativos de locais que são normalmente acessíveis anonimamente, que exigem nenhuma autenticação, talvez você prefira desabilitar a opção localizada na caixa de diálogo **propriedades**.

![Figura 26](media/App-proxy-deployment-plan/assignment-required.png)


Deixar essa opção definida como não permite aos usuários acessar o aplicativo local por meio do Proxy de aplicativo do Azure AD sem permissões, portanto, use com cuidado.

Depois que seu aplicativo é publicado, ele deve ser acessível digitando a URL externa em um navegador ou seu ícone no [ https://myapps.microsoft.com ](https://myapps.microsoft.com/).

### <a name="enable-pre-authentication"></a>Habilitar pré-autenticação

Verifique se seu aplicativo é acessível por meio do Proxy de aplicativo acessando-o via a URL externa. 

1. Navegue até **Azure Active Directory** > **Aplicativos empresariais** > **Todos os aplicativos** e escolha o aplicativo que deseja gerenciar.

2. Selecione **Proxy de Aplicativo**.

3. No **pré-autenticação** campo, use a lista suspensa para selecionar **Azure Active Directory**e selecione **salvar**.

Com pré-autenticação habilitado, Azure AD será desafiar os usuários primeiro para autenticação e se o logon único estiver configurado, em seguida, o aplicativo de back-end será também Verifique se o usuário antes de conceder acesso ao aplicativo. Também alterando o modo de pré-autenticação de passagem para o Azure AD configura a URL externa com HTTPS, portanto, qualquer aplicativo inicialmente configurado para HTTP agora será protegido com HTTPS.

### <a name="enable-single-sign-on"></a>Habilitar Logon Único

SSO fornece a melhor experiência do usuário possíveis e a segurança, pois os usuários só precisam entrar uma vez ao acessar o Azure AD. Quando um usuário fez uma pré-autenticação, SSO é executado por meio da autenticação o conector de Proxy de aplicativo para o aplicativo no local, em nome do usuário. O aplicativo de back-end processa o logon como se fosse o usuário em si. 

Escolhendo a **passagem** opção permite que os usuários acessem o aplicativo publicado sem ter de se autenticar no AD do Azure.

Executando o SSO só é possível que se o Azure AD possa identificar o usuário que está solicitando acesso a um recurso, para que seu aplicativo deve ser configurado para autenticar previamente os usuários com o Azure AD no acesso para que o SSO para função, caso contrário, as opções de SSO serão desabilitadas.

Leia [logon único para aplicativos no Azure AD](what-is-single-sign-on.md) para ajudá-lo a escolher o método mais apropriado de SSO, ao configurar seus aplicativos.

###  <a name="working-with-other-types-of-applications"></a>Trabalhando com outros tipos de aplicativos

O Proxy de aplicativo do Azure AD também pode dar suporte a aplicativos que foram desenvolvidos para usar nossa biblioteca de autenticação do AD do Azure ([ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)) ou biblioteca de autenticação da Microsoft ([MSAL](https://azure.microsoft.com/blog/start-writing-applications-today-with-the-new-microsoft-authentication-sdks/)). Ele dá suporte a aplicativos cliente nativos pelo consumo do Azure AD recebidas nas informações de cabeçalho de solicitação de cliente para executar pré-autenticação em nome dos usuários de tokens emitido.

Leia [publicação de aplicativos cliente móveis e nativos](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client) e [aplicativos baseados em declarações](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps) para saber mais sobre as configurações disponíveis do Proxy de aplicativo.

### <a name="use-conditional-access-to-strengthen-security"></a>Use o acesso condicional para reforçar a segurança

Segurança de aplicativo requer um conjunto avançado de recursos de segurança que podem proteger contra e responder a ameaças complexas locais e na nuvem. Os invasores geralmente obtém acesso à rede corporativa por meio de credenciais de usuário roubadas, padrão ou fraca.  Segurança baseada em identidades do Microsoft reduz o uso de roubo de credenciais Gerenciando e protegendo identidades privilegiadas e sem privilégios.

Os recursos a seguir podem ser usados para dar suporte ao Proxy de aplicativo do Azure AD:

* Usuário e acesso condicional baseado no local: Manter os dados confidenciais protegidos, limitando o acesso de usuário com base em localização geográfica ou um endereço IP com [políticas de acesso condicional com base no local](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

* Acesso condicional baseado em dispositivo: Certifique-se somente a dispositivos registrados, aprovados e em conformidade podem acessar dados corporativos com [acesso condicional baseado em dispositivo](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications).

* Acesso condicional baseado em aplicativo: Trabalho não precisa parar quando um usuário não está na rede corporativa. [Proteger o acesso a aplicativos corporativos de nuvem e locais](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) e manter o controle de acesso condicional.

* Acesso condicional com base em risco: Proteger seus dados contra ataques de hackers com um [política de acesso condicional com base em risco](https://www.microsoft.com/cloud-platform/conditional-access) que podem ser aplicadas a todos os aplicativos e todos os usuários, se local ou na nuvem.

* Painel de acesso do Azure AD: Com o seu serviço de Proxy de aplicativo implantado e aplicativos publicados com segurança, oferecem aos usuários um hub de simple para descobrir e acessar todos os seus aplicativos. Aumente a produtividade com recursos de autoatendimento, como a capacidade de solicitar acesso a novos aplicativos e grupos ou gerenciar o acesso a esses recursos em nome de outros, por meio de [painel de acesso](https://aka.ms/AccessPanelDPDownload).

## <a name="manage-your-implementation"></a>Gerenciar sua implementação

### <a name="required-roles"></a>Funções necessárias

Microsoft defende o princípio de conceder o privilégio mínimo possível para executar as tarefas necessárias com o Azure AD. [Examinar as diferentes funções do Azure que estão disponíveis](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) e escolher o correto para atender às necessidades de cada pessoa. Algumas funções talvez precise ser aplicados temporariamente e removidos após a conclusão da implantação.

| Função de negócios| Tarefas de negócios| Funções do Azure AD |
|---|---|---|
| Ajudar o administrador de suporte técnico | Normalmente é limitado a qualificação de usuário final relatados problemas e executar tarefas limitadas, como alterar as senhas dos usuários, invalidando os tokens de atualização e monitoramento de integridade do serviço. | Administrador de assistência técnica |
| Administrador de identidade| Problemas relacionados ao entrada do AD do Azure de leitura em relatórios e logs de auditoria para depurar o Proxy de aplicativo.| Leitor de segurança |
| Proprietário do aplicativo| Criar e gerenciar todos os aspectos de aplicativos corporativos, registros de aplicativo e as configurações de proxy de aplicativo.| Administrador de aplicativo |
| Administrador de infraestrutura | Proprietário de substituição do certificado | Administrador de aplicativo |

Minimizando o número de pessoas que têm acesso a informações seguras ou recursos o ajudará a reduzir a chance de obter acesso não autorizado ou um usuário autorizado afetar acidentalmente um recurso confidencial de um ator mal-intencionado. 
 
No entanto, os usuários ainda precisam executar operações privilegiadas do dia a dia, portanto, impondo just-in-time (JIT) com base [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) políticas para fornecer sob demanda com privilégios de acesso aos recursos do Azure e o Azure AD é nosso recomendado abordagem para gerenciar o acesso administrativo com eficiência e auditoria.

### <a name="reporting-and-monitoring"></a>Relatórios e monitoramento

Azure AD pode fornecer informações adicionais sobre uso e integridade operacional por meio de relatórios e logs de auditoria de provisionamento de usuário da sua organização. 

#### <a name="application-audit-logs"></a>Logs de auditoria de aplicativo

Esses logs fornecem informações detalhadas sobre logons para aplicativos configurados com o Proxy de aplicativo e o dispositivo e o usuário acessar o aplicativo. Os logs de auditoria estão localizados no portal do Azure e na API de auditoria para exportação.

#### <a name="windows-event-logs-and-performance-counters"></a>Contadores de desempenho e logs de eventos do Windows

Conectores têm de administrador e sessão logs. Os logs de administrador incluem eventos de chave e seus erros. Os logs de sessão incluem todas as transações e seus detalhes de processamento. Logs e contadores estão localizados nos Logs de eventos do Windows e siga este [tutorial para configurar fontes de dados de log de eventos no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events).

### <a name="troubleshooting-guide-and-steps"></a>Guia e as etapas de solução de problemas

Saiba mais sobre problemas comuns e como resolvê-los com nosso guia para [solução de problemas](application-proxy-troubleshoot.md) mensagens de erro. 

Os artigos a seguir abordam os cenários comuns que também podem ser usados para criar guias de solução de problemas de sua organização de suporte. 

* [Problema ao exibir página do aplicativo](application-proxy-page-appearance-broken-problem.md)
* [O carregamento do aplicativo é muito longo](application-proxy-page-load-speed-problem.md)
* [Os links na página de aplicativo não funcionam](application-proxy-page-links-broken-problem.md)
* [Quais portas abrir para meu aplicativo](application-proxy-connectivity-ports-how-to.md)
* [Nenhum conector útil em um grupo de conectores para meu aplicativo](application-proxy-connectivity-no-working-connector.md)
* [Configurar no portal de administração](application-proxy-config-how-to.md)
* [Configurar logon único para meu aplicativo](application-proxy-config-sso-how-to.md)
* [Problema ao criar um aplicativo no portal de administração](application-proxy-config-problem.md)
* [Configurar a Delegação Restrita do Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Configurar com PingAccess](application-proxy-back-end-ping-access-how-to.md)
* [Não é possível acessar este erro de aplicativo corporativo](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Problema ao instalar o Conector de Agente de Proxy do Aplicativo](application-proxy-connector-installation-problem.md)
* [Problema de entrada](application-sign-in-problem-on-premises-application-proxy.md)
