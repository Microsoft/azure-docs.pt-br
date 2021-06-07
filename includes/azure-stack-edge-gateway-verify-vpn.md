---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: efa9e996cbfbc8954c294b4da8900b1d5bcbeeed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96465983"
---
Para verificar a VPN, você pode criar uma conta de armazenamento que só pode ser acessada pela rede virtual que você criou. Siga estas etapas para criar e associar essa conta de armazenamento à rede virtual que você criou:

1. Criar uma conta de armazenamento. Você pode usar a conta de armazenamento que planeja usar com seu dispositivo Azure Stack Edge. Tente acessar a conta de armazenamento de uma rede externa (fora da rede selecionada). A conta de armazenamento deve estar acessível.
2. Na portal do Azure, vá para a conta de armazenamento. 
3. Vá para **firewalls e redes virtuais**. No painel direito para **permitir o acesso**, escolha **redes selecionadas**. Em **proteger nossa conta de armazenamento com redes virtuais**, escolha **+ Adicionar rede virtual existente.**

    ![Verificar VPN 1](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-1.png)

4. Na folha **Adicionar redes** :

    - Selecione a assinatura. Essa é a mesma assinatura associada ao recurso Azure Stack Edge/Gateway do Data Box. 
    - Na lista suspensa, escolha a rede virtual a ser associada a essa conta de armazenamento. Selecione a rede virtual que você criou na etapa anterior.
    - Em **sub-redes**, escolha o **_padrão_* _ e o _GatewaySubnet *. Os pontos de extremidade de serviço serão habilitados para essas combinações de rede virtual/sub-rede. A habilitação do acesso leva até 15 minutos para ser concluída.
    - Selecione **Habilitar**.

    ![Verificar VPN 2](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-2.png)
    
4. Salvar **configurações**.

    ![Verificar VPN 3](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-3.png)

5. Depois que as configurações forem salvas, você poderá ver as sub-redes para as quais a rede virtual está habilitada.

    ![Verificar VPN 4](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-4.png)

5. Para verificar se os dados agora vão apenas pela VPN, siga estas etapas: 
    - Tente acessar a conta de armazenamento de uma rede externa (fora da rede selecionada). A conta de armazenamento não deve estar acessível. 
    - Tente acessar a conta de armazenamento da rede virtual/sub-redes que você habilitou nas redes selecionadas. A conta de armazenamento deve estar acessível. 
 
Você só poderá acessar essa conta de armazenamento se tiver a VPN habilitada. Se você desabilitar a VPN, também precisará ajustar as configurações de rede da conta de armazenamento. 

Para obter mais informações, vá para [configurar redes virtuais e firewalls de armazenamento do Azure](../articles/storage/common/storage-network-security.md). 

