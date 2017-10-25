---
title: Instalar o Azure AD Connect usando um banco de dados ADSync existente | Microsoft Docs
description: "Este tópico descreve como usar um banco de dados ADSync existente."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.reviewer: cychua
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.author: billmath
ms.openlocfilehash: d005042fffcf8f4ff99876961a55d254fd4fb2d5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>Instalar o Azure AD Connect usando um banco de dados ADSync existente
O Azure AD Connect requer um banco de dados do SQL Server para armazenar dados. Você pode usar o SQL Server 2012 Express LocalDB padrão instalado com o Azure AD Connect ou usar sua própria versão completa do SQL. Anteriormente, quando você instalava o Azure AD Connect, um novo banco de dados denominado ADSync era sempre criado. Com o Azure AD Connect versão 1.1.613.0 (ou posterior), você tem a opção de instalar o Azure AD Connect apontando-o para um banco de dados ADSync existente.

## <a name="benefits-of-using-an-existing-adsync-database"></a>Benefícios de usar um banco de dados ADSync existente
Apontando para um banco de dados ADSync existente:

- Com exceção das informações de credenciais, as configurações de sincronização armazenadas no banco de dados ADSync (incluindo as regras de sincronização personalizadas, conectores, filtragem e configuração de recursos opcionais) são automaticamente recuperadas e usadas durante a instalação. As credenciais usadas pelo Azure AD Connect para sincronizar as alterações com o AD local e o Azure AD são criptografadas e só podem ser acessadas pelo servidor do Azure AD Connect anterior.
- Todos os dados de identidade (associados com espaços de conector e metaverso) e cookies de sincronização armazenados no banco de dados ADSync também são recuperados. O servidor do Azure AD Connect recém-instalado pode continuar a sincronizar do ponto em que o servidor do Azure AD Connect anterior parou, sem ter a necessidade de executar uma sincronização completa.

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>Cenários em que é útil usar um banco de dados ADSync
Esses benefícios são úteis nos seguintes cenários:


- Você tem uma implantação existente do Azure AD Connect. O servidor existente do Azure AD Connect não está mais funcionando, mas o SQL Server que contém o banco de dados ADSync ainda está funcionando. Você pode instalar um novo servidor do Azure AD Connect e apontá-lo para o banco de dados ADSync existente. 
- Você tem uma implantação existente do Azure AD Connect. O SQL Server que contém o banco de dados ADSync não está mais funcionando. No entanto, você tem um backup recente do banco de dados. Você pode restaurar o banco de dados ADSync para um novo SQL Server primeiro. Depois disso, você pode instalar um novo servidor do Azure AD Connect e apontá-lo para o banco de dados ADSync restaurado.
- Você tem uma implantação existente do Azure AD Connect que está usando LocalDB. Devido ao limite de 10 GB imposto pelo LocalDB, você deseja migrar para o SQL completo. Você pode fazer backup do banco de dados ADSync do LocalDB e restaurá-lo para um SQL Server. Depois disso, você pode reinstalar um novo servidor do Azure AD Connect e apontá-lo para o banco de dados ADSync restaurado.
- Você está tentando configurar um servidor de preparo e deseja verifique se sua configuração corresponde àquela do servidor ativo atual. Você pode fazer backup do banco de dados ADSync e restaurá-lo para outro SQL Server. Depois disso, você pode reinstalar um novo servidor do Azure AD Connect e apontá-lo para o banco de dados ADSync restaurado.

## <a name="prerequisite-information"></a>Informações de pré-requisito

Observações importantes necessárias antes de continuar:


- Não deixe de examinar os pré-requisitos para a instalação do Azure AD Connect em Hardware e pré-requisitos, bem como as contas e permissões necessárias para instalar o Azure AD Connect. As permissões necessárias para instalar o Azure AD Connect usando o modo "usar o banco de dados existente" são as mesmas que as da instalação "personalizada".
- A versão do Azure AD Connect usada para a instalação deve atender aos seguintes critérios:
    - 1.1.613.0 ou superior, E
    - Igual ou superior à versão do Azure AD Connect usada pela última vez com o banco de dados ADSync. Se a versão do Azure AD Connect usada para a instalação é superior à versão usada pela última vez com o banco de dados ADSync, uma sincronização completa pode ser necessária.  Isso é necessário se houver alterações de esquema ou de regra de sincronização entre as duas versões. 
