---
title: Tutorial para definir as configurações de rede para um dispositivo Azure Stack Edge Pro R no portal do Azure | Microsoft Docs
description: Tutorial para implantar um Azure Stack Edge Pro R que instrui você a definir configurações da rede, da rede de computação e do proxy Web do seu dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: alkohli
ms.openlocfilehash: a1f3966c8794b50f6ec369f1ea86905c4d8aaf3f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059931"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-pro-r"></a>Tutorial: Configurar a rede para o Azure Stack Edge Pro R

Este tutorial descreve como configurar a rede para o dispositivo Azure Stack Edge Pro R usando a IU da Web local.

O processo de conexão pode levar cerca de 20 minutos para ser concluído.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Configurar a rede
> * Habilitar a rede de computação
> * Configurar o proxy Web


## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar seu dispositivo Azure Stack Edge Pro R, verifique o seguinte:

* Você instalou o dispositivo físico conforme detalhado em [Instalar o Azure Stack Edge Pro R](azure-stack-edge-gpu-deploy-install.md).
* Você se conectou à IU da Web local do dispositivo, conforme detalhado em [Conectar-se ao Azure Stack Edge Pro R](azure-stack-edge-gpu-deploy-connect.md)


## <a name="configure-network"></a>Configurar a rede

A página **Introdução** exibe as diversas configurações necessárias para configurar e registrar o dispositivo físico com o serviço do Azure Stack Edge. 

Siga estas etapas para configurar a rede para seu dispositivo.

1. Na IU da Web local do seu dispositivo, acesse a página **Introdução**. 

2. No bloco **Rede**, selecione **Configurar** para acessar a página **Rede**. 
    
    <!--![Local web UI "Network settings" tile](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-1.png)-->

    No dispositivo físico, há quatro adaptadores de rede. PORT 1 e PORT 2 são interfaces de rede de 1 Gbps. PORTA 3 e PORTA 4 são adaptadores de rede de 10/25 Gbps. A PORTA 1 é configurada automaticamente como uma porta somente de gerenciamento, enquanto as PORTAS 2 a 4 são portas de dados. A página **Rede** é a mostrado abaixo.
    
    ![Página "Configurações de rede" da IU da Web local](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-2.png)

   
3. Para alterar as configurações de rede, selecione uma porta e, no painel direito que aparecer, modifique o endereço IP, a sub-rede, o gateway, o DNS primário e o DNS secundário. 

    - Se você selecionar Porta 1, verá que ela é pré-configurada como estática. 

        !["Configurações de rede da Porta 1" da IU da Web local](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-3.png)

    - Se você selecionar Porta 2, Porta 3 e Porta 4, verá que essas portas são configuradas como DHCP por padrão.

        !["Configurações de rede da Porta 3" da IU da Web local](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-4.png)

    Conforme você define as configurações de rede, tenha em mente:

   * Se o DHCP estiver ativado em seu ambiente, as interfaces de rede serão configuradas automaticamente. Um endereço IP, sub-rede, gateway e DNS são atribuídos automaticamente.
   * Se o DHCP não estiver ativado, você poderá atribuir IPs estáticos, se necessário.
   * Você pode configurar sua interface de rede como IPv4.
   * O agrupamento NIC (adaptador de rede) ou a agregação de link não tem suporte com o Azure Stack Edge.
   * O número de série de qualquer porta corresponde ao número de série do nó.
    <!--* On the 25-Gbps interfaces, you can set the RDMA (Remote Direct Access Memory) mode to iWarp or RoCE (RDMA over Converged Ethernet). Where low latencies are the primary requirement and scalability is not a concern, use RoCE. When latency is a key requirement, but ease-of-use and scalability are also high priorities, iWARP is the best candidate.-->
    Quando a rede do dispositivo estiver configurada, a página será atualizada conforme mostrado abaixo.

    ![Página "Configurações de rede" da IU da Web local 2](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-2a.png)<!--change-->


     >[!NOTE]
     >
     > * É recomendável que você não alterne o endereço IP local do adaptador de rede estático para DCHP, a menos que tenha outro endereço IP para se conectar ao dispositivo. Se estiver usando um adaptador de rede e alternar para DHCP, não haverá como determinar o endereço DHCP. Se você quiser alterar para um endereço DHCP, espere até o dispositivo ser ativado com o serviço e então altere. Posteriormente, você pode visualizar os IPs de todos os adaptadores em **Propriedades do dispositivo** no portal do Azure para seu serviço.


    Depois de configurar e aplicar as configurações de rede, selecione **Avançar: Computação** para configurar a rede de computação.

## <a name="enable-compute-network"></a>Habilitar a rede de computação

Siga estas etapas para habilitar a computação e configurar a rede de computação. 

