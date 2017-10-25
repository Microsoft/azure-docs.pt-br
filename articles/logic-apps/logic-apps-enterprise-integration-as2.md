---
title: "Mensagens AS2 para integração de empresas B2B – Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Troca de mensagens AS2 para integração corporativa B2B com Aplicativos Lógicos do Azure"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: c9b7e1a9-4791-474c-855f-988bd7bf4b7f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: LADocs; mandia
ms.openlocfilehash: 91b2f16611b88aa4b9395ca301d88042065ad9dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="exchange-as2-messages-for-enterprise-integration-with-logic-apps"></a>Troca de mensagens AS2 para integração empresarial com aplicativos lógicos

Para poder trocar mensagens AS2 para Aplicativos Lógicos do Azure, você deve criar um contrato AS2 e armazená-lo em sua conta de integração. Aqui estão as etapas para criar um contrato AS2.

## <a name="before-you-start"></a>Antes de começar

Veja os itens necessários:

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md) que já esteja definida e associada à sua assinatura do Azure
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidos em sua conta de integração e configurados com o qualificador AS2 em **Identidades Comerciais**

> [!NOTE]
> Ao criar um contrato, o conteúdo do arquivo do contrato deve corresponder ao tipo de contrato.    

Depois de [criar uma conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md) e [adicionar os parceiros](logic-apps-enterprise-integration-partners.md), você pode criar um contrato AS2 seguindo estas etapas.

## <a name="create-an-as2-agreement"></a>Criar um contrato AS2

