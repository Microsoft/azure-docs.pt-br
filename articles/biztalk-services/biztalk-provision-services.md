---
title: "Criar Serviços BizTalk do Azure no Portal do Azure | Microsoft Docs"
description: "Saiba como provisionar ou criar Serviços BizTalk no Portal do Azure; MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 3ad18876-a649-40d6-9aa0-1509c1d62c43
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: eca77b4a82eb67e1755717bb4429f8d450a64dc5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-biztalk-services-using-the-azure-portal"></a>Criar Serviços BizTalk usando o Portal do Azure

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]


> [!TIP]
> Para entrar no Portal do Azure, você precisa de uma conta e de uma assinatura do Azure. Se você não tiver uma conta, será possível criar uma conta de avaliação gratuita em questão de minutos. Consulte [Avaliação gratuita do Azure](http://go.microsoft.com/fwlink/p/?LinkID=239738).


## <a name="CreateService"></a>Criar um Serviço do BizTalk
Dependendo da Edição que você escolher, será possível que não todas as configurações de Serviço do BizTalk estejam disponíveis.

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Na parte inferior do painel de navegação, selecione **NOVO**:  
   ![Selecionar o botão Novo][NEWButton]
3. Selecione **SERVIÇOS DE APLICATIVOS** > **SERVIÇO BIZTALK** > **CRIAÇÃO PERSONALIZADA**:  
   ![Selecionar Serviço BizTalk e Criação Personalizada][NewBizTalkService]
4. Insira as configurações do Serviço BizTalk:
   
    <table border="1">
    <tr>
    <td><strong>Nome do Serviço BizTalk</strong></td>
    <td>Você pode inserir qualquer nome, mas seja específico. Alguns exemplos incluem:<br/><br/>
    <em>mycompany</em>.biztalk.windows.net<br/>
    <em>mycompanymyapplication</em>.biztalk.windows.net<br/>
    <em>myapplication</em>.biztalk.windows.net<br/><br/>".biztalk.windows.net" é adicionado automaticamente ao nome que você inserir. Isso cria uma URL que é usada para acessar seu Serviço do BizTalk, como <strong>https://<em>meuaplicativo</em>.biztalk.windows.net</strong>.
    </td>
    </tr>
    <tr>
    <td><strong>Edição</strong></td>
    <td>Se você estiver em fase de teste/desenvolvimento, escolha <strong>Desenvolvedor</strong>. Se você estiver na fase de produção, use os <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302279">Serviços BizTalk: Gráfico de Edições</a> para determinar se <strong>Premium</strong>, <strong>Standard</strong> ou <strong>Básico</strong> é a escolha certa para seu cenário de negócios.
    </td>
    </tr>
    <tr>
    <td><strong>Região</strong></td>
    <td>Selecione a região geográfica para hospedar seu Serviço BizTalk.</td>
    </tr>
    <tr>
    <td><strong>URL do Domínio</strong></td>
    <td><strong>Opcional</strong>. Por padrão, a URL do domínio é <em>YourBizTalkServiceName</em>.biztalk.windows.net. Um domínio personalizado também pode ser inserido. Por exemplo, se seu domínio for <em>contoso</em>, insira: <br/><br/>
    <em>MyCompany</em>.contoso.com<br/>
    <em>MyCompanyMyApplication</em>.contoso.com<br/>
    <em>MyApplication</em>.contoso.com<br/>
    <em>YourBizTalkServiceName</em>.contoso.com<br/>
    </td>
    </tr>
    </table>
Selecione a seta de AVANÇO.
5. Inserir as configurações de banco de dados e de armazenamento:  <table border="1">
    <tr>
    <td><strong>Conta de armazenamento de Monitoramento/Arquivamento</strong></td>
    <td>Selecione uma conta de armazenamento existente ou crie uma nova. <br/><br/>Se você criar uma nova conta de Armazenamento, digite o <strong>Nome da Conta de Armazenamento</strong>.</td>
    </tr>
    <tr>
    <td><strong>Acompanhamento de banco de dados</strong></td>
    <td>Se você usar um banco de dados SQL do Azure, não poderá ser usado por outro Serviço do BizTalk. Você precisará do nome e da senha de logon inseridos na criação do Banco de Dados SQL Azure.<br/><br/><strong>DICA</strong> Crie o banco de dados de Acompanhamento e a conta de armazenamento de Monitoramento/Arquivamento na mesma região do Serviço BizTalk.</td>
    </tr>
    </table>
Selecione a seta de AVANÇO.
6. Insira as configurações do banco de dados:  <table border="1">
    <tr>
    <td><strong>Nome</strong></td>
    <td>Disponível quando a opção <strong>Criar uma nova instância do Banco de Dados SQL</strong> é selecionada na tela anterior.
    <br/><br/>
Insira um nome do Banco de Dados SQL a ser usado pelo seu Serviço BizTalk.</td>
    </tr>
    <tr>
    <td><strong>Servidor</strong></td>
    <td>Disponível quando a opção <strong>Criar uma nova instância do Banco de Dados SQL</strong> é selecionada na tela anterior.
    <br/><br/>
Selecione um Servidor de Banco de Dados SQL ou crie um novo servidor de Banco de Dados SQL.</td>
    </tr>
    <tr>
    <td><strong>Nome de logon do servidor</strong></td>
    <td>Digite o nome de usuário do logon.</td>
    </tr>
    <tr>
    <td><strong>Senha de logon do servidor</strong></td>
    <td>Digite a senha do logon.</td>
    </tr>
    <tr>
    <td><strong>Região</strong></td>
    <td>Disponível quando a opção <strong>Criar uma nova instância do Banco de Dados SQL</strong> é selecionada. Selecione a região geográfica para hospedar seu Banco de Dados SQL.</td>
    </tr>
    </table>

Selecione a marca de seleção para concluir o assistente. O ícone de progresso aparece:   
![O ícone de progresso é exibido após a conclusão][ProgressComplete]

Ao concluir, o Serviço BizTalk do Azure será criado e estará pronto para seus aplicativos. As configurações padrão são suficientes. Se você desejar alterar as configurações padrão, selecione **SERVIÇOS BIZTALK** no painel de navegação à esquerda e selecione o seu Serviço BizTalk. Configurações adicionais são exibidas nas [guias Painel, Monitor e Escala.](biztalk-dashboard-monitor-scale-tabs.md) na parte superior.

Dependendo do estado do Serviço BizTalk, existem algumas operações que não podem ser concluídas. Para obter uma lista dessas operações, consulte [Gráfico de estado do serviço BizTalk](biztalk-service-state-chart.md).

## <a name="post-provisioning-steps"></a>Etapas pós-provisionamento
* [Instalar o certificado em um computador local](#InstallCert)
* [Adicionar um certificado pronto para produção](#AddCert)
* [Obter o namespace do Controle de Acesso](#ACS)

#### <a name="InstallCert"></a>Instalar o certificado em um computador local
Como parte do provisionamento do Serviço do BizTalk, um certificado autoassinado é criado e associado com a assinatura do seu serviço do BizTalk. Você deve baixar este certificado e instalá-lo em computadores de onde você implemente os aplicativos do Serviço do BizTalk ou envie mensagens para um ponto de extremidade do Serviço do BizTalk.

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Clique em **SERVIÇOS BIZTALK** no painel de navegação à esquerda e selecione a assinatura do seu Serviço BizTalk.
3. Selecione a guia **Painel** .
4. Selecione **Baixar Certificado SSL**:  
   ![Modificar certificado SSL][QuickGlance]
5. Clique duas vezes no certificado e execute-o com o assistente para instalar o certificado. Certifique-se de instalar o certificado no armazenamento **Autoridades de certificação de raiz confiáveis** .

#### <a name="AddCert"></a>Adicionar um certificado pronto para produção
O certificado autoassinado criado automaticamente durante a criação dos Serviços BizTalk devem ser usados apenas em ambientes de desenvolvimento. Para cenários de produção, substitua-o por um certificado pronto para produção.

1. Na guia **Painel**, selecione **Atualizar Certificado SSL**.
2. Procure o certificado SSL privado (*CertificateName*.pfx) que inclui o nome do seu Serviço do BizTalk, insira a senha e clique na caixa de seleção.

#### <a name="ACS"></a>Obter o namespace do Controle de Acesso
1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Selecione **SERVIÇOS BIZTALK** no painel de navegação à esquerda e selecione o seu Serviço BizTalk.
3. Na barra de tarefas, selecione **Informações da Conexão**:  
   ![Selecionar Informações de conexão][ACSConnectInfo]
4. Copiar os valores de Controle de Acesso.

Ao implantar um projeto do Serviço BizTalk no Visual Studio, você insere esse namespace do Controle de Acesso. O namespace do Controle de Acesso é criado automaticamente para seu Serviço BizTalk.

Os valores de Controle de acesso podem ser usados com qualquer aplicativo. Quando os Serviços do BizTalk do Microsoft Azure estiverem criados, esse namespace do Controle de Acesso controla a autenticação com sua implantação do Serviço BizTalk. Se você desejar alterar a assinatura ou gerenciar o namespace, selecione **ACTIVE DIRECTORY** no painel de navegação à esquerda e selecione seu namespace. A barra de tarefas lista suas opções.

Um clique em **Gerenciar** abre o Portal de Gerenciamento do Controle de Acesso. No Portal de Gerenciamento do Controle de Acesso, o Serviço BizTalk usa **Identidades de serviço**:  
![Identidades do serviço ACS no Portal de Gerenciamento do Controle de Acesso][ACSServiceIdentities]

A identidade do serviço do Controle de Acesso é um conjunto de credenciais que permitem que aplicativos ou clientes façam autenticação diretamente com o Controle de Acesso e recebam um token.

> [!IMPORTANT]
> O Serviço BizTalk usa **Proprietário** para a identidade do serviço padrão e o valor da **Senha**. Se você usar o valor de Chave Simétrica em vez de o valor de Senha, poderá ocorrer o erro a seguir.<br/><br/>*Não foi possível conectar à conta do Serviço de Gerenciamento do Controle de Acesso com as credenciais especificadas*
> 
> 

[Gerenciando o namespace de seu ACS](https://msdn.microsoft.com/library/azure/hh674478.aspx) lista algumas diretrizes e recomendações.

## <a name="requirements-explained"></a>Requisitos explicados
Estes requisitos não se aplicam à edição gratuita.

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>O que você precisa</strong></td>
        <td><strong>Por que você precisa disto</strong></td>
</tr>
<tr>
<td>Assinatura do Azure</td>
<td>A assinatura determina quem pode entrar no portal do Azure. O Titular da conta cria a assinatura em <a HREF="https://account.windowsazure.com/Subscriptions">Assinaturas do Azure</a>.
<br/><br/>
A conta do Azure pode ter várias assinaturas e pode ser gerenciada por qualquer pessoa que tenha permissão. Por exemplo, o titular da sua conta do Azure cria uma assinatura chamada <em>BizTalkServiceSubscription</em> e dá acesso a essa assinatura aos Administradores do BizTalk em sua empresa (por exemplo, ContosoBTSAdmins@live.com). Nesse cenário, os Administradores do BizTalk entram no Portal do Azure e têm acesso total aos serviços hospedados na assinatura, inclusive os Serviços BizTalk do Azure. Os administradores do BizTalk não são os titulares da conta do Azure e, portanto, não têm acesso às informações de cobrança.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577">Gerenciar assinaturas e contas de armazenamento no Portal do Azure</a> fornece mais informações.
</td>
</tr>
<tr>
<td>Banco de Dados SQL do Azure</td>
<td>Armazena as tabelas, as exibições e os procedimentos armazenados usados pelo Serviço BizTalk do Azure, incluindo o Rastreamento de dados
<br/><br/>
Ao criar um Serviço BizTalk, você pode usar um SQL Server do Azure ou um Banco de Dados SQL do Azure ou criar automaticamente um novo servidor ou banco de dados.
<br/><br/>
A escala do Banco de Dados SQL é configurada automaticamente. Tipicamente a escala padrão é suficiente para um Serviço BizTalk. Alterar a escala afeta ao preço. Confira <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=234930">Contas e faturamento no banco de dados SQL do Azure</a>
<br/><br/>
<strong>Observações</strong>
<br/>
<ul>
<li> Quando você criar um novo SQL Server e Banco de Dados do Azure, os Serviços do Azure são habilitados automaticamente. O Serviço do BizTalk requer que os Serviços do Azure sejam habilitados.</li>
<li>Se você criar um novo Banco de Dados SQL do Azure em um SQL Server do Azure existente, as regras do firewall do servidor não serão alterados. Como resultado, é possível que outros Serviços do Azure não tenham permissão de acesso aos bancos de dados do servidor.</li>
</ul>
</td>
</tr>
<tr>
<td>Namespace do Controle de Acesso do Azure</td>
<td>Autenticados com os Serviços do BizTalk do Azure. Ao implantar um projeto do Serviço BizTalk no Visual Studio, você insere esse namespace do Controle de Acesso. Quando você cria um Serviço BizTalk, o namespace de Controle de Acesso é criado automaticamente.</td>
</tr>

<tr>
<td>Conta de Armazenamento do Azure</td>
<td>Dá acesso às tabelas, aos blobs e às filas usadas pelos Serviços do BizTalk para fazer o seguinte:

<ul>
<li>Arquivos de log que monitoram o Serviço do BizTalk. A saída do monitoramento também é exibida na guia **Monitoramento** no Portal do Azure.</li>
<li>Ao criar um contrato X12 ou AS2 entre parceiros, você pode habilitar o recurso de Arquivamento para armazenar propriedades de mensagens. Esses dados são salvos na conta de Armazenamento.</li>
</ul>
<br/>
Ao criar um Serviço BizTalk, você pode usar uma conta de Armazenamento existente ou criar automaticamente uma nova conta de Armazenamento.
<br/><br/>
As configurações de Armazenamento padrão são suficientes para um Serviço BizTalk.
<br/><br/>
Quando você cria uma Conta de Armazenamento, uma chave primária e uma chave secundária são criadas automaticamente. Essas chaves controlam o acesso à sua conta de Armazenamento. O Serviço BizTalk usa automaticamente a chave primária.
<br/><br/>
Consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285671">Armazenamento</a> para obter mais informações.
</td>
</tr>

<tr>
<td>Certificado SSL privado</td>
<td>
Quando um Serviço do BizTalk do Azure for criado, uma URL HTTPS que inclui o nome do seu Serviço do BizTalk também é criado. Esta URL é configurada automaticamente para usar um certificado autoassinado apenas de desenvolvimento. Para produção, você necessita um certificado SSL privado.
<br/><br/>
<strong>Informações importantes sobre o Certificado SSL</strong>

<ul>
<li>A data de expiração do certificado deve ser anterior a cinco anos.</li>
<li>Todos os certificados privados exigem uma senha. Saiba essa senha e, como uma prática recomendada, compartilhe essa senha com seus administradores.</li>
<li>Os certificados autoassinados são usados em um ambientes de desenvolvimento/teste. Ao usar certificados autoassinados, importe o certificado para o armazenamento de certificados pessoais e o armazenamento de certificados de Autoridades de Certificação Raiz Confiáveis.</li>
</ul>
<br/>Ao enviar a solicitação de certificado de produção à autoridade de certificação, forneça as seguintes propriedades de certificado:
<br/>

<ul>
<li><strong>Uso avançado de chave</strong>: no mínimo, os Serviços BizTalk do Azure exigem a Autenticação de Servidor.</li>
<li><strong>Nome comum</strong>: insira o nome de domínio totalmente qualificado (FQDN) da sua URL de Serviço do BizTalk do Azure. Veja <a HREF="#CreateService">Criar um Serviço BizTalk</a> neste artigo.</li>
</ul>
<br/>
Um certificado novo ou diferente pode ser adicionado após o Serviço BizTalk ser criado.
</td>
</tr>
</table>
<!---Loc Comment: Please, check link [Create a BizTalk Service] since it is not redirecting to any location.--->



## <a name="hybrid-connections"></a>Conexões Híbridas
Quando você cria um Serviço do BizTalk do Azure, a guia **Conexões híbridas** está disponível:

![Guia de Conexões Híbridas][HybridConnectionTab]

As Conexões Híbridas são usadas para conectar um site do Azure ou um serviço móvel do Azure a um recurso local que utilize uma porta TCP estática, como SQL Server, MySQL, APIs da web HTTP, e os serviços Web mais personalizados.  As Conexões híbridas e o Serviço do adaptador do BizTalk são diferentes. O Serviço do Adaptador do BizTalk é usado para conectar os Serviços do BizTalk do Azure a um sistema de Linha de Negócios (LOB) local.

 Consulte [Conexões híbridas](integration-hybrid-connection-overview.md) para saber mais, incluindo a criação e gerenciamento de Conexões híbridos.

## <a name="next-steps"></a>Próximas etapas
Agora que o Serviço BizTalk foi criado, familiarize-se com os diferentes [Serviços BizTalk: guias Painel, Monitor e Escala](biztalk-dashboard-monitor-scale-tabs.md). Seu Serviço BizTalk está pronto para os seus aplicativos. Para começar a criar aplicativos, visite [Serviços BizTalk do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Consulte também
* [Serviços BizTalk: gráfico de edições](biztalk-editions-feature-chart.md)<br/>
* [Serviços BizTalk: gráfico de estado](biztalk-service-state-chart.md)<br/>
* [Serviços BizTalk: backup e restauração](biztalk-backup-restore.md)<br/>
* [Serviços BizTalk: limitação](biztalk-throttling-thresholds.md)<br/>
* [Serviços BizTalk: nome e chave do emissor](biztalk-issuer-name-issuer-key.md)<br/>
* [Como começar a usar o SDK dos Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Conexões Híbridas](integration-hybrid-connection-overview.md)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
[HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png
