---
title: Configurar o grupo de disponibilidade (modelo de início rápido do Azure)
description: Use os modelos de início rápido do Azure para criar o cluster de failover do Windows, adicionar as VMs do SQL Server a ele, criar o ouvinte e configurar o balanceador de carga interno no Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: d7dfe010a3f4a1559454c49545af81eb14797bf1
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359907"
---
# <a name="use-azure-quickstart-templates-to-configure-an-availability-group-for-sql-server-on-azure-vm"></a>Usar modelos de início rápido do Azure para configurar um grupo de disponibilidade para o SQL Server em uma VM do Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo descreve como usar os modelos de início rápido do Azure para automatizar parcialmente a implantação de uma configuração de grupo de disponibilidade AlwaysOn para VMs (máquinas virtuais) do SQL Server no Azure. Dois modelos de início rápido do Azure são usados neste processo: 

   | Modelo | Descrição |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Cria o cluster de failover do Windows e adiciona as VMs do SQL Server a ele. |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | Cria o ouvinte do grupo de disponibilidade e configura o balanceador de carga interno. Este modelo pode ser usado somente quando o cluster de failover do Windows é criado com o modelo **101-sql-vm-ag-setup**. |
   | &nbsp; | &nbsp; |

Outras partes da configuração do grupo de disponibilidade devem ser feitas manualmente, como criar o grupo de disponibilidade e criar o balanceador de carga interno. Este artigo fornece a sequência de etapas manuais e automatizadas.

Embora este artigo use os modelos de início rápido do Azure para configurar o ambiente do grupo de disponibilidade, também é possível fazer isso usando o [portal do Azure](availability-group-azure-portal-configure.md), [o PowerShell ou o CLI do Azure](availability-group-az-commandline-configure.md), ou [manualmente](availability-group-manually-configure-tutorial.md) também. 
 

