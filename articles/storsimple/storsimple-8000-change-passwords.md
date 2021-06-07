---
title: Alterar suas senhas do StorSimple | Microsoft Docs
description: Descreve como usar o serviço Gerenciador de Dispositivos do StorSimple para alterar suas senhas de administrador do dispositivo e do StorSimple Snapshot Manager.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 038084ba9ae43e14bc2eb42bf258912be27d062c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023747"
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>Usar o serviço do Gerenciador de Dispositivos do StorSimple para alterar suas senhas do StorSimple

## <a name="overview"></a>Visão geral
A opção **Configurações de dispositivo** do Portal do Azure contém todos os parâmetros de dispositivo que podem ser reconfigurados em um dispositivo StorSimple gerenciado por um serviço do Gerenciador de Dispositivos do StorSimple. Este tutorial explica como você pode usar a opção **Segurança** nas **Configurações do dispositivo** para alterar sua senha de administrador do dispositivo ou do StorSimple Snapshot Manager.

## <a name="change-the-device-administrator-password"></a>Alterar a senha de administrador do dispositivo
Quando você usar a interface do Windows PowerShell para acessar o dispositivo StorSimple, será solicitada a inserção de uma senha de administrador do dispositivo. Quando o primeiro dispositivo StorSimple é registrado em um serviço, a senha padrão para essa interface é *Password1*. Para a segurança de seus dados, você deve alterar essa senha no fim do processo de registro. Não é possível sair do processo de registro sem alterar essa senha. Para obter mais informações, consulte a [Etapa 3: configurar e registrar o dispositivo por meio do Windows PowerShell para StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

A senha que foi definida pela primeira vez por meio da interface do Windows PowerShell durante o registro pode ser alterada mais tarde no Portal do Azure. Execute as etapas a seguir para alterar a senha de administrador do dispositivo.

#### <a name="to-change-the-device-administrator-password"></a>Para alterar a senha de administrador do dispositivo
1. Acesse o serviço Gerenciador de Dispositivo StorSimple e clique em **Dispositivos**.

2. Na listagem tabular dos dispositivos, selecione e clique no dispositivo cuja senha você deseja alterar.

    ![Captura de tela mostrando o serviço StorSimple Gerenciador de Dispositivos. Em gerenciamento, dispositivos é selecionado. Na lista de dispositivos, um dispositivo é selecionado.](./media/storsimple-8000-change-passwords/changepwd1.png)

3. Na folha **Configurações**, acesse **Configurações do dispositivo > Segurança**.

    ![Captura de tela mostrando a folha configurações do serviço Gerenciador de Dispositivos. Em configurações do dispositivo, a segurança é selecionada.](./media/storsimple-8000-change-passwords/changepwd2.png)

4. Na folha **Configurações de segurança**, clique em **Senha** para alterar a senha de administrador do dispositivo.

    ![Captura de tela mostrando a folha configurações de segurança. O botão senha é realçado.](./media/storsimple-8000-change-passwords/changepwd3.png)

5. Na folha **Senha**, forneça uma senha do administrador contendo de 8 a 15 caracteres. A senha deve ser uma combinação de três ou mais caracteres maiúsculos, minúsculos, numéricos e especiais.

6. Confirme a senha.

    ![Captura de tela mostrando a folha senha. Em senha do administrador do dispositivo, as caixas nova senha e confirmar senha são preenchidas.](./media/storsimple-8000-change-passwords/changepwd4.png)

7. Clique em **Salvar** e, quando precisar confirmar, clique em **Sim**.

    ![Captura de tela mostrando a folha senha. O botão salvar é realçado.](./media/storsimple-8000-change-passwords/changepwd6.png)

A senha do administrador do dispositivo agora deve estar atualizada. Você pode usar essa senha modificada para acessar a interface do Windows PowerShell.

## <a name="set-the-storsimple-snapshot-manager-password"></a>Definir a senha do StorSimple Snapshot Manager
O software Gerenciador de Instantâneos StorSimple software reside no host Windows e permite que os administradores gerenciem backups do seu dispositivo StorSimple na forma de instantâneos locais e de nuvem.

Ao configurar um dispositivo no StorSimple Snapshot Manager, será solicitado que você forneça o endereço IP e a senha do dispositivo para autenticar o dispositivo de armazenamento.

Você pode definir ou alterar a senha do StorSimple Snapshot Manager pelo Portal do Azure. Realize as etapas a seguir para definir ou alterar a senha do StorSimple Snapshot Manager.

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>Para definir a senha do StorSimple Snapshot Manager
1. Acesse o serviço Gerenciador de Dispositivo StorSimple e clique em **Dispositivos**.

2. Na listagem tabular dos dispositivos, selecione e clique no dispositivo cuja senha do StorSimple Snapshot Manager você deseja definir ou alterar.

     ![Captura de tela mostrando o serviço StorSimple Gerenciador de Dispositivos. Em gerenciamento, dispositivos é selecionado. Na lista de dispositivos, um dispositivo é selecionado.](./media/storsimple-8000-change-passwords/changepwd1.png)

3. Na folha **Configurações**, acesse **Configurações do dispositivo > Segurança**.

     ![Captura de tela mostrando a folha configurações do serviço Gerenciador de Dispositivos. Em configurações do dispositivo, a segurança é selecionada.](./media/storsimple-8000-change-passwords/changepwd2.png)

4. Na folha **Configurações de segurança**, clique em **Senha** para definir ou alterar a senha do StorSimple Snapshot Manager.

     ![Captura de tela mostrando a folha configurações de segurança. O botão senha é realçado.](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. Na folha **Senha**, insira uma senha que tenha 14 ou 15 caracteres. Verifique se a senha contém uma combinação de três ou mais caracteres maiúsculos, minúsculos, numéricos e especiais.

6. Confirme a senha.

     ![Captura de tela mostrando a folha senha. Em Snapshot Manager senha, as caixas nova senha e confirmar senha são preenchidas.](./media/storsimple-8000-change-passwords/changepwd5.png)

7. Clique em **Salvar** e, quando precisar confirmar, clique em **Sim**.

     ![Captura de tela mostrando a folha senha. O botão salvar é realçado.](./media/storsimple-8000-change-passwords/changepwd6.png)

A senha do StorSimple Snapshot Manager agora deve ser atualizada.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre a [segurança do StorSimple](storsimple-8000-security.md).
* Saiba mais sobre [como modificar a configuração do dispositivo](storsimple-8000-modify-device-config.md).
* Saiba mais sobre como [usar o serviço Gerenciador de Dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

