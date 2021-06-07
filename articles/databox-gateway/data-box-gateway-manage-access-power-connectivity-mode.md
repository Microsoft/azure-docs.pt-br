---
title: Gateway do Azure Data Box o acesso ao dispositivo, a energia e o modo de conectividade
description: Descreve como gerenciar o acesso, a potência e o modo de conectividade para o dispositivo de Azure Data Box Gateway que ajuda a transferir dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: c4e2894d193309c169adbea96491e0754d479a8a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98786799"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>Gerenciar o acesso, a energia e o modo de conectividade para seu Gateway do Azure Data Box

Este artigo descreve como gerenciar o modo de acesso, energia e conectividade para o Azure Data Box Gateway. Essas operações são executadas por meio da interface do usuário da web local ou o portal do Azure. 

Neste artigo, você aprenderá como:

> [!div class="checklist"]
>
> * Gerenciar o acesso de dispositivo
> * Gerenciar o modo de conectividade
> * Gerenciar potência

## <a name="manage-device-access"></a>Gerenciar o acesso de dispositivo

O acesso ao seu dispositivo de Gateway do Data Box é controlado pelo uso de uma senha de dispositivo. Você pode alterar a senha por meio da interface do usuário da Web local. Você também pode redefinir a senha do dispositivo no portal do Azure.

### <a name="change-device-password"></a>Alterar a senha de dispositivo

Siga estas etapas na interface do usuário local para alterar a senha do dispositivo.

