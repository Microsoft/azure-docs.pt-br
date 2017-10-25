---
title: "Usar o Servidor de Backup do Azure para fazer backup de cargas de trabalho no Portal Clássico do Azure | Microsoft Docs"
description: "Tenha certeza de que seu ambiente está preparado adequadamente para fazer backup de cargas de trabalho usando o Servidor de Backup do Azure."
services: backup
documentationcenter: 
author: pvrk
manager: shivamg
editor: 
keywords: servidor de backup do Azure; cofre de backup
ms.assetid: d86450e8-da63-4283-8fd7-2ee629fa14ab
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: masaran;trinadhk;pullabhk;markgal
ms.openlocfilehash: ffef289e154986e4b08a072d3a95f77818fb9c35
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="preparing-to-back-up-workloads-using-azure-backup-server"></a>Preparação para fazer backup de cargas de trabalho usando o Servidor de Backup do Azure
> [!div class="op_single_selector"]
> * [Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
> * [Servidor de Backup do Azure (clássico)](backup-azure-microsoft-azure-backup-classic.md)
> * [SCDPM (clássico)](backup-azure-dpm-introduction-classic.md)
>
>

Este artigo é sobre a preparação do seu ambiente para fazer backup de cargas de trabalho usando o Servidor de Backup do Azure. Com o Servidor de Backup do Azure, você pode proteger cargas de trabalho de aplicativo como VMs do Hyper-V, o Microsoft SQL Server, o SharePoint Server, o Microsoft Exchange e os clientes de um único console.

> [!WARNING]
> O Servidor de Backup do Azure herda a funcionalidade do Data Protection Manager (DPM) para backup de cargas de trabalho. Você encontrará ponteiros para a documentação DPM para alguns desses recursos. No entanto, o Servidor de Backup do Azure não oferece proteção em fita ou se integra ao System Center.
>
>

## <a name="1-windows-server-machine"></a>1. Computador do Windows Server
![etapa1](./media/backup-azure-microsoft-azure-backup/step1.png)

A primeira etapa para colocar o Servidor de Backup do Azure em execução é ter um computador do Windows Server.

| Local | Requisitos mínimos | Instruções adicionais |
| --- | --- | --- |
| As tabelas |Máquina virtual de IaaS do Azure<br><br>A2 Standard: 2 núcleos, 3,5 GB de RAM |Você pode começar com uma única imagem da galeria do Windows Server 2012 R2 Datacenter. [Protegendo cargas de trabalho IaaS usando o Servidor de Backup do Azure (DPM)](https://technet.microsoft.com/library/jj852163.aspx) apresenta várias nuances. Certifique-se de ler o artigo na íntegra antes de implantar a máquina. |
| Configuração local |VM do Hyper-V,<br> VM do VMWare<br> ou um host físico<br><br>2 núcleos e 4 GB de RAM |Você pode eliminar duplicadas do armazenamento DPM usando a Eliminação de Duplicação do Windows Server. Saiba mais sobre como o [DPM e a eliminação de duplicação](https://technet.microsoft.com/library/dn891438.aspx) funcionam juntos quando implantados em VMs do Hyper-V. |

> [!NOTE]
> É recomendável que o Servidor de Backup do Azure esteja instalado em um computador com Windows Server 2012 R2 Datacenter. Muitos dos pré-requisitos são automaticamente abrangidos pela versão mais recente do sistema operacional Windows.
>
>

Se você planeja ingressar o Servidor de Backup do Azure em um domínio, recomendamos que você ingresse o servidor físico ou máquina virtual no domínio antes de instalar o software do Servidor de Backup do Azure. *Não há suporte* para a movimentação de um Servidor de Backup do Azure para um novo domínio após a implantação.

## <a name="2-backup-vault"></a>2. Cofre de backup
![etapa2](./media/backup-azure-microsoft-azure-backup/step2.png)

Se você envia os dados de backup para o Azure ou os mantêm localmente, o Servidor de Backup do Azure deve ser registrado em um cofre. Se você é um novo usuário do Backup do Azure e deseja usar o Servidor de Backup do Azure, consulte a versão do portal do Azure deste artigo – [Preparar-se para fazer backup de cargas de trabalho usando o Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md).

> [!IMPORTANT]
> A partir de março de 2017, você não poderá mais usar o portal clássico para criar os cofres de Backup.
> Agora você pode atualizar os cofres de Backup para cofres dos Serviços de Recuperação. Para obter detalhes, veja o artigo [Atualizar um cofre de Backup para um cofre dos Serviços de Recuperação](backup-azure-upgrade-backup-to-recovery-services.md). A Microsoft incentiva você a atualizar os cofres de Backup para os cofres dos Serviços de Recuperação.<br/> Após 15 de outubro de 2017, você não poderá usar o PowerShell para criar os Cofres do Backup. **Em 1º de novembro de 2017**:
>- Todos os Cofres do Backup restantes serão atualizados automaticamente para os cofres dos Serviços de Recuperação.
>- Você não poderá acessar os dados de backup no portal clássico. Em vez disso, use o portal do Azure para acessar os dados de backup nos cofres dos Serviços de Recuperação.
>



## <a name="3-software-package"></a>3. Pacote de software
![etapa3](./media/backup-azure-microsoft-azure-backup/step3.png)

### <a name="downloading-the-software-package"></a>Baixando o pacote de software
Semelhante às credenciais do cofre, você pode baixar o Backup do Microsoft Azure para cargas de trabalho de aplicativo da **Página de Início Rápido** do cofre de backup.

1. Clique em **Para Cargas de Trabalho do Aplicativo (Disco para Disco para Nuvem)**. Isso levará você até a página do Centro de Download, de onde o pacote de software pode ser baixado.

    ![Tela de boas-vindas do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/dpm-venus1.png)
2. Clique em **Download**.

    ![Centro de download 1](./media/backup-azure-microsoft-azure-backup/downloadcenter1.png)
3. Selecione todos os arquivos e clique em **Avançar**. Baixe todos os arquivos provenientes da página de download do Backup do Microsoft Azure e coloque todos os arquivos na mesma pasta.
   ![Centro de download 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Como o tamanho do download de todos os arquivos juntos é superior a 3G, em um link de download de 10 Mbps link ele poderá levar até 60 minutos para ser concluído.

### <a name="extracting-the-software-package"></a>Extraindo o pacote de software
Depois de baixar todos os arquivos, clique em **MicrosoftAzureBackupInstaller.exe**. Isso iniciará o **Assistente de Instalação do Backup do Microsoft Azure** para extrair os arquivos de instalação para um local especificado. Continue no assistente e clique no botão **Extrair** para iniciar o processo de extração.

> [!WARNING]
> Pelo menos 4 GB de espaço livre são necessários para a extração dos arquivos de instalação.
>
>

![Assistente de Instalação do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Após concluir o processo de extração, marque a caixa para iniciar o *setup.exe* recém-extraído e começar a instalação do Servidor de Backup do Microsoft Azure e clique no botão **Concluir** .

### <a name="installing-the-software-package"></a>Instalando o pacote de software
1. Clique em **Backup do Microsoft Azure** para iniciar o assistente de instalação.

    ![Assistente de Instalação do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Na tela de Boas-Vindas, clique no botão **Avançar** . Isso o levará à seção *Verificações de Pré-Requisitos* . Nessa tela, clique no botão **Verificar** para determinar se os pré-requisitos de hardware e software do Servidor de Backup do Azure foram atendidos. Se todos os pré-requisitos tiverem sido atendidos com êxito, você verá uma mensagem indicando que o computador atende aos requisitos. Clique no botão **Avançar** .

    ![Servidor de Backup do Azure - Boas-vindas e Verificação de pré-requisitos](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. O Servidor de Backup do Microsoft Azure requer o SQL Server Standard e o pacote de instalação do Servidor de Backup do Azure é fornecido com os binários do SQL Server apropriados. Ao iniciar uma nova instalação do Servidor de Backup do Azure, você deverá escolher a opção **Instalar nova Instância do SQL Server com esta Instalação** e clicar no botão **Verificar e Instalar**. Depois que os pré-requisitos tiverem sido instalados com êxito, clique em **Avançar**.

    ![Servidor de Backup do Azure - verificação do SQL](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Se ocorrer uma falha com uma recomendação de reiniciar o computador, faça isso e clique em **Verificar Novamente**.

   > [!NOTE]
   > O Servidor de Backup do Azure não funcionará com uma instância remota do SQL Server. A instância que está sendo usada pelo Servidor de Backup do Azure precisa ser local.
   >
   >

4. Forneça um local para a instalação dos arquivos do servidor de Backup do Microsoft Azure e clique em **Avançar**.

    ![Pré-requisito 2 do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    O local temporário é um requisito para o backup do Azure. Verifique se o local temporário é de pelo menos 5% dos dados planejados para fazer backup na nuvem. Para proteção de disco, será necessário configurar discos separados após a conclusão da instalação. Para saber mais sobre pools de armazenamento, consulte [Configurar os pools de armazenamento e o armazenamento em disco](https://technet.microsoft.com/library/hh758075.aspx).
5. Forneça uma senha forte para as contas de usuário local restritas e clique em **Avançar**.

    ![Pré-requisito 2 do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Selecione se você deseja usar o *Microsoft Update* para verificar se há atualizações e clique em **Avançar**.

   > [!NOTE]
   > É recomendável fazer o Windows Update ser redirecionado para o Microsoft Update, que oferece segurança e atualizações importantes para o Windows e outros produtos, como o Servidor de Backup do Microsoft Azure.
   >
   >

    ![Pré-requisito 2 do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Examine o *Resumo das Configurações* e clique em **Instalar**.

    ![Pré-requisito 2 do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. A instalação acontece em fases. Na primeira fase, o Agente de Serviços de Recuperação do Microsoft Azure é instalado no servidor. O assistente também verifica a conectividade direta com a Internet. Se houver conectividade com a Internet, você poderá prosseguir com a instalação; caso contrário, será necessário fornecer detalhes de proxy para se conectar à Internet.

    A próxima etapa será configurar o Agente de Serviços de Recuperação do Microsoft Azure. Como parte da configuração, você precisará fornecer as credenciais do cofre para registrar o computador no cofre de backup. Você fornecerá uma senha para criptografar/descriptografar os dados enviados entre o Azure e seus locais. Você pode gerar automaticamente uma senha ou fornecer sua própria senha mínima de 16 caracteres. Prossiga com o assistente até o agente ser configurado.

    ![Pré-requisito 2 do Servidor de Backup do Azure](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Assim que o registro do Servidor de Backup do Microsoft Azure for concluído com êxito, o assistente de instalação geral prosseguirá na instalação e na configuração dos componentes do SQL Server e do Servidor de Backup do Azure. Após a conclusão da instalação do componente do SQL Server, os componentes do Servidor de Backup do Azure serão instalados.

    ![Servidor de Backup do Azure](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Quando a etapa de instalação for concluída, os ícones da área de trabalho do produto também terão sido criados. Basta clicar duas vezes no ícone para iniciar o produto.

### <a name="add-backup-storage"></a>Adicionar armazenamento de backup
A primeira cópia de backup é mantida no armazenamento anexado ao computador do Servidor de Backup do Azure. Para saber mais sobre a adição de discos, consulte [Configurar os pools de armazenamento e o armazenamento em disco](https://technet.microsoft.com/library/hh758075.aspx).

> [!NOTE]
> Você precisará adicionar armazenamento de backup mesmo se planejar enviar dados para o Azure. Na arquitetura atual do Servidor de Backup do Azure, o cofre do Backup do Azure mantém a *segunda* cópia dos dados, enquanto o armazenamento local mantém a primeira (e obrigatória) cópia de backup.  
>
>

## <a name="4-network-connectivity"></a>4. Conectividade de rede
![etapa4](./media/backup-azure-microsoft-azure-backup/step4.png)

O Servidor de Backup do Azure requer conectividade com o serviço de Backup Azure para que o produto funcione com êxito. Para validar se o computador tem conectividade com o Azure, use o commandlet ```Get-DPMCloudConnection``` no console do PowerShell do Servidor de Backup do Azure. Se a saída do commandlet for TRUE, haverá conectividade. Caso contrário, não haverá conectividade.

Ao mesmo tempo, a assinatura do Azure deve estar em um estado íntegro. Para descobrir o estado de sua assinatura e gerenciá-la, faça logon no [portal da assinatura](https://account.windowsazure.com/Subscriptions).

Quando você souber o estado da conectividade do Azure e da assinatura do Azure, poderá usar a tabela abaixo para descobrir o impacto sobre a funcionalidade de backup/restauração oferecida.

| Estado de conectividade | Assinatura do Azure | Backup no Azure | Backup em disco | Restaurar do Azure | Restaurar do disco |
| --- | --- | --- | --- | --- | --- |
| Conectado |Ativo |Permitido |Permitido |Permitido |Permitido |
| Conectado |Expirado |Parada |Parada |Permitido |Permitido |
| Conectado |Provisionamento Cancelado |Parada |Parada |Parado e pontos de recuperação do Azure excluídos |Parada |
| Perda de conectividade > 15 dias |Ativo |Parada |Parada |Permitido |Permitido |
| Perda de conectividade > 15 dias |Expirado |Parada |Parada |Permitido |Permitido |
| Perda de conectividade > 15 dias |Provisionamento Cancelado |Parada |Parada |Parado e pontos de recuperação do Azure excluídos |Parada |

### <a name="recovering-from-loss-of-connectivity"></a>Recuperação de perda de conectividade
Se você tiver um firewall ou um proxy que esteja impedindo o acesso do Azure, precisará incluir os seguintes endereços de domínio na lista de permissões no perfil do firewall/proxy:

* www.msftncsi.com
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

Depois que a conectividade com o Azure tiver sido restaurada para o computador do Servidor de Backup do Azure, as operações que puderem ser executadas serão determinadas pelo estado de assinatura do Azure. A tabela acima tem detalhes sobre as operações permitidas depois que a máquina estiver "Conectada".

### <a name="handling-subscription-states"></a>Tratando os estados de assinatura
É possível fazer uma assinatura do Azure passar de um estado *Expirado* ou *Desprovisionado* para o estado *Ativo*. No entanto, isso terá algumas implicações no comportamento do produto enquanto o estado não for *Ativo*:

* Uma assinatura *Desprovisionada* perderá a funcionalidade durante o período em que estiver desprovisionada. Ao se tornar *Ativa*, a funcionalidade de backup/restauração do produto será reativada. Os dados de backup no disco local também poderão ser recuperados caso tenham sido mantidos por um período de retenção suficientemente grande. No entanto, os dados de backup no Azure serão irremediavelmente perdidos depois da assinatura entrar no estado *Desprovisionado* .
* Uma assinatura *Expirada* só perderá a funcionalidade até ficar *Ativa* novamente. Nenhum backup agendado para o período em que a assinatura estiver *Expirada* será executado.

## <a name="troubleshooting"></a>Solucionar problemas
Se o servidor de Backup do Microsoft Azure falha com erros durante a fase de instalação (ou no backup ou na restauração), consulte o [documento de códigos de erro](https://support.microsoft.com/kb/3041338) para saber mais.
Você também pode consultar as [Perguntas frequentes relacionadas ao Backup do Azure](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Próximas etapas
É possível obter informações detalhadas sobre como [preparar seu ambiente para o DPM](https://technet.microsoft.com/library/hh758176.aspx) no site do Microsoft TechNet. Ele também contém informações sobre as configurações com suporte, nas quais o Servidor de Backup do Azure pode ser implantado e usado.

Você pode usar estes artigos para obter um entendimento mais profundo sobre a proteção da carga de trabalho usando o servidor de Backup do Microsoft Azure.

* [Backup do SQL Server](backup-azure-backup-sql.md)
* [Backup do servidor do SharePoint](backup-azure-backup-sharepoint.md)
* [Backup do servidor alternativo](backup-azure-alternate-dpm-server.md)
