---
title: 'Azure AD Connect: Como recuperar LocalDB problema de limite de 10 GB | Microsoft Docs'
description: "Este tópico descreve como recuperar o serviço de sincronização do Azure do AD Connect quando encontra LocalDB 10GB limitar o problema."
services: active-directory
documentationcenter: 
author: cychua
manager: femila
editor: 
ms.assetid: 41d081af-ed89-4e17-be34-14f7e80ae358
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: billmath
ms.openlocfilehash: 08e682c51b12d4506019d2f6b68e1eae0798b990
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-how-to-recover-from-localdb-10-gb-limit"></a>Azure AD Connect: Como se recuperar de um limite de 10 GB do LocalDB
O Azure AD Connect requer um banco de dados do SQL Server para armazenar dados de identidade. Você pode usar o padrão que do SQL Server 2012 Express LocalDB instalado com o Azure AD Connect ou usar seu próprio SQL completo. O SQL Server Express impõe um limite de tamanho de 10 GB. Ao usar o LocalDB e esse limite for atingido, o serviço de sincronização do Azure do AD Connect não pode iniciar ou sincronizar corretamente. Este artigo fornece as etapas de recuperação.

## <a name="symptoms"></a>Sintomas
Há dois sintomas comuns:

* O Serviço de sincronização do Azure do AD Connect **está em execução**, mas não consegue sincronizar com o erro *"stopped-database-disk-full"*.

* Serviço de sincronização do Azure do AD Connect **não pode iniciar**. Quando você tentar iniciar o serviço, ele falha com o evento 6323 e mensagem de erro *"o servidor encontrou um erro porque o SQL Server está sem espaço em disco“.*

