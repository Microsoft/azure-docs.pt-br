---
title: Alterar a senha de administrador do dispositivo da StorSimple Virtual Array | Microsoft Docs
description: Descreve como usar o portal do Azure ou a interface do usuário da Web da Matriz Virtual StorSimple para alterar a senha do administrador do dispositivo.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 11490814-d9fd-4dc7-9c3b-55dd2c23eaf1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5308badf439254062a8aefca1840eb21bc234ace
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60580321"
---
# <a name="change-the-storsimple-virtual-array-device-administrator-password-via-storsimple-device-manager"></a>Alterar a senha de administrador do dispositivo StorSimple Virtual Array por meio do Gerenciador de Dispositivos do StorSimple

## <a name="overview"></a>Visão geral

Quando você usar a interface do Windows PowerShell para acessar o dispositivo da Matriz Virtual StorSimple, será solicitada a inserção de uma senha do administrador do dispositivo. Quando o dispositivo StorSimple é configurado e iniciado pela primeira vez, a senha padrão é *Password1*. Para a segurança dos seus dados, a senha padrão expira na primeira vez que você entra e é necessário alterá-la.

Você também pode usar a interface do usuário da Web local ou o portal do Azure para alterar a senha do administrador do dispositivo a qualquer momento depois que o dispositivo for implantado no seu ambiente de produção. Cada um desses procedimentos é descrito neste artigo.

 ![folha de dispositivos](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>Usar o portal do Azure para alterar a senha

Execute as etapas a seguir para alterar a senha do administrador do dispositivo por meio do portal do Azure.

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>Para alterar a senha do administrador do dispositivo por meio do portal do Azure

1. Na página de aterrissagem do serviço, selecione o seu serviço, clique duas vezes no nome do serviço e na seção **Gerenciamento**, clique em **Dispositivos**. Isso abre a folha **Dispositivos** que lista todos os dispositivos de Matriz Virtual StorSimple.

2. Na folha **Dispositivos**, clique duas vezes no dispositivo que requer uma alteração de senha.

3. Na folha **Configurações** do seu dispositivo, clique em **Segurança**.

4. Na folha **Configurações de segurança**, faça o seguinte:
   
   1. Role para baixo até a seção **Senha de Administrador do Dispositivo** . Forneça uma senha do administrador que contenha de 8 a 15 caracteres.
   2. Confirme a senha.
   3. Clique em **Salvar** na parte superior da folha.

A senha de administrador do dispositivo agora está atualizada. Você pode usar essa senha modificada para acessar o dispositivo localmente.

![Folha Configurações de segurança](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>Usar a interface do usuário da Web local para alterar a senha

Execute as etapas a seguir para alterar a senha do administrador do dispositivo por meio da interface do usuário da Web local.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Para alterar a senha do administrador do dispositivo por meio da interface do usuário da web local

1. Na interface do usuário da Web local, clique em **Manutenção** > **Alteração de senha** para seu dispositivo.
   
    ![alterar password1](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. Insira a **Senha atual**.
3. Forneça uma **Nova senha**. A senha deve ter pelo menos oito caracteres. Deve conter três dentre estas quatro opções: caracteres maiúsculos, minúsculos, numéricos e especiais.
   
    Observe que sua senha não pode ser igual às 24 últimas senhas.
4. Insira a senha novamente para confirmá-la.
   
    ![alterar password2](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. Na parte inferior da página, clique em **Aplicar**. A nova senha foi aplicada. Se a alteração da senha não for concluída com êxito, você verá o erro a seguir:
   
    ![erro de senha](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    Depois que a senha for atualizada com êxito, você será notificado. Pode-se usar essa senha modificada para acessar o dispositivo localmente.


## <a name="next-steps"></a>Próximas etapas
Aprenda como [administrar sua StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