## <a name="prerequisites"></a>Pré-requisitos 
Para automatizar a instalação de um grupo de disponibilidade AlwaysOn usando modelos de início rápido, você deve ter os seguintes pré-requisitos: 
- Uma [assinatura do Azure](https://azure.microsoft.com/free/).
- Um grupo de recursos com um controlador de domínio. 
- Uma ou mais VMs ingressadas [no domínio no Azure que executam o SQL Server 2016 (ou posterior) Enterprise Edition](./create-sql-vm-portal.md) que estão no mesmo conjunto de disponibilidade ou zona de disponibilidade e que foram [registradas com a extensão do SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md).  
- Dois endereços IP disponíveis (não usados por outra entidade): um para o balanceador de carga interno e outro para o ouvinte do grupo de disponibilidade na mesma sub-rede que o grupo de disponibilidade. Se um balanceador de carga existente estiver sendo usado, você precisará de apenas um endereço IP disponível.  

## <a name="permissions"></a>Permissões
As seguintes permissões são necessárias para configurar o grupo de disponibilidade AlwaysOn usando os modelos de início rápido do Azure: 

- Uma conta de usuário de domínio existente com permissão para **Criar objeto de computador** no domínio.  Por exemplo, uma conta de administrador do domínio normalmente tem permissão suficiente (por exemplo: account@domain.com). _Essa conta também deve fazer parte do grupo administrador local em cada VM para criar o cluster._
- A conta de usuário do domínio que controla o SQL Server. 


## <a name="create-cluster"></a>Criar cluster
Depois que as VMs do SQL Server tiverem sido registradas com a extensão do SQL IaaS Agent, você poderá unir suas VMs do SQL Server ao *SqlVirtualMachineGroups*. Esse recurso define os metadados do cluster de failover do Windows. Os metadados incluem a versão, a edição, o nome de domínio totalmente qualificado, as contas do Active Directory para gerenciar o cluster e o SQL Server e a conta de armazenamento como a testemunha em nuvem. 

Adicionar VMs do SQL Server ao grupo de recursos *SqlVirtualMachineGroups* inicializa o Serviço de Cluster de Failover do Windows para criar o cluster e então une essas VMs do SQL Server àquele cluster. Esta etapa é automatizada com o modelo de início rápido **101-sql-vm-ag-setup**. Você pode implementá-lo seguindo as seguintes etapas:

1. Acesse o modelo de início rápido [**101-sql-vm-ag-setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup). Selecione **Implantar no Azure** para abrir o modelo de início rápido no portal do Azure.
1. Preencha os campos obrigatórios para configurar os metadados do cluster de failover do Windows. Você pode deixar os campos opcionais em branco.

   A seguinte tabela mostra os valores necessários para o modelo: 

   | **Campo** | Valor |
   | --- | --- |
   | **Assinatura** |  A assinatura em que estão suas VMs do SQL Server. |
   |**Grupo de recursos** | O grupo de recursos em que suas VMs do SQL Server residem. | 
   |**Nome do cluster de failover** | O nome desejado para o novo cluster de failover do Windows. |
   | **Lista de VMs existentes** | As VMs do SQL Server que devem fazer parte do grupo de disponibilidade e do novo cluster. Separe estes valores com uma vírgula e um espaço (por exemplo: *SQLVM1, SQLVM2*). |
   | **Versão do SQL Server** | A versão do SQL Server de suas VMs do SQL Server. Selecione-a na lista suspensa. Atualmente, há suporte apenas para imagens do SQL Server 2016 e do SQL Server 2017. |
   | **Nome de domínio totalmente qualificado existente** | O FQDN existente para o domínio no qual residem suas VMs do SQL Server. |
   | **Conta de domínio existente** | Uma conta de usuário de domínio existente que tenha permissão para **Criar o objeto de computador** no domínio, pois o [CNO](/windows-server/failover-clustering/prestage-cluster-adds) é criado durante a implantação do modelo. Por exemplo, uma conta de administrador do domínio normalmente tem permissão suficiente (por exemplo: account@domain.com). *Essa conta também deve fazer parte do grupo administrador local em cada VM para criar o cluster.*| 
   | **Senha da conta de domínio** | A senha da conta de usuário de domínio mencionada anteriormente. | 
   | **Conta do serviço SQL existente** | A conta de usuário do domínio que controla o [serviço do SQL Server](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions) durante a implantação do grupo de disponibilidade (por exemplo: account@domain.com). |
   | **Senha de serviço do SQL** | A senha usada pela conta de usuário do domínio que controla o SQL Server. |
   | **Nome de Testemunha de Nuvem** | Uma nova conta de armazenamento do Azure que será criada e usada para a testemunha em nuvem. Você pode modificar esse nome. |
   | **\_Localização de artefatos** | Este campo é definido por padrão e não deve ser modificado. |
   | **\_Token SaS de localização de artefatos** | Esse campo é deixado em branco intencionalmente. |
   | &nbsp; | &nbsp; |

1. Se você concordar com os termos e condições, marque a caixa de seleção **Eu concordo com os termos e condições declarados acima**. Selecione **Comprar** para concluir a implantação do modelo de início rápido. 
1. Para monitorar sua implantação, selecione-a no ícone de sino **Notificações** na faixa de navegação superior ou acesse o **Grupo de recursos** no portal do Azure. Selecione **Implantações** em **Configurações** e escolha a implantação **Microsoft.Template**. 

>[!NOTE]
> As credenciais fornecidas na implantação do modelo são armazenadas apenas durante a implantação. Quando ela termina, as senhas são removidas. Você será solicitado a fornecê-las novamente se adicionar mais VMs do SQL Server ao cluster. 



## <a name="validate-cluster"></a>Validar cluster 

Para que um cluster de failover seja suportado pela Microsoft, ele deve passar na validação do cluster. Conecte-se à VM usando seu método preferido, como protocolo RDP (RDP) e valide se o cluster passa pela validação antes de continuar. Deixar de fazer isso deixa o cluster em um estado sem suporte. 

Você pode validar o cluster usando Gerenciador de Cluster de Failover (FCM) ou o seguinte comando do PowerShell:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```


## <a name="create-availability-group"></a>Criar grupo de disponibilidade 
Crie manualmente o grupo de disponibilidade, como de costume, usando o [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), o [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell) ou o [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> *Não* crie um ouvinte neste momento, pois o modelo de início rápido **101-sql-vm-aglistener-setup** faz isso automaticamente na etapa 4. 

## <a name="create-load-balancer"></a>Criar um balanceador de carga

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

O ouvinte do grupo de disponibilidade AlwaysOn requer uma instância interna do Azure Load Balancer. O balanceador de carga interno fornece um endereço IP "flutuante" para o ouvinte do grupo de disponibilidade, o que permite failover e reconexão mais rápidos. Se as VMs do SQL Server em um grupo de disponibilidade fizerem parte do mesmo conjunto de disponibilidade, você poderá usar um balanceador de carga básico. Caso contrário, precisará usar um balanceador de carga padrão. 

> [!IMPORTANT]
> O balanceador de carga interno deve estar na mesma rede virtual que as instâncias de VM do SQL Server. 

Você apenas precisa criar o balanceador de carga interno. Na etapa 4, o modelo de início rápido **101-sql-vm-aglistener-setup** processa o restante da configuração (como o pool de back-end, a investigação de integridade e as regras de balanceamento de carga). 

1. No Portal do Azure, abra o grupo de recursos que contém as máquinas virtuais do SQL Server. 
2. No grupo de recursos, selecione **Adicionar**.
3. Pesquise pelo **balanceador de carga**. Nos resultados da pesquisa, selecione **Balanceador de carga**, que é publicado pela **Microsoft**.
4. Na folha **Balanceador de carga**, selecione **Criar**.
5. Na caixa de diálogo **Criar balanceador de carga**, configure o balanceador de carga da seguinte maneira:

   | Configuração | Valor |
   | --- | --- |
   | **Nome** |Insira um nome de texto que represente o balanceador de carga. Por exemplo, insira **sqlLB**. |
   | **Tipo** |**Interna**: a maioria das implementações usa um balanceador de carga interno que permite a conexão dos aplicativos na mesma rede virtual ao grupo de disponibilidade.  </br> **Externa**: permite que os aplicativos se conectem ao grupo de disponibilidade por meio de uma conexão à Internet pública. |
   | **Rede virtual** | Selecione a rede virtual na qual estão as instâncias do SQL Server. |
   | **Sub-rede** | Selecione a sub-rede na qual estão as instâncias do SQL Server. |
   | **Atribuição de endereço IP** |**Estático** |
   | **Endereço IP privado** | Especifique um endereço IP disponível na sub-rede. |
   | **Assinatura** |Se você tiver várias assinaturas, este campo poderá aparecer. Selecione a assinatura que você deseja associar a esse recurso. Normalmente, trata-se da mesma assinatura de todos os recursos do grupo de disponibilidade. |
   | **Grupo de recursos** |Selecione o grupo de recursos no qual estão as instâncias do SQL Server. |
   | **Localidade** |Selecione o local do Azure no qual estão as instâncias do SQL Server. |
   | &nbsp; | &nbsp; |

6. Selecione **Criar**. 


>[!IMPORTANT]
> O recurso de IP público de cada VM do SQL Server deve ter uma SKU padrão para ser compatível com o balanceador de carga padrão. Para determinar a SKU do recurso de IP público da sua VM, acesse **Grupo de recursos**, selecione o recurso **Endereço IP público** para a VM do SQL Server desejada e localize o valor em **SKU** no painel **Visão geral**. 

## <a name="create-listener"></a>Criar ouvinte 

Crie o ouvinte do grupo de disponibilidade e configure o balanceador de carga interno automaticamente usando o modelo de início rápido **101-sql-vm-aglistener-setup**. O modelo provisiona o recurso Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/AvailabilityGroupListener. O modelo de início rápido  **101-SQL-VM-aglistener-setup** , por meio da extensão do agente IaaS do SQL, realiza as seguintes ações:

- Cria um novo recurso IP de front-end (com base no valor do endereço IP fornecido durante a implantação) para o ouvinte. 
- Define as configurações de rede para o cluster e o balanceador de carga interno. 
- Configura o pool de back-end para o balanceador de carga interno, a investigação de integridade e as regras de balanceamento de carga.
- Cria o ouvinte do grupo de disponibilidade com o nome e o endereço IP fornecidos.
 
>[!NOTE]
> Você pode usar **101-sql-vm-aglistener-setup** somente se o cluster de failover do Windows foi criado com o modelo **101-sql-vm-ag-setup**.
   
   
Para configurar o balanceador de carga interno e criar o ouvinte do grupo de disponibilidade, faça o seguinte:
1. Acesse o modelo de início rápido [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) e selecione **Implantar no Azure** para iniciar o modelo de início rápido no portal do Azure.
1. Preencha os campos obrigatórios para configurar o balanceador de carga interno e crie o ouvinte do grupo de disponibilidade. Você pode deixar os campos opcionais em branco. 

   A seguinte tabela mostra os valores necessários para o modelo: 

   | **Campo** | Valor |
   | --- | --- |
   |**Grupo de recursos** | O grupo de recursos em que estão suas VMs do SQL Server e o grupo de disponibilidade. | 
   |**Nome do cluster de failover existente** | O nome do cluster em que suas VMs do SQL Server ingressaram. |
   | **Grupo de disponibilidade do SQL existente**| O nome do grupo de disponibilidade de que suas VMs do SQL Server fazem parte. |
   | **Lista de VMs existentes** | Os nomes das VMs do SQL Server que fazem parte do grupo de disponibilidade mencionado anteriormente. Separe os nomes com uma vírgula e um espaço (por exemplo: *SQLVM1, SQLVM2*). |
   | **Ouvinte** | O nome DNS que você deseja atribuir ao ouvinte. Por padrão, esse modelo especifica o nome “aglistener”, mas isso pode ser alterado. O nome não deve exceder 15 caracteres. |
   | **Porta do ouvinte** | A porta que você deseja que o ouvinte use. Em geral, essa porta deve ser 1433 por padrão. Esse é o número da porta que o modelo especifica. Porém, se a porta padrão for alterada, a porta do ouvinte deverá usar esse valor. | 
   | **IP do Ouvinte** | O endereço IP que você deseja que o ouvinte use. Esse endereço será criado durante a implantação do modelo, portanto, forneça um que ainda não esteja em uso.  |
   | **Sub-rede existente** | O nome da sub-rede interna de suas VMs do SQL Server (por exemplo: *padrão*). Você pode determinar esse valor ao acessar o **Grupo de recursos**, selecionar sua rede virtual, selecionar **Sub-redes** no painel **Configurações** e copiar o valor em **Nome**. |
   | **Balanceador de carga interno existente** | O nome do balanceador de carga interno que você criou na etapa 3. |
   | **Porta de investigação** | A porta de investigação que o balanceador de carga interno usará. O modelo usa 59999 por padrão, mas você pode alterar esse valor. |
   | &nbsp; | &nbsp; |

1. Se você concordar com os termos e condições, marque a caixa de seleção **Eu concordo com os termos e condições declarados acima**. Selecione **Comprar** para concluir a implantação do modelo de início rápido. 
1. Para monitorar sua implantação, selecione-a no ícone de sino **Notificações** na faixa de navegação superior ou acesse o **Grupo de recursos** no portal do Azure. Selecione **Implantações** em **Configurações** e escolha a implantação **Microsoft.Template**. 

>[!NOTE]
>Se sua implantação falhar na metade do caminho, você precisará [remover manualmente o ouvinte recém-criado](#remove-listener) usando o PowerShell antes de reimplantar o modelo de início rápido **101-sql-vm-aglistener-setup**. 

## <a name="remove-listener"></a>Remover ouvinte
Se posteriormente você precisar remover o ouvinte do grupo de disponibilidade que o modelo configurou, deverá passar pela extensão do agente IaaS do SQL. Como o ouvinte é registrado por meio da extensão do agente IaaS do SQL, apenas excluí-lo via SQL Server Management Studio é insuficiente. 

O melhor método é excluí-lo por meio da extensão do agente IaaS do SQL usando o trecho de código a seguir no PowerShell. Isso remove os metadados do ouvinte do grupo de disponibilidade da extensão do SQL IaaS Agent. Também exclui fisicamente o ouvinte do grupo de disponibilidade. 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>Erros comuns
Esta seção aborda alguns problemas conhecidos e suas soluções possíveis. 

O **ouvinte do grupo de disponibilidade ' \<AG-Name> ' já existe** o grupo de disponibilidade selecionado usado no modelo de início rápido do Azure para o ouvinte do grupo de disponibilidade já contém um ouvinte. Ele está fisicamente dentro do grupo de disponibilidade ou seus metadados permanecem dentro da extensão do SQL IaaS Agent. Remova o ouvinte usando o [PowerShell](#remove-listener) antes de reimplantar o modelo de início rápido **101-sql-vm-aglistener-setup**. 

A **conexão só funciona da réplica primária** Esse comportamento é provavelmente de uma implantação de modelo **101-SQL-VM-aglistener-setup** com falha que deixou a configuração do balanceador de carga interno em um estado inconsistente. Verifique se o pool de back-end lista o conjunto de disponibilidade, e se há regras para a análise da integridade e o balanceamento de carga. Se algo estiver faltando, a configuração do balanceador de carga interno estará com um estado inconsistente. 

Para resolver esse comportamento, remova o ouvinte usando o [PowerShell](#remove-listener), exclua o balanceador de carga interno no portal do Azure e comece novamente na etapa 3. 

**BadRequest-somente a lista de máquinas virtuais SQL pode ser atualizada** Esse erro pode ocorrer quando você estiver implantando o modelo **101-SQL-VM-aglistener-setup** se o ouvinte tiver sido excluído via SQL Server Management Studio (SSMS), mas não tiver sido excluído da extensão do agente IaaS do SQL. Excluir o ouvinte via SSMS não remove os metadados do ouvinte da extensão do agente IaaS do SQL. O ouvinte deve ser excluído do provedor de recursos por meio do [PowerShell](#remove-listener). 

A **conta de domínio não existe** Esse erro pode ter duas causas. A conta de domínio especificada não existe ou não tem os dados de [Nome UPN](/windows/desktop/ad/naming-properties#userprincipalname). O modelo **101-sql-vm-ag-setup** espera uma conta de domínio no formato UPN (ou seja, user@domain.com), mas algumas contas de domínio podem não ter esse formato. Normalmente, isso ocorre quando um usuário local foi migrado para ser a primeira conta de administrador do domínio quando o servidor foi promovido a controlador de domínio, ou quando um usuário foi criado por meio do PowerShell. 

Verifique se a conta existe. Caso exista, talvez seu problema seja a segunda situação. Para resolver o problema, faça o seguinte:

1. No controlador de domínio, abra a janela **Usuários e Computadores do Active Directory** na opção **Ferramentas** em **Gerenciador do Servidor**. 
2. Acesse a conta selecionando **Usuários** no painel esquerdo.
3. Clique com o botão direito do mouse na conta e selecione **Propriedades**.
4. Selecione a guia **Conta**. Se a caixa **Nome de logon do usuário** estiver em branco, essa será a causa do erro. 

    ![Conta de usuário em branco indica UPN ausente](./media/availability-group-quickstart-template-configure/account-missing-upn.png)

5. Preencha a caixa **Nome de logon do usuário** de modo a corresponder ao nome do usuário e selecione o domínio apropriado na lista suspensa. 
6. Selecione **Aplicar** para salvar suas alterações e fechar a caixa de diálogo selecionando **OK**. 

Depois de fazer essas alterações, tente implantar o modelo de início rápido do Azure mais uma vez. 


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos: 

* [Visão geral das VMs do SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Perguntas frequentes para VMs do SQL Server](frequently-asked-questions-faq.md)
* [Diretrizes de preços para VMs do SQL Server](pricing-guidance.md)
* [Notas de versão para VMs do SQL Server](../../database/doc-changes-updates-release-notes.md)
* [Alternar modelos de licenciamento para uma VM do SQL Server](licensing-model-azure-hybrid-benefit-ahb-change.md)