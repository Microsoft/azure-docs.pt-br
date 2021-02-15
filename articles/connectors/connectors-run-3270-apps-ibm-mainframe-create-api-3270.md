---
title: Conecte-se a aplicativos 3270 em mainframes IBM
description: Integre e automatize aplicativos baseados em tela de 3270 com o Azure usando o aplicativo lógico do Azure e o conector IBM 3270
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: estfan, valthom
ms.topic: article
ms.date: 02/03/2021
tags: connectors
ms.openlocfilehash: 7873076618125241d9bdf4f225c194572553d060
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573425"
---
# <a name="integrate-3270-screen-driven-apps-on-ibm-mainframes-with-azure-by-using-azure-logic-apps-and-ibm-3270-connector"></a>Integre aplicativos baseados na tela 3270 em mainframes IBM com o Azure usando o aplicativo lógico do Azure e o conector IBM 3270

Com os Aplicativos Lógicos do Azure e o conector IBM 3270, é possível acessar e executar aplicativos de mainframe IBM que você geralmente direciona navegando pelas telas de emulador 3270. Dessa forma, você pode integrar seus aplicativos de mainframe IBM com o Azure, a Microsoft e outros aplicativos, serviços e sistemas criando fluxos de trabalho automatizados com os Aplicativos Lógicos do Azure. O conector se comunica com mainframes IBM usando o protocolo TN3270 e está disponível em todas as regiões de Aplicativos Lógicos do Azure, exceto para Azure Governamental e Azure China 21Vianet. Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

Este artigo descreve esses aspectos para usar o conector 3270: 

* Por que usar o conector IBM 3270 no aplicativo lógico do Azure e como o conector executa aplicativos baseados em tela de 3270

* Os pré-requisitos e a configuração para usar o conector 3270

* As etapas para adicionar 3270 ações de conector ao seu aplicativo lógico

## <a name="why-use-this-connector"></a>Por que usar este conector?

Para acessar aplicativos em mainframes IBM, você normalmente usa um emulador de terminal 3270, geralmente chamado de "tela verde". Esse método é uma maneira testada por tempo, mas tem limitações. Embora Host Integration Server (sua) ajude você a trabalhar diretamente com esses aplicativos, às vezes, separar a tela e a lógica de negócios pode não ser possível. Ou talvez você não tenha mais informações sobre como os aplicativos host funcionam.

Para estender esses cenários, o conector IBM 3270 nos aplicativos lógicos do Azure funciona com a ferramenta de design 3270, que você usa para gravar ou "capturar", as telas de host usadas para uma tarefa específica, definir o fluxo de navegação para essa tarefa por meio de seu aplicativo de mainframe e definir os métodos com parâmetros de entrada e saída para essa tarefa. A ferramenta de design converte essas informações em metadados que o conector 3270 usa ao chamar uma ação que representa a tarefa do seu aplicativo lógico.

