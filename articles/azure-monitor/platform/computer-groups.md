---
title: Grupos de computadores nas consultas de log do Azure Monitor | Microsoft Docs
description: Os grupos de computadores no Azure Monitor permitem que você analise as consultas de log em um determinado conjunto de computadores.  Este artigo descreve os diferentes métodos que podem ser utilizados para criar grupos de computadores e como usá-los em uma consulta de log.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: bwren
ms.openlocfilehash: c2babb5a86d69881b6a76c6dceae80a24a891f6c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60740897"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Grupos de computadores em consultas de log do Azure Monitor
Os grupos de computadores no Azure Monitor permitem que você analise as [consultas de log](../log-query/log-query-overview.md) de um determinado conjunto de computadores.  Cada grupo é preenchido com computadores usando uma consulta que você define ou importando grupos de fontes diferentes.  Quando o grupo é incluído em uma consulta de log, os resultados são limitados aos registros que correspondem aos computadores no grupo.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>Criando um grupo de computadores
É possível criar um grupo de computadores no Azure Monitor usando qualquer um dos métodos da tabela a seguir.  Detalhes sobre cada método são fornecidos nas seções a seguir. 

| Método | DESCRIÇÃO |
|:--- |:--- |
| Consulta de log |Crie uma consulta de log que retorne uma lista de computadores. |
| API da Pesquisa de Log |Use a API da Pesquisa de Logs para criar programaticamente um grupo de computadores com base nos resultados de uma consulta de log. |
| Active Directory |Examine automaticamente a associação de grupo de qualquer computador de agente que seja membro de um domínio do Active Directory e crie um grupo no Azure Monitor para cada grupo de segurança. (computadores Windows, somente)|
| Gerenciador de Configurações | Importe coleções do System Center Configuration Manager e crie um grupo no Azure Monitor para cada uma. |
| Windows Server Update Services |Examine automaticamente clientes ou servidores do WSUS para grupos de direcionamento e crie um grupo no Azure Monitor para cada um. |

### <a name="log-query"></a>Consulta de log
Grupos de computadores criados por meio de uma consulta de log contêm todos os computadores retornados por uma consulta que você define.  Essa consulta é executada sempre que o grupo de computadores é utilizado para que qualquer alteração desde que o grupo foi criado seja refletida.  

Você pode usar qualquer consulta de um grupo de computadores, mas ela deve retornar um conjunto distinto de computadores usando `distinct Computer`.  A seguir, uma consulta de exemplo típica que poderia ser usada como um grupo de computadores.

    Heartbeat | where Computer contains "srv" | distinct Computer

Use o procedimento a seguir para criar um grupo de computadores de uma pesquisa de logs no portal do Azure.

1. Clique em **Logs** no menu **Azure Monitor** no portal do Azure.
1. Crie e execute uma consulta que retorne os computadores que você quer no grupo.
1. Clique em **Salvar** na parte superior da tela.
1. Altere **Salvar como** para **Função** e selecione **Salvar essa consulta como um grupo de computadores**.
1. Forneça valores para cada propriedade para o grupo de computadores descrito na tabela e clique em **Salvar**.

A tabela a seguir descreve as propriedades que definem um grupo de computadores.

| Propriedade | Descrição |
|:---|:---|
| NOME   | Nome da consulta a ser exibida no portal. |
| Alias da função | Um alias exclusivo usado para identificar o grupo de computadores em uma consulta. |
| Categoria       | Categoria para organizar as consultas no portal. |


### <a name="active-directory"></a>Active Directory
Ao configurar o Azure Monitor para importar associações de grupos do Active Directory, ele analisa a associação de grupo de qualquer computador ingressado no domínio do Windows com o agente do Log Analytics.  Um grupo de computadores é criado no Azure Monitor para cada grupo de segurança no Active Directory e cada computador Windows é adicionado aos grupos de computadores correspondentes aos grupos de segurança dos quais são membros.  Essa associação é atualizada continuamente a cada 4 horas.  

> [!NOTE]
> Os grupos importados do Active Directory contêm apenas computadores Windows.

Você configura o Azure Monitor para importar grupos de segurança do Active Directory a partir das **Configurações avançadas** no espaço de trabalho do Log Analytics no portal do Azure.  Selecione **Grupos de Computadores**, **Active Directory** e **Importe as associações de grupo do Active Directory dos computadores**.  Não é necessária nenhuma configuração.

![Grupos de computadores do Active Directory](media/computer-groups/configure-activedirectory.png)

Quando os grupos são importados, o menu lista o número de computadores com a associação de grupo detectada e o número de grupos importados.  Você pode clicar em qualquer um desses links para retornar os registros de **ComputerGroup** com essas informações.

