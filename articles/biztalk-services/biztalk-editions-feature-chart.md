---
title: "Saiba mais sobre recursos das edições dos Serviços BizTalk | Microsoft Docs"
description: "Compare os recursos das edições dos Serviços BizTalk: Free, Developer, Basic, Standard e Premium. MABS, WABS."
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: c589629f-06b1-44bb-b8ca-1db71826ea59
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 718b57a801a9ba62a0154ae42da2ac0c0741f203
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="biztalk-services-editions-chart"></a>Serviços BizTalk: gráfico de edições

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Os Serviços BizTalk do Azure oferecem várias edições. Use este tópico para determinar qual edição é adequada para seu cenário e suas necessidades comerciais.

## <a name="compare-the-editions"></a>Comparar edições
**Free (Visualização)**

É possível criar e gerenciar Conexões Híbridas. Uma Conexão Híbrida é uma maneira fácil de conectar um Site do Azure a um sistema local, como o SQL Server.

**Desenvolvedores**

Inclui Conexões Híbridas, processamento de mensagens EAI & EDI com um portal de gerenciamento de parceiro comercial fácil de usar e dá suporte a esquemas EDI comuns e a processamento sofisticado de EDI por x12 e AS2. Pode criar cenários EAI comuns conectando serviços na nuvem com qualquer protocolo HTTP/S, REST, FTP, WCF e SFTP para ler e escrever mensagens.  Utilize a conectividade com sistemas LOB locais com adaptadores SAP, Oracle eBusiness, Oracle DB, Siebel e SQL Server prontos para uso. Use um ambiente centralizado no desenvolvedor com ferramentas do Visual Studio para permitir desenvolvimento e implantação fáceis. Limitado para fins de desenvolvimento e teste apenas sem nenhum Contrato de Nível de Serviço (SLA).

**Básico**

Inclui a maioria dos recursos do Developer com aumentos em Conexões Híbridas, pontes EAI, Contratos EDI, e conexões do BizTalk Adapter Pack. Também oferece alta disponibilidade, e a opção de dimensionar com um Contrato de Nível de Serviço (SLA).

**Standard**

Inclui todos os recursos do Basic com aumentos em Conexões Híbridas, pontes EAI, Contratos EDI, e conexões do BizTalk Adapter Pack. Também oferece alta disponibilidade, e a opção de dimensionar com um Contrato de Nível de Serviço (SLA).

**Premium**

Inclui todos os recursos do Standard com aumentos em Conexões Híbridas, pontes EAI, Contratos EDI, e conexões do BizTalk Adapter Pack. Também inclui arquivamento, alta disponibilidade, e a opção de dimensionar com um Contrato de Nível de Serviço (SLA).

## <a name="editions-chart"></a>Tabela de edições
A tabela a seguir lista as diferenças.

<table border="1">
<tr bgcolor="FAF9F9">
        <th></th>
        <th>Free (Visualização)</th>
        <th>Desenvolvedores</th>
        <th>Básico</th>
        <th>Standard</th>
        <th>Premium</th>
</tr>

