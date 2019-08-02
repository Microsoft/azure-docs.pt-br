---
title: Publicar aplicativos locais com o Proxy de Aplicativo do Azure AD
description: Entenda por que usar o Proxy de Aplicativo para publicar aplicativos Web locais externamente para usuários remotos. Saiba mais sobre a arquitetura, conectores, métodos de autenticação e benefícios de segurança do Proxy de Aplicativo.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 05/31/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f23b20d460952ae582c292c8015851b9dc2ea98
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108171"
---
# <a name="using-azure-ad-application-proxy-to-publish-on-premises-apps-for-remote-users"></a>Usar o Proxy de Aplicativo do Azure AD para publicar aplicativos locais para usuários remotos

O Azure Active Directory (Azure AD) oferece vários recursos para proteger usuários, aplicativos e dados na nuvem e locais. Em particular, o recurso Proxy de Aplicativo do Azure AD pode ser implementado por profissionais de TI que desejam publicar aplicativos Web locais externamente. Os usuários remotos que precisarem acessar aplicativos internos poderão acessá-los de maneira segura.

A capacidade de acessar com segurança aplicativos internos do lado de fora da rede torna-se ainda mais crítica no ambiente de trabalho moderno. Com cenários como BYOD (Traga seu próprio dispositivo) e dispositivos móveis, os profissionais de TI são desafiados a atingir duas metas:

* Capacitar os usuários finais para serem produtivos sempre e em qualquer lugar
* Proteger ativos corporativos a todo momento

Muitas organizações acreditam que eles estão controlados e protegidos quando existem recursos dentro dos limites das redes corporativas. No entanto, no local de trabalho digital atual, esse limite foi expandido com recursos e dispositivos móveis gerenciados e serviços na nuvem. Agora você precisa gerenciar a complexidade da proteção das identidades e dados de seus usuários armazenados nos dispositivos e aplicativos.

Talvez você já esteja usando o Azure AD para gerenciar usuários na nuvem que precisam acessar o Office 365 e outros aplicativos SaaS, além de aplicativos Web hospedados no local. Se você já tiver o Azure AD, poderá aproveitá-lo como um plano de controle para permitir acesso transparente e seguro aos aplicativos locais. Ou, talvez, você ainda esteja considerando uma mudança para a nuvem. Se for esse o caso, você pode começar sua jornada para a nuvem implementando o Proxy de Aplicativo e dar o primeiro passo para a criação de uma forte base de identidade.

Embora não seja abrangente, a lista abaixo ilustra algumas das coisas que você pode ativar implementando o Proxy de Aplicativo em um cenário híbrido de coexistência:

* Publicar aplicativos Web locais externamente de maneira simplificada sem uma DMZ
* Dar suporte ao logon único (SSO) em dispositivos, recursos e aplicativos na nuvem e locais
* Dar suporte à autenticação multifator para aplicativos em nuvem e locais
* Aproveitar imediatamente os recursos de nuvem com a segurança do Microsoft Cloud
* Centralizar o gerenciamento de conta de usuário
* Centralizar o controle de identidade e segurança
* Adicionar ou remover automaticamente o acesso de usuário para aplicativos com base na associação de grupo

Este artigo explica como o Azure AD e o Proxy de Aplicativo oferecem aos usuários remotos uma experiência de logon único (SSO). Os usuários se conectam com segurança a aplicativos locais sem uma VPN ou servidores de hospedagem dupla e regras de firewall. Este artigo ajuda você a entender como o Proxy de Aplicativo traz os recursos e as vantagens de segurança da nuvem para os aplicativos Web locais. Ele também descreve a arquitetura e as topologias possíveis.

## <a name="remote-access-in-the-past"></a>Acesso remoto no passado

Anteriormente, seu plano de controle para proteger recursos internos contra invasores e facilitar o acesso de usuários remotos estava todo na rede DMZ ou de perímetro. No entanto, as soluções de VPN e proxy reverso implantadas na DMZ usadas por clientes externos para acessar recursos corporativos não são adequadas para o mundo da nuvem. Eles normalmente têm as seguintes desvantagens:

* Custos de hardware
* Manutenção da segurança (aplicação de patches, monitoramento de portas, etc.)
* Autenticação de usuários na borda
* Autenticação de usuários em servidores Web na rede de perímetro
* Manter o acesso VPN para usuários remotos com a distribuição e a configuração do software do cliente VPN. Além disso, manter servidores associados ao domínio na DMZ que podem estar vulneráveis a ataques externos.

No atual mundo da nuvem, o Azure AD é o recurso ideal para controlar quem e o que entra na sua rede. O Proxy de Aplicativo do Azure AD se integra à autenticação moderna e às tecnologias baseadas na nuvem, como aplicativos SaaS e provedores de identidade. Essa integração permite que os usuários acessem aplicativos de qualquer lugar. O Proxy de Aplicativo não só é mais adequado para o local de trabalho digital atual como também é mais seguro que as soluções de VPN e proxy reverso. Além disso, é mais fácil de implementar. Os usuários remotos podem acessar seus aplicativos locais da mesma forma que acessam o O365 e outros aplicativos SaaS integrados com o Azure AD. Você não precisa alterar ou atualizar seus aplicativos para que funcionem com o Proxy de Aplicativo. Além disso, o Proxy de Aplicativo não exige que você abra conexões de entrada por meio do firewall. Com o Proxy de Aplicativo, basta definir e pronto.

## <a name="the-future-of-remote-access"></a>O futuro do acesso remoto

