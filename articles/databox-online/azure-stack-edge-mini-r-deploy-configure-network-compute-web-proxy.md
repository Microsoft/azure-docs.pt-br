---
title: Tutorial para definir as configurações de rede para um dispositivo Azure Stack Edge Mini R no portal do Azure
description: Tutorial para implantar uma GPU do Azure Stack Edge Mini R que instrui você a definir configurações da rede, da rede de computação e do proxy Web do seu dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: alkohli
ms.openlocfilehash: 34a11679626653afd04b0cd17c77188cbc995308
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061716"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-mini-r"></a>Tutorial: Configurar a rede para o Azure Stack Edge Mini R

Este tutorial descreve como configurar a rede para o dispositivo do Azure Stack Edge Mini R com uma GPU integrada usando a IU da Web local.

O processo de conexão pode levar cerca de 20 minutos para ser concluído.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Configurar a rede
> * Habilitar a rede de computação
> * Configurar o proxy Web


## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar seu dispositivo Azure Stack Edge Mini R, verifique o seguinte:

* Você instalou o dispositivo físico conforme detalhado em [Instalar o Azure Stack Edge Mini R](azure-stack-edge-gpu-deploy-install.md).
* Você se conectou à IU da Web local do dispositivo, conforme detalhado em [Conectar-se ao Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-connect.md)


## <a name="configure-network"></a>Configurar a rede

A página **Introdução** exibe as diversas configurações necessárias para configurar e registrar o dispositivo físico com o serviço do Azure Stack Edge. 

Siga estas etapas para configurar a rede para seu dispositivo.

1. Na IU da Web local do seu dispositivo, acesse a página **Introdução**. 