<tr>
<td><strong>Preço inicial</strong></td>
<td colspan="5"><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Preço dos Serviços BizTalk do Azure</a> <br/><br/> <a HREF="http://azure.microsoft.com/pricing/calculator/?scenario=full"> Calculadora de preços do Azure</a></td>
</tr>
<tr>
<td><strong>Configuração mínima padrão</strong></td>
<td>1 Unidade Free</td>
<td>1 unidade Developer</td>
<td>1 unidade Basic</td>
<td>1 unidade Standard</td>
<td>1 unidade Premium</td>
</tr>
<tr>
<td><strong>Escala</strong></td>
<td>Sem escala</td>
<td>Sem escala</td>
<td>Sim, em incrementos de 1 unidade Basic</td>
<td>Sim, em incrementos de 1 unidade Standard</td>
<td>Sim, em incrementos de 1 unidade Premium</td>
</tr>
<tr>
<td><strong>Escala máxima permitida</strong></td>
<td>Sem escala</td>
<td>Sem escala</td>
<td>Até 8 unidades</td>
<td>Até 8 unidades</td>
<td>Até 8 unidades</td>
</tr>
<tr>
<td><strong>Pontos EAI por unidade</strong></td>
<td>Não incluso</td>
<td>25</td>
<td>25</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDI, AS2</strong>
<br/><br/>
Inclui Contratos TPM</td>
<td>Não incluso</td>
<td>Incluso. 10 contratos por unidade.</td>
<td>Incluso. 50 contratos por unidade.</td>
<td>Incluso. 250 contratos por unidade.</td>
<td>Incluso. 1000 contratos por unidade.</td>
</tr>
<tr>
<td><strong>Conexões híbridas por unidade</strong></td>
<td>5</td>
<td>5</td>
<td>10</td>
<td>50</td>
<td>100</td>
</tr>
<tr>
<td><strong>Transferência de dados por conexões híbridas (GB) por unidade</strong></td>
<td>5</td>
<td>5</td>
<td>50</td>
<td>250</td>
<td>500</td>
</tr>
<tr>
<td><strong>Conexões do Serviço do Adaptador do BizTalk para sistemas de LOB locais</strong></td>
<td>Não incluso</td>
<td>1 conexão</td>
<td>2 conexões</td>
<td>5 conexões</td>
<td>25 conexões</td>
</tr>
<tr>
<td align="left"><strong>Protocolos/sistemas com suporte:</strong>
<ul>
<li>http</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>Service Bus (SB)</li>
<li>Blob do Azure</li>
<li>APIs REST</li>
</ul>
</td>
<td>Não incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Alta disponibilidade</strong>
<br/><br/>
Para obter o Contrato de Nível de Serviço (SLA), consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Preços dos Serviços BizTalk do Azure</a>.
</td>
<td>Não incluso</td>
<td>Não incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Backup e restauração</strong></td>
<td>Não incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Acompanhamento</strong></td>
<td>Não incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Arquivamento</strong><br/><br/>
Inclui NRR (não repúdio de recebimento) e o download de mensagens rastreadas</td>
<td>Não incluso</td>
<td>Incluso</td>
<td>Não incluso</td>
<td>Não incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Uso de código personalizado</strong></td>
<td>Não incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Uso de transformações, incluindo XSLT personalizado</strong></td>
<td>Não incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
</table>

> [!NOTE]
> Para resiliência contra falhas de hardware, a Alta Disponibilidade implica em ter várias VMs dentro de uma única Unidade do BizTalk.
> 
> 

## <a name="faqs"></a>Perguntas frequentes
#### <a name="what-is-a-biztalk-unit"></a>O que é uma Unidade do BizTalk?
Uma “unidade” é o nível atômico de uma implantação dos Serviços BizTalk do Azure. Cada edição é fornecida com uma unidade que tem capacidade e memória de computação diferentes. Por exemplo, uma Unidade Básica tem mais computação do que a Developer, a Standard tem mais computação do que a Basic e assim por diante. Quando você dimensiona um Serviço BizTalk, você o faz em termos de Unidades.

#### <a name="what-is-the-difference-between-biztalk-services-and-azure-biztalk-vm"></a>Qual é a diferença entre os Serviços BizTalk e a VM BizTalk do Azure?
Os Serviços BizTalk fornecem uma arquitetura de PaaS (Plataforma como Serviço) verdadeira para criar soluções de integração na nuvem. Com o modelo de PaaS, você focaliza completamente na lógica do aplicativo e deixa toda a infraestrutura de gerenciamento para a Microsoft, incluindo:

* Sem necessidade de gerenciar ou aplicar patches em máquinas virtuais
* A Microsoft garante a disponibilidade
* Você controla a escala sob demanda simplesmente solicitando mais ou menos capacidade por meio do Portal de Gerenciamento do Azure

O BizTalk Server em Máquinas Virtuais do Azure fornece uma arquitetura de IaaS (Infraestrutura como Serviço). Você cria máquinas virtuais e as configura exatamente como seu ambiente local, facilitando a execução dos aplicativos existentes na nuvem sem alterações no código. Com a IaaS, você ainda é responsável por configurar as máquinas virtuais, gerenciá-las (por exemplo, instalando software e patches do sistema operacional) e pela criação da arquitetura do aplicativo para alta disponibilidade.

Se você estiver planejando criar novas soluções de integração que minimizem seu esforço de gerenciamento da infraestrutura, use os Serviços BizTalk. Se estiver planejando migrar rapidamente suas soluções existentes do BizTalk ou procurando um ambiente sob demanda para desenvolver e testar aplicativos do BizTalk Server, use o BizTalk Server em uma Máquina Virtual do Azure.

