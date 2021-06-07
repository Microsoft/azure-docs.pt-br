---
title: Instalar a atualização no dispositivo Azure Stack GPU pro Edge | Microsoft Docs
description: Descreve como aplicar atualizações usando o portal do Azure e a interface do usuário da Web local para o dispositivo Azure Stack Edge pro GPU e o cluster kubernetes no dispositivo
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/25/2021
ms.author: alkohli
ms.openlocfilehash: ac5ed0e5941c6251d632d029fe4c9f80bbcf12df
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612544"
---
# <a name="update-your-azure-stack-edge-pro-gpu"></a>Atualizar sua GPU do Azure Stack Edge pro 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve as etapas necessárias para instalar a atualização no Azure Stack Edge pro com GPU por meio da interface do usuário da Web local e por meio do portal do Azure. Aplique as atualizações de software ou hotfixes para manter seu Azure Stack dispositivo do Edge pro e o cluster kubernetes associado no dispositivo atualizado.

O procedimento descrito neste artigo foi executado usando uma versão diferente do software, mas o processo permanece o mesmo para a versão atual do software.

> [!IMPORTANT]
> - A atualização **2103** é a atual e corresponde a:
>   - Versão do software do dispositivo- **2.2.1540.2890**
>   - Kubernetes Server versão- **v 1.17.3**
>   - Versão do IoT Edge: **0.1.0-beta13**
>   - Versão do driver de GPU: **460.32.03**
>   - Versão do CUDA: **11,2**
>    
>    Para obter informações sobre as novidades dessa atualização, acesse [notas de versão](azure-stack-edge-gpu-2103-release-notes.md).
> - Para aplicar a atualização 2103, seu dispositivo deve estar executando o 2010. Se você não estiver executando a versão mínima com suporte, verá este erro: o *pacote de atualização não pode ser instalado porque suas dependências não são atendidas*.
> - Essa atualização exige que você aplique duas atualizações sequencialmente. Primeiro, aplique as atualizações de software do dispositivo e, em seguida, as atualizações do kubernetes.
> - Tenha em mente que instalar uma atualização ou um hotfix reinicia seu dispositivo. Esta atualização contém as atualizações de software do dispositivo e as atualizações do kubernetes. Considerando que o Azure Stack Edge pro é um dispositivo de nó único, qualquer e/s em andamento será interrompida e o dispositivo apresentará um tempo de inatividade de até 1,5 horas para a atualização.

Para instalar atualizações em seu dispositivo, primeiro você precisa configurar o local do servidor de atualização. Depois que o servidor de atualização estiver configurado, você poderá aplicar as atualizações por meio da interface do usuário do portal do Azure ou da interface do usuário da Web local.

Cada uma dessas etapas é descrita nas seções a seguir.

## <a name="configure-update-server"></a>Configurar servidor de atualização

1. Na interface do usuário da Web local, vá para **configuração**  >  **servidor de atualização**.
   
    ![Configurar atualizações 1](./media/azure-stack-edge-gpu-install-update/configure-update-server-1.png)

2. Em **Selecionar tipo de servidor de atualização**, na lista suspensa, escolha do servidor de Microsoft Update (padrão) ou Windows Server Update Services.  
   
    Se estiver atualizando do Windows Server Update Services, especifique o URI do servidor. O servidor nesse URI implantará as atualizações em todos os dispositivos conectados a esse servidor.

    ![Configurar atualizações 2](./media/azure-stack-edge-gpu-install-update/configure-update-server-2.png)
    
    O servidor do WSUS é usado para gerenciar e distribuir atualizações por meio de um console de gerenciamento. Um servidor do WSUS também pode ser a fonte de atualização de outros servidores do WSUS na organização. O servidor WSUS que atua como fonte de atualização é chamado de servidor upstream. Em uma implementação do WSUS, pelo menos um servidor do WSUS na rede precisa conseguir se conectar ao Microsoft Update para obter as informações de atualizações disponíveis. Como administrador, você pode determinar, com base na segurança e na configuração da rede, quantos outros servidores WSUS se conectam diretamente ao Microsoft Update.
    
    Para obter mais informações, acesse [Windows Server Update Services (WSUS)](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus)

## <a name="use-the-azure-portal"></a>Use o Portal do Azure

Recomendamos que você instale as atualizações por meio do portal do Azure. O dispositivo verifica automaticamente se há atualizações uma vez por dia. Depois que as atualizações estiverem disponíveis, você verá uma notificação no Portal. Em seguida, você poderá baixar e instalar as atualizações.

> [!NOTE]
> Verifique se o dispositivo está íntegro e se o status é mostrado enquanto **o dispositivo está funcionando bem!** antes de prosseguir com a instalação das atualizações.

