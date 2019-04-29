---
title: Modo de conectividade, energia e acesso de dispositivos de borda da caixa de dados do Microsoft Azure | Microsoft Docs
description: Descreve como gerenciar o acesso, a potência e o modo de conectividade para o dispositivo de borda de caixa de dados do Azure que ajuda a transferir dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 813563b500b9365289285a89536f2724fb87acad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60757257"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-edge"></a>Gerenciar o acesso, a potência e o modo de conectividade para a borda da caixa de dados do Azure

Este artigo descreve como gerenciar o modo de acesso, energia e conectividade para a borda da caixa de dados do Azure. Essas operações são executadas por meio da interface do usuário da web local ou o portal do Azure.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Gerenciar o acesso de dispositivo
> * Gerenciar o modo de conectividade
> * Gerenciar potência


## <a name="manage-device-access"></a>Gerenciar o acesso de dispositivo

O acesso ao seu dispositivo de borda da caixa de dados é controlado pelo uso de uma senha do dispositivo. Você pode alterar a senha por meio da interface do usuário da web local. Você também pode redefinir a senha do dispositivo no portal do Azure.

### <a name="change-device-password"></a>Alterar a senha de dispositivo

Siga estas etapas na interface do usuário local para alterar a senha do dispositivo.

1. Na interface do usuário de web local, vá para **manutenção > alteração de senha**.
2. Digite a senha atual e, em seguida, a nova senha. A senha fornecida deve ter entre 8 e 16 caracteres. A senha deve ter 3 dos seguintes caracteres: maiúscula, minúscula, numérica e caracteres especiais. Confirme a nova senha.

    ![Alterar senha](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. Selecione **Alterar senha**.
 
### <a name="reset-device-password"></a>Redefinir senha do dispositivo

O fluxo de trabalho de redefinição não exige que o usuário recupere a senha antiga e é útil quando a senha é perdida. Esse fluxo de trabalho é executado no portal do Azure.

1. No portal do Azure, acesse **visão geral > Redefinir senha de administrador**.

    ![Redefinir senha](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. Digite a nova senha e confirme-a. A senha fornecida deve ter entre 8 e 16 caracteres. A senha deve ter 3 dos seguintes caracteres: maiúscula, minúscula, numérica e caracteres especiais. Selecione **redefinir**.

    ![Redefinir senha](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-connectivity-mode"></a>Gerenciar o modo de conectividade

Além de modo padrão totalmente conectada, o dispositivo também pode executar no modo parcialmente conectado ou desconectado totalmente. Cada um desses modos é descrita como abaixo:

- **Totalmente conectada** -este é o modo padrão normal em que o dispositivo funciona. O upload de nuvem e o download de dados está habilitada neste modo. Você pode usar o portal do Azure ou da interface do usuário da web local para gerenciar o dispositivo.

- **Parcialmente desconectado** – nesse modo, o dispositivo não é possível carregar ou baixar qualquer compartilhamento no entanto os dados podem ser gerenciados por meio do portal do Azure.

    Esse modo é normalmente usado quando em uma rede de satélite medida e o objetivo é minimizar o consumo de largura de banda da rede. Consumo de rede mínima ainda poderão ocorrer para monitoramento de operações de dispositivo.

- **Disconnected** - Neste modo, o dispositivo é totalmente desconectado da nuvem e os uploads e downloads na nuvem são desativados. O dispositivo só pode ser gerenciado pela interface da web local.

    Esse modo é normalmente usado quando você deseja colocar o dispositivo offline.

Para alterar o modo de dispositivo, siga estas etapas:

1. Na interface do usuário da web local do seu dispositivo, acesse **Configuração> Configurações da nuvem**.
2. Na lista suspensa, selecione o modo que você deseja que operam no dispositivo. Você pode selecionar **totalmente conectada**, **parcialmente conectada**, e **totalmente desconectado**. Para executar o dispositivo no modo desconectado parcialmente, habilite **gerenciamento do portal do Azure**.

    ![Modo de conectividade](media/data-box-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>Gerenciar potência

Você pode desligar ou reiniciar seu dispositivo físico, usando o interface do usuário da web local. Nós recomendamos que antes de reiniciar, colocar os compartilhamentos offline no servidor de data e, em seguida, no dispositivo. Essa ação minimiza a possibilidade de corrupção de dados.

1. Na interface do usuário de web local, vá para **manutenção > configurações de energia**.
2. Selecione **desligamento** ou **reiniciar** dependendo do que você pretende fazer.

    ![Configurações de energia](media/data-box-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Quando solicitado a confirmar, selecione **Sim** para continuar.

> [!NOTE]
> Se você desligar o dispositivo físico, será preciso pressionar o botão de energia no dispositivo para ativá-lo.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [gerenciar compartilhamentos](data-box-edge-manage-shares.md).