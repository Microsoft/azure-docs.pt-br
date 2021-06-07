---
title: Codificar ou decodificar arquivos simples
description: Codificar ou decodificar arquivos simples para integração corporativa em aplicativos lógicos do Azure usando o Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 05/09/2020
ms.openlocfilehash: aebce8f284ed4bb21d99efffc8dd6d0c51b39533
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87001478"
---
# <a name="encode-and-decode-flat-files-in-azure-logic-apps-by-using-the-enterprise-integration-pack"></a>Codificar ou decodificar arquivos simples nos Aplicativos Lógicos do Azure usando o Enterprise Integration Pack

Antes de enviar conteúdo XML para um parceiro de negócios em um cenário B2B (entre empresas), você pode querer codificar esse conteúdo primeiro. Ao criar um aplicativo lógico, você pode codificar e decodificar arquivos simples usando o conector de **arquivo simples** . Seu aplicativo lógico pode obter esse conteúdo XML de várias fontes, como o gatilho de solicitação, outro aplicativo ou outros [conectores com suporte dos aplicativos lógicos do Azure](../connectors/apis-list.md). Para obter mais informações, consulte [o que são os aplicativos lógicos do Azure](logic-apps-overview.md)?

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O aplicativo lógico no qual você deseja usar o conector de **arquivo simples** e um gatilho que inicia o fluxo de trabalho do aplicativo lógico. O conector de **arquivo simples** fornece apenas ações, não gatilhos. Você pode usar o gatilho ou outra ação para alimentar o conteúdo XML em seu aplicativo lógico para codificação ou decodificação. Se ainda não estiver familiarizado com aplicativos lógicos, veja o [Guia de Início Rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) associada à sua assinatura do Azure e [vinculada ao aplicativo lógico](./logic-apps-enterprise-integration-create-integration-account.md#link-account) no qual você planeja usar o conector de **arquivo simples** . O aplicativo lógico e a conta de integração devem existir no mesmo local ou região do Azure.

* Um [esquema](logic-apps-enterprise-integration-schemas.md) de arquivo simples que você carregou em sua conta de integração para codificar ou decodificar o conteúdo XML

* Pelo menos dois [parceiros comerciais](logic-apps-enterprise-integration-partners.md) que você já definiu em sua conta de integração

## <a name="add-flat-file-encode-action"></a>Adicionar ação de codificação de arquivo simples

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

1. No gatilho ou ação em seu aplicativo lógico, selecione **nova etapa**  >  **Adicionar uma ação**. Este exemplo usa o gatilho Request, que é nomeado **quando uma solicitação HTTP é recebida** e manipula solicitações de entrada de fora do aplicativo lógico.

   > [!TIP]
   > Fornecer um esquema JSON é opcional. Se você tiver um conteúdo de exemplo da solicitação de entrada, selecione **usar conteúdo de exemplo para gerar o esquema**, insira o conteúdo de exemplo e selecione **concluído**. O esquema aparece na caixa **esquema JSON do corpo da solicitação** .

1. Em **escolher uma ação**, digite `flat file` . Na lista ações, selecione esta ação: **codificação de arquivo simples**

   ![Selecione a ação "codificação de arquivo simples"](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-encoding.png)

1. Clique dentro da caixa de **conteúdo** para que a lista de conteúdo dinâmico seja exibida. Na lista, na seção **quando uma solicitação HTTP é recebida** , selecione a propriedade **corpo** , que contém a saída do corpo da solicitação do gatilho e o conteúdo a ser codificado.

   ![Selecione o conteúdo a ser codificado na lista de conteúdo dinâmico](./media/logic-apps-enterprise-integration-flatfile/select-content-to-encode.png)

   > [!TIP]
   > Se você não vir a propriedade **corpo** na lista de conteúdo dinâmico, selecione **Ver mais** ao lado do rótulo de seção **quando uma solicitação HTTP é recebida** .
   > Você também pode inserir diretamente o conteúdo a ser decodificado na caixa **conteúdo** .

1. Na lista **nome do esquema** , selecione o esquema que está em sua conta de integração vinculada a ser usado para codificação, por exemplo:

   ![Selecione o esquema a ser usado para codificação](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-encoding.png)

   > [!NOTE]
   > Se nenhum esquema aparecer na lista, sua conta de integração não conterá nenhum arquivo de esquema a ser usado para codificação. Carregue o esquema que você deseja usar para sua conta de integração.

1. Salve seu aplicativo lógico. Para testar seu conector, faça uma solicitação para o ponto de extremidade HTTPS, que aparece na propriedade **URL Post http** do gatilho de solicitação e inclua o conteúdo XML que você deseja codificar no corpo da solicitação.

Agora, você concluiu a configuração de sua ação de codificação de arquivo simples. Em um aplicativo do mundo real, talvez você queira armazenar os dados codificados em um aplicativo de linha de negócios (LOB), como o Salesforce. Ou você pode enviar os dados codificados para um parceiro comercial. Para enviar a saída da ação de codificação para o Salesforce ou para seu parceiro comercial, use os outros [conectores disponíveis nos aplicativos lógicos do Azure](../connectors/apis-list.md).

## <a name="add-flat-file-decode-action"></a>Adicionar decodificação de arquivo simples

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

1. No gatilho ou ação em seu aplicativo lógico, selecione **nova etapa**  >  **Adicionar uma ação**. Este exemplo usa o gatilho Request, que é nomeado **quando uma solicitação HTTP é recebida** e manipula solicitações de entrada de fora do aplicativo lógico.

   > [!TIP]
   > Fornecer um esquema JSON é opcional. Se você tiver um conteúdo de exemplo da solicitação de entrada, selecione **usar conteúdo de exemplo para gerar o esquema**, insira o conteúdo de exemplo e selecione **concluído**. O esquema aparece na caixa **esquema JSON do corpo da solicitação** .

1. Em **escolher uma ação**, digite `flat file` . Na lista ações, selecione esta ação: **decodificação de arquivo simples**

   ![Selecione a ação "decodificação de arquivo simples"](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-decoding.png)

1. Clique dentro da caixa de **conteúdo** para que a lista de conteúdo dinâmico seja exibida. Na lista, na seção **quando uma solicitação HTTP é recebida** , selecione a propriedade **corpo** , que contém a saída do corpo da solicitação do gatilho e o conteúdo a ser decodificado.

   ![Selecione o conteúdo a ser decodificado da lista de conteúdo dinâmico](./media/logic-apps-enterprise-integration-flatfile/select-content-to-decode.png)

   > [!TIP]
   > Se você não vir a propriedade **corpo** na lista de conteúdo dinâmico, selecione **Ver mais** ao lado do rótulo de seção **quando uma solicitação HTTP é recebida** . Você também pode inserir diretamente o conteúdo a ser decodificado na caixa **conteúdo** .

1. Na lista **nome do esquema** , selecione o esquema que está em sua conta de integração vinculada a ser usado para decodificação, por exemplo:

   ![Selecione o esquema a ser usado para decodificação](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-decoding.png)

   > [!NOTE]
   > Se nenhum esquema aparecer na lista, sua conta de integração não conterá nenhum arquivo de esquema a ser usado para decodificação. Carregue o esquema que você deseja usar para sua conta de integração.

1. Salve seu aplicativo lógico. Para testar seu conector, faça uma solicitação para o ponto de extremidade HTTPS, que aparece na propriedade **URL Post http** do gatilho de solicitação e inclua o conteúdo XML que você deseja decodificar no corpo da solicitação.

Agora, você concluiu a configuração de sua ação de decodificação de arquivo simples. Em um aplicativo do mundo real, talvez você queira armazenar os dados decodificados em um aplicativo de linha de negócios (LOB), como o Salesforce. Ou você pode enviar os dados decodificados para um parceiro comercial. Para enviar a saída da ação de decodificação para o Salesforce ou para seu parceiro comercial, use os outros [conectores disponíveis nos aplicativos lógicos do Azure](../connectors/apis-list.md).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
