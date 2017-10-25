---
title: 'Azure AD Connect: atualizar do DirSync | Microsoft Docs'
description: Saiba como atualizar do DirSync para o Azure AD Connect. Este artigo descreve as etapas para atualizar do DirSync para o Azure AD Connect.
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: baf52da7-76a8-44c9-8e72-33245790001c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 7049af4567947d3d799a38c5a3940ba25a2c0f18
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-upgrade-from-dirsync"></a>Azure AD Connect: atualizar do DirSync
O Azure AD Connect é o sucessor do DirSync. Neste tópico, você conhecerá as maneiras de atualizar do DirSync. Essas etapas não funcionam para atualizar de outra versão do Azure AD Connect ou do Azure AD Sync.

Antes de iniciar a instalação do Azure AD Connect, [baixe o Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) e conclua as etapas de pré-requisito em [Azure AD Connect: hardware e pré-requisitos](active-directory-aadconnect-prerequisites.md). Em particular, leia o seguinte, pois essas áreas são diferentes do DirSync:

* A versão necessária do .Net e do PowerShell. Versões mais recentes do que aquelas de que DirSync precisa devem estar no servidor.
* A configuração do servidor proxy. Se você usa um servidor proxy para acessar a Internet, essa configuração deve ser definida antes da atualização. DirSync sempre usava o servidor proxy configurado para o usuário que o instalava, mas o Azure AD Connect usa as configurações do computador em vez disso.
* As URLs que precisam ser abertas no servidor proxy. Para cenários básicos, estes cenários, também com suporte no DirSync, os requisitos são os mesmos. Se você quiser usar qualquer um dos novos recursos incluídos no Azure AD Connect, algumas novas URLs deverão ser abertas.

> [!NOTE]
> Depois que você tiver ativado o novo servidor do Azure AD Connect para iniciar a sincronização de alterações para o Azure AD, não deverá reverter usando o DirSync ou o Azure AD Sync. Fazer downgrade do Azure AD Connect para clientes herdados, incluindo o DirSync e o Azure AD Sync, não tem suporte e pode levar a problemas como a perda de dados no Azure AD.

