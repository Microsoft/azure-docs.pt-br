---
title: Central de Segurança do Azure gratuita versus Azure Defender habilitado
description: Saiba mais sobre os benefícios de habilitar o Azure Defender para proteção de carga de trabalho de nuvem na Central de Segurança do Azure
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/23/2021
ms.openlocfilehash: aa65989953f761ff915383fcb59da7f36ea98dab
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600475"
---
# <a name="azure-security-center-free-vs-azure-defender-enabled"></a>Central de Segurança do Azure gratuita versus Azure Defender habilitado
O Azure Defender é gratuito nos primeiros 30 dias. Ao final dos 30 dias, se você optar por continuar usando o serviço, a cobrança pelo uso será iniciada automaticamente.

Você pode atualizar da página **Preço e configurações**, conforme descrito em [Início Rápido: habilitar Azure Defender](enable-azure-defender.md). Para obter detalhes de preço na sua moeda preferencial e de acordo com a sua região, confira [Preço da Central de Segurança](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="what-are-the-benefits-of-enabling-azure-defender"></a>Quais são os benefícios de habilitar o Azure Defender?

A Central de Segurança é oferecida em dois modos:

- **Azure Defender DESATIVADO** (Gratuito) – A Central de Segurança sem o Azure Defender está habilitada gratuitamente em todas as suas assinaturas do Azure quando você acessa o painel da Central de Segurança do Azure no portal do Azure pela primeira vez ou, se ela é habilitada de maneira programática por meio da API. O uso desse modo gratuito fornece uma política de segurança, uma avaliação de segurança contínua e recomendações de segurança práticas para ajudar você a proteger seus recursos do Azure.

- **Azure Defender ATIVADO**: a habilitação do Azure Defender estende as funcionalidades do modo gratuito para cargas de trabalho em execução em nuvens privadas e outras nuvens públicas, oferecendo gerenciamento unificado de segurança e proteção contra ameaças nas suas cargas de trabalho de nuvem híbrida. Alguns dos principais recursos do Azure Defender:

    - **Microsoft Defender para Ponto de Extremidade** – o Azure Defender para servidores inclui o [Microsoft Defender para Ponto de Extremidade](https://www.microsoft.com/microsoft-365/security/endpoint-defender) para EDR (detecção e resposta de ponto de extremidade) ampla. Saiba mais sobre os benefícios de usar o Microsoft Defender para Ponto de Extremidade junto com o Azure Defender em [Usar a solução de EDR integrada da Central de Segurança](security-center-wdatp.md).
    - **Verificação de vulnerabilidades de máquinas virtuais e registros de contêiner**: implante com facilidade um verificador em todas as suas máquinas virtuais que fornece a solução mais avançada do setor para o gerenciamento de vulnerabilidades. Veja, investigue e corrija as descobertas diretamente na Central de Segurança. 
    - **Segurança híbrida** – Obtenha uma exibição unificada sobre a segurança em todas as suas cargas de trabalho locais e na nuvem. Aplique políticas de segurança e avalie continuamente a segurança de suas cargas de trabalho de nuvem híbrida a fim de garantir a conformidade com padrões de segurança. Colete, pesquise e analise dados de segurança de várias fontes, incluindo firewalls e outras soluções de parceiros.
    - **Alertas de proteção contra ameaças**: a análise comportamental avançada e o Gráfico de Segurança Inteligente da Microsoft oferecem uma vantagem em relação aos ataques cibernéticos em evolução. A análise comportamental interna e o machine learning podem identificar ataques e explorações de dia zero. Monitore redes, computadores e serviços de nuvem contra ataques recebidos e atividade pós-violação. Simplifique a investigação com ferramentas interativas e inteligência contextual contra ameaças.
    - **AAC** (controles de acesso e de aplicativo): bloqueie malware e outros aplicativos indesejados aplicando recomendações ativadas por machine learning adaptadas para suas cargas de trabalho específicas a fim de criar listas de permissões e negação. Reduza a superfície de ataque da rede com o acesso just-in-time controlado às portas de gerenciamento nas VMs do Azure. O AAC reduz significativamente a exposição a ataques de força bruta e outros ataques de rede.
    - **Recursos de segurança do contêiner**: beneficie-se do gerenciamento de vulnerabilidades e da proteção contra ameaças em tempo real nos seus ambientes em contêineres. Ao habilitar o **Azure Defender para registros de contêiner**, podem ser necessárias até 12 horas até que todos os recursos sejam habilitados. As cobranças são baseadas no número de imagens de contêiner exclusivas enviadas por push para o registro conectado. Depois que uma imagem for verificada uma vez, você não será cobrado novamente, a menos que ela seja modificada e enviada por push mais uma vez.
    - **Proteção contra ameaças com abrangência para recursos conectados ao ambiente do Azure**: o Azure Defender inclui a proteção contra ameaças com abrangência nativa do Azure para os serviços do Azure comuns a todos os seus recursos: Azure Resource Manager, DNS do Azure, camada de rede do Azure e Azure Key Vault. O Azure Defender tem visibilidade exclusiva da camada de gerenciamento do Azure e da camada do DNS do Azure e, portanto, pode proteger os recursos de nuvem conectados a essas camadas.


## <a name="faq---pricing-and-billing"></a>Perguntas frequentes – Preços e cobrança 

- [Como posso rastrear quem, na minha organização, habilitou as alterações no Azure Defender na Central de Segurança?](#how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center)
- [Quais são os planos oferecidos pela Central de Segurança?](#what-are-the-plans-offered-by-security-center)
- [Como fazer para habilitar o Azure Defender para minha assinatura?](#how-do-i-enable-azure-defender-for-my-subscription)
- [Posso habilitar o Azure Defender para servidores em um subconjunto de servidores em minha assinatura?](#can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription)
- [Se eu já tenho uma licença para o Microsoft Defender para ponto de extremidade, ganho desconto no Azure Defender?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [Minha assinatura tem o Azure Defender para servidores habilitado. Os servidores que não estão em execução são cobrados?](#my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers)
- [Serei cobrado pelos computadores sem o agente do Log Analytics instalado?](#will-i-be-charged-for-machines-without-the-log-analytics-agent-installed)
- [Se um agente do Log Analytics for subordinado a vários workspaces, serei cobrado duas vezes?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice)
- [Se um agente do Log Analytics for subordinado a vários workspaces, a ingestão de dados gratuita de 500 MB estará disponível em todos eles?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them)
- [A ingestão de dados gratuita de 500 MB é calculada para um workspace inteiro ou estritamente por computador?](#is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine)
- [Quais tipos de dados estão incluídos na provisão diária de dados de 500 MB?](#what-data-types-are-included-in-the-500-mb-data-daily-allowance)


### <a name="how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center"></a>Como posso rastrear quem, na minha organização, habilitou as alterações no Azure Defender na Central de Segurança?
As assinaturas do Azure podem ter vários administradores com permissões para alterar as configurações de preço. Para descobrir qual usuário fez uma alteração, use o log de atividades do Azure.

:::image type="content" source="media/security-center-pricing/logged-change-to-pricing.png" alt-text="Log de atividades do Azure mostrando um evento de alteração de preço":::

Se as informações do usuário não estiverem listadas na coluna **Evento iniciado por**, explore o JSON do evento para ver os detalhes relevantes.

:::image type="content" source="media/security-center-pricing/tracking-pricing-changes-in-activity-log.png" alt-text="Explorador de JSON do Log de atividades do Azure":::


### <a name="what-are-the-plans-offered-by-security-center"></a>Quais são os planos oferecidos pela Central de Segurança? 
A Central de Segurança tem duas ofertas: 

- Central de Segurança do Azure gratuita 
- Azure Defender  

### <a name="how-do-i-enable-azure-defender-for-my-subscription"></a>Como fazer para habilitar o Azure Defender para minha assinatura? 
Use uma das seguintes maneiras para habilitar o Azure Defender na sua assinatura: 

| Método                                          | Instruções                                                                                                                                       |
|-------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Páginas da Central de Segurança do Azure do portal do Azure | [Habilitar o Azure Defender](enable-azure-defender.md)                                                                                                  |
| API REST                                        | [API de Preços](/rest/api/securitycenter/pricings)                                                                                                  |
| CLI do Azure                                       | [az security pricing](/cli/azure/security/pricing)                                                                                                 |
| PowerShell                                      | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy                                    | [Preços de pacotes](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|                                                 |                                                                                                                                                    |

### <a name="can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription"></a>Posso habilitar o Azure Defender para servidores em um subconjunto de servidores em minha assinatura?
Não. Quando você habilita o [Azure Defender para servidores](defender-for-servers-introduction.md) em uma assinatura, todos os servidores na assinatura são protegidos pelo Azure Defender. 

Uma alternativa é habilitar o Azure Defender para servidores no nível do workspace do Log Analytics. Se você fizer isso, somente os servidores subordinados a esse workspace serão protegidos e cobrados. No entanto, várias funcionalidades ficarão indisponíveis. Isso inclui acesso just-in-time à VM, detecções de rede, conformidade regulatória, proteção de rede adaptável, controle de aplicativo adaptável, entre outros. 

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Se eu já tenho uma licença para o Microsoft Defender para ponto de extremidade, ganho desconto no Azure Defender?
Se você já tem uma licença para o Microsoft Defender para ponto de extremidade, não precisa pagar por essa parte da sua licença do Azure Defender.

Para confirmar o desconto, entre em contato com a equipe de suporte da Central de Segurança e forneça a ID, a região e as informações de licença relevantes do workspace para cada licença relevante.

### <a name="my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers"></a>Minha assinatura tem o Azure Defender para servidores habilitado. Os servidores que não estão em execução são cobrados? 
Não. Ao habilitar o [Azure Defender para servidores](defender-for-servers-introduction.md) em uma assinatura, você não será cobrado por nenhum computador que esteja no estado de energia desalocado enquanto ele estiver nesse estado. Os computadores são cobrados de acordo com o estado de energia, conforme mostrado na seguinte tabela:

| Estado        | Descrição                                                                                                                                      | Uso de instância cobrado |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Iniciando     | A VM está iniciando.                                                                                                                               | Não é cobrado            |
| Executando      | Estado de funcionamento normal para uma VM                                                                                                                    | Cobrado                |
| Parando     | Esse é um estado de transição. Quando concluído, ele será exibido como Parado.                                                                           | Cobrado                |
| Parado      | A VM foi desligada de dentro do sistema operacional convidado ou usando as APIs PowerOff. O hardware ainda estará alocado para a VM e permanecerá no host. | Cobrado                |
| Desalocando | Estado de transição. Quando concluído, a VM será mostrada como Desalocada.                                                                             | Não é cobrado            |
| Desalocada  | A VM foi parada com êxito e removida do host.                                                                                  | Não é cobrado            |

:::image type="content" source="media/security-center-pricing/deallocated-virtual-machines.png" alt-text="Máquinas virtuais do Azure mostrando um computador desalocado":::

### <a name="will-i-be-charged-for-machines-without-the-log-analytics-agent-installed"></a>Serei cobrado pelos computadores sem o agente do Log Analytics instalado?
Sim. Quando você habilita o [Azure Defender para servidores](defender-for-servers-introduction.md) em uma assinatura, os computadores dessa assinatura obtêm uma gama de proteções, mesmo que você não tenha instalado o agente do Log Analytics.

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice"></a>Se um agente do Log Analytics for subordinado a vários workspaces, serei cobrado duas vezes? 
Sim. Se você tiver configurado o agente do Log Analytics para enviar dados para dois ou mais workspaces diferentes do Log Analytics (hospedagem múltipla), será cobrado por todos os workspaces que tenham soluções de 'Segurança' ou de 'Antimalware' instaladas. 

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them"></a>Se um agente do Log Analytics for subordinado a vários workspaces, a ingestão de dados gratuita de 500 MB estará disponível em todos eles?
Sim. Se você tiver configurado o agente do Log Analytics para enviar dados para dois ou mais workspaces diferentes do Log Analytics (hospedagem múltipla), obterá a ingestão de dados gratuita de 500 MB. Isso é calculado por nó, por workspace relatado e por dia e está disponível para todos os workspaces com soluções de 'Segurança' ou 'Antimalware' instaladas. Você será cobrado por todos os dados ingeridos acima dos 500 MB.

### <a name="is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine"></a>A ingestão de dados gratuita de 500 MB é calculada para um workspace inteiro ou estritamente por computador?
Você receberá a ingestão de dados gratuita de 500 MB por dia para cada computador conectado ao workspace. Especificamente para tipos de dados de segurança coletados diretamente pela Central de Segurança do Azure.

Esses dados são uma taxa diária média em todos os nós. Sendo assim, mesmo que alguns computadores enviem 100 MB e outros enviem 800 MB, se o total não ultrapassar o limite gratuito de **[número de computadores] x 500 MB**, você não precisará pagar nenhum valor extra.

### <a name="what-data-types-are-included-in-the-500-mb-data-daily-allowance"></a>Quais tipos de dados estão incluídos na provisão diária de dados de 500 MB?

A cobrança da Central de Segurança está intimamente ligada à cobrança do Log Analytics. A Central de Segurança fornece uma alocação de 500 MB/nó/dia para o seguinte subconjunto de [tipos de dados de segurança](/azure/azure-monitor/reference/tables/tables-category#security):
- WindowsEvent
- SecurityAlert
- SecurityBaseline
- SecurityBaselineSummary
- SecurityDetection
- SecurityEvent
- WindowsFirewall
- MaliciousIPCommunication
- LinuxAuditLog
- SysmonEvent
- ProtectionStatus
- Os tipos de dados Update e UpdateSummary quando a solução de Gerenciamento de Atualizações não está em execução no workspace ou quando o direcionamento de solução está habilitado

Se o workspace estiver no tipo de preço herdado Por Nó, as alocações da Central de Segurança e do Log Analytics serão combinadas e aplicadas em conjunto a todos os dados ingeridos passíveis de cobrança.

## <a name="next-steps"></a>Próximas etapas
Este artigo explicou as opções de preços da Central de Segurança. Para obter materiais relacionados, confira:

- [Como otimizar os custos da carga de trabalho do Azure](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Detalhes de preços na sua moeda de escolha e de acordo com a sua região](https://azure.microsoft.com/pricing/details/security-center/)
- Talvez você queira gerenciar os custos e limitar a quantidade de dados coletados de uma solução limitando-a a determinado conjunto de agentes. O [direcionamento de solução](../azure-monitor/insights/solution-targeting.md) permite que você aplique um escopo à solução e direcione a um subconjunto de computadores no workspace. Se você estiver usando o direcionamento de solução, a Central de Segurança listará o workspace como não tendo uma solução.