1. Na página **Computação**, selecione o adaptador de rede que você deseja habilitar para a computação. 

    ![Página de computação na IU local 2](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-2.png)

1. Na caixa de diálogo **Configurações de rede**, selecione **Habilitar**. Quando você habilita a computação, um comutador virtual é criado em seu dispositivo nesse adaptador de rede. O comutador virtual é usado para a infraestrutura de computação no dispositivo. 
    
1. Atribuir **IPs de nó de Kubernetes**. Esses endereços IP estáticos são para a VM de computação.  

    Para um dispositivo de nó *n*, um intervalo contíguo de no mínimo *n + 1* endereços IPv4 (ou mais) são fornecidos para a VM de computação, usando os endereços IP inicial e final. Considerando que o Azure Stack Edge é um dispositivo de 1 nó, será fornecido o mínimo de 2 endereços IPv4 contíguos. Esses endereços IP precisam estar na mesma rede em que você habilitou a computação e em que o comutador virtual foi criado.

    > [!IMPORTANT]
    > O Kubernetes no Azure Stack Edge usa a sub-rede 172.27.0.0/16 para pod e a sub-rede 172.28.0.0/16 para o serviço. Verifique se elas não estão em uso na sua rede. Se essas sub-redes já estiverem em uso em sua rede, você poderá alterar essas sub-redes executando o cmdlet `Set-HcsKubeClusterNetworkInfo` da interface do PowerShell do dispositivo. Para obter mais informações, confira [Alterar o pod do Kubernetes e as sub-redes de serviço](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets).


1. Atribuir **IPs de serviço externo do Kubernetes**. Esses também são os endereços IP de balanceamento de carga. Esses endereços IP contíguos são para serviços que você deseja expor fora do cluster Kubernetes e você especifica o intervalo de IP estático com base no número de serviços expostos. 
    
    > [!IMPORTANT]
    > É altamente recomendável que você especifique no mínimo um endereço IP para o serviço de Hub do Azure Stack Edge Pro R para acessar os módulos de computação. Opcionalmente, você pode especificar mais endereços IP para outros serviços/módulos do IoT Edge (um por serviço/módulo) que precisam ser acessados de fora do cluster. Os endereços IP do serviço podem ser atualizados mais tarde. 
    
1. Selecione **Aplicar**.

    ![Página de computação na IU local 3](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-3.png)

1. A configuração leva alguns minutos para ser aplicada e talvez seja necessário atualizar o navegador. Você pode ver que a porta especificada está habilitada para computação. 
 
    ![Página de computação na IU local 4](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-4.png)

    Selecione **Avançar: proxy Web** para configurar o proxy Web.  

  
## <a name="configure-web-proxy"></a>Configurar o proxy Web

Esta é uma configuração opcional.

> [!IMPORTANT]
> * Se você habilitar a computação e usar o módulo do IoT Edge no seu dispositivo Azure Stack Edge Pro R, recomendamos definir a autenticação de proxy Web como **Nenhuma**. Não há suporte para o NTLM.
>* Não há suporte para arquivos PAC (configuração automática de proxy). Um arquivo PAC define como os navegadores da Web e outros agentes de usuário podem escolher automaticamente o servidor proxy apropriado (método de acesso) para buscar uma determinada URL. Os proxies que tentam interceptar e ler todo o tráfego (e depois assinar novamente tudo com a própria certificação) não são compatíveis, pois o certificado do proxy não é confiável. Normalmente, os proxies transparentes funcionam bem com o Azure Stack Edge Pro R. Não há suporte para proxies Web não transparentes.


1. Na página **Configurações do proxy Web**, siga estas etapas:

    1. Na **URL do proxy Web**, digite a URL neste formato: `http://host-IP address or FQDN:Port number`. Não há suporte para URLs HTTPS.

    2. Em **Autenticação**, selecione **Nenhuma** ou **NTLM**. Se você habilitar a computação e usar o módulo do IoT Edge no seu dispositivo Azure Stack Edge Pro R, será conveniente definir a autenticação de proxy Web como **Nenhum**. Não há suporte para o **NTLM**.

    3. Se você estiver usando a autenticação, insira um nome de usuário e uma senha.

    4. Para validar e aplicar as configurações de proxy da Web, selecione **Aplicar**.
    
   ![Página "Configurações de proxy da Web" da IU da Web local 2](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/web-proxy-2.png)

2. Depois que as configurações forem aplicadas, selecione **Avançar: Dispositivo**.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Pré-requisitos
> * Configurar a rede
> * Habilitar a rede de computação
> * Configurar o proxy Web


Para saber como configurar seu dispositivo Azure Stack Edge Pro R, confira:

> [!div class="nextstepaction"]
> [Definir configurações de dispositivo](./azure-stack-edge-pro-r-deploy-set-up-device-update-time.md)
