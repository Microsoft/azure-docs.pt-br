---
title: Gerenciamento de Wi-Fi do Azure Stack Edge
description: Descreve como usar o portal do Azure para gerenciar Wi-Fi no seu Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/24/2021
ms.author: alkohli
ms.openlocfilehash: a2cc0707c344c3ca537795666a3f60f648026596
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043760"
---
# <a name="use-the-local-web-ui-to-manage-wireless-connectivity-on-your-azure-stack-edge-mini-r"></a>Use a interface do usuário da Web local para gerenciar a conectividade sem fio em seu Azure Stack Edge mini R

Este artigo descreve como gerenciar a conectividade de rede sem fio no seu Azure Stack dispositivo mini R de borda. Você pode usar a interface do usuário da Web local em seu dispositivo de borda Azure Stack mini R via para adicionar, conectar e excluir perfis de Wi-Fi.

## <a name="about-wi-fi"></a>Sobre Wi-Fi

Seu dispositivo de borda do Azure Stack mini R pode operar ambos quando conectados à rede ou por meio de uma rede sem fio. O dispositivo tem uma porta Wi-Fi que deve ser habilitada para permitir que o dispositivo se conecte a uma rede sem fio. 

Seu dispositivo tem cinco portas, porta 1 até a porta 4 e uma quinta Wi-Fi porta. Aqui está um diagrama do plano de fundo de um dispositivo de mini-R quando conectado a uma rede sem fio.

![Cabeamento para Wi-Fi](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)


## <a name="add-connect-to-wi-fi-profile"></a>Adicionar, conectar-se ao Wi-Fi perfil

Execute as etapas a seguir na interface do usuário local do seu dispositivo para adicionar e conectar-se a um perfil de Wi-Fi.