### <a name="windows-server-update-service"></a>Serviços de Atualização do Windows Server
Ao configurar o Azure Monitor para importar associações de grupos do WSUS, ele analisa a associação de grupo de direcionamento de qualquer computador com o agente do Log Analytics.  Se você estiver usando direcionamento no lado do cliente, qualquer computador que estiver conectado ao Azure Monitor e fizer parte de qualquer grupo de direcionamento do WSUS terá as associações de grupo importadas para o Azure Monitore. Se você estiver usando direcionamento no lado do servidor, o agente do Log Analytics deverá ser instalado no servidor do WSUS para que as informações de associação de grupo sejam importadas para o Azure Monitor.  Essa associação é atualizada continuamente a cada 4 horas. 

Você configura o Azure Monitor para importar grupos do WSUS a partir de **Configurações avançadas** no espaço de trabalho do Log Analytics no portal do Azure.  Selecione **Grupos de Computadores**, **WSUS** e, em seguida, **Importar associações de grupo do WSUS**.  Não é necessária nenhuma configuração.

![Grupos de computadores do WSUS](media/computer-groups/configure-wsus.png)

Quando os grupos são importados, o menu lista o número de computadores com a associação de grupo detectada e o número de grupos importados.  Você pode clicar em qualquer um desses links para retornar os registros de **ComputerGroup** com essas informações.

### <a name="system-center-configuration-manager"></a>System Center Configuration Manager
Quando você configura o Azure Monitor para importar associações de coleção do Configuration Manager, ele cria um grupo de computadores para cada coleção.  As informações de associação de coleção são recuperadas a cada 3 horas para manter os grupos de computadores atualizados. 

Antes de importar as coleções do Gerenciador de Configurações, será necessário [conectar o Configuration Manager ao Azure Monitor](collect-sccm.md).  Em seguida, você poderá configurar a importação a partir das **Configurações avançadas** no espaço de trabalho do Log Analytics no portal do Azure.  Selecione **Grupos de Computadores**, **SCCM** e, em seguida, **Importar associações de coleção do Configuration Manager**.  Não é necessária nenhuma configuração.

![Grupos de computadores do SCCM](media/computer-groups/configure-sccm.png)

Quando as coleções tiverem sido importadas, o menu listará o número de computadores com a associação de grupo detectada e o número de grupos importados.  Você pode clicar em qualquer um desses links para retornar os registros de **ComputerGroup** com essas informações.

## <a name="managing-computer-groups"></a>Gerenciando grupos de computadores
É possível exibir grupos de computadores que foram criados a partir de uma consulta de log ou API de Pesquisa de Logs pelas **Configurações avançadas** no espaço de trabalho do Log Analytics no portal do Azure.  Selecione **Grupos de Computadores** e então **Grupos Salvos**.  

Clique no **x** na coluna **Remover** para excluir o grupo de computadores.  Clique no ícone **Exibir membros** para que um grupo execute a pesquisa de log do grupo que retorna seus membros.  Não é possível modificar um grupo de computadores, assim, em vez disso, você precisar excluir e recriar o grupo com as configurações modificadas.

![Grupos de computadores salvados](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>Usar um grupo de computadores em uma consulta de log
Você usa um grupo de computadores criado a partir de uma consulta de log em uma consulta, tratando o alias como uma função, geralmente com a seguinte sintaxe:

  `Table | where Computer in (ComputerGroup)`

Por exemplo, você pode usar o seguinte para retornar registros de UpdateSummary somente para os computadores em um grupo de computadores chamado mycomputergroup.
 
  `UpdateSummary | where Computer in (mycomputergroup)`


Os grupos de computadores importados e seus computadores incluídos estão armazenados na tabela **ComputerGroup**.  Por exemplo, a seguinte consulta retornaria uma lista de computadores no grupo de Computadores do domínio do Active Directory. 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

A consulta a seguir retornaria registros UpdateSummary apenas para computadores em Computadores de domínio.

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```




## <a name="computer-group-records"></a>Registros de grupo de computadores
Um registro é criado no espaço de trabalho do Log Analytics para cada associação do grupo do computadores criada no Active Directory ou no WSUS.  Esses registros de desempenho têm um tipo de **ComputerGroup** e têm as propriedades na tabela a seguir.  Os registros não são criados para grupos de computadores com base em consultas de log.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| `Type` |*ComputerGroup* |
| `SourceSystem` |*SourceSystem* |
| `Computer` |Nome do computador membro. |
| `Group` |Nome do grupo. |
| `GroupFullName` |Caminho completo para o grupo, incluindo a fonte e o nome da fonte. |
| `GroupSource` |Fonte da qual o grupo foi coletado. <br><br>Active Directory<br>WSUS<br>WSUSClientTargeting |
| `GroupSourceName` |Nome da origem da qual o grupo foi coletado.  Para o Active Directory, este é o nome de domínio. |
| `ManagementGroupName` |Nome do grupo de gerenciamento de agentes do SCOM.  Para outros agentes, ele é AOI-\<ID do espaço de trabalho\> |
| `TimeGenerated` |Data e hora em que o grupo de computadores foi criado ou atualizado. |

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [registrar consultas](../log-query/log-query-overview.md) para analisar os dados coletados de fontes de dados e soluções.  