Se você não estiver atualizando do DirSync, confira a [documentação relacionada](#related-documentation) para outros cenários.

## <a name="upgrade-from-dirsync"></a>Atualizar do DirSync
Dependendo de sua implantação atual do DirSync, há diferentes opções de atualização. Se o tempo de atualização esperado for menor que três horas, a recomendação será fazer uma atualização in-loco. Se o tempo de atualização esperado for maior do que três horas, a recomendação será fazer uma implantação paralela em outro servidor. Estima-se que se você tiver mais de 50.000 objetos, a atualização levará mais de três horas.

| Cenário |
| --- | --- |
| [Atualização in-loco](#in-place-upgrade) |
| [Implantação paralela](#parallel-deployment) |

> [!NOTE]
> Quando você planejar atualizar do DirSync para o Azure AD Connect, não desinstale o DirSync por conta própria antes da atualização. O Azure AD Connect lerá e migrará a configuração do DirSync e a desinstalará depois de inspecionar o servidor.

**Atualização in-loco**  
O tempo estimado para concluir a atualização é exibido pelo assistente. Essa estimativa se baseia na suposição de que serão necessárias três horas para concluir uma atualização para um banco de dados com 50.000 objetos (usuários, contatos e grupos). Se o número de objetos no banco de dados é menor do que 50.000, o Azure AD Connect recomenda uma atualização in-loco. Se você decidir continuar, suas configurações atuais serão aplicadas automaticamente durante a atualização e o servidor reiniciará automaticamente a sincronização ativa.

Se você quiser fazer uma migração da configuração e uma implantação paralela, pode substituir a recomendação de atualização in-loco. Por exemplo, você pode aproveitar para atualizar o hardware e o sistema operacional. Para saber mais, veja a seção [implantação paralela](#parallel-deployment).

**Implantação paralela**  
Se você tiver mais de 50.000 objetos, é recomendável uma implantação paralela. Isso evita que atrasos operacionais sejam percebidos pelos usuários. A instalação do Azure AD Connect tenta estimar o tempo de inatividade da atualização, mas, se você tiver atualizado o DirSync no passado, sua própria experiência provavelmente será o melhor guia.

### <a name="supported-dirsync-configurations-to-be-upgraded"></a>Configurações compatíveis com o DirSync a serem atualizadas
As seguintes alterações de configuração são compatíveis com o DirSync atualizado:

* Domínio e filtragem de unidade organizacional
* ID alternativa (UPN)
* Sincronização de senha e configurações híbridas do Exchange
* Suas configurações de domínio/floresta e do AD do Azure
* Filtragem com base em atributos de usuário

As alterações a seguir não podem ser atualizadas. Se você tiver essa configuração, a atualização será bloqueada:

* Alterações de DirSync sem suporte, por exemplo, atributos removidos e uso de uma DLL de extensão personalizada

![Atualização bloqueada](./media/active-directory-aadconnect-dirsync-upgrade-get-started/analysisblocked.png)

Nesses casos, a recomendação é instalar um novo servidor do Azure AD Connect no [modo de preparo](active-directory-aadconnectsync-operations.md#staging-mode) e verificar a antiga configuração do DirSync e a nova configuração do Azure AD Connect. Aplique qualquer alteração novamente usando uma configuração personalizada, como descrito em [Configuração personalizada de sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Senhas usadas pelo DirSync para as contas de serviço não podem ser recuperadas e não são migradas. Essas senhas são redefinidas durante a atualização.

### <a name="high-level-steps-for-upgrading-from-dirsync-to-azure-ad-connect"></a>Etapas de alto nível para atualização do DirSync para o Azure AD Connect
1. Bem-vindo ao Azure AD Connect
2. Análise da configuração do DirSync atual
3. Coletar senha de administrador global do AD do Azure
4. Coletar credenciais para uma conta de administrador corporativo (usado somente durante a instalação do Azure AD Connect)
5. Instalação do Azure AD Connect
   * Desinstalar o DirSync (ou desabilitá-lo temporariamente)
   * Instalar o Azure AD Connect
   * Opcionalmente, inicie a sincronização

Etapas adicionais são necessárias quando:

* Você está usando no momento o SQL Server completo - local ou remoto
* Você tem mais de 50.000 objetos no escopo para sincronização

## <a name="in-place-upgrade"></a>Atualização in-loco
1. Iniciar o instalador do Azure AD Connect (MSI).
2. Leia e concorde com os termos de licença e o aviso de privacidade.  
   ![Bem-vindo ao AD do Azure](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Welcome.png)
3. Clique em Avançar para começar a análise de sua instalação do DirSync existente.  
   ![Analisando instalação de sincronização de diretório existente](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Analyze.png)
4. Quando a análise for concluída, você verá as recomendações sobre como proceder.  
   * Se você usar o SQL Server Express e tiver menos de 50.000 objetos, a seguinte tela será mostrada:   
     ![Análise concluída pronta para atualizar a partir do DirSync](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReady.png)
   * Se você usar um SQL Server completo para DirSync, verá esta página:  
     ![Análise concluída pronta para atualizar a partir do DirSync](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png)  
     São exibidas as informações sobre o servidor de banco de dados existente do SQL Server que está sendo usado pelo DirSync. Faça os ajustes apropriados se necessário. Clique em **Avançar** para continuar a instalação.
   * Se você tiver mais de 50.000 objetos, verá esta tela:  
     ![Análise concluída pronta para atualizar a partir do DirSync](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)  
     Para continuar com a atualização in-loco, clique na caixa de seleção junto à mensagem: **Continuar atualizando o DirSync neste computador.**
     Para fazer uma [parallel deployment](#parallel-deployment) , você exporta as definições de configuração do DirSync e move a configuração para o novo servidor.
5. Forneça a senha da conta que você usa atualmente para se conectar ao AD do Azure. Ela precisa ser a conta usada atualmente pelo DirSync.  
   ![Insira suas credenciais de AD do Azure](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToAzureAD.png)  
   Se você encontrar um erro e tiver problemas de conectividade, confira [Solucionar problemas de conectividade](active-directory-aadconnect-troubleshoot-connectivity.md).
6. Forneça uma conta de administrador corporativo para o Active Directory.  
   ![Inserir suas credenciais do ADDS](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToADDS.png)
7. Agora você está pronto para configurar. Quando você clica em **Atualizar**, DirSync é desinstalado e o Azure AD Connect é configurado e começa a sincronização.  
   ![Pronto para configurar](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ReadyToConfigure.png)
8. Após a conclusão da instalação, saia e entre novamente no Windows antes de usar o Synchronization Service Manager ou o Synchronization Rule Editor, ou tentar fazer outras alterações de configuração.

## <a name="parallel-deployment"></a>Implantação paralela
### <a name="export-the-dirsync-configuration"></a>Exportar a configuração do DirSync
**Implantação paralela com mais de 50.000 objetos**

Se você tiver mais de 50.000 objetos, a instalação do Azure AD Connect recomendará uma implantação paralela.

Será exibida uma tela semelhante à seguinte:  
![Análise concluída](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

Se quiser continuar com a implantação paralela, você precisará executar as seguintes etapas:

* Clique no botão **Exportar configurações** . Quando você instala o Azure AD Connect em um servidor separado, essas configurações são migradas de seu DirSync atual para a nova instalação do Azure AD Connect.

Após as configurações terem sido exportadas com êxito, você pode sair do Assistente de conexão do Azure AD Connect no servidor DirSync. Siga para a próxima etapa para [instalar o Azure AD Connect em um servidor separado](#installation-of-azure-ad-connect-on-separate-server)

**Implantação paralela com menos de 50.000 objetos**

Se você tiver menos de 50.000 objetos, mas ainda quiser fazer uma implantação paralela, faça o seguinte:

1. Execute o instalador do Azure AD Connect (MSI).
2. Quando você vir a tela **Bem-vindo ao Azure AD Connect** , saia do assistente de instalação clicando no “X” no canto superior direito da janela.
3. Abra um prompt de comando.
4. No local de instalação do Azure AD Connect (Padrão: C:\Program Files\Microsoft Azure Active Directory Connect), execute o seguinte comando: `AzureADConnect.exe /ForceExport`.
5. Clique no botão **Exportar configurações** . Quando você instala o Azure AD Connect em um servidor separado, essas configurações são migradas de seu DirSync atual para a nova instalação do Azure AD Connect.

![Análise concluída](./media/active-directory-aadconnect-dirsync-upgrade-get-started/forceexport.png)

Após as configurações terem sido exportadas com êxito, você pode sair do Assistente de conexão do Azure AD Connect no servidor DirSync. Siga para a próxima etapa para [instalar o Azure AD Connect em um servidor separado](#installation-of-azure-ad-connect-on-separate-server).

### <a name="install-azure-ad-connect-on-separate-server"></a>Instalar o Azure AD Connect em um servidor separado
Quando você instala o Azure AD Connect em um novo servidor, a premissa é que você deseja executar uma instalação limpa do Azure AD Connect. Como você quer usar a configuração do DirSync, há algumas etapas adicionais a serem executadas:

1. Execute o instalador do Azure AD Connect (MSI).
2. Quando você vir a tela **Bem-vindo ao Azure AD Connect** , saia do assistente de instalação clicando no “X” no canto superior direito da janela.
3. Abra um prompt de comando.
4. No local de instalação do Azure AD Connect (padrão: C:\Arquivos de Programas\Microsoft Azure Active Directory Connect), execute o seguinte comando: `AzureADConnect.exe /migrate`.
   O assistente de instalação do Azure AD Connect é iniciado e exibe a seguinte tela:  
   ![Insira suas credenciais de AD do Azure](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ImportSettings.png)
5. Selecione o arquivo de configurações exportado de sua instalação DirSync.
6. Configure as opções avançadas incluindo:
   * Um local de instalação personalizada para o Azure AD Connect.
   * Uma instância existente do SQL Server (padrão: o Azure AD Connect instala o SQL Server 2012 Express). Não use a mesma instância de banco de dados que o servidor DirSync.
   * Uma conta de serviço usada para conectar-se ao SQL Server (se o banco de dados do SQL Server for remoto, essa conta deverá ser uma conta de serviço de domínio).
     Essas opções podem ser vistas nesta tela:   
     ![Insira suas credenciais de AD do Azure](./media/active-directory-aadconnect-dirsync-upgrade-get-started/advancedsettings.png)
7. Clique em **Avançar**.
8. Na página **Pronto para configurar**, deixe a opção **Iniciar o processo de sincronização assim que a configuração for concluída** marcada. O servidor está agora no [modo de preparo](active-directory-aadconnectsync-operations.md#staging-mode) , assim, as alterações não são exportadas para o Azure AD.
9. Clique em **Instalar**.
10. Após a conclusão da instalação, saia e entre novamente no Windows antes de usar o Synchronization Service Manager ou o Synchronization Rule Editor, ou tentar fazer outras alterações de configuração.

> [!NOTE]
> A sincronização entre o Active Directory do Windows Server e o Azure Active Directory começa, mas nenhuma alteração é exportada para o Azure AD. Apenas uma ferramenta de sincronização pode exportar ativamente alterações de cada vez. Esse estado é chamado de [modo de preparo](active-directory-aadconnectsync-operations.md#staging-mode).

### <a name="verify-that-azure-ad-connect-is-ready-to-begin-synchronization"></a>Verificar se o Azure AD Connect está pronto para iniciar a sincronização
Para determinar se o Azure AD Connect está pronto para assumir o controle do DirSync, você precisa abrir o **Gerenciador do Serviço de Sincronização** no grupo **Azure AD Connect** do menu Iniciar.

No aplicativo, vá para a guia **Operações** . Nessa guia, confirme que você concluiu as seguintes operações:

* Importação no AD Connector
* Importação no Azure AD Connector
* Sincronização completa no AD Connector
* Sincronização completa no Azure AD Connector

![Importação e sincronização concluídas](./media/active-directory-aadconnect-dirsync-upgrade-get-started/importsynccompleted.png)

Examine o resultado dessas operações e certifique-se de que não há erros.

Para ver e inspecionar as alterações que estão prestes a serem exportadas para o AD do Azure, leia sobre como verificar a configuração em [modo de preparo](active-directory-aadconnectsync-operations.md#staging-mode). Faça alterações de configuração necessárias até que você não veja nada inesperado.

Você estará pronto para alternar do DirSync para o Azure AD quando concluir essas etapas e estiver satisfeito com o resultado.

### <a name="uninstall-dirsync-old-server"></a>Desinstalar o DirSync (servidor antigo)
* Em **Programas e recursos**, localize a **ferramenta de sincronização do Windows Azure Active Directory**
* Desinstale a **Ferramenta de sincronização do Microsoft Azure Active Directory**
* A desinstalação pode levar até 15 minutos para ser concluída.

Se preferir desinstalar o DirSync posteriormente, você também poderá desligar temporariamente o servidor ou desabilitar o serviço. Se algo der errado, esse método permitirá que você o habilite novamente. No entanto, não é esperado que a próxima etapa tenha falhas, então, isso não deve ser necessário.

Com o DirSync desinstalado ou desabilitado, não há nenhum servidor ativo exportando para o AD do Azure. A próxima etapa para habilitar o Azure AD Connect deve ser concluída antes que qualquer alteração no seu Active Directory local continuará a ser sincronizado com o AD do Azure.

### <a name="enable-azure-ad-connect-new-server"></a>Habilitar o Azure AD Connect (novo servidor)
Após a instalação, a reabertura do Azure AD Connect permitirá fazer alterações de configuração adicionais. Inicie o **Azure AD Connect** no menu iniciar ou em um atalho na área de trabalho. Não tente executar novamente a instalação MSI.

Você deve ver o seguinte:  
![Tarefas adicionais](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AdditionalTasks.png)

* Selecione **Configurar modo de preparo**.
* Desative o preparo desmarcando a caixa de seleção **Modo de preparo habilitado** .

![Insira suas credenciais de AD do Azure](./media/active-directory-aadconnect-dirsync-upgrade-get-started/configurestaging.png)

* Clique no botão **Avançar**
* Na página de confirmação, clique no botão **Instalar** .

O Azure AD Connect é agora o servidor ativo e você não deve voltar a usar o servidor DirSync existente.

## <a name="next-steps"></a>Próximas etapas
Agora que você tem o Azure AD Connect instalado, é possível [verificar a instalação e atribuir licenças](active-directory-aadconnect-whats-next.md).

Saiba mais sobre estes novos recursos, que foram habilitados com a instalação: [Atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md), [Impedir exclusões acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) e [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-sync.md).

Saiba mais sobre estes tópicos comuns: [Agendador e como disparar a sincronização](active-directory-aadconnectsync-feature-scheduler.md).

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).