#### <a name="what-is-the-difference-between-biztalk-adapter-service-and-hybrid-connections"></a>Qual é a diferença entre o Serviço do Adaptador do BizTalk e as Conexões Híbridas?
O Serviço do Adaptador do BizTalk é usado por um Serviço do BizTalk do Azure. O Serviço do Adaptador do BizTalk usa o BizTalk Adapter Pack para conectar-se a um sistema de Linha de Negócios (LOB) local. Uma conexão híbrida permite uma conexão fácil e conveniente de aplicativos do Azure, como os Aplicativos Web no Serviço de Aplicativo do Azure e nos Serviços Móveis do Azure, a um recurso local.

#### <a name="what-does-hybrid-connection-data-transfer-gb-per-unit-mean-is-this-per-minutehourdayweekmonth-what-happens-when-the-limit-is-reached"></a>O que significa “Transferência de dados por conexões híbridas (GB) por unidade”? Isso é por minuto/hora/dia/semana/mês? O que acontece quando o limite é atingido?
O custo de Conexão Híbrida por unidade dependerá da edição de Serviços BizTalk. Resumindo, o custo depende de quantos dados você transfere. Por exemplo, a transferência de 10 GB de dados diariamente custa menos do que a transferência de 100 GB diariamente. Use a [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/?scenario=full) para os Serviços BizTalk para determinar os custos específicos. Normalmente, os limites são impostos diariamente. Se você exceder o limite, qualquer excedente será cobrado na taxa de US$1 por GB.

#### <a name="when-i-create-an-agreement-in-biztalk-services-why-does-the-number-of-bridges-go-up-by-two-instead-of-just-one"></a>Quando crio um contrato nos Serviços do BizTalk, por que o número de pontes é aumentado em dois, em vez de apenas um?
Cada contrato é composto de duas pontes diferentes, uma ponte de comunicação do lado do envio e outra do lado do recebimento.

#### <a name="what-happens-when-i-hit-the-quota-limit-on-the-number-of-bridges-or-agreements"></a>O que acontecerá quando eu atingir o limite de cota do número de pontes ou contratos?
Você não poderá implantar uma ponte nova ou criar novos contratos. Para implantar mais, será necessário escalar verticalmente para mais unidades do Serviço BizTalk ou atualizar para uma Edição superior.

#### <a name="how-do-i-migrate-from-one-tier-of-biztalk-services-to-another"></a>Como faço para migrar de uma camada dos Serviços do BizTalk para outra?
A edição gratuita não pode ser migrada ou 'expandida' para outra camada e não pode ser submetida a backup e restaurada para outra camada. Se você precisar de outra camada, crie um novo Serviço BizTalk usando a nova camada. Quaisquer artefatos criados usando a edição Gratuita, incluindo conexões híbridas, precisam ser recriados no novo Serviço BizTalk. 

Para as edições restantes, use o backup e a restauração para migrar os artefatos de uma camada para outra. Por exemplo, faça backup dos artefatos na camada Standard e, em seguida, restaure-os para a camada Premium. [Serviços BizTalk: Backup e Restauração](biztalk-backup-restore.md) descreve os caminhos de migração com suporte e lista de quais artefatos é feito backup. Observe que não é feito backup das Conexões Híbridas. Depois de fazer backup e realizar a restauração em uma nova camada, recrie as conexões híbridas.  

#### <a name="is-the-biztalk-adapter-service-included-in-the-service-how-do-i-receive-the-software"></a>O Serviço do Adaptador do BizTalk está incluído no serviço? Como recebo o software?
Sim, o Serviço do Adaptador do BizTalk com o BizTalk Adapter Pack são incluídos com o SDK dos Serviços BizTalk do Azure [download](http://www.microsoft.com/download/details.aspx?id=39087).

## <a name="next-steps"></a>Próximas etapas
Para criar os Serviços BizTalk do Azure no Portal de Gerenciamento do Azure, consulte [Serviços BizTalk: provisionamento com o Portal do Azure](biztalk-provision-services.md). Para começar a criar aplicativos, visite [Serviços BizTalk do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="additional-resources"></a>Recursos adicionais
* [Serviços BizTalk: provisionamento com o Portal do Azure](biztalk-provision-services.md)<br/>
* [Serviços BizTalk: gráfico do status do provisionamento](biztalk-service-state-chart.md)<br/>
* [Serviços BizTalk: guias Painel, Monitor e Escala](biztalk-dashboard-monitor-scale-tabs.md)<br/>
* [BizTalk Services: Backup and restore](biztalk-backup-restore.md)<br/>
* [Serviços BizTalk: limitação](biztalk-throttling-thresholds.md)<br/>
* [Serviços BizTalk: nome e chave do emissor](biztalk-issuer-name-issuer-key.md)<br/>
* [Como começar a usar o SDK dos Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>

