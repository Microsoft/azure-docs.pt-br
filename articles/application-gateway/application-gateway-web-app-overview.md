---
title: Visão geral de back-ends de vários locatários, como Azure App Service, com Aplicativo Azure gateway
description: Esta página fornece uma visão geral do suporte a vários sites do Gateway de Aplicativo para back-ends com vários locatários.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/18/2019
ms.author: victorh
ms.openlocfilehash: 66e4a578e3f443f4cbc3f6e5467cf9a86adf05fe
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297054"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>Suporte do gateway de aplicativo para back-ends de vários locatários, como o serviço de aplicativo

Em designs de arquitetura multilocatário em servidores Web, vários sites estão em execução na mesma instância do servidor Web. Os nomes de host são usados para diferenciar entre os diferentes aplicativos hospedados. Por padrão, o gateway de aplicativo não altera o cabeçalho de host HTTP recebido do cliente e envia o cabeçalho inalterado para o back-end. Isso funciona bem para membros do pool de back-end, como NICs, conjuntos de dimensionamento de máquinas virtuais, endereços IP públicos, endereços IP internos e FQDN, pois eles não dependem de um cabeçalho de host específico ou extensão SNI para resolver para o ponto de extremidade correto. No entanto, há muitos serviços, como Azure App aplicativos Web de serviço e o gerenciamento de API do Azure que são multilocatários por natureza e dependem de um cabeçalho de host específico ou extensão de SNI para resolver para o ponto de extremidade correto. Normalmente, o nome DNS do aplicativo que, por sua vez, é o nome DNS associado ao gateway de aplicativo, é diferente do nome de domínio do serviço de back-end. Portanto, o cabeçalho de host na solicitação original recebida pelo gateway de aplicativo não é o mesmo que o nome de host do serviço de back-end. Por isso, a menos que o cabeçalho de host na solicitação do gateway de aplicativo para o back-end seja alterado para o nome de host do serviço de back-end, os back-ends de vários locatários não poderão resolver a solicitação para o ponto de extremidade correto. 

O Gateway de Aplicativo fornece uma funcionalidade que permite aos usuários substituir o cabeçalho do host HTTP na solicitação com base no nome do host do back-end. Esse recurso habilita o suporte a back-ends com vários locatários, como os aplicativos Web do Azure e ao gerenciamento de APIs. Esse recurso está disponível para as SKUs v1 e v2 padrão e WAF. 

![substituição de host](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> Isso não se aplica ao ambiente de serviço do Azure App (ASE), pois o ASE é um recurso dedicado ao contrário de Azure App serviço, que é um recurso multilocatário.

## <a name="override-host-header-in-the-request"></a>Substituir o cabeçalho de host na solicitação

A capacidade de especificar uma substituição de host é definida nas [configurações de http](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) e pode ser aplicada a qualquer pool de back-end durante a criação da regra. Há suporte para as seguintes duas maneiras de substituir o cabeçalho de host e a extensão SNI para back-ends com vários locatários:

- A capacidade de definir o nome do host para um valor fixo explicitamente inserido nas configurações de HTTP. Esse recurso garante que o cabeçalho de host seja substituído por esse valor para todo o tráfego para o pool de back-end em que as configurações de HTTP específicas são aplicadas. Ao usar SSL de ponta a ponta, esse nome de host substituído é usado na extensão de SNI. Esse recurso habilita cenários onde um farm de pool de back-end espera um cabeçalho de host que seja diferente do cabeçalho de host de entrada do cliente.

- A capacidade de derivar o nome do host do IP ou FQDN dos membros do pool de back-end. As configurações HTTP também fornecem uma opção para escolher dinamicamente o nome do host de um FQDN do membro do pool de back-end, se configurado com a opção de derivar o nome do host de um membro individual do pool de back-end. Ao usar SSL de ponta a ponta, esse nome de host é derivado do FQDN e é usado na extensão de SNI. Esse recurso habilita cenários em que um pool de back-end pode ter dois ou mais serviços PaaS com vários locatários, como aplicativos Web do Azure, e o cabeçalho de host da solicitação para cada membro pode conter o nome de host derivado do seu FQDN. Para implementar esse cenário, usamos uma opção nas configurações de HTTP chamada [escolher nome do host do endereço de back-end](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address) que substituirá dinamicamente o cabeçalho do host na solicitação original para aquele mencionado no pool de back-end.  Por exemplo, se o FQDN do pool de back-end contiver "contoso11.azurewebsites.net" e "contoso22.azurewebsites.net", o cabeçalho de host da solicitação original que for contoso.com será substituído para contoso11.azurewebsites.net ou contoso22.azurewebsites.net Quando a solicitação é enviada ao servidor de back-end apropriado. 

  ![cenário de aplicativo Web](./media/application-gateway-web-app-overview/scenario.png)

Com essa funcionalidade, os clientes especificam as opções de configuração apropriadas das investigações personalizadas e das configurações de HTTP. Essa configuração é, em seguida, vinculada a um ouvinte e a um pool de back-end por meio de uma regra.

## <a name="special-considerations"></a>Considerações especiais

### <a name="ssl-termination-and-end-to-end-ssl-with-multi-tenant-services"></a>Término de SSL e SSL de ponta a ponta com serviços de vários locatários

O encerramento de SSL e a criptografia SSL de ponta a ponta têm suporte com serviços multilocatários. Para terminação SSL no gateway de aplicativo, o certificado SSL continua a ser adicionado ao ouvinte do gateway de aplicativo. No entanto, no caso de SSL de ponta a ponta, os serviços confiáveis do Azure, como os aplicativos Web do serviço Azure App, não exigem a lista de permissões de back-ends no gateway de aplicativo. Portanto, não há necessidade de adicionar certificados de autenticação. 

![SSL de ponta a ponta](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

Observe que, na imagem acima, não há nenhum requisito para adicionar certificados de autenticação quando o serviço de aplicativo é selecionado como back-end.

### <a name="health-probe"></a>Investigação de integridade

Substituir o cabeçalho de host nas **configurações de http** afeta apenas a solicitação e seu roteamento. Ele não afeta o comportamento da investigação de integridade. Para a funcionalidade de ponta a ponta funcionar, a investigação e as configurações de HTTP devem ser modificadas para refletir a configuração correta. Além de fornecer a capacidade de especificar um cabeçalho de host na configuração de investigação, as investigações personalizadas também oferecem suporte à capacidade de derivar o cabeçalho de host nas configurações de HTTP configuradas no momento. Essa configuração pode ser especificada usando o parâmetro `PickHostNameFromBackendHttpSettings` na configuração da investigação.

### <a name="redirection-to-app-services-url-scenario"></a>Redirecionamento para o cenário de URL do serviço de aplicativo

Pode haver cenários em que o nome do host na resposta do serviço de aplicativo pode direcionar o navegador do usuário final para o nome de host *. azurewebsites.net em vez do domínio associado ao gateway de aplicativo. Esse problema pode ocorrer quando:

- Você tem o redirecionamento configurado em seu serviço de aplicativo. O redirecionamento pode ser tão simples quanto adicionar uma barra à direita à solicitação.
- Você tem a autenticação do Azure AD que causa o redirecionamento.

Para resolver esses casos, consulte [solucionar problemas de redirecionamento para o problema de URL do serviço de aplicativo](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url).

## <a name="next-steps"></a>Próximas etapas

Saiba como configurar um gateway de aplicativo com um aplicativo multilocatário, como Azure App aplicativo Web do serviço como um membro do pool de back-end visitando [configurar aplicativos Web do serviço de aplicativo com o gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/configure-web-app-portal)
