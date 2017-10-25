---
title: Gateway de dados local | Microsoft Docs
description: "Um gateway local será necessário se o servidor do Analysis Services no Azure for se conectar a fontes de dados locais."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: cd596155-b608-4a34-935e-e45c95d884a9
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 10/06/2017
ms.author: owend
ms.openlocfilehash: 31e4913aceb1c4b51ddc7cde6381bc21b50187c1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connecting-to-on-premises-data-sources-with-azure-on-premises-data-gateway"></a>Conectar-se a fontes de dados locais com o Gateway de Dados Local do Azure
O gateway de dados local atua como uma ponte, fornecendo transferência de dados segura entre fontes de dados locais e seus servidores do Azure Analysis Services na nuvem. Além de trabalhar com diversos servidores do Azure Analysis Services na mesma região, a versão mais recente do gateway também funciona com os Aplicativos Lógicos do Azure, o Power BI, o Power Apps e o Microsoft Flow. Você pode associar vários serviços na mesma região a um único gateway. 

Instalar o gateway pela primeira vez é um processo de quatro partes:

- **Baixar e executar a instalação** – esta etapa instala um serviço de gateway em um computador em sua organização. Você também pode entrar no Azure usando uma conta no Azure AD do seu [locatário](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant). Não há suporte para contas B2B (convidadas) do Azure.

- **Registrar seu gateway** – nesta etapa, você especifica um nome e uma chave de recuperação para o gateway e seleciona uma região, registrando o gateway no Serviço de Nuvem do Gateway. O recurso de gateway **deve ser registrado na mesma região** que os seus servidores do Analysis Services. 

- **Criar um recurso de gateway no Azure** – nesta etapa, você cria um recurso de gateway em sua assinatura do Azure.

- **Conectar os servidores ao recurso de gateway** – assim que tiver um recurso de gateway em sua assinatura, você pode começar a conectar seus servidores a ele. Você pode conectar vários servidores e outros recursos a ele, desde que estejam na região.

Para começar imediatamente, consulte [Instalar e configurar gateway de dados local](analysis-services-gateway-install.md).

## <a name="how-it-works"> </a>Como funciona
O gateway que você instala em um computador de sua organização é executado como um serviço Windows, o **Gateway de dados local**. Esse serviço local é registrado no Serviço de Nuvem do Gateway por meio do Barramento de Serviço do Azure. Em seguida, você cria um Serviço de Nuvem do Gateway do recurso de gateway para sua assinatura do Azure. Seus servidores do Azure Analysis Services são, então, conectados ao recurso de gateway. Quando modelos em seu servidor precisarem se conectar às fontes de dados locais para consultas ou processamento, um fluxo de dados e consultas atravessará o recurso de gateway, o Barramento de Serviço do Azure, o serviço de gateway de dados local e suas fontes de dados. 

