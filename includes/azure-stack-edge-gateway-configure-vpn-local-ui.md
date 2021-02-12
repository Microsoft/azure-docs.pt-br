---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: fa65a7354112a2b220686372459b348d45832dd9
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465981"
---
Execute as etapas a seguir na interface do usuário da Web local do seu dispositivo. Esta etapa leva cerca de 15 minutos, incluindo o carregamento do arquivo de configuração de VPN (ou o arquivo de marca de serviço). 

1. Vá para **configuração > VPN**. Selecione **Configurar**.

    ![Configurar a interface do usuário local 1](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-1.png)

2. Na folha **Configurar VPN** :

    - Habilite **as configurações de VPN**.
    - Forneça o **segredo compartilhado de VPN**. Essa é a chave compartilhada que você forneceu ao criar o recurso de conexão VPN do Azure.
    - Forneça o endereço **IP do gateway de VPN** . Este é o endereço IP do gateway de rede local do Azure.
    - Para o **grupo PFS**, selecione **nenhum**. 
    - Para o **grupo DH**, selecione **group2**.
    - Para o **método de integridade IPSec**, selecione **SHA256**.
    - Para **constantes de transformação de codificação IPSec**, selecione **GCMAES256**.
    - Para obter as **constantes de transformação de autenticação IPsec**, selecione **GCMAES256**.
    - Para o **método de criptografia IKE**, selecione **aes256**.
    - Selecione **Aplicar**.

        ![Configurar a interface do usuário local 2](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-2.png)

    Para obter mais informações sobre os algoritmos criptográficos com suporte, vá para [sobre requisitos de criptografia e gateways de VPN do Azure](../articles/vpn-gateway/vpn-gateway-about-compliance-crypto.md#ipsecike-policy-faq). 

3. Para carregar o arquivo de configuração de rota de VPN, selecione **carregar**. 

    ![Configurar a interface do usuário local 3](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-3.png)

    - Navegue até o arquivo *JSON* de marcas de serviço que você baixou no sistema local na etapa anterior.
    - Selecione a região como a região do Azure associada ao dispositivo, à rede virtual e aos gateways.
    - Selecione **Aplicar**.

        ![Configurar a interface do usuário local 4](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-4.png)
    
    O carregamento leva cerca de 7-8 minutos no dispositivo.

4. Para adicionar rotas específicas do cliente, configure os intervalos de endereços IP a serem acessados usando somente VPN. 

    - Em **intervalos de endereços IP a serem acessados usando somente VPN**, selecione **Configurar**.
    - Forneça um intervalo IPv4 válido e selecione **Adicionar**. Repita as etapas para adicionar outros intervalos.
    - Selecione **Aplicar**.

        ![Configurar a interface do usuário local 5](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-5.png)

