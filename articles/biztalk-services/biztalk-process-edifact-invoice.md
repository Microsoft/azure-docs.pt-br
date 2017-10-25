---
title: "Tutorial: Processar faturas EDIFACT usando os Serviços BizTalk do Azure | Microsoft Docs"
description: "Como criar e configurar o conector do Box ou aplicativo de API e usá-lo em um aplicativo lógico no Serviço de Aplicativo do Azure"
services: biztalk-services
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
ms.assetid: 7e0b93fa-3e2b-4a9c-89ef-abf1d3aa8fa9
ms.service: biztalk-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/31/2016
ms.author: deonhe
ms.openlocfilehash: 4597ee28e4c3b797c0ab050b21a126a95d9e8191
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-process-edifact-invoices-using-azure-biztalk-services"></a>Tutorial: Processar faturas EDIFACT usando os Serviços BizTalk do Azure

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Você pode usar o Portal dos Serviços BizTalk para configurar e implantar contratos X12 e EDIFACT. Neste tutorial, vamos examinar como criar um contrato EDIFACT para trocar faturas entre parceiros comerciais. Este tutorial foi escrito em torno de uma solução comercial de ponta a ponta envolvendo dois parceiros comerciais, Northwind e Contoso, que trocam mensagens EDIFACT.  

