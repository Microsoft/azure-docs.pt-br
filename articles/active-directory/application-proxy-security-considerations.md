---
title: "Considerações de segurança para o Proxy de Aplicativo do Azure AD | Microsoft Docs"
description: "Aborda considerações de segurança para usar o Proxy de Aplicativo do Azure AD"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 031632aa9e01c66e836d607d588ededb7140589f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Considerações de segurança para acessar aplicativos remotamente com o Proxy de Aplicativo do Azure AD

Este artigo explica os componentes que funcionam para manter usuários e aplicativos seguros quando você usar o Proxy de Aplicativo do Azure Active Directory.

O diagrama a seguir mostra como o Azure AD permite acesso remoto seguro aos aplicativos locais.

 ![Diagrama de acesso remoto por meio do Proxy de Aplicativo do Azure AD](./media/application-proxy-security-considerations/secure-remote-access.png)

## <a name="security-benefits"></a>Benefícios de segurança

O Proxy de Aplicativo do Azure AD oferece os seguintes benefícios de segurança:

### <a name="authenticated-access"></a>Acesso autenticado 

Se você optar por usar a pré-autenticação do Azure Active Directory, somente conexões autenticadas podem acessar sua rede.

Proxy de Aplicativo do Azure AD depende do Azure AD serviço de token de segurança (STS) para todas as autenticações.  A pré-autenticação, por sua própria natureza, bloqueia um número significativo de ataques anônimos, pois somente identidades autenticadas podem acessar o aplicativo de back-end.

Se você escolher Passagem como seu método de pré-autenticação, não terá esse benefício. 

### <a name="conditional-access"></a>Acesso condicional

Aplique controles de política mais rígidos antes que as conexões com sua rede sejam estabelecidas.

Com [acesso condicional](active-directory-conditional-access-azuread-connected-apps.md), você pode definir restrições em relação ao que o tráfego pode acessar nos seus aplicativos de back-end. É possível criar políticas que restrinjam entradas com base no local, na força da autenticação e no perfil de risco do usuário.

Você também pode usar o acesso condicional para configurar políticas de Autenticação Multifator, adicionando outra camada de segurança para suas autenticações de usuário. 

### <a name="traffic-termination"></a>Encerramento de tráfego

Todo o tráfego é encerrado na nuvem.

Como o Proxy de Aplicativo do Azure AD é um proxy reverso, todo o tráfego para os aplicativos de back-end é encerrado no serviço. A sessão pode obter restabelecida apenas com o servidor de back-end, o que significa que seus servidores back-end não são expostos para o tráfego HTTP direto. Essa configuração significa que você terá uma proteção melhor contra ataques direcionados.

### <a name="all-access-is-outbound"></a>Todo o acesso é de saída 

Não é necessário abrir as conexões de entrada para a rede corporativa.

Os Conectores do Proxy de Aplicativo utilizam apenas conexões de saída para o serviço de Proxy de Aplicativo do Azure AD, o que significa que não há necessidade de abrir portas de firewall para conexões de entrada. As abordagens tradicionais exigiam uma rede de perímetro (também conhecida como *DMZ*, *zona desmilitarizada*, ou *sub-rede filtrada*) e permitiam acesso a conexões não autenticadas na borda da rede. Este cenário já fez os investimentos necessários em produtos de firewall de aplicativo Web para analisar tráfego e proteger o ambiente. Com o Proxy de Aplicativo, você não precisa de uma rede de perímetro porque todas as conexões são de saída e ocorrem por um canal seguro.

Para saber mais sobre conectores, veja [Noções básicas sobre conectores de proxy de aplicativo do Azure AD](application-proxy-understand-connectors.md).

### <a name="cloud-scale-analytics-and-machine-learning"></a>Machine learning e análise em escala de nuvem 

Obtenha proteção de segurança de ponta.

Como faz parte do Azure Active Directory, o Proxy de Aplicativo pode aproveitar a [Azure AD Identity Protection](active-directory-identityprotection.md) com dados do Microsoft Security Response Center e da Digital Crimes Unit. Juntos, identificamos proativamente contas comprometidas e oferecemos proteção em conexões de alto risco. Podemos levar em consideração vários fatores para determinar quais tentativas de entrada são de alto risco. Esses fatores incluem a sinalização de dispositivos infectados, anonimização de redes e locais atípicas ou improváveis.

Muitos desses relatórios e eventos já estão disponíveis por meio de uma API para integração com as informações de segurança e sistemas de gerenciamento (SIEM) do evento.

### <a name="remote-access-as-a-service"></a>Acesso remoto como serviço

Você não precisa se preocupar com a manutenção e a aplicação de patches em servidores locais.