- O banco de dados ADSync usado deve conter um estado de sincronização relativamente recente. A última atividade de sincronização com o banco de dados ADSync existente deve estar dentro das últimas três semanas.
- Ao instalar o Azure AD Connect usando o método "usar o banco de dados existente", o método de entrada configurado no servidor do Azure AD Connect anterior não é preservado. Além disso, você não pode configurar o método de entrada durante a instalação. Você só pode configurar o método de entrada após a conclusão da instalação.
- Você não pode ter vários servidores de conexão do Azure AD compartilhando o mesmo banco de dados ADSync. O método "usar o banco de dados existente" permite que você reutilize um banco de dados ADSync existente com um novo servidor do Azure AD Connect. Ele não dá suporte a compartilhamento.

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>Etapas para instalar o Azure AD Connect com o modo "usar banco de dados existente"
1.  Baixe o instalador do Azure AD Connect (AzureADConnect.MSI) para o Windows Server. Clique duas vezes no instalador do Azure AD Connect para iniciar a instalação do Azure AD Connect.
2.  Depois de concluir a instalação do MSI, o assistente do Azure AD Connect começa a instalação do modo Express. Feche a tela clicando no ícone de Saída.
![Bem-vindo](media/active-directory-aadconnect-existing-database/db1.png)
3.  Inicie um novo prompt de comando ou sessão do PowerShell. Navegue até a pasta <drive>\Program Files\Microsoft Azure AD Connect. Execute o comando .\AzureADConnect.exe /useexistingdatabase para iniciar o assistente do Azure AD Connect no modo de configuração "Usar banco de dados existente".
![PowerShell](media/active-directory-aadconnect-existing-database/db2.png)
4.  Você receberá boas-vindas com a tela Bem-vindo ao Azure AD Connect. Depois que você concordar com os termos de licença e o aviso de privacidade, clique em **Continuar**.
![Bem-vindo](media/active-directory-aadconnect-existing-database/db3.png)
5.  Na tela **Instalar os componentes necessários**, a opção **Usar um SQL Server existente** é habilitada. Especifique o nome do SQL Server hospedando o banco de dados ADSync. Se a instância do mecanismo SQL usada para hospedar o banco de dados ADSync não for a instância padrão no SQL Server, você deverá especificar o nome de instância do mecanismo SQL. Além disso, se a pesquisa do SQL não está habilitada, você também deve especificar o número da porta da instância do mecanismo SQL. Por exemplo:         
![Bem-vindo](media/active-directory-aadconnect-existing-database/db4.png)           

6.  Na tela **Conectar ao Azure AD**, você deve fornecer as credenciais de um administrador global do seu diretório do Azure AD. Uma recomendação é usar uma conta no domínio onmicrosoft.com padrão. Lembre-se de que essa conta é usada apenas para criar uma conta de serviço no Azure AD e não é usada após a conclusão do assistente.
![Conectar](media/active-directory-aadconnect-existing-database/db5.png)
 
7.  Na tela **Conectar seus diretórios**, a floresta do AD existente configurada para sincronização de diretório está listada com um ícone de cruz vermelha ao lado dela. Para sincronizar as alterações de uma floresta do AD local, uma conta do AD DS é necessária. O assistente do Azure AD Connect não é capaz de recuperar as credenciais da conta do AD DS armazenadas no banco de dados ADSync porque as credenciais são criptografadas e podem ser descriptografadas somente pelo servidor do Azure AD Connect anterior. Clique em **Alterar Credenciais** para especificar a conta do AD DS para a floresta do AD.
![Diretórios](media/active-directory-aadconnect-existing-database/db6.png)
 
 
8.  No diálogo pop-up, você pode (i) fornecer uma credencial de admin corporativo e permitir que o Azure AD Connect crie a conta do AD DS para você ou (ii) criar a conta do AD DS você mesmo e fornecer a credencial dela para o Azure AD Connect. Depois que você tiver selecionado uma opção e fornecido as credenciais necessárias, clique em **OK** para fechar o diálogo pop-up.
![Bem-vindo](media/active-directory-aadconnect-existing-database/db7.png)
 
 
9.  Depois que as credenciais são fornecidas, o ícone de cruz vermelha é substituído por um ícone de tique verde. Clique em **Avançar**.
![Bem-vindo](media/active-directory-aadconnect-existing-database/db8.png)
 
 
10. Na tela **Pronto para configurar**, clique em **Instalar**.
![Bem-vindo](media/active-directory-aadconnect-existing-database/db9.png)
 
 
11. Depois que a instalação for concluída, o servidor do Azure AD Connect será habilitado automaticamente para o Modo de Preparo. É recomendável que você examine a configuração do servidor e exportações pendentes para alterações inesperadas antes de desabilitar o Modo de Preparo. 

## <a name="next-steps"></a>Próximas etapas

- Agora que você tem o Azure AD Connect instalado, é possível [verificar a instalação e atribuir licenças](active-directory-aadconnect-whats-next.md).
- Saiba mais sobre estes recursos, que foram habilitados com a instalação: [impedir exclusões acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) e [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-sync.md).
- Saiba mais sobre estes tópicos comuns: [Agendador e como disparar a sincronização](active-directory-aadconnectsync-feature-scheduler.md).
- Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).
