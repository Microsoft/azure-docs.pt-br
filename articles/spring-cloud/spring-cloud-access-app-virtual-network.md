---
title: Aplicativo Azure Spring Cloud Access na rede virtual
description: Acessar o aplicativo em uma nuvem Spring do Azure na rede virtual.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/11/2020
ms.custom: devx-track-java
ms.openlocfilehash: 37c8b4bc186c217ecb27638f5f50297102345de7
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576524"
---
# <a name="access-your-application-in-a-private-network"></a>Acessar seu aplicativo em uma rede privada

Este documento explica como acessar um ponto de extremidade para seu aplicativo em uma rede privada.  Para obter acesso, você precisa criar uma **zona de DNS privado do Azure** em sua assinatura para converter/resolver o nome de domínio totalmente qualificado (FQDN) privado para seu endereço IP.

Quando a **atribuição de ponto de extremidade** para aplicativos em uma instância do serviço de nuvem do Azure Spring é implantada em sua rede virtual, o ponto de extremidade é um FQDN privado. O domínio só pode ser acessado na rede privada. Aplicativos e serviços usam o ponto de extremidade do aplicativo. Eles incluem o **ponto de extremidade de teste** descrito em [Exibir aplicativos e implantações](spring-cloud-howto-staging-environment.md#view-apps-and-deployments). O **streaming de log**, descrito em [transmitir logs de aplicativo de nuvem Spring do Azure em tempo real](spring-cloud-howto-log-streaming.md), também funciona apenas dentro da rede privada.

## <a name="create-a-private-dns-zone"></a>Criar uma zona DNS privada

O procedimento a seguir cria uma zona DNS privada para um aplicativo na rede privada.

1. Abra o portal do Azure. Na caixa de pesquisa superior, pesquise **zonas de DNS privado** e selecione **DNS privado zonas** do resultado.

2. Na página **zonas de DNS privado** , selecione **+ Adicionar**.

3. Preencha o formulário na página **criar zona de DNS privado** . Insira **<span>Private.azuremicroservices.Io</span>** como o **nome** da zona.

4. Selecione **Examinar + criar**.

5. Selecione **Criar**.

A criação da zona pode levar alguns minutos.

## <a name="link-the-virtual-network"></a>Vincular a rede virtual

Para vincular a zona DNS privada à rede virtual, você precisa criar um link de rede virtual.

1. Selecione o recurso de zona DNS particular criado acima: **<span>Private.azuremicroservices.Io</span>** 

2. No painel esquerdo, selecione **Links de rede virtual**.

3. Selecione **Adicionar**.

4. Insira **Azure-Spring-Cloud-DNS-link** para o **nome do link**.

5. Para **rede virtual**, selecione a rede virtual que você criou conforme explicado em [implantar o Azure Spring Cloud em sua rede virtual do Azure (injeção de VNet)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md).

    ![Adicionar link de rede virtual](media/spring-cloud-access-app-vnet/add-virtual-network-link.png)

6. Clique em **OK**.

## <a name="create-dns-record"></a>Criar registro de DNS

Para usar a zona DNS privada para converter/resolver o DNS, você deve criar um registro de tipo "A" na zona.

1. Selecione o recurso de rede virtual criado conforme explicado em [implantar o Azure Spring Cloud em sua rede virtual do Azure (injeção de VNet)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md).

2. Na caixa de pesquisa **dispositivos conectados** , digite *kubernetes-Internal*.

3. No resultado filtrado, localize o **dispositivo** conectado à **sub-rede** de tempo de execução do serviço da instância do serviço e copie seu **endereço IP**. Neste exemplo, o endereço IP é *10.1.0.7*.

    [![Criar registro ](media/spring-cloud-access-app-vnet/create-dns-record.png) DNS](media/spring-cloud-access-app-vnet/create-dns-record.png)

Ou, você pode buscar o IP usando o seguinte comando AZ CLI:

```azurecli
SPRING_CLOUD_RG= # Resource group name of your Azure Spring Cloud service instance
SPRING_CLOUD= # Name of your Azure Spring Cloud service instance

SERVICE_RUNTIME_RG=`az spring-cloud show -g $SPRING_CLOUD_RG -n $SPRING_CLOUD --query \
"properties.networkProfile.serviceRuntimeNetworkResourceGroup" -o tsv`

IP_ADDRESS=`az network lb frontend-ip list --lb-name kubernetes-internal -g \
$SERVICE_RUNTIME_RG --query "[0].privateIpAddress" -o tsv`
```

4. Selecione o recurso de zona DNS particular criado acima: **<span>Private.azuremicroservices.Io</span>**.

5. Selecione **+ Conjunto de registros**.

6. Em **Adicionar conjunto de registros**, insira ou selecione estas informações:

    |Configuração     |Valor                                                                      |
    |------------|---------------------------------------------------------------------------|
    |Nome        |Insira *\**                                                                 |
    |Tipo        |Selecione **um**                                                               |
    |TTL         |Insira *1*                                                                  |
    |Unidade de TTL    |Selecionar **horas**                                                           |
    |Endereço IP  |Insira o endereço IP copiado na etapa 3. No exemplo, insira *10.1.0.7*.    |

    Depois, selecione **OK**.

    ![Adicionar registro de zona DNS privada](media/spring-cloud-access-app-vnet/private-dns-zone-add-record.png)

## <a name="assign-private-fqdn-for-your-application"></a>Atribuir um FQDN privado para seu aplicativo

Depois de seguir o procedimento em [Compilar e implantar aplicativos de microatendimento](spring-cloud-tutorial-deploy-in-azure-virtual-network.md), você pode atribuir o FQDN privado para seu aplicativo.

1. Selecione a instância do serviço de nuvem do Azure Spring implantada em sua rede virtual e abra a guia **aplicativos** no menu à esquerda.

2. Selecione o aplicativo para mostrar a página **visão geral** .

3. Selecione **atribuir ponto de extremidade** para atribuir um FQDN privado ao seu aplicativo. Isso pode levar alguns minutos.

    ![Atribuir ponto de extremidade privado](media/spring-cloud-access-app-vnet/assign-private-endpoint.png)

4. O FQDN privado atribuído (chamada de **URL**) agora está disponível. Ele só pode ser acessado na rede privada, mas não na Internet.

## <a name="access-application-private-fqdn"></a>Acessar o FQDN privado do aplicativo

Após a atribuição, você pode acessar o FQDN privado do seu aplicativo na rede privada. Por exemplo, você pode criar um computador Jumpbox na mesma rede virtual ou uma rede virtual emparelhada e, nesse computador Jumpbox, o FQDN privado é acessível.

![Acessar ponto de extremidade privado na vnet](media/spring-cloud-access-app-vnet/access-private-endpoint.png)

## <a name="next-steps"></a>Próximas etapas

- [Expor aplicativos para a Internet usando o gateway de aplicativo e o Firewall do Azure](spring-cloud-expose-apps-gateway-azure-firewall.md)

## <a name="see-also"></a>Veja também

- [Solução de problemas do Azure Spring Cloud na VNET](spring-cloud-troubleshooting-vnet.md)
- [Responsabilidades do cliente para executar o Azure Spring Cloud na VNET](spring-cloud-vnet-customer-responsibilities.md)