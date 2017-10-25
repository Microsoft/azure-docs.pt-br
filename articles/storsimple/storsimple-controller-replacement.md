---
title: Substituir um controlador de dispositivo StorSimple | Microsoft Docs
description: "Explica como remover e substituir um ou ambos os módulos do dispositivo StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: e25b52b7-60f5-47f3-bffc-6c157d57ab5d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/03/2017
ms.author: alkohli
ms.openlocfilehash: 5dd5ffc7c08fcc9263b91ca5ac86de5163f91657
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Substituir um módulo de controlador em seu dispositivo StorSimple
## <a name="overview"></a>Visão geral
Este tutorial explica como remover e substituir um ou ambos os módulos de um dispositivo StorSimple. Também aborda a lógica subjacente dos cenários de substituição do controlador único e duplo.

> [!NOTE]
> Antes de executar a substituição de um controlador, é recomendável sempre atualizar o firmware do controlador para a versão mais recente.
> 
> Para evitar danos ao seu dispositivo StorSimple, não retire o controlador até que os LEDs exibam uma das seguintes possibilidades:
> 
> * Todas as luzes estão desligadas.
> * LED 3, ![ícone de marca de seleção verde](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png) e ![ícone de cruz vermelha](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) estão piscando e LED 0 e LED 7 estão **ACESOS**.
> 
> 

A tabela a seguir mostra os cenários de substituição do controlador aceitos.