No espaço de trabalho digital atual, os usuários trabalham em qualquer lugar com vários dispositivos e aplicativos. A única constante é a identidade do usuário. É por isso que, atualmente, o primeiro passo para se ter uma rede segura é usar os recursos de [gerenciamento de identidades do Azure AD](https://docs.microsoft.com/azure/security/security-identity-management-overview) como seu plano de controle de segurança. Um modelo que usa a identidade como plano de controle geralmente é composto pelos seguintes componentes:

* Um provedor de identidade para controlar os usuários e informações relacionadas ao usuário.
* Um diretório de dispositivos para manter uma lista de dispositivos que têm acesso aos recursos corporativos. Esse diretório inclui informações sobre dispositivo correspondente (por exemplo, tipo de dispositivo, integridade etc.).
* O serviço de avaliação de políticas para determinar se um usuário e um dispositivo estão em conformidade com a política definida pelos administradores de segurança.
* A capacidade de conceder ou negar acesso aos recursos organizacionais.

Com o Proxy de Aplicativo, o Azure AD controla os usuários que precisam acessar aplicativos Web publicados no local e na nuvem. Ele oferece um ponto de gerenciamento central para esses aplicativos. Embora não seja necessário, também é recomendável habilitar o acesso condicional do Azure Active Directory. Definindo as condições de como os usuários se autenticam e obtêm acesso, você garantirá que as pessoas certas terão acesso aos aplicativos.

**Observação:** É importante entender que o Proxy de Aplicativo do Azure AD destina-se a ser um substituto da VPN ou proxy reverso para usuários de roaming (ou remotos) que precisam de acesso a recursos internos. Não é destinado a usuários internos na rede corporativa. Os usuários internos que usam desnecessariamente o Proxy de Aplicativo podem apresentar problemas de desempenho inesperados e indesejados.

![Azure Active Directory e todos os seus aplicativos](media/what-is-application-proxy/azure-ad-and-all-your-apps.png)

### <a name="an-overview-of-how-app-proxy-works"></a>Uma visão geral de como o Proxy de Aplicativo funciona

O Proxy de Aplicativo é um serviço do Azure AD que você configura no portal do Azure. Ele permite que você publique o ponto de extremidade de uma URL HTTP/HTTPS pública externa na Nuvem do Azure que se conecta a uma URL do servidor de aplicativos interno em sua organização. Esses aplicativos Web locais podem ser integrados ao Azure AD para dar suporte ao logon único. Os usuários finais podem acessar seus aplicativos Web locais da mesma forma que acessam o Office 365 e outros aplicativos SaaS.

Os componentes desse recurso incluem o serviço Proxy de Aplicativo, executado na nuvem, o conector do Proxy de Aplicativo, que é um agente leve executado em um servidor local, e o Azure AD, que é o provedor de identidade. Todos os três componentes trabalham em conjunto para fornecer ao usuário uma experiência de logon único para acessar aplicativos Web locais.

Após o logon, os usuários externos podem acessar os aplicativos Web locais usando uma URL conhecida ou o [painel de acesso MyApps](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access) dos dispositivos de área de trabalho ou dispositivos iOS/MAC. Por exemplo, o Proxy de Aplicativo pode fornecer acesso remoto e o logon único para a Área de Trabalho Remota, o SharePoint, o Tableau, o Qlik, o Outlook na Web e aplicativos LOB (linha de negócios).

![Arquitetura do Proxy de Aplicativo do Azure AD](media/what-is-application-proxy/azure-ad-application-proxy-architecture.png)

### <a name="authentication"></a>Authentication

Há várias maneiras de configurar um aplicativo para logon único e o método escolhido depende da autenticação que seu aplicativo usa. O Proxy de Aplicativo dá suporte aos seguintes tipos de aplicativos:

* Aplicativos Web
* APIs Web que você deseja expor a aplicativos avançados em diferentes dispositivos
* Aplicativos hospedados por trás de um Gateway de Área de Trabalho Remota
* Aplicativos cliente avançados que são integrados com a ADAL (Biblioteca de autenticação do Active Directory)

O Proxy de Aplicativo funciona com aplicativos que usam o seguinte protocolo de autenticação nativa:

* **[IWA (Autenticação Integrada do Windows)](application-proxy-configure-single-sign-on-with-kcd.md).** Para IWA, os conectores de Proxy de Aplicativo usam a KCD (Delegação Restrita de Kerberos) para autenticar os usuários no aplicativo do Kerberos.

O Proxy de Aplicativo também é compatível com os seguintes protocolos de autenticação com integração de terceiros ou em cenários específicos de configuração:

* [**Autenticação baseada em cabeçalho**](application-proxy-configure-single-sign-on-with-ping-access.md). Esse método de logon usa um serviço de autenticação de terceiros chamado PingAccess e é usado quando o aplicativo usa cabeçalhos para autenticações. Nesse cenário, a autenticação é manipulada pelo PingAccess.
* [**Autenticação baseada em senha ou formulários**](application-proxy-configure-single-sign-on-password-vaulting.md). Com esse método de autenticação, os usuários entram no aplicativo com um nome de usuário e uma senha na primeira vez que o acessam. Após o primeiro logon, o Azure AD fornece o nome de usuário e a senha ao aplicativo. Nesse cenário, a autenticação é manipulada pelo Azure AD.
* [**Autenticação de SAML**](application-proxy-configure-single-sign-on-on-premises-apps.md). O logon único baseado em SAML é compatível com aplicativos que usam protocolos SAML 2.0 ou WS-Federation. Com o logon único SAML, o Azure AD realiza a autenticação do aplicativo usando a conta do Azure AD do usuário.

Para obter mais informações sobre métodos com suporte, consulte [Escolhendo um método de logon único](what-is-single-sign-on.md#choosing-a-single-sign-on-method).

### <a name="security-benefits"></a>Benefícios de segurança

A solução de acesso remoto oferecida pelo Proxy de Aplicativo e o Azure AD dá suporte a várias vantagens de segurança que os clientes podem aproveitar, incluindo:

* **Acesso autenticado**. O Proxy de Aplicativo é mais adequado para publicar aplicativos com [pré-autenticação](application-proxy-security.md#authenticated-access) para garantir que somente conexões autenticadas atinjam sua rede. Para aplicativos publicados com pré-autenticação, não é permitido que nenhum tráfego passe para seu ambiente local pelo serviço Proxy de Aplicativo sem um token válido. A pré-autenticação, por sua própria natureza, bloqueia um número significativo de ataques direcionados, pois somente identidades autenticadas podem acessar o aplicativo de back-end.
* **Acesso condicional**. Controles de política mais rígidos podem ser aplicados antes que as conexões com sua rede sejam estabelecidas. Com acesso condicional, você pode definir restrições em relação ao que o tráfego pode acessar nos seus aplicativos de back-end. Crie políticas que restrinjam entradas com base no local, na força da autenticação e no perfil de risco do usuário. À medida que o acesso condicional evolui, mais controles são adicionados para fornecer segurança adicional, como a integração com o Microsoft Cloud App Security (MCAS). A integração com o MCAS permite configurar um aplicativo local para [monitoramento em tempo real](application-proxy-integrate-with-microsoft-cloud-application-security.md) aproveitando o acesso condicional para monitorar e controlar sessões em tempo real com base em políticas de acesso condicional.
* **Encerramento de tráfego**. Todo o tráfego para o aplicativo de back-end é encerrado no serviço do Proxy de Aplicativo na nuvem enquanto a sessão é restabelecida com o servidor de back-end. Essa estratégia de conexão significa que os servidores de back-end não são expostos ao tráfego HTTP direto. Eles têm mais proteção contra ataques DoS (ataque de negação de serviço) direcionados já que seu firewall não está sob ataque.
* **Todo acesso é de saída**. Os conectores do Proxy de Aplicativo só usam conexões de saída para o serviço do Proxy de Aplicativo na nuvem pelas portas 80 e 443. Sem conexões de entrada, não é necessário abrir portas de firewall para conexões de entrada ou componentes na DMZ. Todas as conexões são de saída e em um canal seguro.
* **Inteligência baseada na Análise de Segurança e Machine Learning (ML)** . Como faz parte do Azure Active Directory, o Proxy de Aplicativo pode aproveitar a [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview) (exige o [licenciamento Premium P2](https://azure.microsoft.com/pricing/details/active-directory/)). O Azure AD Identity Protection combina a inteligência de segurança de aprendizado de máquina com feeds de dados da [Unidade de Crimes Digitais](https://news.microsoft.com/stories/cybercrime/index.html) e [Microsoft Security Response Center](https://www.microsoft.com/msrc) da Microsoft para identificar proativamente contas comprometidas. O Identity Protection oferece proteção em tempo real contra logons de alto risco. Ele leva em consideração fatores como acessos de dispositivos infectados por meio de redes anônimas ou de locais atípicos e improváveis para aumentar o perfil de risco de uma sessão. Este perfil de risco é usado para proteção em tempo real. Muitos desses relatórios e eventos já estão disponíveis por meio de uma API para integração aos sistemas SIEM.

* **Acesso remoto como um serviço**. Você não precisa se preocupar com a manutenção e a aplicação de patches em servidores locais para habilitar o acesso remoto. O Proxy de Aplicativo é um serviço em escala de Internet da Microsoft. Sendo assim, você sempre obterá os patches e as atualizações de segurança mais recentes. A não aplicação de patches no software ainda é responsável por um grande número de ataques. Segundo o Departamento de Segurança Interna, até [85% dos ataques direcionados podem ser evitados](https://www.us-cert.gov/ncas/alerts/TA15-119A). Com este modelo de serviço, você já não precisa carregar a responsabilidade de ter que gerenciar servidores de borda e se esforçar para corrigi-los sempre que necessário.

* **Integração com o Intune**. Com o Intune, o tráfego corporativo é roteado separadamente do tráfego pessoal. O Proxy de Aplicativo garante que o tráfego corporativo seja autenticado. O [Proxy de Aplicativo e o Intune Managed Browser](https://docs.microsoft.com/intune/app-configuration-managed-browser#how-to-configure-application-proxy-settings-for-protected-browsers) também podem ser usados em conjunto para permitir que usuários remotos acessem sites internos com segurança em dispositivos iOS e Android.

### <a name="roadmap-to-the-cloud"></a>Roteiro para a nuvem

Outro benefício importante da implementação do Proxy de Aplicativo é estender o Azure AD ao ambiente local. Na verdade, a implementação do Proxy de Aplicativo é uma etapa importante na mudança de sua organização e aplicativos para a nuvem. Com a migração para a nuvem, afastando-se da autenticação local, você reduz o espaço ocupado no local e usa os recursos de gerenciamento de identidades do Azure AD como plano de controle. Sem atualizações ou com atualizações mínimas de aplicativos existentes, você tem acesso a recursos da nuvem, como logon único, autenticação multifator e gerenciamento central. É fácil instalar os componentes necessários no Proxy de Aplicativo para estabelecer uma estrutura de acesso remoto. E, migrando para a nuvem, você terá acesso aos recursos, atualizações e funcionalidades mais recentes do Azure AD, como alta disponibilidade e recuperação de desastre.

Para saber mais sobre como migrar seus aplicativos para o Azure AD, confira o white paper [Migrar seus aplicativos para o Azure Active Directory](https://aka.ms/migrateapps/whitepaper).

## <a name="architecture"></a>Arquitetura

O diagrama a seguir ilustra, de forma geral, como os serviços de autenticação do Azure AD e o Proxy de Aplicativo trabalham em conjunto para fornecer logon único a aplicativos locais para usuários finais.

![Fluxo de autenticação do Proxy de Aplicativo do Azure AD](media/what-is-application-proxy/azure-ad-application-proxy-authentication-flow.png)

1. Após o usuário ter acessado o aplicativo por meio de um ponto de extremidade, ele será redirecionado para a página de entrada do Azure AD. Se você tiver configurado políticas de acesso condicional, as condições específicas serão verificadas no momento para garantir que você esteja em conformidade com os requisitos de segurança da organização.
2. Após uma entrada com êxito, o Azure AD envia um token para o dispositivo cliente do usuário.
3. O cliente agora envia o token para o serviço Proxy de Aplicativo que recuperará o nome UPN e o SPN (nome da entidade de segurança) do token.
4. O Proxy de Aplicativo encaminha a solicitação que é escolhida pelo [conector](application-proxy-connectors.md) do Proxy de Aplicativo.
5. O conector executa as autenticações adicionais necessárias em nome do usuário (*Opcional, dependendo do método de autenticação*), solicita o ponto de extremidade interno do servidor de aplicativos e envia a solicitação ao aplicativo local.
6. A resposta do servidor de aplicativo é enviada pelo conector para o serviço Proxy de Aplicativo.
7. A resposta é enviada do serviço Proxy de Aplicativo para o usuário.

|**Componente**|**Descrição**|
|:-|:-|
|Ponto de extremidade|O ponto de extremidade é uma URL ou um  [portal do usuário final](end-user-experiences.md). Os usuários podem acessar aplicativos enquanto estão fora de sua rede ao acessar uma URL externa. Usuários dentro de sua rede podem acessar o aplicativo por meio de uma URL ou de um portal do usuário final. Quando os usuários acessam um desses pontos de extremidade, eles são autenticados no Azure AD e, em seguida, são direcionados por meio do conector até o aplicativo local.|
|AD do Azure|O Azure AD executa a autenticação usando o diretório do locatário armazenado na nuvem.|
|Serviço do Proxy de Aplicativo|Esse serviço de Proxy de Aplicativo é executado na nuvem como parte do Azure AD. Ele passa o token de logon do usuário para o Conector de Proxy de Aplicativo. O Proxy de Aplicativo encaminha qualquer cabeçalho acessível na solicitação e define os cabeçalhos de acordo com seu protocolo para o endereço IP do cliente. Se a solicitação de entrada para o proxy já tiver esse cabeçalho, o endereço IP do cliente será adicionado ao final da lista separada por vírgulas que é o valor do cabeçalho.|
|Conector do Proxy de Aplicativo|O conector é um agente leve executado em um Windows Server na sua rede. Ele gerencia a comunicação entre o serviço de Proxy de Aplicativo na nuvem e o aplicativo local. O conector usa apenas conexões de saída, portanto você não precisa abrir portas de entrada nem colocar nada na DMZ. Os conectores são sem monitoração de estado e efetuam pull de informações da nuvem conforme necessário. Para saber mais sobre conectores, como eles fazem o balanceamento de carga e a autenticação, confira  [Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](application-proxy-connectors.md).|
|AD (Active Directory)|O Active Directory é executado localmente para realizar a autenticação para contas de domínio. Quando o logon único está configurado, o conector se comunica com o AD para realizar qualquer autenticação adicional necessária.|
|Aplicativo local|Por fim, o usuário é capaz de acessar um aplicativo local.|

O Proxy de Aplicativo do Azure AD é formado pelo serviço Proxy de Aplicativo baseado em nuvem e um conector local. O conector escuta solicitações do serviço Proxy de Aplicativo e trata das conexões para os aplicativos internos. É importante observar que todas as comunicações ocorrem por SSL e sempre originam-se no conector para o serviço Proxy de Aplicativo. Ou seja, as comunicações são apenas de saída. O conector usa um certificado de cliente para autenticar o serviço Proxy de Aplicativo para todas as chamadas. A única exceção desta segurança de conexão é a etapa de configuração inicial em que o certificado de cliente é estabelecido. Para obter mais detalhes, confira [Nos bastidores](application-proxy-security.md#under-the-hood) do Proxy de Aplicativo.

### <a name="application-proxy-connectors"></a>Conectores do Proxy de Aplicativo

Os [conectores do Proxy de Aplicativo](application-proxy-connectors.md) são agentes leves implantados no local que facilitam a conexão de saída para o serviço Proxy de Aplicativo na nuvem. Os conectores devem ser instalados em um servidor Windows que tenha acesso ao aplicativo de back-end. Os usuários se conectam ao serviço de nuvem Proxy de Aplicativo que encaminha seu tráfego para os aplicativos por meio dos conectores, conforme ilustrado abaixo.

![Conexões de rede do Proxy de Aplicativo do Azure AD](media/what-is-application-proxy/azure-ad-application-proxy-network-connections.png)

A configuração e o registro entre um conector e o serviço Proxy de Aplicativo são realizados da seguinte forma:

1. O administrador de TI abre as portas 80 e 443 para o tráfego de saída e permite o acesso a várias URLs necessárias para o conector, o serviço Proxy de Aplicativo e o Azure AD.
2. O administrador entra no portal do Azure e corre um executável para instalar o conector em um servidor Windows local.
3. O conector começa a "escutar" o serviço do Proxy de Aplicativo.
4. O administrador adiciona o aplicativo local ao Azure AD e define configurações, como as URLs necessárias para se conectar aos aplicativos.

Para saber mais, confira [Planejar uma implantação do Proxy de Aplicativo do Azure AD](application-proxy-deployment-plan.md).

É recomendável que você sempre implante vários conectores para redundância e dimensionamento. Os conectores, juntamente com o serviço, cuidam de todas as tarefas de alta disponibilidade e podem ser adicionados ou removidos dinamicamente. Sempre que uma nova solicitação chega, ela é roteada para um dos conectores que está disponível. Quando um conector está em execução, ele permanece ativo, pois se conecta ao serviço. Se um conector estiver temporariamente indisponível, ele não responderá a esse tráfego. Os conectores não utilizados são marcados como inativos e são removidos depois de 10 dias de inatividade.

Os conectores também sondam o servidor para descobrir se há uma versão mais recente do conector. Embora a atualização possa ser feita manualmente, os conectores serão atualizados automaticamente sem tempo de inatividade desde que o serviço de Atualizador do Conector do Proxy de Aplicativo seja executado. Para locatários com vários conectores, as atualizações automáticas destinam-se a um conector de cada vez em cada grupo para evitar tempo de inatividade em seu ambiente.

**Observação**: assine o feed RSS para monitorar a página do histórico de versões do [Proxy de Aplicativo](application-proxy-release-version-history.md) para ser notificado quando as atualizações forem lançadas.

Cada conector do Proxy de Aplicativo é atribuído a um [grupo de conectores](application-proxy-connector-groups.md). Os conectores que pertencem ao mesmo grupo de conectores agem como uma unidade única em relação à alta disponibilidade e ao balanceamento de carga. Você pode criar novos grupos, atribuir conectores aos grupos no portal do Azure e atribuir conectores específicos para atender a aplicativos específicos. Para alta disponibilidade, é recomendável ter pelo menos dois conectores em cada grupo de conectores.

Os grupos de conectores são úteis quando você precisa dá suporte aos seguintes cenários:

* Publicação de aplicativos geográficos
* Isolamento/segmentação de aplicativos
* Publicar aplicativos Web em execução na nuvem ou no local

Para saber mais sobre como escolher o local de instalação dos conectores e otimizar sua rede, confira [Considerações sobre a topologia de rede ao usar o Proxy de Aplicativo do Azure Active Directory](application-proxy-network-topology.md).

## <a name="other-use-cases"></a>Outros casos de uso

Até este ponto, nos concentramos em usar o Proxy de Aplicativo para publicar aplicativos locais externamente também permitindo o logon único em todos os aplicativos na nuvem e no local. No entanto, vale a pena mencionar outros casos de uso do Proxy de Aplicativo. Elas incluem:

* **Publicar APIs REST com segurança**. Quando você tem lógica de negócios ou APIs em execução no local ou hospedadas em máquinas virtuais na nuvem, o Proxy de Aplicativo fornece um ponto de extremidade público para acesso à API. O acesso ao ponto de extremidade da API permite controlar a autenticação e a autorização sem exigir portas de entrada. Ele fornece segurança adicional por meio dos recursos do Azure AD Premium, como autenticação multifator e acesso condicional baseado em dispositivo para áreas de trabalho, iOS, MAC e dispositivos Android usando o Intune. Para saber mais, confira [Como habilitar aplicativos cliente nativos para interagir com aplicativos de proxy](application-proxy-configure-native-client-application.md) e [Proteger uma API usando o OAuth 2.0 com o Azure Active Directory e o Gerenciamento de API](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad).
* **Serviços da Área de Trabalho Remota** **(RDS)** . As implantações de RDS padrão exigem conexões de entrada abertas. No entanto, a [implantação de RDS com o Proxy de Aplicativo](application-proxy-integrate-with-remote-desktop-services.md) tem uma conexão permanente de saída no servidor que executa o serviço do conector. Dessa forma, você pode oferecer mais aplicativos aos usuários finais publicando aplicativos locais por meio dos Serviços de Área de Trabalho Remota. Além disso, é possível reduzir a superfície de ataque da implantação com um conjunto limitado de verificação em duas etapas e controles de acesso condicional para o RDS.
* **Publicar aplicativos que se conectam usando WebSockets**. O suporte ao [Qlik Sense](application-proxy-qlik.md) está em Versão prévia pública e será expandido para outros aplicativos no futuro.
* **Habilitar aplicativos cliente nativos para interagir com aplicativos de proxy**. Você pode usar o Proxy de Aplicativo do Azure AD para publicar aplicativos Web. No entanto, ele também pode ser usado para publicar [aplicativos cliente nativos](application-proxy-configure-native-client-application.md) configurados com a ADAL (Biblioteca de Autenticação do AD). Os aplicativos cliente nativos diferem dos aplicativos Web porque eles são instalados em um dispositivo, enquanto os aplicativos Web são acessados por meio de um navegador.

## <a name="conclusion"></a>Conclusão

O modo como trabalhamos e as ferramentas que usamos têm mudado rapidamente. A forma como as organizações gerenciam e protegem seus dados também deverá evoluir já que cada vez mais os funcionários trazem seus próprios dispositivos para o local de trabalho e o uso de aplicativos SaaS (software como serviço) tem sido cada vez mais difundido. As empresas deixarão de funcionar apenas dentro de seus próprios muros, protegidas por um fosso que circunda suas fronteiras. Os dados trafegam para mais locais do que nunca, em ambientes locais e na nuvem. Essa evolução ajudou a aumentar a produtividade e a capacidade de colaboração dos usuários, mas também tornou a proteção de dados confidenciais mais desafiadora.

Se você estiver usando o Azure AD para gerenciar usuários em um cenário de coexistência híbrida ou estiver interessado em iniciar sua jornada para a nuvem, a implementação do Proxy de Aplicativo do Azure AD ajudará a reduzir o tamanho do espaço local fornecendo acesso remoto como um serviço.

As organizações devem começar a usufruir do Proxy de Aplicativo hoje mesmo para aproveitar os seguintes benefícios:

* Publicar externamente os aplicativos locais sem a sobrecarga associada à manutenção da VPN tradicional ou de outras soluções locais de publicação na Web e a abordagem da DMZ
* Logon único para todos os aplicativos, que seja o Office 365 ou outros aplicativos SaaS, incluindo aplicativos locais
* Segurança na escala de nuvem em que o Azure AD aproveita a telemetria do Office 365 para impedir o acesso não autorizado
* Integração com o Intune para garantir que o tráfego corporativo seja autenticado
* Centralização do gerenciamento de conta de usuário
* Atualizações automáticas para garantir que você tem os patches de segurança mais recentes
* Novos recursos assim que eles são lançados; o suporte mais recente para logon único de SAML e gerenciamento mais granular de cookies de aplicativos

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o planejamento, operação e gerenciamento do Proxy de Aplicativo do Azure AD, confira [Planejar uma implantação do Proxy de Aplicativo do Azure AD](application-proxy-deployment-plan.md).
* Para agendar uma demonstração ao vivo ou obter uma avaliação gratuita de 90 dias, confira [Introdução ao Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial).
