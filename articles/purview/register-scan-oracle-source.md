---
title: Registrar uma fonte do Oracle e configurar verificações (versão prévia) no Azure Purview
description: Este artigo descreve como registrar uma fonte do Oracle no Azure Purview e configurar uma verificação.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 76aadd667691e12c61e0e5e13c13ca0241a9f0ce
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045494"
---
# <a name="register-and-scan-oracle-source-preview"></a>Registrar e verificar a fonte do Oracle (versão prévia)

Este artigo descreve como registrar um banco de dados do Oracle no Azure Purview e configurar uma verificação.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

A fonte do Oracle dá suporte à **Verificação completa** para extrair metadados de um banco de dados do Oracle e busca a **Linhagem** entre os ativos de dados.

## <a name="prerequisites"></a>Pré-requisitos

1.  Configure o [runtime de integração auto-hospedada](https://www.microsoft.com/download/details.aspx?id=39717) mais recente.
    Para obter mais informações, confira [Criar e configurar um runtime de integração auto-hospedada](../data-factory/create-self-hosted-integration-runtime.md).

2.  Verifique se o [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) está instalado na máquina virtual em que o runtime de integração auto-hospedada está instalado.

3.  Verifique se o \"Pacote Redistribuível do Visual C++ 2012, Atualização 4\" está instalado no computador do runtime de integração auto-hospedada. Se ele ainda não estiver instalado, baixe-o [aqui](https://www.microsoft.com/download/details.aspx?id=30679).

4.  Você precisará baixar manualmente um driver JDBC do Oracle [daqui](https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html) em sua máquina virtual na qual o runtime de integração auto-hospedada está em execução.

    > [!Note] 
    > O driver deve estar acessível para todas as contas na VM. Não o instale em uma conta de usuário.

5.  As versões do banco de dados do Oracle compatíveis são a 6i até a 19C.

6.  Permissão de usuário: para garantir uma verificação bem-sucedida na primeira vez, é necessária uma permissão de administrador do tipo Full Sys.

    Para verificações subsequentes, é necessário ter um acesso somente leitura às tabelas do sistema. O usuário deve ter permissão para criar uma sessão, bem como a função SELECT\_CATALOG\_ROLE atribuída. Como alternativa, o usuário pode ter a permissão SELECT concedida individualmente para cada tabela de sistema na qual esse conector consulte metadados:
       > grant create session to \[user\];\
        grant select on all\_users to \[user\];\
        grant select on dba\_objects to \[user\];\
        grant select on dba\_tab\_comments to \[user\];\
        grant select on dba\_external\_locations to \[user\];\
        grant select on dba\_directories to \[user\];\
        grant select on dba\_mviews to \[user\];\
        grant select on dba\_clu\_columns to \[user\];\
        grant select on dba\_tab\_columns to \[user\];\
        grant select on dba\_col\_comments to \[user\];\
        grant select on dba\_constraints to \[user\];\
        grant select on dba\_cons\_columns to \[user\];\
        grant select on dba\_indexes to \[user\];\
        grant select on dba\_ind\_columns to \[user\];\
        grant select on dba\_procedures to \[user\];\
        grant select on dba\_synonyms to \[user\];\
        grant select on dba\_views to \[user\];\
        grant select on dba\_source to \[user\];\
        grant select on dba\_triggers to \[user\];\
        grant select on dba\_arguments to \[user\];\
        grant select on dba\_sequences to \[user\];\
        grant select on dba\_dependencies to \[user\];\
        grant select on V\_\$INSTANCE to \[user\];\
        grant select on v\_\$database to \[user\];
    
## <a name="setting-up-authentication-for-a-scan"></a>Configurar a autenticação para uma verificação

A única autenticação compatível com uma fonte do Oracle é a **Autenticação básica**.

## <a name="register-an-oracle-source"></a>Registrar uma fonte do Oracle

Para registrar uma nova fonte do Oracle no catálogo de dados, faça o seguinte:

1.  Acesse sua conta do Purview.
2.  Selecione **Fontes** no painel de navegação à esquerda.
3.  Escolha **Registrar**
4.  Em Registrar fontes, selecione **Oracle**. Selecione **Continuar**.

    :::image type="content" source="media/register-scan-oracle-source/register-sources.png" alt-text="registrar fontes" border="true":::

Na tela **Registrar fontes (Oracle)** , faça o seguinte:

1.  Insira um **Nome** com o qual a fonte de dados será listada no Catálogo.

2.  Insira o nome do **Host** para se conectar a uma fonte do Oracle. Pode ser:
    - Um nome de host usado pelo JDBC para se conectar ao servidor de banco de dados. Por exemplo, MyDatabaseServer.com ou
    - Endereço IP. Por exemplo,192.169.1.2 ou
    - Sua cadeia de conexão do JDBC totalmente qualificada. Por exemplo,\
        jdbc:oracle:thin:@(DESCRIPTION=(LOAD\_BALANCE=on)(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver1)(PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver2)(PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver3)(PORT=1521))(CONNECT\_DATA=(SERVICE\_NAME=orcl)))

