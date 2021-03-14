---
title: Acesso ao dispositivo pro Edge Azure Stack, energia e modo de conectividade | Microsoft Docs
description: Descreve como gerenciar o acesso, a energia e o modo de conectividade para o dispositivo Azure Stack Edge pro que ajuda a transferir dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 240d3872536e6974d7f65eed22dace6816844e9e
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200198"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-stack-edge-pro"></a>Gerencie o modo de acesso, energia e conectividade para seu Azure Stack Edge pro

Este artigo descreve como gerenciar o modo de acesso, energia e conectividade para seu Azure Stack Edge pro. Essas operações são executadas por meio da interface do usuário da web local ou o portal do Azure.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Gerenciar o acesso de dispositivo
> * Gerenciar o modo de conectividade
> * Gerenciar potência


## <a name="manage-device-access"></a>Gerenciar o acesso de dispositivo

O acesso ao seu dispositivo Azure Stack Edge pro é controlado pelo uso de uma senha de dispositivo. Você pode alterar a senha por meio da interface do usuário da Web local. Você também pode redefinir a senha do dispositivo no portal do Azure.

### <a name="change-device-password"></a>Alterar a senha de dispositivo

Siga estas etapas na interface do usuário local para alterar a senha do dispositivo.

1. Na interface do usuário de web local, vá para **manutenção > alteração de senha**.
2. Digite a senha atual e, em seguida, a nova senha. A senha fornecida deve ter entre 8 e 16 caracteres. A senha deve ter 3 dos seguintes caracteres: maiúscula, minúscula, numérica e caracteres especiais. Confirme a nova senha.

    ![Alterar senha](media/azure-stack-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. Selecione **Alterar senha**.
 
### <a name="reset-device-password"></a>Redefinir senha do dispositivo

O fluxo de trabalho de redefinição não exige que o usuário recupere a senha antiga e é útil quando a senha é perdida. Esse fluxo de trabalho é executado no portal do Azure.

1. No portal do Azure, acesse **visão geral > Redefinir senha de administrador**.

    ![Redefinir senha](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. Digite a nova senha e confirme-a. A senha fornecida deve ter entre 8 e 16 caracteres. A senha deve ter 3 dos seguintes caracteres: maiúscula, minúscula, numérica e caracteres especiais. Selecione **Restaurar**.

    ![Redefinir senha 2](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Gerenciar o acesso de recursos

Para criar seu Azure Stack Edge/Gateway do Data Box, o Hub IoT e o recurso de armazenamento do Azure, você precisa de permissões como um colaborador ou superior em um nível de grupo de recursos. Você também precisa que os provedores de recursos correspondentes sejam registrados. Para qualquer operação que envolva credenciais e chave de ativação, as permissões para a API de Microsoft Graph também são necessárias. Elas são descritas nas seções a seguir. 

### <a name="manage-microsoft-graph-api-permissions"></a>Gerenciar permissões de API de Microsoft Graph

Ao gerar a chave de ativação para o Azure Stack dispositivo pro Edge ou executar qualquer operação que exija credenciais, você precisará de permissões para Azure Active Directory API do Graph. As operações que precisam de credenciais podem ser:

-  Criando um compartilhamento com uma conta de armazenamento associada.
-  Criar um usuário que pode acessar os compartilhamentos no dispositivo.

Você deve ter `User` acesso ao Active Directory locatário, pois precisa ser capaz de fazer isso `Read all directory objects` . Você não pode ser um usuário convidado, pois não tem permissões para `Read all directory objects` . Se você for um convidado, as operações, como a geração de uma chave de ativação, a criação de um compartilhamento no dispositivo Azure Stack Edge pro, a criação de um usuário, a configuração da função de computação de borda, falharão ao redefinir a senha do dispositivo.

Para obter mais informações sobre como fornecer acesso aos usuários para Microsoft Graph API, consulte [referência de permissões de Microsoft Graph](/graph/permissions-reference).

### <a name="register-resource-providers"></a>Registrar provedores de recursos

Para provisionar um recurso no Azure (no modelo de Azure Resource Manager), você precisa de um provedor de recursos que ofereça suporte à criação desse recurso. Por exemplo, para provisionar uma máquina virtual, você deve ter um provedor de recursos ' Microsoft. Compute ' disponível na assinatura.
 
Provedores de recursos são registrados no nível da assinatura. Por padrão, qualquer nova assinatura do Azure é previamente registrada com uma lista de provedores de recursos comumente usados. O provedor de recursos para ' Microsoft. DataBoxEdge ' não está incluído nesta lista.

Você não precisa conceder permissões de acesso ao nível de assinatura para que os usuários possam criar recursos como ' Microsoft. DataBoxEdge ' dentro de seus grupos de recursos aos quais eles têm direitos de proprietário, desde que os provedores de recursos desses recursos já estejam registrados.

Antes de tentar criar qualquer recurso, verifique se o provedor de recursos está registrado na assinatura. Se o provedor de recursos não estiver registrado, você precisará certificar-se de que o usuário que está criando o novo recurso tenha direitos suficientes para registrar o provedor de recursos necessário no nível de assinatura. Se você ainda não fez isso, verá o seguinte erro:

*A assinatura \<Subscription name> não tem permissões para registrar o (s) provedor (es) de recursos: Microsoft. DataBoxEdge.*


Para obter uma lista de provedores de recursos registrados na assinatura atual, execute o seguinte comando:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Para Azure Stack dispositivo pro Edge, `Microsoft.DataBoxEdge` deve ser registrado. Para se registrar `Microsoft.DataBoxEdge` , o administrador de assinatura deve executar o seguinte comando:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Para obter mais informações sobre como registrar um provedor de recursos, consulte [resolver erros de registro do provedor de recursos](../azure-resource-manager/templates/error-register-resource-provider.md).

## <a name="manage-connectivity-mode"></a>Gerenciar o modo de conectividade

Além do modo totalmente conectado padrão, o dispositivo também pode ser executado em modo parcialmente conectado ou totalmente desconectado. Cada um desses modos é descrita como abaixo:

- **Totalmente conectado** – esse é o modo padrão normal no qual o dispositivo opera. O carregamento de nuvem e o download de dados estão habilitados nesse modo. Você pode usar o portal do Azure ou a interface do usuário da Web local para gerenciar o dispositivo.

- **Parcialmente conectado** – nesse modo, o dispositivo não pode carregar nem baixar dados de compartilhamento, no entanto, pode ser gerenciado por meio do portal do Azure.

    Esse modo é normalmente usado quando em uma rede de satélite medida e o objetivo é minimizar o consumo de largura de banda da rede. Consumo de rede mínima ainda poderão ocorrer para monitoramento de operações de dispositivo.

- **Disconnected** - Neste modo, o dispositivo é totalmente desconectado da nuvem e os uploads e downloads na nuvem são desativados. O dispositivo só pode ser gerenciado pela interface da web local.

    Esse modo é normalmente usado quando você deseja colocar o dispositivo offline.

Para alterar o modo de dispositivo, siga estas etapas:

1. Na interface do usuário da web local do seu dispositivo, acesse **Configuração> Configurações da nuvem**.
2. Na lista suspensa, selecione o modo no qual você deseja operar o dispositivo. Você pode selecionar entre **totalmente** conectado, **parcialmente conectado** e **totalmente desconectado**. Para executar o dispositivo no modo desconectado parcialmente, habilite **gerenciamento do portal do Azure**.

    ![Modo de conectividade](media/azure-stack-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>Gerenciar potência

Você pode desligar ou reiniciar o dispositivo físico usando a IU da Web local. Nós recomendamos que antes de reiniciar, você coloque os compartilhamentos offline no servidor de dados e, em seguida, no dispositivo. Essa ação minimiza a possibilidade de corrupção de dados.

1. Na interface do usuário de web local, vá para **manutenção > configurações de energia**.
2. Selecione **desligar** ou **reiniciar** , dependendo do que você pretende fazer.

    ![Configurações da energia](media/azure-stack-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Quando a confirmação for solicitada, selecione **Sim** para continuar.

> [!NOTE]
> Se você desligar o dispositivo físico, será necessário pressionar o botão de energia no dispositivo para ativá-lo.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [gerenciar compartilhamentos](azure-stack-edge-manage-shares.md).
