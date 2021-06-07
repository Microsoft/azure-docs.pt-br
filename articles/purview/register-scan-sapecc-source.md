---
title: Registrar uma fonte do SAP ECC e configurar verificações (versão prévia) no Azure Purview
description: Este artigo descreve como registrar uma fonte do SAP ECC no Azure Purview e configurar uma verificação.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: af3f54778882df9aaa06297f291c12a0f4b1577c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105046038"
---
# <a name="register-and-scan-sap-ecc-source-preview"></a>Registrar e verificar uma fonte do SAP ECC (versão prévia)

Este artigo descreve como registrar uma fonte do SAP ECC no Purview e configurar uma verificação.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

A fonte do SAP ECC dá suporte à **Verificação completa** para extrair metadados de uma instância do SAP ECC e busca a **Linhagem** entre os ativos de dados.

## <a name="prerequisites"></a>Pré-requisitos

1.  Configure o [runtime de integração auto-hospedada](https://www.microsoft.com/download/details.aspx?id=39717) mais recente.
    Para obter mais informações, confira [Criar e configurar um runtime de integração auto-hospedada](../data-factory/create-self-hosted-integration-runtime.md).

2.  Verifique se o [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) está instalado na máquina virtual em que o runtime de integração auto-hospedada está instalado.

3.  Verifique se o \"Pacote Redistribuível do Visual C++ 2012, Atualização 4\" está instalado no computador do runtime de integração auto-hospedada. Se ele ainda não estiver instalado, baixe-o [aqui](https://www.microsoft.com/download/details.aspx?id=30679).

4.  Baixe o Conector SAP de 64 bits [para Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) do site da SAP e instale-o no computador do runtime de integração auto-hospedada. Durante a instalação, lembre-se de selecionar a opção **Instalar Assemblies no GAC** na janela **Etapas de instalação opcionais**.

    :::image type="content" source="media/register-scan-sapecc-source/requirement.png" alt-text="pré-requisito" border="true":::

5.  O conector lê metadados do SAP usando a API do Conector do Java (JCo) 3.0. Portanto, verifique se o Conector do Java está disponível na máquina virtual em que o runtime de integração auto-hospedada está instalado.
    Verifique se você está usando a distribuição do JCo correta para o seu ambiente. Por exemplo, em um computador Microsoft Windows, verifique se os arquivos sapjco3.jar e sapjco3.dll estão disponíveis.

    > [!Note] 
    > O driver deve estar acessível para todas as contas na VM. Não o instale em uma conta de usuário.

6.  Implante o módulo de função ABAP de extração de metadados no servidor SAP seguindo as etapas mencionadas no [Guia de implantação de funções ABAP](abap-functions-deployment-guide.md). Você precisará de uma conta de desenvolvedor ABAP para criar o módulo de função RFC no servidor SAP. A conta de usuário requer permissões suficientes para se conectar ao servidor SAP e executar os seguintes módulos de função RFC:
    -   STFC_CONNECTION (verificar a conectividade)
    -   RFC_SYSTEM_INFO (verificar informações do sistema)


## <a name="setting-up-authentication-for-a-scan"></a>Configurar a autenticação para uma verificação

A única autenticação compatível com uma fonte do SAP ECC é a **Autenticação básica**.

## <a name="register-sap-ecc-source"></a>Registrar uma fonte do SAP ECC

Para registrar uma nova fonte do SAP ECC no catálogo de dados, faça o seguinte:

1.  Acesse sua conta do Purview.
2.  Selecione **Fontes** no painel de navegação à esquerda.
3.  Escolha **Registrar**
4.  Em Registrar fontes, selecione **SAP ECC**. Selecione **Continuar.**

    :::image type="content" source="media/register-scan-sapecc-source/register-sapecc.png" alt-text="registrar opções do SAPECC" border="true":::

Na tela **Registrar fontes (SAP ECC)** , faça o seguinte:

1.  Insira um **Nome** com o qual a fonte de dados será listada no Catálogo.

2.  Insira o nome do **Servidor de aplicativos** para se conectar à fonte do SAP ECC.
    Também pode ser um endereço IP do host do servidor de aplicativos SAP.

3.  Insira o **Número do sistema** SAP. Trata-se de um inteiro de dois dígitos entre 00 e 99.

4.  Selecionar ou criar uma coleção (opcional)

5.  Conclua a etapa para registrar a fonte de dados.

    :::image type="content" source="media/register-scan-sapecc-source/register-sapecc-2.png" alt-text="registrar SAPECC" border="true":::

## <a name="creating-and-running-a-scan"></a>Criando e executando uma verificação

Para criar e executar uma nova verificação, faça o seguinte:

1.  No Centro de Gerenciamento, clique em Runtimes de integração. Verifique se um runtime de integração auto-hospedada está configurado. Caso não esteja configurado, use as etapas mencionadas [aqui](./manage-integration-runtimes.md) para criar um runtime de integração auto-hospedada.

2.  Navegue até **Fontes**

3.  Selecione a fonte registrada do ECC SAP.

4.  Selecione **+ Nova verificação**

5.  Forneça os detalhes abaixo:

    a.  **Nome**: o nome da verificação

    b.  **Conectar-se por meio do runtime de integração**: selecione o runtime de integração auto-hospedada

    c.  **Credencial**: selecione a credencial para se conectar à fonte de dados. Não se esqueça de:

    -   Selecionar Autenticação básica ao criar uma credencial.
    -   Para se conectar ao servidor SAP, forneça uma ID de usuário no campo de entrada Nome de usuário.
    -   Armazene a senha do usuário usada para se conectar ao servidor SAP na chave secreta.

    d.  **ID do cliente**: insira a ID do cliente SAP. Trata-se de um valor numérico de três dígitos entre 000 e 999.

    e.  **Caminho da biblioteca do JCo**: o caminho do diretório em que as bibliotecas do JCo estão localizadas

    f.  **Memória máxima disponível:** memória máxima (em GB) disponível na VM do cliente a ser usada pelos processos de verificação. Isso depende do tamanho da fonte do SAP ECC a ser verificada.

    :::image type="content" source="media/register-scan-sapecc-source/scan-sapecc.png" alt-text="verificar SAPECC" border="true":::

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