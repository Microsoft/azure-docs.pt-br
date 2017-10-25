---
title: "Preparação do portal para o StorSimple Virtual Array | Microsoft Docs"
description: "O primeiro tutorial para implantar o StorSimple Array Virtual envolve a preparação do portal do Azure"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 68a4cfd3-94c9-46cb-805c-46217290ce02
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3d0801053721f98ce7a2b0fcbe3c65da8dbdd8d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-storsimple-virtual-array---prepare-the-azure-portal"></a>Implantar o StorSimple Virtual Array – preparar o portal do Azure

![](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)
## <a name="overview"></a>Visão geral

É o primeiro artigo da série de tutoriais de implantação necessários para implantar completamente sua matriz virtual como um servidor de arquivos ou um servidor iSCSI usando o modelo do Resource Manager. Este artigo descreve a preparação necessária para criar e configurar o serviço Gerenciador de Dispositivos do StorSimple antes do provisionamento de uma matriz virtual. Também tem links para uma lista de verificação da configuração da implantação e para pré-requisitos de configuração.

Você precisará de privilégios de administrador para concluir o processo de instalação e configuração. Recomenda-se que você examine a lista de verificação de configuração da implantação antes de começar. A preparação do portal leva menos de 10 minutos.

As informações publicadas neste artigo se aplicam à implantação de Matrizes Virtuais StorSimple no portal do Azure e na Nuvem do Microsoft Azure Governamental.

### <a name="get-started"></a>Introdução
O fluxo de trabalho de implantação consiste em preparação do portal, provisionamento de uma matriz virtual no seu ambiente virtualizado e conclusão da instalação. Para começar com a implantação do StorSimple Virtual Array como um servidor de arquivos ou um servidor iSCSI, você precisará consultar os seguintes recursos tabulados.

#### <a name="deployment-articles"></a>Artigos sobre implantação

Consulte os artigos a seguir na sequência prescrita para implantar sua Matriz Virtual StorSimple.

