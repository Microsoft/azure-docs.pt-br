---
title: Conectar-se aos aplicativos 3270 em mainframes da IBM com o Azure - aplicativos lógicos do Azure
description: Integre e automatize 3270 aplicativos baseados em tela com o Azure usando o conector do IBM 3270 e aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: estfan, valthom
ms.topic: article
ms.date: 03/06/2019
tags: connectors
ms.openlocfilehash: 7388dc0c61dad9c31da0c178febcee4c8481bc50
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60538756"
---
# <a name="integrate-3270-screen-driven-apps-on-ibm-mainframes-with-azure-by-using-azure-logic-apps-and-ibm-3270-connector"></a>Integrar aplicativos com base em tela 3270 em mainframes da IBM com o Azure usando o conector do IBM 3270 e aplicativos lógicos do Azure

> [!NOTE]
> Esse conector está em [ *visualização pública*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Com aplicativos lógicos do Azure e o conector do IBM 3270, você pode acessar e executar aplicativos de mainframe IBM que você geralmente a unidade navegando pelas 3270 telas do emulador. Dessa forma, você pode integrar seus aplicativos de mainframe IBM do Azure, Microsoft e outros aplicativos, serviços e sistemas com a criação de fluxos de trabalho automatizados com aplicativos lógicos do Azure. O conector se comunica com mainframes IBM por meio do protocolo TN3270 e está disponível em todas as regiões de aplicativos lógicos do Azure, exceto para o Azure governamental e Azure China 21Vianet. Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

Este artigo descreve esses aspectos para usar o conector 3270: 

* Por que usar o conector do IBM 3270 em aplicativos lógicos do Azure e como o conector é executado em aplicativos com base em tela 3270

* Os pré-requisitos e instalação para usar o conector 3270

* As etapas para adicionar 3270 ações do conector ao seu aplicativo lógico

## <a name="why-use-this-connector"></a>Por que usar esse conector?

Para acessar aplicativos em mainframes da IBM, você normalmente usa um emulador de terminal 3270, geralmente chamado de "tela verde". Esse método é uma maneira comprovada, mas tem limitações. Embora o Host Integration Server (HIS) ajuda que você a trabalhar diretamente com esses aplicativos, às vezes, separar a lógica de negócios e de tela pode não ser possível. Ou, talvez você não tem mais informações sobre como funcionam os aplicativos host.

Para estender esses cenários, o conector do IBM 3270 no aplicativo lógico do Azure funciona com a ferramenta de Design 3270, que você usa para o registro, ou "capturar", as telas de host usadas para uma tarefa específica, definir o fluxo de navegação para essa tarefa por meio de seu aplicativo de mainframe e definir o métodos com parâmetros de entrada e saídos para a tarefa. A ferramenta de design converte essas informações em metadados que o conector 3270 usa ao chamar uma ação que representa a tarefa de seu aplicativo lógico.

Depois de gerar o arquivo de metadados da ferramenta de design, você adiciona esse arquivo para uma conta de integração no Azure. Dessa forma, seu aplicativo lógico pode acessar metadados do seu aplicativo quando você adiciona uma ação do conector 3270. O conector lê o arquivo de metadados em sua conta de integração, manipula a navegação pelas 3270 telas e apresenta dinamicamente os parâmetros para a ação de conector 3270. Em seguida, você pode fornecer dados para o aplicativo host e o conector retorna os resultados para seu aplicativo lógico. Dessa forma, você pode integrar seus aplicativos herdados com o Azure, Microsoft e outros aplicativos, serviços e sistemas que oferece suporte a aplicativos lógicos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Recomendável: Um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 

  Você pode selecionar esse ambiente como o local para criar e executar seu aplicativo lógico. Um ISE fornece acesso de seu aplicativo lógico a recursos que são protegidos dentro de redes virtuais do Azure.

* O aplicativo lógico a usar para automatizar e executar seu aplicativo com base em tela 3270

  O conector do IBM 3270 não possui gatilhos, portanto, use outro gatilho para iniciar seu aplicativo lógico, como o **recorrência** gatilho. Em seguida, você pode adicionar 3270 ações do conector. Para começar, [criar um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Se você usar um ISE, selecione esse ISE como local do seu aplicativo lógico.

* [Baixe e instale a ferramenta de Design 3270](https://aka.ms/3270-design-tool-download).
É o único pré-requisito [Microsoft .NET Framework 4.6.1](https://aka.ms/net-framework-download).

  Essa ferramenta o ajuda a gravar as telas, caminhos de navegação, métodos e parâmetros para as tarefas em seu aplicativo que você adiciona e executar como 3270 ações do conector. A ferramenta gera um arquivo XML de Designer de integração de Host (HIDX) que fornece os metadados necessários para o conector para usar para impulsionar seu aplicativo de mainframe.
  
  Após baixar e instalar essa ferramenta, siga estas etapas para se conectar ao seu host:

  1. Abra a ferramenta de Design 3270. Dos **sessão** menu, selecione **Host sessões**.
  
  1. Forneça seu host TN3270 informações do servidor.

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), que é o local onde você armazena seu arquivo HIDX como um mapa para que seu aplicativo lógico possa acessar as definições de metadados e o método nesse arquivo. 

  Certifique-se de que sua conta de integração está vinculada ao aplicativo lógico que você está usando. Além disso, se você usar um ISE, verifique se o que local da sua conta de integração é o mesmo do ISE do seu aplicativo lógico usa.

* Acesso ao servidor TN3270 que hospeda o aplicativo de mainframe

<a name="define-app-metadata"></a>

## <a name="create-metadata-overview"></a>Criar a visão geral de metadados

Em um aplicativo controlado por tela 3270 as telas e campos de dados são exclusivos para seus cenários, portanto, o conector 3270 precisa dessas informações sobre seu aplicativo, que você pode fornecer como metadados. Esses metadados descrevem as informações que seu aplicativo lógico o ajuda a identificar e reconhecer as telas, descreve como navegar entre telas, onde os dados de entrada e onde esperar resultados. Para especificar e gerar esses metadados, você usar a ferramenta de Design 3270, que orienta você por meio desses específico *modos*, ou estágios, conforme descrito posteriormente em mais detalhes:

* **Capturar**: Nesse modo, registre as telas são necessárias para concluir uma tarefa específica com o aplicativo de mainframe, por exemplo, obtendo um saldo bancário.

* **Navegação**: Nesse modo, você pode especificar o plano ou o caminho para saber como navegar por telas do seu aplicativo de mainframe para a tarefa específica.

* **métodos**: Nesse modo, você define o método, por exemplo, `GetBalance`, que descreve o caminho de navegação de tela. Você também pode escolher os campos em cada tela que tornam-se a entrada do método e parâmetros de saída.

### <a name="unsupported-elements"></a>Elementos sem suporte

A ferramenta de design não oferece suporte a esses elementos:

* Mapas de suporte de mapeamento básica IBM (BMS) parcial: Se você importar um mapa BMS, a ferramenta de design ignora as definições de tela parcial.
* In/Out parâmetros: Você não pode definir parâmetros de entrada/saída.
* Processamento de menu: Não tem suporte durante a visualização
* Processamento de matriz: Não tem suporte durante a visualização

<a name="capture-screens"></a>

## <a name="capture-screens"></a>Captura de telas

Nesse modo, você deve marcar um item em cada tela 3270 que identifica exclusivamente essa tela. Por exemplo, você pode especificar uma linha de texto ou um conjunto mais complexo de condições, como um texto específico e um campo vazio. Você pode registrar essas telas ao longo de uma conexão ao vivo para o servidor host ou importar essas informações de um mapa de suporte de mapeamento básica IBM (BMS). A conexão ao vivo usa um emulador TN3270 para se conectar ao host. Cada ação do conector deve mapear para uma única tarefa que começa com a conexão à sua sessão e termina com a desconexão de sua sessão.

1. Se você ainda não fez isso, abra a ferramenta de Design 3270. Na barra de ferramentas, escolha **capturar** para que você entra no modo de captura.

1. Para iniciar a gravação, pressione a tecla F5, ou a partir de **gravando** menu, selecione **Iniciar gravação**. 

1. Dos **sessão** menu, selecione **Connect**.

1. No **capturar** painel, começando na primeira tela em seu aplicativo, percorra o seu aplicativo para a tarefa específica que você está gravando.

1. Depois de concluir a tarefa, saia do aplicativo como faria normalmente.

1. Dos **sessão** menu, selecione **Disconnect**.

1. Para interromper a gravação, pressione Shift + F5 chaves, ou o **gravando** menu, selecione **Parar gravação**.

   Depois de capturar telas para uma tarefa, a ferramenta de designer mostra as miniaturas que representam essas telas. Algumas observações sobre dessas miniaturas:

   * Incluído com as telas capturadas, você tem uma tela que é chamada de "Vazio".

     Quando você se conecta pela primeira vez a [CICS](https://www.ibm.com/it-infrastructure/z/cics), você deve enviar a chave "Limpar" antes de inserir o nome da transação que você deseja executar. A tela em que você envia a chave "Limpar" não tem qualquer *atributos de reconhecimento*, como um título de tela, que pode ser adicionado usando o editor de reconhecimento de tela. Para representar essa tela, as miniaturas inclui uma tela chamada "Empty". Mais tarde você pode usar esta tela para que representa a tela em que você insira o nome da transação.

   * Por padrão, o nome de uma tela capturada usa a primeira palavra na tela. Se esse nome já existir, a ferramenta de design anexa o nome com um sublinhado e um número, por exemplo, "WBGB" e "WBGB_1".

1. Para dar um nome mais significativo para uma tela capturada, siga estas etapas:

   1. No **telas de Host** painel, selecione a tela que você deseja renomear.

   1. No mesmo painel, na parte inferior no mesmo painel, localize o **nome da tela** propriedade.

   1. Altere o nome da tela atual para um nome mais descritivo.

1. Agora, especifique os campos para identificar cada tela.

   Com o fluxo de dados 3270 direcionado, telas não tem identificadores de padrão, portanto, você precisa selecionar texto exclusivo em cada tela. Para cenários complexos, você pode especificar várias condições, por exemplo, texto exclusivo e um campo com uma condição específica.

Depois de terminar de selecionar os campos de reconhecimento, mova para o modo de Avançar.

### <a name="conditions-for-identifying-repeated-screens"></a>Condições para identificar as telas repetidas

Para navegar e diferenciar entre as telas do conector, você normalmente encontrar texto exclusivo em uma tela que você pode usar como um identificador entre as telas capturadas. Para telas repetidas, talvez seja necessário mais métodos de identificação. Por exemplo, suponha que você tem duas telas que têm a mesma aparência, exceto uma tela retorna um valor válido, enquanto outra tela retorna uma mensagem de erro.

Na ferramenta de design, você pode adicionar *atributos de reconhecimento*, por exemplo, um título da tela como "Obter o saldo de conta," usando o editor de reconhecimento de tela. Se você tiver um caminho bifurcado e ambas as ramificações retornam a mesma tela, mas com resultados diferentes, você precisa de outros atributos de reconhecimento. Em tempo de execução, o conector usa esses atributos para determinar o branch atual e a bifurcação. Aqui estão as condições que você pode usar:

* Valor específico: Esse valor corresponde à cadeia de caracteres especificada no local especificado.
* NÃO é um valor específico: Esse valor não corresponde à cadeia de caracteres especificada no local especificado.
* Vazio: Este campo está vazio.
* NÃO vazio: Este campo não está vazio.

Para obter mais informações, consulte o [plano de navegação de exemplo](#example-plan) mais adiante neste tópico.

<a name="define-navigation"></a>

## <a name="define-navigation-plans"></a>Defina planos de navegação

Nesse modo, você pode definir o fluxo ou etapas para navegar pelas telas do seu aplicativo de mainframe para a tarefa específica. Por exemplo, às vezes, você pode ter mais de um caminho que seu aplicativo pode tomar em que um caminho produz o resultado correto, enquanto o outro caminho produz um erro. Para cada tela, especifique os pressionamentos de teclas necessários para ir para a próxima tela, como `CICSPROD <enter>`.

> [!TIP]
> Se você estiver automatizando várias tarefas que usam o mesmo conectar e desconectar telas, a ferramenta de design fornece tipos especiais de plano de conectar e desconectar. Quando você define esses planos, você pode adicioná-los para o início e fim de seu plano de navegação.

### <a name="guidelines-for-plan-definitions"></a>Diretrizes para as definições de plano

* Inclua todas as telas, começando com a conexão e terminando com desconectando.

* Você pode criar um plano autônomo ou usar os planos de conectar e desconectar, que permitem que você reutilize uma série de telas comuns a todas as suas transações.

  * A última tela em seu plano de conexão deve ser a mesma tela como a primeira tela em seu plano de navegação.

  * A primeira tela em seu plano de desconexão deve ser a mesma tela como a última tela em seu plano de navegação.

* As telas capturadas podem contêm muitas telas repetidas, portanto, selecione e usar apenas uma instância de qualquer tela repetida em seu plano. Aqui estão alguns exemplos de telas repetidas:

  * O sinal na tela, por exemplo, o **MSG-10** tela
  * A tela de boas-vindas para CICS
  * "Limpar" ou **vazio** tela

<a name="create-plans"></a>

### <a name="create-plans"></a>Criar planos de

1. Na barra de ferramentas da ferramenta de Design 3270, escolha **navegação** para que você entra no modo de navegação.

1. Para iniciar seu plano, além de **navegação** painel, escolha **novo plano**.

1. Sob **escolher novo nome do plano**, insira um nome para seu plano. Dos **tipo** , selecione o tipo de plano:

   | Tipo de plano | DESCRIÇÃO |
   |-----------|-------------|
   | **Processo** | Para planos combinados ou autônomo |
   | **Conectar** | Para conectar-se planos |
   | **Desconectar** | Para planos de desconexão |
   |||

1. Dos **telas de Host** painel, arraste as miniaturas capturadas ao plano de navegação à tona na **navegação** painel.

   Para representar a tela em branco em que você insira o nome da transação, use a tela "Empty".

1. Organize as telas na ordem em que descreva a tarefa que você está definindo.

1. Para definir o caminho de fluxo entre telas, incluindo bifurcações e junções, na barra de ferramentas da ferramenta de design, escolha **fluxo**.

1. Escolha a primeira tela no fluxo. Arrastar e desenhar uma conexão para a próxima tela no fluxo.

1. Para cada tela, forneça os valores para o **chave de AUXÍLIO** propriedade (identificador de atenção) e para o **texto fixo** propriedade, que move o fluxo para a próxima tela.

   Você pode ter apenas a chave de Ajuda, ou a chave de Ajuda tanto o texto fixo.

Depois de concluir seu plano de navegação, você pode [definir métodos no modo de Avançar](#define-method).

<a name="example-plan"></a>

### <a name="example"></a>Exemplo

Neste exemplo, suponha que você executar uma transação do CICS denominada "WBGB" que tem as seguintes etapas: 

* Na primeira tela, você pode inserir um nome e uma conta.
* Na segunda tela, você obtém o saldo da conta.
* Saia na tela "Empty".
* Você se desconecte do CICS na tela "MSG-10".

Suponha também que você repetir essas etapas, mas insira dados incorretos, portanto, você pode capturar a tela que mostra o erro. Aqui estão as telas que você capture:

* MSG-10
* Bem-vindo do CICS
* Vazio
* WBGB_1 (input)
* WBGB_2 (erro)
* Empty_1
* MSG-10_1

Embora muitas telas aqui obtém nomes exclusivos, algumas telas são a mesma tela, por exemplo, "MSG-10" e "Empty". Para uma tela repetida, use apenas uma instância para essa tela em seu plano. Aqui estão exemplos que mostram como um plano autônomo, plano conectar, desconectar plano e um plano combinado podem parecer:

* Plano autônomo

  ![Plano de navegação autônomo](./media/connectors-create-api-3270/standalone-plan.png)

* Conectar-se o plano

  ![Conectar-se o plano](./media/connectors-create-api-3270/connect-plan.png)

* Desconecte o plano

  ![Desconecte o plano](./media/connectors-create-api-3270/disconnect-plan.png)

* Plano combinado

  ![Plano combinado](./media/connectors-create-api-3270/combined-plan.png)

#### <a name="example-identify-repeated-screens"></a>Exemplo: Identificar telas repetidas

Para navegar e diferenciar as telas do conector, você normalmente encontrar texto exclusivo em uma tela que você pode usar como um identificador entre as telas capturadas. Para telas repetidas, talvez seja necessário mais métodos de identificação. O plano de exemplo tem uma bifurcação de onde você pode obter telas semelhantes. Uma tela retorna um saldo de conta, enquanto outra tela retorna uma mensagem de erro.

A ferramenta de design permite que você adicione atributos de reconhecimento, por exemplo, um título da tela chamada "Obter o saldo de conta," usando o reconhecimento de tela editor. No caso de com telas semelhantes, você precisa de outros atributos. Em tempo de execução, o conector usa esses atributos para determinar a ramificação e a bifurcação.

* Na ramificação que retorna uma entrada válida, que é a tela com o saldo da conta, você pode adicionar um campo que tem uma condição "não" vazia".

* Na ramificação que retorna um erro, você pode adicionar um campo que tem uma condição de "empty".

<a name="define-method"></a>

## <a name="define-methods"></a>Definir métodos

Nesse modo, você deve definir um método que está associada com seu plano de navegação. Para cada parâmetro de método, você pode especifica o tipo de dados, como cadeia de caracteres, inteiro, data ou hora e assim por diante. Quando você terminar, você pode testar seu método no host ao vivo e confirme se o método funciona conforme o esperado. Você, em seguida, gerar o arquivo de metadados ou o arquivo XML de Designer de integração de Host (HIDX), que agora tem as definições do método a ser usado para criar e executar uma ação para o conector do IBM 3270.

1. Na barra de ferramentas da ferramenta de Design 3270, escolha **métodos** para que você entra no modo de métodos. 

1. No **navegação** painel, selecione a tela que tem os campos de entrada que você deseja.

1. Para adicionar o primeiro parâmetro de entrada para o método, siga estas etapas:

   1. No **capturar** painel na tela do emulador 3270, escolha o campo inteiro, não apenas texto dentro do campo que você deseja que a primeira entrada.

      > [!TIP]
      > Para exibir todos os campos e certifique-se de que você selecione o campo concluída, na **modo de exibição** menu, selecione **todos os campos**.

   1. Na barra de ferramentas da ferramenta de design, escolha **campo de entrada**. 

   Para adicionar mais parâmetros de entrada, repita as etapas anteriores para cada parâmetro.

1. Para adicionar o primeiro parâmetro de saída para o método, siga estas etapas:

   1. No **capturar** painel na tela do emulador 3270, escolha o campo inteiro, não apenas texto dentro do campo que você deseja como a primeira saída.

      > [!TIP]
      > Para exibir todos os campos e certifique-se de que você selecione o campo concluída, na **modo de exibição** menu, selecione **todos os campos**.

   1. Na barra de ferramentas da ferramenta de design, escolha **campo de saída**.

   Para adicionar mais parâmetros de saída, repita as etapas anteriores para cada parâmetro.

1. Depois de adicionar todos os seus parâmetros do método, defina essas propriedades para cada parâmetro:

   | Nome da propriedade | Valores possíveis | 
   |---------------|-----------------|
   | **Tipo de dados** | Byte, tempo de data, Decimal, Int, Long, Short, de cadeia de caracteres |
   | **Técnica de preenchimento do campo** | Parâmetros de suportam a esses tipos de preenchimento, preenchimento com espaços em branco, se necessário: <p><p>- **Tipo**: Insira caracteres sequencialmente o campo. <p>- **Preencher**: Substitua o conteúdo do campo de caracteres, preenchendo com espaços em branco, se necessário. <p>- **EraseEofType**: Limpar o campo e, em seguida, insira caracteres sequencialmente no campo. |
   | **Cadeia de caracteres de formato** | Alguns tipos de dados de parâmetro usam uma cadeia de caracteres de formato, que informa o conector 3270 como converter o texto na tela em um tipo de dados do .NET: <p><p>- **DateTime**: A cadeia de caracteres de formato de data e hora segue o [cadeias de caracteres de formato .NET Data e hora personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Por exemplo, a data `06/30/2019` usa a cadeia de caracteres de formato `MM/dd/yyyy`. <p>- **Decimal**: A cadeia de caracteres de formato decimal usa o [cláusula COBOL imagem](https://www.ibm.com/support/knowledgecenter/SS6SG3_5.2.0/com.ibm.cobol52.ent.doc/PGandLR/ref/rlddepic.html). Por exemplo, o número `100.35` usa a cadeia de caracteres de formato `999V99`. |
   |||

## <a name="save-and-view-metadata"></a>Salvar e exibir metadados

Depois de definir seu método, mas antes de testar seu método, salve todas as informações que você definiu até o momento em um arquivo RAP (.rap).
Você pode salvar esse arquivo RAP a qualquer momento durante qualquer modo. A ferramenta de design também inclui um arquivo RAP de exemplo que você pode abrir e revisar navegando até a pasta de instalação da ferramenta de design neste local e abrir o arquivo "WoodgroveBank.rap":

`..\Program Files\Microsoft Host Integration Server - 3270 Design Tool\SDK\WoodgroveBank.rap`

No entanto, se você tentar salvar alterações para o arquivo de exemplo RAP ou gerar um arquivo HIDX do arquivo RAP exemplo enquanto o arquivo permanece na pasta de instalação da ferramenta de design, você poderá receber um erro de "acesso negado". Por padrão, a ferramenta de design instalada na pasta arquivos de programa sem permissões elevadas. Se você receber um erro, tente uma destas soluções:

* Copie o arquivo de exemplo para um local diferente.
* Execute a ferramenta de design como administrador.
* Torne-se o proprietário para a pasta do SDK.

## <a name="test-your-method"></a>Seu método de teste

1. Para executar o método no host em tempo real, enquanto estiver no modo de métodos, pressione a tecla F5 ou, na barra de ferramentas da ferramenta de design, escolha **executar**.

   > [!TIP]
   > Você pode alterar os modos a qualquer momento. Sobre o **arquivo** menu, selecione **modo**e, em seguida, selecione o modo desejado.

1. Insira os valores dos seus parâmetros e escolha **Okey**.

1. Para continuar para a próxima tela, escolha **próxima**.

1. Quando tiver terminado, escolha **feito**, que mostra os valores de parâmetro de saída.

<a name="add-metadata-integration-account"></a>

## <a name="generate-and-upload-hidx-file"></a>Gerar e carregar o arquivo HIDX

Quando você estiver pronto, gere o arquivo HIDX, portanto, você pode carregar em sua conta de integração. A ferramenta de Design 3270 cria o arquivo HIDX em uma nova subpasta em que você salvou o arquivo RAP.

1. Na barra de ferramentas da ferramenta de Design 3270, escolha **gerar código**.

1. Vá para a pasta que contém seu arquivo RAP e abra a subpasta que a ferramenta criada depois de gerar o arquivo HIDX. Confirme que a ferramenta criou o arquivo HIDX.

1. Entrar para o [portal do Azure](https://portal.azure.com)e encontre sua conta de integração.

1. Adicionar seu arquivo HIDX como um mapa para sua conta de integração [siga estas etapas semelhantes para adicionar mapas](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md), mas quando você seleciona o tipo de mapa, selecione **HIDX**.

Neste tópico, quando você adiciona uma ação IBM 3270 ao seu aplicativo lógico pela primeira vez, você será solicitado a criar uma conexão entre seu aplicativo lógico e o servidor de host, fornecendo informações de conexão, como os nomes para o servidor de host e a conta de integração . Depois de criar a conexão, você pode selecionar o arquivo HIDX adicionado anteriormente, o método a ser executado e os parâmetros a serem usados.

Quando você concluir todas essas etapas, você pode usar a ação que você cria no seu aplicativo lógico para se conectar ao seu mainframe IBM, telas de unidade para seu aplicativo, inserir dados, retornar resultados e assim por diante. Você também pode continuar adicionando outras ações ao seu aplicativo lógico para a integração com outros aplicativos, serviços e sistemas.

<a name="run-action"></a>

## <a name="run-ibm-3270-actions"></a>Executar as ações de 3270 IBM

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Na última etapa em que você deseja adicionar uma ação, escolha **nova etapa**e selecione **adicionar uma ação**. 

1. Na caixa de pesquisa, escolha **Enterprise**. Na caixa de pesquisa, insira "3270" como filtro. Na lista de ações, selecione esta ação: **Executa um programa de mainframe ao longo de uma conexão TN3270**

   ![Selecionar ação 3270](./media/connectors-create-api-3270/select-3270-action.png)

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. 
   Escolha o sinal de adição (**+**) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Se nenhuma conexão existir ainda, forneça as informações necessárias para sua conexão e escolha **criar**.

   | Propriedade | Necessário | Value | DESCRIÇÃO |
   |----------|----------|-------|-------------|
   | **Nome da Conexão** | Sim | <*connection-name*> | O nome para a sua conexão |
   | **ID da conta de integração** | Sim | <*integration-account-name*> | Nome da sua conta de integração |
   | **URL de SAS de conta de integração** | Sim | <*integration-account-SAS-URL*> | URL de assinatura de acesso compartilhado (SAS) da sua conta de integração, que você pode gerar das configurações da sua conta de integração no portal do Azure. <p>1. Em sua integração de conta de menu, em **as configurações**, selecione **URL de retorno de chamada**. <br>2. No painel direito, copie o **URL de retorno de chamada gerada** valor. |
   | **Servidor** | Sim | <*TN3270-server-name*> | O nome do servidor para seu serviço TN3270 |
   | **Porta** | Não  | <*TN3270-server-port*> | A porta usada pelo servidor TN3270. Se deixado em branco, o conector usa `23` como o valor padrão. |
   | **Tipo de dispositivo** | Não  | <*IBM-terminal-model*> | O nome do modelo ou um número para o terminal da IBM para emular. Se deixado em branco, o conector usa valores padrão. |
   | **Página de código** | Não  | <*code-page-number*> | O número de página de código para o host. Se deixado em branco, o conector usa `37` como o valor padrão. |
   | **Nome da unidade lógica** | Não  | <*logical-unit-name*> | O nome da unidade lógica específica para a solicitação do host |
   | **Habilitar SSL?** | Não  | Ativar ou desativar | Ligar ou desligar a criptografia SSL. |
   | **Validar o certificado de ssl do host?** | Não  | Ativar ou desativar | Ativar ou desativar a validação para o certificado do servidor. |
   ||||

   Por exemplo: 

   ![Propriedades da conexão](./media/connectors-create-api-3270/connection-properties.png)

1. Forneça as informações necessárias para a ação:

   | Propriedade | Necessário | Value | DESCRIÇÃO |
   |----------|----------|-------|-------------|
   | **Nome de Hidx** | Sim | <*HIDX-file-name*> | Selecione o arquivo HIDX 3270 que você deseja usar. |
   | **Nome do método** | Sim | <*method-name*> | Selecione o método no arquivo HIDX que você deseja usar. Depois de selecionar um método, o **adicionar novo parâmetro** lista é exibida para que você possa selecionar os parâmetros a serem usados com esse método. |
   ||||

   Por exemplo: 

   **Selecione o arquivo HIDX**

   ![Selecione o arquivo HIDX](./media/connectors-create-api-3270/select-hidx-file.png)

   **Selecione o método**

   ![Selecione o método](./media/connectors-create-api-3270/select-method.png)

   **Selecione os parâmetros**

   ![Selecione parâmetros](./media/connectors-create-api-3270/add-parameters.png)

1. Quando terminar, salve e execute seu aplicativo lógico.

   Depois de sua lógica de aplicativo termina de executar, as etapas da execução seja exibido. 
   Etapas bem-sucedida mostram marcas de seleção, enquanto malsucedidas etapas mostram a letra "X".

1. Para examinar as entradas e saídas para cada etapa, expanda essa etapa.

1. Para examinar as saídas, escolha **ver saídas brutas**.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre o limite, ações e gatilhos, que são descritos por OpenAPI do conector (anteriormente conhecido como Swagger) descrição, examine os [página de referência do conector](/connectors/si3270/).

## <a name="get-support"></a>Obter suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
