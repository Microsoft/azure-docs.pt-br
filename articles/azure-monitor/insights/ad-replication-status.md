---
title: Monitorar Active Directory status de replicação
description: O pacote de solução de Status de Replicação do Active Directory monitora regularmente seu ambiente do Active Directory em busca de falhas de replicação.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/24/2018
ms.openlocfilehash: c99ad16e119c4262aa6d9d645b2457cdd46061b2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700670"
---
# <a name="monitor-active-directory-replication-status-with-azure-monitor"></a>Monitorar o Status de Replicação do Active Directory com o Azure Monitor

![Símbolo do Status de Replicação do AD](./media/ad-replication-status/ad-replication-status-symbol.png)

O Active Directory é um componente-chave de um ambiente de TI corporativo. Para garantir a alta disponibilidade e alto desempenho, cada controlador de domínio tem sua própria cópia do banco de dados do Active Directory. Controladores de domínio replicam entre si para propagar alterações em toda a empresa. Falhas nesse processo de replicação podem causar vários problemas em toda a empresa.

A solução Status de Replicação do AD monitora regularmente seu ambiente de Active Directory em busca de falhas de replicação.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand-solution.md)]

## <a name="installing-and-configuring-the-solution"></a>Instalando e configurando a solução
Use as informações a seguir para instalar e configurar a solução.

### <a name="prerequisites"></a>Pré-requisitos

* A solução Status de Replicação do AD requer uma versão com suporte do .NET Framework 4.6.2 ou superior instalada em cada computador que tenha o agente do Log Analytics para Windows (também conhecido como Microsoft Monitoring Agent (MMA)) instalado.  O agente é usado pelo System Center 2016 – Operations Manager, pelo Operations Manager 2012 R2 e pelo Azure Monitor.
* A solução oferece suporte a controladores de domínio que executam o Windows Server 2008 e 2008 R2, o Windows Server 2012 e 2012 R2 e o Windows Server 2016.
* Um espaço de trabalho do Log Analytics para adicionar a solução de Verificação de Integridade do Active Directory no Azure marketplace, no Portal do Azure. Não é necessário realizar uma configuração adicional.


### <a name="install-agents-on-domain-controllers"></a>Instalar agentes em controladores de domínio
Você deve instalar os agentes em controladores de domínio membros do domínio a ser avaliado. Ou você precisa instalar os agentes em servidores membros e configurar os agentes para enviar dados de replicação do AD para o Azure Monitor. Para saber como conectar computadores Windows ao Azure Monitor, confira [Conectar computadores Windows ao Azure Monitor](../agents/agent-windows.md). Se o controlador de domínio já fizer parte de um ambiente existente do System Center Operations Manager que você quer conectar ao Azure Monitor, confira [Conectar o Operations Manager ao Azure Monitor](../agents/om-agents.md).

### <a name="enable-non-domain-controller"></a>Habilitar o controlador fora do domínio
Se não quiser conectar nenhum de seus controladores de domínio diretamente ao Azure Monitor, você poderá usar qualquer outro computador do domínio conectado ao Azure Monitor para coletar dados para o pacote de solução de Status de Replicação do AD e fazê-lo enviar os dados.

