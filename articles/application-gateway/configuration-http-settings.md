---
title: Configuração de configurações de HTTP do gateway de Aplicativo Azure
description: Este artigo descreve como definir Aplicativo Azure configurações de HTTP do gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: c0c939a6a8323dfdfafddb46ccdb7d7ef3dd2f2c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89652746"
---
# <a name="application-gateway-http-settings-configuration"></a>Configuração de configurações de HTTP do gateway de aplicativo

O gateway de aplicativo roteia o tráfego para os servidores back-end usando a configuração que você especificar aqui. Depois de criar uma configuração de HTTP, você deve associá-la a uma ou mais regras de roteamento de solicitação.

## <a name="cookie-based-affinity"></a>Afinidade baseada em cookie

Aplicativo Azure gateway usa cookies gerenciados por gateway para manter sessões de usuário. Quando um usuário envia a primeira solicitação ao gateway de aplicativo, ele define um cookie de afinidade na resposta com um valor de hash que contém os detalhes da sessão, para que as solicitações subsequentes que tenham o cookie de afinidade sejam roteadas para o mesmo servidor de back-end para manter a adesão. 

Esse recurso é útil quando você deseja manter uma sessão de usuário no mesmo servidor e quando o estado da sessão é salvo localmente no servidor para uma sessão de usuário. Se o aplicativo não puder lidar com a afinidade baseada em cookie, você não poderá usar esse recurso. Para usá-lo, verifique se os clientes dão suporte a cookies.