## <a name="short-term-recovery-steps"></a>Etapas de recuperação de curto prazo
Esta seção fornece as etapas para recuperar o espaço de banco de dados necessário para o serviço do Azure AD Connect sincronização retomar a operação. As etapas são as seguintes:
1. [Determinar o status do Serviço de Sincronização](#determine-the-synchronization-service-status)
2. [Reduzir o banco de dados](#shrink-the-database)
3. [Excluir dados de histórico de execução](#delete-run-history-data)
4. [Reduzir o período de retenção de dados de histórico de execução](#shorten-retention-period-for-run-history-data)

### <a name="determine-the-synchronization-service-status"></a>Determinar o status do serviço de sincronização
Primeiro, determine se o serviço de sincronização ainda está executando ou não:

1. Faça logon no seu servidor do Azure AD Connect como administrador.

2. Vá para **Service Control Manager**.

3. Verifique o status do **Microsoft Azure AD Sync**.


4. Se estiver em execução, não parar ou reiniciar o serviço. Ignorar [reduzir o banco de dados](#shrink-the-database) etapa e vá para [excluir dados de histórico de executar](#delete-run-history-data) etapa.

5. Se não estiver em execução, tente iniciar o serviço. Se o serviço foi iniciado com êxito, ignore [reduzir o banco de dados](#shrink-the-database) etapa e vá para [excluir dados de histórico de executar](#delete-run-history-data) etapa. Caso contrário, prossiga com a etapa [Reduzir o banco de dados](#shrink-the-database).

### <a name="shrink-the-database"></a>Reduzir o banco de dados
Use a operação de redução para liberar espaço de banco de dados para iniciar o serviço de sincronização. Ele libera espaço de banco de dados, removendo os espaços em branco no banco de dados. Essa etapa é melhor esforço, como não há garantia de que você sempre pode recuperar espaço. Para saber mais sobre a operação de redução, leia este artigo [reduzir um banco de dados](https://msdn.microsoft.com/library/ms189035.aspx).

> [!IMPORTANT]
> Ignore esta etapa se você pode obter o serviço de sincronização para execução. Não é recomendável para reduzir o banco de dados SQL que pode levar a mau desempenho devido à maior fragmentação.

O nome do banco de dados criado para o Azure AD Connect é **ADSync**. Para executar uma operação de redução, você deve fazer em como o administrador do sistema ou DBO do banco de dados. Durante a instalação do Azure AD Connect, as contas a seguir são concedidas direitos de sysadmin:
* Administradores Locais
* A conta de usuário que foi usada para executar a instalação do Azure AD Connect.
* A conta de serviço de sincronização que é usada como o contexto operacional do serviço de sincronização do Azure do AD Connect.
* O grupo local ADSyncAdmins criado durante a instalação.

1. O banco de dados por meio da cópia de backup **ADSync.mdf** e **ADSync_log.ldf** arquivos localizados em `%ProgramFiles%\program files\Microsoft Azure AD Sync\Data` para um local seguro.

2. Inicie uma nova sessão do PowerShell.

3. Navegue até a pasta `%ProgramFiles%\Program Files\Microsoft SQL Server\110\Tools\Binn`.

4. Iniciar **sqlcmd** utilitário executando o comando `./SQLCMD.EXE -S “(localdb)\.\ADSync” -U <Username> -P <Password>`, usando a credencial de um administrador do sistema ou o DBO do banco de dados.

5. Para reduzir o banco de dados, no prompt do sqlcmd (1>), digite `DBCC Shrinkdatabase(ADSync,1);`, seguido por `GO` na próxima linha.

6. Se a operação for bem-sucedida, tente iniciar o serviço de sincronização novamente. Se você puder iniciar o serviço de sincronização, vá para [excluir dados de histórico de executar](#delete-run-history-data) etapa. Caso contrário, contate o Suporte.

### <a name="delete-run-history-data"></a>Excluir dados de histórico de execução
Por padrão, Azure AD Connect retém a dias sete de dados de histórico de execução. Nesta etapa, podemos excluir os dados de histórico de execução para recuperar espaço de banco de dados para que o serviço de sincronização do Azure do AD Connect pode começar a sincronizar novamente.

1.  Inicie o **Gerenciador de Serviço de Sincronização** indo para INICIAR → Serviço de Sincronização.

2.  Vá para a guia **Operações**.

3.  Em **Ações**, selecione **Limpar Execuções**…

4.  Você pode escolher a opção **Limpar todas as execuções** ou **Limpar execuções antes de... <date>**. É recomendável que você comece desmarcando os dados de histórico de execução com mais de dois dias. Se você continuar a executar o problema de tamanho do banco de dados, escolha o **limpar todas as execuções** opção.

### <a name="shorten-retention-period-for-run-history-data"></a>Reduzir o período de retenção de dados de histórico de execução
Esta etapa é reduzir a probabilidade de executando o problema de limite de 10 GB após vários ciclos de sincronização.

1. Abra uma nova sessão do PowerShell.

2. Execute `Get-ADSyncScheduler` e anote a propriedade PurgeRunHistoryInterval, que especifica o período de retenção atual.

3. Execute `Set-ADSyncScheduler -PurgeRunHistoryInterval 2.00:00:00` para definir o período de retenção como dois dias. Ajuste o período de retenção, como apropriado.

## <a name="long-term-solution--migrate-to-full-sql"></a>Solução de longo prazo – migrar para o SQL completo
Em geral, o problema é indicativo de que o tamanho do banco de dados de 10 GB não é mais suficiente para o Azure AD Connect sincronizar seu Active Directory local ao Azure AD. É recomendável que você troque pelo uso da versão completa do SQL Server. Você não pode substituir o LocalDB de uma implantação existente do Azure AD Connect diretamente com o banco de dados da versão completa do SQL. Em vez disso, você deve implantar um novo servidor do Azure AD Connect com a versão completa do SQL. É recomendável que você faça uma migração swing, onde o novo servidor do Azure AD Connect (com o banco de dados SQL) é implantado como um servidor de preparo, ao lado do servidor do Azure AD Connect existente (com o LocalDB). 
* Para obter instruções sobre como configurar o remote SQL com o Azure AD Connect, consulte o artigo [instalação personalizada do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-custom).
* Para obter instruções sobre a migração swing para atualização do Azure AD Connect, veja o artigo [Azure AD Connect: Atualização de uma versão anterior para a versão mais recente](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#swing-migration).

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).