A não aplicação de patches no software ainda é responsável por um grande número de ataques. O Proxy de Aplicativo do Azure AD é um serviço em escala de Internet da Microsoft. Sendo assim, você sempre obterá os patches e as atualizações de segurança mais recentes.

Para melhorar a segurança dos aplicativos publicados pelo Proxy de Aplicativo do Azure AD, bloquearemos robôs do rastreador da Web de indexação e o arquivamento de seus aplicativos. Cada vez que um robô rastreador da Web tentar recuperar as configurações de robôs para um aplicativo publicado, o Proxy de Aplicativo responderá com um arquivo robots.txt que inclui `User-agent: * Disallow: /`.

### <a name="ddos-prevention"></a>Prevenção de DDOS

Os aplicativos publicados por meio do Proxy de Aplicativo estão protegidos contra ataques DDOS (ataque de negação de serviço distribuído).

O serviço Proxy de Aplicativo monitora a quantidade de tráfego tentando acessar seus aplicativos e rede. Se o número de dispositivos solicitando acesso remoto aos seus aplicativos atingir um pico, a Microsoft limita o acesso à rede. 

A Microsoft inspeciona os padrões de tráfego para aplicativos individuais em sua assinatura como um todo. Se um aplicativo receber mais do que as solicitações normais, as solicitações aguardando para acessar esse aplicativo serão negadas por um curto período de tempo. Se você receber mais do que o número normal de solicitações em toda a sua assinatura, as solicitações para acessar qualquer um dos seus aplicativos serão negadas. Essa medida preventiva impede que os servidores de aplicativo sejam sobrecarregados com solicitações de acesso remoto, de forma que os usuários locais possam continuar acessando seus aplicativos. 

## <a name="under-the-hood"></a>Nos bastidores

O Proxy de Aplicativo do Azure AD consiste em duas partes:

* O serviço baseado em nuvem: este serviço é executado no Azure e é onde as conexões de cliente/usuário externo são feitas.
* [O conector local](application-proxy-understand-connectors.md): componente local, o conector escuta solicitações das conexões de serviço e identificadores de Proxy de Aplicativo do Azure AD para aplicativos internos. 

Um fluxo entre o conector e o serviço de Proxy de Aplicativo é estabelecido quando:

* O conector é configurado pela primeira vez.
* O conector extrai informações de configuração do serviço de Proxy de Aplicativo.
* Um usuário acessa um aplicativo publicado.

>[!NOTE]
>Todas as comunicações ocorrem por SSL e sempre originam-se no conector para o serviço de Proxy de Aplicativo. O serviço é apenas de saída.

O conector usa um certificado de cliente para autenticar o serviço de Proxy de Aplicativo para quase todas as chamadas. A única exceção deste processo é a etapa de configuração inicial em que o certificado de cliente é estabelecido.

### <a name="installing-the-connector"></a>Instalação do conector

Quando o conector é configurado pela primeira vez, ocorrem os seguintes eventos de fluxo:

1. O registro do conector para o serviço ocorre como parte da instalação do conector. Os usuários são solicitados a digitar suas credenciais de administrador do Azure AD. O token adquirido dessa autenticação é então apresentado ao serviço de Proxy de Aplicativo do Azure AD.
2. O serviço de Proxy de Aplicativo avalia o token. Ele verifica se o usuário é um administrador da empresa no locatário. Se o usuário não for um administrador, o processo será encerrado.
3. O conector gera uma solicitação de certificado de cliente e a passa, juntamente com o token, para o serviço de Proxy de Aplicativo. O serviço, por sua vez, verifica o token e faz a solicitação do certificado de cliente.
4. O conector usa o certificado de cliente para comunicação futura com o serviço de Proxy de Aplicativo.
5. O conector realiza uma extração inicial dos dados de configuração do sistema do serviço usando o respectivo certificado de cliente e está pronto para receber solicitações.

### <a name="updating-the-configuration-settings"></a>Atualização das definições da configuração

Sempre que o serviço de Proxy de Aplicativo atualiza as definições de configuração, ocorrem os seguintes eventos de fluxo:

1. O conector se conecta ao ponto de extremidade de configuração no serviço de Proxy de Aplicativo usando seu certificado de cliente.
2. Após a validação do certificado do cliente, o serviço Proxy de Aplicativo retorna dados de configuração para o conector (por exemplo, o grupo de conectores do que o conector deve fazer parte).
3. Se o certificado atual tiver mais de 180 dias, o conector gerará uma nova solicitação de certificado, que atualiza efetivamente o certificado de cliente a cada 180 dias.

### <a name="accessing-published-applications"></a>Acesso a aplicativos publicados

Quando os usuários acessam um aplicativo publicado, os seguintes eventos ocorrem entre o serviço de Proxy de Aplicativo e o conector de Proxy de Aplicativo:

