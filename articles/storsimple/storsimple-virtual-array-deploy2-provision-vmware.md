---
title: Provisionar um StorSimple Virtual Array no VMware | Microsoft Docs
description: Esse segundo tutorial sobre a série de implantação da StorSimple Virtual Array envolve o provisionamento de um dispositivo virtual no VMware.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/11/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c9fe597957057dc61da5c2b1cf6f9216711764a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61418559"
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>Implantar a StorSimple Virtual Array - Provisionar no VMware
![](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Visão geral
Este tutorial descreve como provisionar e conectar uma Matriz Virtual StorSimple em um sistema de host que executa o VMware ESXi 5.0, 5.5, 6.0 ou 6.5. Este artigo se aplica à implantação de Matrizes Virtuais StorSimple no portal do Azure, bem como na Nuvem de Governo do Microsoft Azure.

Você precisa de privilégios de administrador para provisionar e conectar-se a um dispositivo virtual. O provisionamento e a configuração inicial podem levar cerca de 10 minutos para ser concluídos.

## <a name="provisioning-prerequisites"></a>Pré-requisitos de provisionamento
Os pré-requisitos para provisionar um dispositivo virtual em um sistema de host que executa o VMware ESXi 5.0, 5.5, 6.0 ou 6.5 são os seguintes.

### <a name="for-the-storsimple-device-manager-service"></a>Para implantar o serviço Gerenciador de Dispositivos StorSimple
Antes de começar, verifique se:

* Você concluiu todas as etapas em [Preparar o portal para o StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).
* Você baixou a imagem do dispositivo virtual para o VMware no portal do Azure. Para obter mais informações, confira a **Etapa 3: baixar a imagem do dispositivo virtual** do [guia Preparar o portal da Matriz Virtual StorSimple](storsimple-virtual-array-deploy1-portal-prep.md).

### <a name="for-the-storsimple-virtual-device"></a>Para o dispositivo virtual StorSimple
Antes de implantar um dispositivo virtual, verifique se:

* Você tem acesso a um sistema de host que executa o Hyper-V (2008 R2 ou superior) que pode ser usado para provisionar um dispositivo.
* O sistema de host é capaz de dedicar os recursos a seguir para provisionar seu dispositivo virtual:

  * Um mínimo de quatro núcleos.
  * Pelo menos 8 GB de RAM. Se você planeja configurar a matriz virtual como servidor de arquivos, 8 GB oferece suporte a menos de 2 milhões de arquivos. Você precisa de 16 GB de RAM para dar suporte a 2-4 milhões de arquivos.
  * Uma interface de rede.
  * Um disco virtual de 500 GB para dados do sistema.

### <a name="for-the-network-in-datacenter"></a>Para a rede no datacenter
Antes de começar, verifique se:

* Você revisou os requisitos de rede para implantar um dispositivo virtual StorSimple e configurou a rede de datacenter de acordo com os requisitos. 

## <a name="step-by-step-provisioning"></a>Provisionamento passo a passo
Para provisionar e se conectar a um dispositivo virtual, você precisa executar as seguintes etapas:

1. Verificar se o sistema de host tem recursos suficientes para cumprir os requisitos mínimos de dispositivos virtuais.
2. Provisionar um dispositivo virtual no seu hipervisor.
3. Iniciar o dispositivo virtual e obter o endereço IP.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Etapa 1: Verifique se o sistema host cumpre os requisitos mínimos de dispositivo virtual
Para criar um dispositivo virtual, você precisará de:

* Acesso a um sistema host com VMware ESXi 5.0, 5.5, 6.0 ou 6.5.
* Cliente VMware vSphere em seu sistema para gerenciar o host ESXi.

  * Um mínimo de quatro núcleos.
  * Pelo menos 8 GB de RAM. Se você planeja configurar a matriz virtual como servidor de arquivos, 8 GB oferece suporte a menos de 2 milhões de arquivos. Você precisa de 16 GB de RAM para dar suporte a 2-4 milhões de arquivos.
  * Uma interface de rede conectada à rede capaz de rotear o tráfego para a Internet. A largura de banda de Internet mínima deve ser de 5 Mbps para permitir o funcionamento ideal do dispositivo.
  * Um disco virtual de 500 GB para dados.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Etapa 2: Provisionar um dispositivo virtual no hipervisor
Execute as etapas a seguir para provisionar um dispositivo virtual no seu hipervisor.

1. Copie a imagem do dispositivo virtual no seu sistema. Você baixou essa imagem virtual por meio do portal do Azure.

   1. Certifique-se de ter baixado o arquivo de imagem mais recente. Se você baixou a imagem anteriormente, baixe-a novamente para garantir que você tenha a imagem mais recente. A imagem mais recente tem dois arquivos (em vez de um).
   2. Anote o local em que você copiou a imagem, pois ela será usada posteriormente no procedimento.

2. Faça logon no servidor ESXi usando o cliente vSphere. Você precisa ter privilégios de administrador para criar uma máquina virtual.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. No cliente vSphere, na seção de estoque no painel esquerdo, selecione o servidor ESXi.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. Carregar o VMDK para o servidor ESXi. Navegue até a guia **Configuração** no painel direito. Em **Hardware**, selecione **Armazenamento**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. No painel direito, em **Repositórios de Dados**, selecione o repositório de dados no qual deseja carregar o VMDK. O repositório de dados deve ter espaço livre suficiente para os discos de sistema operacional e de dados.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Clique com o botão direito do mouse e selecione **Procurar no Repositório de Dados**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. Uma janela **Navegador de Repositório de Dados** é exibida.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. Na barra de ferramentas, clique no ícone ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png) para criar uma nova pasta. Especifique o nome da pasta e anote-o. Você usará posteriormente este nome de pasta ao criar uma máquina virtual (melhor prática recomendada). Clique em **OK**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. A nova pasta aparece no painel à esquerda do **Navegador do Repositório de Dados**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. Clique no ícone Carregar ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png) e selecione **Carregar Arquivo**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. Procure e aponte para os arquivos VMDK que você baixou. Existem dois itens. Selecione um arquivo para carregar.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. Clique em **Abrir**. O upload do arquivo VMDK para o repositório de dados especificado é iniciado. Pode levar vários minutos para que o arquivo seja carregado.
13. Quando o upload for concluído, você verá o arquivo no repositório de dados na pasta que você criou.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    Agora carregue o segundo arquivo VMDK para o mesmo repositório de dados.
14. Retorne à janela do cliente vSphere. Com o servidor ESXi selecionado, clique com botão direito do mouse e selecione **Nova Máquina Virtual**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. Uma janela **Criar nova máquina Virtual** será exibida. Sobre a página **Configuração**, selecione a opção **Personalizada**. Clique em **Avançar**.
    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. Na página **Nome e Local** , especifique o nome da máquina virtual. Esse nome deve corresponder ao nome da pasta (melhor prática recomendada) que você especificou anteriormente na Etapa 8.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. Na página **Armazenamento** selecione um repositório de dados que você deseja usar para provisionar sua VM.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. Na página **Versão da máquina virtual**, selecione **Versão da máquina virtual: 8**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. Na página **Sistema Operacional Convidado**, selecione o **Sistema Operacional Convidado** como **Windows**. Para **Versão**, na lista suspensa, selecione **Microsoft Windows Server 2012 (64 bits)**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. Na página **CPUs**, ajuste o **Número de soquetes virtuais** e o **Número de núcleos por soquete virtual** para que o **Número total de núcleos** seja 4 (ou mais). Clique em **Avançar**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. Na página **Memória** , especifique 8 GB (ou mais) de RAM. Clique em **Avançar**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. Na página **Rede** especifique o número de interfaces de rede. O requisito mínimo é uma interface de rede.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. Na página **Controlador SCSI**, aceite o padrão **Controlador SAS de Lógica LSI**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. Na página **Selecionar um Disco**, escolha **Usar um disco virtual existente**. Clique em **Avançar**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. Na página **Selecionar Disco Existente**, em **Caminho de Arquivo de Disco**, clique em **Procurar**. Isso abre uma caixa de diálogo **Procurar em Repositórios de Dados** . Navegue até o local em que você carregou o VMDK. Você verá agora somente um arquivo no repositório de dados, já que os dois arquivos que você carregou inicialmente foram mesclados. Selecione o arquivo e clique em **OK**. Clique em **Avançar**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. Na página **Opções Avançadas**, aceite o padrão e clique em **Próximo**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. Na página **Pronto para Concluir** , examine todas as configurações associadas à nova máquina virtual. Verifique **Editar as configurações de máquina virtual antes da conclusão**. Clique em **Continuar**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. Na página **Propriedades de Máquinas Virtuais**, na guia **Hardware**, localize o hardware de dispositivo. Selecione **Novo Disco Rígido**. Clique em **Adicionar**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. Você verá uma janela **Adicionar Hardware**. Na página **Tipo de Dispositivo**, em **Escolha o tipo de dispositivo que você deseja adicionar**, selecione **Disco Rígido** e clique em **Próximo**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. Na página **Selecionar um Disco**, escolha **Criar um novo disco virtual**. Clique em **Avançar**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. Na página **Criar um Disco**, altere o **Tamanho do Disco** para 500 GB (ou mais). Embora 500 GB seja o requisito mínimo, você sempre poderá provisionar um disco maior. Observe que, depois de provisionado, você não poderá expandir ou reduzir o disco. Para obter mais informações sobre o tamanho do disco a ser provisionado, examine a seção sobre dimensionamento no [documento sobre melhores práticas](storsimple-ova-best-practices.md). Em **Provisionamento de Disco**, selecione **Provisionamento Dinâmico**. Clique em **Avançar**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. Na página **Opções Avançadas** , aceite o padrão.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. Na página **Pronto para Concluir** , examine as opções de disco. Clique em **Concluir**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Retorne à página Propriedades de Máquina Virtual. Um novo disco rígido é adicionado à sua máquina virtual. Clique em **Concluir**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. Com sua máquina virtual selecionada no painel direito, navegue até a guia **Resumo** . Examine as configurações para sua máquina virtual.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

