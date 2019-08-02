---
title: Coleta de dados na Central de Segurança do Azure | Microsoft Docs
description: " Saiba como habilitar a coleta de dados na Central de Segurança do Azure. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/06/2019
ms.author: v-mohabe
ms.openlocfilehash: 86d48360e37f26d19d15c62a8109c030d421c661
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551896"
---
# <a name="data-collection-in-azure-security-center"></a>Coleta de dados na Central de Segurança do Azure
A Central de segurança coleta dados de suas máquinas virtuais (VMs), conjuntos de dimensionamento de máquinas virtuais, contêineres de IaaS e computadores não Azure (incluindo local) para monitorar as ameaças e vulnerabilidades de segurança. Os dados são coletados usando o Microsoft Monitoring Agent, que lê várias configurações e logs de eventos relacionados à segurança do computador e copia os dados em seu workspace para serem analisados. Exemplos desses dados são: tipo e versão do sistema operacional, logs do sistema operacional (logs de eventos do Windows), processos em execução, nome do computador, endereços IP e usuário conectado. O agente Microsoft Monitoring Agent também copia os arquivos de despejo para seu espaço de trabalho.

A coleta de dados é necessária para fornecer visibilidade sobre atualizações ausentes, definições de segurança do sistema operacional configuradas incorretamente, habilitação da proteção de ponto de extremidade e detecções de ameaças e da integridade. 

Este artigo fornece diretrizes sobre como instalar um Microsoft Monitoring Agent e definir um espaço de trabalho do Log Analytics no qual armazenar os dados coletados. As duas operações são necessárias para habilitar a coleta de dados. 

