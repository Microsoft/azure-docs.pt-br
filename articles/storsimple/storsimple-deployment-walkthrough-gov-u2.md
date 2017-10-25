---
title: Implantar o dispositivo StorSimple no Portal do Governo | Microsoft Docs
description: "Descreve as etapas e as práticas recomendadas para a implantação do dispositivo e do serviço StorSimple Atualização 2 no Portal do Azure Governamental."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 5277538c-797e-4e8e-b613-31b5c10cf5a9
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/16/2016
ms.author: v-sharos
ms.openlocfilehash: 0b22dcdfc0432533b286e70d130bfe2ee2db92b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-your-on-premises-storsimple-device-in-the-government-portal-update-2"></a>Implantar seu dispositivo StorSimple local no Portal do governo (Atualização 2)
[!INCLUDE [storsimple-version-selector-deploy-gov](../../includes/storsimple-version-selector-deploy-gov.md)]

## <a name="overview"></a>Visão geral
Bem-vindo à implantação do dispositivo Microsoft Azure StorSimple. Esses tutoriais de implantação aplicam-se à Série StorSimple 8000 executando o Software de atualização 2 no Portal do Azure Governamental. Esta série de tutoriais inclui uma lista de verificação de configuração, uma lista de pré-requisitos de configuração e etapas de configuração detalhadas para seu dispositivo StorSimple.

As informações nesses tutoriais pressupõem que você revisou as precauções de segurança e desembalou, colocou seu dispositivo StorSimple em um rack e instalou os cabos. Se você ainda precisa executar essas tarefas, comece com a revisão das [precauções de segurança](storsimple-safety.md). Siga as instruções específicas do dispositivo para desempacotar, montar em rack e cabear o dispositivo.

* [Desembalar, montar em rack e cabear o 8100](storsimple-8100-hardware-installation.md)
* [Desembalar, montar em rack e cabear o 8600](storsimple-8600-hardware-installation.md)

Você precisará de privilégios de administrador para concluir o processo de instalação e configuração. Recomenda-se que você leia a lista de verificação de configuração antes de começar. O processo de implantação e configuração pode levar algum tempo para ser concluído.

