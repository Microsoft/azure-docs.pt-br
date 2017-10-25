---
title: "Gerenciar volumes do StorSimple (Atualização 3) | Microsoft Docs"
description: "Explica como adicionar, modificar, monitorar e excluir volumes do StorSimple e como colocá-los offline, se necessário."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/19/2017
ms.author: alkohli
ms.openlocfilehash: 09f4de79ab9b0cdfafd10c7c7c29b0f8e6304f14
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-manage-volumes-update-3-or-later"></a>Usar o serviço do Gerenciador de Dispositivos do StorSimple para gerenciar volumes (Atualização 3 e posterior)

## <a name="overview"></a>Visão geral

Este tutorial explica como usar o serviço do Gerenciador de Dispositivos do StorSimple para criar e gerenciar volumes nos dispositivos StorSimple da série 8000 que executam a Atualização 3 e posterior.

O serviço Gerenciador de Dispositivos StorSimple é uma extensão do portal do Azure que permite gerenciar a solução StorSimple em uma única interface da Web. Use o portal do Azure para gerenciar volumes em todos os seus dispositivos. Você também pode criar e gerenciar serviços StorSimple, gerenciar dispositivos, políticas de backup e o catálogo de backup e exibir alertas.

## <a name="volume-types"></a>Tipos de volumes

Os volumes do StorSimple podem ser:

* **Volumes fixados localmente**: os dados desses volumes permanecem no dispositivo StorSimple local em todos os momentos.
* **Volumes hierárquicos**: os dados desses volumes podem transbordar para a nuvem.

Um volume de arquivamento é um tipo de volume em camadas. O maior tamanho de bloco de eliminação de duplicação usado para volumes de arquivamento permite que o dispositivo transfira mais segmentos de dados para a nuvem.