1. Verifique se o computador é um membro do domínio que você deseja monitorar usando a solução de Status de Replicação do AD.
2. [Conecte o computador Windows ao Azure Monitor](../agents/om-agents.md) ou [conecte-o usando seu ambiente existente do Operations Manager para Azure Monitor](../agents/om-agents.md) se ele ainda não estiver conectado.
3. Nesse computador, defina a seguinte chave do Registro:<br>Chave: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management Groups\<ManagementGroupName> \Solutions\ADReplication**<br>Valor: **IsTarget**<br>Dados do Valor: **true**

   > [!NOTE]
   > Essas alterações não entram em vigor até que você reinicie o serviço de Microsoft Monitoring Agent (HealthService.exe).
   > ### <a name="install-solution"></a>Instalar a solução
   > Siga o processo descrito em [Instalar uma solução de monitoramento](solutions.md#install-a-monitoring-solution) para adicionar o **Status de Replicação do Active Directory** ao espaço de trabalho do Log Analytics. Não é necessária nenhuma configuração.


## <a name="ad-replication-status-data-collection-details"></a>Detalhes de coleta de dados do Status de Replicação do AD
A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados para o Status de Replicação de AD.

| plataforma | Agente direto | Agente SCOM | Armazenamento do Azure | SCOM necessário? | Os dados do agente SCOM enviados por meio do grupo de gerenciamento | frequência de coleta |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |a cada cinco dias |



## <a name="understanding-replication-errors"></a>Entendendo erros de replicação

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

O bloco Status de Replicação do AD exibe quantos erros de replicação você tem no momento. **Erros Críticos de Replicação** são aqueles erros iguais ou superiores a 75% do [tempo de vida da marca de exclusão](/previous-versions/windows/it-pro/windows-server-2003/cc784932(v=ws.10)) para sua floresta do Active Directory.

![Bloco do Status de Replicação do AD](./media/ad-replication-status/oms-ad-replication-tile.png)

Ao clicar no bloco, você poderá ver mais informações sobre os erros.
![Painel Status de Replicação do AD](./media/ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>Status do Servidor de Destino e Status do Servidor de Origem
Essas colunas mostram o status dos servidores de destino e servidores de origem que estão com erros de replicação. O número após cada nome de controlador de domínio indica o número de erros de replicação no controlador de domínio.

Os erros para os servidores de destino e os de origem são mostrados porque alguns problemas são mais fáceis de solucionar da perspectiva do servidor de origem e outros da perspectiva do servidor de destino.

Neste exemplo, você pode ver que muitos servidores de destino têm aproximadamente o mesmo número de erros, mas há um servidor de origem (ADDC35) com muito mais erros do que todos os outros. É provável que haja algum problema no ADDC35 que esteja causando a falha ao enviar dados para seus parceiros de replicação. Corrigir os problemas no ADDC35 provavelmente resolverá muitos dos erros que aparecem na área do servidor de destino.

### <a name="replication-error-types"></a>Tipos de Erros de Replicação
Essa área fornece informações sobre os tipos de erros detectados em toda a empresa. Cada erro tem um código numérico exclusivo e uma mensagem que pode ajudar a determinar a causa do erro.

A rosca na parte superior dá uma ideia de quais erros aparecerem com mais ou menos frequência no seu ambiente.

Ela mostra quando vários controladores de domínio têm o mesmo erro de replicação. Nesse caso, você poderá descobrir ou identificar uma solução em um controlador de domínio e repeti-la em outros controladores de domínio afetados pelo mesmo erro.

### <a name="tombstone-lifetime"></a>tempo de vida da marca de exclusão
O tempo de vida da marca de exclusão determina quanto tempo um objeto excluído, conhecido como uma marca para exclusão, é mantido no banco de dados do Active Directory. Quando um objeto excluído ultrapassa o tempo de vida da marca de exclusão, um processo de coleta de lixo automaticamente o remove do banco de dados do Active Directory.

O tempo de vida da marca de exclusão padrão é 180 dias para versões mais recentes do Windows, mas era 60 dias em versões mais antigas e pode ser alterado explicitamente por um administrador do Active Directory.

É importante saber se você está tendo erros de replicação que estão se aproximando ou ultrapassaram o tempo de vida da marca de exclusão. Se dois controladores de domínio tiverem um erro de replicação que persiste após o tempo de vida da marca para exclusão, a replicação será desabilitada entre esses dois controladores de domínio, mesmo se o erro de replicação subjacente for corrigido.

A área Tempo de Vida da Marca de Exclusão ajuda a identificar os locais onde há o risco de acontecer a replicação desabilitada. Cada erro na categoria **Maior que 100% TSL** representa uma partição não replicada entre seu servidor de origem e de destino por pelo menos o tempo de vida da marca de exclusão da floresta.

Nessa situação, simplesmente corrigir o erro de replicação não será suficiente. No mínimo, você precisará investigar manualmente para identificar e limpar objetos remanescentes antes de reiniciar a replicação. Talvez ainda seja necessário encerrar um controlador de domínio.

Além de identificar os erros de replicação que persistiram após o tempo de vida da marca para exclusão, também é desejável prestar atenção nos erros que se enquadram nas categorias **50% a 75% TSL** ou **75% a 100% TSL**.

Esses são erros claramente remanescentes, não transitórios, e que provavelmente precisarão da sua intervenção para serem resolvidos. A boa notícia é que eles ainda não atingiram o tempo de vida da marca de exclusão. Se você corrigir estes problemas imediatamente e *antes* de atingirem o tempo de vida da marca de exclusão, a replicação poderá ser reiniciada com intervenção manual mínima.

Conforme observado anteriormente, o bloco do painel para a solução de Status de Replicação do AD mostra o número de erros *críticos* de replicação em seu ambiente, que é definido como erros de mais de 75% de tempo de vida da marca de exclusão (incluindo erros que são mais 100% da TSL). Tente manter esse número em 0.

> [!NOTE]
> Todos os cálculos de percentual de tempo de vida de marca de exclusão baseiam-se em valores atuais sua floresta do Active Directory, portanto você pode confiar que essas porcentagens são precisas, mesmo se tiver um valor de tempo de vida de marca de exclusão personalizado definido.
>
>

### <a name="ad-replication-status-details"></a>Detalhes do status de Replicação do AD
Ao clicar em qualquer item de uma das listas, você verá detalhes adicionais sobre ele usando a uma consulta de log. Os resultados são filtrados para mostrar somente os erros relacionados a esse item. Por exemplo, se clicar no primeiro controlador de domínio listado em **Status do Servidor de Destino (ADDC02)**, você verá os resultados da consulta filtrados para mostrar erros com esse controlador de domínio listado como o servidor de destino:

![Erros de Status de Replicação do AD nos resultados da consulta](./media/ad-replication-status/oms-ad-replication-search-details.png)

Aqui, você pode filtrar ainda mais, modificar a consulta de log e assim por diante. Para obter mais informações sobre como usar as consultas de log no Azure Monitor, confira [Analisar dados de log no Azure Monitor](../logs/log-query-overview.md).

O campo **HelpLink** mostra a URL de uma página do TechNet com detalhes adicionais sobre esse erro específico. Você pode copiar e colar esse link na janela do navegador para ver informações sobre solução de problemas e corrigir o erro.

Você também pode clicar em **Exportar** para exortar os resultados para o Excel. A exportação dos dados pode ajudá-lo a visualizar dados de erro de replicação de qualquer forma que você deseja.

![erros de status de replicação de AD exportados no Excel](./media/ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>Perguntas frequentes do Status de Replicação do AD
**P: Com que frequência os dados de status de replicação do AD são atualizados?**
R: As informações são atualizadas a cada cinco dias.

**P: Há uma maneira de configurar a frequência com que tais dados são atualizados?**
 R: Não no momento.

**P: Preciso adicionar todos os meus controladores de domínio ao meu espaço de trabalho do Log Analytics para ver o status de replicação?**
 R: Não, apenas um único controlador de domínio deve ser adicionado. Se você tiver vários controladores de domínio em seu espaço de trabalho do Log Analytics, dados de todos eles serão enviados ao Azure Monitor.

**P: não desejo adicionar nenhum controlador de domínio ao meu espaço de trabalho do Log Analytics. Ainda posso usar a solução Status de Replicação do AD?**

A: Sim. Você pode definir o valor de uma chave do Registro para habilitá-la. Confira [Habilitar o controlador fora do domínio](#enable-non-domain-controller).

**P: Qual é o nome do processo que faz a coleta de dados?**
 R: AdvisorAssessment.exe

**P: Quanto tempo leva para os dados serem coletados?**
 R: O tempo de coleta de dados depende do tamanho do ambiente do Active Directory, mas geralmente leva menos de 15 minutos.

**P: Que tipo de dados é coletado?**
 R: Informações de replicação são coletadas por meio do LDAP.

**P: Há uma maneira de configurar quando os dados são coletados?**
 R: Não no momento.

**P: De quais permissões preciso para coletar dados?**
R: As permissões de usuário normais para o Active Directory são suficientes.

## <a name="troubleshoot-data-collection-problems"></a>Solucionar problemas de coleta de dados
Para coletar dados, o pacote de solução de Status de Replicação do AD requer pelo menos um controlador de domínio esteja conectado ao seu espaço de trabalho do Log Analytics. Até você conectar um controlador de domínio, será exibida uma mensagem indicando que os **dados ainda estão sendo coletados**.

Se precisar de assistência para conectar um de seus controladores de domínio, você poderá exibir a documentação em [Conectar computadores Windows ao Azure Monitor](../agents/om-agents.md). Como alternativa, se o controlador de domínio já estiver conectado a um ambiente existente do System Center Operations Manager, você poderá exibir a documentação em [Conectar o System Center Operations Manager ao Azure Monitor](../agents/om-agents.md).

Se você não quiser conectar nenhum controlador de domínio diretamente ao Azure Monitor ou ao System Center Operations Manager, confira [Habilitar um controlador fora do domínio](#enable-non-domain-controller).

## <a name="next-steps"></a>Próximas etapas
* Use [Consultas de log no Azure Monitor](../logs/log-query-overview.md) para exibir dados detalhados do Status de Replicação do Active Directory.

