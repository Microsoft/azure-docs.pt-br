---
title: Instalação do Azure Functions Runtime | Microsoft Docs
description: Como instalar a versão prévia 2 do Azure Functions Runtime
services: functions
author: apwestgarth
manager: stefsch
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: aae6bc41f3c2fc2c5f8cf63d07f6b4d79bb3564a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61022689"
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>Instale a versão prévia 2 do Azure Functions Runtime

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Se você quiser instalar a versão prévia 2 do Azure Functions Runtime, execute estas etapas:

1. Certifique-se de que sua máquina atenda aos requisitos mínimos.
1. Baixe o [Instalador da visualização do Azure Functions Runtime](https://aka.ms/azafrv2).
1. Desinstale a versão prévia 1 do Azure Functions Runtime.
1. Instale a versão prévia 2 do Azure Functions Runtime.
1. Conclua a configuração da versão prévia 2 do Azure Functions Runtime.
1. Criar sua primeira função na versão prévia do Azure Functions Runtime

## <a name="prerequisites"></a>Pré-requisitos

Antes de instalar a visualização do Azure Functions Runtime, você deve ter os seguintes recursos disponíveis:

1. Um computador executando o Microsoft Windows Server 2016 ou a Atualização do Microsoft Windows 10 para Criadores (Professional ou Enterprise Edition).
1. Uma instância do SQL Server em execução em sua rede.  O requisito mínimo de edição é o SQL Server Express.

## <a name="uninstall-previous-version"></a>Desinstalar a versão anterior

Se você instalou anteriormente a versão prévia de Azure Functions Runtime, deverá desinstalá-la antes de instalar a versão mais recente.  Desinstale a versão prévia do Azure Functions Runtime removendo o programa em Adicionar/Remover Programas no Windows.

## <a name="install-the-azure-functions-runtime-preview"></a>Instalar a visualização do Azure Functions Runtime

O instalador da versão prévia do Azure Functions Runtime orienta você durante a instalação das Funções de Gerenciamento e de Trabalho da versão prévia do Azure Functions Runtime.  É possível instalar as Funções de Gerenciamento e de Trabalho no mesmo computador.  No entanto, à medida que você adiciona mais aplicativos de funções, é necessário implantar mais funções de trabalho em computadores adicionais para poder escalar suas funções para vários trabalhadores.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>Instalar as Funções de Gerenciamento e de Trabalho no mesmo computador

1. Execute o Instalador da visualização do Azure Functions Runtime.

    ![Instalador da versão prévia do Azure Functions Runtime][1]

1. Clique em **Avançar**.
1. Depois de ler os termos do **EULA**, **marque a caixa** para aceitar os termos e clique em **Avançar** para avançar.
1. Selecione as funções que você deseja instalar no computador **Função de Gerenciamento do Functions** e/ou **Função de Trabalho do Functions** e clique em **Avançar**.

    ![Instalador da versão prévia do Azure Functions Runtime - seleção de função][3]

    > [!NOTE]
    > Você pode instalar a **Função de trabalho do Functions** em muitos outros computadores. Para fazer isso, siga estas instruções e só selecione a **Função de trabalho do Functions** no instalador.

1. Clique em **Avançar** para que o **Assistente de instalação do Tempo de Execução do Azure Functions** comece o processo de instalação no seu computador.
1. Após a conclusão, o assistente de instalação inicia a ferramenta de configuração do **Azure Functions Runtime**.

    ![Instalador da versão prévia do Azure Functions Runtime concluída][6]

    > [!NOTE]
    > Se você estiver instalando no **Windows 10** e o recurso **Contêiner** não tiver sido habilitado, a **Configuração do Azure Functions Runtime** solicitará a reinicialização do computador para concluir a instalação.

## <a name="configure-the-azure-functions-runtime"></a>Configurar o Azure Functions Runtime

Para concluir a instalação do Azure Functions Runtime, você deverá concluir a configuração.

1. A **ferramenta de Configuração do Azure Functions Runtime** mostra quais funções estão instaladas em seu computador.

    ![Ferramenta de configuração da versão prévia do Azure Functions Runtime][7]

1. Clique na guia **Banco de Dados**, insira os detalhes de conexão para a instância do SQL Server, incluindo a especificação de uma [chave mestra de banco de dados](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine)e clique em **Aplicar**.  A conectividade com uma instância do SQL Server é necessária para que o Azure Functions Runtime crie um banco de dados a fim de dar suporte ao Runtime.

    ![Configuração de banco de dados da versão prévia do Azure Functions Runtime][8]

1. Clique na guia **Credenciais**.  Aqui, você deve criar duas novas credenciais para uso com um compartilhamento de arquivos para hospedar todos os seus aplicativos de funções.  Especifique combinações de **Nome de usuário** e **Senha** para o **proprietário do compartilhamento de arquivo** e para o **usuário do compartilhamento de arquivo** e clique em **Aplicar**.

    ![Credenciais da versão prévia do Azure Functions Runtime][9]

1. Clique na guia **Compartilhamento de Arquivos**.  Aqui, você deve especificar os detalhes do local do compartilhamento de arquivos.  O compartilhamento de arquivos pode ser criado para você, ou você pode usar um Compartilhamento de Arquivo existente e clicar em **Aplicar**.  Se você selecionar um novo local de Compartilhamento de Arquivos, especifique um diretório para ser usado pelo Azure Functions Runtime.

    ![Compartilhamento de arquivo da versão prévia do Azure Functions Runtime][10]

1. Clique na guia **IIS**.  Essa guia mostra os detalhes dos sites no IIS que serão criados pela ferramenta de configuração do Azure Functions Runtime.  Você pode especificar um nome DNS personalizado aqui para o portal da versão prévia do Azure Functions Runtime.  Clique em **Aplicar** para concluir.

    ![IIS da versão prévia do Azure Functions Runtime][11]

1. Clique na guia **Serviços**.  Essa guia mostra o status dos serviços em sua instalação do Tempo de Execução do Azure Functions.  Se o **Serviço de Ativação de Host do Azure Functions** não estiver em execução após a configuração inicial, clique em **Iniciar Serviço**.

    ![Configuração da versão prévia do Azure Functions Runtime concluída][12]

1. Navegue até o **Portal do Azure Functions Runtime** como`https://<machinename>.<domain>/`.

    ![Portal de versão prévia do Azure Functions Runtime][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Criar sua primeira função na versão prévia do Azure Functions Runtime

Para criar sua primeira função na versão prévia do Azure Functions Runtime

1. Navegue até a **Portal do Azure Functions Runtime** como `https://<machinename>.<domain>` por exemplo `https://mycomputer.mydomain.com`.

1. Você será solicitado a **Entrar**; se implantado em um domínio, use seu nome de usuário da conta do domínio e a senha. Caso contrário, use seu nome de usuário de conta local e a senha para fazer logon no portal.

    ![Logon do portal da versão prévia do Azure Functions Runtime][14]

1. Para criar aplicativos de funções, você deve criar uma assinatura.  No canto superior esquerdo do portal, clique no **+** opção ao lado das assinaturas.

    ![Assinaturas do portal de versão prévia do Azure Functions Runtime][15]

1. Escolha **DefaultPlan**, insira um nome para sua assinatura e clique em **Criar**.

    ![Nome e plano da assinatura do portal da versão prévia do Azure Functions Runtime][16]

1. Todos os seus aplicativos de funções estão listados no painel esquerdo do portal.  Para criar um novo aplicativo de funções, selecione o título **Aplicativo de Funções** e clique na opção **+**.

1. Insira um nome para seu aplicativo de funções, selecione a assinatura correta, escolha a versão do Azure Functions Runtime que deseja programar e clique em **Criar**

    ![Novo aplicativo de funções no portal da versão prévia do Azure Functions Runtime][17]

1. Seu novo aplicativo de funções está listado no painel esquerdo do portal.  Selecione Functions e clique em **Nova Função** na parte superior do painel central no portal.

    ![Modelos do portal de versão prévia do Azure Functions Runtime][18]

1. Selecione a função Gatilho de Temporizador, no submenu direito, nomeie sua função e altere o Agendamento para `*/5 * * * * *` (essa expressão cron faz com que a função de temporizador seja executada a cada cinco segundos) e clique em **Criar**

    ![Configuração de nova função de temporizador na versão prévia do Azure Functions Runtime][19]

1. A função foi criada.  Você pode exibir o log de execução do seu aplicativo de funções expandindo o painel **log** na parte inferior do portal.

    ![Execução de função da versão prévia do Azure Functions Runtime][20]

<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-Progress.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer6-InstallComplete.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[13]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png
[14]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Login.png
[15]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions.png
[16]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions1.png
[17]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewFunctionApp.png
[18]: ./media/functions-runtime-install/AzureFunctionsRuntime_v1FunctionsTemplates.png
[19]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewTimerFunction.png
[20]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_RunningV2Function.png
