---
title: Clonar um backup do StorSimple Virtual Array | Microsoft Docs
description: Saiba como clonar um backup e recuperar um arquivo da Matriz Virtual StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: af6e979c-55e3-477c-b53e-a76a697f80c9
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: feffbb634af62d70a840febcf2a04afb7bdeeddd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60580752"
---
# <a name="clone-from-a-backup-of-your-storsimple-virtual-array"></a>Clonar de um backup da Matriz Virtual StorSimple

## <a name="overview"></a>Visão geral

Este artigo descreve passo a passo como clonar um conjunto de backup de seus volumes ou compartilhamentos na sua Matriz Virtual do Microsoft Azure StorSimple. O backup clonado é usado para recuperar um arquivo excluído ou perdido. O artigo também inclui etapas detalhadas para realizar uma recuperação em nível de item na Matriz Virtual StorSimple configurada como um servidor de arquivos.

## <a name="clone-shares-from-a-backup-set"></a>Clonar compartilhamentos de um conjunto de backup

**Antes de tentar clonar os compartilhamentos, verifique se você tem espaço suficiente no dispositivo para concluir esta operação.** Para clonar de um backup, execute as etapas a seguir no [portal do Azure](https://portal.azure.com/).

#### <a name="to-clone-a-share"></a>Para clonar um compartilhamento

1. Navegue até a folha **Dispositivos**. Selecione e clique em seu dispositivo e em **Compartilhamentos**. Selecione o compartilhamento que você deseja clonar e clique com o botão direito do mouse no compartilhamento para invocar o menu de contexto. Selecione **Clonar**.
   
   ![Clonar um backup](./media/storsimple-virtual-array-clone/cloneshare1.png)
2. Na folha **Clonar**, clique em **Backup > Selecionar** e faça o seguinte: 
   
    a.    Filtre um backup neste dispositivo com base no intervalo de tempo. Você pode escolher entre **Últimos 7 dias**, **Últimos 30 dias** e **Ano passado**.
   
   b.    Na lista de backups filtrados exibida, selecione um backup a ser clonado.
   
   c.    Clique em **OK**.
   
   ![Clonar um backup](./media/storsimple-virtual-array-clone/cloneshare3.png)
3. Na folha **Clonar**, clique em **Configurações de destino** e faça o seguinte:
   
    a.    Forneça um nome do compartilhamento. O nome do compartilhamento deve conter entre 3 e 127 caracteres.
   
   b.    Opcionalmente, forneça uma descrição para o compartilhamento clonado.
   
   c.    Você não pode alterar o tipo do compartilhamento que está restaurando. Um compartilhamento em camadas é clonado como em camadas, e um compartilhamento fixado localmente é clonado como um compartilhamento fixado localmente.
   
   d.    A capacidade é definida como igual ao tamanho do compartilhamento que está sendo clonado.
   
   e.    Atribua administradores para o compartilhamento. Você poderá modificar as propriedades de compartilhamento usando o Gerenciador de Arquivos após a clonagem ser concluída.
   
   f.    Clique em **OK**.
   
   ![Clonar um backup](./media/storsimple-virtual-array-clone/cloneshare6.png)

4. Clique em **Clonar** para iniciar um trabalho de clonagem. Depois que o trabalho for concluído, a operação de clonagem será iniciada e você será notificado. Para monitorar o progresso da clonagem, vá para a folha **Trabalhos** e clique no trabalho a fim de exibir os detalhes dele.
5. Depois que o clone é criado com êxito, navegue até a folha **Compartilhamentos** no seu dispositivo.
6. Agora você pode exibir o novo compartilhamento clonado na lista de compartilhamentos em seu dispositivo. Um compartilhamento em camadas é clonado como em camadas, e um compartilhamento fixado localmente é clonado como um compartilhamento fixado localmente.
   
   ![Clonar um backup](./media/storsimple-virtual-array-clone/cloneshare10.png)

## <a name="clone-volumes-from-a-backup-set"></a>Clonar volumes de um conjunto de backup

Para clonar um backup, você precisa executar etapas semelhantes às da clonagem de um compartilhamento no portal do Azure. A operação de clonagem clona o backup em um novo volume no mesmo dispositivo virtual. Você não pode clonar em um dispositivo diferente.

#### <a name="to-clone-a-volume"></a>Para clonar um volume

1. Navegue até a folha **Dispositivos**. Selecione e clique em seu dispositivo e em **Volumes**. Selecione o volume que você deseja clonar e clique com o botão direito do mouse no volume para invocar o menu de contexto. Selecione **Clonar**.
   
   ![Clonar um volume](./media/storsimple-virtual-array-clone/clonevolume1.png)
2. Na folha **Clonar**, clique em **Backup** e faça o seguinte: 
   
    a.    Filtre um backup neste dispositivo com base no intervalo de tempo. Você pode escolher entre **Últimos 7 dias**, **Últimos 30 dias** e **Ano passado**. 
   
   b.    Na lista de backups filtrados exibida, selecione um backup a ser clonado.
   
   c.    Clique em **OK**.
   
   ![Clonar um backup](./media/storsimple-virtual-array-clone/clonevolume3.png)
3. Na folha **Clonar**, clique em **Configurações do volume de destino** e faça o seguinte:
   
    a. O nome do dispositivo é preenchido automaticamente.
   
   b. Forneça um nome de volume para o **volume clonado**. O nome do volume deve conter entre 3 e 127 caracteres.
   
   c. O tipo de volume é definido automaticamente como volume original. Um volume em camadas é clonado como em camadas, e um volume fixado localmente é clonado como um volume fixado localmente.
   
   d. Para os **Hosts conectados**, clique em **Selecionar**.
   
   ![Clonar um backup](./media/storsimple-virtual-array-clone/clonevolume4.png)
4. Na folha **Hosts conectados**, selecione um ACR existente ou adicione um novo ACR. Para adicionar um novo ACR, você precisará fornecer um nome de ACR e o IQN do host. Clique em **Selecionar**.
   
   ![Clonar um backup](./media/storsimple-virtual-array-clone/clonevolume5.png)
5. Clique em **Clonar** para iniciar um trabalho de clonagem.
   
   ![Clonar um backup](./media/storsimple-virtual-array-clone/clonevolume6.png)  
6. Depois que o trabalho de clone é criado, a clonagem é iniciada. Depois que o clone é criado, ele aparece na folha Volumes do seu dispositivo. Um volume em camadas é clonado como em camadas, e um volume fixado localmente é clonado como um volume fixado localmente.
   
   ![Clonar um backup](./media/storsimple-virtual-array-clone/clonevolume8.png)
7. Quando o volume for exibido online na lista de volumes, o volume estará disponível para uso. No host do iniciador iSCSI, atualize a lista de destinos na janela de propriedades do iniciador iSCSI. Um novo destino que contém o nome do volume clonado deve aparecer como 'inativo' na coluna status.
8. Selecione o destino e clique em **Conectar**. Após o iniciador ser conectado ao destino, o status deverá mudar para **Conectado**.
9. Na janela **Gerenciamento de Disco**, os volumes montados aparecem conforme mostrado na ilustração a seguir. Clique com o botão direito no volume descoberto (clique no nome do disco) e depois clique em **Online**.

> [!IMPORTANT]
> Ao tentar clonar um volume ou um compartilhamento de um conjunto de backup, se o trabalho de clonagem falhar, um volume de destino ou compartilhamento ainda poderá ser criado no portal. É importante excluir este volume de destino ou compartilhá-lo no portal para minimizar quaisquer problemas futuros causado por esse elemento.
> 
> 

## <a name="item-level-recovery-ilr"></a>ILR (recuperação em nível de item)

Esta versão apresenta a ILR (recuperação em nível de item) em uma Matriz Virtual StorSimple configurada como um servidor de arquivos. O recurso permite fazer uma recuperação granular de arquivos e pastas de um backup de nuvem de todos os compartilhamentos no dispositivo StorSimple. Você pode recuperar arquivos excluídos de backups recentes usando um modelo de autoatendimento.

Cada compartilhamento tem uma pasta *.backups* que contém os backups mais recentes. Você pode navegar até o backup desejado, copiar arquivos e pastas relevantes do backup e restaurá-los. Esse recurso elimina chamadas aos administradores para restaurar arquivos de backups.

1. Ao executar a ILR, você poderá exibir os backups usando o Gerenciador de Arquivos. Clique no compartilhamento específico em busca do qual você deseja pesquisar o backup. Você verá uma pasta *.backups* criada no compartilhamento que armazena todos os backups. Expanda a pasta *.backups* para exibir os backups. A pasta mostra a exibição detalhada de toda a hierarquia de backup. Este modo de exibição é criado sob demanda e sua criação geralmente leva apenas alguns segundos.
   
   Os últimos cinco backups são exibidos dessa forma e podem ser usados para realizar uma recuperação em nível de item. Os cinco backups recentes incluem tanto os backups agendados padrão quanto os manuais.
   
   * **Backups agendados** nomeados como &lt;Device name&gt;DailySchedule-YYYYMMDD-HHMMSS-UTC.
   * **Backups manuais** nomeados como Ad-hoc-YYYYMMDD-HHMMSS-UTC.
     
     ![](./media/storsimple-virtual-array-clone/image14.png)

2. Identifique o backup que contém a versão mais recente do arquivo excluído. Embora o nome da pasta contenha um carimbo de data/hora UTC em cada um dos casos anteriores, a hora em que a pasta foi criada é a hora efetiva do dispositivo no momento em que o backup foi iniciado. Use o carimbo de data/hora da pasta para localizar e identificar os backups.

3. Localize a pasta ou o arquivo que você deseja restaurar no backup que você identificou na etapa anterior. Observação que você pode exibir somente os arquivos ou pastas para os quais você tem permissões. Se você não pode acessar determinados arquivos ou pastas, contate o administrador do compartilhamento. O administrador pode usar o Explorador de Arquivos para editar as permissões de compartilhamento e fornecer acesso a determinado arquivo ou pasta. É uma melhor prática recomendada que o administrador do compartilhamento seja um grupo de usuários em vez de um único usuário.

4. Copie o arquivo ou pasta no compartilhamento apropriado no servidor de arquivos StorSimple.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [administrar sua StorSimple Virtual Array usando a interface do usuário da Web local](storsimple-ova-web-ui-admin.md).

