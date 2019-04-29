---
title: Provisionar no Azure multilocatário de SaaS | Microsoft Docs
description: Saiba como provisionar e catalogar novos locatários em um aplicativo SaaS multilocatário do Banco de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib,andrela,stein
manager: craigg
ms.date: 09/24/2018
ms.openlocfilehash: d29baaad6090cea5eb31f5f50bba444cb3771155
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485847"
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>Provisionar e catalogar novos locatários em um aplicativo de SaaS usando um banco de dados SQL do Azure multilocatário fragmentado do Azure

Este artigo aborda o provisionamento e a catalogação de novos locatários, em um modelo ou padrão de *banco de dados fragmentado multilocatário*.

Este artigo tem duas partes principais:

- [Discussão conceitual](#goto_2_conceptual) do provisionamento e da catalogação de novos locatários.

- [Tutorial](#goto_1_tutorial) que destaca o código de script do PowerShell que realiza o provisionamento e a catalogação.
  - O tutorial usa o aplicativo de SaaS de Wingtip Tickets, adaptado para o padrão de banco de dados fragmentado multilocatário.

<a name="goto_2_conceptual"/>

## <a name="database-pattern"></a>Padrão de banco de dados

Esta seção e as próximas discutem os conceitos do padrão de banco de dados fragmentado multilocatário.

Neste modelo fragmentado multilocatário, os esquemas de tabela dentro de cada banco de dados incluem uma chave de locatário na chave primária das tabelas que armazenam dados de locatário. A chave de locatário permite que cada banco de dados individual armazene zero, um ou vários locatários. O uso de bancos de dados fragmentados contribui para que o sistema de aplicativos tenha capacidade para um grande número de locatários. Todos os dados de um locatário são armazenados em um banco de dados. O grande número de locatários é distribuído entre vários bancos de dados fragmentados. Um banco de dados do catálogo armazena o mapeamento de cada locatário para seu banco de dados.

#### <a name="isolation-versus-lower-cost"></a>Isolamento em relação ao custo mais baixo

Um locatário que tem um banco de dados inteiro para si usufrui dos benefícios do isolamento. O locatário pode restaurar o banco de dados para uma data anterior sem ser restringido pelo impacto em outros locatários. O desempenho do banco de dados pode ser ajustado para ser otimizado para um único locatário, novamente sem a necessidade de se comprometer com outros locatários. O problema é que o isolamento custa mais do que o compartilhamento de um banco de dados com outros locatários.

Quando um novo locatário é provisionado, ele pode compartilhar um banco de dados com outros locatários ou pode ser colocado em seu próprio banco de dados novo. Posteriormente, você poderá mudar de ideia e mover o banco de dados para a outra situação.

Bancos de dados com vários locatários e locatários únicos podem ser combinados no mesmo aplicativo de SaaS para otimizar o custo ou o isolamento para cada locatário.

   ![Aplicativo de banco de dados multilocatário fragmentado com catálogo de locatários](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>Padrão do catálogo de locatários

Quando você tiver dois ou mais bancos de dados contendo pelo menos um locatário, o aplicativo deverá ter uma maneira de descobrir qual banco de dados armazena o locatário de interesse no momento. Um banco de dados de catálogo armazena esse mapeamento.

#### <a name="tenant-key"></a>Chave do locatário

Para cada locatário, o aplicativo Wingtip pode derivar uma chave exclusiva, que é a chave do locatário. O aplicativo extrai o nome do locatário da URL da página da Web. O aplicativo corta o nome para obter a chave. O aplicativo usa a chave para acessar o catálogo. O catálogo cruza referências de informações sobre o banco de dados no qual o locatário está armazenado. O aplicativo usa as informações do banco de dados para conectar-se. Outros esquemas de chave de locatário também podem ser usados.

Usar um catálogo permite que o nome ou o local de um banco de dados de locatário seja alterado após provisionar sem interromper o aplicativo. Em um modelo de banco de dados multilocatário, o catálogo acomoda a movimentação de um locatário entre bancos de dados.

#### <a name="tenant-metadata-beyond-location"></a>Metadados do locatário além do local

O catálogo também pode indicar se um locatário está offline para manutenção ou outras ações. E o catálogo pode ser estendido para armazenar metadados adicionais do locatário ou do banco de dados, como os seguintes itens:
- A camada de serviço ou a edição de um banco de dados.
- A versão do esquema do banco de dados.
- O nome do locatário e seu SLA (Contrato de Nível de Serviço).
- Informações para habilitar o gerenciamento de aplicativos, o suporte ao cliente ou os processos de DevOps.  

O catálogo também pode ser usado para habilitar relatórios entre locatários, gerenciamento de esquemas e extração de dados para fins de análise. 

### <a name="elastic-database-client-library"></a>Biblioteca de cliente do Banco de Dados Elástico 

Em Wingtip, o catálogo é implementado no banco de dados *tenantcatalog*. O *tenantcatalog* é criado usando os recursos de gerenciamento de fragmento da [EDCL (Biblioteca de Clientes do Banco de Dados Elástico)](sql-database-elastic-database-client-library.md). A biblioteca permite que um aplicativo crie, gerencie e use um *mapa de fragmentos* que é armazenado em um banco de dados. Um mapa de fragmentos cruza referências da chave de locatário com seu fragmento, o que significa seu banco de dados fragmentado.

Durante o provisionamento de locatários, as funções EDCL podem ser usadas em aplicativos ou em scripts do PowerShell para criar as entradas no mapa de fragmentos. Mais tarde, as funções EDCL podem ser usadas para conectar-se ao banco de dados correto. O EDCL armazena as informações de conexão em cache para minimizar o tráfego no banco de dados do catálogo e acelerar o processo de conexão.

> [!IMPORTANT]
> *Não* edite os dados no banco de dados do catálogo por meio do acesso direto! As atualizações diretas não são compatíveis devido ao alto risco de dados corrompidos. Nesse caso, edite os dados de mapeamento somente por meio de APIs de EDCL.

## <a name="tenant-provisioning-pattern"></a>Padrão de provisionamento de locatários

#### <a name="checklist"></a>Lista de verificação

Quando você quiser provisionar um novo locatário em um banco de dados compartilhado existente, responda às seguintes perguntas em relação ao banco de dados compartilhado:
- Ele tem espaço suficiente para o novo locatário?
- Ele tem tabelas com os dados de referência necessários para o novo locatário, ou os dados podem ser adicionados?
- Ele tem a variação apropriada do esquema base para o novo locatário?
- Ele está no local geográfico apropriado próximo ao novo locatário?
- Ele está na camada de serviço certa do novo locatário?

Quando você desejar que o novo locatário seja isolado em seu próprio banco de dados, crie-o para atender às especificações do locatário.

Depois que o provisionamento for concluído, você deverá registrar o locatário no catálogo. Por fim, o mapeamento de locatários pode ser adicionado para referenciar o fragmento adequado.

#### <a name="template-database"></a>Banco de dados de modelo

Provisione o banco de dados executando scripts SQL, implantando um bacpac ou copiando um banco de dados de modelo. Os aplicativos Wingtip copiam um banco de dados de modelo para criar novos bancos de dados de locatário.

Assim como qualquer aplicativo, o Wingtip evoluirá ao longo do tempo. Às vezes, o Wingtip exige alterações no banco de dados. As alterações podem incluir os seguintes itens:
- Esquema novo ou alterado.
- Dados de referência novos ou alterados.
- Tarefas de manutenção de rotina do banco de dados para garantir o desempenho ideal do aplicativo.

Com um aplicativo SaaS, essas alterações precisam ser implantadas de maneira coordenada em uma frota potencialmente grande de bancos de dados de locatário. Para que essas alterações estejam em bancos de dados de locatário futuros, elas precisam ser incorporadas ao processo de provisionamento. Esse desafio é mais explorado no [tutorial de gerenciamento de esquema](saas-tenancy-schema-management.md).

#### <a name="scripts"></a>Scripts

Os scripts de provisionamento de locatário neste tutorial são compatíveis com os dois cenários a seguir:
- O provisionamento de um locatário em um banco de dados existente compartilhado com outros locatários.
- O provisionamento de um locatário em seu próprio banco de dados.

Os dados de locatário são inicializados e registrados no mapa de fragmentos de catálogo. No aplicativo de exemplo, os bancos de dados que contêm vários locatários recebem um nome genérico, como *tenants1* ou *tenants2*. Os bancos de dados que contêm um único locatário recebem o nome do locatário. As convenções de nomenclatura específicas usadas no exemplo não são uma parte crítica do padrão, uma vez que o uso de um catálogo permite que qualquer nome seja atribuído ao banco de dados.  

<a name="goto_1_tutorial"/>

## <a name="tutorial-begins"></a>Início do tutorial

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Provisionar um locatário em um banco de dados multilocatário
> * Provisionar um locatário em um banco de dados de locatário único
> * Provisionar um lote de locatários em bancos de dados multilocatários e de locatário único
> * Registrar um banco de dados e o mapeamento de locatários em um catálogo

#### <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

- O Azure PowerShell está instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

- O aplicativo SaaS de Banco de Dados Multilocatário Wingtip Tickets foi implantado. Para implantá-lo em menos de cinco minutos, confira [Implantar e explorar o aplicativo SaaS de Banco de Dados Multilocatário Wingtip Tickets](saas-multitenantdb-get-started-deploy.md)

- Obtenha os scripts e o código-fonte do Wingtip:
    - Os scripts e o código-fonte do aplicativo SaaS de Banco de Dados Multilocatário Wingtip Tickets estão disponíveis no repositório [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) do GitHub.
    - Consulte as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas para baixar e desbloquear os scripts do Wingtip. 

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>Provisionar um locatário em um banco de dados *compartilhado* com outros locatários

Nesta seção, você verá uma lista das principais ações de provisionamento que são realizadas pelos scripts do PowerShell. Em seguida, você usará o depurador do ISE do PowerShell para percorrer os scripts e ver as ações no código.

#### <a name="major-actions-of-provisioning"></a>Principais ações de provisionamento

A seguir, estão os principais elementos do fluxo de trabalho de provisionamento que você percorrerá:

- **Calcule a nova chave de locatário**: Uma função de hash é usada para criar a chave de locatário com base no nome do locatário.
- **Verifique se a chave de locatário já existe**: O catálogo é verificado para garantir que a chave ainda não tenha sido registrada.
- **Inicializar o locatário no banco de dados de locatário padrão**: O banco de dados de locatário é atualizado para adicionar as novas informações de locatário.  
- **Registrar o locatário no catálogo**: o mapeamento entre a nova chave do locatário e o banco de dados tenants1 existente é adicionado ao catálogo. 
- **Adicionar o nome do locatário a uma tabela de extensão de catálogo**: O nome do local é adicionado à tabela Locatários no catálogo.  Essa adição mostra como o banco de dados de catálogo pode ser estendido para permitir dados específicos do aplicativo adicionais.
- **Abrir página Eventos para o novo locatário**: A página de eventos *Bushwillow Blues* é aberta no navegador.

   ![events](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>Etapas do depurador

Para entender como o aplicativo Wingtip implementa o provisionamento do novo locatário em um banco de dados compartilhado, adicione um ponto de interrupção e percorra o fluxo de trabalho:

1. No *ISE do PowerShell*, abra ...\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* e defina estes parâmetros:
   - **$TenantName** = **Bushwillow Blues**, o nome de um novo local.
   - **$VenueType** = **blues**, um dos tipos de local predefinidos: blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer (letras minúsculas, sem espaços).
   - **$DemoScenario** = **1**, para provisionar um locatário em um banco de dados compartilhado com outros locatários.

2. Adicione um ponto de interrupção, colocando o cursor em qualquer local na linha 38, que diz: *New-Tenant '*, em seguida, pressione **F9**.

   ![ponto de interrupção](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. Execute o script pressionando **F5**.

4. Depois que a execução do script for interrompida no ponto de interrupção, pressione **F11** para intervir no código.

   ![depurar](media/saas-multitenantdb-provision-and-catalog/debug.png)

5. Rastreie a execução do script usando as opções de menu **Depurar**, **F10** e **F11** para contornar ou intervir nas funções chamadas.

Para saber mais sobre como depurar scripts do PowerShell, confira [Dicas sobre como trabalhar e depurar scripts do PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).

## <a name="provision-a-tenant-in-its-own-database"></a>Provisionar um locatário no seu *próprio* banco de dados

#### <a name="major-actions-of-provisioning"></a>Principais ações de provisionamento

A seguir, estão os principais elementos do fluxo de trabalho que você percorrerá ao rastrear o script:

- **Calcule a nova chave de locatário**: Uma função de hash é usada para criar a chave de locatário com base no nome do locatário.
- **Verifique se a chave de locatário já existe**: O catálogo é verificado para garantir que a chave ainda não tenha sido registrada.
- **Crie um novo banco de dados de locatário**: O banco de dados é criado copiando o banco de dados *basetenantdb* usando um modelo do Resource Manager.  O nome do novo banco de dados é baseado no nome do locatário.
- **Adicione o banco de dados ao catálogo**: O novo banco de dados de locatário é registrado como um fragmento no catálogo.
- **Inicializar o locatário no banco de dados de locatário padrão**: O banco de dados de locatário é atualizado para adicionar as novas informações de locatário.  
- **Registrar o locatário no catálogo**: o mapeamento entre a nova chave do locatário e o banco de dados *sequoiasoccer* é adicionado ao catálogo.
- **O nome do locatário é adicionado ao catálogo**: O nome do local é adicionado à tabela de extensão Locatários no catálogo.
- **Abrir página Eventos para o novo locatário**: A página de eventos *Sequoia Soccer* é aberta no navegador.

   ![events](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>Etapas do depurador

Agora, percorra o processo de script ao criar um locatário em seu próprio banco de dados:

1. Ainda em ...\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1*, defina estes parâmetros:
   - **$TenantName** = **Sequoia Soccer**, o nome de um novo local.
   - **$VenueType** = **soccer**, um dos tipos de local predefinidos: blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer (em minúsculas, sem espaços).
   - **$DemoScenario** = **2**, para provisionar um locatário em seu próprio banco de dados.

2. Adicione um novo ponto de interrupção posicionando seu cursor em qualquer lugar na linha 57, que diz: *&&nbsp;$PSScriptRoot\New-TenantAndDatabase `* e pressione **F9**.

   ![ponto de interrupção](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. Execute o script pressionando **F5**.

4. Depois que a execução do script for interrompida no ponto de interrupção, pressione **F11** para entrar no código.  Use **F10** e **F11** para contornar e intervir em funções para rastrear a execução.

## <a name="provision-a-batch-of-tenants"></a>Provisionar um lote de locatários

Este exercício provisiona um lote com 17 locatários. É recomendável provisionar esse lote de locatários antes de iniciar outros tutoriais do Wingtip Tickets, de forma que haja mais bancos de dados com os quais trabalhar.

1. No *ISE do PowerShell*, abra ...\\Módulos de Aprendizagem\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* e altere o parâmetro *$DemoScenario* para 4:
   - **$DemoScenario** = **4** para provisionar um lote de locatários em um banco de dados compartilhado.

2. Pressione **F5** e execute o script.

### <a name="verify-the-deployed-set-of-tenants"></a>Verificar o conjunto implantado de locatários 

Neste estágio, você tem uma combinação de locatários implantados em um banco de dados compartilhado e locatários implantados em seus próprios bancos de dados. O Portal do Azure pode ser usado para inspecionar os bancos de dados criados. No [Portal do Azure](https://portal.azure.com), abra o servidor **tenants1-mt-\<USUÁRIO\>** navegando até a lista de servidores SQL.  A lista de **bancos de dados SQL** deve incluir o banco de dados **tenants1** compartilhado e os bancos de dados para os locatários que estão em seu próprio banco de dados:

   ![lista de banco de dados](media/saas-multitenantdb-provision-and-catalog/Databases.png)

Embora o Portal do Azure mostre os bancos de dados de locatário, ele não permite que você veja os locatários *dentro* do banco de dados compartilhado. A lista completa de locatários pode ser vista na página da Web **Hub de Eventos** do Wingtip e navegando no catálogo.

#### <a name="using-wingtip-tickets-events-hub-page"></a>Usando a página de hub de eventos de tíquetes do Wingtip

Abra a página Hub de eventos no navegador (http:events.wingtip-mt.\<USUÁRIO\>.trafficmanager.net)  

#### <a name="using-catalog-database"></a>Usando o banco de dados do catálogo

A lista completa de locatários e o banco de dados correspondente para cada um está disponível no catálogo. É fornecido um modo SQL que o une o nome de locatário ao nome do banco de dados. O modo oferece uma boa demonstração da importância de estender os metadados armazenados no catálogo.
- O modo SQL está disponível no banco de dados tenantcatalog.
- O nome do locatário é armazenado na tabela Locatários.
- O nome do banco de dados é armazenado nas tabelas de gerenciamento de fragmentos.

1. No SQL Server Management Studio (SSMS), conecte-se ao servidor de locatários em **catalog-MT.\<usuário\>. database.windows.net**, com o logon = **desenvolvedor**e a senha =  **P\@ssword1**

    ![caixa de diálogo de conexão do SSMS](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. No Pesquisador de Objetos do SSMS, navegue até as exibições no banco de dados *tenantcatalog*.

3. Clique com o botão direito do mouse na exibição *TenantsExtended* e escolha **Selecionar as 1000 primeiras linhas**. Observe o mapeamento entre o nome do locatário e o banco de dados de diferentes locatários.

    ![Exibição ExtendedTenants no SSMS](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>Outros padrões de provisionamento

Esta seção aborda outros padrões de provisionamento interessantes.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>Pré-provisionamento de bancos de dados de pools elásticos

O padrão de pré-provisionamento explora o fato de que, ao usar pools elásticos, a cobrança é pelo pool, não pelos bancos de dados. Assim, os bancos de dados podem ser adicionados a um pool elástico antes que eles sejam necessários sem incorrer em custo extra. Este pré-provisionamento reduz significativamente o tempo necessário para provisionar um locatário em um banco de dados. O número de bancos de dados pré-provisionados pode ser ajustado conforme necessário para manter um buffer adequado para a taxa de provisionamento esperada.

#### <a name="auto-provisioning"></a>Provisionamento automático

No padrão de provisionamento automático, um serviço de provisionamento dedicado é usado para provisionar servidores, pools e bancos de dados automaticamente, conforme o necessário. Essa automação inclui o pré-provisionamento de bancos de dados em pools elásticos. E, se os bancos de dados forem encerrados e excluídos, as lacunas criadas nos pools elásticos poderão ser preenchidas pelo serviço de provisionamento conforme o desejado.

Esse tipo de serviço automatizado pode ser simples ou complexo. Por exemplo, a automação pode lidar com o provisionamento em várias regiões geográficas e pode configurar a replicação geográfica para recuperação de desastre. Com o padrão de provisionamento automático, um script ou aplicativo cliente envia uma solicitação de provisionamento a uma fila para ser processada por um serviço de provisionamento. Em seguida, o script sondará para detectar a conclusão. Se o pré-provisionamento for usado, as solicitações serão manipuladas rapidamente, enquanto um serviço de segundo plano gerencia o provisionamento de um banco de dados de substituição.

## <a name="additional-resources"></a>Recursos adicionais

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [Biblioteca de cliente do banco de dados elástico](sql-database-elastic-database-client-library.md)
- [Como depurar scripts no ISE do Windows PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Provisionar um novo locatário único em um banco de dados multilocatário e em seu próprio banco de dados
> * Provisionar um lote de locatários adicionais
> * Percorrer os detalhes do provisionamento de locatários e do registro deles no catálogo

Experimente o [Tutorial de monitoramento de desempenho](saas-multitenantdb-performance-monitoring.md).