## <a name="sample-based-on-this-tutorial"></a>Exemplo baseado neste tutorial
Este tutorial foi criado com base em uma amostra, **Enviando faturas EDIFACT usando os Serviços BizTalk**, que está disponível para download na [Galeria de Códigos do MSDN](http://go.microsoft.com/fwlink/?LinkId=401005). Você pode usar o exemplo e percorrer este tutorial para entender como o exemplo foi criado. Ou você pode usar este tutorial para criar sua própria solução do zero. Este tutorial é destinado a uma segunda abordagem, para que você entenda como a solução foi criada. Além disso, tanto quanto possível, o tutorial é consistente com o exemplo e usa os mesmos nomes para artefatos (por exemplo, esquemas, transformações) usados no exemplo.  

> [!NOTE]
> Como essa solução envolve o envio de uma mensagem de uma ponte EAI para uma ponte EDI, ela reutiliza o [Exemplo de encadeamento de Ponte dos Serviços BizTalk](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104) .  
> 
> 

## <a name="what-does-the-solution-do"></a>O que faz a solução?
Nesta solução, a Northwind recebe faturas EDIFACT da Contoso. As faturas não estão em formato EDI padrão. Portanto, antes de enviar a fatura para a Northwind, ela deve ser transformada em um documento de fatura EDIFACT (também chamado INVOIC). Após o recebimento, a Northwind deve processar a fatura EDIFACT e retornar uma mensagem de controle (também chamada CONTRL) para a Contoso.

![][1]  

Para obter esse cenário de negócios, a Contoso usa os recursos fornecidos pelos Serviços BizTalk do Microsoft Azure.

* A Contoso usa pontes EAI para transformar a fatura original em EDIFACT INVOIC.
* A ponte EAI, em seguida, envia a mensagem a uma ponte de envio EDI implantada como parte de um contrato configurado no Portal dos Serviços BizTalk.
* A ponte de envio EDI processa a EDIFACT INVOIC e a encaminha para a Northwind.
* Depois de receber a fatura, a Northwind retorna uma mensagem CONTRL à ponte de recebimento EDI implantada como parte do contrato.  

> [!NOTE]
> Opcionalmente, essa solução também demonstra como usar o processamento em lotes para enviar faturas em lotes em vez de enviar cada fatura separadamente.  
> 
> 

Para concluir o cenário, usamos as filas do Barramento de Serviço para enviar a fatura da Contoso para a Northwind ou receber a confirmação da Northwind. Essas filas podem ser criadas usando um aplicativo cliente, que está disponível para download e está incluído no pacote de exemplo disponível como parte deste tutorial.  

## <a name="prerequisites"></a>Pré-requisitos
* Você deve ter um namespace do Barramento de Serviço. Para obter instruções sobre como criar um namespace, confira [Como criar ou modificar um namespace do Barramento de Serviço](https://msdn.microsoft.com/library/azure/hh674478.aspx). Vamos supor que você já tenha um namespace de Barramento de serviço provisionado, chamado **edifactbts**.
* Você deve ter uma assinatura dos Serviços BizTalk. Para obter instruções, confira [Criar um Serviço BizTalk usando o portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=302280). Para este tutorial, suponhamos que você tenha uma assinatura dos Serviços BizTalk chamada **contosowabs**.
* Registre sua assinatura dos Serviços BizTalk no Portal dos Serviços BizTalk. Para obter instruções, confira [Registrar uma implantação de Serviço BizTalk no Portal dos Serviços BizTalk](https://msdn.microsoft.com/library/hh689837.aspx)
* Você deve ter o Visual Studio instalado.
* Você deve ter o SDK dos Serviços BizTalk instalado. Você pode baixar o SDK em [http://go.microsoft.com/fwlink/?LinkId=235057](http://go.microsoft.com/fwlink/?LinkId=235057)  

## <a name="step-1-create-the-service-bus-queues"></a>Etapa 1: Criar as filas do Barramento de Serviço
Essa solução usa filas do Barramento de Serviço para trocar mensagens entre parceiros comerciais. A Contoso e a Northwind enviam mensagens às filas de onde as pontes EAI e/ou EDI irão consumi-las. Para essa solução, você precisa de três filas do Barramento de Serviço:

* **northwindreceive** – A Northwind recebe a fatura da Contoso por essa fila.
* **contosoreceive** – A Contoso recebe a confirmação da Northwind por essa fila.
* **suspended** – Todas as mensagens suspensas são roteadas para essa fila. As mensagens são suspensas quando falham durante o processamento.

Você pode criar essas filas do Barramento de Serviço usando um aplicativo cliente incluído no pacote de exemplo.  

1. No local de onde você baixou o exemplo, abra **Tutorial Sending Invoices Using BizTalk Services EDI Bridges.sln**.
2. Pressione **F5** para compilar e iniciar o aplicativo **Tutorial Cliente**.
3. Na tela, insira o namespace, o nome do emissor e a chave do emissor do ACS do Barramento de Serviço.
   
   ![][2]  
4. Uma caixa de mensagem solicitará a criação de três filas no namespace do Barramento de Serviço. Clique em **OK**.
5. Deixe o Tutorial Cliente em execução. Abra, clique em **Barramento de Serviço** > ***seu namespace do Barramento de Serviço*** > **Filas** e verifique se as três filas foram criadas.  

## <a name="step-2-create-and-deploy-trading-partner-agreement"></a>Etapa 2: Criar e implantar o acordo entre parceiros comerciais
Crie um acordo entre os parceiros comerciais Contoso e Northwind. Um acordo entre parceiros comerciais define o contrato comercial entre os dois parceiros comerciais, como o esquema de mensagem a ser usado, qual protocolo de mensagens usar, etc. Um acordo entre parceiros comerciais inclui duas pontes EDI, uma para enviar mensagens a parceiros comerciais (chamada **ponte de envio EDI**) e uma para receber mensagens de parceiros comerciais (chamada **ponte de recebimento EDI**).

No contexto desta solução, a ponte de envio EDI corresponde ao lado do envio do acordo e é usada para enviar a fatura EDIFACT da Contoso para a Northwind. Da mesma forma, a ponte de recebimento EDI corresponde ao lado de recebimento do acordo e é usada para receber as confirmações da Northwind.  

### <a name="create-the-trading-partners"></a>Criar os parceiros comerciais
Para começar, crie parceiros comerciais para a Contoso e para a Northwind.  

1. No Portal dos Serviços BizTalk, na guia **Parceiros** clique em **Adicionar**.
2. Na página Novo parceiro, insira **Contoso** como um nome de parceiro e clique em **Salvar**.
3. Repita as etapas para criar um segundo parceiro, **Northwind**.  

### <a name="create-the-agreement"></a>Criar o acordo
Acordos entre parceiros comerciais são criados entre os perfis comerciais dos parceiros comerciais. Essa solução usa perfis de parceiros padrão que são criados automaticamente quando criamos os parceiros.  

1. No Portal dos Serviços BizTalk, clique em **Contratos** > **Adicionar**.
2. Na página **Configurações Gerais** do novo acordo, especifique os valores conforme mostrado na imagem abaixo e clique em **Continuar**.
   
   ![][3]  
   
   Depois de clicar em **Continuar**, duas guias, **Configurações de Recebimento** e **Configurações de Envio**, se tornarem disponíveis.
3. Criar o contrato de envio entre a Contoso e a Northwind. Este contrato determina como a Contoso envia a fatura EDIFACT para a Northwind.
   
   1. Clique em **Configurações de Envio**.
   2. Mantenha os valores padrão nas guias **URL de entrada**, **Transformar** e **Em lote**.
   3. Na guia **Protocolo**, na seção **Esquemas**, carregue o esquema **EFACT_D93A_INVOIC.xsd**. Esse esquema está disponível com o pacote de exemplo.
      
      ![][4]  
   4. Na guia **Transporte** , especifique os detalhes das filas do Barramento de Serviço. Para o contrato do lado de envio, usamos a fila **northwindreceive** para enviar a fatura EDIFACT à Northwind e a fila **suspended** para rotear todas as mensagens que falham durante o processamento e são suspensas. Você criou essas filas na **Etapa 1: criar as filas do Barramento de Serviço** (neste tópico).
      
      ![][5]  
      
      Em **Configurações de Transporte > Tipo de transporte** e **Configurações de Suspensão de Mensagem > Tipo de transporte**, selecione o Barramento de Serviço do Azure e forneça os valores conforme mostrado na imagem.
4. Criar o contrato de recebimento entre a Contoso e a Northwind. Esse contrato determina como a Contoso recebe a confirmação da Northwind.
   
   1. Clique em **Configurações de Recebimento**.
   2. Mantenha os valores padrão nas guias **Transporte** e **Transformar**.
   3. Na guia **Protocolo**, na seção **Esquemas**, carregue o esquema **EFACT_4.1_CONTRL.xsd**. Esse esquema está disponível com o pacote de exemplo.
   4. Na guia **Roteamento** , crie um filtro para garantir que somente as confirmações da Northwind são roteadas para a Contoso. Em **Configurações de Roteamento**, clique em **Adicionar** para criar o filtro de roteamento.
      
      ![][6]  
      
      1. Forneça os valores de **Nome da Regra**, **Regra da rota**, e **Destino da rota** conforme mostrado na imagem.
      2. Clique em **Salvar**.
   5. Novamente na guia **Roteamento** , especifique para onde as confirmações suspensas (confirmações que apresentaram falha durante o processamento) serão roteadas. Defina o tipo de transporte para o Barramento de Serviço do Azure, faça o roteamento do tipo de destino para **Fila**, do tipo de autenticação para **Assinatura de Acesso Compartilhado** (SAS), forneça a cadeia de conexão SAS para o namespace do Barramento de Serviço e insira o nome da fila como **suspended**.
5. Por fim, clique em **Implantar** para implantar o contrato. Observe os pontos de extremidade onde os contratos de envio e de recebimento foram implantados.
   
   * Na guia **Configurações de Envio**, em **URL de entrada**, observe o ponto de extremidade. Para enviar uma mensagem da Contoso para a Northwind usando a ponte de envio EDI, você deve enviar uma mensagem para esse ponto de extremidade.
   * Na guia **Configurações de Recebimento**, em **Transporte**, observe o ponto de extremidade. Para enviar uma mensagem da Northwind para a Contoso usando a ponte de recebimento EDI, você deve enviar uma mensagem para esse ponto de extremidade.  

## <a name="step-3-create-and-deploy-the-biztalk-services-project"></a>Etapa 3: Criar e implantar o projeto dos Serviços BizTalk
Na etapa anterior, você implantou os contratos de envio e recebimento EDI para processar confirmações e faturas EDIFACT. Esses contratos podem processar apenas mensagens que estão em conformidade com o esquema de mensagem EDIFACT padrão. No entanto, de acordo com o cenário para essa solução, a Contoso envia uma fatura para a Northwind em um esquema de propriedade interna. Portanto, antes da mensagem ser enviada para a ponte de envio EDI, ela deve ser transformada do esquema interno para o esquema de fatura EDIFACT padrão. O projeto EAI dos Serviços BizTalk faz isso.

O projeto dos Serviços BizTalk, **InvoiceProcessingBridge**, que transforma a mensagem também está incluído no exemplo que você baixou. O projeto inclui os seguintes artefatos:

* **INHOUSEINVOICE. XSD** – Esquema da fatura interna que é enviada para a Northwind.
* **EFACT_D93A_INVOIC. XSD** – Esquema da fatura EDIFACT padrão.
* **EFACT_4.1_CONTRL. XSD** – Esquema da confirmação EDIFACT que Northwind envia para a Contoso.
* **INHOUSEINVOICE_to_D93AINVOIC. TRFM** – A transformação que mapeia o esquema de fatura interna para o esquema de fatura EDIFACT padrão.  

### <a name="create-the-biztalk-services-project"></a>Criar o projeto dos Serviços BizTalk
1. Na solução do Visual Studio, expanda o projeto InvoiceProcessingBridge e abra o arquivo **Messageflowitinerary** .
2. Clique em qualquer lugar na tela e defina a **URL do Serviço BizTalk** na caixa de propriedade para especificar o nome da sua assinatura dos Serviços BizTalk. Por exemplo: `https://contosowabs.biztalk.windows.net`.
   
   ![][7]  
3. Na caixa de ferramentas, arraste uma **Ponte unidirecional de Xml** para a tela. Defina as propriedades **Entity Name** e **Relative Address** da ponte para **ProcessInvoiceBridge**. Clique duas vezes em **ProcessInvoiceBridge** para abrir a superfície de configuração da ponte.
4. Na caixa **Tipos de Mensagem**, clique no sinal de adição (**+**) para especificar o esquema da mensagem de entrada. Como a mensagem de entrada para a ponte EAI é sempre a fatura interna, defina-a como **INHOUSEINVOICE**.
   
   ![][8]  
5. Clique na forma **Transformação Cml** e na caixa de propriedade; para a propriedade **Maps**, clique no botão de reticências (**...**). Na caixa de diálogo **Seleção de Mapas**, selecione o arquivo de transformação **INHOUSEINVOICE_to_D93AINVOIC** e clique em **OK**.
   
   ![][9]  
6. Volte para **MessageFlowItinerary.bcs** e, na caixa de ferramentas, arraste um **Ponto de extremidade de serviço externo bidirecional** à direita do **ProcessInvoiceBridge**. Defina a propriedade **Entity Name** como **EDIBridge**.
7. No Gerenciador de Soluções, expanda o **MessageFlowItinerary.bcs** e clique duas vezes no arquivo **EDIBridge.config**. Substitua o conteúdo de **EDIBridge.config** com o seguinte:
   
   > [!NOTE]
   > Por que preciso editar o arquivo .config? O ponto de extremidade de serviço externo que adicionamos à tela do designer de ponte representa as pontes EDI que foram implantadas anteriormente. As pontes EDI são bidirecionais, com lado de envio e de recebimento. No entanto, a ponte EAI que adicionamos ao designer de ponte é uma ponte unidirecional. Portanto, para lidar com os padrões de troca de mensagem diferentes das duas pontes, usamos um comportamento de ponte personalizado, incluindo sua configuração no arquivo.config. Além disso, o comportamento personalizado também lida com a autenticação para o ponto de extremidade de ponte de envio EDI. Esse comportamento personalizado está disponível como um exemplo separado em [Exemplo de encadeamento de ponte dos Serviços BizTalk – EAI para EDI](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104). Essa solução reutiliza o exemplo.  
   > 
   > 
   
   ```
   <?xml version="1.0" encoding="utf-8"?>
   <configuration>
     <system.serviceModel>
       <extensions>
         <behaviorExtensions>
           <add name="BridgeAuthentication" 
                 type="Microsoft.BizTalk.Bridge.Behaviour.BridgeBehaviorElement, Microsoft.BizTalk.Bridge.Behaviour, Version=1.0.0.0, Culture=neutral, PublicKeyToken=ae58f69b69495c05" />
         </behaviorExtensions>
       </extensions>
       <behaviors>
         <endpointBehaviors>
           <behavior name="BridgeAuthenticationConfiguration">
             <!-- Enter the ACS namespace, issuer name and issuer secret of the BizTalk Services deployment -->
             <BridgeAuthentication acsnamespace="[YOUR ACS NAMESPACE]" 
                                   issuername="owner" 
                                   issuersecret="[YOUR ACS SECRET]" />
             <webHttp />
           </behavior>
         </endpointBehaviors>
       </behaviors>
       <bindings>
         <webHttpBinding>
           <binding name="BridgeBindingConfiguration">
             <security mode="Transport" />
           </binding>
         </webHttpBinding>
       </bindings>
       <client>
         <clear />
         <!--
           Go BizTalk Portal > Agreement > Send Settings > Inbound URL
           Copy the Endpoint URL and paste it in the below address field
         -->
         <endpoint name="TwoWayExternalServiceEndpointReference1" 
                   address="[YOUR EDI BRIDGE SEND URI]" 
                   behaviorConfiguration="BridgeAuthenticationConfiguration" 
                   binding="webHttpBinding" 
                   bindingConfiguration="BridgeBindingConfiguration" 
                   contract="System.ServiceModel.Routing.IRequestReplyRouter" />
       </client>
     </system.serviceModel>
   </configuration>
   
   ```
8. Atualize o arquivo EDIBridge.config para incluir detalhes de configuração
   
   * Em *<behaviors>*, forneça o namespace do ACS e a chave associada à assinatura dos Serviços BizTalk.
   * Em *<client>*, forneça o ponto de extremidade no qual o contrato de envio EDI está implantado.
   
   Salve as alterações e feche o arquivo de configuração.
9. Na Caixa de Ferramentas, clique no **Conector** e adicione os componentes **ProcessInvoiceBridge** e **EDIBridge**. Selecione o conector e, na caixa Propriedades, defina **Condição de Filtro** como **Corresponder Tudo**. Isso garante que todas as mensagens processadas pela ponte EAI sejam roteadas para a ponte EDI.
   
   ![][10]  
10. Salve as alterações na solução.  

### <a name="deploy-the-project"></a>Implantar o projeto
1. No computador onde você criou o projeto dos Serviços BizTalk, baixe e instale o certificado SSL para sua assinatura dos Serviços BizTalk. Em Serviços BizTalk, clique em **Painel** e em **Baixar Certificado SSL**. Clique duas vezes no certificado e siga os prompts para concluir a instalação. Não deixe de instalar o certificado no repositório de certificados **Autoridades confiáveis de certificação de raiz** .
2. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto **InvoiceProcessingBridge** e em **Implantar**.
3. Forneça os valores conforme mostrado na imagem e clique em **Implantar**. É possível obter as credenciais do ACS para os Serviços BizTalk clicando em **Informações de Conexão** no painel dos Serviços BizTalk.
   
   ![][11]  
   
   No painel de saída, copie o ponto de extremidade onde a ponte EAI foi implantada, por exemplo, `https://contosowabs.biztalk.windows.net/default/ProcessInvoiceBridge`. Você precisará dessa URL de ponto de extremidade mais tarde.  

## <a name="step-4-test-the-solution"></a>Etapa 4: Testar a solução
Neste tópico, veremos como testar a solução usando o aplicativo **Cliente de Tutorial** fornecido como parte do exemplo.  

1. No Visual Studio, pressione F5 para iniciar o **Cliente de Tutorial**.
2. A tela deverá ter valores pré-populados da etapa de criação de filas do Barramento de Serviço. Clique em **Avançar**.
3. Na próxima janela, forneça as credenciais de ACS para a assinatura dos Serviços BizTalk e os pontos de extremidade onde as pontes EAI e EDI (recebimento) foram implantadas.
   
   O ponto de extremidade de ponte EAI foi copiado na etapa anterior. Para o ponto de extremidade da ponte de recebimento EDI, no Portal dos Serviços BizTalk, acesse o contrato > Configurações de Recebimento > Transporte > Ponto de Extremidade.
   
   ![][12]  
4. Na próxima janela, em Contoso, clique no botão **Enviar Fatura Interna** . Na caixa de diálogo Abrir arquivo, abra o arquivo INHOUSEINVOICE.txt. Examine o conteúdo do arquivo e clique em **OK** para enviar a fatura.
   
   ![][13]  
5. Em poucos segundos, a fatura é recebida pela Northwind. Clique no link **Exibir Mensagem** para ver a fatura recebida pela Northwind. Observe como a fatura recebida pela Northwind está no esquema padrão EDIFACT enquanto aquela enviada pela Contoso estava no esquema interno.
   
   ![][14]  
6. Selecione a fatura e clique em **Enviar Confirmação**. Na caixa de diálogo exibida, observe que o ID da troca é igual ao da fatura recebida e da confirmação enviada. Clique em OK na caixa de diálogo **Enviar Confirmação** .
   
   ![][15]  
7. Em poucos segundos, a fatura será recebida pela Contoso.
   
   ![][16]  

## <a name="step-5-optional-send-edifact-invoice-in-batches"></a>Etapa 5 (opcional): Enviar fatura EDIFACT em lotes
As pontes EDI dos Serviços BizTalk também dão suporte ao processamento em lotes de mensagens de saída. Esse recurso é útil para parceiros receptores que preferem receber um lote de mensagens (atendendo a um determinado critério) em vez de mensagens individuais.

O aspecto mais importante ao trabalhar em lotes é a versão real do lote, também chamada de critério de liberação. Os critérios de liberação podem basear-se em como o parceiro receptor deseja receber mensagens. Se o envio em lote estiver habilitado, a ponte EDI não enviará a mensagem de saída para o parceiro receptor até que os critérios de liberação sejam atendidos. Por exemplo, um critério de lote baseado no tamanho da mensagem liberará um lote somente quando ' n' mensagens estiverem agrupadas. Um critério de lote também pode ser um baseado em tempo, para que um lote seja enviado em uma hora fixa todo dia. Nesta solução, experimentamos os critérios de acordo com o tamanho da mensagem.

1. No Portal dos Serviços BizTalk, clique no contrato que você criou anteriormente. Clique em Configurações de Envio > Em lote > Adicionar Lotes.
2. Para o nome do lote, digite **InvoiceBatch**, forneça uma descrição e clique em **Avançar**.
3. Especifique os critérios de lote, que definem quais mensagens devem ser agrupadas. Nesta solução, agrupamos todas as mensagens. Selecione a opção Usar definições avançadas e digite **1 = 1**. Essa é uma condição que será sempre verdadeira e, portanto, todas as mensagens serão agrupadas. Clique em **Avançar**.
   
   ![][17]  
4. Especifique um critério de liberação de lote. Na caixa suspensa, selecione **MessageCountBased** e, para **Contagem**, especifique **3**. Isso significa que um lote de três mensagens será enviado para a Northwind. Clique em **Avançar**.
   
   ![][18]  
5. Examine o resumo e clique em **Salvar**. Clique em **Implantar** para reimplantar o contrato.
6. Volte para o **Cliente de Tutorial**, clique em **Enviar Fatura Interna** e siga os prompts para enviar a fatura. Você observará que nenhuma fatura foi recebida na Northwind porque o tamanho do lote não foi atendido. Repita essa etapa mais duas vezes, para que você tenha três mensagens de faturas enviadas para a Northwind. Isso satisfaz os critérios de liberação de lote de três mensagens e agora você deverá ver uma fatura na Northwind.

<!--Image references-->
[1]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-1.PNG  
[2]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-2.PNG  
[3]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-3.PNG
[4]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-4.PNG  
[5]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-5.PNG  
[6]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-6.PNG  
[7]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-7.PNG  
[8]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-8.PNG
[9]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-9.PNG  
[10]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-10.PNG  
[11]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-11.PNG  
[12]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-12.PNG  
[13]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-13.PNG
[14]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-14.PNG  
[15]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-15.PNG  
[16]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-16.PNG  
[17]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-17.PNG  
[18]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-18.PNG