3.  Insira o **Número da porta** usado pelo JDBC para se conectar ao servidor de banco de dados (1521 por padrão para Oracle).

4.  Insira o **Nome do serviço Oracle** usado pelo JDBC para se conectar ao servidor de banco de dados.

5.  Selecionar ou criar uma coleção (opcional)

6.  Conclua a etapa para registrar a fonte de dados.

    :::image type="content" source="media/register-scan-oracle-source/register-sources-2.png" alt-text="opções de registro de fontes" border="true":::

## <a name="creating-and-running-a-scan"></a>Criando e executando uma verificação

Para criar e executar uma nova verificação, faça o seguinte:

1.  No Centro de Gerenciamento, clique em Runtimes de integração. Verifique se um runtime de integração auto-hospedada está configurado. Caso não esteja configurado, use as etapas mencionadas [aqui](./manage-integration-runtimes.md) para criar um runtime de integração auto-hospedada.

2.  Navegue até **Fontes**.

3.  Selecione a fonte do Oracle registrada.

4.  Selecione **+ Nova verificação**.

5.  Forneça os detalhes abaixo:

    a.  **Nome**: o nome da verificação

    b.  **Conectar-se por meio do runtime de integração**: selecione o runtime de integração auto-hospedada

    c.  **Credencial**: selecione a credencial para se conectar à fonte de dados. Não se esqueça de:
    - Selecionar Autenticação básica ao criar uma credencial.        
    - Forneça o nome de usuário usado pelo JDBC para se conectar ao servidor de banco de dados no campo de entrada Nome de usuário        
    - Armazene a senha de usuário usada pelo JDBC para se conectar ao servidor de banco de dados na chave secreta.

    d.  **Esquema**: liste o subconjunto de esquemas a serem importados expressos como uma lista separada por ponto e vírgula. Por exemplo, schema1; schema2. Todos os esquemas de usuário serão importados se essa lista estiver vazia. Todos os esquemas (por exemplo, SysAdmin) e objetos do sistema são ignorados por padrão. Quando a lista estiver vazia, todos os esquemas disponíveis serão importados.
        Padrões de nome de esquema aceitáveis usando a sintaxe de expressões SQL LIKE incluem o uso de %, por exemplo: A%; %B; %C%; D
       -   começar com A ou        
       -   terminar com B ou        
       -   conter C ou        
       -   igual a D

    O uso de NOT e de caracteres especiais não é aceitável.

6.  **Local do driver**: especifique o caminho para o local do driver JDBC em sua VM, no qual o runtime de integração auto-hospedada está em execução. Esse deve ser o caminho para o local da pasta JAR válida.

7.  **Memória máxima disponível**: memória máxima (em GB) disponível na VM do cliente a ser usada pelos processos de verificação. Isso depende do tamanho da fonte do SAP S/4HANA a ser verificada.

    :::image type="content" source="media/register-scan-oracle-source/scan.png" alt-text="verificar oracle" border="true":::

8.  Clique em **Continuar**.

9.  Escolha o **gatilho da verificação**. Você pode configurar uma agenda ou executar o exame uma vez.

10.  Analise a verificação e clique em **Salvar e Executar**.

## <a name="viewing-your-scans-and-scan-runs"></a>Exibindo as verificações e as execuções de verificação

1. Navegue até o centro de gerenciamento. Selecione **Fontes de dados** na seção **Fontes e verificação**.

2. Selecione a fonte de dados desejada. Você verá uma lista de verificações existentes nessa fonte de dados.

3. Selecione a verificação cujos resultados você está interessado em exibir.

4. Esta página mostrará que todas as verificações anteriores são executadas junto com as métricas e o status de cada execução de verificação. Ele também exibirá se a verificação foi agendada ou manual, quantos ativos tiveram classificações aplicadas, quantos ativos totais foram descobertos, a hora de início e de término da verificação e a duração total da verificação.

## <a name="manage-your-scans"></a>Gerenciar os exames

Para gerenciar ou excluir uma verificação, faça o seguinte:

1. Navegue até o centro de gerenciamento. Selecione **Fontes de dados** na seção **Fontes e verificação** e selecione a fonte de dados desejada.

2. Selecione a verificação que você deseja gerenciar. Você pode editar a verificação selecionando **Editar**.

3. Você pode excluir sua verificação selecionando **Excluir**.

## <a name="next-steps"></a>Próximas etapas

- [Navegar pelo Catálogo de Dados do Azure Purview](how-to-browse-catalog.md)
- [Pesquisar no Catálogo de Dados do Azure Purview](how-to-search-catalog.md)