Depois de gerar o arquivo de metadados da ferramenta de design, você adiciona esse arquivo a uma conta de integração no Azure. Dessa forma, seu aplicativo lógico pode acessar os metadados do seu aplicativo quando você adiciona uma ação de conector 3270. O conector lê o arquivo de metadados de sua conta de integração, manipula a navegação pelas telas 3270 e apresenta dinamicamente os parâmetros para a ação do conector 3270. Em seguida, você pode fornecer dados para o aplicativo host e o conector retorna os resultados para seu aplicativo lógico. Dessa forma, você pode integrar seus aplicativos herdados ao Azure, à Microsoft e a outros aplicativos, serviços e sistemas aos quais o aplicativo lógico do Azure dá suporte.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Recomendado: um [ISE (ambiente do serviço de integração)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 

  Você pode selecionar esse ambiente como o local para criar e executar seu aplicativo lógico. Um ISE fornece acesso de seu aplicativo lógico a recursos que são protegidos dentro de redes virtuais do Azure.

* O aplicativo lógico a ser usado para automatizar e executar seu aplicativo baseado na tela 3270

  O conector IBM 3270 não tem gatilhos, portanto, use outro gatilho para iniciar seu aplicativo lógico, como o gatilho de **recorrência** . Em seguida, você pode adicionar ações do conector 3270. Para começar, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Se você usar um ISE, selecione esse ISE como o local do seu aplicativo lógico.

* [Baixe e instale a ferramenta de design do 3270](https://aka.ms/3270-design-tool-download).
O único pré-requisito é [Microsoft .NET Framework 4,8](https://aka.ms/net-framework-download).

  Essa ferramenta ajuda a registrar as telas, os caminhos de navegação, os métodos e os parâmetros das tarefas em seu aplicativo que você adiciona e executa como ações de conector 3270. A ferramenta gera um arquivo XML do designer de integração do host (HIDX) que fornece os metadados necessários para o conector usar para conduzir seu aplicativo de mainframe.
  
  Depois de baixar e instalar essa ferramenta, siga estas etapas para se conectar ao seu host:

  1. Abra a ferramenta de design 3270. No menu **sessão** , selecione **sessões de host**.
  
  1. Forneça as informações do servidor host do TN3270.

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), que é o local onde você armazena o arquivo HIDX como um mapa para que seu aplicativo lógico possa acessar os metadados e as definições de método nesse arquivo. 

  Verifique se sua conta de integração está vinculada ao aplicativo lógico que você está usando. Além disso, se você usar um ISE, verifique se o local da sua conta de integração é o mesmo ISE que seu aplicativo lógico usa.

* Acesso ao servidor TN3270 que hospeda seu aplicativo de mainframe

<a name="define-app-metadata"></a>

## <a name="create-metadata-overview"></a>Criar visão geral de metadados

Em um aplicativo baseado em tela 3270, as telas e os campos de dados são exclusivos para seus cenários, portanto, o conector 3270 precisa dessas informações sobre seu aplicativo, que você pode fornecer como metadados. Esses metadados descrevem informações que ajudam seu aplicativo lógico a identificar e reconhecer telas, descreve como navegar entre telas, onde inserir dados e onde esperar resultados. Para especificar e gerar esses metadados, use a ferramenta de design 3270, que orienta você durante esses *modos* específicos ou estágios, conforme descrito mais adiante em mais detalhes:

* **Captura**: nesse modo, você registra as telas necessárias para concluir uma tarefa específica com seu aplicativo de mainframe, por exemplo, obtendo um saldo bancário.

* **Navegação**: nesse modo, você especifica o plano ou o caminho para como navegar pelas telas do aplicativo de mainframe para a tarefa específica.

* **Métodos**: nesse modo, você define o método, por exemplo,, `GetBalance` que descreve o caminho de navegação da tela. Você também seleciona os campos em cada tela que se tornam os parâmetros de entrada e saída do método.

### <a name="unsupported-elements"></a>Elementos sem suporte

A ferramenta de design não dá suporte a esses elementos:

* Mapas parciais de suporte a mapeamento básico IBM (BMS): se você importar um mapa do BMS, a ferramenta de design ignorará as definições parciais de tela.

* Processamento de menu

<a name="capture-screens"></a>

## <a name="capture-screens"></a>Capturar telas

Nesse modo, você marca um item em cada tela 3270 que identifica exclusivamente essa tela. Por exemplo, você pode especificar uma linha de texto ou um conjunto de condições mais complexo, como um texto específico e um campo não vazio. Você pode registrar essas telas em uma conexão dinâmica com o servidor de host ou importar essas informações de um mapa de suporte de mapeamento básico IBM (BMS). A conexão dinâmica usa um emulador TN3270 para se conectar ao host. Cada ação de conector deve mapear para uma única tarefa que começa com a conexão à sua sessão e termina com a desconexão da sua sessão.

1. Se você ainda não fez isso, abra a ferramenta de design 3270. Na barra de ferramentas, selecione **capturar** para entrar no modo de captura.

1. No menu **sessão** , selecione **conectar**.

1. Para iniciar a gravação, no menu de **gravação** , selecione **Iniciar gravação**. (Teclado: CTRL + E)

1. No painel **capturar** , começando na primeira tela em seu aplicativo, percorra seu aplicativo para a tarefa específica que você está gravando.

1. Depois de concluir a tarefa, saia do seu aplicativo como faria normalmente.

1. No menu **sessão** , selecione **Desconectar**.

1. Para interromper a gravação, no menu de **gravação** , selecione **parar gravação**. (Teclado: Ctrl + Shift + E)

   Depois de capturar as telas de uma tarefa, a ferramenta de designer mostra miniaturas que representam essas telas. Algumas observações sobre estas miniaturas:

   * Incluído nas telas capturadas, você tem uma tela denominada "vazio".

     Quando você se conecta pela primeira vez ao [CICS](https://www.ibm.com/it-infrastructure/z/cics), você deve enviar a chave "Clear" antes de inserir o nome da transação que deseja executar. A tela em que você envia a chave "Clear" não tem nenhum *atributo de reconhecimento*, como um título de tela, que você pode adicionar usando o editor de reconhecimento de tela. Para representar essa tela, as miniaturas incluem uma tela chamada "Empty". Posteriormente, você pode usar essa tela para representar a tela em que você insere o nome da transação.

   * Por padrão, o nome de uma tela capturada usa a primeira palavra na tela. Se esse nome já existir, a ferramenta de design acrescentará o nome com um sublinhado e um número, por exemplo, "WBGB" e "WBGB_1".

1. Para fornecer um nome mais significativo para uma tela capturada, siga estas etapas:

   1. No painel de **telas do host** , selecione a tela que você deseja renomear.

   1. No mesmo painel, próximo à parte inferior no mesmo painel, localize a propriedade **nome da tela** .

   1. Altere o nome da tela atual para um nome mais descritivo.

1. Agora, especifique os campos para identificar cada tela.

   Com o fluxo de dados 3270, as telas não têm identificadores padrão, portanto, você precisa selecionar texto exclusivo em cada tela. Para cenários complexos, você pode especificar várias condições, por exemplo, texto exclusivo e um campo com uma condição específica.

Depois de concluir a seleção dos campos de reconhecimento, vá para o modo seguinte.

### <a name="conditions-for-identifying-repeated-screens"></a>Condições para identificar telas repetidas

Para que o conector Navegue e diferencie as telas, geralmente você encontra um texto exclusivo em uma tela que pode ser usado como um identificador entre as telas capturadas. Para telas repetidas, talvez você precise de mais métodos de identificação. Por exemplo, suponha que você tenha duas telas que parecem as mesmas, exceto que uma tela retorna um valor válido, enquanto a outra tela retorna uma mensagem de erro.

Na ferramenta de design, você pode adicionar *atributos de reconhecimento*, por exemplo, um título de tela, como "obter saldo de conta", usando o editor de reconhecimento de tela. Se você tiver um caminho bifurcado e ambas as ramificações retornarem a mesma tela, mas com resultados diferentes, você precisará de outros atributos de reconhecimento. No tempo de execução, o conector usa esses atributos para determinar o Branch atual e a bifurcação. Estas são as condições que você pode usar:

* Valor específico: esse valor corresponde à cadeia de caracteres especificada no local especificado.
* Não é um valor específico: esse valor não corresponde à cadeia de caracteres especificada no local especificado.
* Empty: Este campo está vazio.
* NÃO vazio: Este campo não está vazio.

Para saber mais, consulte o [plano de navegação de exemplo](#example-plan) mais adiante neste tópico.

<a name="define-navigation"></a>

## <a name="define-navigation-plans"></a>Definir planos de navegação

Nesse modo, você define o fluxo ou as etapas para navegar pelas telas de seu aplicativo de mainframe para sua tarefa específica. Por exemplo, às vezes, você pode ter mais de um caminho que seu aplicativo pode assumir onde um caminho produz o resultado correto, enquanto o outro caminho produz um erro. Para cada tela, especifique os pressionamentos de tecla necessários para mover para a próxima tela, como `CICSPROD <enter>` .

> [!TIP]
> Se você estiver automatizando várias tarefas que usam as mesmas telas conectar e desconectar, a ferramenta de design fornecerá tipos especiais de plano de conexão e desconexão. Ao definir esses planos, você pode adicioná-los ao início e ao fim do plano de navegação.

### <a name="guidelines-for-plan-definitions"></a>Diretrizes para definições de plano

* Inclua todas as telas, começando com a conexão e terminando com a desconexão.

* Você pode criar um plano autônomo ou usar os planos conectar e desconectar, o que permite reutilizar uma série de telas comuns a todas as suas transações.

  * A última tela em seu plano de conexão deve ser a mesma tela que a primeira tela em seu plano de navegação.

  * A primeira tela no seu plano de desconexão deve ser a mesma tela que a última tela em seu plano de navegação.

* As telas capturadas podem conter muitas telas repetidas, portanto, selecione e use apenas uma instância de qualquer tela repetida em seu plano. Aqui estão alguns exemplos de telas repetidas:

  * A tela de entrada, por exemplo, a tela **MSG-10**
  * A tela de boas-vindas para CICS
  * A tela "limpar" ou **vazia**

<a name="create-plans"></a>

### <a name="create-plans"></a>Criar planos

1. Na barra de ferramentas da ferramenta de design do 3270, selecione **navegação** para entrar no modo de navegação.

1. Para iniciar seu plano, no painel de **navegação** , selecione **novo plano**.

1. Em **escolher novo nome do plano**, insira um nome para seu plano. Na lista **tipo** , selecione o tipo de plano:

   | Tipo de plano | Descrição |
   |-----------|-------------|
   | **Processo** | Para planos autônomos ou combinados |
   | **Connect** | Para planos do Connect |
   | **Desconectar** | Para planos de desconexão |
   |||

1. No painel de **telas do host** , arraste as miniaturas capturadas para a superfície do plano de navegação no painel de **navegação** .

   Para representar a tela em branco onde você insere o nome da transação, use a tela "vazio".

1. Organize as telas na ordem que descreve a tarefa que você está definindo.

1. Para definir o caminho do fluxo entre telas, incluindo bifurcações e junções, na barra de ferramentas da ferramenta de design, selecione **fluxo**.

1. Escolha a primeira tela no fluxo. Arraste e desenhe uma conexão com a próxima tela no fluxo.

1. Para cada tela, forneça os valores para a propriedade **chave de auxílio** (identificador de atenção) e para a propriedade **texto fixo** , que move o fluxo para a próxima tela.

   Você pode ter apenas a chave de auxílio ou a chave de auxílio e o texto fixo.

Depois de concluir o plano de navegação, você poderá [definir métodos no modo seguinte](#define-method).

<a name="example-plan"></a>

### <a name="example"></a>Exemplo

Neste exemplo, suponha que você execute uma transação CICS chamada "WBGB" que tem estas etapas: 

* Na primeira tela, insira um nome e uma conta.
* Na segunda tela, você obtém o saldo da conta.
* Você sai para a tela "vazia".
* Você sai do CICS para a tela "MSG-10".

Além disso, suponha que você repita essas etapas, mas inseriu dados incorretos para poder capturar a tela que mostra o erro. Estas são as telas capturadas:

* MSG-10
* CICS Welcome
* Vazio
* WBGB_1 (entrada)
* WBGB_2 (erro)
* Empty_1
* MSG-10_1

Embora muitas telas aqui obtenham nomes exclusivos, algumas telas são a mesma tela, por exemplo, "MSG-10" e "Empty". Para uma tela repetida, use apenas uma instância para essa tela em seu plano. Aqui estão exemplos que mostram como um plano autônomo, um plano de conexão, um plano de desconexão e um plano combinado podem parecer:

* Plano autônomo

  ![Plano de navegação autônomo](./media/connectors-create-api-3270/standalone-plan.png)

* Plano de conexão

  ![Plano de conexão](./media/connectors-create-api-3270/connect-plan.png)

* Desconectar plano

  ![Desconectar plano](./media/connectors-create-api-3270/disconnect-plan.png)

* Plano combinado

  ![Plano combinado](./media/connectors-create-api-3270/combined-plan.png)

#### <a name="example-identify-repeated-screens"></a>Exemplo: identificar telas repetidas

Para que o conector Navegue e diferencie as telas, geralmente você encontra um texto exclusivo em uma tela que pode ser usado como um identificador nas telas capturadas. Para telas repetidas, talvez você precise de mais métodos de identificação. O plano de exemplo tem uma bifurcação em que você pode obter telas parecidas. Uma tela retorna um saldo de conta, enquanto a outra tela retorna uma mensagem de erro.

A ferramenta de design permite que você adicione atributos de reconhecimento, por exemplo, um título de tela chamado "obter saldo de conta", usando o editor de reconhecimento de tela. No caso de telas semelhantes, você precisa de outros atributos. No tempo de execução, o conector usa esses atributos para determinar a ramificação e a bifurcação.

* Na ramificação que retorna uma entrada válida, que é a tela com o saldo da conta, você pode adicionar um campo que tenha uma condição "não vazia".

* Na ramificação que retorna com um erro, você pode adicionar um campo que tenha uma condição "vazia".

<a name="define-method"></a>

## <a name="define-methods"></a>Definir métodos

Nesse modo, você define um método que está associado ao seu plano de navegação. Para cada parâmetro de método, você especifica o tipo de dados, como uma cadeia de caracteres, um número inteiro, uma data ou hora, e assim por diante. Quando terminar, você poderá testar seu método no host ao vivo e confirmar que o método funciona conforme o esperado. Em seguida, você gera o arquivo de metadados ou o arquivo XML do designer de integração do host (HIDX), que agora tem as definições de método a serem usadas para criar e executar uma ação para o conector IBM 3270.

1. Na barra de ferramentas da ferramenta de design do 3270, selecione **métodos** para que você insira o modo métodos. 

1. No painel de **navegação** , selecione a tela que contém os campos de entrada desejados.

1. Para adicionar o primeiro parâmetro de entrada para o método, siga estas etapas:

   1. No painel **captura** , na tela do emulador 3270, selecione o campo inteiro, não apenas o texto dentro do campo, que você deseja como a primeira entrada.

      > [!TIP]
      > Para exibir todos os campos e certificar-se de selecionar o campo completo, no menu **Exibir** , selecione **todos os campos**.

   1. Na barra de ferramentas da ferramenta de design, selecione **campo de entrada**. 

   Para adicionar mais parâmetros de entrada, repita as etapas anteriores para cada parâmetro.

1. Para adicionar o primeiro parâmetro de saída para o método, siga estas etapas:

   1. No painel **captura** , na tela do emulador 3270, selecione o campo inteiro, não apenas o texto dentro do campo, que você deseja como a primeira saída.

      > [!TIP]
      > Para exibir todos os campos e certificar-se de selecionar o campo completo, no menu **Exibir** , selecione **todos os campos**.

   1. Na barra de ferramentas da ferramenta de design, selecione **campo de saída**.

   Para adicionar mais parâmetros de saída, repita as etapas anteriores para cada parâmetro.

1. Depois de adicionar todos os parâmetros do método, defina essas propriedades para cada parâmetro:

   | Nome da propriedade | Valores possíveis | 
   |---------------|-----------------|
   | **Tipo de Dados** | Byte, data e hora, Decimal, int, longo, curto, Cadeia de caracteres |
   | **Técnica de preenchimento de campo** | Os parâmetros dão suporte a esses tipos de preenchimento, preenchendo com espaços em branco, se necessário: <p><p>- **Tipo**: insira caracteres sequencialmente no campo. <p>- **Preenchimento**: substitua o conteúdo do campo por caracteres, preenchendo com espaços em branco, se necessário. <p>- **EraseEofType**: Limpe o campo e, em seguida, insira os caracteres sequencialmente no campo. |
   | **Cadeia de formato** | Alguns tipos de dados de parâmetro usam uma cadeia de caracteres de formato, que informa ao conector 3270 como converter o texto da tela em um tipo de dados .NET: <p><p>- **DateTime**: a cadeia de caracteres de formato DateTime segue as [cadeias de formato de data e hora personalizadas do .net](/dotnet/standard/base-types/custom-date-and-time-format-strings). Por exemplo, a data `06/30/2019` usa a cadeia de caracteres de formato `MM/dd/yyyy` . <p>- **Decimal**: a cadeia de caracteres de formato decimal usa a [cláusula de imagem COBOL](https://www.ibm.com/support/knowledgecenter/ssw_ibm_i_73/rzasb/picture.htm). Por exemplo, o número `100.35` usa a cadeia de caracteres de formato `999V99` . |
   |||

## <a name="save-and-view-metadata"></a>Salvar e exibir metadados

Depois de definir seu método, mas antes de testar seu método, salve todas as informações que você definiu até agora em um arquivo RAP (. rap).
Você pode salvar nesse arquivo RAP a qualquer momento durante qualquer modo. A ferramenta de design também inclui um arquivo RAP de exemplo que você pode abrir e examinar navegando até a pasta de instalação da ferramenta de design neste local e abrindo o arquivo "WoodgroveBank. rap":

`..\Program Files\Microsoft Host Integration Server - 3270 Design Tool\SDK\WoodgroveBank.rap`

No entanto, se você tentar salvar as alterações no arquivo RAP de exemplo ou gerar um arquivo HIDX do arquivo RAP de exemplo enquanto o arquivo permanecer na pasta de instalação da ferramenta de design, você poderá receber um erro de "acesso negado". Por padrão, a ferramenta de design é instalada na pasta arquivos de programas sem permissões elevadas. Se você receber um erro, tente uma destas soluções:

* Copie o arquivo de exemplo em um local diferente.
* Execute a ferramenta de design como administrador.
* Torne-se o proprietário da pasta do SDK.

## <a name="test-your-method"></a>Testar seu método

1. Para executar o método no host ao vivo, enquanto ainda estiver no modo de métodos, pressione a tecla F5 ou, na barra de ferramentas da ferramenta de design, selecione **testar**.

   > [!TIP]
   > Você pode alterar os modos a qualquer momento. No menu **arquivo** , selecione **modo** e, em seguida, selecione o modo desejado.

1. Insira os valores dos parâmetros e selecione **OK**.

1. Para continuar na próxima tela, selecione **Avançar**.

1. Quando tiver terminado, selecione **concluído**, que mostra os valores de parâmetro de saída.

<a name="add-metadata-integration-account"></a>

## <a name="generate-and-upload-hidx-file"></a>Gerar e carregar arquivo HIDX

Quando estiver pronto, gere o arquivo HIDX para que você possa carregá-lo em sua conta de integração. A ferramenta de design 3270 cria o arquivo HIDX em uma nova subpasta em que você salvou o arquivo RAP.

1. Na ferramenta de design do 3270, no menu **ferramentas** , selecione **gerar definições**. (Teclado: F6)

1. Vá para a pasta que contém o arquivo RAP e abra a subpasta criada pela ferramenta depois de gerar o arquivo HIDX. Confirme se a ferramenta criou o arquivo HIDX.

1. Entre no [portal do Azure](https://portal.azure.com)e localize sua conta de integração.

1. Adicione o arquivo HIDX como um mapa à sua conta de integração, seguindo [estas etapas semelhantes para adicionar mapas](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md), mas ao selecionar o tipo de mapa, selecione **HIDX**.

Posteriormente neste tópico, ao adicionar uma ação IBM 3270 ao seu aplicativo lógico pela primeira vez, você será solicitado a criar uma conexão entre seu aplicativo lógico e o servidor host fornecendo informações de conexão, como os nomes de sua conta de integração e servidor host. Depois de criar a conexão, você pode selecionar o arquivo HIDX adicionado anteriormente, o método a ser executado e os parâmetros a serem usados.

Ao concluir todas essas etapas, você pode usar a ação que você cria em seu aplicativo lógico para se conectar ao mainframe IBM, telas de unidade para seu aplicativo, inserir dados, retornar resultados e assim por diante. Você também pode continuar adicionando outras ações ao seu aplicativo lógico para integração com outros aplicativos, serviços e sistemas.

<a name="run-action"></a>

## <a name="run-ibm-3270-actions"></a>Executar ações do IBM 3270

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Na última etapa em que você deseja adicionar uma ação, selecione **nova etapa** **>** **Adicionar uma ação**. 

1. Na caixa de pesquisa, selecione **Enterprise**. Na caixa de pesquisa, insira `3270` como o filtro. Na lista ações, selecione a ação chamada **executar um programa de mainframe em uma conexão TN3270**

   ![Selecione a ação 3270](./media/connectors-create-api-3270/select-3270-action.png)

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. Selecione o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Se nenhuma conexão ainda existir, forneça as informações necessárias para sua conexão e selecione **criar**.

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome da conexão** | Sim | <*nome da conexão*> | O nome para a sua conexão |
   | **ID da conta de integração** | Sim | <*integração-nome da conta*> | O nome da sua conta de integração |
   | **URL SAS da conta de integração** | Sim | <*integração-conta-SAS-URL*> | A URL da assinatura de acesso compartilhado (SAS) da sua conta de integração, que pode ser gerada com base nas configurações da sua conta de integração no portal do Azure. <p>1. no menu da sua conta de integração, em **configurações**, selecione **URL de retorno de chamada**. <br>2. no painel à direita, copie o valor da **URL de retorno de chamada gerado** . |
   | **Servidor** | Sim | <*TN3270-nome-do-servidor*> | O nome do servidor para o serviço TN3270 |
   | **Porta** | Não | <*TN3270-servidor-porta*> | A porta usada pelo servidor TN3270. Se for deixado em branco, o conector usará `23` como o valor padrão. |
   | **Tipo de Dispositivo** | Não | <*IBM-terminal-Model*> | O nome do modelo ou o número do terminal IBM a ser emulado. Se for deixado em branco, o conector usará valores padrão. |
   | **Página de Código** | Não | <*código-página-número*> | O número da página de código para o host. Se for deixado em branco, o conector usará `37` como o valor padrão. |
   | **Nome da unidade lógica** | Não | <*nome da unidade lógica*> | O nome da unidade lógica específica a ser solicitada do host |
   | **Habilitar SSL?** | Não | Ativar ou desativar | Ative ou desative a criptografia TLS. |
   | **Validar o certificado SSL do host?** | Não | Ativar ou desativar | Ative ou desative a validação para o certificado do servidor. |
   ||||

   Por exemplo:

   ![Propriedades da conexão](./media/connectors-create-api-3270/connection-properties.png)

1. Forneça as informações necessárias para a ação:

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome do Hidx** | Sim | <*HIDX-nome-do-arquivo*> | Selecione o arquivo 3270 HIDX que você deseja usar. |
   | **Nome do método** | Sim | <*nome do método*> | Selecione o método no arquivo HIDX que você deseja usar. Depois de selecionar um método, a lista **Adicionar novo parâmetro** é exibida para que você possa selecionar os parâmetros a serem usados com esse método. |
   ||||

   Por exemplo:

   **Selecione o arquivo HIDX**

   ![Selecionar arquivo HIDX](./media/connectors-create-api-3270/select-hidx-file.png)

   **Selecione o método**

   ![Selecione o método](./media/connectors-create-api-3270/select-method.png)

   **Selecione os parâmetros**

   ![Selecionar parâmetros](./media/connectors-create-api-3270/add-parameters.png)

1. Quando terminar, salve e execute seu aplicativo lógico.

   Depois que o aplicativo lógico terminar a execução, as etapas da execução serão exibidas. 
   As etapas bem-sucedidas mostram marcas de seleção, enquanto as etapas sem êxito mostram a letra "X".

1. Para examinar as entradas e saídas de cada etapa, expanda essa etapa.

1. Para examinar as saídas, selecione **Ver saídas brutas**.

## <a name="connector-reference"></a>Referência de conector

Para obter mais detalhes técnicos sobre esse conector, como gatilhos, ações e limites, conforme descrito pelo arquivo Swagger do conector, consulte a [página de referência do conector](/connectors/si3270/).

> [!NOTE]
> Para aplicativos lógicos em um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), a versão rotulada do ISE do conector usa os [limites de mensagem do ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) em vez disso.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)