A atualização do [Chromium Browser](https://www.chromium.org/Home) [V80](https://chromiumdash.appspot.com/schedule) colocou um mandato em que os cookies http sem o atributo [SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) têm que ser tratados como SameSite = LAX. No caso de solicitações CORS (compartilhamento de recursos entre origens), se o cookie tiver que ser enviado em um contexto de terceiros, ele precisará usar *SameSite = None; Proteja* os atributos e eles devem ser enviados somente por HTTPS. Caso contrário, em um cenário somente HTTP, o navegador não enviará os cookies no contexto de terceiros. O objetivo dessa atualização do Chrome é aprimorar a segurança e evitar ataques CSRF (solicitação entre sites forjada). 

Para dar suporte a essa alteração, a partir de fevereiro de 17 2020, o gateway de aplicativo (todos os tipos de SKU) injetará outro cookie chamado *ApplicationGatewayAffinityCORS* além do cookie *ApplicationGatewayAffinity* existente. O cookie *ApplicationGatewayAffinityCORS* tem mais dois atributos adicionados a ele (*"SameSite = None; Proteger "*) para que a sessão adesiva seja mantida até mesmo para solicitações entre origens.

Observe que o nome do cookie de afinidade padrão é *ApplicationGatewayAffinity* e você pode alterá-lo. Caso você esteja usando um nome de cookie de afinidade personalizado, um cookie adicional é adicionado com CORS como sufixo. Por exemplo, *CustomCookieNameCORS*.

> [!NOTE]
> Se o atributo *SameSite = None* estiver definido, é obrigatório que o cookie também contenha o sinalizador de *segurança* e deve ser enviado via HTTPS.  Se a afinidade de sessão for necessária em CORS, você deverá migrar sua carga de trabalho para HTTPS. Consulte o descarregamento de TLS e a documentação de TLS de ponta a ponta para o gateway de aplicativo aqui – [visão geral](ssl-overview.md), [configurar um gateway de aplicativo com terminação tls usando o portal do Azure](create-ssl-portal.md), [configurar TLS de ponta a ponta usando o gateway de aplicativo com o portal](end-to-end-ssl-portal.md).

## <a name="connection-draining"></a>Descarregamento de conexão

O descarregamento de conexão ajuda você a remover normalmente os membros do pool de back-end durante as atualizações de serviço planejadas. Você pode aplicar essa configuração a todos os membros de um pool de back-ends habilitando a descarga da conexão na configuração de HTTP. Ele garante que todas as instâncias de cancelamento de registro de um pool de back-end continuem a manter as conexões existentes e atendem a solicitações em andamento para um tempo limite configurável e não recebam novas solicitações ou conexões. A única exceção a isso são solicitações associadas para cancelar o registro de instâncias devido à afinidade de sessão gerenciada pelo gateway e continuarão sendo encaminhadas para as instâncias de cancelamento de registro. O descarregamento de conexão se aplica a instâncias de back-end que são explicitamente removidas do pool de back-end.

## <a name="protocol"></a>Protocolo

O gateway de aplicativo dá suporte a HTTP e HTTPS para roteamento de solicitações para os servidores back-end. Se você escolher HTTP, o tráfego para os servidores back-end será descriptografado. Se a comunicação não criptografada não for aceitável, escolha HTTPS.

Essa configuração combinada com HTTPS no ouvinte dá suporte a [TLS de ponta a ponta](ssl-overview.md). Isso permite que você transmita com segurança dados confidenciais criptografados para o back-end. Cada servidor back-end no pool de back-end que tem o TLS de ponta a ponta habilitado deve ser configurado com um certificado para permitir a comunicação segura.

## <a name="port"></a>Porta

Essa configuração especifica a porta em que os servidores back-end escutam o tráfego do gateway de aplicativo. Você pode configurar portas que variam de 1 a 65535.

## <a name="request-timeout"></a>Tempo limite da solicitação

Essa configuração é o número de segundos que o gateway de aplicativo aguarda para receber uma resposta do servidor back-end.

## <a name="override-back-end-path"></a>Substituir caminho de back-end

Essa configuração permite que você configure um caminho de encaminhamento personalizado opcional para usar quando a solicitação for encaminhada para o back-end. Qualquer parte do caminho de entrada que corresponde ao caminho personalizado no campo **substituir caminho de back-end** é copiada para o caminho encaminhado. A tabela a seguir mostra como esse recurso funciona:

- Quando a configuração HTTP é anexada a uma regra básica de roteamento de solicitação:

  | Solicitação original  | Substituir caminho de back-end | Solicitação encaminhada para o back-end |
  | ----------------- | --------------------- | ---------------------------- |
  | /Home/            | /Override.            | /override/home/              |
  | /home/secondhome/ | /Override.            | /override/home/secondhome/   |

- Quando a configuração HTTP é anexada a uma regra de roteamento de solicitação baseada em caminho:

  | Solicitação original           | Regra de caminho       | Substituir caminho de back-end | Solicitação encaminhada para o back-end |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | pathrule      | /Override.            | /override/home/              |
  | /pathrule/home/secondhome/ | pathrule      | /Override.            | /override/home/secondhome/   |
  | /Home/                     | pathrule      | /Override.            | /override/home/              |
  | /home/secondhome/          | pathrule      | /Override.            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | /Override.            | /Override.                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /Override.            | /override/secondhome/        |
  | pathrule                 | pathrule      | /Override.            | /Override.                   |

## <a name="use-for-app-service"></a>Usar para o serviço de aplicativo

Esse é apenas um atalho de interface do usuário que seleciona as duas configurações necessárias para o back-end do serviço de Azure App. Ele permite **escolher o nome do host do endereço de back-end** e cria uma nova investigação personalizada, se você ainda não tiver uma. (Para obter mais informações, consulte a seção [escolher nome do host do endereço de back-end](#pick-host-name-from-back-end-address)deste artigo.) Uma nova investigação é criada e o cabeçalho de investigação é escolhido do endereço do membro de back-end.

## <a name="use-custom-probe"></a>Usar investigação personalizada

Essa configuração associa uma [investigação personalizada](application-gateway-probe-overview.md#custom-health-probe) a uma configuração de http. Você pode associar apenas uma investigação personalizada a uma configuração de HTTP. Se você não associar explicitamente uma investigação personalizada, a [investigação padrão](application-gateway-probe-overview.md#default-health-probe-settings) será usada para monitorar a integridade do back-end. Recomendamos que você crie uma investigação personalizada para maior controle sobre o monitoramento de integridade de seus back-ends.

> [!NOTE]
> A investigação personalizada não monitora a integridade do pool de back-end, a menos que a configuração de HTTP correspondente esteja explicitamente associada a um ouvinte.

## <a name="pick-host-name-from-back-end-address"></a>Escolher o nome do host do endereço de back-end

Esse recurso define dinamicamente o cabeçalho de *host* na solicitação para o nome de host do pool de back-ends. Ele usa um endereço IP ou FQDN.

Esse recurso ajuda quando o nome de domínio do back-end é diferente do nome DNS do gateway de aplicativo e o back-end depende de um cabeçalho de host específico para resolver para o ponto de extremidade correto.

Um caso de exemplo são os serviços multilocatários como o back-end. Um serviço de aplicativo é um serviço de vários locatários que usa um espaço compartilhado com um único endereço IP. Portanto, um serviço de aplicativo só pode ser acessado por meio de nomes de host que são configurados nas configurações de domínio personalizado.

Por padrão, o nome de domínio personalizado é *example.azurewebsites.net*. Para acessar o serviço de aplicativo usando um gateway de aplicativo por meio de um nome de host que não está explicitamente registrado no serviço de aplicativo ou por meio do FQDN do gateway de aplicativo, você substitui o nome do host na solicitação original para o nome de host do serviço de aplicativo. Para fazer isso, habilite a configuração **escolher nome do host de endereço de back-end** .

Para um domínio personalizado cujo nome DNS personalizado existente está mapeado para o serviço de aplicativo, você não precisa habilitar essa configuração.

> [!NOTE]
> Essa configuração não é necessária para Ambiente do Serviço de Aplicativo, que é uma implantação dedicada.

## <a name="host-name-override"></a>Substituição do nome do host

Esse recurso substitui o cabeçalho de *host* na solicitação de entrada no gateway de aplicativo pelo nome de host que você especificar.

Por exemplo, se *www.contoso.com* for especificado na configuração do **nome do host** , a solicitação original * `https://appgw.eastus.cloudapp.azure.com/path1` será alterada para * `https://www.contoso.com/path1` quando a solicitação for encaminhada para o servidor back-end.

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre o pool de back-ends](configuration-overview.md#back-end-pool)