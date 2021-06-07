---
title: Conectar-se de forma privada a um aplicativo Web do Azure usando o ponto de extremidade privado
description: Conecte-se em particular a um aplicativo Web usando o Ponto de extremidade privado do Azure
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 03/16/2021
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 4de405e07a9ae9d1efb33f2cee3630a1eefdef33
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655896"
---
# <a name="using-private-endpoints-for-azure-web-app"></a>Uso de pontos de extremidade privados para Aplicativo Web do Azure

> [!IMPORTANT]
> O ponto de extremidade privado está disponível para aplicativos Web do Windows e Linux, em contêineres ou não, hospedados nesses planos do serviço de aplicativo: aplicativos **isolados**, **PremiumV2**, **PremiumV3**, **funções Premium** (às vezes chamados de plano Premium elástico). 

Você pode usar o Ponto de extremidade privado para o Azure Web App para permitir que os clientes localizados na sua rede privada acessem o aplicativo com segurança pelo link privado. O ponto de extremidade privado usa um endereço IP do espaço de endereço da VNet do Azure. O tráfego de rede entre um cliente em sua rede privada e o Web App atravessa a VNet e um Link Privado na rede principal da Microsoft, eliminando a exposição da Internet pública.

O uso do Ponto de extremidade privado para o seu aplicativo da Web permite:

- Proteja seu Web App configurando o ponto de extremidade privado, eliminando a exposição pública.
- Conecte-se com segurança ao Web App a partir de redes locais que se conectam à VNet usando uma conexão privada VPN ou ExpressRoute.
- Evite qualquer exfiltração de dados da sua VNet. 

Se você precisar apenas de uma conexão segura entre sua VNet e seu aplicativo Web, um ponto de extremidade de serviço será a solução mais simples. Se você também precisar acessar o aplicativo Web do local por meio de um gateway do Azure, uma VNet emparelhada regionalmente ou uma VNet emparelhada globalmente, o ponto de extremidade privado será a solução.  

Para saber mais, confira [Ponto de extremidade de serviço][serviceendpoint].

## <a name="conceptual-overview"></a>Visão geral conceitual

Um ponto de extremidade privado é uma NIC (interface de rede especial) para seu aplicativo Web do Azure em uma sub-rede em sua rede virtual (VNet).
Quando você cria um ponto de extremidade privado para seu aplicativo Web, ele fornece conectividade segura entre clientes em sua rede privada e seu aplicativo Web. O ponto de extremidade privado recebe um endereço IP do intervalo de endereços IP de sua VNet.
A conexão entre o ponto de extremidade privado e o aplicativo Web usa um [link privado][privatelink] seguro. O ponto de extremidade privado é usado apenas para fluxos de entrada no seu aplicativo da Web. Os fluxos de saída não usarão esse ponto de extremidade privado, mas você pode injetar fluxos de saída na sua rede em uma sub-rede diferente por meio do [recurso de integração da VNet][vnetintegrationfeature].

A sub-rede na qual você conecta o ponto de extremidade privado pode ter outros recursos, não é necessária uma sub-rede vazia dedicada.
Você também pode implantar o ponto de extremidade privado em uma região diferente do aplicativo Web. 

> [!Note]
>O recurso de integração VNet não pode usar a mesma sub-rede que o ponto de extremidade privado, essa é uma limitação do recurso de integração VNet.

Do ponto de vista da segurança:

- Ao habilitar pontos de extremidade privados para seu aplicativo Web, você desabilita todo o acesso público.
- Você pode habilitar vários pontos de extremidade privados em outros VNets e sub-redes, incluindo VNets em outras regiões.
- O endereço IP da NIC do ponto de extremidade privado deve ser dinâmico, mas permanecerá o mesmo até que você exclua o ponto de extremidade privado.
- A NIC do ponto de extremidade privado não pode ter um NSG associado.
- A sub-rede que hospeda o ponto final privado pode ter um NSG associado, mas você deve desativar a imposição de políticas de rede para o ponto final privado: consulte [Desativar diretivas de rede para pontos finais privados][disablesecuritype]. Como resultado, você não pode filtrar por nenhum NSG o acesso ao seu endpoint privado.
- Quando você ativa o Ponto de extremidade privado no seu Web App, a configuração de [restrições de acesso][accessrestrictions] do Web App não é avaliada.
- Você pode eliminar o risco de vazamento de dados da VNet removendo todas as regras de NSG em que o destino é marca Internet ou serviços do Azure. Ao implantar um ponto de extremidade privado para um aplicativo da Web, você só pode acessar esse aplicativo da Web específico por meio do ponto de extremidade privado. Se você tiver outro aplicativo da Web, implante outro ponto de extremidade privado dedicado para esse outro aplicativo da Web.

