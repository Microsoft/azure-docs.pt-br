---
title: Instalar o agente de Serviços de Recuperação do Microsoft Azure (MARS)
description: Saiba como instalar o agente de Serviços de Recuperação do Microsoft Azure (MARS) para fazer backup de computadores Windows.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: acf38fdf338fcdd0cd7902d4295f0f03310543a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98986863"
---
# <a name="install-the-azure-backup-mars-agent"></a>Instalar o agente MARS do backup do Azure

Este artigo explica como instalar o agente de Serviços de Recuperação do Microsoft Azure (MARS). O MARS também é conhecido como o agente de backup do Azure.

## <a name="about-the-mars-agent"></a>Sobre o agente MARS

O Backup do Azure usa o agente de MARS para fazer backup de arquivos, pastas e estado do sistema por meio de computadores locais e de VMs do Azure. Esses backups são armazenados em um cofre dos Serviços de Recuperação no Azure. Você pode executar o agente:

* Diretamente em computadores Windows locais. Esses computadores podem fazer backup diretamente para um cofre dos Serviços de Recuperação do Azure.
* Em VMs do Azure que executam o Windows lado a lado com a extensão de backup da VM do Azure. O agente faz backup de arquivos e pastas específicos na VM.
* Em uma instância do MABS (servidor de Backup do Microsoft Azure) ou um servidor do System Center Data Protection Manager (DPM). Nesse cenário, as máquinas e as cargas de trabalho fazem backup em MABS ou Data Protection Manager. Em seguida, MABS ou Data Protection Manager usa o agente MARS para fazer backup em um cofre no Azure.

Os dados que estão disponíveis para backup dependem do local em que o agente está instalado.

