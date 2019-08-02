---
title: Preparar o servidor de DPM para fazer backup das cargas de trabalho no Azure
description: Uma introdução ao backup de dados do DPM em um cofre dos Serviços de Recuperação do Azure.
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: kasinh
ms.openlocfilehash: 09c324fa1c24c56ede431fab8e175b166a52b438
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466572"
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

O [System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview) faz backup dos dados de arquivos e aplicativos. O DPM interage com o Backup do Azure da seguinte maneira:

* **DPM em execução em um servidor físico ou VM local**: você pode fazer backup de dados em um cofre de backup no Azure, além de backup em disco e em fita.
* **DPM em execução em uma VM do Azure**: a partir do System Center 2012 R2 com a Atualização 3 ou posterior, você pode implantar o DPM em uma VM do Azure. Você pode fazer backup de dados em discos do Azure conectados à VM ou usar o Backup do Azure para fazer backup dos dados em um cofre de backup.

Os benefícios comerciais do backup de servidores DPM para o Azure incluem:

* Para implantação do DPM no local, o Backup do Azure oferece uma alternativa à implantação de longo prazo em fita.
* Para o DPM em execução em uma VM do Azure, o Backup do Azure permite descarregar o armazenamento do disco do Azure. O armazenamento de dados mais antigos no cofre de Backup permite escalar verticalmente os negócios, armazenando novos dados no disco.

## <a name="prerequisites-and-limitations"></a>Pré-requisitos e limitações

**Configuração** | **Requisito**
--- | ---
DPM em uma VM do Azure | System Center 2012 R2 com o Pacote Cumulativo de Atualizações 3 ou posterior do DPM 2012 R2.
DPM em um servidor físico | System Center 2012 SP1 ou posterior; System Center 2012 R2.
DPM em uma VM do Hyper-V | System Center 2012 SP1 ou posterior; System Center 2012 R2.
DPM em uma VM do VMware | System Center 2012 R2 com o Pacote Cumulativo de Atualizações 5 ou posterior.
Componentes | O servidor DPM deve ter o Windows PowerShell e o .NET Framework 4,5 instalados.
Aplicativos com suporte | [Saiba](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix) o que o DPM pode incluir no backup.
Tipos de arquivo com suporte | É possível fazer backup destes tipos de arquivo com o Backup do Azure: Criptografado (apenas backups completos); Compactados (suporte para backups incrementais); Esparsos (suporte para backups incrementais); Compactados e esparsos (tratados como esparsos).
Tipos de arquivo sem suporte | Servidores em sistemas de arquivos com diferenciação de maiúsculas e minúsculas; links rígidos (ignorados); pontos de nova análise (ignorados); criptografados e compactados (ignorados); criptografados e esparsos (ignorados); fluxo comprimido; fluxo de análise.
Armazenamento local | Cada máquina da qual você deseja fazer backup deve ter armazenamento livre local com pelo menos 5% do tamanho dos dados que estão sendo armazenados em backup. Por exemplo, um backup de 100 GB de dados requer um mínimo de 5 GB de espaço livre na localização temporária.
Armazenamento de cofre | Não há nenhum limite para a quantidade de dados de backup em um cofre de Backup do Azure, mas o tamanho de uma fonte de dados (por exemplo, máquina virtual ou banco de dados) não deve ultrapassar 54400 GB.
Azure ExpressRoute | Se o Azure ExpressRoute estiver configurado com o emparelhamento privado ou da Microsoft, ele não poderá ser usado para fazer backup dos dados no Azure.<br/><br/> Se o Azure ExpressRoute estiver configurado com o emparelhamento público, ele poderá ser usado para fazer backup dos dados no Azure.<br/><br/> **Observação:** O emparelhamento público foi preterido para novos circuitos.
Agente de Backup do Azure | Se o DPM estiver sendo executado no System Center 2012 SP1, instale o pacote cumulativo de atualizações 2 ou posterior para o DPM SP1. Isso é necessário para a instalação do agente.<br/><br/> Este artigo descreve como implantar a versão mais recente do agente do Backup do Azure, também conhecido como agente do Serviço de Recuperação do Microsoft Azure (MARS). Se você tiver uma versão anterior implantada, atualize para a versão mais recente para garantir que o backup funcione conforme o esperado.

