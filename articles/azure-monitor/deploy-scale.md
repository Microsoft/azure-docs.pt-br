---
title: Implantar Azure Monitor em escala usando Azure Policy
description: Implante Azure Monitor recursos em escala usando Azure Policy.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/08/2020
ms.openlocfilehash: cc55cd17a547b9c63f2c26479d5797fae016d8d7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102044061"
---
# <a name="deploy-azure-monitor-at-scale-using-azure-policy"></a>Implantar Azure Monitor em escala usando Azure Policy
Embora alguns recursos de Azure Monitor sejam configurados uma vez ou um número limitado de vezes, outros devem ser repetidos para cada recurso que você deseja monitorar. Este artigo descreve métodos para usar o Azure Policy para implementar Azure Monitor em escala para garantir que o monitoramento seja configurado de forma consistente e precisa para todos os seus recursos do Azure.

Por exemplo, você precisa criar uma configuração de diagnóstico para todos os recursos existentes do Azure e para cada novo recurso que você criar. Você também precisa ter um agente instalado e configurado sempre que criar uma máquina virtual. Você pode usar métodos como o PowerShell ou a CLI para executar essas ações, já que esses métodos estão disponíveis para todos os recursos do Azure Monitor. Usando Azure Policy, você pode ter uma lógica em vigor que executará automaticamente a configuração apropriada sempre que você criar ou modificar um recurso.


## <a name="azure-policy"></a>Azure Policy
Esta seção fornece uma breve introdução ao [Azure Policy](../governance/policy/overview.md) que permite avaliar e impor padrões organizacionais em toda a sua assinatura do Azure ou grupo de gerenciamento com esforço mínimo. Consulte a [documentação do Azure Policy](../governance/policy/overview.md) para obter detalhes completos.

Com Azure Policy você pode especificar requisitos de configuração para todos os recursos que são criados e identificar os recursos que estão fora de conformidade, impedir que os recursos sejam criados ou adicionar a configuração necessária. Ele funciona interceptando chamadas para criar um novo recurso ou modificar um recurso existente. Ele pode responder com esses efeitos como negar a solicitação se não corresponder à correspondência com as propriedades esperadas em uma definição de política, sinalizando-a para não conformidade ou implantando um recurso relacionado. Você pode corrigir os recursos existentes com uma definição de política **deployIfNotExists** ou **Modify** .