| **#** | **Nesta etapa** | **Para fazer isso...** | **Use estes documentos.** |
| --- | --- | --- | --- |
| 1. |**Configurar o portal do Azure** |Crie e configure o serviço Gerenciador de Dispositivos StorSimple antes do provisionamento de uma Matriz Virtual StorSimple. |[Preparar o portal](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**Provisionar o Virtual Array** |Para o Hyper-V, provisione e conecte-se a uma Matriz Virtual StorSimple em um sistema de host que executa o Hyper-V no Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2. <br></br> <br></br> Para o VMware, provisione e se conecte à Matriz Virtual StorSimple local em um sistema de host que executa o VMware ESXi 5.5 e posterior.<br></br> |[Provisionar uma matriz virtual no Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [Provisionar uma matriz virtual no VMware](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**Configurar o Virtual Array** |Para seu servidor de arquivos, execute a configuração inicial, registre seu servidor de arquivos do StorSimple e conclua a configuração do dispositivo. Em seguida, é possível provisionar compartilhamentos SMB. <br></br> <br></br> Para o servidor iSCSI, realize a configuração inicial, registre seu servidor iSCSI do StorSimple e conclua a configuração do dispositivo. Em seguida, é possível provisionar volumes iSCSI. |[Configurar a matriz virtual como um servidor de arquivos](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[Configurar a matriz virtual como um servidor iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md) |

Agora, você pode começar a configurar o portal do Azure.

## <a name="configuration-checklist"></a>Lista de verificação da configuração

A lista de verificação da configuração a seguir descreve as informações que você precisa coletar antes de configurar o software em sua Matriz Virtual StorSimple. Preparar essas informações antecipadamente ajuda a simplificar o processo de implantação do dispositivo StorSimple em seu ambiente. Dependendo de como a Matriz Virtual StorSimple será implantada, como um servidor de arquivos ou um servidor iSCSI, você precisará de uma das listas de verificação a seguir.

* Baixe a [Lista de verificação da configuração do servidor de arquivos do StorSimple Virtual Array](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).
* Baixe a [Lista de verificação da configuração do servidor iSCSI do StorSimple Virtual Array](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## <a name="prerequisites"></a>Pré-requisitos

Aqui você encontra os pré-requisitos de configuração para seu serviço Gerenciador de Dispositivos StorSimple, sua Matriz Virtual StorSimple e a rede de datacenter.

### <a name="for-the-storsimple-device-manager-service"></a>Para implantar o serviço Gerenciador de Dispositivos StorSimple

Antes de começar, verifique se:

* Você tem sua conta da Microsoft com credenciais de acesso.
* Você tem sua conta de armazenamento do Microsoft Azure com credenciais de acesso.
* Sua assinatura do Microsoft Azure deve estar habilitada para o serviço Gerenciador de Dispositivos StorSimple.

### <a name="for-the-storsimple-virtual-array"></a>Usar a matriz virtual StorSimple

Antes de implantar um dispositivo virtual, verifique se:

* Você tem acesso a um sistema de host que executa o Hyper-V no Windows Server 2008 R2 ou posterior ou VMware (ESXi 5.5 ou posterior) que pode ser usado para provisionar um dispositivo.
* O sistema de host é capaz de dedicar os recursos abaixo para provisionar seu dispositivo virtual:
  
  * Um mínimo de quatro núcleos.
  * Pelo menos 8 GB de RAM. Se você planeja configurar a matriz virtual como servidor de arquivos, 8 GB oferece suporte a 2 milhões de arquivos. Você precisa de 16 GB de RAM para dar suporte a 2-4 milhões de arquivos.
  * Uma interface de rede.
  * Um disco virtual de 500 GB para dados do sistema.

### <a name="for-the-datacenter-network"></a>Para a rede de datacenter

Antes de começar, verifique se:

* A rede em seu datacenter está configurada de acordo com os requisitos de rede para seu dispositivo StorSimple. Para obter mais informações, veja os [Requisitos do sistema do StorSimple Virtual Array](storsimple-ova-system-requirements.md).
* Sua Matriz Virtual StorSimple tem uma largura de banda de Internet dedicada de 5 Mbps (ou mais) sempre disponível. Essa largura de banda não deve ser compartilhada com outros aplicativos.

## <a name="step-by-step-preparation"></a>Preparação passo a passo

Use as seguintes instruções passo a passo para preparar seu portal para o serviço Gerenciador de Dispositivos StorSimple.

## <a name="step-1-create-a-new-service"></a>Etapa 1: Criar um novo serviço

Uma única instância do serviço Gerenciador de Dispositivos StorSimple pode gerenciar várias Matrizes Virtuais StorSimple. Execute as etapas a seguir para criar uma nova instância do serviço Gerenciador de Dispositivos StorSimple. Se tiver um serviço Gerenciador de Dispositivos StorSimple existente para gerenciar as matrizes virtuais, ignore esta etapa e vá para a [Etapa 2: obter a chave de registro do serviço](#step-2-get-the-service-registration-key).

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

> [!IMPORTANT]
> Se você não ativou a criação automática de uma conta de armazenamento com seu serviço, você precisará criar pelo menos uma conta de armazenamento depois que você criou com êxito um serviço.
> 
> * Se você não tiver criado uma conta de armazenamento automaticamente, vá para [Configurar uma nova conta de armazenamento para o serviço](#optional-step-configure-a-new-storage-account-for-the-service) para obter instruções detalhadas.
> * Se você habilitou a criação automática de uma conta de armazenamento, vá para [Etapa 2: Obter a chave de registro do serviço](#step-2-get-the-service-registration-key).
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>Etapa 2: Obter a chave de registro do serviço

Depois que o serviço Gerenciador de Dispositivos StorSimple estiver em execução, será necessário obter a chave de registro do serviço. Essa chave é usada para registrar e conectar o seu dispositivo StorSimple com o serviço.

Execute as etapas a seguir no [portal do Azure](https://portal.azure.com/).

[!INCLUDE [storsimple-virtual-array-get-service-registration-key](../../includes/storsimple-virtual-array-get-service-registration-key.md)]

> [!NOTE]
> A chave de registro de serviço é usada para registrar todos os dispositivos do Gerenciador de Dispositivos StorSimple que precisam se registrar no serviço Gerenciador de Dispositivos StorSimple.
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>Etapa 3: baixar a imagem da matriz virtual

Depois que tiver a chave de registro de serviço, você precisará baixar a imagem da matriz virtual apropriada para provisionar uma matriz virtual no sistema de host. As imagens da matriz virtual são específicas do sistema operacional e podem ser baixadas na página de Início Rápido no portal do Azure.

> [!IMPORTANT]
> O software em execução na matriz virtual StorSimple só pode ser usado com o serviço Gerenciador StorSimple.
> 
> 

Execute as etapas a seguir no [portal do Azure](https://portal.azure.com/).

#### <a name="to-get-the-virtual-array-image"></a>Para obter a imagem da matriz virtual

1. Faça logon no [Portal do Azure](https://portal.azure.com/). 
2. No portal do Azure, clique em **Procurar > Gerenciadores de Dispositivos StorSimple**.
3. Selecione um serviço Gerenciador de Dispositivos StorSimple existente. Na folha **Gerenciador de Dispositivos StorSimple**, clique em **Início Rápido**. 
4. Clique no link correspondente à imagem que você deseja baixar do Centro de Download da Microsoft. Os arquivos de imagem têm aproximadamente 4,8 GB.
   
   * VHDX para Hyper-V no Windows Server 2012 e posterior
   * VHD para Hyper-V no Windows Server 2008 R2 e posterior
   * VMDK para VMWare ESXi 5.5 e posterior
5. Baixe e descompacte o arquivo em uma unidade local, observando onde o arquivo descompactado está localizado.

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Etapa opcional: Configurar uma nova conta de armazenamento para o serviço

Esta é uma etapa opcional e deve ser executada somente se você não habilitou a criação automática de uma conta de armazenamento com seu serviço.

Se precisar criar uma conta de armazenamento do Azure em uma região diferente, veja [Como criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account) para obter instruções passo a passo.

Execute as etapas a seguir no [Portal do Azure](https://ms.portal.azure.com/) na página do serviço Gerenciador de Dispositivos StorSimple para adicionar uma conta de armazenamento do Microsoft Azure existente.

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Para adicionar uma credencial de conta de armazenamento com a mesma assinatura do Azure como o serviço Gerenciador de Dispositivos

1. Navegue até seu serviço Gerenciador de Dispositivos, selecione e clique duas vezes nele. Isso abre a folha **Visão geral**.
2. Selecione **Credenciais da conta de armazenamento** na seção **Configuração**.
3. Clique em **Adicionar**.
4. Na folha **Adicionar uma conta de armazenamento**, faça o seguinte:
   
    1. Para **Assinatura**, selecione **Atual**.
   
    2. Forneça o nome da sua conta de armazenamento do Azure.
   
    3. Selecione **Habilitar** para criar um canal seguro para comunicação de rede entre o dispositivo StorSimple e a nuvem. Selecione **Desabilitar** somente se você estiver operando em uma nuvem privada.
   
    4. Clique em **Adicionar**. Você será notificado depois que a conta de armazenamento tiver sido criada com êxito.<br></br>
   
     ![Adicionar uma credencial de conta de armazenamento existente](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>Próxima etapa

A próxima etapa é provisionar uma máquina virtual para a matriz virtual StorSimple. Dependendo do sistema operacional host, consulte as instruções detalhadas em:

* [Provisionar um StorSimple Virtual Array no Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [Provisionar um StorSimple Virtual Array no VMware](storsimple-virtual-array-deploy2-provision-vmware.md)