2. Se uma atualização de dia zero for necessária, você poderá fazer isso aqui configurando uma porta de dados com uma conexão com fio. Para saber mais sobre como configurar uma conexão com fio para o dispositivo, confira [Cabear o dispositivo](azure-stack-edge-mini-r-deploy-install.md#cable-the-device). Quando a atualização terminar, você poderá remover a conexão com fio.

3. Crie certificados para a Wi-Fi e a cadeia de assinatura. Os certificados da cadeia de assinatura e da Wi-Fi devem estar no formato DER com uma extensão de arquivo *.cer*. Para obter instruções, confira [Criar certificados](azure-stack-edge-gpu-manage-certificates.md).

4. Na IU da Web local, acesse **Introdução**. No bloco **Segurança**, selecione **Certificados** e escolha **Configurar**. 

    [![Página "Certificados" da IU da Web local](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/get-started-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/get-started-1.png#lightbox)

    1. Selecione **+ Adicionar certificado**. 
    
        [![Página "Certificados" na IU da Web local 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-1.png#lightbox)

    2. Carregue a cadeia de assinatura e selecione **Aplicar**.

        ![Página 2 de "Certificados" da IU da Web local](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-2.png)

    3. Repita o procedimento com o certificado de Wi-Fi. 

        ![Página 3 de "Certificados" da IU da Web local](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-4.png)

    4. Os novos certificados devem ser exibidos na página **Certificados**. 
    
        [![Página 4 de "Certificados" da IU da Web local](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-5.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-5.png#lightbox)

    5. Volte para **Introdução**.

3. No bloco **Rede**, selecione **Configurar**.  
    
    No dispositivo físico, há cinco adaptadores de rede. PORT 1 e PORT 2 são interfaces de rede de 1 Gbps. PORTA 3 e PORTA 4 são adaptadores de rede de 10 Gbps. A quinta é a porta Wi-Fi. 

    [![Página "Configurações de rede" na IU da Web local 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png#lightbox)    
    
    Selecione a porta Wi-Fi e defina suas configurações. 
    
    > [!IMPORTANT]
    > É altamente recomendável configurar um endereço IP estático para a porta Wi-Fi.  

    ![Página "Configurações de rede" da IU da Web local 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-2.png)

    A página **Rede** é atualizada após a aplicação das configurações da porta Wi-Fi.

    ![Página "Configurações de rede" na IU da Web local 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-4.png)
   
4. Selecione **Adicionar perfil de Wi-Fi** e carregue seu perfil de Wi-Fi. 

    !["Configurações de rede Wi-Fi da porta" na IU da Web local 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-1.png)
    
    Um perfil de rede sem fio contém o SSID (nome da rede), a chave de senha e as informações de segurança para se conectar a uma rede sem fio. Você pode obter o perfil de Wi-Fi do seu ambiente com seu administrador de rede.

    !["Configurações de rede Wi-Fi da porta" na IU da Web local 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-2.png)

    Após o perfil ser adicionado, a lista de perfis Wi-Fi é atualizada de maneira a refletir o novo perfil. O perfil deve mostrar o **Status da conexão** **Desconectado**. 

    !["Configurações de rede Wi-Fi da porta" na IU da Web local 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-3.png)


5. Após o perfil de rede sem fio ser carregado com êxito, conecte-se a ele. Selecione **Conectar-se ao perfil de Wi-Fi**. 

    !["Configurações de rede Wi-Fi da porta" na IU da Web local 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-4.png)

6. Selecione o perfil de Wi-Fi que você adicionou na etapa anterior e escolha **Aplicar**. 

    !["Configurações de rede Wi-Fi da porta" na IU da Web local 5](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-5.png)

    O **Status da conexão** deve ser atualizado para **Conectado**. A intensidade do sinal é atualizada para indicar sua qualidade. 

    !["Configurações de rede Wi-Fi da porta" na IU da Web local 6](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-6.png)

    > [!NOTE]
    > Para transferir grandes quantidades de dados, recomendamos que você use uma conexão com fio em vez da rede sem fio. 

6. Desconecte a PORTA 1 do dispositivo do laptop. 

7. Conforme você define as configurações de rede, tenha em mente:

   - Se o DHCP estiver ativado em seu ambiente, as interfaces de rede serão configuradas automaticamente. Um endereço IP, sub-rede, gateway e DNS são atribuídos automaticamente.
   - Se o DHCP não estiver ativado, você poderá atribuir IPs estáticos, se necessário.
   - Você pode configurar sua interface de rede como IPv4.
   - O agrupamento NIC (adaptador de rede) ou a agregação de link não tem suporte com o Azure Stack Edge.
   - O número de série de qualquer porta corresponde ao número de série do nó. Para um dispositivo da série K, apenas um número de série é exibido.

     >[!NOTE] 
     > É recomendável que você não alterne o endereço IP local do adaptador de rede estático para DCHP, a menos que tenha outro endereço IP para se conectar ao dispositivo. Se estiver usando um adaptador de rede e alternar para DHCP, não haverá como determinar o endereço DHCP. Se você quiser alterar para um endereço DHCP, espere até o dispositivo ser registrado com o serviço e então altere. Posteriormente, você pode visualizar os IPs de todos os adaptadores em **Propriedades do dispositivo** no portal do Azure para seu serviço.

Depois de configurar e aplicar as configurações de rede, selecione **Avançar: Computação** para configurar a rede de computação.

## <a name="enable-compute-network"></a>Habilitar a rede de computação

Siga estas etapas para habilitar a computação e configurar a rede de computação. 


1. Na página **Computação**, selecione o adaptador de rede que você deseja habilitar para a computação. 

    ![Página de computação na IU local 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-1.png)

1. Na caixa de diálogo **Configurações de rede**, selecione **Habilitar**. Quando você habilita a computação, um comutador virtual é criado em seu dispositivo nesse adaptador de rede. O comutador virtual é usado para a infraestrutura de computação no dispositivo. 
    
1. Atribuir **IPs de nó de Kubernetes**. Esses endereços IP estáticos são para a VM de computação.  

    Para um dispositivo de nó *n*, um intervalo contíguo de no mínimo *n + 1* endereços IPv4 (ou mais) são fornecidos para a VM de computação, usando os endereços IP inicial e final. Considerando que o Azure Stack Edge é um dispositivo de 1 nó, será fornecido o mínimo de 2 endereços IPv4 contíguos.

    > [!IMPORTANT]
    > O Kubernetes no Azure Stack Edge usa a sub-rede 172.27.0.0/16 para pod e a sub-rede 172.28.0.0/16 para o serviço. Verifique se elas não estão em uso na sua rede. Se essas sub-redes já estiverem em uso em sua rede, você poderá alterar essas sub-redes executando o cmdlet `Set-HcsKubeClusterNetworkInfo` da interface do PowerShell do dispositivo. Para obter mais informações, confira [Alterar o pod do Kubernetes e as sub-redes de serviço](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets).


1. Atribuir **IPs de serviço externo do Kubernetes**. Esses também são os endereços IP de balanceamento de carga. Esses endereços IP contíguos são usados nos serviços que você deseja expor fora do cluster do Kubernetes e você especifica o intervalo de IP estático com base no número de serviços expostos. 
    
    > [!IMPORTANT]
    > É altamente recomendável que você especifique no mínimo um endereço IP para o serviço de Hub do Azure Stack Edge Mini R para acessar os módulos de computação. Opcionalmente, você pode especificar mais endereços IP para outros serviços/módulos do IoT Edge (um por serviço/módulo) que precisam ser acessados de fora do cluster. Os endereços IP do serviço podem ser atualizados mais tarde. 
    
1. Selecione **Aplicar**.

    ![Página de computação na IU local 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-3.png)

1. A configuração leva alguns minutos para ser aplicada e talvez seja necessário atualizar o navegador. Você pode ver que a porta especificada está habilitada para computação. 
 
    ![Página de computação na IU local 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-4.png)

    Selecione **Avançar: proxy Web** para configurar o proxy Web.  

  
## <a name="configure-web-proxy"></a>Configurar o proxy Web

Esta é uma configuração opcional.

> [!IMPORTANT]
> * Se você habilitar a computação e usar o módulo do IoT Edge no seu dispositivo do Azure Stack Edge Mini R, recomendamos definir a autenticação de proxy Web como **Nenhuma**. Não há suporte para o NTLM.
>* Não há suporte para arquivos PAC (configuração automática de proxy). Um arquivo PAC define como os navegadores da Web e outros agentes de usuário podem escolher automaticamente o servidor proxy apropriado (método de acesso) para buscar uma determinada URL. Os proxies que tentam interceptar e ler todo o tráfego (e depois assinar novamente tudo com a própria certificação) não são compatíveis, pois o certificado do proxy não é confiável. Normalmente, os proxies transparentes funcionam bem com o Azure Stack Edge Mini R. Não há suporte para proxies Web não transparentes.


1. Na página **Configurações do proxy Web**, siga estas etapas:

    1. Na **URL do proxy Web**, digite a URL neste formato: `http://host-IP address or FQDN:Port number`. Não há suporte para URLs HTTPS.

    2. Em **Autenticação**, selecione **Nenhuma** ou **NTLM**. Se você habilitar a computação e usar o módulo do IoT Edge no seu dispositivo do Azure Stack Edge Mini R, recomendamos definir a autenticação de proxy Web como **Nenhuma**. Não há suporte para o **NTLM**.

    3. Se você estiver usando a autenticação, insira um nome de usuário e uma senha.

    4. Para validar e aplicar as configurações de proxy da Web, selecione **Aplicar**.
    
   ![Página "Configurações de proxy da Web" da IU da Web local](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/web-proxy-1.png)

2. Depois que as configurações forem aplicadas, selecione **Avançar: Dispositivo**.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Pré-requisitos
> * Configurar a rede
> * Habilitar a rede de computação
> * Configurar o proxy Web


Para saber como configurar seu dispositivo Azure Stack Edge Mini R, confira:

> [!div class="nextstepaction"]
> [Definir configurações de dispositivo](./azure-stack-edge-mini-r-deploy-set-up-device-update-time.md)
