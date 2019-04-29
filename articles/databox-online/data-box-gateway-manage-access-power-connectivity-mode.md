---
title: O acesso ao dispositivo Microsoft Azure Data Box Gateway, energia e acesso ao modo conectividade | Microsoft Docs
description: Descreve como gerenciar o acesso, a potência e o modo de conectividade para o dispositivo de Azure Data Box Gateway que ajuda a transferir dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 72d3455f37d0ccef0dd5b7d8882f70670de07572
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60755399"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>Gerenciar o acesso, a potência e o modo de conectividade para o Gateway de caixa de dados do Azure

Este artigo descreve como gerenciar o modo de acesso, energia e conectividade para o Azure Data Box Gateway. Essas operações são executadas por meio da interface do usuário da web local ou o portal do Azure.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Gerenciar o acesso de dispositivo
> * Gerenciar o modo de conectividade
> * Gerenciar potência

## <a name="manage-device-access"></a>Gerenciar o acesso de dispositivo

O acesso ao seu dispositivo de Gateway da caixa de dados é controlado pelo uso de uma senha do dispositivo. Você pode alterar a senha por meio da interface do usuário da web local. Você também pode redefinir a senha do dispositivo no portal do Azure.

### <a name="change-device-password"></a>Alterar a senha de dispositivo

Siga estas etapas na interface do usuário local para alterar a senha do dispositivo.

1. Na interface do usuário de web local, vá para **manutenção > alteração de senha**.
2. Digite a senha atual e, em seguida, a nova senha. A senha fornecida deve ter entre 8 e 16 caracteres. A senha deve ter 3 dos seguintes caracteres: maiúscula, minúscula, numérica e caracteres especiais. Confirme a nova senha.

    ![Alterar senha](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Clique em **alterar a senha**.
 
### <a name="reset-device-password"></a>Redefinir senha do dispositivo

O fluxo de trabalho de redefinição não exige que o usuário recupere a senha antiga e é útil quando a senha é perdida. Esse fluxo de trabalho é executado no portal do Azure.

1. No portal do Azure, acesse **visão geral > Redefinir senha de administrador**.

    ![Redefinir senha](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Digite a nova senha e confirme-a. A senha fornecida deve ter entre 8 e 16 caracteres. A senha deve ter 3 dos seguintes caracteres: maiúscula, minúscula, numérica e caracteres especiais. Clique em **redefinir**.

    ![Redefinir senha](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-connectivity-mode"></a>Gerenciar o modo de conectividade

Além do modo normal padrão, o dispositivo também pode ser executado no modo parcialmente desconectado ou desconectado. Cada um desses modos é descrita como abaixo:

- **Parcialmente desconectado** - Nesse modo, o dispositivo não pode carregar dados para os compartilhamentos, mas pode ser gerenciado pelo portal do Azure.

    Esse modo é normalmente usado quando em uma rede de satélite medida e o objetivo é minimizar o consumo de largura de banda da rede. Consumo de rede mínima ainda poderão ocorrer para monitoramento de operações de dispositivo.

- **Disconnected** - Neste modo, o dispositivo é totalmente desconectado da nuvem e os uploads e downloads na nuvem são desativados. O dispositivo só pode ser gerenciado pela interface da web local.

    Esse modo é normalmente usado quando você deseja colocar o dispositivo offline.

Para alterar o modo de dispositivo, siga estas etapas:

1. Na interface do usuário da web local do seu dispositivo, acesse **Configuração> Configurações da nuvem**.
2. Desabilitar a **upload e download de nuvem**.
3. Para executar o dispositivo no modo desconectado parcialmente, habilite **gerenciamento do portal do Azure**.

    ![Modo de conectividade](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Para executar o dispositivo no modo desconectado, desabilite **gerenciamento do portal do Azure**. Agora, o dispositivo só pode ser gerenciado por meio da interface do usuário da web local.

    ![Modo de conectividade](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>Gerenciar potência

Você pode desligar ou reiniciar seu dispositivo físico e virtual usando a interface da Web local. Nós recomendamos que antes de reiniciar, você coloque os compartilhamentos offline no host e, em seguida, no dispositivo. Essa ação minimiza a possibilidade de corrupção de dados.

1. Na interface do usuário de web local, vá para **manutenção > configurações de energia**.
2. Clique em **desligamento** ou **reiniciar** dependendo do que você pretende fazer.

    ![Configurações de energia](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Quando solicitado a confirmar, clique em **Sim** para continuar.

> [!NOTE]
> Se você desligar o dispositivo virtual, você precisará iniciar o dispositivo por meio do gerenciamento de hipervisor.
