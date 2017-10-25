---
title: "Failover de dispositivo e recuperação de desastre da StorSimple Virtual Array | Microsoft Docs"
description: Saiba mais sobre como realizar failover em sua StorSimple Virtual Array.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 3c1f9c62-af57-4634-a0d8-435522d969aa
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 12079f8dbc409afe5acc274fa08bda878c90b76e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array-via-azure-portal"></a>Failover de dispositivo e recuperação de desastre para o StorSimple Virtual Array via portal do Azure

## <a name="overview"></a>Visão geral
Este artigo descreve a recuperação de desastre para a Matriz Virtual Microsoft Azure StorSimple, incluindo as etapas detalhadas para fazer failover para outra matriz virtual. Um failover permite que você mova os dados de um dispositivo de *origem* no datacenter para um dispositivo de *destino*. O dispositivo de destino pode estar localizado no mesmo local geográfico ou em um diferente. O failover de dispositivo é para todo o dispositivo. Durante o failover, a propriedade dos dados de nuvem para o dispositivo de origem é alterada para aquela do dispositivo de destino.

Este artigo se aplica somente aos StorSimple Virtual Arrays. Para realizar failover em um dispositivo da série 8000, vá para [Failover de dispositivo e recuperação de desastre do seu dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md).

## <a name="what-is-disaster-recovery-and-device-failover"></a>O que é recuperação de desastre e failover de dispositivo?

Em um cenário de recuperação de desastre (DR), o dispositivo principal para de funcionar. Nesse cenário, você pode mover os dados de nuvem associados ao dispositivo com falha para outro dispositivo. Você pode usar o dispositivo primário como a *origem* e especificar outro dispositivo como o *destino*. Esse processo é conhecido como *failover*. Durante o failover, todos os volumes ou compartilhamentos do dispositivo de origem sofrem alteração de propriedade e são transferidos para o dispositivo de destino. Não há permissão para nenhuma filtragem dos dados.

A recuperação de desastres é modelada como uma restauração completa do dispositivo usando a disposição em camadas e acompanhamento baseados em mapa de calor. Um mapa de calor é definido pela atribuição de um valor de calor para os dados com base nos padrões de leitura e gravação. Esse mapa de calor, em seguida, distribui em camadas os fragmentos de dados com nível de calor mais baixo para a nuvem primeiro, mantendo enquanto isso os fragmentos de dados com nível de calor maior alto (mais usados) na camada local. Durante uma recuperação de desastre, o StorSimple usa o mapa de calor para restaurar e reidratar os dados da nuvem. O dispositivo agrupa todos os volumes/compartilhamentos no último backup recente (conforme determinado internamente) e executa uma restauração desse backup. A matriz virtual orquestra o processo de recuperação de desastre inteiro.

> [!IMPORTANT]
> O dispositivo de origem é excluído no final do failover de dispositivo e, portanto, não há suporte para um failback.
> 
> 

A recuperação de desastre é orquestrada por meio do recurso de failover de dispositivo e é iniciada na folha **Dispositivos** . Esta folha exibe em formato de tabela todos os dispositivos StorSimple conectados ao seu serviço Gerenciador de Dispositivos StorSimple. Para cada dispositivo, você pode ver o nome amigável, o status, a capacidade de provisionamento e a capacidade máxima, o tipo e o modelo são exibidos.

## <a name="prerequisites-for-device-failover"></a>Pré-requisitos para failover de dispositivo

### <a name="prerequisites"></a>Pré-requisitos

Para um failover de dispositivo, certifique-se de que os pré-requisitos a seguir sejam atendidos:

* O dispositivo de origem deve estar em um estado **Desativado** .
* O dispositivo de destino deve aparecer como **Pronto para configurar** no portal do Azure. Provisione uma matriz virtual de destino de mesma capacidade ou superior. Use a interface do usuário da Web local para configurar e registrar com êxito o dispositivo virtual.
  
  > [!IMPORTANT]
  > Não tente configurar o dispositivo virtual registrado por meio do serviço. Nenhuma configuração de dispositivo deve ser realizada por meio do serviço.
  > 
  > 
