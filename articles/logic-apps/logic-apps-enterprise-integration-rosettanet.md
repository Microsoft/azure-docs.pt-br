---
title: Mensagens de RosettaNet para integração de empresas B2B – aplicativos lógicos do Azure
description: Trocar mensagens RosettaNet aplicativo lógico do Azure com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 88e02f3fbbca8007fdf479bb973f50c42a878d6e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332753"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Trocar mensagens RosettaNet para integração de empresas B2B em aplicativos lógicos do Azure 

[RosettaNet](https://resources.gs1us.org) é um consórcio sem fins lucrativos que estabeleceu processos padrão para o compartilhamento de informações de negócios. Esses padrões são usados para processos da cadeia de suprimentos e são amplamente nos setores de semicondutores, eletrônicos e logística. O consórcio RosettaNet cria e mantém processos de Interface de parceiro (PIPs), que fornece definições comuns de processo de negócios para todas as trocas de mensagens RosettaNet. RosettaNet é baseado em XML e define as diretrizes de mensagem, interfaces para processos de negócios e estruturas de implementação para a comunicação entre empresas.

Na [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md), o conector RosettaNet ajuda você a criar soluções de integração que dão suporte a padrões RosettaNet. O conector é baseado na estrutura de implementação da RosettaNet (RNIF) versão 2.0.01. RNIF é uma estrutura de aplicativo de rede aberta que permite que os parceiros de negócios executar colaborativamente RosettaNet PIPs. Essa estrutura define a estrutura da mensagem, a necessidade de confirmações, codificação de email extensões MIME (Multipurpose Internet) e a assinatura digital.

Especificamente, o conector fornece esses recursos:

* Codificar ou receber mensagens RosettaNet.
* Decodificar ou enviar mensagens RosettaNet.
* Aguarde a resposta e a geração da notificação de falha.

Para esses recursos, o conector dá suporte a todos os pontos que são definidos por RNIF 2.0.01. Comunicação com o parceiro pode ser síncrono ou assíncrono.

## <a name="rosettanet-concepts"></a>Conceitos de RosettaNet

Aqui estão alguns conceitos e termos que são exclusivos para a especificação de RosettaNet e são importantes ao criar com base em RosettaNet integrações:

* **PIP**

  A organização RosettaNet cria e mantém processos de Interface de parceiro (PIPs), que fornece definições comuns de processo de negócios para todas as trocas de mensagens RosettaNet. Cada especificação de PIP fornece um arquivo DTD (definição) do tipo de documento e um documento de diretrizes de mensagem. O arquivo DTD define a estrutura de conteúdo do serviço de mensagens. O documento de diretrizes de mensagem, que é um arquivo HTML legível por humanos, especifica as restrições de nível de elemento. Juntos, esses arquivos fornecem uma definição completa do processo de negócios.

   Os pontos são categorizados por uma função de negócios de alto nível, cluster e uma subfunção ou segmento. Por exemplo, "3A4" é o PIP para ordem de compra, enquanto "3" é a função de gerenciamento de pedidos e "3A" é a subfunção da cotação & entrada de ordem. Para obter mais informações, consulte o [RosettaNet site](https://resources.gs1us.org).

* **Ação**

  Parte do PIP, mensagens de ação são mensagens de negócios que são trocadas entre os parceiros.

* **sinal**

   Parte do PIP, mensagens de sinal são confirmações são enviadas em resposta às mensagens de ação.

* **Ação de única e dupla ação**

  Para um única ação PIP, a única resposta é uma mensagem de confirmação de sinal. Para um ação dupla PIP, o iniciador recebe uma mensagem de resposta e responde com uma confirmação além do fluxo de mensagem de ação única.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso você ainda não tenha uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) para armazenar seu contrato e outros artefatos B2B. Essa conta de integração deve estar associada com sua assinatura do Azure.

* Pelo menos duas [parceiros](../logic-apps/logic-apps-enterprise-integration-partners.md) que são definidos em sua conta de integração e configurados com o qualificador "DUNS" em **identidades comerciais**

* Uma configuração de processo PIP, que é necessário para enviar ou receber mensagens da RosettaNet, em sua conta de integração. A configuração do processo armazena todas as características de configuração de PIP. Você pode fazer referência a essa configuração quando você cria um contrato com o parceiro. Para criar uma configuração de processo do PIP na sua conta de integração, consulte [configuração do processo de adicionar PIP](#add-pip).

* Opcional [certificados](../logic-apps/logic-apps-enterprise-integration-certificates.md) para criptografar, descriptografar ou assinar as mensagens que você carregar para a conta de integração. Certificados são necessários somente se você for usar assinatura ou criptografia.

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>Adicionar configuração do processo PIP

Para adicionar uma configuração de processo PIP para sua conta de integração, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com), encontre e abra sua conta de integração.

1. Sobre o **visão geral** painel, selecione o **RosettaNet PIP** lado a lado.

   ![Escolha o bloco RosettaNet](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. Sob **RosettaNet PIP**, escolha **Add**. Forneça os detalhes PIP.

   ![Adicionar detalhes RosettaNet PIP](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | Propriedade | Obrigatório | DESCRIÇÃO |
   |----------|----------|-------------|
   | **Nome** | Sim | Seu nome PIP |
   | **Código PIP** | Sim | O código de três dígitos PIP. Para obter mais informações, consulte [RosettaNet PIPs](https://docs.microsoft.com/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips). |
   | **Versão do PIP** | Sim | O número de versão do PIP, que está disponível com base em seu código PIP selecionado |
   ||||

   Para obter mais informações sobre essas propriedades PIP, visite o [RosettaNet site](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg).

1. Quando terminar, escolha **Okey**, que cria a configuração de PIP.

1. Para exibir ou editar a configuração do processo, selecione o PIP e escolha **editar como JSON**.

   Todas as configurações provenientes de especificações do PIP de configuração do processo. Os aplicativos lógicos popula a maioria das configurações com os valores padrão que são os mais usados valores para essas propriedades.

   ![Editar configuração de PIP RosettaNet](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. Confirme se as configurações correspondem aos valores na especificação do PIP apropriado e atender às suas necessidades de negócios. Se necessário, atualize os valores em JSON e salvar essas alterações.

## <a name="create-rosettanet-agreement"></a>Criar contrato RosettaNet

1. No [portal do Azure](https://portal.azure.com), localize e abra a conta de integração, caso ainda não esteja aberta.

1. Sobre o **visão geral** painel, selecione o **contratos** lado a lado.

   ![Escolha o que bloco de contratos](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. Em **Contratos**, escolha **Adicionar**. Forneça os detalhes do contrato.

   ![Adicionar detalhes do contrato](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | Propriedade | Obrigatório | DESCRIÇÃO |
   |----------|----------|-------------|
   | **Nome** | Sim | O nome do contrato |
   | **Tipo de contrato** | Sim | Selecione **RosettaNet**. |
   | **Parceiro de host** | Sim | Um contrato precisa dos parceiros host e convidado. O parceiro host representa a organização que está configurando o contrato. |
   | **Host Identity** | Sim | Um identificador para o parceiro host |
   | **Parceiro convidado** | Sim | Um contrato precisa dos parceiros host e convidado. O parceiro convidado representa a organização que está fazendo negócios com o parceiro host. |
   | **Identidade do convidado** | Sim | Um identificador para o parceiro convidado |
   | **Configurações de recebimento** | Varia | Essas propriedades se aplicam a todas as mensagens recebidas pelo parceiro de host |
   | **Configurações de envio** | Varia | Essas propriedades se aplicam a todas as mensagens enviadas pelo parceiro de host |  
   | **Referências de PIP RosettaNet** | Sim | As referências PIP para o contrato. Todas as mensagens de RosettaNet exigem configurações de PIP. |
   ||||

1. Para configurar seu contrato para o recebimento de mensagens de entrada do parceiro convidado, selecione **configurações de recebimento**.

   ![Configurações de recebimento](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. Para habilitar a assinatura ou criptografia de mensagens de entrada, sob **mensagens**, selecione **mensagem deve ser assinada** ou **mensagem deve ser criptografada** , respectivamente.

      | Propriedade | Obrigatório | DESCRIÇÃO |
      |----------|----------|-------------|
      | **Mensagem deve ser assinada** | Não | Assinar as mensagens de entrada com o certificado selecionado. |
      | **Certificate** | Sim, se a assinatura está habilitada | O certificado a ser usado para a assinatura |
      | **Habilitar a criptografia de mensagem** | Não | Criptografe as mensagens de entrada com o certificado selecionado. |
      | **Certificate** | Sim, se a criptografia está habilitada | O certificado a ser usado para criptografia |
      ||||

   1. Em cada seleção, selecione os respectivos [certificado](./logic-apps-enterprise-integration-certificates.md), que você adicionou anteriormente à sua conta de integração, a ser usado para assinatura ou criptografia.

1. Para configurar seu contrato para enviar mensagens ao parceiro convidado, selecione **configurações de envio**.

   ![Configurações de envio](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. Para habilitar a assinatura ou criptografia de mensagens de saída, em **mensagens**, selecione **habilitar assinatura de mensagens** ou **habilitar a criptografia de mensagem** , respectivamente. Em cada seleção, selecione o algoritmo respectivo e [certificado](./logic-apps-enterprise-integration-certificates.md), que você adicionou anteriormente à sua conta de integração, a ser usado para assinatura ou criptografia.

      | Propriedade | Obrigatório | DESCRIÇÃO |
      |----------|----------|-------------|
      | **Habilitar assinatura de mensagens** | Não | Assinar as mensagens de saída com o algoritmo de assinatura selecionado e o certificado. |
      | **Algoritmo de assinatura** | Sim, se a assinatura está habilitada | O algoritmo de assinatura para usar, com base em certificado selecionado |
      | **Certificate** | Sim, se a assinatura está habilitada | O certificado a ser usado para a assinatura |
      | **Habilitar a criptografia de mensagem** | Não | Criptografe com o algoritmo de criptografia selecionado e o certificado de saída. |
      | **Algoritmo de criptografia** | Sim, se a criptografia está habilitada | O algoritmo de criptografia a ser usado, com base em certificado selecionado |
      | **Certificate** | Sim, se a criptografia está habilitada | O certificado a ser usado para criptografia |
      ||||

   1. Sob **pontos de extremidade**, especifique as URLs necessárias para usar para enviar mensagens de ação e confirmações.

      | Propriedade | Obrigatório | DESCRIÇÃO |
      |----------|----------|-------------|
      | **URL da ação** |  Sim | A URL a ser usado para enviar mensagens de ação. A URL é um campo obrigatório para mensagens síncronas e assíncronas. |
      | **URL de confirmação** | Sim | A URL a ser usado para enviar mensagens de confirmação. A URL é um campo obrigatório para mensagens assíncronas. |
      ||||

1. Para configurar seu contrato com as referências de PIP RosettaNet para parceiros, selecione **faz referência a RosettaNet PIP**. Sob **PIP nome**, selecione o nome do seu PIP criado anteriormente.

   ![Referências PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   Sua seleção popula as propriedades restantes, que se baseiam no PIP que você configurou na sua conta de integração. Se necessário, você pode alterar o **PIP função**.

   ![PIP selecionado](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

Depois de concluir essas etapas, você está pronto para enviar ou receber mensagens RosettaNet.

## <a name="rosettanet-templates"></a>Modelos de RosettaNet

Para acelerar o desenvolvimento e recomendável padrões de integração, você pode usar modelos de aplicativo lógico para decodificação e codificação de mensagens RosettaNet. Quando você cria um aplicativo lógico, você pode selecionar na Galeria de modelo no Designer de aplicativo lógico. Você também pode encontrar esses modelos na [repositório do GitHub para aplicativos lógicos do Azure](https://github.com/Azure/logicapps).

![Modelos de RosettaNet](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>Receber ou decodificar mensagens RosettaNet

1. [Criar um aplicativo lógico em branco](quickstart-create-first-logic-app-workflow.md).

1. [Vincular sua conta de integração](logic-apps-enterprise-integration-create-integration-account.md#link-account) ao seu aplicativo lógico.

1. Antes de adicionar uma ação para decodificar a mensagem RosettaNet, você deve adicionar um gatilho para iniciar seu aplicativo lógico, como um gatilho de solicitação.

1. Depois de adicionar o gatilho, escolha **nova etapa**.

   ![Adicionar gatilho de solicitação](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Na caixa de pesquisa, digite "rosettanet" e selecione esta ação: **Decodificar RosettaNet**

   ![Localize e selecione a ação "RosettaNet decodificar"](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. Forneça as informações para as propriedades da ação:

   ![Forneça os detalhes da ação](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | Propriedade | Obrigatório | DESCRIÇÃO |
   |----------|----------|-------------|
   | **Mensagem** | Sim | A mensagem RosettaNet para decodificar  |
   | **Cabeçalhos** | Sim | Os cabeçalhos HTTP que fornecem os valores para a versão, o que é a versão RNIF, e o tipo de resposta, que indica o tipo de comunicação entre os parceiros e pode ser síncrono ou assíncrono |
   | **Função** | Sim | A função do parceiro de host em que o PIP |
   ||||

   Da ação de decodificação RosettaNet, a saída, juntamente com outras propriedades, inclui **sinal saída**, que você pode optar por codificar e retornar para o parceiro ou executar qualquer outra ação no que saída.

## <a name="send-or-encode-rosettanet-messages"></a>Enviar ou codificar mensagens RosettaNet

1. [Criar um aplicativo lógico em branco](quickstart-create-first-logic-app-workflow.md).

1. [Vincular sua conta de integração](logic-apps-enterprise-integration-create-integration-account.md#link-account) ao seu aplicativo lógico.

1. Antes de adicionar uma ação para codificar a mensagem RosettaNet, você deve adicionar um gatilho para iniciar seu aplicativo lógico, como um gatilho de solicitação.

1. Depois de adicionar o gatilho, escolha **nova etapa**.

   ![Adicionar gatilho de solicitação](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Na caixa de pesquisa, digite "rosettanet" e selecione esta ação: **Codificar RosettaNet**

   ![Localize e selecione a ação "RosettaNet codificar"](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. Forneça as informações para as propriedades da ação:

   ![Forneça os detalhes da ação](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | Propriedade | Obrigatório | DESCRIÇÃO |
   |----------|----------|-------------|
   | **Mensagem** | Sim | A mensagem RosettaNet para codificar  |
   | **Parceiro de host** | Sim | O nome do parceiro de host |
   | **Parceiro convidado** | Sim | O nome do parceiro convidado |
   | **Código PIP** | Sim | O código PIP |
   | **Versão do PIP** | Sim | A versão do PIP |  
   | **Identidade da instância PIP** | Sim | O identificador exclusivo para essa mensagem PIP |  
   | **Tipo de mensagem** | Sim | O tipo da mensagem para codificar |  
   | **Função** | Sim | A função de parceiro do host |
   ||||

   A mensagem codificada agora está pronta para enviar ao parceiro.

1. Para enviar a mensagem codificada, este exemplo usa o **HTTP** ação, que é renomeado "HTTP - codificado de enviar mensagem ao parceiro".

   ![Ação de HTTP para enviar mensagem RosettaNet](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   Acordo com os padrões RosettaNet, transações de negócios são consideradas concluídas somente quando todas as etapas definidas pelo PIP forem concluídas.

1. Depois que o host envia a mensagem codificada ao parceiro, o host espera o sinal e a confirmação. Para realizar essa tarefa, adicione a **RosettaNet aguardar resposta** ação.

   ![Adicionar ação de "RosettaNet aguardar resposta"](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   A duração a ser usado para em espera e o número de repetições são com base na configuração da PIP na sua conta de integração. Se a resposta não for recebida, essa ação gera uma notificação de falha. Para lidar com repetições, sempre coloque os **Encode** e **aguardar resposta** ações em um **até** loop.

   ![Até que o loop com ações RosettaNet](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>Próximas etapas

* Saiba como validar, transformar e outras operação de mensagem com o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
