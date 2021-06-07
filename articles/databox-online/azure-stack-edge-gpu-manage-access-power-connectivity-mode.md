---
title: Azure Stack o acesso ao dispositivo do Edge pro GPU, a energia e o modo de conectividade | Microsoft Docs
description: Descreve como gerenciar o acesso, a energia e o modo de conectividade para o dispositivo Azure Stack Edge pro GPU que ajuda a transferir dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 39990a557315c3fcc79f2b9dab59f25f758ab2bd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443107"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-stack-edge-pro-gpu"></a>Gerenciar o acesso, a energia e o modo de conectividade para sua GPU pro do Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve como gerenciar o modo de acesso, energia e conectividade para seu Azure Stack Edge pro com o dispositivo de GPU. Essas operações são executadas por meio da interface do usuário da web local ou o portal do Azure.

Este artigo aplica-se a Azure Stack Edge pro GPU, Azure Stack borda pro R e Azure Stack dispositivos mini R do Edge.


Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Gerenciar o acesso de dispositivo
> * Gerenciar o acesso de recursos
> * Gerenciar o modo de conectividade
> * Gerenciar potência


## <a name="manage-device-access"></a>Gerenciar o acesso de dispositivo

O acesso ao seu dispositivo Azure Stack Edge pro é controlado pelo uso de uma senha de dispositivo. Você pode alterar a senha por meio da interface do usuário da Web local. Você também pode redefinir a senha do dispositivo no portal do Azure.

O acesso aos dados nos discos do dispositivo também é controlado por chaves de criptografia em repouso.

### <a name="change-device-password"></a>Alterar a senha de dispositivo

Siga estas etapas na interface do usuário local para alterar a senha do dispositivo.

1. Na interface do usuário da Web local, vá para **manutenção > senha**.
2. Digite a senha atual e, em seguida, a nova senha. A senha fornecida deve ter entre 8 e 16 caracteres. A senha deve ter 3 dos seguintes caracteres: maiúscula, minúscula, numérica e caracteres especiais. Confirme a nova senha.

    ![Alterar senha](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/change-password-1.png)

3. Selecione **Alterar senha**.
 
### <a name="reset-device-password"></a>Redefinir senha do dispositivo

O fluxo de trabalho de redefinição não exige que o usuário recupere a senha antiga e é útil quando a senha é perdida. Esse fluxo de trabalho é executado no portal do Azure.

1. No portal do Azure, acesse **visão geral > Redefinir senha de administrador**.

    ![Captura de tela mostra o dispositivo com a senha de redefinição do dispositivo selecionada.](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/reset-password-1.png)


2. Digite a nova senha e confirme-a. A senha fornecida deve ter entre 8 e 16 caracteres. A senha deve ter 3 dos seguintes caracteres: maiúscula, minúscula, numérica e caracteres especiais. Selecione **Restaurar**.

    ![Redefinir senha 2](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-2.png)

### <a name="manage-access-to-device-data"></a>Gerenciar o acesso aos dados do dispositivo

Para os dispositivos do Azure Stack Edge pro R e Azure Stack mini-R Edge, o acesso aos dados do dispositivo é controlado usando chaves de criptografia em repouso para as unidades de dispositivo. Depois de configurar com êxito o dispositivo para criptografia em repouso, a opção girar chaves de criptografia em repouso fica disponível na interface do usuário local do dispositivo. 

Essa operação permite alterar as chaves para volumes do BitLocker `HcsData` e `HcsInternal` todas as unidades de criptografia automática em seu dispositivo.

Siga estas etapas para girar as chaves de criptografia em repouso.

1. Na interface do usuário local do dispositivo, vá para a página de **introdução** . No bloco **segurança** , selecione **criptografia em repouso: opção teclas de rotação** . Essa opção só estará disponível depois que você tiver configurado com êxito as chaves de criptografia em repouso.

    ![Selecionar teclas de rotação para criptografia em repouso na página de introdução](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-1.png)

1. Você pode usar suas próprias chaves do BitLocker ou usar as chaves geradas pelo sistema.  

    Para fornecer sua própria chave, insira uma cadeia de caracteres com codificação de base 64 de 32 caracteres. A entrada é semelhante ao que você forneceria ao configurar a criptografia em repouso pela primeira vez.

    ![Traga sua própria chave de criptografia em repouso](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-2.png)

    Você também pode optar por usar uma chave gerada pelo sistema.

    ![Usar a chave de criptografia em repouso gerada pelo sistema](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-3.png)

1. Escolha **Aplicar**. Os protetores de chave são girados.

    ![Aplicar a nova chave de criptografia em repouso](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-4.png)

1. Quando for solicitado a baixar e salvar o arquivo de chave, selecione **baixar e continuar**. 

    ![Baixar e continuar o arquivo de chave](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-5.png)

    Salve o `.json` arquivo de chave em um local seguro. Esse arquivo é usado para facilitar uma possível recuperação futura do dispositivo.

    ![Captura de tela mostra a caixa de diálogo Redefinir senha do dispositivo.](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-2.png)

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

- **Parcialmente desconectado** – nesse modo, o dispositivo não pode carregar nem baixar dados de compartilhamento, no entanto, pode ser gerenciado por meio do portal do Azure.

    Esse modo é normalmente usado quando em uma rede de satélite medida e o objetivo é minimizar o consumo de largura de banda da rede. Consumo de rede mínima ainda poderão ocorrer para monitoramento de operações de dispositivo.

- **Disconnected** - Neste modo, o dispositivo é totalmente desconectado da nuvem e os uploads e downloads na nuvem são desativados. O dispositivo só pode ser gerenciado pela interface da web local.

    Esse modo é normalmente usado quando você deseja colocar o dispositivo offline.

Para alterar o modo de dispositivo, siga estas etapas:

1. Na interface do usuário da Web local do seu dispositivo, vá para **configuração > nuvem**.
2. Na lista suspensa, selecione o modo no qual você deseja operar o dispositivo. Você pode selecionar entre **totalmente** conectado, **parcialmente conectado** e **totalmente desconectado**. Para executar o dispositivo no modo desconectado parcialmente, habilite **gerenciamento do portal do Azure**.

 
## <a name="manage-power"></a>Gerenciar potência

Você pode desligar ou reiniciar o dispositivo físico usando a IU da Web local. Nós recomendamos que antes de reiniciar, você coloque os compartilhamentos offline no servidor de dados e, em seguida, no dispositivo. Essa ação minimiza a possibilidade de corrupção de dados.

1. Na interface do usuário da Web local, vá para **manutenção > energia**.
2. Selecione **desligar** ou **reiniciar** , dependendo do que você pretende fazer.

    ![Configurações da energia](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Quando a confirmação for solicitada, selecione **Sim** para continuar.

> [!NOTE]
> Se você desligar o dispositivo físico, será necessário pressionar o botão de energia no dispositivo para ativá-lo.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [gerenciar compartilhamentos](azure-stack-edge-manage-shares.md).