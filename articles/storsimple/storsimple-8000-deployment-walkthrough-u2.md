---
title: "Implantar seu dispositivo StorSimple série 8000 no portal do Azure | Microsoft Docs"
description: "Descreve as etapas e as práticas recomendadas para implantar o dispositivo StorSimple série 8000 executando a Atualização 3 e posteriores, e o serviço do Gerenciador de Dispositivos StorSimple."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: bcf42ebb081517d247690ee57c2be274784ef29d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-your-on-premises-storsimple-device-update-3-and-later"></a>Implantar o dispositivo StorSimple no local (Atualização 3 e posteriores)

## <a name="overview"></a>Visão geral
Bem-vindo à implantação do dispositivo Microsoft Azure StorSimple. Estes tutoriais de implantação se aplicam à Atualização 3 ou posteriores do StorSimple série 8000. Esta série de tutoriais inclui uma lista de verificação de configuração, pré-requisitos de configuração e etapas de configuração detalhadas para seu dispositivo StorSimple.

As informações nesses tutoriais pressupõem que você revisou as precauções de segurança e desembalou, colocou seu dispositivo StorSimple em um rack e instalou os cabos. Se você ainda precisa executar essas tarefas, comece com a revisão das [precauções de segurança](storsimple-8000-safety.md). Siga as instruções específicas do dispositivo para desempacotar, montar em rack e cabear o dispositivo.

* [Desembalar, montar em rack e cabear o 8100](storsimple-8100-hardware-installation.md)
* [Desembalar, montar em rack e cabear o 8600](storsimple-8600-hardware-installation.md)

Você precisará de privilégios de administrador para concluir o processo de instalação e configuração. Recomenda-se que você leia a lista de verificação de configuração antes de começar. O processo de implantação e configuração pode levar algum tempo para ser concluído.

