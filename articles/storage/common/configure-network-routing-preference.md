---
title: Configurar preferência de roteamento de rede
titleSuffix: Azure Storage
description: Configure a preferência de roteamento de rede para sua conta de armazenamento do Azure para especificar como o tráfego de rede é roteado para sua conta de clientes pela Internet.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/17/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 0738f7e427c2ff094c9b6df7539ba67dff80d095
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589847"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>Configurar a preferência de roteamento de rede para o armazenamento do Azure

Este artigo descreve como você pode configurar a preferência de roteamento de rede e os pontos de extremidade específicos de rota para sua conta de armazenamento. 

A preferência de roteamento de rede especifica como o tráfego de rede é roteado para sua conta de clientes pela Internet. Os pontos de extremidade específicos de rota são novos pontos de extremidade que o armazenamento do Azure cria para sua conta de armazenamento. Esses pontos de extremidade roteiam o tráfego sobre um caminho desejado sem alterar a preferência de roteamento padrão. Para saber mais, consulte [preferência de roteamento de rede para o armazenamento do Azure](network-routing-preference.md).

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>Configurar a preferência de roteamento para o ponto de extremidade público padrão

Por padrão, a preferência de roteamento para o ponto de extremidade público da conta de armazenamento é definida como Microsoft Global Network. Você pode escolher entre a rede global da Microsoft e o roteamento da Internet como a preferência de roteamento padrão para o ponto de extremidade público da sua conta de armazenamento. Para saber mais sobre a diferença entre esses dois tipos de roteamento, consulte [preferência de roteamento de rede para o armazenamento do Azure](network-routing-preference.md). 

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para alterar sua preferência de roteamento para roteamento da Internet:

1. Entre no [portal do Azure](https://portal.azure.com).

2. Navegue até sua conta de armazenamento no Portal.

3. Em **configurações**, escolha **rede**.

    > [!div class="mx-imgBorder"]
    > ![Opção de menu rede](./media/configure-network-routing-preference/networking-option.png)

4.  Na guia **firewalls e redes virtuais** , em **Roteamento de rede**, altere a configuração de **preferência de roteamento** para roteamento da **Internet**.

5.  Clique em **Save** (Salvar).

    > [!div class="mx-imgBorder"]
    > ![opção de roteamento da Internet](./media/configure-network-routing-preference/internet-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Entre em sua assinatura do Azure com o comando `Connect-AzAccount` e siga as instruções na tela para fazer a autenticação.

   ```powershell
   Connect-AzAccount
   ```

2. Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa para a assinatura da conta de armazenamento que hospedará seu site estático.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Substitua o valor de espaço reservado `<subscription-id>` pela ID da sua assinatura.

3. Para alterar a preferência de roteamento para roteamento da Internet, use o comando [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) e defina o `--routing-choice` parâmetro como `InternetRouting` .

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -RoutingChoice InternetRouting
   ```

   Substitua o `<resource-group-name>` valor do espaço reservado pelo nome do grupo de recursos que contém a conta de armazenamento.

   Substitua o `<storage-account-name>` valor do espaço reservado pelo nome da conta de armazenamento.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Entre na sua assinatura do Azure.

   - Para iniciar o Azure Cloud Shell, entre no [portal do Azure](https://portal.azure.com).

   - Para fazer logon em sua instalação local da CLI, execute o comando [AZ login](/cli/azure/reference-index#az-login) :

     ```azurecli
     az login
     ```
2. Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa para a assinatura da conta de armazenamento que hospedará seu site estático.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Substitua o valor de espaço reservado `<subscription-id>` pela ID da sua assinatura.

3. Para alterar sua preferência de roteamento para roteamento da Internet, use o comando [AZ Storage Account Update](/cli/azure/storage/account#az_storage_account_update) e defina o `--routing-choice` parâmetro como `InternetRouting` .

   ```azurecli
   az storage account update --name <storage-account-name> --routing-choice InternetRouting
   ```

   Substitua o valor de espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

---

## <a name="configure-a-route-specific-endpoint"></a>Configurar um ponto de extremidade específico de rota

Você também pode configurar um ponto de extremidade específico de rota. Por exemplo, você pode definir a preferência de roteamento para o ponto de extremidade padrão para *Roteamento da Internet* e, em seguida, publicar um ponto de extremidade específico de rota que permite o tráfego entre clientes na Internet e sua conta de armazenamento para ser roteado por meio da rede global da Microsoft.

Essa preferência afeta apenas o ponto de extremidade específico da rota. Essa preferência não afeta a preferência de roteamento padrão.  

### <a name="portal"></a>[Portal](#tab/azure-portal)

1.  Navegue até sua conta de armazenamento no Portal.

2.  Em **configurações**, escolha **rede**.

3.  Na guia **firewalls e redes virtuais** , em **publicar pontos de extremidade específicos da rota**, escolha a preferência de roteamento do ponto de extremidades específico de rota e clique em **salvar**.

    A imagem a seguir mostra a opção de **Roteamento de rede da Microsoft** selecionada.

    > [!div class="mx-imgBorder"]
    > ![Opção de roteamento de rede da Microsoft](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Para configurar um ponto de extremidade específico de rota, use o comando [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) . 

   - Para criar um ponto de extremidade específico de rota que usa a preferência de roteamento de rede da Microsoft, defina o `-PublishMicrosoftEndpoint` parâmetro como `true` . 

   - Para criar um ponto de extremidade específico de rota que usa a preferência de roteamento de Internet, defina o `-PublishInternetEndpointTo` parâmetro como `true` .  

   O exemplo a seguir cria um ponto de extremidade específico de rota que usa a preferência de roteamento de rede da Microsoft.

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -PublishMicrosoftEndpoint $true
   ```

   Substitua o `<resource-group-name>` valor do espaço reservado pelo nome do grupo de recursos que contém a conta de armazenamento.

   Substitua o `<storage-account-name>` valor do espaço reservado pelo nome da conta de armazenamento.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Para configurar um ponto de extremidade específico de rota, use o comando [AZ Storage Account Update](/azure/storage/account#az-storage-account-update) . 

   - Para criar um ponto de extremidade específico de rota que usa a preferência de roteamento de rede da Microsoft, defina o `--publish-microsoft-endpoints` parâmetro como `true` . 

   - Para criar um ponto de extremidade específico de rota que usa a preferência de roteamento de Internet, defina o `--publish-internet-endpoints` parâmetro como `true` .  

   O exemplo a seguir cria um ponto de extremidade específico de rota que usa a preferência de roteamento de rede da Microsoft.

   ```azurecli
   az storage account update --name <storage-account-name> --publish-microsoft-endpoints true
   ```

   Substitua o `<storage-account-name>` valor do espaço reservado pelo nome da conta de armazenamento.

---

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>Localizar o nome do ponto de extremidade para um ponto de extremidade específico de rota

Se você configurou um ponto de extremidade específico de rota, poderá encontrar o ponto de extremidade nas propriedades de sua conta de armazenamento.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1.  Em **configurações**, escolha **Propriedades**.

    > [!div class="mx-imgBorder"]
    > ![opção de menu Propriedades](./media/configure-network-routing-preference/properties.png)

2.  O ponto de extremidade de **Roteamento de rede da Microsoft** é mostrado para cada serviço que dá suporte às preferências de roteamento. Essa imagem mostra o ponto de extremidade para os serviços BLOB e arquivo.

    > [!div class="mx-imgBorder"]
    > ![Opção de roteamento de rede da Microsoft para pontos de extremidade específicos de rota](./media/configure-network-routing-preference/routing-url.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Para imprimir os pontos de extremidade no console, use a `PrimaryEndpoints` Propriedade do objeto de conta de armazenamento.

   ```powershell
   Get-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name>
   write-Output $StorageAccount.PrimaryEndpoints
   ```

   Substitua o `<resource-group-name>` valor do espaço reservado pelo nome do grupo de recursos que contém a conta de armazenamento.

   Substitua o `<storage-account-name>` valor do espaço reservado pelo nome da conta de armazenamento.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Para imprimir os pontos de extremidade no console, use a propriedade [AZ Storage Account show](/cli/azure/storage/account#az_storage_account_show) do objeto de conta de armazenamento.

   ```azurecli
   az storage account show -g <resource-group-name> -n <storage-account-name>
   ```

   Substitua o `<resource-group-name>` valor do espaço reservado pelo nome do grupo de recursos que contém a conta de armazenamento.

   Substitua o `<storage-account-name>` valor do espaço reservado pelo nome da conta de armazenamento.

---

## <a name="see-also"></a>Confira também

- [Preferência de roteamento de rede](network-routing-preference.md)
- [Configurar redes virtuais e firewalls do Armazenamento do Microsoft Azure](storage-network-security.md)
- [Recomendações de segurança para o armazenamento de blobs](../blobs/security-recommendations.md)
