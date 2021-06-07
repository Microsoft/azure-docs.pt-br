---
title: Usar pontos de extremidade privados com contas do Lote do Azure
description: Saiba como conectar-se de forma privada a uma conta do lote do Azure usando pontos de extremidade privados.
ms.topic: how-to
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: d2e9d36e9e964f2e9f9a5a986fbf55d19b3069d8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919996"
---
# <a name="use-private-endpoints-with-azure-batch-accounts"></a>Usar pontos de extremidade privados com contas do Lote do Azure

Por padrão, [as contas do lote do Azure](accounts.md) têm um ponto de extremidade público e podem ser acessadas publicamente. O serviço de lote oferece a capacidade de criar contas do lote particulares, desabilitando o acesso à rede pública.

Usando o [link privado do Azure](../private-link/private-link-overview.md), você pode se conectar a uma conta do lote do Azure por meio de um [ponto de extremidade privado](../private-link/private-endpoint-overview.md). O ponto de extremidade privado é um conjunto de endereços IP privados em uma sub-rede dentro da rede virtual. Você pode então limitar o acesso a uma conta do lote do Azure sobre endereços IP privados.

O link privado permite que os usuários acessem uma conta do lote do Azure de dentro da rede virtual ou de qualquer rede virtual emparelhada. Os recursos mapeados para o link privado também podem ser acessados localmente no emparelhamento privado por meio de VPN ou [do Azure ExpressRoute](../expressroute/expressroute-introduction.md). Você pode se conectar a uma conta do lote do Azure configurada com o link privado usando o [método de aprovação automática ou manual](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow).

> [!IMPORTANT]
> O suporte para conectividade privada no lote do Azure está disponível atualmente para todas as regiões, exceto Alemanha central, Alemanha nordeste, Leste da China, Leste da China 2, Norte da China e Norte da China 2.

Este artigo descreve as etapas para criar uma conta do lote particular e acessá-la usando um ponto de extremidade privado.

## <a name="azure-portal"></a>Portal do Azure

Use as etapas a seguir para criar uma conta do lote particular usando o portal do Azure:

1. No painel **criar um recurso** , escolha **serviço de lote** e, em seguida, selecione **criar**.
2. Insira a assinatura, o grupo de recursos, a região e o nome da conta do lote na guia **noções básicas** e selecione **Avançar: avançado**.
3. Na guia **avançado** , defina **acesso à rede pública** como **desabilitado**.
4. Em **configurações**, selecione **conexões de ponto de extremidade privado** e, em seguida, selecione **+ ponto de extremidade privado**.
   :::image type="content" source="media/private-connectivity/private-endpoint-connections.png" alt-text="Conexões de ponto de extremidade privado":::
5. No painel **noções básicas** , insira ou selecione a assinatura, o grupo de recursos, o nome do recurso do ponto de extremidade privado e os detalhes da região e, em seguida, selecione **Avançar: recurso**.
6. No painel de **recursos** , defina o **tipo de recurso** como **Microsoft.Batch/batchAccounts**. Selecione a conta do lote particular que você deseja acessar e, em seguida, selecione **Avançar: configuração**.
   :::image type="content" source="media/private-connectivity/create-private-endpoint.png" alt-text="Criar um ponto de extremidade privado – painel de recursos":::
7. No painel **configuração** , insira ou selecione estas informações:
   - **Rede virtual**: selecione sua rede virtual.
   - **Sub-rede**: selecione sua sub-rede.
   - **Integrar com a zona DNS privada**: selecione **Sim**. Para se conectar em particular com o seu ponto de extremidade privado, você precisa de um registro DNS. Recomendamos que você integre seu ponto de extremidade privado a uma zona DNS privada. Você também pode usar seus próprios servidores DNS ou criar registros DNS usando os arquivos host em suas máquinas virtuais.
   - **Zona de DNS privado**: selecione privatelink. \<region\> . batch.azure.com. A zona DNS privada é determinada automaticamente. Você não pode alterá-la usando o portal do Azure.
8. Selecione **revisar + criar** e aguarde até que o Azure valide sua configuração.
9. Quando vir a mensagem **Validação aprovada**, selecione **Criar**.

Depois que o ponto de extremidade privado for provisionado, você poderá acessar a conta do lote de VMs na mesma rede virtual usando o ponto de extremidade privado.

> [!IMPORTANT]
> Executar operações fora da rede virtual em que o ponto de extremidade privado é provisionado resultará em uma mensagem "AuthorizationFailure" no portal do Azure.

Para exibir o endereço IP do portal do Azure:

1. Selecione **Todos os recursos**.
2. Pesquise o ponto de extremidade privado que você criou anteriormente.
3. Selecione a guia **Visão Geral** para ver as configurações de DNS e os endereços IP.

:::image type="content" source="media/private-connectivity/access-private.png" alt-text="Configurações de DNS e endereços IP do ponto de extremidade privado":::

## <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

Ao [criar uma conta do lote usando Azure Resource Manager modelo](quick-create-template.md), modifique o modelo para definir **publicNetworkAccess** como **desabilitado** , conforme mostrado abaixo.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "batchAccountName": {
      "type": "string",
      },
    "location": {
      "type": "string",
    }
  },
  "resources": [
    {
      "name": "[parameters('batchAccountName')]",
      "type": "Microsoft.Batch/batchAccounts",
      "apiVersion": "2020-03-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": []
      "properties": {
        "poolAllocationMode": "BatchService"
        "publicNetworkAccess": "Disabled"
      }
    }
  ]
}
```

## <a name="configure-dns-zones"></a>Configurar zonas DNS

Use uma [zona DNS privada](../dns/private-dns-privatednszone.md) na sub-rede em que você criou o ponto de extremidade privado. Configure os pontos de extremidade para que cada endereço IP privado seja mapeado para uma entrada DNS.

Ao criar o ponto de extremidade privado, você pode integrá-lo a uma [zona DNS privada](../dns/private-dns-privatednszone.md) no Azure. Se você optar por usar um [domínio personalizado](../dns/dns-custom-domain.md), deverá configurá-lo para adicionar registros DNS para todos os endereços IP privados reservados para o ponto de extremidade privado.

## <a name="pricing"></a>Preços

Para obter detalhes sobre os custos relacionados a pontos de extremidade privados, consulte [preços do link privado do Azure](https://azure.microsoft.com/pricing/details/private-link/).

## <a name="current-limitations-and-best-practices"></a>Limitações atuais e práticas recomendadas

Ao criar sua conta do lote particular, tenha em mente o seguinte:

- Os recursos do ponto de extremidade privado devem ser criados na mesma assinatura que a conta do lote.
- Para excluir a conexão privada, você deve excluir o recurso de ponto de extremidade privado.
- Depois que uma conta do lote é criada com acesso à rede pública, você não pode alterá-la somente para acesso privado.
- Os registros DNS na zona DNS privada não são removidos automaticamente quando você exclui um ponto de extremidade privado ou quando você remove uma região da conta do lote. Você deve remover manualmente os registros DNS antes de adicionar um novo ponto de extremidade privado vinculado a essa zona DNS privada. Se você não limpar os registros DNS, problemas de plano de dados inesperados podem acontecer, como interrupções de dados para regiões adicionadas após remoção de ponto de extremidade particular ou remoção de região.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar pools do lote em redes virtuais](batch-virtual-network.md).
- Saiba como [criar pools do lote sem endereços IP públicos](batch-pool-no-public-ip-address.md)
- Saiba como [criar pools do lote com endereços IP públicos especificados](create-pool-public-ip.md).
- Saiba mais sobre o [link privado do Azure](../private-link/private-link-overview.md).