| Caixa | Cenário de substituição | Procedimento aplicável |
|:--- |:--- |:--- |
| 1 |Um controlador está em estado de falha, o outro controlador está íntegro e ativo. |[Substituição de controlador único](#replace-a-single-controller), que descreve a [lógica por trás de uma substituição de controlador único](#single-controller-replacement-logic), bem como as [etapas de substituição](#single-controller-replacement-steps). |
| 2 |Ambos os controladores falharam e exigem substituição. O chassi, os discos e o compartimento de disco estão íntegros. |[Substituição de controlador duplo](#replace-both-controllers), que descreve a [lógica por trás de uma substituição de controlador duplo](#dual-controller-replacement-logic), bem como as [etapas de substituição](#dual-controller-replacement-steps). |
| 3 |Os controladores no mesmo dispositivo ou de dispositivos diferentes estão trocados. O chassi, os discos e os compartimentos de disco estão íntegros. |Será exibida uma mensagem de alerta de incompatibilidade do slot. |
| 4 |Um controlador está ausente e o outro controlador falha. |[Substituição de controlador duplo](#replace-both-controllers), que descreve a [lógica por trás de uma substituição de controlador duplo](#dual-controller-replacement-logic), bem como as [etapas de substituição](#dual-controller-replacement-steps). |
| 5 |Um ou ambos os controladores falharam. Não é possível acessar o dispositivo por meio do console serial ou de comunicação remota do Windows PowerShell. |[Contate o Suporte da Microsoft](storsimple-contact-microsoft-support.md) para saber o procedimento manual de substituição do controlador. |
| 6 |Os controladores têm uma versão de compilação diferente, o que pode ser devido a:<ul><li>Os controladores têm uma versão de software diferente.</li><li>Os controladores têm uma versão de firmware diferente.</li></ul> |Se as versões de software do controlador forem diferentes, a lógica de substituição detectará isso e atualizará a versão do software no controlador de substituição.<br><br>Se as versões de firmware do controlador forem diferentes e a versão antiga do firmware **não** for atualizável de forma automática, uma mensagem de alerta aparecerá no Portal clássico do Azure. Você deve verificar se há atualizações e instalar as atualizações de firmware.</br></br>Se as versões do firmware do controlador forem diferentes e a versão do firmware antigo puder ser atualizada automaticamente, a lógica de substituição de controlador detecta isso e, depois que o controlador iniciar, o firmware será atualizado automaticamente. |

Você precisa remover um módulo do controlador caso apresente falha. Um ou ambos os módulos de controlador podem falhar, o que pode resultar em substituição de controlador único ou substituição de controlador duplo. Para conhecer os procedimentos de substituição e a lógica por trás deles, consulte o seguinte:

* [Substituir controlador único](#replace-a-single-controller)
* [Substituir ambos os controladores](#replace-both-controllers)
* [Remover um controlador](#remove-a-controller)
* [Inserir um controlador](#insert-a-controller)
* [Identificar o controlador ativo em seu dispositivo](#identify-the-active-controller-on-your-device)

> [!IMPORTANT]
> Antes de remover e substituir um controlador, examine as informações de segurança em [Substituição do componente de hardware do StorSimple](storsimple-hardware-component-replacement.md).
> 
> 

## <a name="replace-a-single-controller"></a>Substituir controlador único
Quando um dos dois controladores no dispositivo Microsoft Azure StorSimple falhar, estiver com defeito ou ausente, será necessário substituir um único controlador. 

### <a name="single-controller-replacement-logic"></a>Lógica de substituição de controlador único
Em uma substituição de controlador único, é preciso primeiro remover o controlador com falha. (O controlador restante no dispositivo é o controlador ativo.) Ao inserir o controlador de substituição, ocorrem as seguintes ações:

1. O controlador de substituição começa imediatamente a comunicação com o dispositivo StorSimple.
2. Um instantâneo do disco rígido virtual (VHD) referente ao controlador ativo é copiado no controlador de reposição.
3. O instantâneo é modificado para que quando o controlador de substituição iniciar a partir desse VHD, seja reconhecido como controlador em espera.
4. Quando as modificações forem concluídas, o controlador de substituição será iniciado como controlador em espera.
5. Quando ambos os controladores estiverem em execução, o cluster fica online.

### <a name="single-controller-replacement-steps"></a>Etapas de substituição de controlador único
Conclua as etapas a seguir se um dos controladores do dispositivo Microsoft Azure StorSimple falhar. (O outro controlador precisa estar ativo e em execução. Se ambos os controladores falharem ou apresentarem defeito, vá para [Etapas de substituição de controlador duplo](#dual-controller-replacement-steps).)

> [!NOTE]
> Pode levar de 30 a 45 minutos para o controlador reiniciar e se recuperar completamente do procedimento de substituição de controlador único. O tempo total para o procedimento completo, incluindo a conexão dos cabos, é de aproximadamente 2 horas.
> 
> 

#### <a name="to-remove-a-single-failed-controller-module"></a>Para remover um módulo de controlador único com falha
1. No portal clássico do Azure, acesse o serviço StorSimple Manager, clique na guia **Dispositivos** e, em seguida, clique no nome do dispositivo que você deseja monitorar.
2. Vá para **Manutenção > Status do Hardware**. O status do Controlador 0 ou Controlador 1 deve estar vermelho, o que indica uma falha.
   
   > [!NOTE]
   > O controlador com falha na substituição de controlador único é sempre um controlador em espera.
   > 
   > 
3. Use a Figura 1 e a tabela a seguir para localizar o módulo do controlador com falha.  
   
    ![Backplane dos módulos do compartimento primário do dispositivo](./media/storsimple-controller-replacement/IC740994.png)
   
    **Figura 1** Parte posterior do dispositivo StorSimple
   
   | Rótulo | Descrição |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Controlador 0 |
   | 4 |Controlador 1 |
4. No controlador com falha, remova todos os cabos de rede conectados das portas de dados. Se você estiver usando um modelo 8600, também remova os cabos SAS que conectam o controlador ao controlador do EBOD.
5. Siga as etapas em [remover um controlador](#remove-a-controller) para remover o controlador com falha. 
6. Instale a peça de reposição de fábrica no mesmo slot do qual o controlador com falha foi removido. Isso aciona a lógica de substituição do controlador único Para saber mais, consulte [Lógica de substituição do controlador único](#single-controller-replacement-logic).
7. Enquanto a lógica de substituição do controlador único continua no plano de fundo, reconecte os cabos. Tome cuidado para conectar todos os cabos exatamente da mesma maneira que estavam conectados antes da substituição.
8. Depois que o controlador for reiniciado, verifique o **Status do controlador** e o **Status do cluster** no portal clássico do Azure para verificar se o controlador está de volta a um estado íntegro e está em modo de espera.

> [!NOTE]
> Se você estiver monitorando o dispositivo através do console serial, você poderá ver diversas reinicializações enquanto o controlador se recupera do procedimento de substituição. Quando o menu do console serial é apresentado, você saberá que a substituição foi concluída. Se o menu não aparecer dentro de duas horas do início da substituição do controlador, [contate o Suporte da Microsoft](storsimple-contact-microsoft-support.md).
>
> A partir da Atualização 4, também é possível usar o cmdlet `Get-HCSControllerReplacementStatus` na interface do Windows PowerShell do dispositivo para monitorar o status do processo de substituição de controlador.
> 

## <a name="replace-both-controllers"></a>Substituir ambos os controladores
Quando ambos os controladores no dispositivo Microsoft Azure StorSimple falharem, estiverem com defeito ou ausentes, será necessário substituir ambos os controladores. 

### <a name="dual-controller-replacement-logic"></a>Lógica de substituição do controlador duplo
Na substituição de um controlador duplo, primeiro remova os dois controladores com falha e insira as peças de reposição. Quando os dois controladores de reposição estiverem inseridos, ocorrerão as seguintes ações:

1. O controlador de reposição no slot 0 verifica o seguinte:
   
   1. Ele está usando as versões atuais do firmware e software?
   2. É parte do cluster?
   3. O controlador de par está em execução e é clusterizado?
      
      Se nenhuma dessas condições for verdadeira, o controlador procurará o backup diário mais recente (localizado no **nonDOMstorage** na unidade S). O controlador copia o instantâneo mais recente do VHD do backup.
2. O controlador no slot 0 usa o instantâneo para criar uma imagem de si.
3. Enquanto isso, o controlador no slot 1 aguarda controlador 0 concluir a criação de imagem e iniciar.
4. Depois que o controlador 0 iniciar, o controlador 1 detecta o cluster criado pelo controlador 0, que dispara a lógica de substituição do controlador único. Para saber mais, consulte [Lógica de substituição do controlador único](#single-controller-replacement-logic).
5. Posteriormente, ambos os controladores estarão em execução e o cluster ficará online.

> [!IMPORTANT]
> Após substituição de um controlador duplo, depois que o dispositivo StorSimple estiver configurado, é essencial fazer um backup manual do dispositivo. Os backups diários da configuração do dispositivo não são acionados antes de 24 horas. Trabalhe com o [Suporte da Microsoft](storsimple-contact-microsoft-support.md) para fazer um backup manual de seu dispositivo.
> 
> 

### <a name="dual-controller-replacement-steps"></a>Etapas de substituição de controlador duplo
Esse fluxo de trabalho é necessário quando ambos os controladores em seu dispositivo Microsoft Azure StorSimple falharem. Isso pode acontecer em um data center em que o sistema de resfriamento pare de funcionar e, como resultado, ambos os controladores falhem dentro de um curto período de tempo. Dependendo do estado do dispositivo StorSimple, desativado ou ativado, e se você estiver usando um modelo 8600 ou 8100, um conjunto diferente de etapas será necessário.

> [!IMPORTANT]
> Pode levar de 45 minutos a uma hora para o controlador reiniciar e se recuperar completamente do procedimento de substituição do controlador duplo. O tempo total de todo o procedimento, incluindo a conexão dos cabos, é de aproximadamente 2,5 horas.
> 
> 

#### <a name="to-replace-both-controller-modules"></a>Para substituir ambos os módulos de controlador
1. Se o dispositivo estiver desligado, ignore esta etapa e vá para a próxima etapa. Se o dispositivo estiver ligado, desligue o dispositivo.
   
   1. Se você estiver usando um modelo 8600, primeiro desligue o compartimento primário e, em seguida, desligue o compartimento EBOD.
   2. Aguarde até que o dispositivo esteja completamente desligado. Todos os LEDs na parte posterior do dispositivo serão desativados.
2. Remova todos os cabos de rede conectados às portas de dados. Se você estiver usando um modelo 8600, também remova os cabos SAS que conectam o compartimento primário ao compartimento EBOD.
3. Remova ambos os controladores do dispositivo StorSimple. Para obter mais informações, consulte [Remover um controlador](#remove-a-controller).
4. Primeiro insira a peça de reposição de fábrica do Controlador 0 e, em seguida, insira o Controlador 1. Para saber mais, consulte [insert a controller](#insert-a-controller). Isso aciona a lógica de substituição do controlador duplo Para saber mais, consulte [dual controller replacement logic](#dual-controller-replacement-logic).
5. Enquanto a lógica de substituição do controlador continua no plano de fundo, reconecte os cabos. Tome cuidado para conectar todos os cabos exatamente da mesma maneira que estavam conectados antes da substituição. Confira as instruções detalhadas do seu modelo na seção Cabear o dispositivo de [Instalar o dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) ou [Instalar o dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).
6. Ligue o dispositivo StorSimple. Se estiver usando um modelo 8600:
   
   1. Certifique-se de que o compartimento EBOD seja ativado primeiro.
   2. Aguarde até que o compartimento EBOD esteja  em execução.
   3. Ligue o compartimento primário.
   4. Após o primeiro controlador ser reiniciado e estar em um estado íntegro, o sistema será executado.
      
      > [!NOTE]
      > Se você estiver monitorando o dispositivo através do console serial, você poderá ver diversas reinicializações enquanto o controlador se recupera do procedimento de substituição. Quando o menu do console serial é apresentado, você saberá que a substituição foi concluída. Se o menu não aparecer dentro de 2,5 horas do início da substituição do controlador, [contate o Suporte da Microsoft](storsimple-contact-microsoft-support.md).
      > 
      > 

## <a name="remove-a-controller"></a>Remover um controlador
Use o procedimento a seguir para remover um módulo do controlador com falha do dispositivo StorSimple.

> [!NOTE]
> As ilustrações a seguir são para o controlador 0. No controlador 1, as ilustrações seriam invertidas.
> 
> 

#### <a name="to-remove-a-controller-module"></a>Para remover um módulo do controlador
1. Segure a trava do módulo entre o polegar e o dedo indicador.
2. Aperte gentilmente o polegar e o dedo indicador para liberar a trava do controlador.
   
    ![Liberando a trava do controlador](./media/storsimple-controller-replacement/IC741047.png)
   
    **Figura 2** Liberando a trava do controlador
3. Use a trava como alça para deslizar o controlador para fora do chassi.
   
    ![Deslizando o controlador para fora do chassi](./media/storsimple-controller-replacement/IC741048.png)
   
    **Figura 3** Deslizando o controlador para fora do chassi

## <a name="insert-a-controller"></a>Inserir um controlador
Use o procedimento a seguir para instalar um módulo do controlador fornecido pela fábrica depois de remover um módulo com falha do seu dispositivo StorSimple.

#### <a name="to-install-a-controller-module"></a>Para instalar um módulo do controlador
1. Verifique se há danos aos conectores de interface. Não instale o módulo se algum pino do conector estiver danificado ou torto.
2. Deslize o módulo do controlador no chassi com a trava totalmente liberada. 
   
    ![Deslizando o controlador para dentro do chassi](./media/storsimple-controller-replacement/IC741053.png)
   
    **Figura 4** Deslizando o controlador no chassi
3. Com o módulo do controlador inserido, comece fechando a trava enquanto continua empurrando o módulo do controlador no chassi. A trava engatará para orientar o controlador até o lugar.
   
    ![Fechando a trava do controlador](./media/storsimple-controller-replacement/IC741054.png)
   
    **Figura 5** Fechando a trava do controlador
4. Você terminará quando a trava se encaixar no lugar. O LED **OK** já deve estar aceso.  
   
   > [!NOTE]
   > Pode levar até 5 minutos até que o controlador e o LED estejam ativados.
   > 
   > 
5. Para verificar se a substituição foi bem-sucedida, no portal clássico do Azure, vá para **Dispositivos** > **Manutenção** > **Status de Hardware** e certifique-se de que o controlador 0 e o controlador 1 estejam íntegros (status verde).

## <a name="identify-the-active-controller-on-your-device"></a>Identificar o controlador ativo em seu dispositivo
Há muitas situações, como substituição de controlador ou de registro do dispositivo pela primeira vez, que exigem que você localize o controlador ativo em um dispositivo StorSimple. O controlador ativo processa todas as operações de rede e firmware de disco. Você pode usar qualquer um dos seguintes métodos para identificar o controlador ativo:

* [Usar o Portal clássico do Azure para identificar o controlador ativo](#use-the-azure-classic-portal-to-identify-the-active-controller)
* [Usar o Windows PowerShell para StorSimple para identificar o controlador ativo](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)
* [Verificar o dispositivo físico para identificar o controlador ativo](#check-the-physical-device-to-identify-the-active-controller)

Cada um desses procedimentos é descrito abaixo.

### <a name="use-the-azure-classic-portal-to-identify-the-active-controller"></a>Usar o Portal clássico do Azure para identificar o controlador ativo
No portal clássico do Azure, navegue até **Dispositivos** > **Manutenção** e role até a seção **Controladores**. Aqui você pode verificar qual controlador está ativo.

![Identificar o controlador ativo no portal clássico do Azure](./media/storsimple-controller-replacement/IC752072.png)

**Figura 6** portal clássico do Azure mostrando o controlador ativo

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>Usar o Windows PowerShell para StorSimple para identificar o controlador ativo
Quando você acessar o dispositivo por meio do console serial, será apresentada uma mensagem de cabeçalho. A mensagem de cabeçalho contém informações de dispositivo básicas, como modelo, nome, versão do software instalado e status do controlador que você está acessando. A imagem a seguir mostra um exemplo de uma mensagem de cabeçalho:

![Mensagem da faixa serial](./media/storsimple-controller-replacement/IC741098.png)

**Figura 7** Mensagem de cabeçalho mostrando o controlador 0 como ativo

Você pode usar a mensagem de cabeçalho para determinar se o controlador ao qual você está conectado está ativo ou passivo.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Verificar o dispositivo físico para identificar o controlador ativo
Para identificar o controlador ativo no dispositivo, localize o LED azul acima a porta DATA 5 na parte traseira do compartimento primário.

Se esse LED estiver piscando, o controlador estará ativo e o outro controlador estará em modo de espera. Use o diagrama e a tabela a seguir como auxílio.

![Backplane do compartimento primário do dispositivo com portas de dados](./media/storsimple-controller-replacement/IC741055.png)

**Figura 8** Parte traseira do compartimento primário com portas de dados e LEDs de monitoramento

| Rótulo | Descrição |
|:--- |:--- |
| 1-6 |Portas de rede DATA 0 – 5 |
| 7 |LED azul |

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [substituição de componentes de hardware do StorSimple](storsimple-hardware-component-replacement.md).