> [!NOTE]
> - Coleta de dados é necessário apenas para recursos de computação (máquinas virtuais, conjuntos de dimensionamento de máquinas virtuais, contêineres de IaaS e computadores não Azure). Você pode aproveitar a Central de Segurança do Azure mesmo se não provisionar agentes, no entanto, a segurança será limitada e as funcionalidades listadas acima não terão suporte.  
> - Para ver a lista das plataformas compatíveis, consulte [Plataformas compatíveis com a Central de Segurança do Azure](security-center-os-coverage.md).
> - Armazenamento de dados no Log Analytics, independentemente de você usar um espaço de trabalho novo ou existente, pode incorrer em encargos adicionais para o armazenamento de dados. Para obter mais detalhes, consulte o [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

## Habilitar provisionamento automático do Microsoft Monitoring Agent <a name="auto-provision-mma"></a>

Para coletar os dados de computadores, você deve ter o Microsoft Monitoring Agent instalado.  Instalação do agente pode ser feita automaticamente (recomendado) ou você pode instalar o agente manualmente.  

>[!NOTE]
> O provisionamento automático é desativado por padrão. Para configurar a Central de Segurança para instalar o provisionamento automático por padrão, defina-o como **Ativado**.
>

Quando o provisionamento automático está ativado, a Central de segurança provisiona o Microsoft Monitoring agent em todas as VMs do Azure e quaisquer novas VMs que são criados. O provisionamento automático é altamente recomendável, mas a instalação manual do agente também está disponível. [Saiba como instalar a extensão do Microsoft Monitoring Agent](#manualagent).



Para habilitar o provisionamento automático do Microsoft Monitoring Agent:
1. Sob o menu principal da Central de segurança, selecione **preços & configurações**.
2. Clique na assinatura aplicável

   ![Escolha a assinatura][7]

3. Selecione **coleta de dados**.
4. Em **Provisionamento Automático**, selecione **Ativar** para habilitar o provisionamento automático.
5. Clique em **Salvar**.

   ![Habilitar o provisionamento automático][1]

>[!NOTE]
> - Para obter instruções sobre como provisionar uma instalação já existente, veja [Provisionamento automático em caso de uma instalação de agente pré-existente](#preexisting).
> - Para ver as instruções de provisionamento manual, consulte [Instalar a extensão do Microsoft Monitoring Agent manualmente](#manualagent).
> - Para ver as instruções sobre como desativar o provisionamento automático, consulte [Desativar o provisionamento automático](#offprovisioning).
> - Para obter instruções sobre como integrar a Central de Segurança usando o PowerShell, consulte [Automatizar a integração da Central de Segurança do Azure usando o PowerShell](security-center-powershell-onboarding.md).
>

## <a name="workspace-configuration"></a>Configuração do workspace
Os dados coletados pela Central de Segurança são armazenados nos espaços de trabalho do Log Analytics existentes.  Você pode optar por ter os dados coletados de VMs do Azure armazenados em workspaces criados pela Central de Segurança ou em um workspace existente que você criou. 

A configuração do workspace é definida por assinatura, e várias assinaturas podem usar o mesmo workspace.

### <a name="using-a-workspace-created-by-security-center"></a>Usar workspaces criados pela Central de Segurança

A Central de Segurança pode criar automaticamente um workspace padrão no qual armazenar os dados. 

Para selecionar um workspace criados pela Central de Segurança:

1. Em **Configuração do workspace padrão**, selecione Usar workspaces criados pela Central de Segurança.
   ![Selecione o tipo de preço][10] 

1. Clique em **Salvar**.<br>
    A Central de Segurança criará um novo grupo de recursos e um workspace padrão nessa geolocalização e conectará o agente a esse workspace. A convenção de nomenclatura para o grupo de recursos e o workspace é:<br>
   **Workspace: DefaultWorkspace-[ID da assinatura]-[localização geográfica]<br> Grupo de recursos: DefaultResourceGroup-[geo]**

   Se uma assinatura contém VMs de várias localizações, a Central de Segurança cria vários workspaces. Vários workspaces são criados para manter as regras de privacidade de dados.
1. A Central de segurança habilitará automaticamente uma solução da Central de Segurança no workspace por tipo de preço definido para a assinatura. 

> [!NOTE]
> O tipo de preço do Log Analytics para workspaces criados pela Central de Segurança não afeta a cobrança da Central de Segurança. A cobrança da Central de Segurança sempre se baseia na sua política de segurança da Central de Segurança e nas soluções instaladas em um workspace. Para a Camada gratuita, a Central de Segurança instala a solução *SecurityCenterFree* no workspace padrão. Para a Camada Standard, a Central de Segurança habilita a solução *Security* no workspace padrão.
> Armazenamento de dados no Log Analytics pode incorrer em encargos adicionais para o armazenamento de dados. Para obter mais detalhes, consulte o [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

Para obter mais informações sobre as contas existentes do log analytics, consulte [os clientes de análise de log existente](security-center-faq.md#existingloganalyticscust).

### <a name="using-an-existing-workspace"></a>Usar um workspace existente

Se você já tiver um espaço de trabalho do Log Analytics existente, convém usar esse mesmo espaço de trabalho.

Para usar o espaço de trabalho do Log Analytics existente, você precisará ter permissões de leitura e gravação no espaço de trabalho.

> [!NOTE]
> Soluções habilitadas no workspace existente serão aplicadas às VMs do Azure que estão conectados a ele. Para soluções pagas, isso pode resultar em cobranças adicionais. Por questões de considerações de privacidade de dados, verifique se seu workspace selecionado está na região geográfica correta.
> Armazenamento de dados no log analytics pode incorrer em encargos adicionais para o armazenamento de dados. Para obter mais detalhes, consulte o [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

Para selecionar um espaço de trabalho do Log Analytics existente:

1. Em **Configuração do workspace padrão**, selecione **Usar outro workspace**.

   ![Selecionar um workspace existente][2]

2. No menu suspenso, selecione um workspace para armazenar os dados coletados.

   > [!NOTE]
   > No menu suspenso, todos os workspaces em todas as suas inscrições estão disponíveis. Consulte a [seleção de workspace de assinatura cruzada](security-center-enable-data-collection.md#cross-subscription-workspace-selection) para obter mais informações. Você precisa de permissão para acessar o workspace.
   >
   >

3. Clique em **Salvar**.
4. Após selecionar **Salvar**, será perguntado se você deseja reconfigurar as VMs monitoradas que estavam conectadas ao workspace padrão anteriormente.

   - Selecione **Não** se quiser que as novas configurações de workspace sejam aplicadas somente às novas VMs. As novas configurações de workspace se aplicam somente a novas instalações de agente: VMs recém-descobertas que não têm o Microsoft Monitoring Agent instalado.
   - Selecione **Sim** se quiser que as novas configurações de workspace sejam aplicadas a todas as VMs. Além disso, todas as VMs conectadas a um workspace criado da Central de Segurança serão reconectadas ao novo workspace de destino.

   > [!NOTE]
   > Se selecionar Sim, você não deverá excluir os workspaces criados pela Central de Segurança até que todas as VMs sejam reconectadas ao novo workspace de destino. Essa operação falhará se um workspace for excluído muito cedo.
   >
   >

   - Selecione **Cancelar** para cancelar a operação.

     ![Selecionar um workspace existente][3]

5. Selecione o tipo de preço para o workspace desejado que você pretende definir no Microsoft Monitoring Agent. <br>Para usar um workspace existente, defina o tipo de preço do workspace. Isso instalará uma solução da Central de Segurança no workspace, se ainda não existir.

    a.  No menu principal do Central de segurança, selecione **preços & configurações**.
     
    b.  Selecione o workspace desejado ao qual você pretende conectar o agente.
        ![Selecione o workspace][7] c. Defina o tipo de preço.
        ![Selecione o tipo de preço][9]
   
   >[!NOTE]
   >Se o workspace já tiver uma solução de **Segurança** ou **SecurityCenterFree** habilitada, o preço será definido automaticamente. 

## <a name="cross-subscription-workspace-selection"></a>Seleção de workspace entre assinaturas
Ao selecionar um workspace para armazenar os dados, todos os workspaces em todas as assinaturas estarão disponíveis. A seleção do workspace entre assinaturas permite coletar dados de máquinas virtuais em execução em assinaturas diferentes e armazená-los no workspace de sua preferência. Essa seleção será útil se você utilizar um workspace centralizado na sua organização e quiser usá-lo para coleta de dados de segurança. Para obter mais informações sobre como gerenciar workspaces, consulte [Gerenciar o acesso ao workspace](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access).


## <a name="data-collection-tier"></a>Camada de coleta de dados
A seleção de uma camada de coleta de dados na Central de Segurança do Azure afetará apenas o armazenamento de eventos de segurança no espaço de trabalho do Log Analytics. O agente do Log Analytics ainda será colete e analise os eventos de segurança necessários para detecções de ameaças da Central de segurança do Azure, independentemente de qual camada de eventos de segurança que você optar por armazenar em seu espaço de trabalho do Log Analytics (se houver). A escolha de armazenar eventos de segurança na área de trabalho permitirá investigar, pesquisar e auditar esses eventos na área de trabalho. 
> [!NOTE]
> Armazenamento de dados no log analytics pode incorrer em encargos adicionais para o armazenamento de dados. Para obter mais detalhes, consulte o [página de preços](https://azure.microsoft.com/pricing/details/security-center/).
> 
> É possível escolher a diretiva de filtragem correta para assinaturas e espaços de trabalho de quatro conjuntos de eventos a serem armazenados na área de trabalho: 

- **Nenhum** – Desabilitar armazenamento de eventos de segurança. Esta é a configuração padrão.
- **Mínimo** – um conjunto menor de eventos, para clientes que desejam minimizar o volume de eventos.
- **Comum** – este é um conjunto de eventos que satisfaz a maioria dos clientes, garantindo uma trilha de auditoria completa.
- **Todos os eventos** – Para clientes que querem garantir que todos os eventos sejam armazenados.


> [!NOTE]
> Esses conjuntos de eventos de segurança estão disponíveis apenas na Camada Standard da Central de Segurança. Confira os [Preços](security-center-pricing.md) para saber mais sobre os tipos de preço da Central de Segurança.
Esses conjuntos foram projetados para lidar com cenários típicos. Avalie qual atende às suas necessidades antes de implementá-los.
>
>

Para determinar os eventos que farão parte dos conjuntos de eventos **Comum** e **Mínimo**, trabalhamos com clientes e padrões do setor para saber mais sobre a frequência não filtrada de cada evento e seu uso. Neste processo, usamos as seguintes diretrizes:

- **Mínimo** – Certificar-se de que esse conjunto aborde apenas os eventos que possam indicar uma violação com êxito e eventos importantes que tenham um volume muito baixo. Por exemplo, este conjunto contém logon usuário bem-sucedidas e com falha (evento IDs 4624 e 4625), mas ele não contém um sinal de saída que é importante para a auditoria, mas não é significativo para a detecção e tem um volume relativamente alto. A maior parte do volume de dados desse conjunto é dos eventos de logon e do evento de criação de processo (ID de evento 4688).
- **Comum** – fornecer uma trilha de auditoria de usuário completa neste conjunto. Por exemplo, este conjunto contém logons de usuário e detalhes de logon do usuário (ID de evento 4634). Incluímos ações de auditoria, como alterações de grupo de segurança, operações Kerberos do controlador de domínio de chave e outros eventos que são recomendados por organizações do setor.

Eventos que têm um volume muito baixo foram incluídos no conjunto Comum, pois a principal motivação para escolhê-los dentre todos os eventos era reduzir o volume e não filtrar eventos específicos.

Aqui está um detalhamento completo das IDs de eventos de Segurança e do AppLocker para cada conjunto:

| Camada de dados | Indicadores de eventos coletados |
| --- | --- |
| Mínimo | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Comum | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - Se você está usando o GPO (Objeto de Política de Grupo), é recomendável habilitar o Evento de Criação de Processo 4688 das políticas de auditoria, bem como o campo *CommandLine* dentro do evento 4688. Para obter mais informações sobre o Processo de Criação do Evento 4688, consulte as [Perguntas Frequentes](security-center-faq.md#what-happens-when-data-collection-is-enabled) da Central de Segurança. Para obter mais informações sobre essas políticas de auditoria, consulte [Recomendações de Política de Auditoria](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Para habilitar a coleta de dados para [Controles de Aplicativo Adaptáveis](security-center-adaptive-application.md), a Central de Segurança configura uma política do AppLocker local no modo de Auditoria para permitir todos os aplicativos. Isso fará com que o AppLocker gere eventos que são coletados e aproveitados pela Central de Segurança. É importante observar que essa política não será configurada em computadores nos quais já houver uma política do AppLocker configurada. 
> - Para coletar a Plataforma para Filtros do Windows [ID de evento 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156), será preciso habilitar a [Conexão da Plataforma para Filtros de Auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol /set /subcategory:"Filtering Platform Connection" /Success:Enable)
>

Para escolher a política de filtragem:
1. Sobre o **coleta de dados** , selecione a política de filtragem em **eventos de segurança**.
2. Clique em **Salvar**.

   ![Escolher a política de filtragem][5]

### Provisionamento automático em caso de uma instalação de agente pré-existente <a name="preexisting"></a> 

Os casos de uso a seguir especificam como o provisionamento automático funciona quando já existe um agente ou extensão instalados. 

- Microsoft Monitoring Agent estiver instalado no computador, mas não como uma extensão (agente direto)<br>
Se o Microsoft Monitoring Agent estiver instalado diretamente na VM (não como uma extensão do Azure), a Central de segurança instalará a extensão Microsoft Monitoring Agent e pode atualizar o Microsoft Monitoring agent para a versão mais recente.
O agente instalado continuará reportando para seus espaços de trabalho já configurados e Além disso, irá relatar ao espaço de trabalho configurado na Central de segurança (hospedagem múltipla é suportada em computadores Windows).
Se o espaço de trabalho configurado é um espaço de trabalho do usuário (não a Central de segurança espaço de trabalho padrão), você precisará instalar o "segurança / solução"securityFree"nele para a Central de segurança iniciar o processamento de eventos de VMs e computadores relatando para esse espaço de trabalho.<br>
<br>
Para máquinas Linux, múltipla Agent ainda não tiver sido suporte – portanto, se uma instalação de agente existente for detectada, o provisionamento automático não ocorrerá e a configuração do computador não será alterada.
<br>
Para máquinas existentes em integrado de assinaturas para a Central de segurança antes de 2019-03-17, quando um agente existente será detectado, a extensão Microsoft Monitoring Agent não será instalada e a máquina não será afetada. Para esses computadores, consulte a recomendação "Resolver problemas de integridade do agente em seus computadores de monitoramento" para resolver os problemas de instalação do agente nessas máquinas.

  
- Agente do System Center Operations Manager está instalado no computador<br>
A Central de segurança será instalado a Microsoft Monitoring Agent extensão lado a lado para o Operations Manager existente. O agente do Operations Manager existente continuará reportando ao servidor do Operations Manager normalmente. Observe que o agente do Operations Manager e o Microsoft Monitoring Agent compartilham bibliotecas comuns do tempo de execução, que serão atualizadas para a versão mais recente durante esse processo.
Observação: se a versão 2012 do agente do Operations Manager estiver instalado, **não** ativar automática de provisionamento no.<br>

- Já há uma extensão de VM existente<br>
    - Quando o agente de monitoramento é instalado como uma extensão, a configuração de extensão permite relatar a apenas um espaço de trabalho. A Central de Segurança não substitui as conexões existentes nos workspaces do usuário. A Central de segurança armazenará dados de segurança da VM no espaço de trabalho já está conectado, desde que a solução de "securityFree" ou "segurança" foi instalada nele. A Central de segurança pode atualizar a versão da extensão para a versão mais recente neste processo.  
    - Para ver para qual workspace a extensão existente está enviando dados, execute o teste para [Validar a conectividade com a Central de Segurança do Azure](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/). Como alternativa, você pode abrir os espaços de trabalho do Log Analytics, selecione um espaço de trabalho, selecione a VM e examinar a conexão de agente do Log Analytics. 
    - Se você tiver um ambiente onde o agente do Log Analytics está instalado nas estações de trabalho do cliente e emissão de relatórios para um espaço de trabalho do Log Analytics existente, examine a lista de [sistemas operacionais com suporte pela Central de segurança do Azure](security-center-os-coverage.md) para certificar-se há suporte para seu sistema operacional e ver [os clientes de análise de log existente](security-center-faq.md#existingloganalyticscust) para obter mais informações.
 
### Desativar o provisionamento automático<a name="offprovisioning"></a>
É possível desativar o provisionamento automático de recursos a qualquer momento desativando essa configuração na política de segurança. 


1. Retorne ao menu principal da Central de Segurança e selecione a Política de segurança.
2. Clique em **editar as configurações** na linha da assinatura para o qual você deseja desabilitar o provisionamento automático.
3. Na folha **Política de Segurança – Coleta de Dados** em **Provisionamento Automático** selecione **Desativar**.
4. Clique em **Salvar**.

   ![Desabilitar o provisionamento automático][6]

Quando o provisionamento automático está desabilitado (desativado), a seção de configuração do workspace padrão não é exibida.

Se você desativar o provisionamento automático que estava ativado anteriormente:
-   Os agentes não serão provisionados em novas VMs.
-   A Central de Segurança interromperá a coleta de dados do workspace padrão.
 
> [!NOTE]
>  Desabilitar o provisionamento automático não remove o Microsoft Monitoring Agent das VMs do Azure nas quais o agente foi provisionado. Para obter informações sobre como remover a extensão do OMS, consulte [Como fazer para remover extensões OMS instaladas pela Central de Segurança](security-center-faq.md#remove-oms).
>
    
## Provisionamento manual de agente <a name="manualagent"></a>
 
Há diversas maneiras de instalar o Microsoft Monitoring Agent manualmente. Verifique se o provisionamento automático está desabilitado ao efetuar a instalação manual.

### <a name="operations-management-suite-vm-extension-deployment"></a>Implantação de extensão de VM do Operations Management Suite 

É possível instalar manualmente o Microsoft Monitoring Agent para que a Central de Segurança possa coletar dados de segurança das suas VMs e faça recomendações e alertas.
1. Selecione o Provisionamento automático – Desativado.
2. Crie um workspace e defina o tipo de preço para o workspace que você pretende definir no Microsoft Monitoring Agent:

   a.  No menu principal da Central de Segurança, selecione **Política de segurança**.
     
   b.  Selecione o Workspace ao qual você pretende conectar o agente. Verifique se que o workspace está na mesma assinatura usada na Central de Segurança e se você tem permissões de leitura/gravação no workspace.
       ![Selecione o workspace][8]
3. Defina o tipo de preço.
   ![Selecione o tipo de preço][9] 
   >[!NOTE]
   >Se o workspace já tiver uma solução de **Segurança** ou **SecurityCenterFree** habilitada, o preço será definido automaticamente. 
   > 

4. Se você quiser implantar os agentes em novas VMs usando um modelo do Resource Manager, instale a extensão de máquina virtual do OMS:

   a.  [Instalar a extensão da máquina virtual do OMS para Windows](../virtual-machines/extensions/oms-windows.md)
    
   b.  [Instalar a extensão da máquina virtual do OMS para Linux](../virtual-machines/extensions/oms-linux.md)
5. Para implantar as extensões em VMs existentes, siga as instruções em [Coletar dados sobre Máquinas Virtuais do Microsoft Azure](../azure-monitor/learn/quick-collect-azurevm.md).

   > [!NOTE]
   > A seção **Coletar dados de desempenho e eventos** é opcional.
   >
6. Para usar o PowerShell e implantar a extensão, utilize o exemplo a seguir do PowerShell:
   
   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
   1. Acesse o **Log Analytics** e clique em **Configurações avançadas**.
    
      ![Definir o Log Analytics][11]

   2. Copie os valores de **WorkspaceID** e **Chave primária**.
  
      ![Copiar valores][12]

   3. Popule a configuração pública e a configuração privada com estes valores:
     
           $PublicConf = '{
               "workspaceId": "WorkspaceID value"
           }' 
 
           $PrivateConf = '{
               "workspaceKey": "<Primary key value>”
           }' 

      - Ao instalar em uma VM do Windows:
        
            Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "MicrosoftMonitoringAgent" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True 
    
      - Ao instalar em uma VM do Linux:
        
            Set-AzVMExtension -ResourceGroupName $vm1.ResourceGroupName -VMName $vm1.Name -Name "OmsAgentForLinux" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "OmsAgentForLinux" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True`

> [!NOTE]
> Para obter instruções sobre como integrar a Central de Segurança usando o PowerShell, consulte [Automatizar a integração da Central de Segurança do Azure usando o PowerShell](security-center-powershell-onboarding.md).

## <a name="troubleshooting"></a>solução de problemas

-   Para identificar problemas de instalação do provisionamento automático, consulte [Problemas de integridade do agente de monitoramento](security-center-troubleshooting-guide.md#mon-agent).

-  Para identificar os requisitos de rede do agente de monitoramento, veja [Solução de problemas dos requisitos de rede do agente de monitoramento](security-center-troubleshooting-guide.md#mon-network-req).
-   Para identificar problemas de integração manual, consulte [Como solucionar problemas de integração do Operations Management Suite](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

- Para identificar problemas de computadores e VMs sem monitoramento, consulte [Computadores e VMs sem monitoramento](security-center-virtual-machine-protection.md#unmonitored-vms-and-computers).

## <a name="next-steps"></a>Próximas etapas
Este artigo mostrou como a coleta de dados e o provisionamento automático na Central de Segurança funcionam. Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre perguntas frequentes sobre como usar o serviço.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md)– saiba como monitorar a integridade dos recursos do Azure.



<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png
[7]: ./media/security-center-enable-data-collection/select-subscription.png
[8]: ./media/security-center-enable-data-collection/manual-provision.png
[9]: ./media/security-center-enable-data-collection/pricing-tier.png
[10]: ./media/security-center-enable-data-collection/workspace-selection.png
[11]: ./media/security-center-enable-data-collection/log-analytics.png
[12]: ./media/security-center-enable-data-collection/log-analytics2.png