Antes de começar, você precisará de uma conta Azure com o recurso de Backup do Azure habilitado. Se não tiver uma conta, você poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Leia sobre os [preços do Backup do Azure](https://azure.microsoft.com/pricing/details/backup/).

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-settings"></a>Modificar configurações de armazenamento

Você pode escolher entre o armazenamento com redundância geográfica e armazenamento com redundância local.

- Por padrão, seu cofre tem armazenamento com redundância geográfica.
- Se o cofre for seu backup principal, deixe a opção definida como armazenamento com redundância geográfica. Se você quiser uma opção mais barata que não seja tão durável, use o procedimento a seguir para configurar o armazenamento com redundância local.
- Saiba mais sobre o [armazenamento do Azure](../storage/common/storage-redundancy.md), e as opções de armazenamento com [redundância geográfica](../storage/common/storage-redundancy-grs.md) e [redundância local](../storage/common/storage-redundancy-lrs.md).
- Modifique as configurações de armazenamento antes do backup inicial. Se você já fez o backup de um item, pare de fazer o backup no cofre antes de modificar as configurações de armazenamento.

Para editar a configuração de replicação de armazenamento:

1. Abra o painel do cofre.

2. Em **Gerenciar**, clique em **Infraestrutura de Backup**.

3. No menu **Configuração de Backup**, selecione uma opção de armazenamento para o cofre.

    ![Lista de cofres de backup](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

## <a name="download-vault-credentials"></a>Baixar as credenciais do cofre

Você usa credenciais do cofre quando registra o servidor DPM no cofre.

- O arquivo de credenciais do cofre é um certificado gerado pelo portal para cada cofre de backup.
- O portal, em seguida, carrega a chave pública no ACS (Serviço de Controle de Acesso).
- Durante o fluxo de trabalho de registro do computador, a chave privada do certificado é disponibilizada para o usuário, o que autentica o computador.
- Com base na autenticação, o serviço de Backup do Azure envia dados para o cofre identificado.

### <a name="best-practices-for-vault-credentials"></a>Melhores práticas para credenciais do cofre

Para obter as credenciais, você faz o download do arquivo da credencial do cofre por meio de um canal seguro no Portal do Azure:

- As credenciais do cofre são usadas somente durante o fluxo de trabalho de registro.
- É sua responsabilidade garantir que o arquivo de credenciais do cofre esteja seguro e não seja comprometido.
    - Se o controle das credenciais for perdido, as credenciais do cofre poderão ser usadas para registrar outros computadores no cofre.
    - No entanto, os dados de backup são criptografados usando uma frase secreta que pertence ao cliente, por isso os dados de backup existentes não poderão ser comprometidos.
- Salve o arquivo em um local que possa ser acessado no servidor DPM. Se forem armazenadas em um compartilhamento de arquivos/SMB, verifique as permissões de acesso.
- As credenciais do cofre expiram após 48 horas. Você pode fazer o download das novas credenciais do cofre tantas vezes quantas forem necessárias. No entanto, apenas o último arquivo de credencial de cofre pode ser usado durante o fluxo de trabalho de registro.
- O serviço de Backup do Azure não tem ciência da chave privada do certificado e a chave privada não está disponível no portal ou no serviço.

Faça o download do arquivo de credenciais do cofre para um computador local da seguinte maneira:

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Abra o cofre no qual você deseja registrar o servidor DPM.
3. Em **Configurações**, clique em **Propriedades**.

    ![Menu Abrir Cofre](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. Em **Propriedades** > **Credenciais de Backup**, clique em **Baixar**. O portal gera o arquivo de credencial do cofre usando uma combinação do nome do cofre e da data atual e o disponibiliza para download.

    ![Baixar](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. Clique em **Salvar** para fazer download das credenciais do cofre para a pasta, ou **Salvar como** e especifique um local. Demorará um minuto para que o arquivo seja gerado.


## <a name="install-the-backup-agent"></a>Instalar o Agente de Backup

Todos os computadores cujo backup é feito pelo Backup do Azure devem ter o agente de backup (também conhecido como agente do Serviço de Recuperação do Microsoft Azure [MARS]) instalado. Instale o agente no servidor DPM da seguinte maneira:

1. Abra o cofre no qual você deseja registrar o servidor DPM.
2. Em **Configurações**, clique em **Propriedades**.

    ![Menu Abrir Cofre](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Na página **Propriedades**, faça download do Agente de Backup do Azure.

    ![Baixar](./media/backup-azure-dpm-introduction/azure-backup-agent.png)


4. Após o download, execute o arquivo MARSAgentInstaller.exe. para instalar o agente no computador do DPM.
5. Selecione uma pasta de instalação e uma pasta de cache para o agente. O espaço livre do local do cache deve ter pelo menos 5% dos dados de backup.
6. Se você usar um servidor proxy para se conectar à Internet, na tela **Configuração de Proxy** , insira os detalhes do servidor proxy. Se você usar um proxy autenticado, insira os detalhes de nome de usuário e senha nessa tela.
7. O agente do Backup do Azure instala o .NET Framework 4.5 e o Windows PowerShell (se não estiverem instalados) para concluir a instalação.
8. Depois que o agente for instalado, você pode **Fechar** a janela.

    ![Feche](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

## <a name="register-the-dpm-server-in-the-vault"></a>Registrar o servidor DPM no cofre

1. No console do Administrador do DPM > **Gerenciamento**, clique em **Online**. Selecione **Registrar**. O Assistente do Servidor de Registro será aberto.
2. Em  **Configuração de Proxy**, especifique as configurações de proxy conforme necessário.

    ![Configuração de Proxy](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
9. Em **Cofre de Backup**, procure e selecione o arquivo de credenciais do cofre que você baixou.

    ![Credenciais do cofre](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

10. Em **Configuração de Limitação**, você pode opcionalmente habilitar a otimização de largura de banda para backups. Você pode definir os limites de velocidade para especificar horas e dias de trabalho.

    ![Configuração de Limitação](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

11. Em **Configuração da Pasta de Recuperação**, especifique um local que possa ser usado durante a recuperação de dados.

    - O Backup do Azure usa esse local como uma área de armazenamento temporário para dados recuperados.
    - Depois de concluir a recuperação de dados, o Backup do Azure limpará os dados nessa área.
    - O local deve ter espaço suficiente para armazenar os itens que você espera recuperar em paralelo.

    ![Configuração de Pasta de Recuperação](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

12. Em **Configuração de Criptografia**, gere ou forneça uma frase secreta.

    - A frase secreta é usada para criptografar os backups na nuvem.
    - Especifique um mínimo de 16 caracteres.
    - Salve o arquivo em um local seguro, é necessário para a recuperação.

    ![Criptografia](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Você possui a frase secreta de criptografia e a Microsoft não tem visibilidade sobre ela.
    > Se a senha for perdida ou esquecida, a Microsoft não poderá ajudar na recuperação dos dados de backup.

13. Clique em **Registrar** para registrar o servidor DPM no cofre.

Depois, o servidor é registrado com êxito no cofre e você está pronto para iniciar o backup no Microsoft Azure.

## <a name="troubleshoot-vault-credentials"></a>Solucionar problemas de credenciais do cofre

### <a name="expiration-error"></a>Erro de expiração

O arquivo de credenciais do cofre é válido somente por 48 horas (após o download do portal). Se você encontrar qualquer erro nessa tela (por exemplo "o arquivo de credenciais do cofre fornecido expirou"), faça logon no Portal do Azure e baixe o arquivo de credenciais do cofre novamente.

### <a name="access-error"></a>Erro de acesso

Certifique-se de que o arquivo de credenciais do cofre esteja disponível em um local que pode ser acessado pelo aplicativo de instalação. Se você encontrar erros relacionados a acesso, copie o arquivo de credenciais do cofre para um local temporário nesse computador e repita a operação.

### <a name="invalid-credentials-error"></a>Erro de credenciais inválidas

Se você encontrar um erro de credencial de cofre inválida (por exemplo, “Credenciais do cofre fornecidas inválidas”), significa que o arquivo está corrompido ou não tem as últimas credenciais associadas ao serviço de recuperação.

- Repita a operação depois de baixar um novo arquivo de credencial de cofre no portal.
- Esse erro geralmente é visto ao clicar na opção **Baixar credencial de cofre** no Portal do Azure, duas vezes em rápida sucessão. Nesse caso, apenas o segundo arquivo de credencial de cofre é válido.
