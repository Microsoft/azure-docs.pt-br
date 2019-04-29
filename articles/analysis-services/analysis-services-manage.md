---
title: Gerenciar o Azure Analysis Services | Microsoft Docs
description: Saiba como gerenciar um servidor do Analysis Services no Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0bae06d46c2c96ba9dd058e9c2d380379523811c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61065146"
---
# <a name="manage-analysis-services"></a>Gerenciar o Analysis Services
Depois de criar um servidor do Analysis Services no Azure, talvez seja necessário executar algumas tarefas de administração e gerenciamento imediatamente ou em algum momento no futuro. Por exemplo, executar o processamento nos dados atualizados, controlar quem pode acessar os modelos em seu servidor ou monitorar a integridade do servidor. Algumas tarefas de gerenciamento só podem ser executadas no Portal do Azure, outras no SQL Server Management Studio (SSMS) e algumas tarefas podem ser executadas em ambos.

## <a name="azure-portal"></a>Portal do Azure
O [Portal do Azure](https://portal.azure.com/) é o local em que você pode criar e excluir servidores, monitorar recursos de servidor, alterar o tamanho e gerenciar quem tem acesso aos seus servidores.  Se você estiver enfrentando problemas, também poderá enviar uma solicitação de suporte.

![Obter o nome do servidor no Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Conectar-se ao seu servidor no Azure é como se conectar a uma instância de servidor em sua própria organização. No SSMS, você pode executar algumas tarefas como processar dados ou criar um script de processamento, gerenciar funções e usar o PowerShell.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>Baixar e instalar o SSMS
Para obter todos os recursos mais recentes e a melhor experiência ao se conectar ao servidor do Azure Analysis Services, certifique-se de que está usando a versão mais recente do SSMS. 

[Baixar o SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


### <a name="to-connect-with-ssms"></a>Para conectar-se com o SSMS
 Ao usar o SSMS, antes de se conectar ao servidor na primeira vez, verifique se que seu nome de usuário está incluído no grupo de administradores do Analysis Services. Para obter mais informações, consulte [Administradores de servidor e usuários de banco de dados](#server-administrators-and-database-users) posteriormente neste artigo.

1. Antes de se conectar, você precisa obter o nome do servidor. No **Portal do Azure** > servidor > **Visão geral** > **Nome do servidor**, copie o nome do servidor.
   
    ![Obter o nome do servidor no Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. No SSMS > **Pesquisador de Objetos**, clique em **Conectar** > **Analysis Services**.
3. Na caixa de diálogo **Conectar ao Servidor**, cole o nome do servidor e, em **Autenticação**, escolha um dos seguintes tipos de autenticação:   
    > [!NOTE]
    > O tipo de autenticação, **Active Directory - Universal com suporte MFA**, é recomendado.

    > [!NOTE]
    > Se você entrar com uma conta da Microsoft, Live ID, Yahoo, Gmail e etc., deixe o campo de senha em branco. Depois de clicar em Conectar, uma senha será solicitada.

    **Autenticação do Windows** para usar suas credenciais de domínio/nome de usuário e senha do Windows.

    **Autenticação de Senha do Active Directory** para usar uma conta organizacional. Por exemplo, ao conectar-se de um computador que não ingressou em um domínio.

    **Active Directory - Universal com suporte MFA** para usar [autenticação multifator ou não interativa](../sql-database/sql-database-ssms-mfa-authentication.md). 
   
    ![Conectar-se no SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>Administradores de servidor e usuários de banco de dados
Nos Azure Analysis Services, há dois tipos de usuários, administradores de servidor e usuários de banco de dados. Os dois tipos de usuários devem estar no Azure Active Directory e devem ser especificados por endereços de email organizacionais ou UPN. Para obter mais informações, consulte [Autenticação e permissões de usuário](analysis-services-manage-users.md).


## <a name="troubleshooting-connection-problems"></a>Solucionar problemas de conexão
Ao conectar usando o SSMS, se você tiver problemas, talvez seja necessário limpar o cache de logon. Nada é armazenado em cache de disco. Para limpar o cache, feche e reinicie o processo de conexão. 

## <a name="next-steps"></a>Próximas etapas
Se você ainda não tiver implantado um modelo de tabela em seu novo servidor, agora é um bom momento. Para saber mais, confira [Implantar no Azure Analysis Services](analysis-services-deploy.md).

Se você tiver implantado um modelo de tabela para seu servidor, você estará pronto para se conectar usando um cliente ou navegador. Para saber mais, confira [Obter dados do servidor do Azure Analysis Services](analysis-services-connect.md).