Se necessário, você pode alterar o tipo de volume de local para camadas ou de camadas para local. Para obter mais informações, vá para [Alterar o tipo de volume](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Volumes fixados localmente

Volumes afixados localmente são volumes totalmente provisionados que não colocam os dados em camadas na nuvem, dando garantia local a dados primário, independentes da conectividade de nuvem. Dados em volumes afixados localmente não são deduplicados nem compactados; no entanto, instantâneos de volumes afixados localmente são deduplicados. 

Volumes afixados localmente são totalmente provisionados; portanto, você deve ter espaço suficiente no dispositivo ao criá-los. Você pode provisionar volumes afixados localmente até um tamanho máximo de 8 TB no dispositivo StorSimple 8100 e 20 TB no dispositivo 8600. O StorSimple reserva o espaço local restante no dispositivo para instantâneos, metadados e processamento de dados. Você pode aumentar o tamanho de um volume afixado localmente para o espaço máximo disponível, mas não é possível diminuir o tamanho de um volume depois de criado.

Quando você cria um volume afixado localmente, o espaço disponível para criação de volumes em camadas é reduzido. O inverso também é verdadeiro: se você tiver volumes em camadas existentes, o espaço disponível para criar volume afixado localmente será menor do que os limites máximos mencionados acima. Para saber mais sobre volumes locais, confira as [Perguntas frequentes sobre volumes afixados localmente](storsimple-8000-local-volume-faq.md).

### <a name="tiered-volumes"></a>Volumes hierárquicos

Volumes em camadas são volumes escassamente provisionados no qual os dados acessados com frequência permanecem locais no dispositivo e os menos usados são colocados automaticamente em camadas na nuvem. Provisionamento dinâmico é uma tecnologia de virtualização em que o armazenamento disponível parece exceder os recursos físicos. Em vez de reservar armazenamento suficiente com antecedência, o StorSimple usa o provisionamento dinâmico para alocar espaço suficiente para atender às necessidades atuais. A natureza elástica de armazenamento em nuvem facilita essa abordagem porque o StorSimple pode aumentar ou diminuir o armazenamento em nuvem para atender às demandas de mudança.

Se estiver usando o volume em camadas para dados de arquivamento, marque a caixa de seleção **Usar este volume para dados de arquivamento acessados com menos frequência** para alterar o tamanho da parte de eliminação de duplicação do volume para 512 KB. Se você não selecionar esta opção, o volume em camadas correspondente usará um tamanho de bloco de 64 KB. Um tamanho maior de bloco de eliminação de duplicação permite que o dispositivo acelere a transferência de dados de arquivo grandes para a nuvem.


### <a name="provisioned-capacity"></a>Capacidade provisionada

Consulte a tabela a seguir para máxima capacidade provisionada para cada tipo de dispositivo e volume. (Observe que os volumes afixados localmente não estão disponíveis em um dispositivo virtual.)

|  | Tamanho máximo do volume em camadas | Tamanho máximo de volume afixado localmente |
| --- | --- | --- |
| **Dispositivos físicos** | | |
| 8100 |64 TB |8 TB |
| 8600 |64 TB |20 TB |
| **Dispositivos virtuais** | | |
| 8010 |30 TB |N/D |
| 8020 |64 TB |N/D |

## <a name="the-volumes-blade"></a>A folha Volumes

A folha **Volumes** permite que você gerencie os volumes de armazenamento provisionados no dispositivo Microsoft Azure StorSimple para os iniciadores (servidores). Ela exibe a lista de volumes nos dispositivos StorSimple conectados ao serviço.

 ![Página Volumes](./media/storsimple-8000-manage-volumes-u2/volumeslist.png)

Um volume consiste em uma série de atributos:

* **Nome** – Um nome descritivo que deve ser exclusivo e que ajuda a identificar o volume. Esse nome também é usado em relatórios de monitoramento ao filtrar um volume específico. Depois que o volume é criado, ele não pode ser renomeado.
* **Status** – Pode ser online ou offline. Se um volume estiver offline, não é visível para os iniciadores (servidores) que têm permissão de acesso para usar o volume.
* **Capacidade** – especifica a quantidade total de dados que pode ser armazenada pelo iniciador (servidor). Volumes afixados localmente são totalmente provisionados e residem no dispositivo StorSimple. Volumes em camada são escassamente provisionados e os dados são deduplicados. Com volumes escassamente provisionados, o dispositivo não aloca previamente capacidade interna de armazenamento físico, nem na nuvem, de acordo com a capacidade de volume configurada. A capacidade de volume é alocada e consumida por demanda.
* **Tipo** – Indica se o volume é **Em camadas** (o padrão) ou **Afixados localmente**.

Use as instruções neste tutorial para executar as seguintes tarefas:

* Adicionar um volume 
* Modificar um volume 
* Alterar o tipo de volume
* Excluir um volume 
* Colocar um volume offline 
* Monitorar um volume 

## <a name="add-a-volume"></a>Adicionar um volume

Você [criou um volume](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) durante a implantação do dispositivo StorSimple da série 8000. Adicionar um volume é um procedimento semelhante.

#### <a name="to-add-a-volume"></a>Para adicionar um volume

1. Na lista tabular de dispositivos na folha **Dispositivos**, selecione seu dispositivo. Clique em **+ Adicionar volume**.

    ![Adicionar um novo volume](./media/storsimple-8000-manage-volumes-u2/step5createvol1.png)

2. Na folha **Adicionar um volume**:
   
    1. O campo **Selecionar dispositivo** é automaticamente preenchido com o dispositivo atual.

    2. Na lista suspensa, selecione o contêiner de volume em que você precisa adicionar um volume.

    3.  Digite uma **Nome** para o seu volume. Depois que o volume é criado, não é possível renomear o volume.

    4. Na lista suspensa, selecione o **Tipo** para o seu volume. Para cargas de trabalho que exigem garantias locais, menos latências e um melhor desempenho, selecione um volume **Fixado localmente** . Para todos os outros dados, selecione um volume **Em camadas** . Se estiver usando esse volume para dados de arquivamento, marque **Usar este volume para dados de arquivamento acessados com menos frequência**.
      
       Um volume em camadas é provisionado por completo e pode ser criado rapidamente. Se você estiver usando o volume em camadas para dados de arquivamento, a seleção de **Usar este volume para dados de arquivamento acessados com menos frequência** altera o tamanho do bloco de eliminação de duplicação para o volume para 512 KB. Se esse campo não estiver marcado, o volume em camadas correspondente usará um tamanho de bloco de 64 KB. Um tamanho maior de bloco de eliminação de duplicação permite que o dispositivo acelere a transferência de dados de arquivo grandes para a nuvem.
       
       Um volume fixado localmente é provisionado estaticamente e garante que os dados primários no volume permanecem como locais para o dispositivo e não são divulgados na nuvem.  Se você criar um volume fixado localmente, o dispositivo verificará o espaço disponível nas camadas locais para provisionar o volume do tamanho solicitado. A operação de criação de um volume fixado localmente pode envolver a perda de dados existentes do dispositivo para a nuvem e o tempo necessário para criar o volume pode ser longo. O tempo total depende do tamanho do volume provisionado, da largura de banda disponível e dos dados no dispositivo.

    5. Especifique o **Capacidade Provisionada** para o seu volume. Anote a capacidade disponível com base no tipo de volume selecionado. O tamanho do volume especificado não deve exceder o espaço disponível.
      
       É possível provisionar volumes fixados localmente de até 8,5 TB ou volumes em camadas de até 200 TB no dispositivo 8100. No dispositivo 8600, que é maior, você pode provisionar volumes localmente afixados de até 22,5 TB ou volumes em camadas de até 500 TB. Como o espaço local no dispositivo é necessário para hospedar o conjunto de trabalho de volumes em camadas, a criação de volumes fixados localmente afetará o espaço disponível para o provisionamento de volumes em camadas. Portanto, se você criar um volume fixado localmente, o espaço disponível para a criação de volumes em camadas será reduzido. Da mesma forma, se um volume em camadas é criado, o espaço disponível para a criação de volumes localmente fixados será reduzido.
      
       Se você provisionar um volume fixado localmente de 8.5 TB (tamanho máximo permitido) em seu dispositivo 8100, você esgotará todo o espaço local disponível no dispositivo. Você não pode criar um volume em camadas desse ponto em diante, pois não há espaço local no dispositivo para hospedar o conjunto de trabalho do volume em camadas. Os volumes existentes em camadas também afetam o espaço disponível. Por exemplo, se você tiver um dispositivo 8100 que já tem volumes em camadas de 106 TB, somente 4 TB de espaço estarão disponíveis para volumes fixados localmente.

    6. No campo **Hosts conectados**, clique na seta. 

        ![Hosts conectados](./media/storsimple-8000-manage-volumes-u2/step5createvol2.png)

    7. Na folha **Hosts conectados**, escolha um ACR existente ou adicione um novo ACR. Se você escolher um novo ACR, forneça um **Nome** para o ACR, o **IQN** (Nome Qualificado do iSCSI) do host do Windows. Se você não tiver o IQN, vá para [Obter o IQN de um host do Windows Server](#get-the-iqn-of-a-windows-server-host). Clique em **Criar**. Será criado um volume com as configurações especificadas.

        ![Clicar em Criar](./media/storsimple-8000-manage-volumes-u2/step5createvol3.png)

O seu novo volume agora está pronto para uso.

> [!NOTE]
> Se você criar um volume afixado localmente e depois criar outro volume afixado localmente imediatamente, os trabalhos de criação de volumes serão executados sequencialmente. O primeiro trabalho de criação de volume deve terminar antes de começar o próximo trabalho de criação de volume.

## <a name="modify-a-volume"></a>Modificar um volume

Modifica um volume quando você precisa expandi-lo ou alterar os hosts que acessam o volume.

> [!IMPORTANT]
> * Se você modificar o tamanho do volume no dispositivo, o tamanho do volume precisa ser alterado no host também.
> * As etapas do lado do host descritas aqui servem para o Windows Server 2012 (2012R2). Procedimentos para Linux ou para outros sistemas operacionais host serão diferentes. Consulte as instruções do sistema operacional host ao modificar o volume em um host que executa outro sistema operacional.

#### <a name="to-modify-a-volume"></a>Para modificar um volume

1. Acesse o serviço StorSimple Device Manager e clique em **Dispositivos**. Na listagem tabular dos dispositivos, selecione o dispositivo que tem o volume que você pretende modificar. Clique em **Configurações > Volumes**.

    ![Acessar a folha Volumes](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

2. Na listagem tabular dos volumes, selecione o volume e clique com o botão direito do mouse para invocar o menu de contexto. Selecione **Colocar offline** para colocar o volume que será modificado offline.

    ![Selecionar e colocar o volume offline](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. Na folha **Colocar offline**, examine o impacto de colocar o volume offline e marque a caixa de seleção correspondente. Verifique se o volume correspondente no host está offline primeiro. Para obter informações sobre como colocar um volume offline no servidor host conectado ao StorSimple, consulte as instruções específicas ao sistema operacional. Clique em **Colocar offline**.

    ![Examinar o impacto de colocar um volume offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

4. Depois que o volume estiver offline (conforme mostrado pelo status do volume), selecione o volume e clique com o botão direito do mouse para invocar o menu de contexto. Selecione **Modificar volume**.

    ![Selecionar Modificar volume](./media/storsimple-8000-manage-volumes-u2/modifyvol9.png)


5. Na folha **Modificar volume**, você pode fazer as seguintes alterações:
   
   1. O **Nome** do volume não pode ser editado.
   2. Converta o **Tipo** de fixado localmente para em camadas ou de em camadas para fixado localmente (consulte [Alterar o tipo de volume](#change-the-volume-type) para obter mais informações).
   3. Aumentar a **Capacidade Provisionada**. A **Capacidade Provisionada** só pode ser aumentada. Não é possível reduzir um volume depois que ele é criado.
   4. Em **Hosts conectados**, você pode modificar o ACR. Para modificar um ACR, o volume deve estar offline.

       ![Examinar o impacto de colocar um volume offline](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

5. Clique em **Salvar** para salvar as alterações. Quando solicitado a confirmar, clique em **Sim**. O portal do Azure exibirá uma mensagem de atualização do volume. Ele exibirá uma mensagem de êxito quando o volume for atualizado com êxito.

    ![Examinar o impacto de colocar um volume offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

7. Se estiver expandindo um volume, conclua as seguintes etapas no computador host do Windows:
   
   1. Acesse **Gerenciamento do Computador** ->**Gerenciamento de Disco**.
   2. Clique com o botão direito do mouse em **Gerenciamento de Disco** e selecione **Examinar Discos Novamente**.
   3. Na lista de discos, selecione o volume que você atualizou, clique com o botão direito do mouse e selecione **Estender Volume**. O Assistente para Estender Volume é iniciado. Clique em **Avançar**.
   4. Conclua o assistente com a aceitação dos valores padrão. Depois que o assistente for concluído, o volume deve mostrar o tamanho aumentado.
      
      > [!NOTE]
      > Se você expandir um volume afixado localmente e depois expandir outro volume afixado localmente imediatamente, os trabalhos de expansão de volumes serão executados sequencialmente. O primeiro trabalho de expansão de volume deve terminar antes de começar o próximo trabalho de expansão de volume.
      

## <a name="change-the-volume-type"></a>Alterar o tipo de volume

Se necessário, você pode alterar o tipo de volume de em camadas para afixado localmente ou de afixado localmente para em camadas. No entanto, essa conversão não deve ser uma ocorrência frequente.

### <a name="tiered-to-local-volume-conversion-considerations"></a>Considerações sobre a conversão de volume em camadas para volume local

Algumas razões para converter um volume em camadas para afixado localmente são:

* Garantias locais sobre disponibilidade e desempenho dos dados
* Eliminação de latências de nuvem e problemas de conectividade de nuvem.

Geralmente, existem pequenos volumes que você quer acessar com frequência. Um volume afixado localmente é totalmente provisionado quando é criado. 

Se você estiver convertendo um volume em camadas para um volume afixado localmente, o StorSimple verifica se você tem espaço suficiente no dispositivo antes de iniciar a conversão. Se não houver espaço suficiente, você receberá um erro e a operação será cancelada. 

> [!NOTE]
> Antes de começar uma conversão de em camadas para afixado localmente, verifique se você considera os requisitos de espaço de outras cargas de trabalho. 

A conversão de um volume em camadas em um volume fixo local pode prejudicar o desempenho do dispositivo. Além disso, os seguintes fatores podem aumentar o tempo necessário para concluir a conversão:

* Não há largura de banda suficiente.
* Não há backup atual.

Para minimizar os efeitos que esses fatores podem ter:

* Examine suas políticas de limitação de largura de banda e certifique-se de que uma largura de banda dedicada de 40 Mbps está disponível.
* Agende a conversão para fora do horário de pico.
* Tire um instantâneo de nuvem antes de iniciar a conversão.

Se estiver convertendo vários volumes (que dão suporte a cargas de trabalho diferentes), será necessário priorizar a conversão de volume para que os volumes de prioridade mais alta sejam convertidos primeiro. Por exemplo, é necessário converter os volumes que hospedam VMs (máquinas virtuais) ou volumes com cargas de trabalho do SQL antes de converter volumes com cargas de trabalho de compartilhamento de arquivos.

### <a name="local-to-tiered-volume-conversion-considerations"></a>Considerações sobre a conversão de volume local para volume em camadas

Altere um volume fixado localmente para um volume em camadas se precisar de espaço adicional para provisionar outros volumes. Ao converter o volume afixado localmente em camadas, a capacidade disponível no dispositivo aumenta de acordo com a capacidade liberada. Caso problemas de conectividade impeçam a conversão de um volume do tipo local para o tipo em camadas, o volume local exibirá propriedades de um volume em camadas até que a conversão seja concluída. Isso ocorre porque alguns dados podem ter sido despejados na nuvem. Esses dados despejados continuarão ocupando espaço local no dispositivo, que não poderá ser liberado até que a operação seja reiniciada e concluída.

> [!NOTE]
> Converter um volume pode levar algum tempo e não é possível cancelar uma conversão depois de iniciada. O volume permanece online durante a conversão e você pode fazer backups, mas não pode expandir nem restaurar o volume durante a conversão.


#### <a name="to-change-the-volume-type"></a>Para alterar o tipo de volume

1. Acesse o serviço StorSimple Device Manager e clique em **Dispositivos**. Na listagem tabular dos dispositivos, selecione o dispositivo que tem o volume que você pretende modificar. Clique em **Configurações > Volumes**.

    ![Acessar a folha Volumes](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Na listagem tabular dos volumes, selecione o volume e clique com o botão direito do mouse para invocar o menu de contexto. Selecione **Modificar**.

    ![Selecionar Modificar no menu de contexto](./media/storsimple-8000-manage-volumes-u2/changevoltype2.png)

4. Na folha **Modificar volume**, altere o tipo de volume selecionando o novo tipo na lista suspensa **Tipo**.
   
   * Se você estiver alterando o tipo de **Localmente afixado**, o StorSimple verificará se há capacidade suficiente.
   * Se você estiver alterando o tipo para **Em camadas** e esse volume for usado em dados de arquivamento, marque a caixa de seleção **Usar este volume para dados de arquivamento acessados com menos frequência**.
   * Se você estiver configurando um volume fixado localmente como em camadas ou _vice versa_, a mensagem a seguir será exibida.
   
    ![Mensagem Alterar tipo de volume](./media/storsimple-8000-manage-volumes-u2/changevoltype3.png)

7. Clique em **Salvar** para salvar as alterações. Quando precisar confirmar, clique em **Sim** para iniciar o processo de conversão. 

    ![Salvar e confirmar](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

8. O portal do Azure exibe uma notificação para a criação de trabalho que atualizará o volume. Clique na notificação para monitorar o status do trabalho de conversão de volume.

    ![Trabalho de conversão de volume](./media/storsimple-8000-manage-volumes-u2/changevoltype5.png)

## <a name="take-a-volume-offline"></a>Colocar um volume offline

Talvez seja necessário colocar um volume offline quando você estiver planejando modificá-lo ou excluí-lo. Quando um volume está offline, não está disponível para acesso de leitura / gravação. Você deve colocar o volume offline no host e no dispositivo.

#### <a name="to-take-a-volume-offline"></a>Para colocar um volume offline

1. Certifique-se de que o volume em questão não está em uso antes de colocá-lo offline.
2. Coloque o volume offline no host primeiro. Isso elimina qualquer risco de corrupção de dados no volume. Para etapas específicas, consulte as instruções do sistema operacional do host.
3. Depois que o host estiver offline, coloque o volume no dispositivo offline executando as seguintes etapas:
   
    1. Acesse o serviço StorSimple Device Manager e clique em **Dispositivos**. Na listagem tabular dos dispositivos, selecione o dispositivo que tem o volume que você pretende modificar. Clique em **Configurações > Volumes**.

        ![Acessar a folha Volumes](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

    2. Na listagem tabular dos volumes, selecione o volume e clique com o botão direito do mouse para invocar o menu de contexto. Selecione **Colocar offline** para colocar o volume que será modificado offline.

        ![Selecionar e colocar o volume offline](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. Na folha **Colocar offline**, examine o impacto de colocar o volume offline e marque a caixa de seleção correspondente. Clique em **Colocar offline**. 

    ![Examinar o impacto de colocar um volume offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)
      
      Você será notificado quando o volume estiver offline. O status do volume também será atualizado para Offline.
      
4. Depois que um volume estiver offline, se você selecionar o volume e clicar com o botão direito do mouse, a opção **Colocar Online** ficará disponível no menu de contexto.

> [!NOTE]
> O comando **Colocar Offline** envia uma solicitação para o dispositivo para colocar o volume offline. Se os hosts ainda estiverem usando o volume, ocorrerão conexões interrompidas, mas não ocorrerá nenhuma falha ao colocar o volume offline.

## <a name="delete-a-volume"></a>Excluir um volume

> [!IMPORTANT]
> Você pode excluir um volume apenas se ele estiver offline.

Conclua as seguintes etapas para excluir um volume.

#### <a name="to-delete-a-volume"></a>Para excluir um volume

1. Acesse o serviço StorSimple Device Manager e clique em **Dispositivos**. Na listagem tabular dos dispositivos, selecione o dispositivo que tem o volume que você pretende modificar. Clique em **Configurações > Volumes**.

    ![Acessar a folha Volumes](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Verifique o status do volume que deseja excluir. Se o volume que você deseja excluir não estiver offline, coloque-o offline primeiro. Siga as etapas em [Colocar um volume offline](#take-a-volume-offline).
4. Depois que o volume estiver offline, selecione o volume, clique com o botão direito do mouse para invocar o menu de contexto e, em seguida, selecione **Excluir**.

    ![Selecionar Excluir no menu de contexto](./media/storsimple-8000-manage-volumes-u2/deletevol1.png)

5. Na folha **Excluir**, examine e marque a caixa de seleção sobre o impacto da exclusão de um volume. Quando você excluir um volume, todos os dados que residem nele serão perdidos. 

    ![Salvar e confirmar as alterações](./media/storsimple-8000-manage-volumes-u2/deletevol2.png)

6. Depois que o volume for excluído, a lista tabular dos volumes será atualizada para indicar a exclusão.

    ![Lista de volumes atualizada](./media/storsimple-8000-manage-volumes-u2/deletevol3.png)
   
   > [!NOTE]
   > Se você excluir um volume fixado local, o espaço disponível para novos volumes pode não ser atualizado imediatamente. O Serviço do Gerenciador de Dispositivos do StorSimple atualiza o espaço local disponível periodicamente. Sugerimos que você aguarde alguns minutos antes de tentar criar o novo volume.
   >
   > Além disso, se você excluir um volume afixado localmente e depois excluir outro volume afixado localmente imediatamente, os trabalhos de exclusão de volumes serão executados sequencialmente. O primeiro trabalho de exclusão de volume deve terminar antes de começar o próximo trabalho de exclusão de volume.

## <a name="monitor-a-volume"></a>Monitorar um volume

O monitoramento de volume permite coletar estatísticas de E/S para um volume. O monitoramento é habilitado por padrão para os primeiros 32 volumes que você criar. O monitoramento de volumes adicionais é desabilitado por padrão. 

> [!NOTE]
> O monitoramento de volumes clonados está desabilitado por padrão.


Execute as seguintes etapas para habilitar ou desabilitar o monitoramento para um volume.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Para habilitar ou desabilitar o monitoramento de volume

1. Acesse o serviço StorSimple Device Manager e clique em **Dispositivos**. Na listagem tabular dos dispositivos, selecione o dispositivo que tem o volume que você pretende modificar. Clique em **Configurações > Volumes**.
2. Na listagem tabular dos volumes, selecione o volume e clique com o botão direito do mouse para invocar o menu de contexto. Selecione **Modificar**.
3. Na folha **Modificar volume**, em **Monitoramento**, selecione **Habilitar** ou **Desabilitar** para habilitar ou desabilitar o monitoramento.

    ![Desabilitar o monitoramento](./media/storsimple-8000-manage-volumes-u2/monitorvol1.png) 

4. Clique em **Salvar** e, quando precisar confirmar, clique em **Sim**. O portal do Azure exibe uma notificação de atualização do volume e, em seguida, uma mensagem de êxito, depois que o volume é atualizado com êxito.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [Clonar um volume StorSimple](storsimple-8000-clone-volume-u2.md).
* Saiba como [usar o serviço Gerenciador de Dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

