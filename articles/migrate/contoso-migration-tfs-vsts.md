---
title: Refatorar uma implantação do Team Foundation Server para o Azure DevOps Services no Azure | Microsoft Docs
description: Saiba como a Contoso refatora sua implantação do TFS local migrando-a para o Azure DevOps Services no Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 21396a10543d388b6ac360f426272f1841b2f510
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60673558"
---
# <a name="contoso-migration--refactor-a-team-foundation-server-deployment-to-azure-devops-services"></a>Migração para Contoso:  Refatorar uma implantação do Team Foundation Server para o Azure DevOps Services

Este artigo mostra como a Contoso está refatorando sua implantação do TFS (Team Foundation Server) local migrando-o para o Azure DevOps Services no Azure. A equipe de desenvolvimento da Contoso usou o TFS para colaboração em equipe e controle do código-fonte nos últimos cinco anos. Agora, eles querem mudar para uma solução baseada em nuvem para o trabalho de desenvolvimento e teste e para o controle do código-fonte. O Azure DevOps Services desempenhará uma função conforme eles mudarem para um modelo do Azure DevOps e desenvolverem novos aplicativos nativos de nuvem.

Este documento é um de uma série de artigos que mostram como a empresa fictícia Contoso migra seus recursos locais para a nuvem do Microsoft Azure. A série inclui informações básicas e cenários que ilustram como configurar uma infraestrutura de migração e executar diferentes tipos de migrações. Cenários aumentam em complexidade. Adicionaremos mais artigos com o tempo.

