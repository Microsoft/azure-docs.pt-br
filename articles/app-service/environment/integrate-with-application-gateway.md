---
title: Integrar no Gateway de Aplicativo
description: Saiba como integrar um aplicativo em seu ILB Ambiente do Serviço de Aplicativo com um gateway de aplicativo neste passo a passo de ponta a ponta.
author: ccompy
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.topic: article
ms.date: 03/03/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 3b73d528802a8aa33c6122eaf5edfa9d046b6753
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88962070"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>Integrar o Ambiente do Serviço de Aplicativo do ILB com o Gateway de Aplicativo do Azure #

O [Ambiente do Serviço de Aplicativo](./intro.md) é uma implantação do Serviço de Aplicativo do Azure na sub-rede da rede virtual do Azure de um cliente. Ele pode ser implantado com um ponto de extremidade público ou privado para acesso ao aplicativo. A implantação do Ambiente do Serviço de Aplicativo com um ponto de extremidade privado (ou seja, um balanceador de carga interno) é chamada de um Ambiente do Serviço de Aplicativo do ILB.  

Firewalls do aplicativo Web ajudam na segurança do aplicativos Web ao inspecionar o tráfego de entrada da Web para bloquear injeções de SQL, script entre sites, uploads de malware, DDoS de aplicativo e outros ataques. Ele também inspeciona as respostas dos servidores Web back-end para DLP (Prevenção contra Perda de Dados). Você pode obter um dispositivo WAF no Microsoft Azure Marketplace ou usar o [Gateway de Aplicativo do Azure][appgw].

O gateway de Aplicativo Azure é uma solução de virtualização que fornece balanceamento de carga de camada 7, descarregamento de TLS/SSL e proteção WAF (firewall de aplicativo Web). Ele pode escutar em um endereço IP público e rotear tráfego para o ponto de extremidade do aplicativo. As informações a seguir descrevem como integrar um Gateway de Aplicativo com WAF configurado a um aplicativo em um ILB ASE.  

A integração do gateway de aplicativo com o Ambiente do Serviço de Aplicativo do ILB é em um nível de aplicativo. Quando você configura o gateway de aplicativo com seu Ambiente do Serviço de Aplicativo do ILB, você faz isso para aplicativos específicos no Ambiente do Serviço de Aplicativo do ILB. Essa técnica permite hospedar aplicativos multilocatários seguros em um único Ambiente do Serviço de Aplicativo do ILB.  

![Gateway de Aplicativo apontando para o aplicativo em um Ambiente do Serviço de Aplicativo do ILB][1]

Neste passo a passo, você vai:

* Criar um Gateway de Aplicativo do Azure.
* Configurar o Gateway de Aplicativo para apontar para um aplicativo no Ambiente do Serviço de Aplicativo do ILB.
* Configurar seu aplicativo para respeitar o nome de domínio personalizado.
* Editar o nome do host DNS público que aponta para o gateway de aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

Para integrar o Gateway de Aplicativo ao Ambiente do Ambiente do Serviço de Aplicativo do ILB, você precisa de:

* Um Ambiente do Serviço de Aplicativo do ILB.
* Um aplicativo em execução no Ambiente do Serviço de Aplicativo do ILB.
* Um nome de domínio roteável da Internet a ser usado com seu aplicativo no Ambiente do Serviço de Aplicativo do ILB.
* O endereço ILB que seu Ambiente do Serviço de Aplicativo do ILB usa. Essas informações estão no portal do ambiente do serviço de aplicativo em **configurações**  >  **endereços IP**:

    ![Lista de exemplos de endereços IP usados pelo Ambiente do Serviço de Aplicativo do ILB][9]
    
* Um nome DNS público que é usado posteriormente para apontar para o Gateway de Aplicativo. 

Para saber mais como criar um Ambiente do Serviço de Aplicativo do ILB, veja [Como criar e usar um Ambiente do Serviço de Aplicativo do ILB][ilbase].

Este artigo pressupõe que você deseja um Gateway de Aplicativo na mesma rede virtual do Azure em que o Ambiente do Serviço de Aplicativo está implantado. Antes de iniciar a criação do Gateway de Aplicativo, escolha ou crie uma sub-rede que você usará para hospedar o Gateway de Aplicativo. 

Você deve usar uma sub-rede diferente daquela nomeada GatewaySubnet. Se você colocar o Gateway de Aplicativo na GatewaySubnet, não será possível criar um gateway de rede virtual mais tarde. 

Você também consegue colocar o gateway na sub-rede que usa o Ambiente do Serviço de Aplicativo do ILB. O Ambiente do Serviço de Aplicativo é a única coisa que pode estar nesta sub-rede.

## <a name="configuration-steps"></a>Etapas de configuração ##

1. Na portal do Azure, vá para **novo**  >  **rede**  >  **Gateway de aplicativo**.