> [!NOTE]
> As informações de implantação do StorSimple publicadas no site do Microsoft Azure se aplicam apenas aos dispositivos da série StorSimple 8000. Para obter informações completas sobre os dispositivos da série 7000, vá para: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Para obter informações de implantação da série 7000, consulte o [Guia de Início Rápido do Sistema StorSimple](http://onlinehelp.storsimple.com/111_Appliance/). 


## <a name="deployment-steps"></a>Etapas de implantação.
Execute estas etapas necessárias para configurar o dispositivo StorSimple e conectá-lo ao serviço do gerenciador de dispositivos StorSimple. Além das etapas necessárias, há etapas e procedimentos opcionais que talvez sejam necessários durante a implantação. As instruções passo a passo de implantação indicam quando você deve executar cada uma destas etapas opcionais.

| Etapa | Descrição |
| --- | --- |
| **PRÉ-REQUISITOS** |Eles precisam ser concluídos na preparação para a próxima implantação. |
| [Lista de verificação da configuração da implantação](#deployment-configuration-checklist) |Use essa lista de verificação para coletar e registrar informações antes e durante a implantação. |
| [Pré-requisitos de implantação](#deployment-prerequisites) |Eles validam se o ambiente está pronto para implantação. |
|  | |
| **IMPLANTAÇÃO PASSO A PASSO** |Essas etapas são necessárias para implantar o dispositivo StorSimple na produção. |
| [Etapa 1: Criar um novo serviço](#step-1-create-a-new-service) |Configure o armazenamento e o gerenciamento de nuvem para o dispositivo StorSimple. *Ignore esta etapa se você tem um serviço existente para outros dispositivos StorSimple*. |
| [Etapa 2: Obter a chave de registro do serviço](#step-2-get-the-service-registration-key) |Use essa chave para registrar e conectar o dispositivo StorSimple ao serviço de gerenciamento. |
| [Etapa 3: Configurar e registrar o dispositivo por meio do Windows PowerShell para StorSimple](#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |Para concluir a configuração usando o serviço de gerenciamento, conecte o dispositivo à sua rede e o registre no Azure. |
| [Etapa 4: Concluir a instalação mínima do dispositivo](#step-4-complete-minimum-device-setup)</br>[Opcional: atualizar o dispositivo StorSimple](#scan-for-and-apply-updates) |Use o serviço de gerenciamento para concluir a instalação do dispositivo e habilitá-lo para fornecer armazenamento. |
| [Etapa 5: Criar um contêiner de volume](#step-5-create-a-volume-container) |Crie um contêiner para provisionar volumes. Um contêiner de volume tem a conta de armazenamento, largura de banda e configurações de criptografia para todos os volumes contidos nele. |
| [Etapa 6: Criar um volume](#step-6-create-a-volume) |Provisione volumes de armazenamento no dispositivo StorSimple para seus servidores. |
| [Etapa 7: Montar, inicializar e formatar um volume](#step-7-mount-initialize-and-format-a-volume)</br>[Opcional: configurar o MPIO](storsimple-8000-configure-mpio-windows-server.md) |Conecte os servidores ao armazenamento iSCSI fornecido pelo dispositivo. Opcionalmente, configure o MPIO para garantir que os servidores possam tolerar a falha de link, rede e interface. |
| [Etapa 8: Fazer um backup](#step-8-take-a-backup) |Configure a política de backup para proteger seus dados |
|  | |
| **OUTROS PROCEDIMENTOS** |Talvez seja necessário consultar esses procedimentos conforme você implantar sua solução. |
| [Configurar uma nova conta de armazenamento para o serviço](#configure-a-new-storage-account-for-the-service) | |
| [Use o PuTTY para conectar-se ao console serial do dispositivo](#use-putty-to-connect-to-the-device-serial-console) | |
| [Obter o IQN de um host do Windows Server](#get-the-iqn-of-a-windows-server-host) | |
| [Criar um backup manual](#create-a-manual-backup) | |


## <a name="deployment-configuration-checklist"></a>Lista de verificação da configuração da implantação
Antes de implantar o seu dispositivo, você precisará coletar informações para configurar o software em seu dispositivo StorSimple. Preparar essas informações antecipadamente ajuda a simplificar o processo de implantação do dispositivo StorSimple no seu ambiente. Baixe e use essa lista de verificação para anotar os detalhes de configuração conforme implanta o seu dispositivo.

* [Baixar lista de verificação da configuração de implantação do StorSimple](http://www.microsoft.com/download/details.aspx?id=49159)

## <a name="deployment-prerequisites"></a>Pré-requisitos de implantação
As seções a seguir explicam os pré-requisitos de configuração para o seu serviço do gerenciador de dispositivos StorSimple e o dispositivo StorSimple.

### <a name="for-the-storsimple-device-manager-service"></a>Para implantar o serviço Gerenciador de Dispositivos StorSimple
Antes de começar, verifique se:

* Você tem sua conta da Microsoft com credenciais de acesso.
* Você tem sua conta de armazenamento do Microsoft Azure com credenciais de acesso.
* A sua assinatura do Microsoft Azure está habilitada para o serviço do gerenciador de dispositivos StorSimple. Sua assinatura deve ser comprada por meio do [Contrato Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/).
* Você tem acesso ao software de emulação de terminal como o PuTTY.

### <a name="for-the-device-in-the-datacenter"></a>Para o dispositivo no datacenter
Antes de configurar o dispositivo, verifique se seu dispositivo está totalmente desembalado, montado em um rack e cabeado para energia, rede e acesso serial conforme descrito em:

* [Desembalar, montar em rack e cabear o dispositivo 8100](storsimple-8100-hardware-installation.md)
* [Desembalar, montar em rack e cabear o dispositivo 8600](storsimple-8600-hardware-installation.md)

### <a name="for-the-network-in-the-datacenter"></a>Para a rede no datacenter
Antes de começar, verifique se:

* As portas no firewall do seu datacenter são abertas para permitir tráfego de nuvem e iSCSI, conforme descrito em [Requisitos de rede para o dispositivo StorSimple](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).

## <a name="step-by-step-deployment"></a>IMPLANTAÇÃO PASSO A PASSO
Use as instruções passo a passo a seguir para implantar seu dispositivo StorSimple no datacenter.

## <a name="step-1-create-a-new-service"></a>Etapa 1: Criar um novo serviço
Um serviço do gerenciador de dispositivos StorSimple e pode gerenciar vários dispositivos StorSimple. Execute as etapas a seguir para criar uma nova instância do serviço Gerenciador de Dispositivos StorSimple.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]

> [!IMPORTANT]
> Se você não ativou a criação automática de uma conta de armazenamento com seu serviço, você precisará criar pelo menos uma conta de armazenamento depois que você criou com êxito um serviço. Esta conta de armazenamento será usada quando você criar um contêiner de volume.
>
> * Se você não tiver criado uma conta de armazenamento automaticamente, vá para [Configurar uma nova conta de armazenamento para o serviço](#configure-a-new-storage-account-for-the-service) para obter instruções detalhadas.
> * Se você habilitou a criação automática de uma conta de armazenamento, vá para [Etapa 2: Obter a chave de registro do serviço](#step-2-get-the-service-registration-key).


## <a name="step-2-get-the-service-registration-key"></a>Etapa 2: Obter a chave de registro do serviço
Depois que o serviço Gerenciador de Dispositivos StorSimple estiver em execução, será necessário obter a chave de registro do serviço. Essa chave é usada para registrar e conectar o seu dispositivo StorSimple com o serviço.

Execute as etapas a seguir no portal do Azure.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>Etapa 3: Configurar e registrar o dispositivo por meio do Windows PowerShell para StorSimple
Use o Windows PowerShell para StorSimple para concluir a configuração inicial do seu dispositivo StorSimple, conforme explicado no procedimento a seguir. Você precisará usar o software de emulação de terminal para concluir esta etapa. Para obter mais informações, consulte [Use o PuTTY para conectar-se ao console serial do dispositivo](#use-putty-to-connect-to-the-device-serial-console).

[!INCLUDE [storsimple-8000-configure-and-register-device-u2](../../includes/storsimple-8000-configure-and-register-device-u2.md)]

## <a name="step-4-complete-minimum-device-setup"></a>Etapa 4: Concluir a instalação mínima do dispositivo
Para a configuração mínima de dispositivo do seu dispositivo StorSimple, é necessário: 

* Forneça um nome amigável para seu dispositivo.
* Defina o fuso horário do dispositivo.
* Atribuir endereços IP fixos para ambos os controladores.

Execute as etapas a seguir no portal do Azure para concluir a configuração mínima do dispositivo.

[!INCLUDE [storsimple-8000-complete-minimum-device-setup-u2](../../includes/storsimple-8000-complete-minimum-device-setup-u2.md)]

## <a name="step-5-create-a-volume-container"></a>Etapa 5: Criar um contêiner de volume
Um contêiner de volume tem a conta de armazenamento, largura de banda e configurações de criptografia para todos os volumes contidos nele. Você precisará criar um contêiner de volume antes de começar a provisionar volumes em seu dispositivo StorSimple.

Execute as etapas a seguir no portal do Azure para criar um contêiner de volume.

[!INCLUDE [storsimple-8000-create-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>Etapa 6: Criar um volume
Depois de criar um contêiner de volume, você pode provisionar um volume de armazenamento no dispositivo StorSimple para seus servidores. Execute as etapas a seguir no portal do Azure para criar um volume.

> [!IMPORTANT]
> O gerenciador de dispositivos StorSimple pode criar apenas volumes pequenos e totalmente provisionados. No entanto, não é possível criar volumes parcialmente provisionados.


[!INCLUDE [storsimple-8000-create-volume](../../includes/storsimple-8000-create-volume-u2.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>Etapa 7: Montar, inicializar e formatar um volume
As etapas a seguir são executadas no host do Windows Server.

> [!IMPORTANT]
> * Para a alta disponibilidade de sua solução StorSimple, recomendamos que você configure o MPIO em seus servidores de host  (opcional) antes de configurar o iSCSI. A configuração do MPIO em servidores de host garantirá que os servidores possam uma falha de link, rede ou interface.
> * Para obter instruções de instalação e configuração do MPIO e iSCSI no host do Windows Server, vá para [Configurar o MPIO para seu dispositivo StorSimple](storsimple-8000-configure-mpio-windows-server.md). Elas também incluirão as etapas para montar, inicializar e formatar volumes StorSimple.
> * Para obter instruções de instalação e configuração do MPIO e iSCSI em um host do Linux, vá para [Configurar o MPIO para seu host do Linux do StorSimple](storsimple-configure-mpio-on-linux.md)

Se você decidir não configurar o MPIO, execute as etapas a seguir para montar, inicializar e formatar os volumes StorSimple em um host do Windows Server.

[!INCLUDE [storsimple-8000-mount-initialize-format-volume](../../includes/storsimple-8000-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>Etapa 8: Fazer um backup
Backups oferecem proteção pontual de volumes e melhoram a capacidade de recuperação, minimizando os tempos de restauração. Você pode executar dois tipos de backup em seu dispositivo StorSimple: instantâneos locais e instantâneos em nuvem. Cada um desses tipos de backup pode ser **Agendado** ou **Manual**.

Execute as etapas a seguir no portal do Azure para criar um backup agendado.

[!INCLUDE [storsimple-8000-take-backup](../../includes/storsimple-8000-take-backup.md)]

Você pode fazer um backup manual a qualquer momento. Para saber os procedimentos, vá para [Criar um backup manual](#create-a-manual-backup).

Você concluiu a configuração do dispositivo.

## <a name="configure-a-new-storage-account-for-the-service"></a>Configurar uma nova conta de armazenamento para o serviço
Esta é uma etapa opcional que você precisa executar somente se não tiver ativado a criação automática de uma conta de armazenamento com o seu serviço. É necessária uma conta de armazenamento do Microsoft Azure para criar um contêiner de volume StorSimple.

Se você precisar criar uma conta de armazenamento do Azure em uma região diferente, consulte [Sobre Contas de Armazenamento do Azure](../storage/common/storage-create-storage-account.md) para obter instruções passo a passo.

Execute as etapas a seguir no portal do Azure, na página **Serviço do gerenciador de dispositivos StorSimple**.

[!INCLUDE [storsimple-8000-configure-new-storage-account-u2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

## <a name="use-putty-to-connect-to-the-device-serial-console"></a>Use o PuTTY para conectar-se ao console serial do dispositivo
Para se conectar ao Windows PowerShell para StorSimple, você precisa usar um software de emulação de terminal como o PuTTY. Você pode usar o PuTTY ao acessar o dispositivo diretamente através do console serial ou abrindo uma sessão de telnet a partir de um computador remoto.

[!INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>Verificar e aplicar atualizações
Atualizar seu dispositivo pode demorar várias horas. Para obter etapas detalhadas sobre como instalar a atualização mais recente, vá para [Instalar a Atualização 4](storsimple-8000-install-update-4.md).


## <a name="get-the-iqn-of-a-windows-server-host"></a>Obter o IQN de um host do Windows Server
Execute as seguintes etapas para obter o iSCSI IQN (Nome Qualificado) de um host do Windows que está executando o Windows Server ® 2012.

[!INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>Criar um backup manual
Execute as etapas a seguir no portal do Azure para criar um backup manual sob demanda para um único volume em seu dispositivo StorSimple.

[!INCLUDE [Create a manual backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Próximas etapas
* [Configurar um Dispositivo de Nuvem StorSimple](storsimple-8000-cloud-appliance-u2.md).
* [Use o serviço Gerenciador de Dispositivos StorSimple para gerenciar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