1.  Entre no [portal do Azure](http://portal.azure.com "portal do Azure").  

2.  No menu à esquerda, selecione **Mais serviços**. Na caixa de pesquisa, digite **integração** como filtro. Na lista de resultados, selecione **Contas de Integração**.

    > [!TIP]
    > Se você não encontrar a opção **Mais serviços**, talvez seja necessário expandir o menu primeiro. Na parte superior do menu recolhido, selecione **Mostrar menu**.

    ![Mais serviços, filtre "integração" e selecione "Contas de Integração"](./media/logic-apps-enterprise-integration-agreements/overview-1.png)

3. Quando a folha **Contas de integração** abrir, selecione a conta de integração onde você deseja criar o contrato.
Caso não encontre nenhuma conta de integração, [crie uma primeiro](../logic-apps/logic-apps-enterprise-integration-accounts.md "O que é uma conta de integração?").  

    ![Selecione a conta de integração em que o contrato deve ser criado](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Escolha o bloco **Contratos**. Se não tiver um bloco de Contratos, primeiro adicione o bloco.

    ![Escolha o bloco de "Contratos"](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

5. Quando a folha "Contratos" abrir, selecione **Adicionar**.

    ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

6. Em **Adicionar**, insira um **Nome** para o seu contrato. Para **Tipo de contrato**, selecione **AS2**. Selecione o **Parceiro Host**, a **Identidade do Host**, o **Parceiro Convidado** e a **Identidade do Convidado** para o contrato.

    ![Fornecer detalhes de contrato](./media/logic-apps-enterprise-integration-agreements/agreement-3.png)  

    | Propriedade | Descrição |
    | --- | --- |
    | Nome |Nome do contrato |
    | Tipo de contrato | Deve ser AS2 |
    | Parceiro de Host |Um contrato precisa dos parceiros host e convidado. O parceiro host representa a organização que está configurando o contrato. |
    | Identidade do Host |Um identificador para o parceiro host |
    | Parceiro Convidado |Um contrato precisa dos parceiros host e convidado. O parceiro convidado representa a organização que está fazendo negócios com o parceiro host. |
    | Identidade do Convidado |Um identificador para o parceiro convidado |
    | Configurações de Recebimento |Essas propriedades se aplicam a todas as mensagens recebidas por um contrato. |
    | Configurações de Envio |Essas propriedades se aplicam a todas as mensagens enviadas por um contrato. |

## <a name="configure-how-your-agreement-handles-received-messages"></a>Configurar como seu contrato lida com mensagens recebidas

Depois de configurar as propriedades do contrato, você pode configurar como este contrato identifica e manipula mensagens de entrada recebidas do seu parceiro por meio deste contrato.

1.  Em **Adicionar**, selecione **Configurações de Recebimento**.
Configure essas propriedades com base em seu contrato com o parceiro que troca mensagens com você. Para obter descrições de propriedades, confira a tabela nesta seção.

    ![Configure "Configurações de recebimento"](./media/logic-apps-enterprise-integration-agreements/agreement-4.png)

2. Você também pode substituir as propriedades de mensagens de entrada selecionando **Substituir as propriedades de mensagem**.

3. Para exigir que todas as mensagens recebidas sejam assinadas, selecione **A mensagem deve ser assinada**. Na lista **Certificado**, selecione um [certificado público de parceiro convidado](../logic-apps/logic-apps-enterprise-integration-certificates.md) existente para validar a assinatura nas mensagens. Ou crie o certificado, se você não tiver um.

4.  Para exigir que todas as mensagens de entrada sejam criptografadas, selecione **Mensagem deve ser criptografada**. Na lista **Certificado**, selecione um [certificado privado de parceiro host](../logic-apps/logic-apps-enterprise-integration-certificates.md) existente para descriptografar as mensagens de entrada. Ou crie o certificado, se você não tiver um.

5. Para exigir que as mensagens sejam compactadas, selecione **A mensagem deve ser compactada**.

6. Para enviar uma notificação de disposição de mensagens (MDN) síncrona das mensagens recebidas, selecione **Enviar MDN**.

7. Para enviar MDNs assinadas para mensagens recebidas, selecione **Enviar MDN assinada**.

8. Para enviar MDNs assíncronas para mensagens recebidas, selecione **Enviar MDN assíncrona**.

9. Após terminar, salve suas configurações, escolhendo **OK**.

Agora o contrato está pronto para lidar com mensagens de entrada de acordo com as configurações selecionadas.

| Propriedade | Descrição |
| --- | --- |
| Substituir as propriedades de mensagem |Indica que as propriedades nas mensagens recebidas podem ser substituídas. |
| Mensagem deve ser assinada |Exige que as mensagens sejam assinadas digitalmente. Configura o certificado público do parceiro convidado para verificação de assinatura.  |
| Mensagem deve ser criptografada |Exige que as mensagens sejam criptografadas. Mensagens não criptografadas são rejeitadas. Configura o certificado particular do parceiro do host para descriptografar as mensagens.  |
| Mensagem deve ser compactada |Exige que as mensagens sejam compactadas. Mensagens não compactadas são rejeitadas. |
| Texto MDN |A notificação de disposição de mensagem (MDN) padrão a ser enviada para o remetente da mensagem. |
| Enviar MDN |Exige que MDNs sejam enviadas. |
| Enviar MDN assinada |Exige que MDNs sejam assinadas. |
| Algoritmo de Controle de Integridade de Credenciais |Selecione o algoritmo a ser usado para assinar mensagens. |
| Enviar MDN assíncrona | Exige que as mensagens sejam enviadas de forma assíncrona. |
| URL | Especifique a URL para a qual enviar os MDNs. |

## <a name="configure-how-your-agreement-sends-messages"></a>Configurar como seu contrato envia mensagens

Você pode configurar como este contrato identifica e trata mensagens de saída enviadas a seus parceiros por meio deste contrato.

1.  Em **Adicionar**, selecione **Configurações de Envio**.
Configure essas propriedades com base em seu contrato com o parceiro que troca mensagens com você. Para obter descrições de propriedades, confira a tabela nesta seção.

    ![Definição das propriedades de "Configurações de Envio"](./media/logic-apps-enterprise-integration-agreements/agreement-51.png)

2. Para enviar mensagens assinadas a seu parceiro, selecione **Habilitar a assinatura de mensagens**. Para assinar as mensagens, na lista **Algoritmo MIC**, selecione o *algoritmo MIC de certificado privado de parceiro host*. E, na lista de **Certificados**, selecione um [certificado particular de parceiro host](../logic-apps/logic-apps-enterprise-integration-certificates.md) existente.

3. Para enviar mensagens criptografadas ao parceiro, selecione **Habilitar a criptografia de mensagens**. Para criptografar as mensagens, na lista **Algoritmo de Criptografia**, selecione o *algoritmo de certificado público de parceiro convidado*.
E, na lista de **Certificados**, selecione um [certificado público de parceiro convidado](../logic-apps/logic-apps-enterprise-integration-certificates.md) existente.

4. Para compactar a mensagem, selecione **Habilitar a compactação de mensagem**.

5. Para desdobrar o cabeçalho de tipo de conteúdo HTTP em uma única linha, selecione **Desdobrar cabeçalhos HTTP**.

6. Para receber MDNs síncronas para mensagens enviadas, selecione **Solicitar MDN**.

7. Para receber MDNs assinadas para mensagens enviadas, selecione **Solicitar MDN assinada**.

8. Para receber MDNs assíncronas para mensagens enviadas, selecione **Solicitar MDN assíncrona**. Se selecionar essa opção, insira a URL para onde enviar as MDNs.

9. Para exigir o não repúdio de recebimento, selecione **Habilitar NRR**.  

10. Para especificar o formato de algoritmo para usar no MIC ou na assinatura dos cabeçalhos de saída da mensagem AS2 ou MDN, selecione **Formato de Algoritmo SHA2**.  

11. Após terminar, salve suas configurações, escolhendo **OK**.

Agora o contrato está pronto para lidar com mensagens de saída de acordo com as configurações selecionadas.

| Propriedade | Descrição |
| --- | --- |
| Habilitar a assinatura de mensagens |Exige que todas as mensagens enviadas do contrato sejam assinadas. |
| Algoritmo de Controle de Integridade de Credenciais |O algoritmo a ser usado para assinar mensagens. Configura o Algoritmo MIC do certificado particular do parceiro do host para assinar as mensagens. |
| Certificado |Selecione o certificado a ser usado para assinar mensagens. Configura o certificado particular do parceiro do host para assinar as mensagens. |
| Habilitar a criptografia de mensagem |Exige a criptografia de todas as mensagens enviadas deste contrato. Configura o algoritmo do certificado público do parceiro convidado para criptografar as mensagens. |
| Algoritmo de Criptografia |O algoritmo de criptografia a ser usado na criptografia de mensagens. Configura o certificado público do parceiro convidado para criptografar as mensagens. |
| Certificado |O certificado a ser usado para criptografar as mensagens. Configura o certificado privado do parceiro convidado para criptografar as mensagens. |
| Habilitar a compactação de mensagem |Exige a compressão de todas as mensagens enviadas deste contrato. |
| Desdobrar cabeçalhos HTTP |Posiciona o cabeçalho de tipo de conteúdo HTTP em uma única linha. |
| Solicitar MDN |Exige uma MDN de todas as mensagens enviadas deste contrato. |
| Solicitar MDN assinada |Exige que todas as MDNs enviadas para este contrato sejam assinadas. |
| Solicitar MDN assíncrona |Exige que as MDNs assíncronas sejam enviadas para este contrato. |
| URL |Especifique a URL para a qual enviar os MDNs. |
| Habilitar NRR |Requer o não-repúdio de recebimento (NRR), um atributo de comunicação que fornece evidência de que os dados foram recebidos conforme endereçados. |
| Formato de algoritmo SHA2 |Selecione o formato de algoritmo para usar no MIC ou na assinatura dos cabeçalhos de saída da mensagem AS2 ou MDN |

## <a name="find-your-created-agreement"></a>Como localizar seu contrato criado

1.  Depois que você terminar de definir todas as suas propriedades de contrato, na folha **Adicionar**, escolha **OK** para terminar de criar o contrato e retornar para a folha da conta de integração.

    Agora seu contrato recém-adicionado é exibido na lista **Contratos**.

2.  Você também pode visualizar seus contratos na visão geral de conta de integração. Na folha de conta de integração, escolha **Visão geral** e selecione o bloco **Contratos**. 

    ![Escolha o bloco de "Contratos" para exibir todos os contratos](./media/logic-apps-enterprise-integration-agreements/agreement-6.png)

## <a name="view-the-swagger"></a>Exibir o Swagger
Consulte os [detalhes do Swagger](/connectors/as2/). 

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")  