Sua máquina virtual está agora provisionada. A próxima etapa é ligar esse computador e obter o endereço IP.

> [!NOTE]
> É recomendável que você não instale as ferramentas do VMware na matriz virtual (como provisionado acima). A instalação das ferramentas do VMware resultará em uma configuração sem suporte.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Etapa 3: Iniciar o dispositivo virtual e obter o IP
Execute as etapas a seguir para iniciar o dispositivo virtual e conectar-se a ele.

#### <a name="to-start-the-virtual-device"></a>Para iniciar o dispositivo virtual
1. Inicie o dispositivo virtual. No vSphere Configuration Manager, no painel esquerdo, selecione seu dispositivo e clique com o botão direito do mouse para abrir o menu de contexto. Selecione **Ligar/Desligar** e, em seguida, selecione **Ligar**. Isso deve ligar sua máquina virtual. Você pode exibir o status no painel inferior **Tarefas Recentes** do cliente vSphere.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. As tarefas de configuração levarão alguns minutos para serem concluídas. Quando o dispositivo estiver em execução, navegue até a guia **Console** . Envie Ctrl+Alt+Delete para fazer logon no dispositivo. Como alternativa, você pode apontar o cursor na janela do console e pressionar Ctrl+Alt+Insert. O usuário padrão é *StorSimpleAdmin* e a senha padrão é *Password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Por motivos de segurança, a senha do administrador do dispositivo expira no primeiro logon. Você será solicitado a alterar a senha.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Insira uma senha que contenha pelo menos oito caracteres. A senha deve conter três dentre estas quatro opções: caracteres maiúsculos, minúsculos, numéricos e especiais. Insira a senha novamente para confirmá-la. Você será notificado de que a senha foi alterada.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. Depois que a senha for alterada com êxito, o dispositivo virtual poderá ser reiniciado. Aguarde a conclusão da reinicialização. O console do Windows PowerShell do dispositivo poderá ser exibido junto com uma barra de progresso.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. As etapas 6 a 8 se aplicam somente na inicialização de um ambiente não DHCP. Se você estiver em um ambiente DHCP, ignore essas etapas e vá para a etapa 9. Caso tenha inicializado seu dispositivo em um ambiente não DHCP, você verá a tela a seguir.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   Em seguida, configure a rede.
7. Use o comando `Get-HcsIpAddress` para listar as interfaces de rede habilitadas em seu dispositivo virtual. Se o dispositivo tiver uma única interface de rede habilitada, o nome padrão atribuído a ela é `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. Use o cmdlet `Set-HcsIpAddress` para configurar a rede. Um exemplo é mostrado abaixo:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. Depois que a configuração inicial for concluída e o dispositivo for inicializado, você verá o texto da faixa do dispositivo. Anote o endereço IP e a URL exibida no texto do banner para gerenciar o dispositivo. Você usará esse endereço IP para se conectar à interface do usuário da Web do seu dispositivo virtual e concluir a configuração local e o registro.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. (Opcional) Execute esta etapa somente se você estiver implantando seu dispositivo na Nuvem do governo. Agora, você habilitará o modo FIPS (Federal Information Processing Standard) dos Estados Unidos em seu dispositivo. O padrão FIPS 140 define algoritmos criptográficos aprovados para uso por sistemas de computador do governo federal dos EUA para a proteção de dados confidenciais.

    1. Para habilitar o modo FIPS, execute o seguinte cmdlet:

        `Enable-HcsFIPSMode`
    2. Reinicialize o dispositivo após ter habilitado o modo FIPS para que as validações criptográficas tenham efeito.

       > [!NOTE]
       > Você pode habilitar ou desabilitar o modo FIPS em seu dispositivo. Alternar o dispositivo entre o modo FIPS e não FIPS não tem suporte.
       >
       >

Se o dispositivo não cumprir os requisitos mínimos de configuração, você verá um erro no texto da faixa (mostrado abaixo). Será necessário modificar a configuração do dispositivo para que ele tenha recursos adequados para cumprir os requisitos mínimos. Em seguida, você pode reiniciar e conectar-se ao dispositivo. Veja os requisitos mínimos de configuração na [Etapa 1: verificar se o sistema de host cumpre os requisitos mínimos de dispositivo virtual](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

Caso observe algum outro erro durante a configuração inicial usando a interface do usuário da Web local, veja os seguintes fluxos de trabalho:

* Execute testes de diagnóstico para [solucionar problemas na configuração da interface do usuário da Web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Gere um pacote de log e exiba os arquivos de log](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Próximas etapas
* [Configurar sua StorSimple Virtual Array como um servidor de arquivos](storsimple-virtual-array-deploy3-fs-setup.md)
* [Configurar sua StorSimple Virtual Array como um servidor iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)
