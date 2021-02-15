---
title: Preparar o servidor DPM para fazer backup de cargas de trabalho
description: Neste artigo, saiba como preparar os backups do System Center Data Protection Manager (DPM) para o Azure usando o serviço de backup do Azure.
ms.topic: conceptual
ms.date: 06/11/2020
ms.openlocfilehash: 823b23d99959df5f2eed20cf4136254e1702fe89
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98985624"
---
# <a name="prepare-to-back-up-workloads-to-azure-with-system-center-dpm"></a>Preparar-se para fazer backup de cargas de trabalho no Azure com o System Center DPM

Este artigo explica como se preparar para backups do System Center Data Protection Manager (DPM) no Azure, usando o serviço de Backup do Azure.

O artigo fornece:

- Uma visão geral da implantação do DPM com o Backup do Azure.
- Pré-requisitos e limitações para usar o Backup do Azure com o DPM.
- Etapas para preparar o Azure, incluindo a configuração de um cofre de Backup dos Serviços de Recuperação e, opcionalmente, a modificação do tipo de armazenamento do Azure para o cofre.
- Etapas para preparar o servidor DPM, incluindo o download de credenciais do cofre, a instalação do agente do Backup do Azure e o registro do servidor DPM no cofre.
- Dicas de solução de problemas para erros comuns.

## <a name="why-back-up-dpm-to-azure"></a>Por que fazer backup do DPM no Azure?

[O System Center DPM](/system-center/dpm/dpm-overview) faz backup de dados de arquivos e aplicativos. O DPM interage com o Backup do Azure da seguinte maneira:

- **DPM em execução em um servidor físico ou VM local** -você pode fazer backup de dados em um cofre de backup no Azure, além do backup em disco e fita.
- **DPM em execução em uma VM do Azure** – do System Center 2012 R2 com atualização 3 ou posterior, você pode implantar o DPM em uma VM do Azure. Você pode fazer backup de dados em discos do Azure conectados à VM ou usar o Backup do Azure para fazer backup dos dados em um cofre de backup.

Os benefícios comerciais do backup de servidores DPM para o Azure incluem:

- Para implantação do DPM no local, o Backup do Azure oferece uma alternativa à implantação de longo prazo em fita.
- Para o DPM em execução em uma VM do Azure, o Backup do Azure permite descarregar o armazenamento do disco do Azure. O armazenamento de dados mais antigos no cofre de Backup permite escalar verticalmente os negócios, armazenando novos dados no disco.

## <a name="prerequisites-and-limitations"></a>Pré-requisitos e limitações

**Configuração** | **Requisito**
--- | ---
DPM em uma VM do Azure | System Center 2012 R2 com o Pacote Cumulativo de Atualizações 3 ou posterior do DPM 2012 R2.
DPM em um servidor físico | System Center 2012 SP1 ou posterior; System Center 2012 R2.
DPM em uma VM do Hyper-V | System Center 2012 SP1 ou posterior; System Center 2012 R2.
DPM em uma VM do VMware | System Center 2012 R2 com o Pacote Cumulativo de Atualizações 5 ou posterior.
Componentes | O servidor DPM deve ter o Windows PowerShell e o .NET Framework 4,5 instalados.
Aplicativos com suporte | [Saiba](/system-center/dpm/dpm-protection-matrix) o que o DPM pode incluir no backup.
Tipos de arquivo com suporte | É possível fazer backup destes tipos de arquivo com o Backup do Azure:<br> <li>Criptografado (somente backups completos)<li> Compactado (backups incrementais com suporte) <li> Esparso (backups incrementais com suporte)<li> Compactados e esparsos (tratados como esparsos)
Tipos de arquivo sem suporte | <li>Servidores em sistemas de arquivos que diferenciam maiúsculas de minúsculas<li> links físicos (ignorados)<li> pontos de nova análise (ignorados)<li> criptografado e compactado (ignorado)<li> criptografado e esparso (ignorado)<li> Fluxo compactado<li> analisar fluxo
Armazenamento local | Cada computador que você deseja fazer backup deve ter armazenamento local livre que tenha pelo menos 5% do tamanho dos dados que estão sendo submetidos a backup. Por exemplo, um backup de 100 GB de dados requer um mínimo de 5 GB de espaço livre na localização temporária.
Armazenamento de cofre | Não há limite para a quantidade de dados que você pode fazer backup em um cofre de backup do Azure, mas o tamanho de uma fonte de dados (por exemplo, uma máquina virtual ou banco de dado) não deve exceder 54.400 GB.
Azure ExpressRoute | Você pode fazer backup dos dados por meio do Azure ExpressRoute com o emparelhamento público (disponível para circuitos antigos) e o emparelhamento da Microsoft. Não há suporte para backup em emparelhamento privado.<br/><br/> **Com o emparelhamento público**: Verifique o acesso aos seguintes domínios/endereços:<br/><br/> URLs:<br> `www.msftncsi.com` <br> . Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>`www.msftconnecttest.com`<br><br>Endereços IP<br>  20.190.128.0/18 <br>  40.126.0.0/18<br> <br/>**Com o emparelhamento da Microsoft**, selecione os seguintes serviços/regiões e os valores de comunidade relevantes:<br/><br/>-Azure Active Directory (12076:5060)<br/><br/>-Microsoft Azure região (de acordo com o local do cofre dos serviços de recuperação)<br/><br/>-Armazenamento do Azure (de acordo com o local do cofre dos serviços de recuperação)<br/><br/>Para obter mais informações, consulte [requisitos de roteamento do ExpressRoute](../expressroute/expressroute-routing.md).<br/><br/>**Observação**: o emparelhamento público foi preterido para novos circuitos.
Agente de Backup do Azure | Se o DPM estiver sendo executado no System Center 2012 SP1, instale o pacote cumulativo de atualizações 2 ou posterior para o DPM SP1. Isso é necessário para a instalação do agente.<br/><br/> Este artigo descreve como implantar a versão mais recente do agente do Backup do Azure, também conhecido como agente do Serviço de Recuperação do Microsoft Azure (MARS). Se você tiver uma versão anterior implantada, atualize para a versão mais recente para garantir que o backup funcione conforme o esperado.