* O dispositivo de destino não pode ter o mesmo nome do que o dispositivo de origem.
* O dispositivo de origem e o de destino precisam ser do mesmo tipo. Você só pode realizar failover de uma matriz virtual configurada como um servidor de arquivos para outro servidor de arquivos. O mesmo é verdadeiro para um servidor iSCSI.
* Para a recuperação de desastre de um servidor de arquivos, é recomendável que você ingresse o dispositivo de destino no mesmo domínio do de origem. Essa configuração garante que as permissões de compartilhamento sejam resolvidas automaticamente. Apenas o failover para um dispositivo de destino no mesmo domínio.
* Os dispositivos de destino disponíveis para recuperação de desastre são dispositivos que têm a capacidade igual ou maior que a do dispositivo de origem. Os dispositivos conectados ao serviço, mas que não atendem aos critérios de espaço suficiente, não estão disponíveis como dispositivos de destino.

### <a name="other-considerations"></a>Outras considerações

* Para um failover planejado 
  
  * Recomendamos que você deixe todos os volumes ou compartilhamentos no dispositivo de origem em estado offline.
  * Nós recomendamos que você faça um backup do dispositivo e, em seguida, continue com o failover para minimizar a perda de dados. 
* Para um failover não planejado, o dispositivo usa o backup mais recente para restaurar os dados.

### <a name="device-failover-prechecks"></a>Verificações prévias de failover de dispositivo

Antes do início da recuperação de desastre, o dispositivo realiza verificações prévias. Essas verificações ajudam a garantir que nenhum erro ocorra quando a recuperação de desastres começar. As pré-verificações incluem:

* Validação da conta de armazenamento.
* Verificação da conectividade de nuvem com o Azure.
* Verificação do espaço disponível no dispositivo de destino.
* Verificando se um volume de dispositivo de origem de servidor iSCSI tem
  
  * nomes válidos de ACR.
  * IQN válido (que não exceda 220 caracteres).
  * senhas CHAP válidas (com 12 a 16 caracteres).

Se alguma das pré-verificações anteriores falhar, você não poderá continuar com a recuperação de desastre. Resolva esses problemas e tente realizar a recuperação de desastre novamente.

Após a recuperação de desastre ser concluída com êxito, a propriedade dos dados de nuvem no dispositivo de origem é transferida para o dispositivo de destino. O dispositivo de origem, em seguida, não está mais disponível no portal. O acesso a todos os volumes/compartilhamentos no dispositivo de origem é bloqueado e o dispositivo de destino torna-se ativo.

> [!IMPORTANT]
> Embora o dispositivo não esteja mais disponível, a máquina virtual que você provisionou no sistema host ainda está consumindo recursos. Depois que a recuperação de desastre for concluída com êxito, você poderá excluir esta máquina virtual do seu sistema host.
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>Fazer failover para uma matriz virtual

É recomendável que você provisione, configure e registre outra Matriz Virtual StorSimple com o serviço Gerenciador de Dispositivos do StorSimple antes de executar este procedimento.

> [!IMPORTANT]
> 
> * Você não pode fazer failover de um dispositivo da série StorSimple 8000 para um dispositivo virtual 1200.
> * É possível realizar failover de um dispositivo virtual habilitado para FIPS (Federal Information Processing Standard) para outro dispositivo habilitado para FIPS ou para um dispositivo não FIPS implantados no portal do governo.


Execute as seguintes etapas para restaurar o dispositivo para um dispositivo virtual do StorSimple de destino.

1. Provisionar e configurar um dispositivo de destino que atenda aos [pré-requisitos para failover de dispositivo](#prerequisites). Concluir a configuração de dispositivo por meio da interface do usuário da Web local e registre-o em seu serviço Gerenciador de Dispositivos StorSimple. Se a criação de um servidor de arquivos, vá para a etapa 1 de [configurar como servidor de arquivos](storsimple-virtual-array-deploy3-fs-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device). Se criar um servidor iSCSI, vá para a etapa 1 de [configurar como servidor iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device).

2. Coloque os volumes/compartilhamento em estado offline no host. Para colocar os volumes/compartilhamentos no estado offline, consulte as instruções específicas do sistema operacional no host. Se já não estiver offline, você precisará colocar todos os volumes/compartilhamentos no estado offline no dispositivo ao fazer o seguinte.
   
    1. Vá para a folha **Dispositivos** e selecione seu dispositivo.
   
    2. Vá para **Configurações > Gerenciar > Compartilhamentos** (ou **Configurações > Gerenciar > Volumes**). 
   
    3. Selecione um compartilhamento/volume, clique com o botão direito do mouse e selecione **Colocar offline**. 
   
    4. Quando a confirmação for solicitada, verifique **Estou ciente do impacto de colocar este compartilhamento offline.** 
   
    5. Clique em **Colocar offline**.

3. Em seu serviço Gerenciador de Dispositivos do StorSimple, vá para **Gerenciamento > Dispositivos**. Na folha **Dispositivos**, selecione e clique em seu dispositivo de origem.

4. Na folha do **painel Dispositivo**, clique em **Desativar**.

5. Na folha **Desativar**, será solicitada uma confirmação. A desativação do dispositivo é um processo *permanente* que não pode ser desfeito. Você também será lembrado para colocar seus compartilhamentos/volumes em estado offline no host. Digite o nome do dispositivo para confirmar e clique em **Desativar**.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover1.png)
6. A desativação é iniciada. Você receberá uma notificação quando a desativação tiver sido concluída com êxito.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover2.png)
7. Na página Dispositivos, o estado do dispositivo será agora alterado para **Desativado**.
    ![](./media/storsimple-virtual-array-failover-dr/failover3.png)
