---
title: Mover o banco de dados do Azure AD Connect do SQL Server Express para o SQL Server. | Microsoft Docs
description: Este documento descreve como mover o banco de dados do Azure AD Connect do servidor do SQL Server Express local para um SQL Server remoto.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94710e99fa7d04d757f2ad5fd7b2d3f6e01371d1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91306324"
---
# <a name="move-azure-ad-connect-database-from-sql-server-express-to-sql-server"></a>Mover o banco de dados do Azure AD Connect do SQL Server Express para o SQL Server 

Este documento descreve como mover o banco de dados do Azure AD Connect do servidor do SQL Server Express local para um SQL Server remoto.  Você pode usar os procedimentos abaixo para realizar essa tarefa.

## <a name="about-this-scenario"></a>Sobre este cenário
A seguir estão algumas informações rápidas sobre este cenário.  Neste cenário, a versão do Azure AD Connect (1.1.819.0) está instalada em um único controlador de domínio do Windows Server 2016.  Ele está usando o SQL Server 2012 Express Edition interno para seu banco de dados.  O banco de dados será movido para um servidor do SQL Server 2017.

![arquitetura do cenário](media/how-to-connect-install-move-db/move1.png)

## <a name="move-the-azure-ad-connect-database"></a>Mover o banco de dados do Azure AD Connect
Use as etapas a seguir para mover o banco de dados do Azure AD Connect para um SQL Server remoto.

1. No servidor do Azure AD Connect, acesse **Serviços** e pare o serviço **Microsoft Azure AD Sync**.
2. Localize a pasta **%ProgramFiles%\Microsoft Azure ad Sync\Data** e copie os arquivos **AdSync. MDF** e **ADSync_log. ldf** para o SQL Server remoto.
3. Reinicie o serviço **Microsoft Azure AD Sync** no servidor do Azure AD Connect.
4. Desinstale o Azure AD Connect acessando o Painel de Controle - - Programas - Programas e Recursos.  Selecione Microsoft Azure AD Connect e clique em Desinstalar, na parte superior.
5. No SQL server remoto, abra o SQL Server Management Studio.
6. Em Bancos de dados, clique com o botão direito do mouse e selecione Anexar.
7. Na tela **Anexar Bancos de dados**, clique em **Adicionar** e navegue até o arquivo ADSync.mdf.  Clique em **OK**.
   ![anexar banco de dados](media/how-to-connect-install-move-db/move2.png)

8. Depois que o banco de dados for anexado, volte para o servidor do Azure AD Connect e instale o Azure AD Connect.
9. Depois de concluir a instalação do MSI, o assistente do Azure AD Connect começa a instalação do modo Express. Feche a tela clicando no ícone de Saída.
   ![Captura de tela que mostra a página "bem-vindo ao Azure a D Connect" com "configurações expressas" no menu do lado esquerdo realçado.](./media/how-to-connect-install-move-db/db1.png)
10. Inicie um novo prompt de comando ou sessão do PowerShell. Navegue até a pasta \<drive>\Program Files\Microsoft Azure AD Connect. Execute o comando .\AzureADConnect.exe /useexistingdatabase para iniciar o assistente do Azure AD Connect no modo de configuração "Usar banco de dados existente".
    ![PowerShell](./media/how-to-connect-install-move-db/db2.png)
11. Você receberá boas-vindas com a tela Bem-vindo ao Azure AD Connect. Depois que você concordar com os termos de licença e o aviso de privacidade, clique em **Continuar**.
    ![Captura de tela que mostra a página "bem-vindo ao Azure A D Connect"](./media/how-to-connect-install-move-db/db3.png)
12. Na tela **Instalar os componentes necessários**, a opção **Usar um SQL Server existente** é habilitada. Especifique o nome do SQL Server hospedando o banco de dados ADSync. Se a instância do mecanismo SQL usada para hospedar o banco de dados ADSync não for a instância padrão no SQL Server, você deverá especificar o nome de instância do mecanismo SQL. Além disso, se a pesquisa do SQL não está habilitada, você também deve especificar o número da porta da instância do mecanismo SQL. Por exemplo:         
    ![Captura de tela que mostra a página "instalar componentes necessários".](./media/how-to-connect-install-move-db/db4.png)           

13. Na tela **Conectar ao Azure AD**, você deve fornecer as credenciais de um administrador global do seu diretório do Azure AD. Uma recomendação é usar uma conta no domínio onmicrosoft.com padrão. Lembre-se de que essa conta é usada apenas para criar uma conta de serviço no Azure AD e não é usada após a conclusão do assistente.
    ![Connect](./media/how-to-connect-install-move-db/db5.png)
 
14. Na tela **Conectar seus diretórios**, a floresta do AD existente configurada para sincronização de diretório está listada com um ícone de cruz vermelha ao lado dela. Para sincronizar as alterações de uma floresta do AD local, uma conta do AD DS é necessária. O assistente do Azure AD Connect não é capaz de recuperar as credenciais da conta do AD DS armazenadas no banco de dados ADSync porque as credenciais são criptografadas e podem ser descriptografadas somente pelo servidor do Azure AD Connect anterior. Clique em **Alterar Credenciais** para especificar a conta do AD DS para a floresta do AD.
    ![Diretórios](./media/how-to-connect-install-move-db/db6.png)
 

15. No diálogo pop-up, você pode (i) fornecer uma credencial de admin corporativo e permitir que o Azure AD Connect crie a conta do AD DS para você ou (ii) criar a conta do AD DS você mesmo e fornecer a credencial dela para o Azure AD Connect. Depois que você tiver selecionado uma opção e fornecido as credenciais necessárias, clique em **OK** para fechar o diálogo pop-up.
    ![Captura de tela da caixa de diálogo pop-up "uma conta da floresta D" com a "criar nova conta D" selecionada.](./media/how-to-connect-install-move-db/db7.png)
 

16. Depois que as credenciais são fornecidas, o ícone de cruz vermelha é substituído por um ícone de tique verde. Clique em **Próximo**.
    ![Captura de tela que mostra a página "conectar seus diretórios" depois de inserir as credenciais da conta.](./media/how-to-connect-install-move-db/db8.png)
 

17. Na tela **pronto para configurar** , clique em **instalar**.
    ![Bem-vindo](./media/how-to-connect-install-move-db/db9.png)
 

18. Depois que a instalação for concluída, o servidor do Azure AD Connect será habilitado automaticamente para o Modo de Preparo. É recomendável que você examine a configuração do servidor e exportações pendentes para alterações inesperadas antes de desabilitar o Modo de Preparo. 

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).
- [Instalar o Azure AD Connect usando um banco de dados ADSync existente](how-to-connect-install-existing-database.md)
- [Instalar o Azure AD Connect usando permissões de administrador do SQL delegado](how-to-connect-install-sql-delegation.md)