![Como ele funciona](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Fluxo de dados e consultas:

1. Uma consulta é criada pelo serviço de nuvem com as credenciais criptografadas para a fonte de dados local. Ela é enviada para uma fila de processamento do gateway.
2. O serviço de nuvem do gateway analisa a consulta e envia a solicitação para o [Barramento de Serviço do Azure](https://azure.microsoft.com/documentation/services/service-bus/).
3. O gateway de dados local sonda o Barramento de Serviço do Azure para verificar solicitações pendentes.
4. O gateway obtém a consulta, descriptografa as credenciais e conecta-se às fontes de dados com essas credenciais.
5. O gateway envia a consulta à fonte de dados para execução.
6. Os resultados são enviados da fonte de dados de volta para o gateway e, em seguida, para o serviço de nuvem e seu servidor.

## <a name="windows-service-account"> </a>Conta de Serviço Windows
O gateway de dados local é configurado para usar *NT SERVICE\PBIEgwService* para as credenciais de logon de serviço do Windows. Por padrão, ele tem o direito de Fazer logon como um serviço; no contexto da máquina na qual você está instalando o gateway. Essa credencial não é da mesma conta usada para se conectar às fontes de dados locais ou da sua conta do Azure.  

Se você encontrar problemas com o servidor proxy devido à autenticação, convém alterar a conta de serviço do Windows para um usuário de domínio ou conta de serviço gerenciado.

## <a name="ports"> </a>Portas
O gateway cria uma conexão de saída para o Barramento de Serviço do Azure. Ele se comunica nas portas de saída: TCP 443 (padrão), 5671, 5672, 9350 a 9354.  O gateway não exige portas de entrada.

Recomendamos a inclusão dos endereços IP em uma lista de permissões para a região de dados em seu firewall. Baixe a [lista de IPs de Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). A lista é atualizada semanalmente.

> [!NOTE]
> Os Endereços IP listados na lista de IP de Datacenter do Azure estão em notação CIDR. Por exemplo, 10.0.0.0/24 significa 10.0.0.0 a 10.0.0.24. Saiba mais sobre a [notação CIDR ](http://whatismyipaddress.com/cidr).
>
>

Veja a seguir os nomes de domínio totalmente qualificados usados pelo gateway.

| Nomes de domínio | Portas de saída | Descrição |
| --- | --- | --- |
| *.powerbi.com |80 |HTTP usado para baixar o instalador. |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *.login.windows.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Ouvintes de Retransmissão do Barramento de Serviço por meio de TCP (requer 443 para aquisição de token de Controle de Acesso) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Usado para testar a conectividade com a Internet se o gateway não poder ser acessado pelo serviço do Power BI. |
| *.microsoftonline p.com |443 |Usado para autenticação, dependendo da configuração. |

### <a name="force-https"></a>Forçar comunicação HTTPS com o Barramento de Serviço do Azure
Você pode forçar o gateway para se comunicar com o Barramento de Serviço do Azure usando HTTPS em vez de TCP direto; no entanto, fazer isso pode reduzir consideravelmente o desempenho. Você pode modificar o arquivo *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* alterando o valor de `AutoDetect` para `Https`. Normalmente, esse arquivo fica localizado em *C:\Arquivos de Programas\Gateway de dados local*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="faq"></a>Perguntas frequentes

### <a name="general"></a>Geral

**P**: Preciso de um gateway para fontes de dados na nuvem, como o Banco de Dados SQL do Azure? <br/>
**R:** Não. Um gateway é necessário para se conectar a fontes de dados locais apenas.

**Pergunta**: O gateway precisa ser instalado no mesmo computador que a fonte de dados? <br/>
**R:** Não. O gateway precisa apenas da capacidade de se conectar ao servidor, geralmente na mesma rede.

<a name="why-azure-work-school-account"></a>

**P**: Por que eu preciso usar uma conta corporativa ou de estudante para entrar? <br/>
**R**: Você apenas pode usar uma conta corporativa ou de estudante ao instalar o gateway de dados local. E essa conta deve estar no mesmo locatário que a assinatura na qual você está configurando o recurso de gateway. Sua conta de entrada é armazenada em um locatário gerenciado pelo Azure AD (Azure Active Directory). Geralmente, o nome UPN da conta do Azure AD corresponde ao endereço de email.

**P**: Em que local minhas credenciais são armazenadas? <br/>
**R**: As credenciais inseridas para uma fonte de dados são criptografadas e armazenadas no Serviço de Nuvem do Gateway. As credenciais são descriptografadas no gateway de dados local.

**P**: Há algum requisito de largura de banda de rede? <br/>
**R**: Recomendamos que sua conexão de rede tenha uma boa taxa de transferência. Cada ambiente é diferente, e a quantidade de dados enviados afeta os resultados. Usar o ExpressRoute pode ajudar a assegurar um nível de taxa de transferência entre os datacenters locais e do Azure.
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
**R**: Em Serviços, o gateway é chamado de Serviço de gateway de dados local.

**P**: O serviço Windows do gateway pode ser executado com uma conta do Azure Active Directory? <br/>
**R:** Não. O serviço do Windows deve ter uma conta válida do Windows. Por padrão, o serviço é executado com o SID de Serviço, NT SERVICE\PBIEgwService.

### <a name="high-availability"></a>Alta disponibilidade e recuperação de desastres

**P**: Quais opções estão disponíveis para recuperação de desastre? <br/>
**R**: Você pode usar a chave de recuperação para restaurar ou mover um gateway. Ao instalar o gateway, especifique a chave de recuperação.

**P**: Qual é o benefício da chave de recuperação? <br/>
**R**: A chave de recuperação oferece uma maneira de migrar ou recuperar as configurações de gateway após um desastre.

## <a name="troubleshooting"> </a>Solução de problemas

**P**: Como posso ver quais consultas estão sendo enviadas à fonte de dados local? <br/>
**R**: Você pode habilitar o rastreamento de consulta, que inclui as consultas que são enviadas. Lembre-se de alterar o rastreamento de consulta de volta para o valor original quando concluir a solução de problemas. Deixar o acompanhamento de consulta ativado cria logs maiores.

Você também pode examinar ferramentas de rastreamento de consultas disponibilizadas por sua fonte de dados. Por exemplo, você pode usar o Extended Events ou o SQL Profiler para SQL Server e Analysis Services.

**P**: Em que local estão os logs do gateway? <br/>
**R**: Veja Logs mais adiante neste artigo.

### <a name="update"></a>Atualização para a versão mais recente

Muitos problemas podem surgir quando a versão do gateway fica desatualizada. Como prática geral recomendada, use a última versão. Se você não atualiza o gateway há um mês ou mais, convém instalar a versão mais recente do gateway e verificar se o problema pode ser reproduzido.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Erro: falha ao adicionar usuário ao grupo. (-2147463168 PBIEgwService Performance Log Users)

Você poderá obter esse erro se tentar instalar o gateway em um controlador de domínio, o que não tem suporte. Implante o gateway em um computador que não seja um controlador de domínio.

## <a name="logs"></a>Logs

Arquivos de log são um recurso importante ao solucionar problemas.

#### <a name="enterprise-gateway-service-logs"></a>Logs de serviço do gateway corporativo

`C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\<yyyyymmdd>.<Number>.log`

#### <a name="configuration-logs"></a>Logs de configuração

`C:\Users\<username>\AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator.log`




#### <a name="event-logs"></a>Logs de eventos

Você pode encontrar os logs do Gateway de Gerenciamento de Dados e do PowerBIGateway em **Logs de Aplicativos e Serviços**.


## <a name="telemetry"></a>Telemetria
Telemetria pode ser usada para monitorar e solucionar problemas. Por padrão

**Para ativar a telemetria**

1.  Verifique o diretório do cliente de gateway de dados local no computador. Normalmente, é **%systemdrive%\Program Files\On-premises data gateway**. Ou, você pode abrir um console de Serviços e verificar o Caminho para o executável: uma propriedade do serviço do gateway de dados local.
2.  No arquivo Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config do diretório do cliente. Altere a configuração SendTelemetry para true.
        
    ```
        <setting name="SendTelemetry" serializeAs="String">
                    <value>true</value>
        </setting>
    ```

3.  Salve suas alterações e reinicie o serviço do Windows: serviço do gateway de dados local.




## <a name="next-steps"></a>Próximas etapas
* [Instalar e configurar um gateway de dados local](analysis-services-gateway-install.md).   
* [Gerenciar o Analysis Services](analysis-services-manage.md)
* [Obter dados do Azure Analysis Services](analysis-services-connect.md)