1. Acesse a página **Introdução** na IU da Web local do seu dispositivo. No bloco **Rede**, selecione **Configurar**.  
    
    No dispositivo físico, há cinco adaptadores de rede. PORT 1 e PORT 2 são interfaces de rede de 1 Gbps. PORTA 3 e PORTA 4 são adaptadores de rede de 10 Gbps. A quinta é a porta Wi-Fi. 

    [![Página "Configurações de rede" na IU da Web local 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png#lightbox)  
    
    Selecione a porta Wi-Fi e defina suas configurações. 
    
    > [!IMPORTANT]
    > É altamente recomendável configurar um endereço IP estático para a porta Wi-Fi.  

    ![Página "Configurações de rede" da IU da Web local 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-2.png)

    A página **Rede** é atualizada após a aplicação das configurações da porta Wi-Fi.

    ![Página "Configurações de rede" na IU da Web local 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-4.png)

   
2. Selecione **Adicionar perfil de Wi-Fi** e carregue seu perfil de Wi-Fi. 

    !["Configurações de rede Wi-Fi da porta" na IU da Web local 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-1.png)
    
    Um perfil de rede sem fio contém o SSID (nome da rede), a chave de senha e as informações de segurança para se conectar a uma rede sem fio. Você pode obter o perfil de Wi-Fi do seu ambiente com seu administrador de rede.

    Para obter informações sobre como preparar seus perfis de Wi-Fi, consulte [usar perfis de Wi-Fi com dispositivos de borda de Azure Stack mini](azure-stack-edge-mini-r-use-wifi-profiles.md).

    !["Configurações de rede Wi-Fi da porta" na IU da Web local 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-2.png)

    Após o perfil ser adicionado, a lista de perfis Wi-Fi é atualizada de maneira a refletir o novo perfil. O perfil deve mostrar o **Status da conexão** **Desconectado**. 

    !["Configurações de rede Wi-Fi da porta" na IU da Web local 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-3.png)
    
3. Após o perfil de rede sem fio ser carregado com êxito, conecte-se a ele. Selecione **Conectar-se ao perfil de Wi-Fi**. 

    !["Configurações de rede Wi-Fi da porta" na IU da Web local 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-4.png)

4. Selecione o perfil de Wi-Fi que você adicionou na etapa anterior e escolha **Aplicar**. 

    !["Configurações de rede Wi-Fi da porta" na IU da Web local 5](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-5.png)

    O **Status da conexão** deve ser atualizado para **Conectado**. A intensidade do sinal é atualizada para indicar sua qualidade. 

    !["Configurações de rede Wi-Fi da porta" na IU da Web local 6](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-6.png)

    > [!NOTE]
    > Para transferir grandes quantidades de dados, recomendamos que você use uma conexão com fio em vez da rede sem fio. 


## <a name="download-wi-fi-profile"></a>Baixar perfil de Wi-Fi

Você pode baixar um perfil de Wi-Fi que você está usando para a conectividade de rede sem fio.

1. Na interface do usuário da Web local do seu dispositivo, vá para **configuração > rede**. 

2. Em Wi-Fi configurações de perfil, selecione **baixar perfil**. Isso deve baixar o perfil de Wi-Fi que você está usando no momento.


## <a name="delete-wi-fi-profile"></a>Excluir perfil de Wi-Fi

Você pode excluir um perfil de Wi-Fi que você está usando para a conectividade de rede sem fio.


1. Na interface do usuário da Web local do seu dispositivo, vá para **configuração > rede**. 

2. Em Wi-Fi configurações de perfil, selecione **excluir Wi-Fi perfil**.

3. Na folha **excluir perfil de Wi-Fi** , escolha o perfil que você deseja excluir. Selecione **Aplicar**.


## <a name="configure-cisco-wi-fi-profile"></a>Configurar o perfil do Cisco Wi-Fi

Aqui estão algumas diretrizes sobre como gerenciar e configurar um Cisco Wireless Controller e um ponto de acesso em seu dispositivo. 

### <a name="dhcp-bridging-mode"></a>Modo de ponte DHCP

Para usar um controlador Cisco sem fio para seu dispositivo, você deve habilitar o modo de ponte DHCP (protocolo de configuração dinâmica de hosts) no WLC (controlador de LAN sem fio).

Para obter mais informações, consulte [modo de ponte DHCP](https://www.cisco.com/c/en/us/support/docs/wireless/4400-series-wireless-lan-controllers/110865-dhcp-wlc.html#anc9).

#### <a name="bridging-configuration-example"></a>Exemplo de configuração de ponte

Para habilitar a funcionalidade de ponte DHCP no controlador, você deve desabilitar o recurso de proxy DHCP no controlador. Para habilitar a ponte DHCP usando a linha de comando:

```powershell
(Cisco Controller) > config dhcp proxy disable
(Cisco Controller) > show dhcp proxy
DHCP Proxy Behaviour: disabled
```

Se o servidor DHCP não existir na mesma rede de camada 2 (L2) que o cliente, a difusão deverá ser encaminhada para o servidor DHCP no gateway do cliente usando um auxiliar de IP. Este é um exemplo dessa configuração:

```powershell
Switch#conf t
Switch(config)#interface vlan <client vlan #>
Switch(config-if)#ip helper-address <dhcp server IP>
```

O recurso de ponte DHCP é uma configuração global, portanto, ele afeta todas as transações DHCP no controlador. Você precisa adicionar instruções auxiliares de IP na infraestrutura com fio para todas as redes locais virtuais (VLAN) do controlador.

### <a name="enable-the-passive-client-for-wlan"></a>Habilitar o cliente passivo para WLAN

Para habilitar o recurso de cliente passivo para WLAN (rede local sem fio) em um controlador Cisco sem fio:

* A interface associada à WLAN deve ter uma marcação de VLAN habilitada.
* A VLAN multicast deve estar habilitada para a WLAN.
* O encaminhamento de GARP deve estar habilitado no WLC.

Para obter mais informações, consulte [informações de VLAN multicast sobre otimização de multicast](https://www.cisco.com/c/en/us/td/docs/wireless/controller/8-5/config-guide/b_cg85/wlan_interfaces.html).

### <a name="troubleshoot"></a>Solucionar problemas

Se você encontrar problemas com as alocações de endereço IP em VMs em execução em um dispositivo de borda Azure Stack mini R, as definições de configuração acima em seu ambiente de rede devem ser validadas.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [implantar Azure Stack dispositivo de borda mini R](azure-stack-edge-mini-r-deploy-prep.md).