1. Quando as atualizações estiverem disponíveis para seu dispositivo, você verá uma notificação. Selecione a notificação ou na barra de comandos superior, **Atualizar dispositivo**. Isso permitirá que você aplique atualizações de software do dispositivo.

    ![Versão do software após a atualização](./media/azure-stack-edge-gpu-install-update/portal-update-1.png)

2. Na folha **atualizações do dispositivo** , verifique se você analisou os termos de licença associados aos novos recursos nas notas de versão.

    Você pode optar por **baixar e instalar** as atualizações ou apenas **baixar** as atualizações. Em seguida, você pode optar por instalar essas atualizações mais tarde.

    ![Versão do software após a atualização 2](./media/azure-stack-edge-gpu-install-update/portal-update-2-a.png)    

    Se você quiser baixar e instalar as atualizações, verifique a opção que as atualizações instalam automaticamente após a conclusão do download.

    ![Versão do software após a atualização 3](./media/azure-stack-edge-gpu-install-update/portal-update-2-b.png)

3. O download das atualizações é iniciado. Você verá uma notificação informando que o download está em andamento.

    ![Versão do software após a atualização 4](./media/azure-stack-edge-gpu-install-update/portal-update-3.png)

    Uma faixa de notificação também é exibida no portal do Azure. Isso indica o progresso do download.

    ![Versão do software após a atualização 5](./media/azure-stack-edge-gpu-install-update/portal-update-4.png)

    Você pode selecionar essa notificação ou selecionar **Atualizar dispositivo** para ver o status detalhado da atualização.

    ![Versão do software após a atualização 6](./media/azure-stack-edge-gpu-install-update/portal-update-5.png)


4. Depois que o download for concluído, a faixa de notificação será atualizada para indicar a conclusão. Se você optar por baixar e instalar as atualizações, a instalação será iniciada automaticamente.

    Se você optar por baixar apenas as atualizações, selecione a notificação para abrir a folha **atualizações do dispositivo** . Selecione **Instalar**.
  
5. Você verá uma notificação de que a instalação está em andamento. O portal também exibe um alerta informativo para indicar que a instalação está em andamento. O dispositivo fica offline e está no modo de manutenção.
   
    ![Versão do software após a atualização 10](./media/azure-stack-edge-gpu-install-update/portal-update-9.png)

6. Como esse é um dispositivo de 1 nó, o dispositivo é reiniciado depois que as atualizações são instaladas. O alerta crítico durante a reinicialização indica que a pulsação do dispositivo foi perdida.

    ![Versão do software após a atualização 11](./media/azure-stack-edge-gpu-install-update/portal-update-10.png)

    Selecione o alerta para ver o evento de dispositivo correspondente.
    
    ![Versão do software após a atualização 12](./media/azure-stack-edge-gpu-install-update/portal-update-11.png)

7. Após a reinicialização, o software do dispositivo terminará de ser atualizado. Depois que a atualização for concluída, você poderá verificar na interface do usuário da Web local que o software do dispositivo está atualizado. A versão do software kubernetes não foi atualizada.

    ![Versão do software após a atualização 13](./media/azure-stack-edge-gpu-install-update/portal-update-12.png)

8. Você verá uma faixa de notificação indicando que as atualizações do dispositivo estão disponíveis. Selecione essa faixa para iniciar a atualização do software kubernetes em seu dispositivo. 

    ![Versão do software após a atualização 13a](./media/azure-stack-edge-gpu-install-update/portal-update-13.png) 


    ![Versão do software após a atualização 14](./media/azure-stack-edge-gpu-install-update/portal-update-14-a.png) 

    Se você selecionar o **dispositivo de atualização** na barra de comandos superior, poderá ver o progresso das atualizações.  

    ![Versão do software após a atualização 15](./media/azure-stack-edge-gpu-install-update/portal-update-14-b.png) 


8. As atualizações de status do dispositivo para **seu dispositivo estão funcionando bem depois que** as atualizações são instaladas. 

    ![Versão do software após a atualização 16](./media/azure-stack-edge-gpu-install-update/portal-update-15.png)

    Vá para a interface do usuário da Web local e vá para a página de **atualização de software** . Verifique se a atualização do kubernetes foi instalada com êxito e se a versão do software reflete isso.

    ![Versão do software após a atualização 17](./media/azure-stack-edge-gpu-install-update/portal-update-16.png)


Depois que o software do dispositivo e as atualizações do kubernetes são instaladas com êxito, a notificação de faixa desaparece. Seu dispositivo agora tem a versão mais recente do software do dispositivo e do kubernetes.


## <a name="use-the-local-web-ui"></a>Usar a interface do usuário da Web local

Há duas etapas ao usar a interface do usuário da Web local:

* Baixe a atualização ou o hotfix
* Instale a atualização ou o hotfix

Cada uma dessas etapas é descrita mais detalhadamente nas seções a seguir.


### <a name="download-the-update-or-the-hotfix"></a>Baixe a atualização ou o hotfix

Execute as etapas a seguir para baixar a atualização. Você pode baixar a atualização do local fornecido pela Microsoft ou do catálogo Microsoft Update.


Execute as etapas a seguir para baixar a atualização do catálogo Microsoft Update.

1. Inicie o navegador e navegue até [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

    ![Pesquisar o catálogo](./media/azure-stack-edge-gpu-install-update/download-update-1.png)

2. Na caixa de pesquisa do catálogo Microsoft Update, insira o número da base de dados de conhecimento (KB) do hotfix ou os termos da atualização que você deseja baixar. Por exemplo, digite **Azure Stack Edge pro** e clique em **Pesquisar**.
   
    A listagem de atualização aparece como **Azure Stack atualização de borda 2103**.
   
    <!--![Search catalog 2](./media/azure-stack-edge-gpu-install-update/download-update-2-b.png)-->

4. Selecione **Baixar**. Há dois pacotes para baixar: KB 4613486 e KB 46134867 que correspondem às atualizações de software do dispositivo (*SoftwareUpdatePackage.exe*) e atualizações Kubernetes (*Kubernetes_Package.exe*), respectivamente. Baixe os pacotes em uma pasta no sistema local. Você também pode copiar a pasta para um compartilhamento de rede que é acessível do dispositivo.

### <a name="install-the-update-or-the-hotfix"></a>Instale a atualização ou o hotfix

Antes da instalação de atualização ou hotfix, certifique-se de que:

 - Você tem a atualização ou hotfix baixado localmente em seu host ou acessível por meio de um compartilhamento de rede.
 - O status do dispositivo é íntegro, conforme mostrado na página **visão geral** da interface do usuário da Web local.

   ![atualizar dispositivo](./media/azure-stack-edge-gpu-install-update/local-ui-update-1.png)

Esse procedimento leva cerca de 20 minutos para ser concluído. Execute as etapas a seguir para instalar a atualização ou o hotfix.

1. Na interface do usuário da Web local, vá para **manutenção**  >  **atualização de software**. Anote a versão do software que você está executando. 
   
   ![atualizar dispositivo 2](./media/azure-stack-edge-gpu-install-update/local-ui-update-2.png)

2. Forneça o caminho para o arquivo de atualização. Você também pode navegar até o arquivo de instalação de atualização, se colocado em um compartilhamento de rede. Selecione o arquivo de atualização de software com o sufixo *SoftwareUpdatePackage.exe* .

   ![atualizar dispositivo 3](./media/azure-stack-edge-gpu-install-update/local-ui-update-3-a.png)

3. Selecione **Aplicar**.

   ![atualizar o dispositivo 4](./media/azure-stack-edge-gpu-install-update/local-ui-update-4.png)

4. Quando a confirmação for solicitada, selecione **Sim** para continuar. Considerando que o dispositivo é um dispositivo de nó único, depois que a atualização é aplicada, o dispositivo é reiniciado e há um tempo de inatividade. 
   
   ![atualizar dispositivo 5](./media/azure-stack-edge-gpu-install-update/local-ui-update-5.png)

5. A atualização será iniciada. Depois que for atualizado com êxito, o dispositivo será reiniciado. A interface do usuário local não estará acessível durante esse tempo.
   
6. Depois que a reinicialização for concluída, você será levado à página **Entrar** . Para verificar se o software do dispositivo foi atualizado, na interface do usuário da Web local, vá para **manutenção**  >  **atualização de software**. Para a versão atual, a versão de software exibida deve ser **Azure Stack borda 2103**. 

   <!--![update device 6](./media/azure-stack-edge-gpu-install-update/local-ui-update-6.png)-->

7. Agora, você atualizará a versão do software kubernetes. Repita as etapas acima. Forneça um caminho para o arquivo de atualização kubernetes com o sufixo *Kubernetes_Package.exe* .  

   <!--![update device](./media/azure-stack-edge-gpu-install-update/local-ui-update-7.png)-->

8. Selecione **aplicar atualização**.

   ![atualizar o dispositivo 7](./media/azure-stack-edge-gpu-install-update/local-ui-update-8.png)

9. Quando a confirmação for solicitada, selecione **Sim** para continuar.

10. Depois que a atualização do kubernetes for instalada com êxito, não haverá alteração no software exibido na   >  **atualização de software** de manutenção.


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [administrar seu Azure Stack Edge pro](azure-stack-edge-manage-access-power-connectivity-mode.md).