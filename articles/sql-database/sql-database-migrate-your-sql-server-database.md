---
title: Migrar o banco de dados SQL Server para o Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba como migrar seu banco de dados do SQL Server para o banco de dados SQL do Azure.
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,load & move data
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 09/01/2017
ms.author: carlrab
ms.openlocfilehash: 8206cba416df4d43594678428632ff161a9564d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-your-sql-server-database-to-azure-sql-database"></a>Migrar seu banco de dados do SQL Server para o banco de dados SQL do Azure

Mover seu banco de dados do SQL Server para o Banco de dados SQL do Azure é tão simples quanto criar um banco de dados SQL vazio no Azure e usar o [DMA](https://www.microsoft.com/download/details.aspx?id=53595) (Assistente de Migração de Dados) para importar o banco de dados para o Azure. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar um banco de dados SQL do Azure vazio no Portal do Azure (usando um servidor novo ou existente do Banco de Dados SQL do Azure)
> * Criar um firewall no nível de servidor no Portal do Azure (se não tiver sido criado anteriormente)
> * Usar o [DMA](https://www.microsoft.com/download/details.aspx?id=53595) (Assistente de Migração de Dados) para importar o banco de dados do SQL Server para o banco de dados SQL do Azure vazio 
> * Usar o [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SQL Server Management Studio) para alterar as propriedades do banco de dados.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

- A versão mais recente instalada do [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).  
- Instalada a versão mais recente do [DMA](https://www.microsoft.com/download/details.aspx?id=53595) (Assistente de Migração de Dados).
- Você identificou e tem acesso a um banco de dados para migrar. Este tutorial usa o banco de dados [SQL Server 2008R2 AdventureWorks OLTP](https://msftdbprodsamples.codeplex.com/releases/view/59211) em uma instância do SQL Server 2008R2 ou mais recente, mas você pode usar qualquer banco de dados de sua escolha. Para corrigir problemas de compatibilidade, use as [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure

Faça logon no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-blank-sql-database"></a>Criar um banco de dados SQL em branco

Um banco de dados SQL do Azure é criado com um conjunto definido de [recursos de computação e armazenamento](sql-database-service-tiers.md). O banco de dados é criado dentro de um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) e em um [servidor lógico de banco de dados SQL do Azure](sql-database-features.md). 

Siga estas etapas para criar um banco de dados SQL em branco. 

1. Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.

2. Selecione **Bancos de Dados** na página **Novo** e selecione **Criar** em **Banco de Dados SQL** na página **Novo**.

   ![criar banco de dados vazio](./media/sql-database-design-first-database/create-empty-database.png)

3. Preencha o formulário do Banco de Dados SQL com as informações abaixo, conforme mostrado na imagem anterior:   

   | Configuração       | Valor sugerido | Descrição | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nome do banco de dados** | mySampleDatabase | Para ver os nomes do banco de dados válidos, consulte [Identificadores do Banco de Dados](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers). | 
   | **Assinatura** | Sua assinatura  | Para obter detalhes sobre suas assinaturas, consulte [Assinaturas](https://account.windowsazure.com/Subscriptions). |
   | **Grupo de recursos** | myResourceGroup | Para ver os nomes do grupo de recursos válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   | **Selecionar fonte** | Banco de dados em branco | Especifica que um banco de dados em branco deve ser criado. |

4. Clique em **Servidor** para criar e configurar um novo servidor para o novo banco de dados. Preencha o **formulário Novo servidor** com as seguintes informações: 

   | Configuração       | Valor sugerido | Descrição | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nome do servidor** | Qualquer nome exclusivo globalmente | Para ver os nomes do servidor válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). | 
   | **Logon de administrador do servidor** | Qualquer nome válido | Para ver os nomes de logon válidos, consulte [Identificadores do Banco de Dados](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers).|
   | **Senha** | Qualquer senha válida | Sua senha deve ter, pelo menos, oito caracteres e deve conter caracteres de três das seguintes categorias: caracteres com letras maiúsculas, letras minúsculas, números e caracteres não alfanuméricos. |
   | **Localidade** | Qualquer local válido | Para obter mais informações sobre as regiões, consulte [Regiões do Azure](https://azure.microsoft.com/regions/). |

   ![criar database-server](./media/sql-database-design-first-database/create-database-server.png)

5. Clique em **Selecionar**.

6. Clique em **Tipo de preço** para especificar a camada de serviço, o número de DTUs e a quantidade de armazenamento. Explore as opções para o número de DTUs e o armazenamento disponível em cada camada de serviço. 

7. Para este tutorial, selecione a camada de serviço **Standard** e, em seguida, use o controle deslizante para selecionar **100 DTUs (S3)** e **400** GB de armazenamento.

   ![Criar database-s1](./media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

8. Aceite os termos da versão prévia para usar a opção **Armazenamento Complementar**. 

   > [!IMPORTANT]
   > \* Tamanhos de armazenamento maiores que a quantidade de armazenamento incluída estão em versão prévia e aplicam-se custos extras. Para obter detalhes, confira [Preços de Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). 
   >
   >\* Na camada Premium, mais de 1 TB de armazenamento está atualmente disponível nas seguintes regiões: Leste dos EUA 2, Oeste dos EUA, Gov. EUA - Virgínia, Europa Ocidental, Alemanha Central, Sudeste Asiático, Leste do Japão, Leste da Austrália, Canadá Central e Leste do Canadá. Consulte [Limitações atuais de P11-P15](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
   > 

9. Depois de selecionar a camada de servidor, o número de DTUs e a quantidade de armazenamento, clique em **Aplicar**.  

10. Selecione um **agrupamento** para o banco de dados em branco (para este tutorial, use o valor padrão). Para obter mais informações sobre agrupamentos, consulte [Agrupamentos](https://docs.microsoft.com/sql/t-sql/statements/collations)

11. Agora que você concluiu o formulário do Banco de Dados SQL, clique em **Criar** para provisionar o banco de dados. O provisionamento demora alguns minutos. 

12. Na barra de ferramentas, clique em **Notificações** para monitorar o processo de implantação.
    
     ![notificação](./media/sql-database-get-started-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Criar uma regra de firewall no nível de servidor

O serviço do Banco de Dados SQL cria um firewall no nível do servidor impedindo que os aplicativos e ferramentas externos conectem o servidor ou os bancos de dados no servidor, a menos que uma regra de firewall seja criada para abrir o firewall para endereços IP específicos. Execute estas etapas a fim de criar uma [regra de firewall no nível do servidor do Banco de Dados SQL](sql-database-firewall-configure.md) para o endereço IP do seu cliente e habilitar a conectividade externa por meio do firewall do Banco de Dados SQL somente para seu endereço IP. 

> [!NOTE]
> O Banco de Dados SQL se comunica pela porta 1433. Se você estiver tentando conectar-se a partir de uma rede corporativa, o tráfego de saída pela porta 1433 poderá não ser permitido pelo firewall de sua rede. Se isto acontecer, você não poderá conectar o servidor do Banco de Dados SQL do Azure, a menos que o departamento de TI abra a porta 1433.
>

1. Depois da implantação ser concluída, clique em **Bancos de dados SQL** no menu à esquerda, depois, clique em **mySampleDatabase** na página **Bancos de dados SQL**. A página de visão geral de seu banco de dados é aberta, mostrando o nome totalmente qualificado do servidor (como **mynewserver-20170824.database.windows.net**) e fornece opções para configurações adicionais. 

2. Copie esse nome do servidor totalmente qualificado para se conectar ao servidor e a seus bancos de dados nos próximos guias de início rápido. 

   ![nome do servidor](./media/sql-database-get-started-portal/server-name.png) 

3. Clique em **Definir firewall do servidor** na barra de ferramentas. A página **Configurações do firewall** do servidor de Banco de Dados SQL é aberta. 

   ![regra de firewall do servidor](./media/sql-database-get-started-portal/server-firewall-rule.png) 

4. Clique em **Adicionar IP do cliente** na barra de ferramentas para adicionar seu endereço IP atual a uma nova regra de firewall. Uma regra de firewall pode abrir a porta 1433 para um único endereço IP ou um intervalo de endereços IP.

5. Clique em **Salvar**. Uma regra de firewall no nível do servidor é criada para a porta de abertura 1433 de seu endereço IP atual no servidor lógico.

6. Clique em **OK**, em seguida, feche a página **Configurações do Firewall**.

Agora é possível conectar-se ao servidor de Banco de Dados SQL e seus bancos de dados usando o SQL Server Management Studio, o Assistente de Migração de Dados ou outra ferramenta de sua escolha desse endereço IP usando a conta do administrador do servidor criada no procedimento anterior.

> [!IMPORTANT]
> Por padrão, o acesso através do firewall do Banco de Dados SQL está habilitado para todos os serviços do Azure. Clique em **DESATIVAR** nesta página para desabilitar todos os serviços do Azure.

## <a name="sql-server-connection-information"></a>Informações de conexão do servidor SQL

Obtenha o nome de servidor totalmente qualificado para o servidor de Banco de Dados SQL do Azure no Portal do Azure. É possível usar o nome do servidor totalmente qualificado para conectar-se ao seu SQL Server do Azure usando ferramentas de cliente, incluindo a Assistência de Migração de Dados e o SQL Server Management Studio.

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Bancos de Dados SQL** no menu à esquerda e clique em seu banco de dados na página **Bancos de Dados SQL**. 
3. No painel **Essentials**, na página do Portal do Azure de seu banco de dados, localize e copie o **Nome do servidor**.

   ![informações da conexão](./media/sql-database-get-started-portal/server-name.png)

## <a name="migrate-your-database"></a>Migre seu banco de dados

Siga estas etapas para usar o **[Assistente de migração de dados](https://www.microsoft.com/download/details.aspx?id=53595)** para avaliar a preparação do banco de dados para migração para o Banco de Dados SQL do Azure e para concluir a migração.

1. Abra o **Assistente de migração de dados**. É possível executar o DMA em qualquer computador com conectividade para a instância do SQL Server que contenha o banco de dados que você planeja migrar e a conectividade com a Internet. Não é necessário instalá-lo no computador que hospeda a instância do SQL Server que você está migrando. A regra de firewall criada em um procedimento anterior deve ser para o computador no qual você está executando o Assistente de Migração de Dados.

     ![abra o assistente de migração de dados](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-open.png)

2. No menu à esquerda, clique em **+ Novo** para criar um projeto de **Avaliação**. Preencha os valores solicitados e, em seguida, clique em **Criar**:

   | Configuração      | Valor sugerido | Descrição | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Tipo de projeto | Migração | Escolha se deseja avaliar seu banco de dados quanto a migração ou escolha avaliar a migração como parte do mesmo fluxo de trabalho |
   |Nome do projeto|Tutorial de migração| Um nome descritivo |
   |Tipo do servidor de origem| SQL Server | Essa é a única fonte com suporte no momento |
   |Tipo do servidor de destino| Banco de Dados SQL do Azure| As opções incluem: Banco de Dados SQL do Azure, SQL Server, SQL Server em máquinas virtuais do Azure |
   |Escopo da migração| Esquema e dados| As opções incluem: esquema e dados, apenas esquema, apenas dados |
   
   ![novo projeto do assistente de migração de dados](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-new-project.png)

3.  Na página **Selecionar origem**, preencha os valores solicitados e, em seguida, clique em **Conectar**:

    | Configuração      | Valor sugerido | Descrição | 
    | ------------ | ------------------ | ------------------------------------------------- | 
    | Nome do servidor | Seu nome do servidor ou endereço IP | Seu nome do servidor ou endereço IP |
    | Tipo de autenticação. | O tipo de autenticação preferencial| Opções: autenticação do Windows, Autenticação do SQL Server, Autenticação integrada do Active Directory, Autenticação de senha do Active Directory |
    | Nome de Usuário | Seu nome de logon | Seu logon deve ter as permissões **CONTROL SERVER** |
    | Senha| Sua senha | Sua senha |
    | Propriedades da conexão| Selecione **Criptografar conexão** e **Certificado do servidor confiável** conforme apropriado para seu ambiente. | Escolha as propriedades apropriadas para conectar-se ao seu servidor |

    ![nova seleção de fonte da migração de dados](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-source.png)

5. Selecione um único banco de dados do seu servidor de origem a ser migrado para o Banco de Dados SQL do Azure e clique em **Avançar**. Para este tutorial, há apenas um único banco de dados.

6. Na página **Selecionar destino**, preencha os valores solicitados e clique em **Conectar**:

    | Configuração      | Valor sugerido | Descrição | 
    | ------------ | ------------------ | ------------------------------------------------- | 
    | Nome do servidor | Seu nome do servidor de Banco de Dados do Azure totalmente qualificado | Seu nome do servidor de Banco de Dados do Azure totalmente qualificado do procedimento anterior |
    | Tipo de autenticação. | Autenticação do SQL Server | A autenticação do SQL Server é a única opção enquanto este tutorial é escrito, mas também há suporte para a Autenticação integrada do Active Directory e a Autenticação de senha do Active Directory pelo Banco de Dados SQL do Azure |
    | Nome de Usuário | Seu nome de logon | O logon deve ter as permissões **CONTROL DATABASE** para o banco de dados de origem |
    | Senha| Sua senha | Sua senha |
    | Propriedades da conexão| Selecione **Criptografar conexão** e **Certificado do servidor confiável** conforme apropriado para seu ambiente. | Escolha as propriedades apropriadas para conectar-se ao seu servidor |

    ![nova seleção de destino da migração de dados](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-target.png)

7. Selecione o banco de dados do servidor de destino criado em um procedimento anterior e clique em **Avançar** para iniciar o processo de avaliação do esquema de banco de dados de origem. Para este tutorial, há apenas um único banco de dados. Observe que o nível de compatibilidade desse banco de dados está definido como 140, que é o nível de compatibilidade padrão para todos os novos bancos de dados no Banco de Dados SQL do Azure.

   > [!IMPORTANT] 
   > Após migrar seu banco de dados para o Banco de Dados SQL do Azure, será possível escolher operar o banco de dados em um nível de compatibilidade especificado para fins de compatibilidade com versões anteriores. Para obter mais informações sobre as implicações e as opções para a operação de um banco de dados em um nível de compatibilidade específico, consulte [nível de compatibilidade de ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). Consulte também [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) para obter informações sobre configurações de nível de banco de dados adicionais relacionadas aos níveis de compatibilidade.
   >

8. Na página **Selecionar objetos**, após a conclusão do processo de avaliação do esquema de banco de dados de origem, examine os objetos selecionados para migração e examine os objetos que contêm problemas. Por exemplo, examine as alterações de comportamento **SERVERPROPERTY('LCID')** do objeto **dbo.uspSearchCandidateResumes** e as alterações de pesquisa de texto completo do objeto **HumanResourcesJobCandidate**. 

   > [!IMPORTANT] 
   > Dependendo do design do projeto e do design do seu aplicativo, quando você migrar seu banco de dados de origem, talvez seja necessário modificar qualquer um dos seus bancos de dados, ou ambos, ou seu aplicativo após a migração (e, em alguns casos, antes da migração). Para obter informações sobre as diferenças do Transact-SQL que podem afetar sua migração, consulte [Resolvendo diferenças de Transact-SQL durante a migração para o Banco de Dados SQL](sql-database-transact-sql-information.md).

     ![nova avaliação de migração de dados e seleção de objetos](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-assessment-results.png)

9. Clique em **Gerar script SQL** para gerar script dos objetos de esquema no banco de dados de origem. 
10. Examine o script gerado e, em seguida, clique em **Próximo problema** conforme necessário para examinar os problemas e recomendações de avaliação identificados. Por exemplo, para Pesquisa de texto completo, a recomendação quando você atualiza é testar seus aplicativos que usam os recursos de texto completo. É possível salvar ou copiar o script, se desejar.

     ![novo script gerado pela migração de dados](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-generated-script.png)

11. Clique em **Implantar esquema** e inspecione o processo de migração de esquema.

     ![nova migração de esquema da migração de dados](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-schema-migration.png)

12. Quando a migração do esquema for concluída, examine se há erros nos resultados e, em seguida, supondo que não haja, clique em **Migrar dados**.
13. Na página **Selecionar tabelas**, examine as tabelas selecionadas para migração e, em seguida, clique em **Iniciar migração de dados**.

     ![nova migração de dados da migração de dados](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-data-migration.png)

14. Inspecione o processo de migração.

     ![novo processo de migração de dados da migração de dados](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-data-migration-process.png)

## <a name="connect-to-the-database-with-ssms"></a>Conectar-se ao banco de dados com o SSMS

Use o [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) para estabelecer uma conexão com seu servidor do Banco de Dados SQL do Azure.

1. Abra o SQL Server Management Studio.

2. Na caixa de diálogo **Conectar ao Servidor**, insira as informações a seguir:

   | Configuração       | Valor sugerido | Descrição | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Tipo de servidor | Mecanismo de banco de dados | Esse valor é obrigatório |
   | Nome do servidor | O nome do servidor totalmente qualificado | O nome deve ser semelhante a este: **mynewserver20170824.database.windows.net**. |
   | Autenticação | Autenticação do SQL Server | A Autenticação do SQL é o único tipo de autenticação que configuramos neste tutorial. |
   | Logon | A conta do administrador do servidor | Esta é a conta que você especificou quando criou o servidor. |
   | Senha | A senha para sua conta do administrador do servidor | Esta é a senha que você especificou quando criou o servidor. |

   ![conectar-se ao servidor](./media/sql-database-connect-query-ssms/connect.png)

3. Clique em **Opções** na caixa de diálogo **Conectar servidor**. Na seção **Conectar ao banco de dados**, digite **mySampleDatabase** para conectar-se a este banco de dados.

   ![conectar o banco de dados no servidor](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Clique em **Conectar**. A janela Pesquisador de Objetos abre no SSMS. 

5. No Pesquisador de Objetos, expanda **Bancos de Dados** e expanda **mySampleDatabase** para exibir os objetos no banco de dados de exemplo.

   ![objetos de banco de dados](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="change-database-properties"></a>Alterar as propriedades de banco de dados

Você pode alterar a camada de serviço, o nível de desempenho e o nível de compatibilidade usando o SQL Server Management Studio. Durante a fase de importação, recomendamos que você importar um banco de dados de nível de desempenho superior para melhor desempenho, mas se você reduzir após a importação ser concluída para economizar dinheiro até que você esteja pronto para usar ativamente o banco de dados importado. Alterar o nível de compatibilidade pode resultar em melhor desempenho e o acesso aos recursos mais recentes do serviço de banco de dados SQL do Azure. Quando você migrar um banco de dados mais antigo, seu nível de compatibilidade do banco de dados será mantido, o que é compatível com o banco de dados que está sendo importado. Para obter mais informações, consulte [Desempenho de consultas aprimorado com nível de compatibilidade 130 no banco de dados SQL do Azure](sql-database-compatibility-level-query-performance-130.md).

1. No Pesquisador de Objetos, clique com o botão direito do mouse em **mySampleDatabase** e clique em **Nova consulta**. Uma janela de consulta se abre conectada ao banco de dados.

2. Execute o seguinte comando para definir a camada de serviço como **Standard** e o nível de desempenho para **S1**.

    ```sql
    ALTER DATABASE mySampleDatabase 
    MODIFY 
        (
        EDITION = 'Standard'
        , MAXSIZE = 250 GB
        , SERVICE_OBJECTIVE = 'S1'
    );
    ```

## <a name="next-steps"></a>Próximas etapas 
Neste tutorial, você aprendeu a:

> * Criar um Banco de Dados SQL do Azure vazio no portal do Azure 
> * Criar um firewall no nível do servidor no portal do Azure 
> * Usar o [DMA](https://www.microsoft.com/download/details.aspx?id=53595) (Assistente de Migração de Dados) para importar o banco de dados do SQL Server para o banco de dados SQL do Azure vazio 
> * Usar o [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SQL Server Management Studio) para alterar as propriedades do banco de dados.

Avance para o próximo tutorial para saber como proteger seu banco de dados.

> [!div class="nextstepaction"]
> [Proteja o Banco de Dados SQL do Azure](sql-database-security-tutorial.md).