> [!NOTE]
> As informações de implantação do StorSimple publicadas no site do Microsoft Azure se aplicam apenas aos dispositivos da série StorSimple 8000. Para obter informações completas sobre os dispositivos da série 7000, vá para: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Para obter informações de implantação da série 7000, consulte o [Guia de Início Rápido do Sistema StorSimple](http://onlinehelp.storsimple.com/111_Appliance/).
> 
> 

## <a name="deployment-steps"></a>Etapas de implantação.
Execute estas etapas necessárias para configurar o dispositivo StorSimple e conectá-lo ao serviço StorSimple Manager. Além das etapas necessárias, há etapas e procedimentos opcionais que talvez seja necessário concluir durante a implantação. As instruções passo a passo de implantação indicam quando você deve executar cada uma destas etapas opcionais.

| Etapa | Descrição |
| --- | --- |
| **PRÉ-REQUISITOS** |Eles precisam ser concluídos na preparação para a próxima implantação. |
| [Lista de verificação da configuração da implantação](#deployment-configuration-checklist) |Use essa lista de verificação para coletar e registrar informações antes e durante a implantação. |
| [Pré-requisitos de implantação](#deployment-prerequisites) |Eles validam que o ambiente está pronto para implantação. |
|  | |
| **IMPLANTAÇÃO PASSO A PASSO** |Essas etapas são necessárias para implantar o dispositivo StorSimple na produção. |
| [Etapa 1: Criar um novo serviço](#step-1-create-a-new-service) |Configure o armazenamento e o gerenciamento de nuvem para o dispositivo StorSimple. *Ignore esta etapa se você tem um serviço existente para outros dispositivos StorSimple*. |
| [Etapa 2: Obter a chave de registro do serviço](#step-2-get-the-service-registration-key) |Use essa chave para registrar e conectar o dispositivo StorSimple ao serviço de gerenciamento. |
| [Etapa 3: Configurar e registrar o dispositivo por meio do Windows PowerShell para StorSimple](#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |Conecte o dispositivo à sua rede e registre-o com o Azure para concluir a instalação usando o serviço de gerenciamento. |
| [Etapa 4: Concluir a instalação mínima do dispositivo](#step-4-complete-minimum-device-setup) </br>Opcional: atualizar o dispositivo StorSimple. |Use o serviço de gerenciamento para concluir a instalação do dispositivo e habilitá-lo para fornecer armazenamento. |
| [Etapa 5: Criar um contêiner de volume](#step-5-create-a-volume-container) |Crie um contêiner para provisionar volumes. Um contêiner de volume tem a conta de armazenamento, largura de banda e configurações de criptografia para todos os volumes contidos nele. |
| [Etapa 6: Criar um volume](#step-6-create-a-volume) |Provisione volumes de armazenamento no dispositivo StorSimple para seus servidores. |
| [Etapa 7: Montar, inicializar e formatar um volume](#step-7-mount-initialize-and-format-a-volume) </br>Opcional: configurar o MPIO. |Conecte os servidores ao armazenamento iSCSI fornecido pelo dispositivo. Opcionalmente, configure o MPIO para garantir que os servidores possam tolerar a falha de link, rede e interface. |
| [Etapa 8: Fazer um backup](#step-8-take-a-backup) |Configure a política de backup para proteger seus dados |
|  | |
| **OUTROS PROCEDIMENTOS** |Talvez seja necessário consultar esses procedimentos conforme você implantar sua solução. |
| [Configurar uma nova conta de armazenamento para o serviço](#configure-a-new-storage-account-for-the-service) | |
| [Use o PuTTY para conectar-se ao console serial do dispositivo](#use-putty-to-connect-to-the-device-serial-console) | |
| [Verificar e aplicar atualizações](#scan-for-and-apply-updates) | |
| [Obter o IQN de um host do Windows Server](#get-the-iqn-of-a-windows-server-host) | |
| [Criar um backup manual](#create-a-manual-backup) | |
| [Configurar o MPIO](#configure-mpio) | |

## <a name="deployment-configuration-checklist"></a>Lista de verificação da configuração da implantação
Antes de implantar seu dispositivo StorSimple, você precisará coletar informações para configurar o software em seu dispositivo. Preparar algumas dessas informações antecipadamente ajudará a simplificar o processo de implantação do dispositivo StorSimple em seu ambiente. Baixe e use essa lista de verificação para anotar os detalhes de configuração conforme implanta o seu dispositivo.  

[Baixar lista de verificação da configuração de implantação do StorSimple](http://www.microsoft.com/download/details.aspx?id=49159)  

## <a name="deployment-prerequisites"></a>Pré-requisitos de implantação
As seções a seguir explicam os pré-requisitos de configuração para o seu serviço StorSimple Manager e o seu dispositivo StorSimple.

### <a name="for-the-storsimple-manager-service"></a>Para o serviço StorSimple Manager
Antes de começar, verifique se:

* Você tem sua conta da Microsoft com credenciais de acesso.
* Você tem sua conta de armazenamento do Microsoft Azure com credenciais de acesso.
* Sua assinatura do Microsoft Azure está habilitada para o serviço StorSimple Manager. Sua assinatura deve ser comprada por meio do [Contrato Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/).
* Você tem acesso ao software de emulação de terminal como o PuTTY.

### <a name="for-the-device-in-the-datacenter"></a>Para o dispositivo no datacenter
Antes de configurar o dispositivo, verifique se:

* Seu dispositivo está totalmente desembalado, montado em um rack e cabeado para energia, rede e acesso serial conforme descrito em:
  
  * [Desembalar, montar em rack e cabear o dispositivo 8100](storsimple-8100-hardware-installation.md)
  * [Desembalar, montar em rack e cabear o dispositivo 8600](storsimple-8600-hardware-installation.md)

### <a name="for-the-network-in-the-datacenter"></a>Para a rede no datacenter
Antes de começar, verifique se:

* As portas no firewall do seu datacenter são abertas para permitir tráfego de nuvem e iSCSI, conforme descrito em [Requisitos de rede para o dispositivo StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).

## <a name="step-by-step-deployment"></a>IMPLANTAÇÃO PASSO A PASSO
Use as instruções passo a passo a seguir para implantar seu dispositivo StorSimple no datacenter.

## <a name="step-1-create-a-new-service"></a>Etapa 1: Criar um novo serviço
Um serviço StorSimple Manager pode gerenciar vários dispositivos StorSimple. Execute as seguintes etapas para criar uma nova instância do serviço StorSimple Manager.

[!INCLUDE [storsimple-create-new-service-gov](../../includes/storsimple-create-new-service-gov.md)]

> [!IMPORTANT]
> Se você não ativou a criação automática de uma conta de armazenamento com seu serviço, você precisará criar pelo menos uma conta de armazenamento depois que você criou com êxito um serviço. Esta conta de armazenamento será usada quando você criar um contêiner de volume.
> 
> * Se você não tiver criado uma conta de armazenamento automaticamente, vá para [Configurar uma nova conta de armazenamento para o serviço](#configure-a-new-storage-account-for-the-service) para obter instruções detalhadas.
> * Se você habilitou a criação automática de uma conta de armazenamento, vá para [Etapa 2: Obter a chave de registro do serviço](#step-2-get-the-service-registration-key).
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>Etapa 2: Obter a chave de registro do serviço
Depois que o serviço StorSimple Manager estiver em execução, será necessário obter a chave de registro do serviço. Essa chave é usada para registrar e conectar o seu dispositivo StorSimple ao serviço.

Execute as etapas a seguir no Portal do Governo.

[!INCLUDE [storsimple-get-service-registration-key-gov](../../includes/storsimple-get-service-registration-key-gov.md)]

## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>Etapa 3: Configurar e registrar o dispositivo por meio do Windows PowerShell para StorSimple
Use o Windows PowerShell para StorSimple para concluir a configuração inicial do seu dispositivo StorSimple, conforme explicado no procedimento a seguir. Você precisará usar o software de emulação de terminal para concluir esta etapa. Para obter mais informações, consulte [Use o PuTTY para conectar-se ao console serial do dispositivo](#use-putty-to-connect-to-the-device-serial-console).

[!INCLUDE [storsimple-configure-and-register-device-gov](../../includes/storsimple-configure-and-register-device-gov-u2.md)]

## <a name="step-4-complete-minimum-device-setup"></a>Etapa 4: Concluir a instalação mínima do dispositivo
Para a configuração mínima de dispositivo do seu dispositivo StorSimple, é necessário:

* Configurar o servidor DNS secundário.
* Habilitar o iSCSI em pelo menos uma interface de rede.
* Atribuir endereços IP fixos para ambos os controladores.

Execute as etapas a seguir no Portal do Governo para concluir a configuração mínima de dispositivo.

[!INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## <a name="step-5-create-a-volume-container"></a>Etapa 5: Criar um contêiner de volume
Um contêiner de volume tem a conta de armazenamento, largura de banda e configurações de criptografia para todos os volumes contidos nele. Você precisará criar um contêiner de volume antes de começar a provisionar volumes em seu dispositivo StorSimple.

Execute as etapas a seguir no Portal do Governo para criar um contêiner de volume.

[!INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>Etapa 6: Criar um volume
Depois de criar um contêiner de volume, você pode provisionar um volume de armazenamento no dispositivo StorSimple para seus servidores. Execute as etapas a seguir no Portal do Governo para criar um volume.

> [!IMPORTANT]
> O StorSimple do Azure pode criar somente volumes pequenos de provisionamento.  Não é possível criar volumes parcialmente ou totalmente provisionados em um sistema StorSimple do Azure.
> 
> 

[!INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume-u2.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>Etapa 7: Montar, inicializar e formatar um volume
Execute estas etapas no host do Windows Server.

> [!IMPORTANT]
> * Para a alta disponibilidade de sua solução StorSimple, recomendamos que você configure o MPIO em seus servidores de host  (opcional) antes de configurar o iSCSI. A configuração do MPIO em servidores de host garantirá que os servidores possam uma falha de link, rede ou interface.
> * Para obter instruções de instalação e configuração do MPIO e iSCSI no host do Windows Server, vá para [Configurar o MPIO para seu dispositivo StorSimple](storsimple-configure-mpio-windows-server.md). Elas também incluirão as etapas para montar, inicializar e formatar volumes StorSimple.
> * Para obter instruções de instalação e configuração do MPIO e iSCSI em um host do Linux, vá para [Configurar o MPIO para seu host do Linux do StorSimple](storsimple-configure-mpio-on-linux.md)
> 
> 

Se você decidir não configurar o MPIO, execute as etapas a seguir para montar, inicializar e formatar os volumes StorSimple em um host do Windows Server.

[!INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>Etapa 8: Fazer um backup
Backups oferecem proteção pontual de volumes e melhoram a capacidade de recuperação, minimizando os tempos de restauração. Você pode executar dois tipos de backup em seu dispositivo StorSimple: instantâneos locais e instantâneos em nuvem. Cada um desses tipos de backup pode ser **Agendado** ou **Manual**.

Execute as etapas a seguir no Portal do Governo para criar um backup agendado.

[!INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

Você pode fazer um backup manual a qualquer momento. Para saber os procedimentos, vá para [Criar um backup manual](#create-a-manual-backup).

## <a name="configure-a-new-storage-account-for-the-service"></a>Configurar uma nova conta de armazenamento para o serviço
Esta é uma etapa opcional que você precisa executar somente se não tiver ativado a criação automática de uma conta de armazenamento com o seu serviço. É necessária uma conta de armazenamento do Microsoft Azure para criar um contêiner de volume StorSimple.

Se você precisar criar uma conta de armazenamento do Azure em uma região diferente, consulte [Sobre Contas de Armazenamento do Azure](../storage/common/storage-create-storage-account.md) para obter instruções passo a passo.

Execute as etapas a seguir no Portal do Governo, na página **Serviço do Gerenciador do StorSimple** .

[!INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]

## <a name="use-putty-to-connect-to-the-device-serial-console"></a>Use o PuTTY para conectar-se ao console serial do dispositivo
Para se conectar ao Windows PowerShell para StorSimple, você precisa usar um software de emulação de terminal como o PuTTY. Você pode usar o PuTTY ao acessar o dispositivo diretamente através do console serial ou abrindo uma sessão de telnet a partir de um computador remoto.

[!INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>Verificar e aplicar atualizações
Atualizar seu dispositivo pode demorar várias horas. Execute as etapas a seguir para verificar e aplicar atualizações em seu dispositivo.

<!--If you have a gateway configured on a network interface other than Data 0, you will need to disable Data 2 and Data 3 network interfaces before installing the update. Go to **Devices > Configure** and disable Data 2 and Data 3 interfaces. You should re-enable these interfaces after the device is updated.-->

#### <a name="to-update-your-device"></a>Para atualizar seu dispositivo
1. Na página **Início Rápido** do dispositivo, clique em **Dispositivos**. Selecione o dispositivo físico, clique em **Manutenção** e em **Verificar Atualizações**.  
2. É criado um trabalho para verificar se há atualizações disponíveis. Se houver atualizações disponíveis, **Verificar Atualizações** muda para **Instalar Atualizações**. Clique em **Instalar Atualizações**.
3. Será criado um trabalho de atualização. Monitore o status da sua atualização navegando até **Trabalhos**.
   
   > [!NOTE]
   > Quando o trabalho de atualização é iniciado, ele imediatamente exibe o status como 50%. O status muda para 100% somente após o trabalho de atualização ser concluído. Não há nenhum status em tempo real para o processo de atualização.
   > 
   > 
4. Depois que o dispositivo for atualizado com êxito, habilite as interfaces de rede Data 2 e Data 3 se elas tiverem sido desabilitadas.

## <a name="get-the-iqn-of-a-windows-server-host"></a>Obter o IQN de um host do Windows Server
Execute as seguintes etapas para obter o iSCSI IQN (Nome Qualificado) de um host do Windows que está executando o Windows Server ® 2012.

[!INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>Criar um backup manual
Execute as etapas a seguir no Portal do Governo para criar um backup manual sob demanda para um único volume no seu dispositivo StorSimple.

[!INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup-gov.md)]

## <a name="configure-mpio"></a>Configurar o MPIO
Multipath I/O (MPIO) é um recurso opcional e não é instalado no Windows Server por padrão. Ele deve ser instalado como um recurso por meio do Gerenciador de Servidores. Para obter instruções de instalação de MPIO, vá para [Configurar MPIO para o seu dispositivo StorSimple](storsimple-configure-mpio-windows-server.md).

Para obter instruções de instalação do MPIO para um dispositivo do StorSimple conectado a um host do Linux, vá para [Configurar o MPIO para seu host do Linux](storsimple-configure-mpio-on-linux.md).

> [!NOTE]
> Não há suporte para MPIO em um dispositivo virtual StorSimple no Azure.
> 
> 

## <a name="next-steps"></a>Próximas etapas
* Configurar um [dispositivo virtual](storsimple-virtual-device-u2.md).
* Use o [Serviço StorSimple Manager](storsimple-manager-service-administration.md) para gerenciar o seu dispositivo StorSimple.

