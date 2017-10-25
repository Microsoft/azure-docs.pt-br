---
title: Gerenciar seus volumes do StorSimple (U2) | Microsoft Docs
description: "Explica como adicionar, modificar, monitorar e excluir volumes do StorSimple e como colocá-los offline, se necessário."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 57896932-0aa5-4805-970c-d13403ae7551
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/28/2016
ms.author: alkohli
ms.openlocfilehash: a61c57cd74a0df8363648dd8df40e433b0e6489d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-volumes-update-2"></a>Usar o serviço StorSimple Manager para gerenciar volumes (Atualização 2)
[!INCLUDE [storsimple-version-selector-manage-volumes](../../includes/storsimple-version-selector-manage-volumes.md)]

## <a name="overview"></a>Visão geral
Este tutorial explica como usar o serviço StorSimple Manager para criar e gerenciar volumes no dispositivo StorSimple e no dispositivo virtual StorSimple com Atualização 2 instalada.

O serviço StorSimple Manager é uma extensão do portal clássico do Azure que permite gerenciar a solução do StorSimple em uma única interface da Web. Além de gerenciar volumes, você pode usar o serviço StorSimple Manager para criar e gerenciar serviços do StorSimple, exibir e gerenciar dispositivos, exibir alertas, exibir e gerenciar políticas de backup e o catálogo de backup.

## <a name="volume-types"></a>Tipos de volumes
Os volumes do StorSimple podem ser:

* **Volumes fixados localmente**: os dados desses volumes permanecem no dispositivo StorSimple local em todos os momentos.
* **Volumes hierárquicos**: os dados desses volumes podem transbordar para a nuvem.

Um volume de arquivamento é um tipo de volume em camadas. O maior tamanho de bloco de eliminação de duplicação usado para volumes de arquivamento permite que o dispositivo transfira mais segmentos de dados para a nuvem. 

