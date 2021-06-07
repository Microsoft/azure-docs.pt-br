---
title: Módulo de função ABAP de extração de metadados no SAP R3 – Azure alcance
description: Este artigo descreve as etapas para implantar o módulo de função ABAP no servidor SAP
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 9bd3c315fcc15317a9fa483289fdc326ca6aa47f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102614353"
---
# <a name="deploy-the-metadata-extraction-abap-function-module-for-the-sap-r3-family-of-bridges"></a>Implantar o módulo de função ABAP de extração de metadados para a família de pontes do SAP R3

Este artigo descreve as etapas para implantar o módulo de função ABAP no servidor SAP.

## <a name="overview"></a>Visão geral

A ponte ERP do SAP Business Suite 4 HANA (S/4HANA), ECC e R/3 pode ser usada para extrair metadados do servidor SAP. Isso é feito colocando o módulo de função ABAP no servidor SAP. Esse módulo de função é acessível remotamente pela ponte para consultar e baixar (como um arquivo de texto) os metadados que contêm no servidor SAP.

Quando executado, a ponte então:

1. Importa metadados de um arquivo existente já baixado localmente de uma execução de ponte anterior.

2. Invoca a API do módulo ABAP, aguarda o download e, em seguida, importa os metadados desse arquivo.

Este documento detalha as etapas necessárias para implantar este módulo.

> [!Note]
> As instruções a seguir foram compiladas com base na GUI do SAP v. 7.2

## <a name="deployment-of-the-module"></a>Implantação do módulo

### <a name="create-a-package"></a>Criar um pacote

Esta etapa é opcional e um pacote existente pode ser usado.

1. Faça logon no servidor SAP S/4HANA ou SAP ECC e abra o **navegador de objetos** (transação SE80).

2. Selecione **pacote** de opção na lista e insira um nome para o novo pacote (por exemplo, Z \_ MITI) e pressione a **exibição** do botão.

3. Selecione **Sim** na janela **criar pacote** . Consequentemente, uma janela do Package **Builder: criar pacote** é aberta. Insira o valor no campo **Descrição curta** e selecione o ícone **continuar** .

4. Selecione **as próprias solicitações** na janela **solicitar solicitação do Workbench local** . Selecione a solicitação de **desenvolvimento** .

### <a name="create-a-function-group"></a>Criar um grupo de funções

No navegador de objetos, selecione **grupo de funções** na lista e digite seu nome no campo de entrada abaixo (por exemplo, Z \_ MITI \_ FGROUP). Selecione o ícone **Exibir** .

1. Na janela **criar objeto** , selecione **Sim** para criar um novo grupo de funções.

2. Especifique uma descrição apropriada no campo de **texto curto** e pressione o botão **salvar**.

3. Escolha um pacote que foi preparado na etapa anterior **criar um pacote** e selecione **salvar**.

4. Confirme uma solicitação pressionando o ícone **continuar**.

5. Ative o grupo de funções.

### <a name="create-the-abap-function-module"></a>Criar o módulo de função ABAP

1. Depois que o grupo de funções for criado, selecione-o.

2. Clique com o botão direito do mouse no nome do grupo de funções no navegador do repositório e selecione **criar** e, em seguida, **módulo de função**.

3. No campo **módulo de função** , digite `Z_MITI_DOWNLOAD` . Preencha a entrada de **texto curto** com uma descrição adequada.

Quando o módulo tiver sido criado, especifique as seguintes informações:

1. Navegue até a guia **atributos** .

2. Selecione o **tipo de processamento** como **módulo de função habilitado para remoto**.

   :::image type="content" source="media/abap-functions-deployment-guide/processing-type.png" alt-text="Opção registrar fontes-Remote-Enabled módulo função" border="true":::

3. Navegue até a guia **código-fonte** . Há duas maneiras de implantar o código para a função:

   a. No menu principal, carregue o arquivo de texto [Z \_ MITI \_ Download](https://github.com/Azure/Purview-Samples/tree/master/connectors/sap) File. Para fazer isso, selecione **utilitários**, **mais utilitários**, **carregar/baixar** e, em seguida, **carregar**.

   b. Como alternativa, abra o arquivo, copie seu conteúdo e cole-o na área de **código-fonte** .

4. Navegue até a guia **importar** e crie os seguintes parâmetros:

   a.  \_Tipo de área P DD02L-TABNAME (opcional = true)

   b.  *P \_ \_Cadeia de caracteres de tipo de caminho local* (opcional = true)

   c.  *\_Tipo de linguagem P L001TAB-data padrão \' E\'*

   d.  *O tipo de cada de-ignora o \_ padrão int 0*

   e.  *TIPO de número de linhas portanto \_ int padrão 0*

   > [!Note]
   > Escolha o **valor de passagem** para todos eles

   :::image type="content" source="media/abap-functions-deployment-guide/import.png" alt-text="Opção registrar fontes – parâmetros de importação" border="true":::

5. Navegue até a guia "tabelas" e defina o seguinte:

   `EXPORT_TABLE LIKE TAB512`

   :::image type="content" source="media/abap-functions-deployment-guide/export-table.png" alt-text="Opções do registro de fontes – guia tabelas" border="true":::

6. Navegue até a guia **exceções** e defina a seguinte exceção: `E_EXP_GUI_DOWNLOADFAILED`

   :::image type="content" source="media/abap-functions-deployment-guide/exceptions.png" alt-text="Opções de registrar origens-guia exceções" border="true":::

7. Salve a função (pressione Ctrl + S ou escolha o **módulo de função** e, em seguida, **salve** no menu principal).

8. Clique no ícone **Ativar** na barra de ferramentas (Ctrl + F3) e selecione o botão  **continuar** na janela de diálogo. Se solicitado, você deve selecionar as inclusões geradas a serem ativadas junto com o módulo de função principal.

### <a name="testing-the-function"></a>Testando a função

Quando todas as etapas anteriores forem concluídas, siga as etapas abaixo para testar a função:

1. Abra o \_ módulo de função de download do Z MITI \_ .

2. Escolha **módulo de função**, **teste** e, em seguida, **teste o módulo função** no menu principal (ou pressione F8).

3. Insira um caminho para a pasta no sistema de arquivos local no caminho local P do parâmetro \_ \_ e pressione o ícone **executar** na barra de ferramentas (ou pressione F8).

4. Coloque o nome da área de interesse em campo de \_ área P se um arquivo com metadados precisar ser baixado ou atualizado. Quando a função termina de funcionar, a pasta que foi indicada no \_ parâmetro de \_ caminho local P deve conter vários arquivos com metadados dentro do. Os nomes de arquivos que imitam áreas que podem ser especificadas no \_ campo da área P.

A função terminará sua execução e os metadados serão baixados muito mais rapidamente no caso de iniciá-lo no computador que tem conexão de rede de alta velocidade com o servidor SAP S/4HANA ou ECC.

## <a name="next-steps"></a>Próximas etapas

- [Registrar e verificar a origem do SAP ECC](register-scan-sapecc-source.md)
- [Registrar e verificar a origem do SAP S/4HANA](register-scan-saps4hana-source.md)