Nos logs HTTP da Web do seu Web App, você encontrará o IP de origem do cliente. Esse recurso é implementado usando o protocolo proxy TCP, encaminhando a propriedade IP do cliente para o aplicativo Web. Para obter mais informações, consulte [Obtendo informações de conexão usando o TCP Proxy v2][tcpproxy].


  > [!div class="mx-imgBorder"]
  > ![Visão geral global do endpoint privado do aplicativo Web](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

Quando você usa o ponto de extremidade privado para o aplicativo Web, a URL solicitada deve corresponder ao nome do seu aplicativo Web. Por padrão, mywebappname.azurewebsites.net.

Por padrão, sem o ponto de extremidade privado, o nome público do seu aplicativo Web é um nome canônico para o cluster.
Por exemplo, a resolução de nome será:

|Nome |Tipo |Valor |
|-----|-----|------|
|mywebapp.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|A|40.122.110.154| 


Quando você implanta um ponto de extremidade privado, atualizamos a entrada DNS para apontar para o nome canônico mywebapp.privatelink.azurewebsites.net.
Por exemplo, a resolução de nome será:

|Nome |Tipo |Valor |Comentário |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|
|mywebapp.privatelink.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|A|40.122.110.154|<--esse IP público não é seu ponto de extremidade privado, você receberá um erro 403|

Você deve configurar um servidor DNS privado ou uma zona privada de DNS do Azure, para testes, você pode modificar a entrada de host do seu computador de teste.
A zona DNS que você precisa criar é: **privatelink.azurewebsites.net**. Registre o registro para seu aplicativo Web com um registro a e o IP do ponto de extremidade privado.
Por exemplo, a resolução de nome será:

|Nome |Tipo |Valor |Comentário |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|<--o Azure cria essa entrada no DNS público do Azure para apontar o serviço de aplicativo para o privatelink e isso é gerenciado por nós|
|mywebapp.privatelink.azurewebsites.net|A|10.10.10.8|<--você gerencia essa entrada em seu sistema DNS para apontar para seu endereço IP do ponto de extremidade privado|

Após essa configuração de DNS, você pode acessar seu aplicativo Web de forma privada com o nome padrão mywebappname.azurewebsites.net. Você deve usar esse nome, pois o certificado padrão é emitido para *. azurewebsites.net.


Se você precisar usar um nome DNS personalizado, deverá adicionar o nome personalizado em seu aplicativo Web. O nome personalizado deve ser validado como qualquer nome personalizado, usando a resolução de DNS público. Para mais informações, consulte [validação DNS personalizada][dnsvalidation].

Para o console do kudu ou a API REST do kudu (implantação com agentes do Azure DevOps auto-hospedados, por exemplo), você deve criar dois registros em sua zona privada do DNS do Azure ou seu servidor DNS personalizado. 

| Nome | Tipo | Valor |
|-----|-----|-----|
| mywebapp.privatelink.azurewebsites.net | A | PrivateEndpointIP | 
| mywebapp.scm.privatelink.azurewebsites.net | A | PrivateEndpointIP | 



## <a name="pricing"></a>Preços

Para obter detalhes de preço, confira [Preço do Link Privado do Azure][pricing].

## <a name="limitations"></a>Limitações

Ao usar a Função do Azure no Elastic Premium Plan com ponto de extremidade privado, para executar ou executar a função no portal da Web do Azure, você deve ter acesso direto à rede ou receberá um erro HTTP 403. Em outras palavras, seu navegador deve ser capaz de acessar o ponto de extremidade privado para executar a função no portal da Web do Azure. 

Você pode conectar até 100 pontos de extremidade privados a um aplicativo Web específico.

A funcionalidade de depuração remota não está disponível quando o ponto de extremidade privado está habilitado para o aplicativo Web. A recomendação é implantar o código em um slot e depurá-lo remotamente.

O acesso ao FTP é fornecido por meio do endereço IP público de entrada. O ponto de extremidade privado não dá suporte ao acesso FTP ao aplicativo Web.

Estamos melhorando o recurso de link privado e o ponto de extremidade privado regularmente, marque [este artigo][pllimitations] para obter informações atualizadas sobre as limitações.

## <a name="next-steps"></a>Próximas etapas

- Para implantar o ponto de extremidade privado para seu aplicativo Web por meio do portal, consulte [como conectar-se de forma privada a um aplicativo Web com o portal][howtoguide1]
- Para implantar o ponto de extremidade privado para seu aplicativo Web usando CLI do Azure, consulte [como conectar-se de forma privada a um aplicativo Web com CLI do Azure][howtoguide2]
- Para implantar o ponto de extremidade privado para seu aplicativo Web usando o PowerShell, consulte [como conectar-se de forma privada a um aplicativo Web com o PowerShell][howtoguide3]
- Para implantar o ponto de extremidade privado para seu aplicativo Web usando o modelo do Azure, confira [como conectar-se de forma privada a um aplicativo Web com o modelo do Azure][howtoguide4]
- Exemplo de ponta a ponta, como conectar um aplicativo Web front-end a um aplicativo Web de back-end protegido com injeção de VNet e ponto de extremidade privado com modelo ARM, consulte este guia de [início rápido][howtoguide5]
- Exemplo de ponta a ponta, como conectar um aplicativo Web front-end a um aplicativo Web de back-end protegido com injeção de VNet e ponto de extremidade privado com Terraform, consulte este [exemplo][howtoguide6]


<!--Links-->
[serviceendpoint]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[privatelink]: ../../private-link/private-link-overview.md
[vnetintegrationfeature]: ../web-sites-integrate-with-vnet.md
[disablesecuritype]: ../../private-link/disable-private-endpoint-network-policy.md
[accessrestrictions]: ../app-service-ip-restrictions.md
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: ../app-service-web-tutorial-custom-domain.md
[pllimitations]: ../../private-link/private-endpoint-overview.md#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide1]: ../../private-link/tutorial-private-endpoint-webapp-portal.md
[howtoguide2]: ../scripts/cli-deploy-privateendpoint.md
[howtoguide3]: ../scripts/powershell-deploy-private-endpoint.md
[howtoguide4]: ../scripts/template-deploy-private-endpoint.md
[howtoguide5]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-privateendpoint-vnet-injection
[howtoguide6]: ../scripts/terraform-secure-backend-frontend.md
