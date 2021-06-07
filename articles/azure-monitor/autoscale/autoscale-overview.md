---
title: Dimensionamento automático no Microsoft Azure
description: Dimensionamento automático no Microsoft Azure
ms.subservice: autoscale
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 4727d562e21b92e58c8091f1161cf53198ff0b26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101725995"
---
# <a name="overview-of-autoscale-in-microsoft-azure"></a>Visão geral do dimensionamento automático no Microsoft Azure
Este artigo descreve o que é dimensionamento automático do Microsoft Azure, seus benefícios e como começar a usá-lo.  

O dimensionamento automático do Azure Monitor aplica-se somente aos [Conjuntos de Dimensionamento de Máquinas Virtuais](https://azure.microsoft.com/services/virtual-machine-scale-sets/), aos [Serviços de Nuvem](https://azure.microsoft.com/services/cloud-services/), ao [Serviço de Aplicativo – Aplicativos Web](https://azure.microsoft.com/services/app-service/web/), aos [Serviços de Gerenciamento de API](../../api-management/api-management-key-concepts.md) e aos [Clusters do Azure Data Explorer](/azure/data-explorer/).

> [!NOTE]
> O Azure tem dois métodos de dimensionamento automático. Uma versão mais antiga do dimensionamento automático aplica-se às Máquinas Virtuais (conjuntos de disponibilidade). Esse recurso tem suporte limitado e recomendamos migrar para os conjuntos de dimensionamento da máquina virtual para obter um suporte mais rápido e confiável do dimensionamento automático. Um link sobre como usar a tecnologia mais antiga será incluído neste artigo.  
>

## <a name="what-is-autoscale"></a>O que é dimensionamento automático?
O dimensionamento automático permite ter a quantidade certa de recursos em execução para lidar com a carga em seu aplicativo. Ele permite adicionar recursos para lidar com os aumentos de carga e também economizar dinheiro removendo os recursos que estão ociosos. Você especifica um número mínimo e máximo de instâncias a serem executadas e adiciona ou remove as VMs automaticamente com base em um conjunto de regras. Ter um mínimo assegura que seu aplicativo estará sempre em execução, mesmo sem carga. Ter um máximo limita seu custo por hora total possível. Você dimensiona automaticamente entre esses dois extremos usando as regras criadas.

 ![Dimensionamento automático explicado. Adicionar e remover as VMs](./media/autoscale-overview/AutoscaleConcept.png)

Quando as condições da regra forem atendidas, uma ou mais ações de dimensionamento automático são disparadas. Você pode adicionar e remover as VMs ou executar outras ações. O seguinte diagrama conceitual mostra esse processo.  

 ![Diagrama do Fluxo do Dimensionamento Automático](./media/autoscale-overview/Autoscale_Overview_v4.png)

A explicação a seguir se aplica às partes do diagrama anterior.   

## <a name="resource-metrics"></a>Métricas do recurso
Os recursos emitem métricas, que são processadas posteriormente por regras. As métricas são fornecidas por métodos diferentes.
Os conjuntos de dimensionamento de máquina virtual usam dados de telemetria de agentes de diagnóstico do Azure, enquanto a telemetria para aplicativos Web e serviços de nuvem vêm diretamente da Infraestrutura do Azure. Algumas estatísticas usadas normalmente incluem o Uso da CPU, utilização de memória, contagens de thread, comprimento da fila e uso do disco. Para obter uma lista de quais dados de telemetria você pode usar, confira [Métricas comuns de dimensionamento automático](autoscale-common-metrics.md).

## <a name="custom-metrics"></a>Métricas personalizadas
Você também pode usar suas próprias métricas personalizadas que seus aplicativos podem emitir. Se tiver configurado seu aplicativo para enviar métricas para o Application Insights, você poderá aproveitar essas métricas para tomar decisões quanto ao dimensionamento.

## <a name="time"></a>Hora
Regras com base em agendamento têm base em UTC. Ao configurar as regras, você deve definir o fuso horário corretamente.  

## <a name="rules"></a>Regras
O diagrama mostra apenas uma regra de dimensionamento automático, mas você pode ter muitas deles. Você pode criar regras complexas de sobreposição, conforme o necessário para sua situação.  Os tipos de regra incluem  

* **Com base em métrica** - Por exemplo, executar esta ação quando o uso da CPU estiver acima de 50%.
* **Com base no tempo** - Por exemplo, disparar um webhook todas os sábados às 8h em um determinado fuso horário.

As regras baseadas na métrica medem a carga do aplicativo e adicionam ou removem as VMs com base nessa carga. As regras baseadas no agendamento permitem que você dimensione quando vê os padrões de tempo em sua carga e deseja dimensionar antes que ocorra um possível aumento ou diminuição de carga.  

## <a name="actions-and-automation"></a>Ações e automação
As regras podem disparar um ou mais tipos de ações.

* **Escala** - Aumenta ou diminui as VMs
* **Email** - Envia um email para os administradores e coadministradores da assinatura, e/ou para o endereço de email adicional especificado
* **Automatizar via webhooks** - Chama webhooks, que podem disparar várias ações complexas dentro ou fora do Azure. Dentro do Azure, você pode iniciar um runbook de Automação do Azure, Função do Azure ou Aplicativo Lógico do Azure. Entre os exemplos de URL de terceiros fora do Azure estão serviços como o Slack e o Twilio.

## <a name="autoscale-settings"></a>Configurações de dimensionamento automático
O dimensionamento automático usa a seguinte terminologia e estrutura.

- Uma **configuração do dimensionamento automático** é lido pelo mecanismo de dimensionamento automático para determinar se é para aumentar ou reduzir. Ele contém um ou mais perfis, informações sobre o recurso de destino e as configurações de notificação.

  - Um **perfil de dimensionamento automático** é um combinação de:

    - **configuração de capacidade**, que indica os valores mínimo, máximo e padrão do número de instâncias.
    - **conjunto de regras**, cada um deles inclui um gatilho (tempo ou métrica) e uma ação de dimensionamento (para cima ou para baixo).
    - **recorrência**, que indica quando o dimensionamento automático deve colocar esse perfil em vigor.

      Você pode ter vários perfis, que permitem cuidar dos diferentes requisitos de sobreposição. Você pode ter diferentes perfis de dimensionamento automático para diferentes horas do dia ou dias da semana, por exemplo.

  - Uma **configuração de notificação** define quais notificações devem ocorrer quando acontece um evento de dimensionamento automático com base no atendimento dos critérios de um dos perfis da configuração do dimensionamento automático. O dimensionamento automático pode notificar um ou mais endereços de email ou fazer chamadas para um ou mais webhooks.


![Configuração do dimensionamento automático do Azure, perfil e estrutura de regras](./media/autoscale-overview/AzureResourceManagerRuleStructure3.png)

A lista completa de campos configuráveis e descrições está disponível na [API REST do Dimensionamento Automático](/rest/api/monitor/autoscalesettings).

Para obter exemplos de código, consulte

* [Configuração avançada de Dimensionamento Automático usando modelos do Resource Manager para conjuntos de escala de VM](autoscale-virtual-machine-scale-sets.md)  
* [API REST do Dimensionamento Automático](/rest/api/monitor/autoscalesettings)

## <a name="horizontal-vs-vertical-scaling&quot;></a>Dimensionamento horizontal vs. vertical
O dimensionamento automático ocorre apenas horizontalmente, que é um aumento (&quot;out") ou uma diminuição ("in") do número de instâncias de VM.  O dimensionamento horizontal é mais flexível em uma nuvem, pois permite que você potencialmente execute milhares de VMs para manipular carga.

Já o dimensionamento vertical é diferente. Ele mantém o mesmo número de VMs, mas torna as VMs mais ("para cima") ou menos ("para baixo") potentes. O poder é medido em termos de memória, velocidade da CPU, espaço em disco etc.  O dimensionamento vertical tem mais limitações. Ele depende da disponibilidade de um hardware maior, que atinge rapidamente um limite superior e pode variar por região. O dimensionamento vertical normalmente também exige que uma VM pare e reinicie.

## <a name="methods-of-access"></a>Métodos de acesso
Você pode configurar o dimensionamento automático via

* [Azure portal](autoscale-get-started.md)
* [PowerShell](../powershell-samples.md#create-and-manage-autoscale-settings)
* [CLI (Interface de linha de comando) de plataforma cruzada](../cli-samples.md#autoscale)
* [API REST do Monitor do Azure](/rest/api/monitor/autoscalesettings)

## <a name="supported-services-for-autoscale"></a>Serviços com suporte para o dimensionamento automático
| Serviço | Esquema e Documentos |
| --- | --- |
| Aplicativos Web |[Dimensionamento de Aplicativos Web](autoscale-get-started.md) |
| Serviços de Nuvem |[Dimensionamento Automático de um Serviço de Nuvem](../../cloud-services/cloud-services-how-to-scale-portal.md) |
| Máquinas Virtuais: Clássico |[Dimensionamento de conjuntos de disponibilidade da máquina virtual clássica](/archive/blogs/kaevans/autoscaling-azurevirtual-machines) |
| Máquinas Virtuais: Conjuntos de Dimensionamento do Windows |[Dimensionamento de conjuntos de dimensionamento de máquina virtual no Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) |
| Máquinas Virtuais: Conjuntos de Dimensionamento do Linux |[Dimensionamento de conjuntos de dimensionamento de máquina virtual no Linux](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) |
| Máquinas Virtuais: Exemplo do Windows |[Configuração avançada de Dimensionamento Automático usando modelos do Resource Manager para conjuntos de escala de VM](autoscale-virtual-machine-scale-sets.md) |
| Serviço de aplicativo do Azure |[Escalar verticalmente um aplicativo no Serviço de Aplicativo do Azure](../../app-service/manage-scale-up.md)|
| Serviço de Gerenciamento de API|[Dimensionar automaticamente uma instância do Gerenciamento de API do Azure](../../api-management/api-management-howto-autoscale.md)
| Clusters do Azure Data Explorer|[Gerenciar o dimensionamento de clusters do Azure Data Explorer para acomodar as mudanças de demanda](/azure/data-explorer/manage-cluster-horizontal-scaling)|
| Aplicativos Lógicos |[Adicionar capacidade do ISE (Ambiente de Serviço de Integração)](../../logic-apps/ise-manage-integration-service-environment.md#add-ise-capacity)|
| Spring Cloud |[Configurar o dimensionamento automático para aplicativos de microsserviço](../../spring-cloud/spring-cloud-tutorial-setup-autoscale.md)|
| Barramento de Serviço |[Atualizar automaticamente as unidades do sistema de mensagens de um namespace do Barramento de Serviço do Azure](../../service-bus-messaging/automate-update-messaging-units.md)|

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o dimensionamento automático, use as Explicações Passo a Passo sobre o Dimensionamento Automático listadas anteriormente ou consulte os recursos a seguir:

* [Métricas comuns de dimensionamento automático do Azure Monitor](autoscale-common-metrics.md)
* [Práticas recomendadas para dimensionamento automático do Azure Monitor](autoscale-best-practices.md)
* [Usar ações de dimensionamento automático para enviar notificações de alerta por email e webhook](autoscale-webhook-email.md)
* [API REST do Dimensionamento Automático](/rest/api/monitor/autoscalesettings)
* [Solução de Problemas do Dimensionamento Automático dos Conjuntos de Dimensionamento da Máquina Virtual](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)