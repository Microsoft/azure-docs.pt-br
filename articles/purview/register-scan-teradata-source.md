---
title: Registrar uma fonte do Teradata e configurar verificações (versão prévia) no Azure Purview
description: Este artigo descreve como registrar uma fonte de dados de Teradata no Azure Purview e configurar um exame.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 8f300f214ed36b7a5257b7276364027b91edc746
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048095"
---
# <a name="register-and-scan-teradata-source-preview"></a>Registrar e examinar fonte de Teradata (versão prévia)

Este artigo descreve como registrar uma fonte de dados de Teradata no Purview e configurar um exame.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

A fonte de Teradata dá suporte ao **Exame completo** para extrair metadados de um banco de dados Teradata e busca a **Linhagem** entre os ativos de dados.

## <a name="prerequisites"></a>Pré-requisitos

1.  Configure o [runtime de integração auto-hospedada](https://www.microsoft.com/download/details.aspx?id=39717) mais recente.
    Para obter mais informações, confira [Criar e configurar um runtime de integração auto-hospedada](../data-factory/create-self-hosted-integration-runtime.md).

2.  Verifique se o [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) está instalado na máquina virtual em que o runtime de integração auto-hospedada está instalado.

3.  Verifique se o \"Pacote Redistribuível do Visual C++ 2012, Atualização 4\" está instalado no computador do runtime de integração auto-hospedada. Se ele ainda não estiver instalado, baixe-o [aqui](https://www.microsoft.com/download/details.aspx?id=30679).

4.  Você precisará baixar manualmente o driver JDBC do Teradata em sua máquina virtual na qual o runtime de integração auto-hospedada está em execução.
    O arquivo JAR executável pode ser baixado no [site](https://downloads.teradata.com/) do Teradata.

    > [!Note]
    > O driver deve estar acessível para todas as contas na VM. Não instale em uma conta de usuário.

5.  As versões do banco de dados Teradata com suporte são 12.x a 16.x. Certifique-se de ter Acesso de leitura à fonte de Teradata que está sendo examinada.

## <a name="setting-up-authentication-for-a-scan"></a>Configurar a autenticação para uma verificação

A única autenticação compatível com uma fonte do Teradata é a **Autenticação básica**.

## <a name="register-a-teradata-source"></a>Registrar uma fonte de Teradata

Para registrar uma nova fonte de Teradata no catálogo de dados, faça o seguinte:

1.  Acesse sua conta do Purview.
2.  Selecione **Fontes** no painel de navegação à esquerda.
3.  Escolha **Registrar**
4.  Em Registrar fontes, selecione **Teradata**. Selecione **Continuar**

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="registrar opções do Teradata" border="true":::

Na tela **Registrar fontes (Teradata)** , faça o seguinte:

1.  Insira um **Nome** com a qual a fonte de dados será listada no Catálogo.

2.  Insira o nome do **Host** para se conectar a uma fonte de Teradata. Ele também pode ser um endereço IP ou uma cadeia de conexão totalmente qualificada para o servidor.

3.  Selecionar ou criar uma coleção (opcional)

4.  Conclua a etapa para registrar a fonte de dados.

    :::image type="content" source="media/register-scan-teradata-source/register-sources-2.png" alt-text="registrar Teradata" border="true":::

## <a name="creating-and-running-a-scan"></a>Criando e executando uma verificação

Para criar e executar uma nova verificação, faça o seguinte:

1.  No Centro de Gerenciamento, clique em **Runtimes de integração**. Verifique se um runtime de integração auto-hospedada está configurado. Caso não esteja configurado, use as etapas mencionadas [aqui](./manage-integration-runtimes.md) para configurar um runtime de integração auto-hospedada

2.  Navegue até as **Fontes**

3.  Selecione a fonte de Teradata registrada.

4.  Selecione **+ Nova verificação**

5.  Forneça os detalhes abaixo:

    a.  **Nome**: o nome da verificação

    b.  **Conectar-se por meio do runtime de integração**: selecione o runtime de integração auto-hospedada.

    c.  **Credencial**: selecione a credencial para se conectar à fonte de dados. Não se esqueça de:

    -   Selecionar Autenticação básica ao criar uma credencial.
    -   Para se conectar ao servidor de banco de dados, forneça um nome de usuário no campo de entrada Nome de usuário
    -   Armazene a senha do servidor de banco de dados na chave secreta.

        Para entender mais sobre credenciais, confira este link [aqui](./manage-credentials.md)

6.  **Esquema**: liste o subconjunto de esquemas a serem importados expressos como uma lista separada por ponto e vírgula. Por exemplo, schema1; schema2. Todos os esquemas de usuário serão importados se essa lista estiver vazia. Todos os esquemas (por exemplo, SysAdmin) e objetos do sistema são ignorados por padrão. Quando a lista estiver vazia, todos os esquemas disponíveis serão importados.

    Padrões de nome de esquema aceitáveis usando a sintaxe de expressões SQL LIKE incluem o uso de %, por exemplo, A%; %B; %C%; D
    - começar com A ou    
    - terminar com B ou    
    - conter C ou    
    - igual a D

    O uso de NOT e de caracteres especiais não é aceitável

7.  **Local do driver**: especifique o caminho para o local do driver JDBC em sua VM, no qual o runtime de integração auto-hospedada está em execução. Esse deve ser o caminho para o local da pasta JAR válida.

8.  **Memória máxima disponível:** memória máxima (em GB) disponível na VM do cliente a ser usada pelos processos de verificação. Isso depende do tamanho da fonte de Teradata a ser examinada.

    > [!Note] 
    > Como regra geral, forneça 2 GB de memória para cada 1000 tabelas

    :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="configuração do exame" border="true":::

6.  Clique em **Continuar**.

7.  Escolha o **gatilho da verificação**. Você pode configurar uma agenda ou executar o exame uma vez.

8.  Analise a verificação e clique em **Salvar e Executar**.

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