2. Na área **Básico**:

   a. Para **Nome**, insira o nome do Gateway de Aplicativo.

   b. Para **Camada**, selecione **WAF**.

   c. Para **Assinatura**, selecione a mesma assinatura que a rede virtual do Ambiente do Serviço de Aplicativo usa.

   d. Para **Grupo de recursos**, crie ou selecione o grupo de recursos.

   e. Para **Local**, selecione o local da rede virtual do Ambiente do Serviço de Aplicativo.

   ![Novas noções básicas de criação de Gateway de Aplicativo][2]

3. Na área **Configurações**:

   a. Para **Rede virtual**, selecione a rede virtual do Ambiente do Serviço de Aplicativo.

   b. Para **Sub-rede**, selecione a sub-rede onde o Gateway de Aplicativo precisa ser implantado. Não use o GatewaySubnet, pois isso impedirá a criação de gateways de VPN.

   c. Para **Tipo de endereço IP**, selecione **Público**.

   d. Para **Endereço IP público**, selecione um endereço IP público. Se não tiver um, crie um agora.

   e. Para **Protocolo**, selecione **HTTP** ou **HTTPS**. Se você configurar para HTTPS, será preciso fornecer um certificado PFX.

   f. Para **Firewall do aplicativo Web**, você pode habilitar o firewall e também defini-lo para **Detecção** ou **Prevenção**, conforme você considerar adequado.

   ![Configurações de criação de novo Gateway de Aplicativo][3]
    
4. Na seção **Resumo**, analise as configurações e selecione **OK**. Seu Gateway de Aplicativo pode demorar um pouco mais de 30 minutos para concluir a configuração.  

5. Depois de concluir a configuração do Gateway de Aplicativo, vá para o Portal do Gateway de Aplicativo. Selecione **pool de back-end**. Adicione o endereço ILB para seu Ambiente do Serviço de Aplicativo do ILB.

   ![Configurar o pool de back-end][4]

6. Depois que o processo para a configuração do pool de back-end for concluído, selecione **Investigações de integridade**. Crie uma investigação de integridade para o nome de domínio que você deseja usar para o aplicativo. 

   ![Configurar investigações da integridade][5]
    
7. Depois que o processo para a configuração das investigações de integridade for concluído, selecione **Configurações de HTTP**. Edite as configurações existentes, selecione **Usar investigação personalizada** e selecione o teste que você configurou.

   ![Definir configurações HTTP][6]
    
8. Vá para a seção **Visão Geral** do Gateway de Aplicativo e copie o endereço IP público que o Gateway de Aplicativo usa. Defina esse endereço IP como um registro para seu nome de domínio de aplicativo ou use o nome DNS para esse endereço em um registro CNAME. É mais fácil selecionar o endereço IP público e copiá-lo do endereço IP público da interface do usuário em vez de copiá-lo do link na seção **Visão Geral** de Gateway de Aplicativo. 

   ![Portal do Gateway de Aplicativo][7]

9. Defina o nome de domínio personalizado para o aplicativo no Ambiente do Serviço de Aplicativo do ILB. Vá para seu aplicativo no portal e, em **configurações**, selecione **domínios personalizados**.

   ![Definir o nome de domínio personalizado no aplicativo][8]

Há informações sobre como definir nomes de domínio personalizados para seus aplicativos Web no artigo [Configurando nomes de domínio personalizados para seu aplicativo Web][custom-domain]. Mas, para um aplicativo em um Ambiente do Serviço de Aplicativo do ILB, não há nenhuma validação no nome de domínio. Como você é proprietário do DNS que gerencia os pontos de extremidade do aplicativo, você pode colocar tudo que quiser lá. O nome de domínio personalizado que você adicionar nesse caso não precisará ser no DNS, mas ainda precisará ser configurado com o aplicativo. 

Depois da instalação ser concluída e você permitir um curto período de tempo para que as alterações ao DNS serem propagadas, você poderá acessar o aplicativo usando o nome de domínio personalizado que você criou. 


<!--IMAGES-->
[1]: ./media/integrate-with-application-gateway/appgw-highlevel.png
[2]: ./media/integrate-with-application-gateway/appgw-createbasics.png
[3]: ./media/integrate-with-application-gateway/appgw-createsettings.png
[4]: ./media/integrate-with-application-gateway/appgw-backendpool.png
[5]: ./media/integrate-with-application-gateway/appgw-healthprobe.png
[6]: ./media/integrate-with-application-gateway/appgw-httpsettings.png
[7]: ./media/integrate-with-application-gateway/appgw-publicip.png
[8]: ./media/integrate-with-application-gateway/appgw-customdomainname.png
[9]: ./media/integrate-with-application-gateway/appgw-iplist.png

<!--LINKS-->
[appgw]: ../../application-gateway/overview.md
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md