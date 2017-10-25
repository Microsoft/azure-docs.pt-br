---
title: Gerenciar dispositivos com o StorSimple Snapshot Manager | Microsoft Docs
description: Descreve como usar o snap-in StorSimple Snapshot Manager MMC para conectar e gerenciar dispositivos StorSimple.
services: storsimple
documentationcenter: 
author: SharS
manager: timlt
editor: 
ms.assetid: 966ecbe3-a7fa-4752-825f-6694dd949946
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: f5e3186a4271e0be781f367fa75ada195c58c960
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>Usar o StorSimple Snapshot Manager para conectar e gerenciar dispositivos StorSimple
## <a name="overview"></a>Visão geral
Você pode usar nós no painel **Escopo** do StorSimple Snapshot Manager para verificar dados de dispositivos StorSimple importados e atualizar os dispositivos de armazenamento conectados. Além disso, quando clica no nó **Dispositivos**, você pode ver uma lista de dispositivos conectados e informações de status correspondentes no painel **Resultados**.

![Dispositivos conectados](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Figura 1: Dispositivo conectado no StorSimple Snapshot Manager** 

Dependendo de suas seleções de **Exibição**, o painel **Resultados** mostra as informações a seguir sobre cada dispositivo. (Para obter mais informações sobre como configurar um modo de exibição, vá até o [menu Exibir](storsimple-use-snapshot-manager.md#view-menu).

| Coluna de resultados | Descrição |
|:--- |:--- |
| Nome |O nome do dispositivo conforme configurado no Portal Clássico do Azure |
| Modelo |O número do modelo do dispositivo |
| Versão |A versão do software instalado no dispositivo |
| Status |Se o dispositivo está disponível |
| Sincronizado pela última vez |Data e hora em que o dispositivo foi sincronizado pela última vez |
| N° de série |O número de série do dispositivo |

Se clicar com o botão direito no nó **Dispositivos** no painel **Escopo**, você poderá selecionar as seguintes ações:

* Adicionar ou substituir um dispositivo
* Conectar um dispositivo e verificar as importações
* Atualizar os dispositivos conectados

Se clicar no nó **Dispositivos** e clicar com o botão direito no nome de um dispositivo no painel **Resultados**, você poderá selecionar as seguintes ações:

* Autenticar um dispositivo
* Exibir detalhes do dispositivo
* Atualizar um dispositivo
* Excluir uma configuração de dispositivo
* Alterar uma senha de dispositivo

> [!NOTE]
> Todas essas ações também estão disponíveis no painel **Ações** .


Este tutorial explica como usar o StorSimple Snapshot Manager para conectar e gerenciar dispositivos e executar as seguintes tarefas:

* Adicionar ou substituir um dispositivo
* Conectar um dispositivo e verificar as importações
* Atualizar os dispositivos conectados
* Autenticar um dispositivo
* Exibir detalhes do dispositivo
* Atualizar um dispositivo individual
* Excluir uma configuração de dispositivo
* Alterar uma senha de dispositivo expirada
* Substituir um dispositivo com falha

> [!NOTE]
> Para obter informações gerais sobre o uso da interface do StorSimple Snapshot Manager, acesse [Interface do usuário do StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).


## <a name="add-or-replace-a-device"></a>Adicionar ou substituir um dispositivo
Use o procedimento a seguir para adicionar ou substituir um dispositivo StorSimple.

#### <a name="to-add-or-replace-a-device"></a>Para adicionar ou substituir um dispositivo
1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Escopo**, clique com o botão direito do mouse no nó **Dispositivos** e clique em **Configurar um dispositivo**. A caixa de diálogo **Configurar um Dispositivo** é exibida.
   
    ![Configurar um dispositivo StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 
3. Na caixa suspensa **Dispositivo** , selecione o endereço IP do dispositivo ou dispositivo virtual. 
4. Na caixa de texto **Senha** , digite a senha do StorSimple Snapshot Manager que você criou para o dispositivo no portal clássico do Azure. Clique em **OK**. O StorSimple Snapshot Manager procura o dispositivo que você identificou. 
   
   * Se o dispositivo estiver disponível, o StorSimple Snapshot Manager adiciona uma conexão.
   * Se o dispositivo estiver indisponível por algum motivo, o StorSimple Snapshot Manager retorna uma mensagem de erro. Clique em **OK** para fechar a mensagem de erro e clique em **Cancelar** para fechar a caixa de diálogo **Configurar um Dispositivo**.

## <a name="connect-a-device-and-verify-imports"></a>Conectar um dispositivo e verificar as importações
Use o procedimento a seguir para conectar um dispositivo StorSimple e confirmar que os grupos de volume existentes que têm backups associados foram importados.

#### <a name="to-connect-a-device-and-verify-imports"></a>Para conectar um dispositivo e verificar as importações
1. Para conectar um dispositivo ao StorSimple Snapshot Manager, siga as instruções em Adicionar ou substituir um dispositivo. Ao se conectar a um dispositivo, o StorSimple Snapshot Manager responde da seguinte maneira:
   
   * Se o dispositivo estiver indisponível por algum motivo, o StorSimple Snapshot Manager retorna uma mensagem de erro. 
   
   * Se o dispositivo estiver disponível, o StorSimple Snapshot Manager adiciona uma conexão. Quando você seleciona o dispositivo, ele aparece no painel **Resultados** e o campo de status indica que o dispositivo está **Disponível**. O StorSimple Snapshot Manager importa os grupos de volumes configurados para o dispositivo, desde que eles tenham backups associados. Políticas de backup não são importadas. Grupos de volumes que não têm backups associados não são importados.
2. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager.
3. Clique com o botão direito no nó superior do painel **Escopo** e clique em **Ativar/Desativar Exibição de Importações**.
   
    ![Selecionar Alternar Exibição de Importações](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 
4. A caixa de diálogo **Alternar Exibição de importações** é exibida, mostrando o status dos grupos de volumes e backups importados. Clique em **OK**.

Após os grupos de volumes e backups terem sido importados com êxito, você pode usar o StorSimple Snapshot Manager para gerenciá-los, exatamente como gerenciaria grupos de volumes e backups que criou e configurou com o StorSimple Snapshot Manager. 

## <a name="refresh-connected-devices"></a>Atualizar os dispositivos conectados
Use o procedimento a seguir para sincronizar os dispositivos conectados do StorSimple com o StorSimple Snapshot Manager.

#### <a name="to-refresh-connected-devices"></a>Para atualizar os dispositivos conectados
1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Escopo**, clique com o botão direito em **Dispositivos** e clique em **Atualizar Dispositivos**. Isso sincroniza os dispositivos conectados com o StorSimple Snapshot Manager para que você possa ver os grupos de volumes e backups, incluindo quaisquer adições recentes. 
   
    ![Atualizar os dispositivos StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)

A ação **Atualizar Dispositivos** recupera quaisquer novos grupos de volumes e backups associados dos dispositivos conectados. Diferentemente da ação **Examinar volumes novamente** disponível para o nó **Volumes**, **Atualizar Dispositivos** não restaura o Registro de backups.

## <a name="authenticate-a-device"></a>Autenticar um dispositivo
Use o procedimento a seguir para autenticar um dispositivo StorSimple com o StorSimple Snapshot Manager.

#### <a name="to-authenticate-a-device"></a>Para autenticar um dispositivo
1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Escopo**, clique em **Dispositivos**.
3. No painel **Resultados**, clique com o botão direito no nome do dispositivo e clique em **Autenticar**.
4. A caixa de diálogo **Autenticar** é exibida. Digite a senha do dispositivo e clique em **OK**.
   
    ![Caixa de diálogo Autenticar](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 

## <a name="view-device-details"></a>Exibir detalhes do dispositivo
Use o procedimento a seguir para exibir os detalhes de um dispositivo StorSimple e, se necessário, ressincronizar o dispositivo ao StorSimple Snapshot Manager.

#### <a name="to-view-and-resynchronize-device-details"></a>Para exibir e ressincronizar detalhes do dispositivo
1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Escopo**, clique em **Dispositivos**.
3. No painel **Resultados**, clique com o botão direito no nome do dispositivo e clique em **Detalhes**.

4. A caixa de diálogo **Detalhes do Dispositivo** é exibida. Esta caixa mostra o nome, modelo, versão, número de série, status, IQN (nome iSCSI qualificado) de destino e data e hora da última sincronização.

* Clique em **Ressincronizar** para sincronizar o dispositivo.
* Clique em **OK** ou em **Cancelar** para fechar a caixa de diálogo.
  
  ![Detalhes do Dispositivo](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 

## <a name="refresh-an-individual-device"></a>Atualizar um dispositivo individual
Use o procedimento a seguir para ressincronizar um dispositivo StorSimple individual ao StorSimple Snapshot Manager.

#### <a name="to-refresh-a-device"></a>Para atualizar um dispositivo
1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager. 
2. No painel **Escopo**, clique em **Dispositivos**. 
3. No painel **Resultados**, clique com o botão direito no nome do dispositivo e clique em **Atualizar Dispositivo**. Isso sincroniza o dispositivo com o StorSimple Snapshot Manager.

## <a name="delete-a-device-configuration"></a>Excluir uma configuração de dispositivo
Use o procedimento a seguir para excluir uma configuração de dispositivo StorSimple individual do StorSimple Snapshot Manager.

#### <a name="to-delete-a-device-configuration"></a>Para excluir uma configuração de dispositivo
1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Escopo**, clique em **Dispositivos**. 
3. No painel **Resultados**, clique com o botão direito no nome do dispositivo e clique em **Excluir**. 
4. A seguinte mensagem é exibida. Clique em **Sim** para excluir a configuração ou em **Não** para cancelar a exclusão.
   
    ![Excluir configuração de dispositivo](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Alterar uma senha de dispositivo expirada
Você deve inserir uma senha para autenticar um dispositivo StorSimple com o StorSimple Snapshot Manager. Você configura essa senha ao usar a interface do Windows PowerShell para configurar o dispositivo. No entanto, a senha pode expirar. Se isso acontecer, você pode usar o Portal clássico do Azure para alterar a senha. Depois, como o dispositivo foi configurado no StorSimple Snapshot Manager antes da senha expirar, você precisa autenticar novamente o dispositivo no StorSimple Snapshot Manager.

#### <a name="to-change-the-expired-password"></a>Para alterar a senha expirada
1. No portal clássico do Azure, inicie o serviço StorSimple Manager.
2. Clique em **Dispositivos** > **Configurar** para o dispositivo.
3. Role até a seção do StorSimple Snapshot Manager. Insira uma senha que tenha 14 ou 15 caracteres. Verifique se a senha contém uma mistura destas quatro opções: caracteres maiúsculos, minúsculos, numéricos e especiais.
4. Reinsira a senha para confirmá-la.
5. Na parte inferior da página, clique em **Salvar** .

#### <a name="to-re-authenticate-the-device"></a>Para autenticar novamente o dispositivo
1. Inicie o StorSimple Snapshot Manager.
2. No painel **Escopo**, clique em **Dispositivos**. É exibida uma lista dos dispositivos configurados no painel **Resultados** .
3. Selecione o dispositivo, clique com o botão direito e clique em **Autenticar**.
4. Na janela **Autenticar** , insira a senha novamente.
5. Selecione o dispositivo, clique com o botão direito e selecione **Atualizar dispositivo**. Isso sincroniza o dispositivo com o StorSimple Snapshot Manager.

## <a name="replace-a-failed-device"></a>Substituir um dispositivo com falha
Se um dispositivo StorSimple falhar e for substituído por um dispositivo em espera (failover), use as seguintes etapas para conectar o novo dispositivo e exibir os backups associados.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Para conectar a um novo dispositivo após failover
1. Reconfigure a conexão iSCSI para o novo dispositivo. Para obter instruções, consulte a "Etapa 7: Montar, inicializar e formatar um volume" em [Implantar seu dispositivo StorSimple local](storsimple-8000-deployment-walkthrough-u2.md).

> [!NOTE]
> Se o novo dispositivo StorSimple tiver o mesmo endereço IP do antigo, você poderá conectar a configuração antiga.


1. Parar o Serviço de Gerenciamento do Microsoft StorSimple:
   
   1. Inicie o Gerenciador do Servidor.
   2. No Painel do Gerenciador de Servidores, no menu **Ferramentas**, selecione **Serviços**.
   3. Na janela **Serviços**, selecione o **Microsoft StorSimple Management Service**.
   4. No painel direito, em **Microsoft StorSimple Management Service**, clique em **Parar o serviço**.
2. Remova as informações de configuração relacionadas ao antigo dispositivo:
   
   1. No Explorador de Arquivos, vá até C:\ProgramData\Microsoft\StorSimple\BACatalog.
   2. Exclua os arquivos na pasta BACatalog.
3. Reinicie o Serviço de Gerenciamento do Microsoft StorSimple:
   
   1. No Painel do Gerenciador de Servidores, no menu **Ferramentas**, selecione **Serviços**.
   2. Na janela **Serviços**, selecione o **Microsoft StorSimple Management Service**.
   3. No painel direito, em **Microsoft StorSimple Management Service**, clique em **Reiniciar o serviço**.
4. Inicie o StorSimple Snapshot Manager.
5. Para configurar o novo dispositivo StorSimple, execute as etapas da Etapa 2: conectar a um dispositivo StorSimple em [Implantar o StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).
6. Clique com o botão direito do mouse no nó de nível superior no painel **Escopo** (StorSimple Snapshot Manager no exemplo) e clique em **Ativar/Desativar Exibição de Importações**. 
7. Uma mensagem é exibida quando os grupos de volumes e backups importados ficam visíveis no StorSimple Snapshot Manager. Clique em **OK**.

## <a name="next-steps"></a>Próximas etapas
* Saiba como [usar o StorSimple Snapshot Manager para administrar sua solução do StorSimple](storsimple-snapshot-manager-admin.md).
* Saiba como [Usar o StorSimple Snapshot Manager para exibir e gerenciar volumes](storsimple-snapshot-manager-manage-volumes.md).

