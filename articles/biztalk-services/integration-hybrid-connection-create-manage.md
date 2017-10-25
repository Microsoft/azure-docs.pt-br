---
title: "Criar e gerenciar Conexões Híbridas | Microsoft Docs"
description: "Saiba como criar uma conexão híbrida, gerenciar a conexão e instalar o Gerenciador de conexões híbridas. MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: erikre
editor: 
ms.assetid: aac0546b-3bae-41e0-b874-583491a395ea
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: ccompy
ms.openlocfilehash: 7b8b9072d0e2fd054ca07873c0a9ce772dc2941e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-hybrid-connections"></a>Criar e gerenciar Conexões Híbridas

> [!IMPORTANT]
> As Conexões Híbridas do BizTalk foram desativadas e substituídas pelas Conexões Híbridas do Serviço de Aplicativo. Para saber mais, incluindo como gerenciar as Conexões Híbridas do BizTalk existentes, veja [Conexões Híbridas do Serviço de Aplicativo do Azure](../app-service/app-service-hybrid-connections.md).


## <a name="overview-of-the-steps"></a>Visão geral das etapas
1. Crie uma Conexão Híbrida inserindo o **host name** ou **FQDN** of the on-premises resource in your private netwouk.
2. Conecte seus aplicativos Web do Azure ou aplicativos móveis do Azure à Conexão Híbrida.
3. Instale o Gerenciador de Conexão Híbrida em seu recurso local e conecte-se à Conexão Híbrida específica. O portal do Azure oferece uma experiência de apenas um clique para instalar e conectar.
4. Gerenciar Conexões Híbridas e suas chaves de conexão.

Este tópico lista estas etapas. 

> [!IMPORTANT]
> É possível definir um ponto de extremidade de Conexão Híbrida a um endereço IP. Se você usar um endereço IP, poderá ou não acessar o recurso local, dependendo de seu cliente. A Conexão Híbrida depende do cliente fazendo uma pesquisa de DNS. Na maioria dos casos, o **cliente** é o código do aplicativo. Se o cliente não executar uma pesquisa de DNS (ele não tenta resolver o endereço IP como se fosse um nome de domínio (x.x.x. x)), o tráfego não será enviado por meio da Conexão Híbrida.
> 
> Por exemplo (pseudocódigo), você define **10.4.5.6** como seu host local:
> 
> **O cenário a seguir funciona:**  
> `Application code -> GetHostByName("10.4.5.6") -> Resolves to 127.0.0.3 -> Connect("127.0.0.3") -> Hybrid Connection -> on-prem host`
> 
> **O cenário a seguir não funciona:**  
> `Application code -> Connect("10.4.5.6") -> ?? -> No route to host`
> 
> 

## <a name="CreateHybridConnection"></a>Criar uma Conexão Híbrida
É possível criar uma Conexão Híbrida no portal do Azure usando Aplicativos Web **ou** os Serviços BizTalk. 

<!-- **To create Hybrid Connections using Web Apps**, see [Connect Azure Web Apps to an On-Premises Resource](../app-service-web/web-sites-hybrid-connection-get-started.md). You can also install the Hybrid Connection Manager (HCM) from your web app, which is the preferred method.  -->

**Para criar Conexões Híbridas em Serviços do BizTalk**:

1. Entre no [portal clássico do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Selecione **Serviços do BizTalk** no painel de navegação à esquerda e selecione o seu Serviço do BizTalk. 
   
    Se não tiver um Serviço BizTalk, você poderá [Criar um Serviço BizTalk](biztalk-provision-services.md).
3. Selecione a guia **Conexões Híbridas**:  
   ![Guia de Conexões Híbridas][HybridConnectionTab]
4. Selecione **Criar uma Conexão Híbrida** ou selecione o botão **ADD** na barra de tarefas. Insira o seguinte:
   
   | Propriedade | Descrição |
   | --- | --- |
   | Nome |O nome da Conexão Híbrida precisa ser único e não pode ser igual ao nome do Serviço do BizTalk. Você pode inserir qualquer nome, mas seja específico quanto à sua finalidade. Alguns exemplos incluem: <br/><br/>Payroll*SQLServer*<br/>SupplyList*SharepointServer*<br/>Clientes*OracleServer* |
   | Nome de host |Insira o nome de host totalmente qualificado, somente o nome de host ou, então, o endereço IPv4 do recurso local. Alguns exemplos incluem: <br/><br/>mySQLServer<br/>*mySQLServer*.*Domain*.corp.*yourCompany*.com<br/>*myHTTPSharePointServer*<br/>*myHTTPSharePointServer*.*yourCompany*.com<br/>10.100.10.10<br/><br/>Se você usar o endereço IPv4, observe que o código de cliente ou aplicativo pode não resolver o endereço IP. Consulte a observação Importante no início deste tópico. |
   | Port |Insira o número da porta no recurso local. Por exemplo, se você estiver usando Aplicativos Web, insira a porta 80 ou 443. Se você estiver usando o SQL Server, insira a porta 1433. |
5. Clique na marca de seleção para concluir a configuração. 

#### <a name="additional"></a>Adicional
* Conexões Híbridas adicionais podem ser criadas. Consulte os [Serviços BizTalk: gráfico de edições](biztalk-editions-feature-chart.md) para obter o número de conexões permitidas. 
* Cada Conexão Híbrida é criada com um par de cadeias de conexão: chaves de aplicativo que ENVIAM e chaves locais que ESCUTAM. Cada par possui uma chave primária e outra secundária. 

## <a name="LinkWebSite"></a>Vincular seu Aplicativo Móvel ou aplicativo Web ou do Serviço de Aplicativo do Azure
Para conectar um aplicativo Web ou Aplicativo Móvel no Serviço de Aplicativo do Azure a uma Conexão Híbrida existente, selecione **usar uma Conexão Híbrida existente** na folha Conexões Híbridas. 
<!-- See [Access on-premises resources using hybrid connections in Azure App Service](../app-service-web/web-sites-hybrid-connection-get-started.md). -->

## <a name="InstallHCM"></a>Instalar o Gerenciador de Conexões Híbridas local
Após uma Conexão Híbrida ser criada, instale o Gerenciador de Conexões Híbridas no recurso local. É possível fazer o download por meio dos aplicativos Web do Azure ou por meio de seu Serviço BizTalk. Etapas dos Serviços BizTalk: 

1. Entre no [portal clássico do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Selecione **Serviços do BizTalk** no painel de navegação à esquerda e selecione o seu Serviço do BizTalk. 
3. Selecione a guia **Conexões Híbridas**:  
   ![Guia de Conexões Híbridas][HybridConnectionTab]
4. Na barra de tarefas, selecione **Configuração Local**:  
   ![Configuração Local][HCOnPremSetup]
5. Selecione **Instalar e Configurar** para executar ou baixar o Gerenciador de Conexões Híbridas no sistema local. 
6. Selecione a marca de seleção para iniciar a instalação. 

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
--> 

#### <a name="additional"></a>Adicional
* O Gerenciador de Conexões Híbridas pode ser instalado nos seguintes sistemas operacionais:
  
  * Windows Server 2008 R2 (.NET Framework 4.5+ e Windows Management Framework 4.0+ obrigatório)
  * Windows Server 2012 (Windows Management Framework 4.0+ obrigatório)
  * Windows Server 2012 R2
* Após você instalar o Gerenciador de Conexões Híbridas, ocorre o seguinte: 
  
  * A Conexão Híbrida hospedada no Azure é configurada automaticamente para utilizar a cadeia de conexão primária do aplicativo. 
  * O recurso local é configurado automaticamente para utilizar a cadeia de conexão primária local.
* O Gerenciador de Conexões Híbridas precisa utilizar uma cadeia de conexão local válida para autorização. Os Aplicativos Web ou Aplicativos Móveis do Azure precisam utilizar uma cadeia de conexão ao aplicativo válida para autorização.
* Você pode dimensionar conexões híbridas instalando outra instância do Gerenciador de conexões híbridas em outro servidor. Configure o ouvinte local para usar o mesmo endereço como o primeiro ouvinte local. Nessa situação, o tráfego é distribuído aleatoriamente (round robin) entre os ouvintes ativos locais. 

## <a name="ManageHybridConnection"></a>Gerenciar Conexões Híbridas
Para gerenciar suas Conexões Híbridas, você pode:

* Utilizar o portal do Azure e ir até seu Serviço do BizTalk. 
* Usar [APIs REST](http://msdn.microsoft.com/library/azure/dn232347.aspx).

#### <a name="copyregenerate-the-hybrid-connection-strings"></a>Copiar/regenerar as cadeias de conexão híbrida
1. Entre no [portal clássico do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Selecione **Serviços do BizTalk** no painel de navegação à esquerda e selecione o seu Serviço do BizTalk. 
3. Selecione a guia **Conexões Híbridas**:  
   ![Guia de Conexões Híbridas][HybridConnectionTab]
4. Selecione a Conexão Híbrida. Na barra de tarefas, selecione **Gerenciar Conexão**:  
   ![Gerenciar opções][HCManageConnection]
   
    **Gerenciar Conexão** lista as cadeias de conexão do aplicativo e local. É possível copiar as cadeias de conexão ou regenerar a chave de acesso usada na cadeia de conexão. 
   
    **Se você selecionar Regenerar**, a Tecla de Acesso compartilhada usada na Cadeia de Conexão será alterada. Faça o seguinte:
   
   * No portal clássico do Azure, selecione **Sincronizar Chaves** no aplicativo do Azure.
   * Execute novamente a **Configuração Local**. Ao executar novamente a Configuração Local, o recurso local é configurado automaticamente para usar a cadeia de conexão primária atualizada.

#### <a name="use-group-policy-to-control-the-on-premises-resources-used-by-a-hybrid-connection"></a>Use a Política de Grupo para controlar os recursos locais utilizados por uma Conexão Híbrida
1. Baixe os [modelos administrativos do Gerenciador de Conexões Híbridas](http://www.microsoft.com/download/details.aspx?id=42963).
2. Extraia os arquivos.
3. No computador que modifica a política de grupo, faça o seguinte:  
   
   * Copie os arquivos .ADMX para a pasta *%WINROOT%\PolicyDefinitions*.
   * Copie os arquivos .ADML para a pasta *%WINROOT%\PolicyDefinitions\en-us*.

Após tê-los copiado, você pode usar o Editor de políticas de grupo para mudar a política.

## <a name="next"></a>Avançar
[Visão geral de Conexões Híbridas](integration-hybrid-connection-overview.md)

## <a name="see-also"></a>Consulte também
[API REST para gerenciamento dos Serviços do BizTalk no Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)  
[Serviços BizTalk: gráfico de edições](biztalk-editions-feature-chart.md)  
[Criar um Serviço BizTalk usando o Portal de clássico do Azure](biztalk-provision-services.md)  
[Serviços BizTalk: guias Painel, Monitor e Escala](biztalk-dashboard-monitor-scale-tabs.md)

[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png 