Antes de começar, você precisará de uma conta Azure com o recurso de Backup do Azure habilitado. Se não tiver uma conta, você poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Leia sobre os [preços do Backup do Azure](https://azure.microsoft.com/pricing/details/backup/).

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-settings"></a>Modificar configurações de armazenamento

Você pode escolher entre o armazenamento com redundância geográfica e armazenamento com redundância local.

- Por padrão, seu cofre tem armazenamento com redundância geográfica.
- Se o cofre for seu backup principal, deixe a opção definida como armazenamento com redundância geográfica. Se você quiser uma opção mais barata que não seja tão durável, use o procedimento a seguir para configurar o armazenamento com redundância local.
- Saiba mais sobre o [armazenamento do Azure](../storage/common/storage-redundancy.md)e as opções de armazenamento com redundância [de zona](../storage/common/storage-redundancy.md#zone-redundant-storage) e redundância [geográfica](../storage/common/storage-redundancy.md#geo-redundant-storage) [local](../storage/common/storage-redundancy.md#locally-redundant-storage) .
- Modifique as configurações de armazenamento antes do backup inicial. Se você já fez o backup de um item, pare de fazer o backup no cofre antes de modificar as configurações de armazenamento.

Para editar a configuração de replicação de armazenamento:

1. Abra o painel do cofre.

2. Em **gerenciar**, selecione **infraestrutura de backup**.

3. No menu **Configuração de Backup**, selecione uma opção de armazenamento para o cofre.

    ![Lista de cofres de backup](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

## <a name="download-vault-credentials"></a>Baixar as credenciais do cofre

Você usa credenciais do cofre quando registra o servidor DPM no cofre.

- O arquivo de credenciais do cofre é um certificado gerado pelo portal para cada cofre de backup.
- O portal, em seguida, carrega a chave pública no ACS (Serviço de Controle de Acesso).
- Durante o fluxo de trabalho de registro do computador, a chave privada do certificado é disponibilizada para o usuário, o que autentica o computador.
- Com base na autenticação, o serviço de Backup do Azure envia dados para o cofre identificado.

### <a name="best-practices-for-vault-credentials"></a>Melhores práticas para credenciais do cofre

Para obter as credenciais, baixe o arquivo de credencial do cofre por meio de um canal seguro do portal do Azure:

- As credenciais do cofre são usadas somente durante o fluxo de trabalho de registro.
- É sua responsabilidade garantir que o arquivo de credenciais do cofre esteja seguro e não seja comprometido.
  - Se o controle das credenciais for perdido, as credenciais do cofre poderão ser usadas para registrar outros computadores no cofre.
  - No entanto, os dados de backup são criptografados usando uma frase secreta que pertence a você, portanto, os dados de backup existentes não podem ser comprometidos.
- Certifique-se de que o arquivo seja salvo em um local que possa ser acessado pelo servidor DPM. Se ele estiver armazenado em um compartilhamento de arquivos/SMB, verifique as permissões de acesso.
- As credenciais do cofre expiram após 48 horas. Você pode fazer o download das novas credenciais do cofre tantas vezes quantas forem necessárias. No entanto, apenas o último arquivo de credencial de cofre pode ser usado durante o fluxo de trabalho de registro.
- O serviço de Backup do Azure não tem ciência da chave privada do certificado e a chave privada não está disponível no portal ou no serviço.

Faça o download do arquivo de credenciais do cofre para um computador local da seguinte maneira:

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Abra o cofre no qual você deseja registrar o servidor DPM.
3. Em **configurações**, selecione **Propriedades**.

    ![Menu Abrir Cofre](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. Em **Propriedades**  >  **credenciais de backup**, selecione **baixar**. O portal gera o arquivo de credencial do cofre usando uma combinação do nome do cofre e da data atual e o disponibiliza para download.

    ![Baixar credenciais](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. Selecione **salvar** para baixar as credenciais do cofre para a pasta ou **salvar como** e especifique um local. Demorará um minuto para que o arquivo seja gerado.

## <a name="install-the-backup-agent"></a>Instalar o Agente de Backup

Todos os computadores cujo backup é feito pelo Backup do Azure devem ter o agente de backup (também conhecido como agente do Serviço de Recuperação do Microsoft Azure [MARS]) instalado. Instale o agente no servidor DPM da seguinte maneira:

1. Abra o cofre no qual você deseja registrar o servidor DPM.
2. Em **configurações**, selecione **Propriedades**.

    ![Abrir configurações do cofre](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Na página **Propriedades**, faça download do Agente de Backup do Azure.

    ![Baixar](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

4. Após o download, execute o arquivo MARSAgentInstaller.exe. para instalar o agente no computador do DPM.
5. Selecione uma pasta de instalação e uma pasta de cache para o agente. O espaço livre do local do cache deve ter pelo menos 5% dos dados de backup.
6. Se você usar um servidor proxy para se conectar à Internet, na tela **Configuração de Proxy** , insira os detalhes do servidor proxy. Se você usar um proxy autenticado, insira os detalhes de nome de usuário e senha nessa tela.
7. O agente do Backup do Azure instala o .NET Framework 4.5 e o Windows PowerShell (se não estiverem instalados) para concluir a instalação.
8. Depois que o agente for instalado, você pode **Fechar** a janela.

    ![Fechar](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

## <a name="register-the-dpm-server-in-the-vault"></a>Registrar o servidor DPM no cofre

1. No console do administrador do DPM > **Gerenciamento**, selecione **online**. Selecione **Registrar**. O Assistente do Servidor de Registro será aberto.
2. Em **Configuração de Proxy**, especifique as configurações de proxy conforme necessário.

    ![Configuração de proxy](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
3. Em **Cofre de Backup**, procure e selecione o arquivo de credenciais do cofre que você baixou.

    ![Credenciais do cofre](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

4. Em **Configuração de Limitação**, você pode opcionalmente habilitar a otimização de largura de banda para backups. Você pode definir os limites de velocidade para especificar horas e dias de trabalho.

    ![Configuração de Limitação](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

5. Em **Configuração da Pasta de Recuperação**, especifique um local que possa ser usado durante a recuperação de dados.

    - O Backup do Azure usa esse local como uma área de armazenamento temporário para dados recuperados.
    - Depois de concluir a recuperação de dados, o Backup do Azure limpará os dados nessa área.
    - O local deve ter espaço suficiente para manter os itens que você espera recuperar em paralelo.

    ![Configuração de Pasta de Recuperação](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

6. Em **configuração de criptografia**, gere ou forneça uma frase secreta.

    - A frase secreta é usada para criptografar os backups na nuvem.
    - Especifique um mínimo de 16 caracteres.
    - Salve o arquivo em um local seguro, é necessário para a recuperação.

    ![Criptografia](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Você possui a frase secreta de criptografia e a Microsoft não tem visibilidade sobre ela.
    > Se a senha for perdida ou esquecida, a Microsoft não poderá ajudar a recuperar os dados de backup.

7. Selecione **registrar** para registrar o servidor DPM no cofre.

Depois que o servidor for registrado com êxito no cofre, agora você estará pronto para iniciar o backup em Microsoft Azure. Você precisará configurar o grupo de proteção no console do DPM para fazer backup de cargas de trabalho no Azure. [Saiba como](/system-center/dpm/create-dpm-protection-groups) implantar grupos de proteção.

## <a name="troubleshoot-vault-credentials"></a>Solucionar problemas de credenciais do cofre

### <a name="expiration-error"></a>Erro de expiração

O arquivo de credenciais do cofre é válido somente por 48 horas (após o download do Portal). Se você encontrar algum erro nessa tela (por exemplo, "o arquivo de credenciais do cofre fornecido expirou"), entre no portal do Azure e baixe o arquivo de credenciais do cofre novamente.

### <a name="access-error"></a>Erro de acesso

Verifique se o arquivo de credenciais do cofre está disponível em um local que pode ser acessado pelo aplicativo de instalação. Se você encontrar erros relacionados a acesso, copie o arquivo de credenciais do cofre para um local temporário nesse computador e repita a operação.

### <a name="invalid-credentials-error"></a>Erro de credenciais inválidas

Se você encontrar um erro de credencial de cofre inválido (por exemplo, "credenciais de cofre fornecidas inválidas"), o arquivo estará corrompido ou não terá as credenciais mais recentes associadas ao serviço de recuperação.

- Repita a operação depois de baixar um novo arquivo de credencial de cofre no portal.
- Esse erro geralmente é visto quando você seleciona a opção **baixar credencial do cofre** na portal do Azure, duas vezes em sucessão rápida. Nesse caso, apenas o segundo arquivo de credencial de cofre é válido.
