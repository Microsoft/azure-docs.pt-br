---
title: Criar ou atualizar um gateway de aplicativo com um firewall do aplicativo Web | Microsoft Docs
description: Saiba como criar um gateway de aplicativo com o firewall do aplicativo Web usando o portal
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: b561a210-ed99-4ab4-be06-b49215e3255a
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: davidmu
ms.openlocfilehash: bfc06c1b44974fd17a3794654503d21d6407a917
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-by-using-the-portal"></a>Criar um gateway de aplicativo com um firewall do aplicativo Web usando o portal

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [CLI do Azure](application-gateway-web-application-firewall-cli.md)

Saiba como criar um gateway de aplicativo habilitado para firewall do aplicativo Web (WAF).

O WAF no Gateway de Aplicativo do Azure protege aplicativos Web contra ataques comuns baseados na Web, como injeção de SQL, ataques de scripts entre sites e sequestros de sessão. O WAF protege contra muitas das 10 principais vulnerabilidades da Web OWASP.

## <a name="scenarios"></a>Cenários

Este artigo apresenta dois cenários. No primeiro cenário, você aprenderá a [criar um gateway de aplicativo com um WAF](#create-an-application-gateway-with-web-application-firewall). No segundo cenário, você aprende a [adicionar o WAF a um gateway de aplicativo existente](#add-web-application-firewall-to-an-existing-application-gateway).

![Cenário de exemplo][scenario]

> [!NOTE]
> Você pode adicionar investigações de integridade personalizadas, endereços de pool de back-ends e regras extras ao gateway de aplicativo. Esses aplicativos são configurados depois que o gateway de aplicativo é configurado e não durante a implantação inicial.

## <a name="before-you-begin"></a>Antes de começar

 Um gateway de aplicativo exige sua própria sub-rede. Ao criar uma rede virtual, certifique-se de deixar espaço de endereço suficiente para ter várias sub-redes. Depois de implantar um gateway de aplicativo em uma sub-rede, apenas gateways de aplicativo adicionais poderão ser incluídos na sub-rede.

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Adicionar um firewall do aplicativo Web a um gateway de aplicativo existente

Este exemplo atualiza um gateway de aplicativo existente para dar suporte ao WAF no modo **Prevenção**.

1. No painel **Favoritos** do portal do Azure, selecione **Todos os recursos**. Na folha **Todos os recursos**, selecione o gateway de aplicativo existente. Se a assinatura escolhida já tiver vários recursos, insira o nome na caixa **Filtrar por nome** para acessar com facilidade a zona DNS.

   ![Seleção do gateway de aplicativo existente][1]

2. Selecione **Firewall do Aplicativo Web** e atualize as configurações do gateway de aplicativo. Quando a atualização for concluída, selecione **Salvar**. 

3. Use as seguintes configurações para atualizar um gateway de aplicativo existente para dar suporte a um WAF:

   | **Configuração** | **Valor** | **Detalhes**
   |---|---|---|
   |**Atualizar para camada WAF**| Verificado | Essa opção define a camada do gateway de aplicativo para a camada WAF.|
   |**Status do firewall**| Habilitado | Essa configuração habilita o firewall no WAF.|
   |**Modo de firewall** | Prevenção | Essa configuração é como o WAF lida com o tráfego mal-intencionado. O modo **Detecção** registra em log apenas os eventos. O modo **Prevenção** registra os eventos e interrompe o tráfego mal-intencionado.|
   |**Conjunto de regras**|3.0|Essa configuração determina o [conjunto principal de regras](application-gateway-web-application-firewall-overview.md#core-rule-sets) usado para proteger os membros do pool de back-ends.|
   |**Configurar regras desabilitadas**|Varia|Para evitar possíveis falsos positivos, você pode usar essa configuração para desabilitar determinadas [regras e grupos de regras](application-gateway-crs-rulegroups-rules.md).|

    >[!NOTE]
    > Ao fazer o upgrade de um gateway de aplicativo existente para o SKU do WAF, o tamanho do SKU muda para **médio**. Após a conclusão da configuração, você poderá reconfigurar esse parâmetro.

    ![Configurações Básicas][2-1]

    > [!NOTE]
    > Para exibir os logs do WAF, habilite o diagnóstico e escolha **ApplicationGatewayFirewallLog**. Escolha uma contagem de instância de **1** apenas para fins de teste. Não é recomendável uma contagem de instâncias em **2**, pois ele não é coberto por um SLA. Gateways pequenos não estão disponíveis quando você usa um WAF.

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>Como criar um gateway de aplicativo com um firewall do aplicativo Web

Este cenário:

* Criará um gateway de aplicativo WAF médio com duas instâncias.
* Criará uma rede virtual chamada AdatumAppGatewayVNET, com um bloco CIDR 10.0.0.0/16 reservado.
* Criará uma sub-rede chamada Appgatewaysubnet que usa 10.0.0.0/28 como seu bloco CIDR.
* Configurará um certificado para descarregamento SSL.

1. Entre no [Portal do Azure](https://portal.azure.com). Caso ainda não tenha uma conta, você poderá se inscrever para obter uma [avaliação gratuita de um mês](https://azure.microsoft.com/free).

2. No painel **Favoritos** do portal, clique em **Novo**.

3. Na folha **Novo**, selecione **Rede**. Na folha **Rede**, selecione **Gateway de Aplicativo**, conforme mostrado na seguinte imagem:

    ![Criação de gateway de aplicativo][1]

4. Na folha **Informações Básicas** exibida, insira os seguintes valores e selecione **OK**:

   | **Configuração** | **Valor** | **Detalhes**
   |---|---|---|
   |**Nome**|AdatumAppGateway|O nome do gateway de aplicativo.|
   |**Camada**|WAF|Os valores disponíveis são Standard e WAF. Para saber mais sobre um WAF, confira [Firewall do aplicativo Web](application-gateway-web-application-firewall-overview.md).|
   |**Tamanho do SKU**|Média|As opções de camada padrão são **Pequeno**, **Médio** e **Grande**. As opções da camada WAF são apenas **Médio** e **Grande**.|
   |**Contagem de instâncias**|2|O número de instâncias do gateway de aplicativo para alta disponibilidade. Use contagens de instância de 1 apenas para fins de teste.|
   |**Assinatura**|[Sua assinatura]|Selecione uma assinatura a ser usada para criar o gateway de aplicativo.|
   |**Grupo de recursos**|**Criar novo:** AdatumAppGatewayRG|Crie um grupos de recursos. O nome do grupo de recursos deve ser exclusivo na assinatura selecionada. Para saber mais sobre grupos de recursos, leia o artigo [Visão geral do Gerenciador de Recursos](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups).|
   |**Localidade**|Oeste dos EUA||

   ![Definição das configurações básicas][2-2]

5. Na folha **Configurações** exibida em **Rede virtual**, selecione **Escolher uma rede virtual**. Na folha **Escolher rede virtual**, clique em **Criar novo**.

   ![Escolha da rede virtual][2]

6. Na **folha Criar rede virtual**, insira os valores a seguir e selecione **OK**. O campo **Sub-rede** na folha **Configurações** é populado com a sub-rede que você escolheu.

   |**Configuração** | **Valor** | **Detalhes** |
   |---|---|---|
   |**Nome**|AdatumAppGatewayVNET|O nome do gateway de aplicativo.|
   |**Espaço de endereço**|10.0.0.0/16| Esse valor é o espaço de endereço da rede virtual.|
   |**Nome da sub-rede**|AppGatewaySubnet|O nome da sub-rede para o gateway de aplicativo.|
   |**Intervalo de endereços da sub-rede**|10.0.0.0/28 | Essa sub-rede permite mais sub-redes adicionais na rede virtual para membros do pool de back-ends.|

7. Na folha **Configurações**, em **Configuração de IP de front-end**, selecione **Público** como o **Tipo de endereço IP**.

8. Na folha **Configurações**, em **Endereço IP Público**, selecione **Escolher um endereço IP público**. Na folha **Escolher endereço IP público**, selecione **Criar novo**.

   ![Escolha do endereço IP público][3]

9. Na folha **Criar endereço IP público**, aceite o valor padrão e selecione **OK**. O campo **Endereço IP público** é populado com o endereço IP público que você escolheu.

10. Na folha **Configurações**, em **Configuração do ouvinte**, selecione **HTTP** em **Protocolo**. Um certificado é necessário para usar **HTTPS**. A chave privada do certificado é necessária. Forneça uma exportação .pfx do certificado e insira a senha para o arquivo.

11. Defina configurações específicas para o **WAF**.

   |**Configuração** | **Valor** | **Detalhes** |
   |---|---|---|
   |**Status do firewall**| habilitado| Essa configuração ativa ou desativa o WAF.|
   |**Modo de firewall** | Prevenção| Essa configuração determina as ações tomadas pelo WAF em relação ao tráfego mal-intencionado. O modo **Detecção** registra em log apenas o tráfego. O modo **Prevenção** registra em log e interrompe o tráfego com uma resposta 403 Não autorizado.|


12. Examine a página **Resumo** e selecione **OK**. Agora o Gateway de Aplicativo está na fila e será criado.

13. Quando o gateway de aplicativo for criado, vá até ele no portal para continuar a configuração do gateway de aplicativo.

    ![Modo de exibição de recursos do gateway de aplicativo][10]

Estas etapas criam um gateway de aplicativo básico com configurações padrão para o ouvinte, pool de back-ends, configurações de HTTP de back-end e regras. Depois que o provisionamento for finalizado com êxito, você poderá modificar essas configurações para ajustá-las à sua implantação.

> [!NOTE]
> Os gateways de aplicativos criados com a configuração básica do WAF são definidos com o CRS 3.0 para proteções.

## <a name="next-steps"></a>Próximas etapas

Para configurar um alias de domínio personalizado para o [endereço IP público](../dns/dns-custom-domain.md#public-ip-address), você pode usar o DNS do Azure ou outro provedor de DNS.

Para configurar o log de diagnósticos para registrar os eventos que são detectados ou evitados com o WAF, confira [Diagnóstico do Gateway de Aplicativo](application-gateway-diagnostics.md).

Para criar investigações de integridade personalizadas, confira [Criar uma investigação de integridade personalizada](application-gateway-create-probe-portal.md).

Para configurar o descarregamento SSL e afastar a onerosa assinatura de SSL dos seus servidores Web, confira [Configurar descarregamento SSL](application-gateway-ssl-portal.md).

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[2-1]: ./media/application-gateway-web-application-firewall-portal/figure2-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png
