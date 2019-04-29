---
title: Visão geral de multilocatário back-ends, como o serviço de aplicativo do Azure, com o Gateway de aplicativo do Azure
description: Esta página fornece uma visão geral do suporte a vários sites do Gateway de Aplicativo para back-ends com vários locatários.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/18/2019
ms.author: victorh
ms.openlocfilehash: 8434340bb7ed95cc36115c05048b2b67682b5796
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831278"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>Suporte do Gateway de aplicativo para vários locatário back termina, como o serviço de aplicativo

Designs de arquiteturas de multilocatário em servidores web, vários sites estão em execução na mesma instância do servidor web. Os nomes de host são usadas para diferenciar entre os diferentes aplicativos que são hospedados. Por padrão, o gateway de aplicativo não altera o cabeçalho de host HTTP recebido do cliente e envia o cabeçalho inalterado para o back-end. Isso funciona bem para membros do pool de back-end, como NICs, dimensionamento de máquinas virtuais define, endereços IP públicos, endereços IP internos e FQDN conforme elas não dependem de um cabeçalho de host específico ou a extensão SNI para resolver para o ponto de extremidade correto. No entanto, há muitos serviços, como aplicativos web do serviço de aplicativo do Azure e o gerenciamento de API do Azure que têm vários locatários por natureza e contam com um cabeçalho de host específico ou a extensão SNI para resolver para o ponto de extremidade correto. Normalmente, o nome DNS do aplicativo, que por sua vez, é o nome DNS associado com o gateway de aplicativo, é diferente do nome de domínio do serviço de back-end. Portanto, o cabeçalho de host na solicitação original recebido pelo gateway de aplicativo não é igual ao nome de host do serviço de back-end. Por isso, a menos que o cabeçalho de host na solicitação de gateway de aplicativo para o back-end é alterado para o nome do host do serviço de back-end, back-ends multilocatário não são capazes de resolver a solicitação para o ponto de extremidade correto. 

O gateway de aplicativo fornece uma funcionalidade que permite aos usuários substituir o cabeçalho de host HTTP na solicitação com base no nome do host do back-end. Esse recurso habilita o suporte para o back-ends multilocatário como aplicativos web do serviço de aplicativo do Azure e o gerenciamento de API. Esse recurso está disponível para o v1 e v2 standard e SKUs do WAF. 

![substituição do host](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> Isso não é aplicável ao ambiente do serviço de aplicativo do Azure (ASE), pois o ASE é um recurso dedicado ao contrário do serviço de aplicativo do Azure que é um recurso de vários locatário.

## <a name="override-host-header-in-the-request"></a>Substituir o cabeçalho de host na solicitação

A capacidade de especificar uma substituição de host é definida na [configurações de HTTP](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) e pode ser aplicado a qualquer pool de back-end durante a criação da regra. Há suporte para os dois métodos de substituição de cabeçalho de host e extensão SNI para o back-ends com vários locatários a seguir:

- A capacidade de definir o nome do host como um valor fixo explicitamente inserido nas configurações de HTTP. Essa funcionalidade garante que o cabeçalho de host seja substituído para esse valor para todo o tráfego para o pool de back-end em que as configurações de HTTP específicas são aplicadas. Ao usar SSL de ponta a ponta, esse nome de host substituído é usado na extensão de SNI. Esse recurso habilita cenários onde um farm de pool de back-end espera um cabeçalho de host que seja diferente do cabeçalho de host de entrada do cliente.

- A capacidade de derivar o nome do host do IP ou FQDN dos membros do pool de back-end. Configurações HTTP também fornecem uma opção para selecionar dinamicamente o nome do host do FQDN de um membro pool de back-end se configurado com a opção para derivar o nome do host de um membro do pool de back-end individual. Ao usar SSL de ponta a ponta, esse nome de host é derivado do FQDN e é usado na extensão de SNI. Esse recurso habilita cenários em que um pool de back-end pode ter dois ou mais serviços PaaS com vários locatários, como aplicativos Web do Azure, e o cabeçalho de host da solicitação para cada membro pode conter o nome de host derivado do seu FQDN. Para implementar esse cenário, usamos uma opção nas configurações de HTTP denominado [escolher um nome de host do endereço de back-end](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-backend-address) dinamicamente que substituirá o cabeçalho de host na solicitação original para mencionada no pool de back-end.  Por exemplo, se o FQDN do pool de back-end contém "contoso11.azurewebsites.net" e "contoso22.azurewebsites.net", cabeçalho de host da solicitação original que é contoso.com será substituído contoso11.azurewebsites.net ou contoso22.azurewebsites.net Quando a solicitação é enviada para o servidor de back-end apropriado. 

  ![cenário de aplicativo Web](./media/application-gateway-web-app-overview/scenario.png)

Com essa funcionalidade, os clientes especificam as opções de configuração apropriadas das investigações personalizadas e das configurações de HTTP. Essa configuração é, em seguida, vinculada a um ouvinte e a um pool de back-end por meio de uma regra.

## <a name="special-considerations"></a>Considerações especiais

### <a name="ssl-termination-and-end-to-end-ssl-with-multi-tenant-services"></a>Terminação SSL e SSL de ponta a ponta com os serviços de multilocatário

Terminação SSL e criptografia SSL de ponta a ponta é compatível com os serviços de vários locatários. Para a terminação SSL no gateway de aplicativo, o certificado SSL continua a ser necessário a ser adicionado ao ouvinte de gateway de aplicativo. No entanto, no caso de SSL de ponta a ponta, o Azure confiável serviços, como aplicativos web do serviço de aplicativo do Azure não exigem a lista de permissões de back-ends no gateway de aplicativo. Portanto, não é necessário para adicionar certificados de autenticação. 

![SSL de ponta a ponta](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

Observe que na imagem acima, não há nenhum requisito de adicionar certificados de autenticação quando o serviço de aplicativo é selecionado como o back-end.

### <a name="health-probe"></a>Investigação de integridade

Substituindo o cabeçalho de host na **configurações de HTTP** afeta somente a solicitação e seu roteamento. ela não afeta o comportamento de investigação de integridade. Para a funcionalidade de ponta a ponta funcionar, a investigação e as configurações de HTTP devem ser modificadas para refletir a configuração correta. Além de fornecer a capacidade de especificar um cabeçalho de host na configuração da investigação, as investigações personalizadas também dão suporte a capacidade de extrair o cabeçalho de host das configurações de HTTP configuradas no momento. Essa configuração pode ser especificada usando o parâmetro `PickHostNameFromBackendHttpSettings` na configuração da investigação.

### <a name="redirection-to-app-services-url-scenario"></a>Redirecionamento para o cenário de URL do serviço de aplicativo

Pode haver situações em que o nome do host na resposta do serviço de aplicativo pode direcionar o navegador do usuário final para o *. azurewebsites.net nome do host em vez do domínio associado com o Gateway de aplicativo. Esse problema pode ocorrer quando:

- Você tem um redirecionamento configurado no seu serviço de aplicativo. Redirecionamento pode ser tão simple quanto adicionar uma barra à direita para a solicitação.
- Você tem a autenticação do Azure AD que faz com que o redirecionamento.

Para resolver tais casos, consulte [solucionar problemas de redirecionamento para o problema de URL do serviço de aplicativo](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url).

## <a name="next-steps"></a>Próximas etapas

Saiba como configurar um gateway de aplicativo com um aplicativo multilocatário, como o aplicativo de web do serviço de aplicativo do Azure como um membro do pool de back-end visitando [aplicativos da web de configurar o serviço de aplicativo com o Gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/create-web-app)