Se necessário, você pode alterar o tipo de volume de local para camadas ou de camadas para local. Para obter mais informações, vá para [Alterar o tipo de volume](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Volumes fixados localmente
Volumes afixados localmente são volumes totalmente provisionados que não colocam os dados em camadas na nuvem, dando garantia local a dados primário, independentes da conectividade de nuvem. Dados em volumes afixados localmente não são deduplicados nem compactados; no entanto, instantâneos de volumes afixados localmente são deduplicados. 

Volumes afixados localmente são totalmente provisionados; portanto, você deve ter espaço suficiente no dispositivo ao criá-los. Você pode provisionar volumes afixados localmente até um tamanho máximo de 8 TB no dispositivo StorSimple 8100 e 20 TB no dispositivo 8600. O StorSimple reserva o espaço local restante no dispositivo para instantâneos, metadados e processamento de dados. Você pode aumentar o tamanho de um volume afixado localmente para o espaço máximo disponível, mas não é possível diminuir o tamanho de um volume depois de criado.

Quando você cria um volume afixado localmente, o espaço disponível para criação de volumes em camadas é reduzido. O inverso também é verdadeiro: se você tiver volumes em camadas existentes, o espaço disponível para criar volume afixado localmente será menor do que os limites máximos mencionados acima. Para saber mais sobre volumes locais, confira as [Perguntas frequentes sobre volumes afixados localmente](storsimple-local-volume-faq.md).   

### <a name="tiered-volumes"></a>Volumes hierárquicos
Volumes em camadas são volumes escassamente provisionados no qual os dados acessados com frequência permanecem locais no dispositivo e os menos usados são colocados automaticamente em camadas na nuvem. Provisionamento dinâmico é uma tecnologia de virtualização em que o armazenamento disponível parece exceder os recursos físicos. Em vez de reservar armazenamento suficiente com antecedência, o StorSimple usa o provisionamento dinâmico para alocar espaço suficiente para atender às necessidades atuais. A natureza elástica de armazenamento em nuvem facilita essa abordagem porque o StorSimple pode aumentar ou diminuir o armazenamento em nuvem para atender às demandas de mudança.

Se você estiver usando o volume em camadas para dados de arquivamento, marcar a caixa de seleção **Usar este volume para dados de arquivamento acessados com menos frequência** alterará o tamanho do bloco de eliminação de duplicação para o volume para 512 KB. Se você não selecionar esta opção, o volume em camadas correspondente usará um tamanho de bloco de 64 KB. Um tamanho maior de bloco de eliminação de duplicação permite que o dispositivo acelere a transferência de dados de arquivo grandes para a nuvem.

> [!NOTE]
> Volumes de arquivamento criados com uma versão anterior à atualização 2 do StorSimple serão importados em camadas, com a caixa de seleção de arquivamento marcada.
> 
> 

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

## <a name="the-volumes-page"></a>A página Volumes
A página **Volumes** página permite que você gerencie os volumes de armazenamento provisionados no dispositivo Microsoft Azure StorSimple para os iniciadores (servidores). Ela exibe a lista de volumes no seu dispositivo StorSimple.

 ![Página Volumes](./media/storsimple-manage-volumes-u2/VolumePage.png)

Um volume consiste em uma série de atributos:

* **Nome** – Um nome descritivo que deve ser exclusivo e que ajuda a identificar o volume. Esse nome também é usado em relatórios de monitoramento ao filtrar um volume específico.
* **Status** – Pode ser online ou offline. Se um volume estiver offline, não é visível para os iniciadores (servidores) que têm permissão de acesso para usar o volume.
* **Capacidade** – especifica a quantidade total de dados que pode ser armazenada pelo iniciador (servidor). Volumes afixados localmente são totalmente provisionados e residem no dispositivo StorSimple. Volumes em camada são escassamente provisionados e os dados são deduplicados. Com volumes escassamente provisionados, o dispositivo não aloca previamente capacidade interna de armazenamento físico, nem na nuvem, de acordo com a capacidade de volume configurada. A capacidade de volume é alocada e consumida por demanda.
* **Tipo** – Indica se o volume é **Em camadas** (o padrão) ou **Afixados localmente**.
* **Backup** – Indica se uma política de backup padrão existe para o volume.
* **Acesso** – Especifica os iniciadores (servidores) que podem acessar este volume. Os iniciadores que não são membros do registro de controle de acesso (ACR) que está associado com o volume não verão o volume.
* **Monitoramento** – Especifica se um volume está sendo monitorado. Um volume terá o monitoramento ativado por padrão quando ele é criado. O monitoramento será, no entanto, desabilitado para um clone de volume. Para habilitar o monitoramento de um volume, siga as instruções em [Monitorar um volume](#monitor-a-volume). 

Use as instruções neste tutorial para executar as seguintes tarefas:

* Adicionar um volume 
* Modificar um volume 
* Alterar o tipo de volume
* Excluir um volume 
* Colocar um volume offline 
* Monitorar um volume 

## <a name="add-a-volume"></a>Adicionar um volume
Você [criou um volume](storsimple-deployment-walkthrough-u2.md#step-6-create-a-volume) durante a implantação da solução StorSimple. Adicionar um volume é um procedimento semelhante.

#### <a name="to-add-a-volume"></a>Para adicionar um volume
1. Na página **Dispositivos**, selecione o dispositivo, clique duas vezes nele e, em seguida, clique na guia **Contêineres de Volume**.
2. Selecione um contêiner de volume na lista e clique duas vezes para acessar os volumes associados ao contêiner.
3. Clique em **Adicionar** na parte inferior da página. Isso inicia o assistente Adicionar um volume.
   
     ![Configurações básicas do assistente para Adicionar volume](./media/storsimple-manage-volumes-u2/TieredVolEx.png)
4. No assistente Adicionar um volume, em **Configurações Básicas**, faça o seguinte:
   
   1. Digite um **Nome** para o seu volume.
   2. Selecione um **Tipo de Uso** na lista suspensa. Para cargas de trabalho que exigem dados disponíveis localmente no dispositivo em todos os momentos, selecione **Localmente Afixado**. Para todos os outros tipos de dados, selecione **Em camadas**. (**Em camadas** é o padrão.)
   3. Se você selecionou **Em camadas** na etapa 2, pode marcar a caixa de seleção **Usar este volume para dados de arquivamento acessados com menos frequência** para configurar um volume de arquivamento.
   4. Especifique a **Capacidade Provisionada** para o seu volume em GB ou TB. Consulte [Capacidade provisionada](#provisioned-capacity) para tamanhos máximos de cada dispositivo e tipo de volume. Examine a **Capacidade Disponível** para determinar a quantidade de armazenamento disponível no dispositivo.
5. Clique no ícone de seta![Ícone de seta](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png). Se você estiver configurando um volume afixado localmente, verá a seguinte mensagem de erro.
   
    ![Tipo de mensagem Alterar volume](./media/storsimple-manage-volumes-u2/LocalVolEx.png)
6. Clique no ícone de seta ![Ícone de seta](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png)novamente para ir para a página **Configurações Adicionais** .
   
    ![Configurações adicionais do assistente para Adicionar volume](./media/storsimple-manage-volumes-u2/AddVolume2.png)<br>
7. Em **Configurações Adicionais**, adicione um novo registro de controle de acesso (ACR):
   
   1. Selecione um registro de controle de acesso (ACR) na lista suspensa. Como opção, você também pode abrir um novo ACR. ACRs determinam quais hosts podem acessar os volumes fazendo a correspondência do IQN do host com aqueles listados no registro. Se você não especificar um ACR, verá a seguinte mensagem de erro.
      
        ![Especifique um ACR](./media/storsimple-manage-volumes-u2/SpecifyACR.png)
   2. É recomendável que você marque a caixa de seleção **Habilitar um backup padrão para este volume** .
   3. Clique no ícone de verificação  ![Ícone de verificação](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) para criar o volume com as configurações especificadas.

O seu novo volume agora está pronto para uso.

> [!NOTE]
> Se você criar um volume afixado localmente e depois criar outro volume afixado localmente imediatamente, os trabalhos de criação de volumes serão executados sequencialmente. O primeiro trabalho de criação de volume deve terminar antes de começar o próximo trabalho de criação de volume.
> 
> 

## <a name="modify-a-volume"></a>Modificar um volume
Modifica um volume quando você precisa expandi-lo ou alterar os hosts que acessam o volume.

> [!IMPORTANT]
> * Se você modificar o tamanho do volume no dispositivo, o tamanho do volume precisa ser alterado no host também. 
> * As etapas do lado do host descritas aqui servem para o Windows Server 2012 (2012R2). Procedimentos para Linux ou para outros sistemas operacionais host serão diferentes. Consulte as instruções do sistema operacional host ao modificar o volume em um host que executa outro sistema operacional. 
> 
> 

#### <a name="to-modify-a-volume"></a>Para modificar um volume
1. Na página **Dispositivos**, selecione o dispositivo, clique duas vezes nele e, em seguida, clique na guia **Contêineres de Volume**.
2. Selecione um contêiner de volume na lista e clique duas vezes para exibir os volumes associados ao contêiner.
3. Selecione um volume e, na parte inferior da página, clique em **Modificar**. O assistente Modificar volume é iniciado.
4. No assistente Adicionar um volume, em **Configurações Básicas**, você pode fazer o seguinte:
   
   * Edite o **Nome**.
   * Converta **Tipo de Uso** de afixado localmente para em camadas ou de em camadas para afixado localmente (consulte [Alterar o tipo de volume](#change-the-volume-type) para obter mais informações).
   * Aumentar a **Capacidade Provisionada**. A **Capacidade Provisionada** só pode ser aumentada. Não é possível reduzir um volume depois que ele é criado.
5. Em **Configurações Adicionais**, você pode modificar o ACR, desde que o volume esteja offline. Se o volume estiver online, você precisará colocá-lo offline primeiro. Consulte as etapas em [Colocar um volume offline](#take-a-volume-offline) antes de modificar o ACR.
   
   > [!NOTE]
   > Você não pode alterar a opção **Habilitar um backup padrão** para o volume.
   > 
   > 
6. Salve suas alterações, clicando no ícone de verificação  ![check-icon](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png). O portal clássico do Azure exibirá uma mensagem de atualização do volume. Ele exibirá uma mensagem de êxito quando o volume for atualizado com êxito.
7. Se estiver expandindo um volume, conclua as seguintes etapas no computador host do Windows:
   
   1. Acesse **Gerenciamento do Computador** ->**Gerenciamento de Disco**.
   2. Clique com o botão direito do mouse em **Gerenciamento de Disco** e selecione **Examinar Discos Novamente**.
   3. Na lista de discos, selecione o volume que você atualizou, clique com o botão direito do mouse e selecione **Estender Volume**. O Assistente para Estender Volume é iniciado. Clique em **Avançar**.
   4. Conclua o assistente com a aceitação dos valores padrão. Depois que o assistente for concluído, o volume deve mostrar o tamanho aumentado.
      
      > [!NOTE]
      > Se você expandir um volume afixado localmente e depois expandir outro volume afixado localmente imediatamente, os trabalhos de expansão de volumes serão executados sequencialmente. O primeiro trabalho de expansão de volume deve terminar antes de começar o próximo trabalho de expansão de volume.
      > 
      > 

![Vídeo disponível](./media/storsimple-manage-volumes-u2/Video_icon.png) **Vídeo disponível**

Para assistir a um vídeo que demonstra como expandir um volume, clique [aqui](https://azure.microsoft.com/documentation/videos/expand-a-storsimple-volume/).

## <a name="change-the-volume-type"></a>Alterar o tipo de volume
Se necessário, você pode alterar o tipo de volume de em camadas para afixado localmente ou de afixado localmente para em camadas. No entanto, essa conversão não deve ser uma ocorrência frequente. Algumas razões para converter um volume em camadas para afixado localmente são:

* Garantias locais sobre disponibilidade e desempenho dos dados
* Eliminação de latências de nuvem e problemas de conectividade de nuvem.

Geralmente, existem pequenos volumes que você quer acessar com frequência. Um volume afixado localmente é totalmente provisionado quando é criado. Se você estiver convertendo um volume em camadas para um volume afixado localmente, o StorSimple verifica se você tem espaço suficiente no dispositivo antes de iniciar a conversão. Se não houver espaço suficiente, você receberá um erro e a operação será cancelada. 

> [!NOTE]
> Antes de começar uma conversão de em camadas para afixado localmente, verifique se você considera os requisitos de espaço de outras cargas de trabalho. 
> 
> 

Altere um volume afixado localmente para um volume em camadas se precisar de espaço adicional para provisionar outros volumes. Ao converter o volume afixado localmente em camadas, a capacidade disponível no dispositivo aumenta de acordo com a capacidade liberada. Se problemas de conectividade impedirem a conversão de um volume de tipo local para tipo em camadas, o volume local exibirá propriedades de um volume em camadas até que a conversão seja concluída. Isso ocorre porque alguns dados podem ter sido despejados na nuvem. Esses dados despejados continuarão a ocupar espaço local no dispositivo que não pode ser liberado até que a operação seja iniciada e concluída.

> [!NOTE]
> Converter um volume pode levar algum tempo e não é possível cancelar uma conversão depois de iniciada. O volume permanece online durante a conversão e você pode fazer backups, mas não pode expandir nem restaurar o volume durante a conversão.  
> 
> 

A conversão de um volume em camadas em um volume fixo local pode prejudicar o desempenho do dispositivo. Além disso, os seguintes fatores podem aumentar o tempo necessário para concluir a conversão:

* Não há largura de banda suficiente.
* Não há backup atual.

Para minimizar os efeitos que esses fatores podem ter:

* Examine suas políticas de limitação de largura de banda e certifique-se de que uma largura de banda dedicada de 40 Mbps está disponível.
* Agende a conversão para fora do horário de pico.
* Tire um instantâneo de nuvem antes de iniciar a conversão.

Se estiver convertendo vários volumes (que dão suporte a cargas de trabalho diferentes), será necessário priorizar a conversão de volume para que os volumes de prioridade mais alta sejam convertidos primeiro. Por exemplo, é necessário converter os volumes que hospedam VMs (máquinas virtuais) ou volumes com cargas de trabalho do SQL antes de converter volumes com cargas de trabalho de compartilhamento de arquivos.

#### <a name="to-change-the-volume-type"></a>Para alterar o tipo de volume
1. Na página **Dispositivos**, selecione o dispositivo, clique duas vezes nele e, em seguida, clique na guia **Contêineres de Volume**.
2. Selecione um contêiner de volume na lista e clique duas vezes para exibir os volumes associados ao contêiner.
3. Selecione um volume e, na parte inferior da página, clique em **Modificar**. O assistente Modificar volume é iniciado.
4. Na página **Configurações Básicas**, altere o tipo de uso selecionando o novo tipo na lista suspensa **Tipo de Uso**.
   
   * Se você estiver alterando o tipo de **Localmente afixado**, o StorSimple verificará se há capacidade suficiente.
   * Se você estiver alterando o tipo para **Em camadas** e esse volume for usado em dados de arquivamento, marque a caixa de seleção **Usar este volume para dados de arquivamento acessados com menos frequência**.
     
       ![Caixa de seleção Arquivo Morto](./media/storsimple-manage-volumes-u2/ModifyTieredVolEx.png)
5. Clique no ícone de seta ![Ícone de seta](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) para ir para a página **Configurações Adicionais** . Se você estiver configurando um volume afixado localmente, a seguinte mensagem de erro aparecerá.
   
    ![Tipo de mensagem Alterar volume](./media/storsimple-manage-volumes-u2/ModifyLocalVolEx.png)
6. Clique no ícone de seta ![Ícone de seta](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) novamente para continuar.
7. Clique no ícone de verificação  ![Ícone de verificação](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) para iniciar o processo de conversão. O portal do Azure exibirá uma mensagem de atualização do volume. Ele exibirá uma mensagem de êxito quando o volume for atualizado com êxito.

## <a name="take-a-volume-offline"></a>Colocar um volume offline
Talvez seja necessário colocar um volume offline quando você estiver planejando modificá-lo ou excluí-lo. Quando um volume está offline, não está disponível para acesso de leitura / gravação. Você precisará colocar o volume offline no host e no dispositivo. 

#### <a name="to-take-a-volume-offline"></a>Para colocar um volume offline
1. Certifique-se de que o volume em questão não está em uso antes de colocá-lo offline.
2. Coloque o volume offline no host primeiro. Isso elimina qualquer risco de corrupção de dados no volume. Para etapas específicas, consulte as instruções do sistema operacional do host.
3. Depois que o host estiver offline, coloque o volume no dispositivo offline executando as seguintes etapas:
   
   1. Na página **Dispositivos**, selecione o dispositivo, clique duas vezes nele e, em seguida, clique na guia **Contêineres de Volume**. A guia **Contêineres de Volume** lista todos os contêineres de volume associados ao dispositivo em formato de tabela.
   2. Selecione um contêiner de volume e clique nele para exibir a lista de todos os volumes dentro do contêiner.
   3. Selecione um volume e clique em **Colocar offline**.
   4. Quando solicitado a confirmar, clique em **Sim**. Agora, o volume deve estar offline.
      
      Depois que um volume está offline, a opção **Colocar Online** ficará disponível.

> [!NOTE]
> O comando **Colocar Offline** envia uma solicitação para o dispositivo para colocar o volume offline. Se os hosts ainda estiverem usando o volume, ocorrerão conexões interrompidas, mas não ocorrerá nenhuma falha ao colocar o volume offline. 
> 
> 

## <a name="delete-a-volume"></a>Excluir um volume
> [!IMPORTANT]
> Você pode excluir um volume apenas se ele estiver offline.
> 
> 

Conclua as seguintes etapas para excluir um volume.

#### <a name="to-delete-a-volume"></a>Para excluir um volume
1. Na página **Dispositivos**, selecione o dispositivo, clique duas vezes nele e, em seguida, clique na guia **Contêineres de Volume**.
2. Selecione o contêiner de volume que possui o volume que você deseja excluir. Clique no contêiner de volume para acessar a página **Volumes** .
3. Todos os volumes associados a este contêiner são exibidos em formato de tabela. Verifique o status do volume que deseja excluir. Se o volume que você deseja excluir não estiver offline, coloque-o offline em primeiro lugar, seguindo as etapas em [Colocar um volume offline](#take-a-volume-offline).
4. Depois que o volume estiver offline, clique em **Excluir** na parte inferior da página.
5. Quando solicitado a confirmar, clique em **Sim**. O volume será excluído e a página **Volumes** mostrará a lista atualizada de volumes dentro do contêiner.
   
   > [!NOTE]
   > Se você excluir um volume fixado local, o espaço disponível para novos volumes pode não ser atualizado imediatamente. O serviço do StorSimple Manager atualiza o espaço local disponível periodicamente. Sugerimos que você aguarde alguns minutos antes de tentar criar o novo volume.<br> Além disso, se você excluir um volume afixado localmente e depois excluir outro volume afixado localmente imediatamente, os trabalhos de exclusão de volumes serão executados sequencialmente. O primeiro trabalho de exclusão de volume deve terminar antes de começar o próximo trabalho de exclusão de volume.
   > 
   > 

## <a name="monitor-a-volume"></a>Monitorar um volume
O monitoramento de volume permite coletar estatísticas de E/S para um volume. O monitoramento é habilitado por padrão para os primeiros 32 volumes que você criar. O monitoramento de volumes adicionais é desabilitado por padrão. Monitoramento de volumes clonados também será desabilitado por padrão.

Execute as seguintes etapas para habilitar ou desabilitar o monitoramento para um volume.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Para habilitar ou desabilitar o monitoramento de volume
1. Na página **Dispositivos**, selecione o dispositivo, clique duas vezes nele e, em seguida, clique na guia **Contêineres de Volume**.
2. Selecione o contêiner de volume em que reside o volume e, em seguida, clique no contêiner de volume para acessar a página **Volumes** .
3. Todos os volumes associados a este contêiner são listados em formato de tabela. Clique e selecione o volume ou o clone do volume.
4. Na parte inferior da página, clique em **Modificar**.
5. No assistente Modificar Volume, em **Configurações Básicas**, selecione **Habilitar** ou **Desabilitar** na lista suspensa **Monitoramento**.

## <a name="next-steps"></a>Próximas etapas
* Saiba como [Clonar um volume StorSimple](storsimple-clone-volume.md).
* Saiba como [usar o serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