Azure Policy consiste nos objetos na tabela a seguir. Consulte [Azure Policy objetos](../governance/policy/overview.md#azure-policy-objects) para obter uma explicação mais detalhada de cada um.

| Item | Descrição |
|:---|:---|
| Definição de política | Descreve as condições de conformidade do recurso e o efeito a ser tomada se uma condição for atendida. Isso pode ser todos os recursos de um tipo específico ou apenas recursos que correspondem a determinadas propriedades. O efeito pode ser simplesmente sinalizar o recurso quanto à conformidade ou implantar um recurso relacionado. As definições de política são escritas usando JSON, conforme descrito em [estrutura de definição de Azure Policy](../governance/policy/concepts/definition-structure.md). Os efeitos são descritos em [entender Azure Policy efeitos](../governance/policy/concepts/effects.md).
| Iniciativa de política | Um grupo de definições de política que devem ser aplicadas juntas. Por exemplo, você pode ter uma definição de política para enviar logs de recursos para um espaço de trabalho Log Analytics e outro para enviar logs de recursos para os hubs de eventos. Crie uma iniciativa que inclui as duas definições de política e aplique a iniciativa aos recursos em vez das definições de política individuais. As iniciativas são escritas usando JSON, conforme descrito em [Azure Policy estrutura de iniciativa](../governance/policy/concepts/initiative-definition-structure.md). |
| Atribuição | Uma definição ou iniciativa de política não entrará em vigor até ser atribuída a um escopo. Por exemplo, atribua uma política a um grupo de recursos para aplicá-la a todos os recursos criados nesse recurso ou aplique-a a uma assinatura para aplicá-la a todos os recursos nessa assinatura.  Para obter mais detalhes, consulte [Azure Policy estrutura de atribuição](../governance/policy/concepts/assignment-structure.md). |

## <a name="built-in-policy-definitions-for-azure-monitor"></a>Definições de política internas para o Azure Monitor
Azure Policy inclui várias definições predefinidas relacionadas a Azure Monitor. Você pode atribuir essas definições de política à sua assinatura existente ou usá-las como base para criar suas próprias definições personalizadas. Para obter uma lista completa do política interna na categoria **monitoramento** , consulte [Azure Policy definições de política internas para Azure monitor](.//policy-reference.md).

Para exibir as definições de política internas relacionadas ao monitoramento, faça o seguinte:

1. Vá para **Azure Policy** na portal do Azure.
2. Selecione **Definições**.
3. Para **tipo**, selecione *interno* e para **categoria**, selecione *monitoramento*.

  ![Captura de tela da página Definições de Azure Policy no portal do Azure mostrando uma lista de definições de política para a categoria de monitoramento e o tipo interno.](media/deploy-scale/builtin-policies.png)


## <a name="diagnostic-settings"></a>Configurações de Diagnóstico
[As configurações de diagnóstico](essentials/diagnostic-settings.md) coletam logs de recursos e métricas de recursos do Azure para vários locais, normalmente para um espaço de trabalho log Analytics que permite que você analise os dados com [consultas de log](logs/log-query-overview.md) e [alertas de log](alerts/alerts-log.md). Use a política para criar automaticamente uma configuração de diagnóstico cada vez que você criar um recurso.

Cada tipo de recurso do Azure tem um conjunto exclusivo de categorias que precisam ser listadas na configuração de diagnóstico. Por isso, cada tipo de recurso requer uma definição de política separada. Alguns tipos de recursos têm definições de políticas internas que você pode atribuir sem modificação. Para outros tipos de recursos, você precisa criar uma definição personalizada.

### <a name="built-in-policy-definitions-for-azure-monitor"></a>Definições de política internas para o Azure Monitor
Há duas definições de política internas para cada tipo de recurso, uma para enviar para Log Analytics espaço de trabalho e outra para o Hub de eventos. Se você precisar apenas de um local, atribua essa política para o tipo de recurso. Se você precisar de ambos, atribua as duas definições de política para o recurso.

Por exemplo, a imagem a seguir mostra as definições de política de configuração de diagnóstico interno para Data Lake Analytics.

  ![Captura de tela parcial na página de definições de Azure Policy mostrando duas definições de política de configuração de diagnóstico interno para Data Lake Analytics.](media/deploy-scale/builtin-diagnostic-settings.png)

### <a name="custom-policy-definitions"></a>Definições de política personalizadas
Para tipos de recursos que não têm uma política interna, você precisa criar uma definição de política personalizada. Você pode fazer isso manualmente no portal do Azure copiando uma política interna existente e, em seguida, modificando para o tipo de recurso. No entanto, é mais eficiente criar a política de forma programática usando um script na Galeria do PowerShell.

O script [Create-AzDiagPolicy](https://www.powershellgallery.com/packages/Create-AzDiagPolicy) cria arquivos de política para um tipo de recurso específico que você pode instalar usando o PowerShell ou a CLI. Use o procedimento a seguir para criar uma definição de política personalizada para configurações de diagnóstico.


1. Verifique se você tem [Azure PowerShell](/powershell/azure/install-az-ps) instalado.
2. Instale o script com o seguinte comando:
  
    ```azurepowershell
    Install-Script -Name Create-AzDiagPolicy
    ```

3. Execute o script usando os parâmetros para especificar para onde enviar os logs. Você será solicitado a especificar uma assinatura e um tipo de recurso. Por exemplo, para criar uma definição de política que envia para Log Analytics espaço de trabalho e Hub de eventos, use o comando a seguir.

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

4. Como alternativa, você pode especificar uma assinatura e um tipo de recurso no comando. Por exemplo, para criar uma definição de política que envia para Log Analytics espaço de trabalho e o Hub de eventos para bancos de dados do Azure SQL Server, use o comando a seguir.

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -SubscriptionID xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -ResourceType Microsoft.Sql/servers/databases  -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

5. O script cria pastas separadas para cada definição de política, cada uma contendo três arquivos chamados azurepolicy.json, azurepolicy.rules.json, azurepolicy.parameters.json. Se você quiser criar a política manualmente no portal do Azure, poderá copiar e colar o conteúdo de azurepolicy.jsno, já que ele inclui a definição de política inteira. Use os outros dois arquivos com o PowerShell ou a CLI para criar a definição de política a partir de uma linha de comando.

    Os exemplos a seguir mostram como instalar a definição de política do PowerShell e da CLI. Cada um inclui metadados para especificar uma categoria de **monitoramento** para agrupar a nova definição de política com as definições de política internas.

      ```azurepowershell
      New-AzPolicyDefinition -name "Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace" -policy .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json -parameter .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json -mode All -Metadata '{"category":"Monitoring"}'
      ```

      ```azurecli
      az policy definition create --name 'deploy-diag-setting-sql-database--workspace' --display-name 'Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace'  --rules 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json' --params 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json' --subscription 'AzureMonitor_Docs' --mode All
      ```

### <a name="initiative"></a>Iniciativa
Em vez de criar uma atribuição para cada definição de política, uma estratégia comum é criar uma iniciativa que inclua as definições de política para criar configurações de diagnóstico para cada serviço do Azure. Crie uma atribuição entre a iniciativa e um grupo de gerenciamento, assinatura ou grupo de recursos, dependendo de como você gerencia seu ambiente. Essa estratégia oferece os seguintes benefícios:

- Crie uma única atribuição para a iniciativa em vez de várias atribuições para cada tipo de recurso. Use a mesma iniciativa para vários grupos de monitoramento, assinaturas ou grupos de recursos.
- Modifique a iniciativa quando precisar adicionar um novo tipo de recurso ou destino. Por exemplo, os requisitos iniciais podem ser enviar dados somente para um espaço de trabalho Log Analytics, mas posteriormente você deseja adicionar o Hub de eventos. Modifique a iniciativa em vez de criar novas atribuições.

Consulte [criar e atribuir uma definição de iniciativa](../governance/policy/tutorials/create-and-manage.md#create-and-assign-an-initiative-definition) para obter detalhes sobre como criar uma iniciativa. Considere as seguintes recomendações:

- Defina a **categoria** para **monitoramento** para agrupá-la com definições de política personalizadas e internas relacionadas.
- Em vez de especificar os detalhes para o espaço de trabalho Log Analytics e o Hub de eventos para definição de política incluída na iniciativa, use um parâmetro de iniciativa comum. Isso permite que você especifique facilmente um valor comum para todas as definições de política e altere esse valor, se necessário.

![Definição de iniciativa](media/deploy-scale/initiative-definition.png)

### <a name="assignment"></a>Atribuição 
Atribua a iniciativa a um grupo de gerenciamento, assinatura ou grupo de recursos do Azure, dependendo do escopo dos recursos a serem monitorados. Um [grupo de gerenciamento](../governance/management-groups/overview.md) é particularmente útil para a diretiva de escopo, especialmente se a sua organização tiver várias assinaturas.

![Captura de tela das configurações da guia noções básicas na seção atribuir iniciativa das configurações de diagnóstico para Log Analytics espaço de trabalho no portal do Azure.](media/deploy-scale/initiative-assignment.png)

Usando parâmetros de iniciativa, você pode especificar o espaço de trabalho ou qualquer outro detalhe uma vez para todas as definições de política na iniciativa. 

![Parâmetros de iniciativa](media/deploy-scale/initiative-parameters.png)

### <a name="remediation"></a>Remediação
A iniciativa será aplicada a cada máquina virtual à medida que ela for criada. Uma [tarefa de correção](../governance/policy/how-to/remediate-resources.md) implanta as definições de política na iniciativa para recursos existentes, portanto, isso permite que você crie configurações de diagnóstico para todos os recursos que já foram criados. Ao criar a atribuição usando o portal do Azure, você tem a opção de criar uma tarefa de correção ao mesmo tempo. Consulte [corrigir recursos sem conformidade com Azure Policy](../governance/policy/how-to/remediate-resources.md) para obter detalhes sobre a correção.

![Correção da iniciativa](media/deploy-scale/initiative-remediation.png)


## <a name="vm-insights"></a>Insights da VM
O [virtual insights](vm/vminsights-overview.md) é a principal ferramenta em Azure monitor para monitorar máquinas virtuais. A habilitação das informações de VM instala o agente de Log Analytics e o agente de dependência. Em vez de executar essas tarefas manualmente, use Azure Policy para garantir que cada máquina virtual seja configurada ao criá-la.

> [!NOTE]
> O Revisions de VM inclui um recurso chamado **cobertura de política de informações de VM** que permite descobrir e corrigir VMs não compatíveis em seu ambiente. Você pode usar esse recurso em vez de trabalhar diretamente com Azure Policy para VMs do Azure e para máquinas virtuais híbridas conectadas com o Azure Arc. Para conjuntos de dimensionamento de máquinas virtuais do Azure, você deve criar a atribuição usando Azure Policy.
 

As informações de VM incluem as seguintes iniciativas internas que instalam os dois agentes para habilitar o monitoramento completo. 

|Nome |Descrição |
|:---|:---|
|Habilitar insights da VM | Instala o agente de Log Analytics e o agente de dependência em VMs do Azure e VMs híbridas conectadas ao Azure Arc. |
|Habilitar Azure Monitor para conjuntos de dimensionamento de máquinas virtuais | Instala o agente de Log Analytics e o agente de dependência no conjunto de dimensionamento de máquinas virtuais do Azure. |


### <a name="virtual-machines"></a>Máquinas virtuais
Em vez de criar atribuições para essas iniciativas usando a interface Azure Policy, o VM insights inclui um recurso que permite inspecionar o número de máquinas virtuais em cada escopo para determinar se a iniciativa foi aplicada. Em seguida, você pode configurar o espaço de trabalho e criar as atribuições necessárias usando essa interface.

Para obter detalhes sobre esse processo, consulte [habilitar o insights de VM usando Azure Policy](./vm/vminsights-enable-policy.md).

![Política de informações de VM](media/deploy-scale/vminsights-policy.png)

### <a name="virtual-machine-scale-sets"></a>conjuntos de escala de máquina virtual
Para usar Azure Policy para habilitar o monitoramento de conjuntos de dimensionamento de máquinas virtuais, atribua a iniciativa **habilitar Azure monitor para conjuntos de dimensionamento de máquinas virtuais** a um grupo de gerenciamento, assinatura ou grupo de recursos do Azure, dependendo do escopo dos recursos a serem monitorados. Um [grupo de gerenciamento](../governance/management-groups/overview.md) é particularmente útil para a diretiva de escopo, especialmente se a sua organização tiver várias assinaturas.

![Captura de tela da página atribuir iniciativa no portal do Azure. A definição de iniciativa está definida para habilitar Azure Monitor para conjuntos de dimensionamento de máquinas virtuais.](media/deploy-scale/virtual-machine-scale-set-assign-initiative.png)

Selecione o espaço de trabalho para o qual os dados serão enviados. Esse espaço de trabalho deve ter a solução *VMInsights* instalada, conforme descrito em [Configurar o espaço de trabalho log Analytics para o VM insights](vm/vminsights-configure-workspace.md).

![Selecione o workspace](media/deploy-scale/virtual-machine-scale-set-workspace.png)

Crie uma tarefa de correção se você tiver um conjunto de dimensionamento de máquinas virtuais existente que precise ser atribuído a essa política.

![Tarefa de correção](media/deploy-scale/virtual-machine-scale-set-remediation.png)

### <a name="log-analytics-agent"></a>Agente do Log Analytics
Você pode ter cenários em que deseja instalar o agente de Log Analytics, mas não o agente de dependência. Não há nenhuma iniciativa interna apenas para o agente, mas você pode criar suas próprias com base nas definições de política internas fornecidas pelo virtual insights.

> [!NOTE]
> Não haveria nenhum motivo para implantar o agente de dependência por conta própria, pois ele requer que o agente de Log Analytics entregue seus dados a Azure Monitor.


|Nome |Descrição |
|-----|------------|
|Implantação de agente de Log Analytics de auditoria – imagem de VM (SO) não listada |Relata as VMs como não compatíveis se a imagem de VM (SO) não estiver definida na lista e o agente não estiver instalado. |
|Implantar o agente do Log Analytics para VMs do Linux |Implante Log Analytics agente para VMs Linux se a imagem de VM (SO) estiver definida na lista e o agente não estiver instalado. |
|Implantar o agente do Log Analytics para VMs do Windows |Implante Log Analytics agente para VMs do Windows se a imagem de VM (SO) estiver definida na lista e o agente não estiver instalado. |
| [Versão prévia]: o agente de Log Analytics deve ser instalado em seus computadores Linux do Azure Arc |Relata os computadores do Arc do Azure híbrido como não compatíveis para VMs do Linux se a imagem da VM (SO) estiver definida na lista e o agente não estiver instalado. |
| [Versão prévia]: o agente de Log Analytics deve ser instalado em seus computadores Windows Azure Arc |Relata os computadores do Arc do Azure híbrido como não compatíveis para VMs do Windows se a imagem de VM (SO) estiver definida na lista e o agente não estiver instalado. |
| [Visualização]: implantar o agente de Log Analytics em computadores com o Arc do Azure para Linux |Implante o agente de Log Analytics para máquinas de Arc do Azure híbridos do Linux se a imagem de VM (SO) estiver definida na lista e o agente não estiver instalado. |
| [Visualização]: implantar o agente de Log Analytics em computadores Windows Azure Arc |Implante o agente de Log Analytics para máquinas Windows híbridas do Azure se a imagem de VM (SO) estiver definida na lista e o agente não estiver instalado. |
|Auditoria de implantação de agente de dependência em conjuntos de dimensionamento de máquinas virtuais – imagem de VM (SO) não listada |Relata o conjunto de dimensionamento de máquinas virtuais como não compatível se a imagem da VM (SO) não estiver definida na lista e o agente não estiver instalado. |
|Auditoria de implantação de agente de Log Analytics em conjuntos de dimensionamento de máquinas virtuais – imagem de VM (SO) não listada |Relata o conjunto de dimensionamento de máquinas virtuais como não compatível se a imagem da VM (SO) não estiver definida na lista e o agente não estiver instalado. |
|Implantar o agente do Log Analytics em conjuntos de dimensionamento de máquinas virtuais do Linux |Implante o agente de Log Analytics para conjuntos de dimensionamento de máquinas virtuais do Linux se a imagem de VM (SO) estiver definida na lista e o agente não estiver instalado. |
|Implantar o agente do Log Analytics em conjuntos de dimensionamento de máquinas virtuais do Windows |Implante o agente de Log Analytics para conjuntos de dimensionamento de máquinas virtuais do Windows se a imagem de VM (SO) estiver definida na lista e o agente não estiver instalado. |


## <a name="next-steps"></a>Próximas etapas

- Leia mais sobre [Azure Policy](../governance/policy/overview.md).
- Leia mais sobre [as configurações de diagnóstico](essentials/diagnostic-settings.md).