---
title: Criar e carregar uma imagem de VM FreeBSD | Microsoft Docs
description: "Saiba como criar e carregar um VHD (disco rígido virtual) que contenha um sistema operacional FreeBSD para criar uma máquina virtual do Azure"
services: virtual-machines-linux
documentationcenter: 
author: thomas1206
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: huishao
ms.openlocfilehash: 0010e01d4333b96696680ec6fbbeee74b17f46a3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-upload-a-freebsd-vhd-to-azure"></a>Criar e carregar um VHD FreeBSD para o Azure
Este artigo mostra como criar e carregar um VHD (disco rígido virtual) que contenha um sistema operacional FreeBSD. Depois de carregá-lo, você pode usá-lo como sua própria imagem para criar uma VM (máquina virtual) no Azure.

> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../../../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Para obter informações sobre como carregar um VHD usando o modelo do Resource Manager, veja [aqui](../upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você tenha os seguintes itens:

* **Uma assinatura do Azure**– Se não tiver uma conta, você poderá criar uma em apenas alguns minutos. Se você tiver uma assinatura do MSDN, confira [Crédito Azure mensal para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Caso contrário, saiba como [criar uma conta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).  
* **Ferramentas do Azure PowerShell**– O módulo do Azure PowerShell deve estar instalado e configurado para que a assinatura do Azure possa ser usada. Para baixar o módulo, consulte [Downloads do Azure](https://azure.microsoft.com/downloads/)(a página pode estar em inglês). Um tutorial que descreve como instalar e configurar o módulo está disponível aqui. Use o cmdlet [Downloads do Azure](https://azure.microsoft.com/downloads/) para carregar o VHD.
* **Sistema operacional FreeBSD instalado em um arquivo .vhd** – É necessário instalar um sistema operacional FreeBSD com suporte em um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd. Por exemplo, você pode usar uma solução de virtualização, como o Hyper-V, para criar o arquivo .vhd e instalar o sistema operacional. Para obter instruções sobre como instalar e usar o Hyper-V, confira [Instalar o Hyper-V e criar uma máquina virtual](http://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Não há suporte para o formato VHDX mais recente no Azure. Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o cmdlet [convert-vhd](https://technet.microsoft.com/library/hh848454.aspx). Além disso, há um [tutorial no MSDN sobre como usar o FreeBSD com o Hyper-V](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx).
>
>

Esta tarefa inclui as cinco etapas a seguir:

## <a name="step-1-prepare-the-image-for-upload"></a>Etapa 1: preparar a imagem para upload
Na máquina virtual na qual o sistema operacional FreeBSD foi instalado, conclua os procedimentos a seguir:

1. Habilitar DHCP.

        # echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
        # service netif restart
2. Habilitar SSH.

    Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização. Ele é habilitado por padrão após a instalação do disco FreeBSD. 
3. Instalar um console serial.

        # echo 'console="comconsole vidconsole"' >> /boot/loader.conf
        # echo 'comconsole_speed="115200"' >> /boot/loader.conf
4. Instalar o sudo.

    A conta raiz está desabilitada no Azure. Isso significa que você precisa utilizar o sudo de um usuário sem privilégios para executar comandos com privilégios elevados.

        # pkg install sudo
   
5. Pré-requisitos para agente do Azure.

        # pkg install python27  
        # pkg install Py27-setuptools  
        # ln -s /usr/local/bin/python2.7 /usr/bin/python   
        # pkg install git
6. Instalar o agente do Azure.

    A versão mais recente do agente do Azure sempre pode ser encontrada no [github](https://github.com/Azure/WALinuxAgent/releases). A versão 2.0.10 + oficialmente dá suporte a FreeBSD 10 e 10.1 e a versão 2.1.4 + (incluindo 2.2.x) oficialmente dá suporte a FreeBSD 10.2 e versões posteriores.

        # git clone https://github.com/Azure/WALinuxAgent.git  
        # cd WALinuxAgent  
        # git tag  
        …
        WALinuxAgent-2.0.16
        …
        v2.1.4
        v2.1.4.rc0
        v2.1.4.rc1

    Para o 2.0, vamos usar 2.0.16 como exemplo:

        # git checkout WALinuxAgent-2.0.16
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  

    Para o 2.1, vamos usar 2.1.4 como exemplo:

        # git checkout v2.1.4
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  
        # ln -sf /usr/local/sbin/waagent2.0 /usr/sbin/waagent2.0

   > [!IMPORTANT]
   > Depois de instalar o agente do Azure, convém verificar se ele está funcionando:
   >
   >

        # waagent -version
        WALinuxAgent-2.1.4 running on freebsd 10.3
        Python: 2.7.11
        # ps auxw | grep waagent
        root   639   0.0  0.5 104620 17520 u0- I    05:17    0:00.20 python /usr/local/sbin/waagent -daemon (python2.7)
        # cat /var/log/waagent.log
7. Desprovisione o sistema.

    Desprovisione o sistema para limpá-lo e torná-lo adequado para o reprovisionamento. O comando a seguir também exclui a última conta de usuário provisionada e os dados associados:

        # echo "y" |  /usr/local/sbin/waagent -deprovision+user  
        # echo  'waagent_enable="YES"' >> /etc/rc.conf

    Agora você pode desligar sua VM.

## <a name="step-2-create-a-storage-account-in-azure"></a>Etapa 2: Criar uma conta de armazenamento no Azure
Você precisa de uma conta de armazenamento no Azure para carregar um arquivo .vhd para que ele possa ser usado para criar uma máquina virtual. Você pode usar o portal clássico do Azure para criar uma conta de armazenamento.

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com).
2. Na barra de comandos, selecione **Novo**.
3. Selecione **Serviços de Dados** > **Armazenamento** > **Criação Rápida**.

    ![Criação rápida de uma conta de armazenamento](./media/freebsd-create-upload-vhd/Storage-quick-create.png)
4. Preencha os campos da seguinte maneira:

   * No campo **URL** , digite um nome de subdomínio para usar na URL da conta de armazenamento. A entrada pode conter de três a 24 letras minúsculas e números. Esse nome se torna o nome do host na URL usada para tratar os recursos de armazenamento do Armazenamento de Blobs do Azure, do Armazenamento de Filas do Azure ou dos recursos do Armazenamento de Tabelas da assinatura.
   * No menu suspenso **Local/Grupo de Afinidades**, escolha o **local ou um grupo de afinidades** para a conta de armazenamento. Um grupo de afinidades permite colocar seus serviços de nuvem e armazenamento no mesmo data center.
   * No campo **Replicação**, decida se quer usar a replicação com **Redundância Geográfica** para a conta de armazenamento. A replicação geográfica é ativada por padrão. Essa opção replica os dados para um local secundário, sem nenhum custo para você, para que o armazenamento efetue o failover para o local se ocorrer uma falha grave no local principal. O local secundário é atribuído automaticamente e não pode ser alterado. Se você precisar de mais controle sobre o local do armazenamento baseado em nuvem devido a requisitos legais ou política organizacional, você pode desligar a replicação geográfica. No entanto, saiba que ao ativar a replicação geográfica posteriormente, você deverá pagar uma taxa de transferência de uma única vez para replicar seus dados existentes para o local secundário. Serviços de armazenamento sem replicação geográfica são oferecidos com desconto. Encontre mais detalhes sobre como gerenciar a replicação geográfica de contas de armazenamento aqui: [Replicação do Armazenamento do Azure](../../../storage/common/storage-redundancy.md).

     ![Insira os detalhes da conta de armazenamento](./media/freebsd-create-upload-vhd/Storage-create-account.png)
5. Escolha **Criar Conta de Armazenamento**. A conta aparece agora em **armazenamento**.

    ![Conta de armazenamento criada com êxito](./media/freebsd-create-upload-vhd/Storagenewaccount.png)
6. Em seguida, crie um contêiner para os seus .vhd carregados. Escolha o nome da conta de armazenamento e selecione **Contêineres**.

    ![Detalhes da conta de armazenamento](./media/freebsd-create-upload-vhd/storageaccount_detail.png)
7. Escolha **Criar um Contêiner**.

    ![Detalhes da conta de armazenamento](./media/freebsd-create-upload-vhd/storageaccount_container.png)
8. No campo **Nome** , digite um nome para seu contêiner. Em seguida, no menu suspenso **Acesso** , selecione o tipo de política de acesso desejado.

    ![Nome do contêiner](./media/freebsd-create-upload-vhd/storageaccount_containervalues.png)

   > [!NOTE]
   > Por padrão, o contêiner é privado e só pode ser acessado pelo proprietário da conta. Para permitir acesso de leitura público dos blobs no contêiner, mas não das propriedades ou metadados do contêiner, use a opção **Blob Público** . Para permitir o acesso de leitura público completo do contêiner e de blobs, use a opção **Contêiner Público** .
   >
   >

## <a name="step-3-prepare-the-connection-to-azure"></a>Etapa 3: Preparar a conexão com o Azure
Para poder carregar um arquivo .vhd, você precisa estabelecer uma conexão segura entre seu computador e sua assinatura do Azure. No entanto, você pode usar o método do Azure AD (Azure Active Directory) ou o método de certificado para realizar esse tipo de trabalho.

### <a name="use-the-azure-ad-method-to-upload-a-vhd-file"></a>Usar o método do Azure AD para carregar um arquivo .vhd
1. Abra o console do PowerShell do Azure.
2. Digite o seguinte comando:   
    `Add-AzureAccount`

    Esse comando abre uma janela de entrada para que você possa entrar com sua conta corporativa ou de estudante.

    ![Janela do PowerShell](./media/freebsd-create-upload-vhd/add_azureaccount.png)
3. O Azure autentica e salva as informações de credenciais. Em seguida, ele fecha a janela.

### <a name="use-the-certificate-method-to-upload-a-vhd-file"></a>Usar o método de certificado para carregar um arquivo .vhd
1. Abra o console do PowerShell do Azure.
2. Digite: `Get-AzurePublishSettingsFile`.
3. Uma janela de navegador será aberta e baixará automaticamente o arquivo .publishsettings. Esse arquivo contém informações e um certificado para sua assinatura do Azure.

    ![Procurar página de download](./media/freebsd-create-upload-vhd/Browser_download_GetPublishSettingsFile.png)
4. Salve o arquivo .publishsettings.
5. Digite: `Import-AzurePublishSettingsFile <PathToFile>`, em que `<PathToFile>` é o caminho completo para o arquivo .publishsettings.

   Para obter mais informações, consulte [Introdução aos cmdlets do Azure](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx).

   Para saber mais sobre como instalar e configurar o PowerShell, confira [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

## <a name="step-4-upload-the-vhd-file"></a>Etapa 4: carregar o arquivo .vhd
Quando carrega o arquivo .vhd, você pode colocá-lo em qualquer lugar em seu Armazenamento de Blobs. Veja a seguir alguns termos que você usará ao carregar o arquivo:

* **BlobStorageURL** é a URL da conta de armazenamento que você criou na Etapa2.
* **YourImagesFolder** é o contêiner dentro do Armazenamento de Blobs em que você deseja armazenar as imagens.
* **VHDName** é o rótulo que aparece no portal clássico do Azure para identificar o disco rígido virtual.
* **PathToVHDFile** é o caminho completo e o nome do arquivo .vhd.

Na janela PowerShell do Azure que você usou na etapa anterior, digite:

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## <a name="step-5-create-a-vm-with-the-uploaded-vhd-file"></a>Etapa 5: Criar uma VM com o arquivo .vhd carregado
Depois de carregar o arquivo .vhd, você pode adicioná-lo como uma imagem à lista de imagens personalizadas associadas à sua assinatura e criar uma máquina virtual com essa imagem personalizada.

1. Na janela PowerShell do Azure que você usou na etapa anterior, digite:

        Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

   > [!NOTE]
   > Use o Linux como o tipo de sistema operacional. A versão atual do Azure PowerShell aceita apenas "Linux" ou "Windows" como parâmetro.
   >
   >
2. Após concluir as etapas anteriores, a nova imagem será listada ao escolher a guia **Imagens** no portal clássico do Azure.  

    ![Escolher uma imagem](./media/freebsd-create-upload-vhd/addfreebsdimage.png)
3. Crie uma máquina virtual da galeria. Essa nova imagem agora está disponível em **Minhas imagens**.
4. Escolha a nova imagem. Em seguida, percorra as instruções para configurar um nome de host, senha, chave SSH etc.

    ![Imagem personalizada](./media/freebsd-create-upload-vhd/createfreebsdimageinazure.png)
5. Após a conclusão do provisionamento, você verá sua VM do FreeBSD em execução no Azure.

    ![Imagem do FreeBSD no azure](./media/freebsd-create-upload-vhd/freebsdimageinazure.png)