**Artigo** | **Detalhes** | **Status**
--- | --- | ---
[Artigo 1: Visão geral](contoso-migration-overview.md) | Fornece uma visão geral de estratégia de migração da Contoso, a série de artigos e os aplicativos de exemplo que usamos. | Disponível
[Artigo 2: Implantar uma infraestrutura do Azure](contoso-migration-infrastructure.md) | Descreve como Contoso prepara seu local e a infraestrutura do Azure para a migração. A mesma infraestrutura é usada para todos os cenários de migração da Contoso. | Disponível
[Artigo 3: Avaliar recursos locais](contoso-migration-assessment.md)  | Mostra como a Contoso executa uma avaliação de seu aplicativo SmartHotel local de duas camadas em execução na VMware. As VMs de aplicativo são avaliadas com o serviço [Migrações para Azure](migrate-overview.md) e o banco de dados do SQL Server do aplicativo com o [Assistente de Migração de Dados do Azure](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponível
[Artigo 4: Mudar o host para VMs do Azure e para uma Instância Gerenciada do SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Demonstra como a Contoso migra o aplicativo SmartHotel para o Azure. Elas migram a VM Web do aplicativo usando o [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) e o banco de dados de aplicativo usando o serviço [Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/dms-overview) para migrar para uma Instância Gerenciada do SQL. | Disponível
[Artigo 5: Mudar o host para VMs do Azure](contoso-migration-rehost-vm.md) | Mostra como a Contoso migra o SmartHotel para VMs IaaS do Azure usando o serviço Site Recovery.
[Artigo 6: Mudar o host para VMs do Azure e para Grupos de Disponibilidade do SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Mostra como a Contoso migra o aplicativo SmartHotel. Eles usam o Site Recovery para migrar as máquinas virtuais do aplicativo e o serviço de Migração de Banco de Dados para migrar o banco de dados de aplicativo para um Grupo de Disponibilidade do SQL Server. | Disponível
[Artigo 7: Hospede novamente um aplicativo do Linux em VMs do Azure](contoso-migration-rehost-linux-vm.md) | Mostra como a Contoso migra o aplicativo Linux osTicket para VMs Azure IaaS usando o Azure Site Recovery.
[Artigo 8: Novo host a um aplicativo do Linux para as VMs do Azure e o Azure MySQL Server](contoso-migration-rehost-linux-vm-mysql.md) | Demonstra como a Contoso migra o aplicativo Linux osTicket. Ela usa o Site Recovery para a migração de VM e o Workbench do MySQL para fazer a migração para uma instância do servidor MySQL do Azure. | Disponível
[Artigo 9: Refatorar um aplicativo para um Aplicativo Web do Azure e para o Banco de Dados SQL do Azure](contoso-migration-refactor-web-app-sql.md) | Demonstra como a Contoso migra o aplicativo SmartHotel para um aplicativo Web baseado em contêiner do Azure e migra o banco de dados de aplicativo para o SQL Server do Azure. | Disponível
[Artigo 10: Refatorar um aplicativo Linux para o Serviço de Aplicativo do Azure e o Servidor MySQL do Azure](contoso-migration-refactor-linux-app-service-mysql.md) | Mostra como a Contoso migra o aplicativo Linux osTicket para o Serviço de Aplicativo do Azure usando o contêiner do Docker do PHP 7.0. A base de código para a implantação é migrada para o GitHub. O banco de dados do aplicativo é migrado para o MySQL do Azure. | Disponível
Artigo 11: Refatorar uma implantação do TFS no Azure DevOps Services | Migrar o TFS do aplicativo de desenvolvimento para o Azure DevOps Services no Azure | Este artigo
[Artigo 12: Recriar um aplicativo nos contêineres do Azure e no Banco de Dados SQL do Azure](contoso-migration-rearchitect-container-sql.md) | Mostra como a Contoso migra e recria a arquitetura de seu aplicativo SmartHotel para o Azure. Eles recriam a camada da Web do aplicativo como um contêiner do Windows e o banco de dados do aplicativo em um Banco de Dados SQL do Azure. | Disponível
[Artigo 13: Recompilar um aplicativo no Azure](contoso-migration-rebuild.md) | Mostra como a Contoso recompila seu aplicativo SmartHotel usando uma variedade de funcionalidades e serviços do Azure, incluindo os Serviços de Aplicativos, o Kubernetes do Azure, o Azure Functions, os Serviços Cognitivos e o Cosmos DB. | Disponível
[Artigo 14: Escalar uma migração para o Azure](contoso-migration-scale.md) | Depois de experimentar combinações de migração, a Contoso se prepara para dimensionar para uma migração completa para o Azure. | Disponível


## <a name="business-drivers"></a>Geradores de negócios

A equipe de liderança de TI trabalhou em conjunto com parceiros de negócios para identificar as metas futuras. Os parceiros não estão excessivamente preocupados com as tecnologias e ferramentas de desenvolvimento, mas eles capturaram estes pontos:

- **Software**: independentemente do negócio principal, todas as empresas agora são empresas de software, incluindo a Contoso. A liderança de negócios está interessada em como a TI pode ajudar a conduzir a empresa com novas práticas de trabalho para os usuários e experiências para seus clientes.
- **Eficiência**: a Contoso precisa simplificar o processo e remover os procedimentos desnecessários para desenvolvedores e usuários. Isso permitirá que a empresa cumpra os requisitos do cliente com mais eficiência. Os negócios precisam que a TI seja rápida, sem perda de tempo ou de dinheiro.
- **Agilidade**:  a Contoso precisa que a TI responda às necessidades de negócios e reaja mais rapidamente do que o marketplace para permitir o sucesso em uma economia global. A TI não deve ser um bloqueador para os negócios.

## <a name="migration-goals"></a>Metas de migração

A equipe de nuvem da Contoso fixou metas para a migração para o Azure DevOps Services:

- A equipe precisa de uma ferramenta para migrar os dados para a nuvem. Alguns processos manuais devem ser necessários.
- O histórico e os dados do item de trabalho do último ano devem ser migrados.
- Eles não querem definir novos nomes de usuário e senhas. Todas as atribuições atuais do sistema devem ser mantidas.
- Eles desejam mudar do TFVC (Controle de Versão do Team Foundation) para o Git para controle do código-fonte.
- A transferência para o Git será uma "migração de ponta" que importa somente a versão mais recente do código-fonte. Ela ocorrerá durante um tempo de inatividade quando todo o trabalho será interrompido conforme a base de código mudar. Eles compreendem que somente o histórico do branch mestre atual estará disponível após a mudança.
- Eles estão preocupados com a alteração e desejam testá-la antes de fazer uma mudança completa. Eles desejam manter o acesso ao TFS mesmo após a mudança para o Azure DevOps Services.
- Eles têm várias coleções e desejam começar com uma que tenha apenas alguns projetos para entender melhor o processo.
- Eles compreendem que as coleções do TFS são uma relação de um para um com organizações do Azure DevOps Services; portanto, eles terão várias URLs. No entanto, isso corresponde ao seu modelo atual de separação para bases de código e projetos.


## <a name="proposed-architecture"></a>Arquitetura proposta

- A Contoso moverá seus projetos do TFS para a nuvem e não hospedará mais seus projetos ou controle do código-fonte no local.
- O TFS será migrado para o Azure DevOps Services.
- Atualmente, a Contoso tem uma coleção do TFS denominada **ContosoDev**, que será migrada para uma organização do Azure DevOps Services chamada **contosodevmigration.visualstudio.com**.
- Os projetos, os itens de trabalho, os bugs e as iterações do último ano serão migrados para o Azure DevOps Services.
- A Contoso utilizará o Azure Active Directory, que eles configuraram quando [implantaram sua infraestrutura do Azure](contoso-migration-infrastructure.md) no início de planejamento de migração. 


![Arquitetura de cenário](./media/contoso-migration-tfs-vsts/architecture.png) 


## <a name="migration-process"></a>Processo de migração

A Contoso concluirá o processo de migração da seguinte maneira:

1. Há muita preparação envolvida. Como primeira etapa, a Contoso precisa atualizar sua implementação do TFS para um nível compatível. No momento, a Contoso está executando o TFS 2017 Atualização 3, mas, para usar a migração de banco de dados, precisa executar uma versão de 2018 compatível com as atualizações mais recentes.
2. Após a atualização, a Contoso executará a ferramenta de migração do TFS e validará sua coleção.
3. Ela compilará um conjunto de arquivos de preparação e executará uma simulação de migração para testes.
4. Em seguida, a Contoso executará outra migração; desta vez uma migração completa que inclui itens de trabalho, bugs, sprints e código.
5. Após a migração, ela moverá seu código do TFVC para o Git.

![Processo de migração](./media/contoso-migration-tfs-vsts/migration-process.png) 


## <a name="scenario-steps"></a>Etapas do cenário

É assim que a Contoso concluirá a migração:

> [!div class="checklist"]
> * **Etapa 1: Criar uma conta do Armazenamento do Azure**: essa conta de armazenamento será usada durante o processo de migração.
> * **Etapa 2: Atualizar o TFS**: a Contoso atualizará sua implantação para o TFS 2018 Atualização 2. 
> * **Etapa 3: Validar a coleção**: a Contoso validará a coleção do TFS em preparação para a migração.
> * **Etapa 4: Criar arquivo de preparação**: a Contoso criará os arquivos de migração usando a Ferramenta de Migração do TFS. 


## <a name="step-1-create-a-storage-account"></a>Etapa 1: Criar uma conta de armazenamento

1. No portal do Azure, os administradores da Contoso criam uma conta de armazenamento (**contosodevmigration**).
2. Eles colocam a conta em sua região secundária que usam para failover – Centro dos EUA. Eles usam uma conta padrão de uso geral com o armazenamento localmente redundante.

    ![Conta de armazenamento](./media/contoso-migration-tfs-vsts/storage1.png) 


**Precisa de mais ajuda?**

- [Introdução ao Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction).
- [Criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).


## <a name="step-2-upgrade-tfs"></a>Etapa 2: Atualizar o TFS

Os administradores da Contoso atualizam o servidor do TFS para o TFS 2018 Atualização 2. Antes de começar, eles:

- Baixam o [TFS 2018 Atualização 2](https://visualstudio.microsoft.com/downloads/)
- Verificam os [requisitos de hardware](https://docs.microsoft.com/tfs/server/requirements) e leem todas as [notas de versão](https://docs.microsoft.com/visualstudio/releasenotes/tfs2018-relnotes) e as [armadilhas da atualização](https://docs.microsoft.com/tfs/server/upgrade/get-started#before-you-upgrade-to-tfs-2018).

Eles atualizam da seguinte maneira:

1. Para iniciar, eles fazer o backup do servidor TFS (em execução em uma vM VMware) e obtêm um instantâneo do VMware.

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade1.png) 

2. O instalador do TFS é iniciado e eles escolhem o local de instalação. O instalador precisa de acesso à Internet.

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade2.png) 

3. Após a conclusão da instalação, o Assistente de Configuração do Servidor é iniciado.

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade3.png) 

4. Após a verificação, o Assistente conclui a atualização.

     ![TFS](./media/contoso-migration-tfs-vsts/upgrade4.png) 

5. A instalação do TFS é verificada revisando projetos, itens de trabalho e código.

     ![TFS](./media/contoso-migration-tfs-vsts/upgrade5.png) 

> [!NOTE]
> Algumas atualizações do TFS precisam executar o Assistente de Configuração de Recursos após a conclusão da atualização. [Saiba mais](https://docs.microsoft.com/azure/devops/reference/configure-features-after-upgrade?utm_source=ms&utm_medium=guide&utm_campaign=vstsdataimportguide&view=vsts).

**Precisa de mais ajuda?**

Saiba mais sobre a [atualização do TFS](https://docs.microsoft.com/tfs/server/upgrade/get-started).

## <a name="step-3-validate-the-tfs-collection"></a>Etapa 3: Validar a coleção do TFS

Os administradores da Contoso executam a Ferramenta de Migração do TFS no banco de dados de coleção ContosoDev validá-lo antes da migração.

1. Eles fazem o download e descompactam a [Ferramenta de Migração do TFS](https://www.microsoft.com/download/details.aspx?id=54274). É importante baixar a versão para a atualização do TFS que está sendo executado. A versão pode ser verificada no console do administrador.

    ![TFS](./media/contoso-migration-tfs-vsts/collection1.png)

2. Eles executam a ferramenta para realizar a validação especificando a URL da coleção de projetos:

   **Tfsmigrator que validar /collection:http:\//contosotfs:8080/tfs/ContosoDev**


3. A ferramenta mostrará um erro.

    ![TFS](./media/contoso-migration-tfs-vsts/collection2.png)

5. Os arquivos de log encontrados estão localizados na pasta **Logs**, imediatamente antes da localização da ferramenta. Um arquivo de log é gerado para cada validação importante. **TfsMigration.log** contém as informações principais.

    ![TFS](./media/contoso-migration-tfs-vsts/collection3.png)

4. Encontram essa entrada, relacionada à identidade.

    ![TFS](./media/contoso-migration-tfs-vsts/collection4.png)

5. Eles executam **TfsMigration validate /help** na linha de comando e veem que o comando **/tenantDomainName** parece ser necessário para validar as identidades.

     ![TFS](./media/contoso-migration-tfs-vsts/collection5.png)

6. Eles executam novamente o comando de validação e incluem esse valor, junto com seu nome do Azure AD: **Tfsmigrator que validar /collection:http:\//contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com**.

    ![TFS](./media/contoso-migration-tfs-vsts/collection7.png)

7. Uma tela de Entrada do Azure AD é exibida e eles inserem as credenciais de um Usuário Administrador Global.

     ![TFS](./media/contoso-migration-tfs-vsts/collection8.png)

8. A validação passa e é confirmado pela ferramenta.

    ![TFS](./media/contoso-migration-tfs-vsts/collection9.png)



## <a name="step-4-create-the-migration-files"></a>Etapa 4: Criar os arquivos de migração

Com a validação completa, os administradores da Contoso podem usar a Ferramenta de Migração do TFS para compilar os arquivos de migração.

1. Eles executarem a etapa de preparação na ferramenta.

    **Tfsmigrator que preparar /collection:http:\//contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com /accountRegion:cus**

     ![Preparar](./media/contoso-migration-tfs-vsts/prep1.png)

    A preparação faz o seguinte:
    - Examina a coleção para localizar uma lista de todos os usuários e popular o log de mapa de identidades (**IdentityMapLog.csv**])
    - Prepara a conexão ao Azure Active Directory para localizar uma correspondência para cada identidade.
    - A Contoso já implantou o Azure AD e o sincronizado usando o AD Connect, portanto, a preparação deve ser capaz de encontrar as identidades correspondentes e marcá-las como Ativas.

2. Uma tela de Entrada do Azure AD é exibida e eles inserem as credenciais de um Administrador Global.

    ![Preparar](./media/contoso-migration-tfs-vsts/prep2.png)

3. A preparação é concluída e a ferramenta relata que os arquivos de importação foram gerados com êxito.

    ![Preparar](./media/contoso-migration-tfs-vsts/prep3.png)

4. Agora, podem ver que os arquivos IdentityMapLog.csv e import.json foram criados em uma nova pasta.

    ![Preparar](./media/contoso-migration-tfs-vsts/prep4.png)

5. O arquivo de import.json fornece configurações de importação. Ele inclui informações como o nome da organização desejada e informações da conta de armazenamento. A maioria dos campos é populada automaticamente. Alguns campos precisam da entrada do usuário. A Contoso abre o arquivo e adiciona o nome da organização do Azure DevOps Services a ser criado: **contosodevmigration**. Com esse nome, a URL do Azure DevOps Services será **contosodevmigration.visualstudio.com**.

    ![Preparar](./media/contoso-migration-tfs-vsts/prep5.png)

    > [!NOTE]
    > A organização deve ser criada antes da migração. Ela pode ser alterada após a conclusão da migração.

6. Eles examinam o arquivo de mapa de log de identidade que mostra as contas que serão trazidas para o Azure DevOps Services durante a importação. 

   - Identidades ativas referem-se a identidades que se tornarão usuários no Azure DevOps Services após a importação.
   - No Azure DevOps Services, essas identidades serão licenciadas e aparecerão como um usuário na organização após a migração.
   - Essas identidades são marcadas como **Ativa** na coluna **Status de Importação Esperado** no arquivo.

     ![Preparar](./media/contoso-migration-tfs-vsts/prep6.png)



## <a name="step-5-migrate-to-azure-devops-services"></a>Etapa 5: Migrar para o Azure DevOps Services

Com a preparação em vigor, os administradores da Contoso já podem se concentrar na migração. Depois de executar a migração, eles mudarão do TFVC para o Git para controle de versão.

Antes de começar, os administradores agendam o tempo de inatividade com a equipe de desenvolvimento, para deixar a coleção offline para a migração. Estas são as etapas para o processo de migração:

1. **Desanexar a coleção**: os dados de identidade para a coleção residem no banco de dados de configuração de servidor do TFS; já a coleção está anexada e online. Quando uma coleção é desanexada do servidor do TFS, ele usa uma cópia dos dados de identidade e a empacota com a coleção para transporte. Sem esses dados, a parte de identidade da importação não pode ser executada. É recomendável que a coleção permaneça desanexada até que a importação seja concluída, pois não há nenhuma maneira de importar as alterações que ocorreram durante a importação.
2. **Gerar um backup**: a próxima etapa do processo de migração é gerar um backup que pode ser importado para o Azure DevOps Services. O DACPAC (Pacotes de Componente de Aplicativos da Camada de Dados) é um recurso do SQL Server que permite que as alterações do banco de dados sejam empacotadas em um único arquivo e implantadas em outras instâncias do SQL. Ele também pode ser restaurado diretamente no Azure DevOps Services e, portanto, é usado como o método de empacotamento para levar os dados da coleção para a nuvem. A Contoso usará a ferramenta SqlPackage.exe para gerar o DACPAC. Essa ferramenta está incluída no SQL Server Data Tools.
3. **Carregar para o armazenamento**: após a criação do DACPAC, é feito o upload para o Armazenamento do Azure. Depois que ele é carregado, eles obtêm uma SAS (assinatura de acesso compartilhado) para permitir o acesso da Ferramenta de Migração do TFS ao armazenamento.
4. **Preencher a importação**: a Contoso pode, então, preencher os campos ausentes no arquivo de importação, incluindo a configuração do DACPAC. Para começar, eles especificarão o que desejam fazer uma importação de **Simulação**, para verificar se tudo está funcionando corretamente antes da migração completa.
5. **Fazer uma simulação**: as importações de simulação ajudam a testar a migração da coleção. As simulações têm uma vida útil limitada e são excluídas antes da execução de uma migração de produção. Elas são excluídas automaticamente após um período definido. Uma observação sobre quando a simulação será excluída está incluída no email de êxito recebido após a importação ser concluída. Tome nota e planeje adequadamente.
6. **Concluir a migração de produção**: com a migração de simulação concluída, os administradores da Contoso realizam a migração final atualizando o import.json e executando a importação novamente.



### <a name="detach-the-collection"></a>Desanexar a coleção

Antes de iniciar, os administradores da Contoso realizam um backup do SQL Server local e um instantâneo do VMware do servidor do TFS antes de desanexar.

1.  No console do Administrador do TFS, eles selecionam a coleção que desejam desanexar (**ContosoDev**).

    ![Migrar](./media/contoso-migration-tfs-vsts/migrate1.png)

2. Em **Geral**, eles selecionam **Desanexar Coleção**

    ![Migrar](./media/contoso-migration-tfs-vsts/migrate2.png)

3. No Assistente de Desanexação de Coleção de Projeto de Equipe > **Mensagem Manutenção**, eles fornecem uma mensagem para os usuários que podem tentar se conectar a projetos na coleção.

    ![Migrar](./media/contoso-migration-tfs-vsts/migrate3.png)

4. Em **Andamento da Desanexação**, eles monitoram o progresso e clicam em **Avançar** quando o processo termina.

    ![Migrar](./media/contoso-migration-tfs-vsts/migrate4.png)

5. Em **Verificações de Preparação**, quando as verificações terminam, eles clicam em **Desanexar**.

    ![Migrar](./media/contoso-migration-tfs-vsts/migrate5.png)

6. Clicarem **Fechar** para concluir.

    ![Migrar](./media/contoso-migration-tfs-vsts/migrate6.png)

6. A coleção não é mais referenciada no console do Administrador do TFS.

    ![Migrar](./media/contoso-migration-tfs-vsts/migrate7.png)


### <a name="generate-a-dacpac"></a>Gerar um DACPAC

A Contoso cria um backup (DACPAC) para importar no Azure DevOps Services.

- O SqlPackage.exe no SQL Server Data Tools é usado para criar o DACPAC. Há várias versões do SqlPackage.exe instaladas com o SQL Server Data Tools, localizadas em pastas com nomes como 120, 130 e 140. É importante usar a versão correta para preparar o DACPAC.
- Importações do TFS 2018 precisam usar o SqlPackage.exe da pasta 140 ou superior.  Para CONTOSOTFS, esse arquivo está localizado na pasta: **C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\140**.


Os administradores da Contoso geram o DACPAC da seguinte maneira:

1. Eles abrem um prompt de comando e navegam até o local de SQLPackage.exe. Eles digitam o seguinte comando para gerar o DACPAC:

    **SqlPackage.exe /sourceconnectionstring:"Data Source=SQLSERVERNAME\INSTANCENAME;Initial Catalog=Tfs_ContosoDev;Integrated Security=True" /targetFile:C:\TFSMigrator\Tfs_ContosoDev.dacpac /action:extract /p:ExtractAllTableData=true /p:IgnoreUserLoginMappings=true /p:IgnorePermissions=true /p:Storage=Memory** 

    ![Backup](./media/contoso-migration-tfs-vsts/backup1.png)

2. A seguinte mensagem é exibida depois que o comando é executado.

    ![Backup](./media/contoso-migration-tfs-vsts/backup2.png)

3. Eles verificam as propriedades do arquivo DACPAC

    ![Backup](./media/contoso-migration-tfs-vsts/backup3.png)

### <a name="update-the-file-to-storage"></a>Atualizar o arquivo para o armazenamento

Depois que o DACPAC é criado, a Contoso o carrega no Armazenamento do Azure.

1. Eles [baixam e instalam](https://azure.microsoft.com/features/storage-explorer/) o Gerenciador de Armazenamento do Azure.

    ![Carregar](./media/contoso-migration-tfs-vsts/backup5.png)

4. Eles se conectam à sua assinatura e localizam a conta de armazenamento que criaram para a migração (**contosodevmigration**). Eles criam um novo contêiner de blobs, **azuredevopsmigration**.

    ![Carregar](./media/contoso-migration-tfs-vsts/backup6.png)

5. Eles especificam o arquivo DACPAC para upload como um blob de blocos.

    ![Carregar](./media/contoso-migration-tfs-vsts/backup7.png)
    
7. Depois que o arquivo é carregado, eles clicam no nome do arquivo > **Gerar SAS**. Eles expandem os contêineres de blobs sob a conta de armazenamento, selecionam o contêiner com os arquivos de importação e clicam em **Obter Assinatura de Acesso Compartilhado**.

    ![Carregar](./media/contoso-migration-tfs-vsts/backup8.png)

8. Eles aceitam os padrões e clicam em **Criar**. Isso habilita o acesso por 24 horas.

    ![Carregar](./media/contoso-migration-tfs-vsts/backup9.png)

9. Eles copiam a URL da Assinatura de Acesso Compartilhado para que ela possa ser usada pela Ferramenta de Migração do TFS.

    ![Carregar](./media/contoso-migration-tfs-vsts/backup10.png)

> [!NOTE]
> A migração deve ocorrer antes dentro da janela de tempo permitida ou as permissões expirarão.
> Não gere uma chave de SAS do portal do Azure. As chaves geradas assim têm o escopo definido de acordo com a conta e não funcionarão com a importação.

### <a name="fill-in-the-import-settings"></a>Preencher as configurações de importação

Anteriormente, os administradores da Contoso preencheram parcialmente o arquivo de especificações de importação (import.json). Agora, precisam adicionar as configurações restantes.

Eles abrem o arquivo import.json e preenchem os seguintes campos: •   Location: local da chave SAS que foi gerada acima.
•   Dacpac: defina o nome para o arquivo DACPAC carregado para a conta de armazenamento. Incluem a extensão ".dacpac".
• ImportType: defina como DryRun por enquanto.


![Configurações de importação](./media/contoso-migration-tfs-vsts/import1.png)


### <a name="do-a-dry-run-migration"></a>Realizar uma migração de simulação

Os administradores da Contoso começam com uma migração de simulação para garantir que tudo está funcionando conforme o esperado.

1. Eles abrem um prompt de comando e apontam para a localização do TfsMigration (C:\TFSMigrator).
2. Como primeira etapa, eles validam o arquivo de importação. Eles querem ter certeza de que o arquivo está formatado corretamente e se a chave de SAS está funcionando.

    **TfsMigrator import /importFile:C:\TFSMigrator\import.json /validateonly**

3. A validação retorna um erro de que a chave de SAS precisa de um prazo de expiração mais longo.

    ![Simulação](./media/contoso-migration-tfs-vsts/test1.png)

3. Eles usam o Gerenciador de Armazenamento do Azure para criar uma nova chave SAS com expiração definida para sete dias.

    ![Simulação](./media/contoso-migration-tfs-vsts/test2.png)

3. Eles atualizam o arquivo import.json e executam a validação novamente. Dessa vez ela é concluída com êxito.

    **TfsMigrator import /importFile:C:\TFSMigrator\import.json /validateonly**

    ![Simulação](./media/contoso-migration-tfs-vsts/test3.png)
    
7. Eles iniciam a simulação:

    **TfsMigrator import /importFile:C:\TFSMigrator\import.json**

8. Uma mensagem é emitida para confirmar a migração. Observe o período pelo qual os dados preparados serão mantidos após a execução da simulação.

    ![Simulação](./media/contoso-migration-tfs-vsts/test4.png)

9. A Entrada no Azure AD aparece e deve ser preenchida com a entrada do Administrador da Contoso.

    ![Simulação](./media/contoso-migration-tfs-vsts/test5.png)

10. Uma mensagem mostra informações sobre a importação.

    ![Simulação](./media/contoso-migration-tfs-vsts/test6.png)

11. Depois de aproximadamente 15 minutos, eles navegam até a URL e veem as informações a seguir:

     ![Simulação](./media/contoso-migration-tfs-vsts/test7.png)

12. Após a migração terminar, um Líder de Desenvolvimento da Contoso entra no Azure DevOps Services para verificar se a simulação funcionou corretamente. Após a autenticação, o Azure DevOps Services precisa de alguns detalhes para confirmar a organização.

    ![Simulação](./media/contoso-migration-tfs-vsts/test8.png)

13. No Azure DevOps Services, o líder de desenvolvimento pode ver que os projetos foram migrados para o Azure DevOps Services. Há um aviso de que a organização será excluída em 15 dias.

    ![Simulação](./media/contoso-migration-tfs-vsts/test9.png)

14. O Líder de Desenvolvimento abre um dos projetos e abre **Itens de Trabalho** > **Atribuídos a mim**. Isso mostra que os dados do item de trabalho foram migrados, juntamente com a identidade.

    ![Simulação](./media/contoso-migration-tfs-vsts/test10.png)

15. O cliente potencial também verifica outros projetos e o código, para confirmar se o código-fonte e o histórico foram migrados.

    ![Simulação](./media/contoso-migration-tfs-vsts/test11.png)


### <a name="run-the-production-migration"></a>Executar a migração de produção

Com a simulação completa, os administradores da Contoso passam para a migração de produção. Eles excluem a simulação, atualizam as configurações de importação e executam a importação novamente.

1. No portal do Azure DevOps Services, eles excluem a organização de simulação.
2. Atualizam o arquivo import.json para definir **ImportType** como **ProductionRun**.

    ![Produção](./media/contoso-migration-tfs-vsts/full1.png)

3. Eles iniciam a migração como fizeram com a simulação: **TfsMigrator import /importFile:C:\TFSMigrator\import.json**.
4. Uma mensagem é exibida para confirmar a migração e avisa que os dados poderiam ser mantidos em um local seguro, como uma área de preparo por até sete dias.

    ![Produção](./media/contoso-migration-tfs-vsts/full2.png)

5. Na Entrada do Azure AD, especificam uma entrada de Administrador da Contoso.

    ![Produção](./media/contoso-migration-tfs-vsts/full3.png)

6. Uma mensagem mostra informações sobre a importação.

    ![Produção](./media/contoso-migration-tfs-vsts/full4.png)

7. Após aproximadamente 15 minutos, navegam até a URL e veem as informações a seguir:

    ![Produção](./media/contoso-migration-tfs-vsts/full5.png)

8. Após a migração terminar, um Líder de Desenvolvimento da Contoso faz logon no Azure DevOps Services para verificar se a migração funcionou corretamente. Após fazer logon, ele pode ver que os projetos foram migrados.

    ![Produção](./media/contoso-migration-tfs-vsts/full6.png)

8. O Líder de Desenvolvimento abre um dos projetos e abre **Itens de Trabalho** > **Atribuídos a mim**. Isso mostra que os dados do item de trabalho foram migrados, juntamente com a identidade.

    ![Produção](./media/contoso-migration-tfs-vsts/full7.png)

9. O cliente potencial verifica outros dados de item de trabalho para confirmar.

    ![Produção](./media/contoso-migration-tfs-vsts/full8.png)

15. O cliente potencial também verifica outros projetos e o código, para confirmar se o código-fonte e o histórico foram migrados.

    ![Produção](./media/contoso-migration-tfs-vsts/full9.png)


### <a name="move-source-control-from-tfvc-to-git"></a>Mover o controle do código-fonte do TFVC para o GIT

Com a migração concluída, a Contoso deseja mudar do TFVC para o Git para gerenciamento de código-fonte. Eles precisam importar o código-fonte atualmente em sua organização do Azure DevOps Services como repositórios Git na mesma organização.

1. No portal do Azure DevOps Services, eles abrem um dos repositórios do TFVC (**$/PolicyConnect**) e o examinam.

    ![Git](./media/contoso-migration-tfs-vsts/git1.png)

2. Clicam no menu suspenso **Fonte** > **Importar**.

    ![Git](./media/contoso-migration-tfs-vsts/git2.png)

3. Na **Tipo de fonte**, selecionam **TFVC** e especificam o caminho para o repositório. Eles decidiram não migrar o histórico.

    ![Git](./media/contoso-migration-tfs-vsts/git3.png)

    > [!NOTE]
    > Devido às diferenças em como o TFVC e o Git armazenam informações de controle de versão, é recomendável que a Contoso não migre o histórico. Essa é a abordagem que a Microsoft adotou quando migrou o Windows e outros produtos do controle de versão centralizado para o Git.

4. Após a importação, os administradores revisam o código.

    ![Git](./media/contoso-migration-tfs-vsts/git4.png)

5. Eles repetem o processo para o segundo repositório (**$/SmartHotelContainer**).

    ![Git](./media/contoso-migration-tfs-vsts/git5.png)

6. Depois de examinar a fonte, o Líder de Desenvolvimento concorda que a migração para o Azure DevOps Services está concluída. Agora, o Azure DevOps Services se torna a fonte para todo o desenvolvimento dentro das equipes envolvidas na migração.

    ![Git](./media/contoso-migration-tfs-vsts/git6.png)



**Precisa de mais ajuda?**

[Saiba mais](https://docs.microsoft.com/azure/devops/repos/git/import-from-TFVC?view=vsts) sobre a importação do TFVC.

##  <a name="clean-up-after-migration"></a>Limpar após a migração

Com a migração completa, a Contoso precisa fazer o seguinte:

- Examinar o artigo sobre [depois da importação](https://docs.microsoft.com/azure/devops/articles/migration-post-import?view=vsts) para obter informações sobre atividades de importação adicionais.
- Excluir os repositórios do TFVC ou colocá-los no modo somente leitura. As bases de código não devem ser usadas, mas podem ser referenciadas para seu histórico.

## <a name="next-steps"></a>Próximas etapas

A Contoso precisará fornecer treinamento sobre o Azure DevOps Services e o Git relevante para os membros da equipe.