1. Na interface do usuário de web local, vá para **manutenção > alteração de senha**.
2. Digite a senha atual e, em seguida, a nova senha. A senha fornecida deve ter entre 8 e 16 caracteres. A senha deve ter 3 dos seguintes caracteres: maiúscula, minúscula, numérica e caracteres especiais. Confirme a nova senha.

    ![Alterar senha](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Clique em **Alterar senha**.
 
### <a name="reset-device-password"></a>Redefinir senha do dispositivo

O fluxo de trabalho de redefinição não exige que o usuário recupere a senha antiga e é útil quando a senha é perdida. Esse fluxo de trabalho é executado no portal do Azure.

1. No portal do Azure, acesse **visão geral > Redefinir senha de administrador**.

    ![Redefinir senha](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Digite a nova senha e confirme-a. A senha fornecida deve ter entre 8 e 16 caracteres. A senha deve ter 3 dos seguintes caracteres: maiúscula, minúscula, numérica e caracteres especiais. Clique em **redefinir**.

    ![Redefinir senha 2](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Gerenciar o acesso de recursos

Para criar seu Gateway do Azure Data Box, o Hub IoT e o recurso de armazenamento do Azure, você precisa de permissões como um colaborador ou superior em um nível de grupo de recursos. Você também precisa que os provedores de recursos correspondentes sejam registrados. Para quaisquer operações que envolvam credenciais e chave de ativação, as permissões para Azure Active Directory API do Graph também são necessárias. Elas são descritas nas seções a seguir.

### <a name="manage-microsoft-graph-api-permissions"></a>Gerenciar permissões de API de Microsoft Graph

Ao gerar a chave de ativação para o dispositivo ou executar qualquer operação que exija credenciais, você precisa de permissões para Microsoft Graph API. As operações que precisam de credenciais podem ser:

-  Criando um compartilhamento com uma conta de armazenamento associada.
-  Criar um usuário que pode acessar os compartilhamentos no dispositivo.

Você deve ter `User` acesso ao locatário do Active Directory para que possa fazê-lo `Read all directory objects` . Um usuário convidado não tem permissões para `Read all directory objects` . Se você for um convidado, operações como a geração de uma chave de ativação, a criação de um compartilhamento em seu dispositivo e a criação de um usuário falharão.

Para obter mais informações sobre como fornecer acesso aos usuários para Microsoft Graph API, consulte [referência de permissões de Microsoft Graph](/graph/permissions-reference).

### <a name="register-resource-providers"></a>Registrar provedores de recursos

Para provisionar um recurso no Azure (no modelo de Azure Resource Manager), você precisa de um provedor de recursos que ofereça suporte à criação desse recurso. Por exemplo, para provisionar uma máquina virtual, você deve ter um provedor de recursos ' Microsoft. Compute ' disponível na assinatura.
 
Provedores de recursos são registrados no nível da assinatura. Por padrão, qualquer nova assinatura do Azure é previamente registrada com uma lista de provedores de recursos comumente usados. O provedor de recursos para ' Microsoft. DataBoxEdge ' não está incluído nesta lista.

Você não precisa conceder permissões de acesso no nível da assinatura para que os usuários possam criar recursos como ' Microsoft. DataBoxEdge ' nos grupos de recursos aos quais eles têm direitos de proprietário, desde que os provedores de recursos desses recursos já estejam registrados.

Antes de tentar criar qualquer recurso, verifique se o provedor de recursos está registrado na assinatura. Se o provedor de recursos não estiver registrado, você precisará certificar-se de que o usuário que está criando o novo recurso tenha direitos suficientes para registrar o provedor de recursos necessário no nível da assinatura. Se você ainda não fez isso, verá o seguinte erro:

*A assinatura \<Subscription name> não tem permissões para registrar o (s) provedor (es) de recursos: Microsoft. DataBoxEdge.*


Para obter uma lista de provedores de recursos registrados na assinatura atual, execute o seguinte comando:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Para um dispositivo Gateway do Data Box, `Microsoft.DataBoxEdge` deve ser registrado. Para se registrar `Microsoft.DataBoxEdge` , o administrador da assinatura deve executar o seguinte comando:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Para obter mais informações sobre como registrar um provedor de recursos, consulte [resolver erros de registro do provedor de recursos](../azure-resource-manager/templates/error-register-resource-provider.md).

## <a name="manage-connectivity-mode"></a>Gerenciar o modo de conectividade

Além do modo normal padrão, o dispositivo também pode ser executado no modo parcialmente desconectado ou desconectado:

- **Parcialmente desconectado** – nesse modo, o dispositivo não pode carregar nenhum dado para os compartilhamentos. No entanto, ele pode ser gerenciado por meio do portal do Azure.

    Esse modo é normalmente usado para minimizar o consumo de largura de banda de rede em uma rede satélite limitada. Consumo de rede mínima ainda poderão ocorrer para monitoramento de operações de dispositivo.

- **Desconectado** – nesse modo, o dispositivo é totalmente desconectado da nuvem, e os downloads e carregamentos de nuvem são desabilitados. O dispositivo só pode ser gerenciado pela interface da web local.

    Esse modo é normalmente usado quando você deseja colocar o dispositivo offline.

Para alterar o modo do dispositivo, siga estas etapas:

1. Na interface do usuário da web local do seu dispositivo, acesse **Configuração> Configurações da nuvem**.
2. Desabilitar a **upload e download de nuvem**.
3. Para executar o dispositivo no modo desconectado parcialmente, habilite **gerenciamento do portal do Azure**.

    ![Modo de conectividade](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Para executar o dispositivo no modo desconectado, desabilite **gerenciamento do portal do Azure**. Agora, o dispositivo só pode ser gerenciado por meio da interface do usuário da web local.

    ![Modo de conectividade 2](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>Gerenciar potência

Você pode desligar ou reiniciar seu dispositivo virtual usando a interface do usuário da Web local. É recomendável que, antes de reiniciar o dispositivo, você coloque os compartilhamentos offline no host e, em seguida, no dispositivo. Essa ação minimiza a possibilidade de corrupção de dados.

1. Na interface do usuário de web local, vá para **manutenção > configurações de energia**.
2. Clique em **desligar** ou **reiniciar** , dependendo do que você pretende fazer.

    ![Configurações da energia](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Quando solicitado a confirmar, clique em **Sim** para continuar.

> [!NOTE]
> Se você desligar o dispositivo virtual, você precisará iniciar o dispositivo por meio do gerenciamento de hipervisor.