1. [O serviço autentica o usuário para o aplicativo](#the-service-checks-the-configuration-settings-for-the-app)
2. [O serviço faz uma solicitação na fila do conector](#The-service-places-a-request-in-the-connector-queue)
3. [Um conector processa a solicitação da fila](#the-connector-receives-the-request-from-the-queue)
4. [O conector aguarda uma resposta](#the-connector-waits-for-a-response)
5. [O serviço transmite dados para o usuário](#the-service-streams-data-to-the-user)

Para saber mais sobre o que acontece em cada uma dessas etapas, continue lendo.


#### <a name="1-the-service-authenticates-the-user-for-the-app"></a>1. O serviço autentica o usuário para o aplicativo

Se você configurou o aplicativo para usar Passagem como seu método de pré-autenticação, as etapas nesta seção são ignoradas.

Se você configurou o aplicativo para pré-autenticar com o Azure AD, os usuários são redirecionados para o STS do Azure AD para autenticar e as seguintes etapas são executadas:

1. O Proxy de Aplicativo verifica todos os requisitos da política de acesso condicional para o aplicativo específico. Esta etapa garante que o usuário foi atribuído ao aplicativo. Se uma verificação em duas etapas for necessária, a sequência de autenticação solicitará ao usuário um segundo método de autenticação.

2. Depois de realizadas todas as verificações, o STS do Azure AD emite um token assinado para o aplicativo e redireciona o usuário de volta ao serviço de Proxy de Aplicativo.

3. O Proxy de Aplicativo verifica se o token foi emitido para corrigir o aplicativo. Ele também faz outras verificações, como ao garantir que o token foi assinado pelo Azure AD, e que ainda está dentro da janela de validade.

4. O Proxy de Aplicativo define um cookie de autenticação criptografado para indicar que a autenticação para o aplicativo ocorreu. Esse cookie inclui um carimbo de data/hora de expiração com base no token do Azure AD e outros dados, como o nome do usuário na qual se baseia a autenticação. O cookie é criptografado com uma chave privada conhecida apenas pelo serviço de Proxy de Aplicativo.

5. O Proxy de Aplicativo redireciona o usuário de volta para a URL originalmente solicitada.

Se qualquer parte das etapas da pré-autenticação falhar, a solicitação do usuário será negada e o usuário verá uma mensagem indicando a origem do problema.


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2. O serviço faz uma solicitação na fila do conector

Os conectores mantêm uma conexão de saída aberta ao serviço de Proxy de Aplicativo. Quando uma solicitação chega, o serviço enfileira a solicitação em uma das conexões abertas para escolha do conector.

A solicitação inclui itens do aplicativo, como os cabeçalhos da solicitação, os dados do cookie criptografado, o usuário que faz a solicitação e a ID de solicitação. Embora os dados do cookie criptografado sejam enviados com a solicitação, o cookie de autenticação não é.

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3. O conector processa a solicitação da fila. 

Com base na solicitação, o Proxy de Aplicativo executa uma das seguintes ações:

* Se a solicitação for uma operação simples (por exemplo, não há dados no corpo como acontece com uma solicitação *GET* RESTful), o conector fará uma conexão com o recurso interno de destino e aguardará uma resposta.

* Se a solicitação tiver dados associados a ela no corpo, por exemplo, uma operação *POST* RESTful, o conector fará uma conexão de saída usando o certificado de cliente com a instância do Proxy de Aplicativo. Ele faz essa conexão para solicitar os dados e abrir uma conexão com o recurso interno. No recebimento da solicitação do conector, o serviço de Proxy de Aplicativo começa aceitando o conteúdo do usuário e encaminha os dados ao conector. O conector, por sua vez, encaminha os dados para o recurso interno.

#### <a name="4-the-connector-waits-for-a-response"></a>4. O conector aguarda uma resposta.

Depois de concluída a solicitação/transmissão de todo o conteúdo ao back-end, o conector espera uma resposta.

Depois de receber a resposta, o conector faz uma conexão de saída com o serviço de Proxy de Aplicativo para retornar os detalhes do cabeçalho e iniciar a transmissão dos dados de retorno.

#### <a name="5-the-service-streams-data-to-the-user"></a>5. O serviço transmite dados para o usuário. 

Algum processamento do aplicativo pode ocorrer aqui. Se você configurou o Proxy de Aplicativo para converter cabeçalhos ou URLs em seu aplicativo, esse processamento ocorre conforme necessário durante esta etapa.


## <a name="next-steps"></a>Próximas etapas

[Considerações de topologia de rede ao usar o Proxy de Aplicativo do Azure AD](application-proxy-network-topology-considerations.md)

[Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](application-proxy-understand-connectors.md)