> [!NOTE]
> Em geral, você faz backup de uma VM do Azure usando uma extensão de backup do Azure na VM. Esse método faz o backup de toda a VM. Se você quiser fazer backup de arquivos e pastas específicos na VM, instale e use o agente MARS junto com a extensão. Para obter mais informações, consulte [arquitetura de um backup de VM do Azure interno](backup-architecture.md#architecture-built-in-azure-vm-backup).

![Etapas do processo de backup](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Antes de começar

* Saiba como [o backup do Azure usa o agente Mars para fazer backup de computadores Windows](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
* Saiba mais sobre a [arquitetura de backup](backup-architecture.md#architecture-back-up-to-dpmmabs) que executa o agente Mars em um servidor mAbs ou de Data Protection Manager secundário.
* Examine [o que tem suporte e o que você pode fazer backup](backup-support-matrix-mars-agent.md) pelo agente Mars.
* Verifique se você tem uma conta do Azure se precisar fazer backup de um servidor ou cliente no Azure. Se você não tiver uma conta, poderá criar uma [gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos.
* Verifique o acesso à Internet nos computadores que deseja fazer backup.
* Verifique se o usuário que está executando a instalação e a configuração do agente MARS tem privilégios de administrador local no servidor a ser protegido.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Modificar a replicação de armazenamento

Por padrão, os cofres usam [GRS (armazenamento com redundância geográfica)](../storage/common/storage-redundancy.md#geo-redundant-storage).

* Se o cofre for seu mecanismo de backup primário, recomendamos que você use GRS.
* Você pode usar o [LRS (armazenamento com redundância local)](../storage/common/storage-redundancy.md#locally-redundant-storage) para reduzir os custos de armazenamento do Azure.

Para modificar o tipo de replicação de armazenamento:

1. No novo cofre, selecione **Propriedades** na seção **configurações** .

1. Na página **Propriedades** , em **configuração de backup**, selecione **Atualizar**.

1. Selecione o tipo de replicação de armazenamento e selecione **salvar**.

    ![Atualizar configuração de backup](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Não é possível modificar o tipo de replicação de armazenamento depois que o cofre é configurado e contém itens de backup. Se precisar fazer isso, será necessário recriar o cofre.
>

### <a name="verify-internet-access"></a>Verifique o acesso à Internet

Se o seu computador tiver acesso limitado à Internet, verifique se as configurações de firewall no computador ou proxy permitem as seguintes URLs e endereços IP:

* URLs
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* Endereços IP
  * 20.190.128.0/18
  * 40.126.0.0/18

### <a name="use-azure-expressroute"></a>Usar o Azure ExpressRoute

Você pode fazer backup de seus dados por meio do Azure ExpressRoute usando o emparelhamento público (disponível para circuitos antigos) e o emparelhamento da Microsoft. Não há suporte para backup em emparelhamento privado.

Para usar o emparelhamento público, primeiro garanta o acesso aos seguintes domínios e endereços:

* `http://www.msftncsi.com/ncsi.txt`
* `http://www.msftconnecttest.com/connecttest.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`
* Endereços IP
  * 20.190.128.0/18
  * 40.126.0.0/18

Para usar o emparelhamento da Microsoft, selecione os seguintes serviços, regiões e valores de comunidade relevantes:

* Azure Active Directory (12076:5060)
* Região do Azure, de acordo com o local do cofre dos serviços de recuperação
* Armazenamento do Azure, de acordo com o local do cofre dos serviços de recuperação

Para obter mais informações, consulte [requisitos de roteamento do ExpressRoute](../expressroute/expressroute-routing.md).

> [!NOTE]
> O emparelhamento público foi preterido para novos circuitos.

Todas as URLs e endereços IP anteriores usam o protocolo HTTPS na porta 443.

### <a name="private-endpoints"></a>Pontos de extremidade privados

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="download-the-mars-agent"></a>Baixar o agente MARS

Baixe o agente de MARS para que possa instalá-lo nos computadores que deseja fazer backup.

Se já tiver instalado o agente em qualquer computador, verifique se é a versão mais recente do agente. Localize a versão mais recente no portal ou vá diretamente para o [Download](https://aka.ms/azurebackup_agent).

1. No cofre, em **introdução**, selecione **backup**.

    ![Abrir o objetivo de backup](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. Em **Onde sua carga de trabalho é executada?**, selecione **Localmente**. Selecione essa opção mesmo que deseje instalar o agente de MARS em uma VM do Azure.
1. Em **O que você deseja incluir no backup?**, selecione **Arquivos e pastas**. Você também pode selecionar **Estado do sistema**. Muitas outras opções estão disponíveis, mas elas são compatíveis somente se você estiver executando um servidor de backup secundário. Selecione **Preparar a infraestrutura**.

    ![Configurar arquivos e pastas](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. Para **preparar a infraestrutura**, em **instalar o agente dos serviços de recuperação**, baixe o agente Mars.

    ![Preparar a infraestrutura](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. No menu Download, selecione **Salvar**. Por padrão, o arquivo *MARSagentinstaller.exe* será salvo em sua pasta Downloads.

1. Selecione **já baixar ou usar o agente de serviços de recuperação mais recente** e, em seguida, baixe as credenciais do cofre.

    ![Baixar as credenciais do cofre](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. Clique em **Salvar**. O arquivo é baixado para a pasta downloads. Você não pode abrir o arquivo de credenciais do cofre.

## <a name="install-and-register-the-agent"></a>Instalar e registrar o agente

1. Execute o arquivo de *MARSagentinstaller.exe* nos computadores que você deseja fazer backup.
1. No assistente de instalação do agente MARS, selecione **configurações de instalação**. Lá, escolha onde instalar o agente e escolha um local para o cache. Em seguida, selecione **Avançar**.
   * O Backup do Azure usa o cache para armazenar instantâneos de dados antes de enviá-los ao Azure.
   * A localização do cache deve ter espaço livre igual a pelo menos 5% do tamanho dos dados que você fará backup.

    ![Escolha as configurações de instalação no assistente de instalação do agente MARS](./media/backup-configure-vault/mars1.png)

1. Para **configuração de proxy**, especifique como o agente que é executado no computador Windows se conectará à Internet. Em seguida, selecione **Avançar**.

   * Se você usar um proxy personalizado, especifique as credenciais e configurações de proxy necessárias.
   * Lembre-se de que o agente precisa de acesso a [URLs específicas](#before-you-start).

    ![Configurar o acesso à Internet no assistente de MARS](./media/backup-configure-vault/mars2.png)

1. Para **instalação**, examine os pré-requisitos e selecione **instalar**.
1. Depois que o agente for instalado, selecione **Prosseguir para o Registro**.
1. Na identificação do cofre do **Assistente para servidor de registro**  >  , navegue até e selecione o arquivo de credenciais que você baixou. Em seguida, selecione **Avançar**.

    ![Adicionar credenciais do cofre usando o assistente para registrar servidor](./media/backup-configure-vault/register1.png)

1. Na página **configuração de criptografia** , especifique uma senha que será usada para criptografar e descriptografar backups para o computador. [Consulte aqui](backup-azure-file-folder-backup-faq.md#what-characters-are-allowed-for-the-passphrase) para obter mais informações sobre caracteres de senha permitidos.

    * Salve a frase secreta em um local seguro. Você precisa dele para restaurar um backup.
    * Se você perder ou esquecer a frase secreta, a Microsoft não poderá ajudá-lo a recuperar os dados de backup.

1. Selecione **Concluir**. Agora, o agente está instalado e o computador está registrado no cofre. Você está pronto para configurar e agendar o backup.

## <a name="next-steps"></a>Próximas etapas

Saiba como [fazer backup de computadores Windows usando o agente Mars de backup do Azure](backup-windows-with-mars-agent.md)
