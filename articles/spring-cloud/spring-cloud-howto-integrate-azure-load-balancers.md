---
title: Tutorial – integrar o Azure Spring Cloud com soluções de balanceamento de carga do Azure
description: Como integrar o Azure Spring Cloud com soluções de balanceamento de carga do Azure
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: cd0b9d1369fb1c0e662de83b7056da0ff7c83bd1
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090821"
---
# <a name="integrate-azure-spring-cloud-with-azure-load-balance-solutions"></a>Integrar o Azure Spring Cloud com soluções de balanceamento de carga do Azure

**Este artigo aplica-se a:** ✔️ Java ✔️ C#

O Azure Spring Cloud dá suporte a microserviços no Azure.  O aumento da empresa pode exigir vários data centers com o gerenciamento de várias instâncias do Azure Spring Cloud.

O Azure já fornece soluções de balanceamento de carga diferentes. Há três opções para integrar o Azure Spring Cloud com soluções de balanceamento de carga do Azure:

1.  Integrar o Azure Spring Cloud ao Gerenciador de tráfego do Azure
2.  Integrar o Azure Spring Cloud com o gateway Azure App
3.  Integrar o Azure Spring Cloud com a porta frontal do Azure

## <a name="prerequisites"></a>Pré-requisitos

* Azure Spring Cloud: [como criar um serviço de nuvem do Azure Spring](./spring-cloud-quickstart.md)
* Gerenciador de tráfego do Azure: [como criar um Gerenciador de tráfego](../traffic-manager/quickstart-create-traffic-manager-profile.md)
* Gateway de Azure App: [como criar um gateway de aplicativo](../application-gateway/quick-create-portal.md)
* Azure front door: [como criar uma porta frontal](../frontdoor/quickstart-create-front-door.md)

## <a name="integrate-azure-spring-cloud-with-azure-traffic-manager"></a>Integrar o Azure Spring Cloud ao Gerenciador de tráfego do Azure

Para integrar o Azure Spring Cloud ao Gerenciador de tráfego, adicione seus pontos de extremidade públicos como pontos de extremidade do Gerenciador de tráfego e, em seguida, configure o domínio personalizado para o Gerenciador de tráfego e o Azure Spring Cloud.

### <a name="add-endpoint-in-traffic-manager"></a>Adicionar ponto de extremidade no Gerenciador de tráfego
Adicionar pontos de extremidade no Gerenciador de tráfego:
1.  Especifique o **tipo** a ser um *ponto de extremidade externo*.
1.  Insira o nome de domínio totalmente qualificado (FQDN) de cada ponto de extremidade público do Azure Spring Cloud.
1. Clique em **OK**.

    ![Gerenciador de tráfego 1 do Traffic Manager ](media/spring-cloud-load-balancers/traffic-manager-1.png) ![ 2](media/spring-cloud-load-balancers/traffic-manager-2.png)

### <a name="configure-custom-domain"></a>Configurar domínio personalizado
Para concluir a configuração:
1.  Entre no site do seu provedor de domínio e crie um mapeamento de registro CNAME de seu domínio personalizado para o nome de domínio padrão do Azure do Gerenciador de tráfego.
1.  Siga as instruções sobre [como adicionar um domínio personalizado ao Azure Spring Cloud](spring-cloud-tutorial-custom-domain.md).
1. Adicione a associação de domínio personalizada acima ao Gerenciador de tráfego para o serviço de aplicativo correspondente do Azure Spring Cloud e carregue o certificado SSL lá.

    ![Gerenciador de tráfego 3](media/spring-cloud-load-balancers/traffic-manager-3.png)

## <a name="integrate-azure-spring-cloud-with-azure-app-gateway"></a>Integrar o Azure Spring Cloud com o gateway Azure App

Para integrar com o serviço de nuvem do Azure Spring, conclua as seguintes configurações:

### <a name="configure-backend-pool"></a>Configurar pool de back-end
1. Especifique o **tipo de destino** como *endereço IP* ou *FQDN*.
1. Insira seus pontos de extremidade públicos do Azure Spring Cloud.

    ![Gateway de aplicativo 1](media/spring-cloud-load-balancers/app-gateway-1.png)

### <a name="add-custom-probe"></a>Adicionar investigação personalizada
1. Selecione **investigações de integridade** e **Adicionar** à caixa de diálogo abrir **investigação** personalizada. 
1. O ponto principal é selecionar *Sim* para **escolher o nome do host da opção de configurações de http de back-end** .

    ![Gateway de aplicativo 2](media/spring-cloud-load-balancers/app-gateway-2.png)

### <a name="configure-http-setting"></a>Definir configuração de http
1.  Selecione **configurações de http** e **Adicionar** para adicionar uma configuração de http.
1.  **Substituir pelo novo nome do host:** selecione *Sim*.
1.  **Substituição do nome do host**: selecione **escolher nome do host do destino de back-end**.
1.  **Usar investigação personalizada**: selecione *Sim* e escolha a investigação personalizada criada acima.

    ![Gateway de aplicativo 3](media/spring-cloud-load-balancers/app-gateway-3.png)

## <a name="integrate-azure-spring-cloud-with-azure-front-door"></a>Integrar o Azure Spring Cloud com a porta frontal do Azure

Para integrar com o serviço de nuvem do Azure Spring e configurar o pool de back-end, 
1. **Adicionar pool de back-end**.
1. Especifique o ponto de extremidade de back-end adicionando host.

    ![Porta frontal 1](media/spring-cloud-load-balancers/front-door-1.png)

1.  Especifique o **tipo de host de back-end** como *host personalizado*.
1.  Insira o FQDN dos pontos de extremidade públicos do Azure Spring Cloud no **nome do host de back-end**.
1.  Aceite o padrão de **cabeçalho de host de back-end** , que é o mesmo que o **nome do host de back-end**.

    ![Porta frontal 2](media/spring-cloud-load-balancers/front-door-2.png)

## <a name="next-steps"></a>Próximas etapas
* [Como criar um Gerenciador de tráfego](../traffic-manager/quickstart-create-traffic-manager-profile.md)
* [Como criar um gateway de aplicativo](../application-gateway/quick-create-portal.md)
* [Como criar uma porta frontal](../frontdoor/quickstart-create-front-door.md)