8. Na folha **Dispositivos**, selecione e clique no dispositivo de origem desativado para failover. 
9. Na folha do **painel Dispositivo**, clique em **Fazer failover**. 
10. Na folha **Fazer failover do dispositivo**, faça o seguinte:
    
    1. O campo de dispositivo de origem é preenchido automaticamente. Observe o tamanho total dos dados para o dispositivo de origem. O tamanho dos dados deve ser menor do que a capacidade disponível no dispositivo de destino. Examine os detalhes associados ao dispositivo de origem, como o nome do dispositivo, a capacidade total e os nomes dos compartilhamentos submetidos a failover.

    2. Na lista suspensa de dispositivos disponíveis, escolha um **Dispositivo de destino**. Somente os dispositivos com capacidade suficiente são exibidos na lista suspensa.

    3. Verifique se **Estou ciente de que esta operação fará failover dos dados para o dispositivo de destino**. 

    4. Clique em **Fazer failover**.
    
        ![](./media/storsimple-virtual-array-failover-dr/failover4.png)
11. Um trabalho de failover é iniciado e você recebe uma notificação. Vá para **Dispositivos > Trabalhos** para monitorar o failover.
    
     ![](./media/storsimple-virtual-array-failover-dr/failover5.png)
12. Na folha **Trabalhos** , você vê um trabalho de failover criado para o dispositivo de origem. Este trabalho executa as pré-verificações de recuperação de desastre.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover6.png)
    
     Após as pré-verificações de recuperação de desastre serem bem-sucedidas, o trabalho de failover gerará trabalhos de restauração para cada volume/compartilhamento que existe em seu dispositivo de origem.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover7.png)
13. Após a conclusão do failover, vá para a folha **Dispositivos** .
    
    1. Selecione e clique no dispositivo StorSimple que foi usado como o dispositivo de destino para o processo de failover.
    2. Vá para **Configurações > Gerenciamento > Compartilhamentos** (ou em **Volumes** se for um servidor iSCSI). Na folha **Compartilhamentos**, você pode exibir todos os compartilhamentos (volumes) do dispositivo antigo.
        ![](./media/storsimple-virtual-array-failover-dr/failover9.png)
14. Será necessário [criar um alias DNS](https://support.microsoft.com/kb/168322) para que todos os aplicativos que estão tentando se conectar possam ser redirecionados para o novo dispositivo.

## <a name="errors-during-dr"></a>Erros durante a recuperação de desastre

**Falha de conectividade de nuvem durante a recuperação de desastre**

Se a conectividade de nuvem é interrompida depois que começou a recuperação de desastre e antes de concluir a restauração de dispositivo, a recuperação de desastre falhará. Você receberá uma notificação de failover. O dispositivo de destino para recuperação de desastre é marcado como *inutilizável.* Você não pode usar o mesmo dispositivo de destino para futuras recuperações de desastre.

**Nenhum dispositivo de destino compatível**

Se os dispositivos de destino não tiverem espaço suficiente, você verá um erro devido a não haver nenhum dispositivo de destino compatível.

**Falhas de pré-verificações**

Se uma das pré-verificações não for atendida, você verá falhas nas pré-verificações.

## <a name="business-continuity-disaster-recovery-bcdr"></a>BCDR (recuperação de desastre de continuidade de negócios)

Um cenário de BCDR (recuperação de desastre de continuidade de negócios) ocorre quando todo o datacenter do Azure para de funcionar. Isso pode afetar o serviço Gerenciador de Dispositivos StorSimple e os dispositivos StorSimple associados.

Se houver dispositivos StorSimple que foram registrados antes da ocorrência de um desastre, talvez eles precisem ser excluídos. Após o desastre, você pode recriar e configurar esses dispositivos.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [administrar sua StorSimple Virtual Array usando a interface do usuário da Web local](storsimple-ova-web-ui-admin.md).

