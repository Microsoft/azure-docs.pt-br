---
title: Mover a configuração de IP público do Azure para outra região do Azure portal do Azure
description: Use um modelo para mover a configuração de IP público do Azure de uma região do Azure para outra usando o portal do Azure.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: b00fca8cf39bc44e0e53a112a332e6f6c5f0194e
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218596"
---
# <a name="move-azure-public-ip-configuration-to-another-region-using-the-azure-portal"></a>Mover a configuração de IP público do Azure para outra região usando o portal do Azure

Há vários cenários em que é possível mover as configurações de IP do Azure existentes de uma região para outra. Por exemplo, talvez você queira criar um IP com a mesma configuração e SKU para teste. Talvez você também queira mover uma configuração de IP para outra região como parte do planejamento de recuperação de desastre.

**Os IPs do Azure são específicos da região e não podem ser movidos de uma região para outra.** No entanto, você pode usar um modelo do Azure Resource Manager para exportar a configuração existente de um IP.  Em seguida, você pode preparar o recurso em outra região, exportando o IP para um modelo, modificando os parâmetros para corresponder à região de destino e, em seguida, implantando o modelo na nova região.  Para obter mais informações sobre o Resource Manager e modelos, consulte [Início Rápido: Crie e implante modelos do Azure Resource Manager usando o portal do Azure](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).


## <a name="prerequisites"></a>Pré-requisitos

- Verifique se o IP do Azure está na região do Azure da qual você quer mover.

- Os IPs públicos do Azure não podem ser movidos entre regiões.  Você precisará associar o novo IP aos recursos na região de destino.

- Para exportar uma configuração de IP e implantar um modelo para criar um IP em outra região, você precisará da função de colaborador de rede ou superior.

- Identifique o layout de rede de origem e todos os recursos que você está usando atualmente. Esse layout inclui, mas não se limita a, balanceadores de carga, NSGs (grupos de segurança de rede) e redes virtuais.

- Verifique se a assinatura do Azure permite criar IPs na região de destino usada. Contate o suporte para habilitar a cota necessária.

- Certifique-se de que sua assinatura tenha recursos suficientes para dar suporte à adição de IPs para esse processo.  Veja [Assinatura do Azure e limites, cotas e restrições de serviço](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-and-move"></a>Preparar e mover
As etapas a seguir mostram como preparar o IP público para a configuração mover usando um modelo do Resource Manager e mover a configuração de IP público para a região de destino usando o portal do Azure.

### <a name="export-the-template-and-deploy-from-a-script"></a>Exportar o modelo e implantar de um script

1. Faça logon no [portal do Azure](https://portal.azure.com)  >  **grupos de recursos**.
2. Localize o grupo de recursos que contém o IP público de origem e clique nele.
3. Selecione **configurações** de >  >  **modelo de exportação**.
4. Escolha **implantar** na folha **Exportar modelo** .
5. Clique em **modelo**  >  **Editar parâmetros** para abrir o **parameters.jsno** arquivo no editor online.
8. Para editar o parâmetro do nome do IP público, altere a propriedade em   >  **valor** de parâmetros do nome IP público de origem para o nome do seu IP público de destino, verifique se o nome está entre aspas:

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```
8.  Clique em **salvar** no editor.

9.  Clique em **modelo**  >  **Editar modelo** para abrir o **template.jsno** arquivo no editor online.

10. Para editar a região de destino em que o IP público será movido, altere a propriedade **local** em **recursos**:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]
    ```

11. Para obter códigos de localização de região, confira [locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/).  O código de uma região é o nome da região sem espaços, **EUA Central**  =  **centralus**.

12. Você também pode alterar outros parâmetros no modelo se quiser, e eles podem ser opcionais dependendo dos seus requisitos:

    * **SKU** -você pode alterar a SKU do IP público na configuração de Standard para básico ou básico para Standard alterando a propriedade nome do **SKU**  >   na **template.jsno** arquivo:

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
        ```

        Para obter mais informações sobre as diferenças entre IPS públicos do SKU básico e Standard, consulte [criar, alterar ou excluir um endereço IP público](./virtual-network-public-ip-address.md):

    * **Método de alocação de IP** e **tempo limite ocioso** – você pode alterar ambas as opções no modelo alterando a propriedade **publicIPAllocationMethod** de **Dinâmico** para **Estático** ou de **Estático** para **Dinâmico**. O tempo limite ocioso pode ser alterado alterando a propriedade **idleTimeoutInMinutes** para o valor desejado.  O padrão é **4**:

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []

        ```

        Para obter mais informações sobre os métodos de alocação e os valores de tempo limite ocioso, confira [Criar, alterar ou excluir um endereço IP](./virtual-network-public-ip-address.md).


13. Clique em **salvar** no editor online.

14. Clique em  >  **assinatura** básica para escolher a assinatura na qual o IP público de destino será implantado.

15. Clique em  >  **grupo de recursos** básico para escolher o grupo de recursos em que o IP público de destino será implantado.  Você pode clicar em **criar novo** para criar um novo grupo de recursos para o IP público de destino.  Verifique se o nome não é o mesmo que o grupo de recursos de origem do IP público de origem existente.

16. Verifique se o  >  **local** básico está definido como o local de destino onde você deseja que o IP público seja implantado.

17. Verifique em **configurações** que o nome corresponde ao nome que você inseriu no editor de parâmetros acima.

18. Marque a caixa em **termos e condições**.

19. Clique no botão **comprar** para implantar o IP público de destino.

## <a name="discard"></a>Descartar

Se você quiser descartar o IP público de destino, exclua o grupo de recursos que contém o IP público de destino.  Para fazer isso, selecione o grupo de recursos do seu painel no portal e selecione **excluir** na parte superior da página Visão geral.

## <a name="clean-up"></a>Limpeza

Para confirmar as alterações e concluir a movimentação do IP público, exclua o IP público de origem ou o grupo de recursos. Para fazer isso, selecione o IP público ou o grupo de recursos do seu painel no portal e selecione **excluir** na parte superior de cada página.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você moveu um IP do Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre como mover recursos entre regiões e recuperação de desastres no Azure, confira:


- [Mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Mover as VMs do Azure para outra região](../site-recovery/azure-to-azure-tutorial-migrate.md)