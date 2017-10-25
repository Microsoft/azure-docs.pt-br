---
title: "Instalar gateway de dados local - Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Antes de acessar fontes de dados locais, instale o gateway de dados local para transferência e criptografia rápidas de dados entre fontes de dados locais e aplicativos lógicos"
keywords: "acessar dados, local, transferência de dados, criptografia, fontes de dados"
services: logic-apps
documentationcenter: 
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 47e3024e-88a0-4017-8484-8f392faec89d
ms.service: logic-apps
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/14/2017
ms.author: LADocs; millopis; estfan
ms.openlocfilehash: b3c1e2afadea91f010c3e4b43206b6d30a75ec38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="install-the-on-premises-data-gateway-for-azure-logic-apps"></a>Instalar o gateway de dados local para Aplicativo Lógico do Azure

Antes que seus aplicativos lógicos possam acessar fontes de dados locais, você deve instalar e configurar o gateway de dados local. O gateway atua como uma ponte que fornece rápida transferência de dados e criptografia entre os sistemas locais e os aplicativos lógicos. O gateway retransmite dados de fontes locais em canais criptografados por meio do Barramento de Serviço do Azure. Todo o tráfego é originado como tráfego de saída seguro do agente de gateway. Saiba mais sobre [como o gateway de dados funciona](#gateway-cloud-service).

O gateway dá suporte a conexões com estas fontes de dados locais:

*   BizTalk Server 2016
*   DB2  
*   Sistema de Arquivos
*   Informix
*   MQ
*   MySQL
*   Banco de dados Oracle
*   PostgreSQL
*   Servidor de aplicativos SAP 
*   Servidor de mensagens SAP
*   SharePoint
*   SQL Server
*   Teradata

Estas etapas mostram como instalar o gateway de dados local primeiro antes de [configurar uma conexão entre o gateway e seus aplicativos lógicos](./logic-apps-gateway-connection.md). Para obter mais informações sobre os conectores com suporte, consulte [Conectores para Aplicativo Lógico do Azure](https://docs.microsoft.com/azure/connectors/apis-list). 

Para obter informações sobre como usar o gateway com outros serviços, consulte estes artigos:

*   [Gateway de dados local do Microsoft Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
*   [Gateway de dados local do Azure Analysis Services](../analysis-services/analysis-services-gateway.md)
*   [Gateway de dados local do Microsoft Flow](https://flow.microsoft.com/documentation/gateway-manage/)
*   [Gateway de dados local do Microsoft PowerApps](https://powerapps.microsoft.com/tutorials/gateway-management/)

<a name="requirements"></a>

## <a name="requirements"></a>Requisitos

**Mínimos**:

* .NET 4.5 Framework
* Versão de 64 bits do Windows 7 ou Windows Server 2008 R2 (ou posterior)

**Recomendados**:

* CPU de 8 núcleos
* Memória de 8 GB
* Versão de 64 bits do Windows 2012 R2 (ou posterior)

**Considerações importantes**:

* instale o gateway de dados local apenas em um computador local.
Você não pode instalar o gateway em um controlador de domínio.

   > [!TIP]
   > Você não precisa instalar o gateway no mesmo computador que a fonte de dados. Para minimizar a latência, você pode instalar o gateway o mais próximo possível da sua fonte de dados ou no mesmo computador, presumindo que você tenha permissões.

* Não instale o gateway em um computador que possa ser desligado, entrar em suspensão ou não se conectar à Internet, pois o gateway não poderá ser executado em tais circunstâncias. Além disso, o desempenho do gateway pode ser reduzido em uma rede sem fio.

* Durante a instalação, você deve entrar com uma [conta corporativa ou de estudante](https://docs.microsoft.com/azure/active-directory/sign-up-organization) gerenciada pelo Azure AD (Azure Active Directory), não uma conta da Microsoft.

  > [!TIP]
  > Se desejar usar uma conta da Microsoft que tenha um Visual Studio com a assinatura do MSDN, primeiro, [crie um diretório (Locatário) no Azure Active Directory](../active-directory/develop/active-directory-howto-tenant.md) com sua conta da Microsoft ou use o diretório padrão. Adicione um usuário com uma senha ao diretório, em seguida, forneça o acesso a esse usuário à sua assinatura. Em seguida, você pode entrar durante a instalação do gateway com esse nome de usuário e senha.

  Você precisa usar a mesma conta corporativa ou de estudante posteriormente no portal do Azure, quando criar e associar um recurso de gateway à sua instalação do gateway. Em seguida, você seleciona esse recurso de gateway quando criar a conexão entre seu aplicativo lógico e a fonte de dados local. [Por que eu devo usar uma conta corporativa ou de estudante do Azure AD?](#why-azure-work-school-account)

  > [!TIP]
  > Se você se inscreveu para uma oferta do Office 365 e não forneceu seu email de trabalho real, o endereço de entrada pode ser semelhante a jeff@contoso.onmicrosoft.com. 

* Se você tiver um gateway existente que deseje configurar com um instalador de uma versão anterior à 14.16.6317.4, não poderá alterar o local do gateway executando o instalador do mais recente. No entanto, você pode usar o instalador do mais recente para configurar um novo gateway com o local desejado, em vez disso.
  
  Se você tiver um instalador do gateway anterior à versão 14.16.6317.4, mas ainda não tiver instalado o gateway, poderá baixar e usar o instalador mais recente.

<a name="install-gateway"></a>

## <a name="install-the-data-gateway"></a>Instalar o gateway de dados

1.  [Baixe e execute o instalador do gateway em um computador local](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

2. Examine e aceite os termos de uso e a política de privacidade.

3. Especifique o caminho no computador local em que você deseja instalar o gateway.

4. Quando solicitado, entre com sua conta corporativa ou de estudante do Azure, e não com uma conta da Microsoft.

   ![Entrar com a conta corporativa ou de estudante do Azure](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

5. Agora registre seu gateway instalado com o [serviço de nuvem do gateway](#gateway-cloud-service). Escolha **Registrar um novo gateway neste computador**.

   O serviço de nuvem do gateway criptografa e armazena suas credenciais da fonte de dados e os detalhes do gateway. 
   O serviço também encaminha consultas e seus resultados entre seu aplicativo lógico, o gateway de dados local e sua fonte de dados local.

6. Forneça um nome para sua instalação do gateway. Crie uma chave de recuperação, em seguida, confirme sua chave de recuperação. 

   > [!IMPORTANT] 
   > Sua chave de recuperação deve conter pelo menos oito caracteres. Certifique-se de salvar e guardar a chave em um local seguro. Você também precisa dessa chave quando deseja migrar, restaurar ou assumir um gateway existente.

   1. Para alterar a região padrão para o serviço de nuvem do gateway e o Barramento de Serviço do Azure usados pela sua instalação do gateway, escolha **Alterar Região**.

      ![Alterar região](./media/logic-apps-gateway-install/change-region-gateway-install.png)

      A região padrão é a região associada ao seu locatário do Azure AD.

   2. No próximo painel, abra **Selecionar Região** para escolher uma região diferente.

      ![Selecionar outra região](./media/logic-apps-gateway-install/select-region-gateway-install.png)

      Por exemplo, você pode selecionar a mesma região que seu aplicativo lógico ou selecionar a região mais próxima de sua fonte de dados local para reduzir a latência. Seu aplicativo lógico e recurso de gateway podem ter locais diferentes.

      > [!IMPORTANT]
      > Você não pode alterar essa região após a instalação. Essa região também determina e restringe o local em que você pode criar o recurso do Azure para seu gateway. Assim, quando você cria seu recurso de gateway no Azure, certifique-se de que a localização do recurso corresponda à região selecionada durante a instalação do gateway.
      > 
      > Se você quiser usar uma região diferente do seu gateway mais tarde, deverá configurar um novo gateway.

   3. Quando estiver pronto, escolha **Concluído**.

7. Agora siga estas etapas no portal do Azure para que você possa [criar um recurso do Azure para seu gateway](../logic-apps/logic-apps-gateway-connection.md). 

Saiba mais sobre [como o gateway de dados funciona](#gateway-cloud-service).

## <a name="migrate-restore-or-take-over-an-existing-gateway"></a>Migrar, restaurar ou assumir um gateway existente

Para executar essas tarefas, você deve ter a chave de recuperação que foi especificada quando o gateway foi instalado.

1. No menu Iniciar do computador, escolha **Gateway de dados local**.

2. Depois que o instalador for aberto, entre com a mesma conta corporativa ou de estudante do Azure usada anteriormente para instalar o gateway.

3. Escolha **Migrar, restaurar ou assumir um gateway existente**.

4. Forneça o nome e a chave de recuperação para o gateway que você deseja migrar, restaurar ou controlar.

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Conta de serviço do Windows

O gateway de dados local é executado como um serviço Windows e é configurado para usar `NT SERVICE\PBIEgwService` para as credenciais de logon de serviço Windows. Por padrão, o gateway tem o direito de "Fazer logon como um serviço" para o computador em que você instala o gateway. Para criar e manter o gateway no portal do Azure, a conta de serviço Windows deve ter, pelo menos, permissões de **Colaborador**. 

> [!NOTE]
> A conta de serviço Windows é diferente da conta usada para conexão a fontes de dados locais e da conta do Azure corporativa ou de estudante usada para entrar em serviços de nuvem.

<a name="restart-gateway"></a>

## <a name="restart-the-gateway"></a>Reinicie o gateway

Como qualquer outro serviço Windows, você pode iniciar e parar o serviço de várias maneiras. Por exemplo, você pode abrir um prompt de comando com permissões elevadas no computador em que o gateway está em execução e executar um destes comandos:

* Para parar o serviço, execute este comando:
  
    `net stop PBIEgwService`

* Para iniciar o serviço, execute este comando:
  
    `net start PBIEgwService`

## <a name="configure-a-firewall-or-proxy"></a>Configure um firewall ou proxy

O gateway cria uma conexão de saída com o [Barramento de Serviço do Azure](https://azure.microsoft.com/services/service-bus/). Para fornecer informações de proxy para o gateway, confira [Definir configurações de proxy](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy/).

Para verificar se o seu firewall ou proxy pode bloquear conexões, verifique se seu computador consegue de fato conectar-se à Internet e ao [Barramento de Serviço do Azure](https://azure.microsoft.com/services/service-bus/). Em um prompt do PowerShell, execute este comando:

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> Esse comando apenas testa a conectividade de rede e a conectividade com o Barramento de Serviço do Azure. Portanto, o comando não tem qualquer relação com o gateway nem com o serviço de nuvem do gateway que criptografa e armazena suas credenciais e detalhes do gateway. 
>
> Além disso, esse comando só está disponível no Windows Server 2012 R2 ou posterior e no Windows 8.1 ou posterior. Em versões anteriores do SO, você pode usar o Telnet para testar a conectividade. Saiba mais sobre [Soluções híbridas e de Barramento de Serviço do Azure](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

Os resultados devem ser semelhantes a este exemplo:

```text
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

Se **TcpTestSucceeded** não for definido como **True**, você poderá estar sendo bloqueado por um firewall. Se você quiser ser abrangente, substitua os valores de **ComputerName** e **Port** pelos valores listados em [Configurar portas](#configure-ports) neste tópico.

O firewall também pode bloquear conexões que o Barramento de Serviço do Azure faz com os datacenters do Azure. Se esse cenário ocorrer, aprove (desbloqueie) todos os endereços IP para os datacenters em sua região. Para esses endereços IP, [obtenha a lista de endereços IP do Azure aqui](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="configure-ports"></a>Configure portas

O gateway cria uma conexão de saída para o [Barramento de Serviço do Azure](https://azure.microsoft.com/services/service-bus/) e comunica-se nas portas de saída: TCP 443 (padrão), 5671, 5672, 9350 a 9354. O gateway não exige portas de entrada. Saiba mais sobre [Soluções híbridas e de Barramento de Serviço do Azure](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

| NOMES DE DOMÍNIO | PORTAS DE SAÍDA | DESCRIÇÃO |
| --- | --- | --- |
| *.analysis.windows.net | 443 | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.servicebus.windows.net | 5671-5672 | Advanced Message Queuing Protocol (AMQP) | 
| *.servicebus.windows.net | 443, 9350-9354 | Ouvintes de Retransmissão do Barramento de Serviço por meio de TCP (requer 443 para aquisição de token de Controle de Acesso) | 
| *.frontend.clouddatahub.net | 443 | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| login.microsoftonline.com | 443 | HTTPS | 
| *.msftncsi.com | 443 | Usado para testar a conectividade com a Internet quando o gateway não puder ser acessado pelo serviço do Power BI. | 

Se precisar aprovar endereços IP em vez dos domínios, você poderá baixar e usar a [lista de intervalos de IP de Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Em alguns casos, as conexões de Barramento de Serviço do Azure são feitas com o Endereço IP, em vez de nomes de domínio totalmente qualificados.

<a name="gateway-cloud-service"></a>
## <a name="how-does-the-data-gateway-work"></a>Como funciona o gateway de dados?

O gateway de dados facilita a comunicação rápida e segura entre seu aplicativo lógico, o serviço de nuvem do gateway e sua fonte de dados local. 

![diagram-for-on-premises-data-gateway-flow](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

Portanto, quando o usuário na nuvem interage com um elemento que está conectado à sua fonte de dados local:

1. O serviço de nuvem do gateway cria uma consulta, juntamente com as credenciais criptografadas para a fonte de dados, e envia a consulta à fila para processamento pelo gateway.

2. O serviço de nuvem do gateway analisa a consulta e envia a solicitação por push ao Barramento de Serviço do Azure.

3. O gateway de dados local sonda o Barramento de Serviço do Azure para verificar solicitações pendentes.

4. O gateway obtém a consulta, descriptografa as credenciais e conecta-se à fonte de dados com essas credenciais.

5. O gateway envia a consulta à fonte de dados para execução.

6. Os resultados são enviados da fonte de dados para o gateway e então para o serviço de nuvem. O serviço de nuvem do gateway então usa os resultados.

<a name="faq"></a>
## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="general"></a>Geral

**P**: Preciso ter um gateway para fontes de dados na nuvem, como o SQL Azure? <br/>
**R:** Não. Um gateway conecta-se apenas a fontes de dados locais.

**Pergunta**: O gateway precisa ser instalado no mesmo computador que a fonte de dados? <br/>
**R:** Não. O gateway se conecta à fonte de dados usando as informações de conexão que foram fornecidas. Considere o gateway como um aplicativo de cliente nesse sentido. O gateway precisa apenas da capacidade de se conectar ao nome do servidor que foi fornecido.

<a name="why-azure-work-school-account"></a>

**P**: Por que eu devo usar uma conta corporativa ou de estudante do Azure para entrar? <br/>
**R**: Você pode usar uma conta corporativa ou de estudante do Azure apenas ao instalar o gateway de dados local. Sua conta de entrada é armazenada em um locatário gerenciado pelo Azure AD (Azure Active Directory). Geralmente, o nome UPN da conta do Azure AD corresponde ao endereço de email.

**P**: Em que local minhas credenciais são armazenadas? <br/>
**R**: As credenciais inseridas para uma fonte de dados são criptografadas e armazenadas no serviço de nuvem do gateway. As credenciais são descriptografadas no gateway de dados local.

**P**: Há algum requisito de largura de banda de rede? <br/>
**R**: recomendamos que sua conexão de rede tenha uma boa taxa de transferência. Cada ambiente é diferente, e a quantidade de dados enviados afeta os resultados. Usar o ExpressRoute pode ajudar a assegurar um nível de taxa de transferência entre os datacenters locais e do Azure.
Você pode usar um aplicativo de Teste de Velocidade do Azure de terceiros para avaliar sua taxa de transferência.

**P**: Qual é a latência para execução de consultas para uma fonte de dados do gateway? Qual é a melhor arquitetura? <br/>
**R**: Para reduzir a latência de rede, instale o gateway o mais próximo possível da fonte de dados. Se você puder instalar o gateway na fonte de dados real, essa proximidade minimizará a latência introduzida. Considere também os datacenters. Por exemplo, se o serviço usar o datacenter Oeste dos EUA e você tiver o SQL Server hospedado em uma VM do Azure, a VM do Azure também deverá estar no Oeste dos EUA. Essa proximidade minimiza a latência e evita encargos de saída na VM do Azure.

**P**: Como os resultados são enviados para a nuvem? <br/>
**R**: Os resultados são enviados por meio do Barramento de Serviço do Azure.

**P**: Existem conexões de entrada para o gateway da nuvem? <br/>
**R:** Não. O gateway usa conexões de saída para o Barramento de Serviço do Azure.

**P**: O que acontecerá se eu bloquear conexões de saída? O que preciso abrir? <br/>
**R**: Verifique as portas e os hosts que o gateway usa.

**P**: Qual é o serviço Windows que é de fato chamado?<br/>
**R**: Em Serviços, o gateway é chamado de Serviço de Gateway Corporativo do Power BI.

**P**: O serviço Windows do gateway pode ser executado com uma conta do Azure Active Directory? <br/>
**R:** Não. O serviço do Windows deve ter uma conta válida do Windows. Por padrão, o serviço é executado com o SID de Serviço, NT SERVICE\PBIEgwService.

### <a name="high-availability-and-disaster-recovery"></a>Alta disponibilidade e recuperação de desastres

**P**: Quais opções estão disponíveis para recuperação de desastre? <br/>
**R**: Você pode usar a chave de recuperação para restaurar ou mover um gateway. Ao instalar o gateway, especifique a chave de recuperação.

**P**: Qual é o benefício da chave de recuperação? <br/>
**R**: A chave de recuperação oferece uma maneira de migrar ou recuperar as configurações de gateway após um desastre.

**P**: Há planos para habilitar cenários de alta disponibilidade com o gateway? <br/>
**R**: Esses cenários estão previstos, mas ainda não temos um cronograma.

## <a name="troubleshooting"></a>Solucionar problemas

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**P**: Como posso ver quais consultas estão sendo enviadas à fonte de dados local? <br/>
**R**: Você pode habilitar o rastreamento de consulta, que inclui as consultas que são enviadas. Lembre-se de alterar o rastreamento de consulta de volta para o valor original quando concluir a solução de problemas. Deixar o acompanhamento de consulta ativado cria logs maiores.

Você também pode examinar ferramentas de rastreamento de consultas disponibilizadas por sua fonte de dados. Por exemplo, você pode usar o Extended Events ou o SQL Profiler para SQL Server e Analysis Services.

**P**: Em que local estão os logs do gateway? <br/>
**R**: Consulte Ferramentas mais adiante neste tópico.

### <a name="update-to-the-latest-version"></a>Obtenha a versão mais recente

Muitos problemas podem surgir quando a versão do gateway fica desatualizada. Como prática geral recomendada, use a última versão. Se você não atualiza o gateway há um mês ou mais, convém instalar a versão mais recente do gateway e verificar se o problema pode ser reproduzido.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Erro: falha ao adicionar usuário ao grupo. (-2147463168 PBIEgwService Performance Log Users)

Você poderá obter esse erro se tentar instalar o gateway em um controlador de domínio, o que não tem suporte. Implante o gateway em um computador que não seja um controlador de domínio.

## <a name="tools"></a>Ferramentas

### <a name="collect-logs-from-the-gateway-configurer"></a>Coletar logs do configurador de gateway

É possível coletar vários logs para o gateway. Sempre comece com os logs!

#### <a name="installer-logs"></a>Logs do instalador

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>Logs de configuração

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>Logs de serviço do gateway corporativo

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>Logs de eventos

Você pode encontrar os logs do Gateway de Gerenciamento de Dados e do PowerBIGateway em **Logs de Aplicativos e Serviços**.

### <a name="fiddler-trace"></a>Rastreamento do Fiddler

[Fiddler](http://www.telerik.com/fiddler) é uma ferramenta gratuita da Telerik que monitora o tráfego HTTP. Você pode ver esse tráfego com o serviço do Power BI da máquina cliente. Esse serviço pode mostrar erros e outras informações relacionadas.

## <a name="next-steps"></a>Próximas etapas
    
* [Conectar-se a dados locais de aplicativos lógicos](../logic-apps/logic-apps-gateway-connection.md)
* [Recursos de integração corporativa](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Conectores de Aplicativos Lógicos do Azure](../connectors/apis-list.md)
