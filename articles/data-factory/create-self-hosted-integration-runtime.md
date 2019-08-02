---
title: Criar um tempo de execução da integração auto-hospedada no Azure Data Factory | Microsoft Docs
description: Saiba como criar um tempo de execução da integração auto-hospedada no Azure Data Factory, que permite que os data factories acessem armazenamentos de dados em uma rede privada.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/18/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 2c90dcf1672a3d3505aaa19aec953ad97f5289bb
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446211"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Criar e configurar um tempo de execução da integração auto-hospedada
O IR (Integration Runtime) é a infraestrutura de computação usada pelo Azure Data Factory para fornecer funcionalidades de integração de dados entre diferentes ambientes de rede. Para obter detalhes sobre o IR, confira [Visão geral do Integration Runtime](concepts-integration-runtime.md).

Um tempo de execução da integração auto-hospedada consegue executar as atividades de cópia entre um armazenamento de dados de nuvem e um armazenamento de dados na rede privada, além de expedir atividades de transformação em recursos de computação em uma rede local ou em uma rede virtual do Azure. A instalação de um tempo de execução da integração auto-hospedada precisa de uma VM (máquina virtual) ou de um computador local dentro de uma rede privada.  

Este documento descreve como você pode criar e configurar o IR auto-hospedado.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="high-level-steps-to-install-a-self-hosted-ir"></a>Etapas de alto nível para instalar um IR auto-hospedado
1. Criar um tempo de execução de integração auto-hospedado. É possível usar a interface do usuário do Azure Data Factory para esta tarefa. Aqui está um exemplo do PowerShell:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Faça o download](https://www.microsoft.com/download/details.aspx?id=39717) e instale o tempo de execução da integração auto-hospedada em um computador local.

3. Recupere a chave de autenticação e registre o tempo de execução da integração auto-hospedada com ela. Aqui está um exemplo do PowerShell:

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

## <a name="setting-up-a-self-hosted-ir-on-an-azure-vm-by-using-an-azure-resource-manager-template"></a>Como configurar um IR auto-hospedado em uma VM do Azure usando um modelo do Azure Resource Manager 
É possível automatizar a configuração do IR auto-hospedado em uma máquina virtual do Azure usando [este modelo do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime). O modelo fornece uma maneira fácil de ter um IR auto-hospedado totalmente funcional dentro da rede virtual do Azure com recursos de alta disponibilidade e escalabilidade (contanto que você defina a contagem de nós como 2 ou superior).

## <a name="command-flow-and-data-flow"></a>Fluxo de comando e fluxo de dados
Quando você move os dados entre a nuvem e o local, a atividade usa um tempo de execução da integração auto-hospedada para transferir os dados da fonte de dados local para a nuvem e vice-versa.

Aqui está o fluxo de dados de alto nível para e o resumo das etapas para a cópia com um IR auto-hospedado:

![Visão geral de alto nível](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. O desenvolvedor de dados cria um tempo de execução da integração auto-hospedada dentro de um Azure Data Factory usando o cmdlet do PowerShell. Atualmente, o Portal do Azure não dá suporte a esse recurso.
2. O desenvolvedor de dados cria um serviço vinculado para um armazenamento de dados local especificando a instância do tempo de execução da integração auto-hospedada que ele deve usar para se conectar a armazenamentos de dados.
3. O nó do tempo de execução da integração auto-hospedada criptografa a credencial usando a DPAPI (Interface de Programação do Aplicativo de Proteção de Dados) do Windows e salva as credenciais localmente. Se vários nós forem definidos para alta disponibilidade, as credenciais serão mais sincronizadas em outros nós. Cada nó criptografa as credenciais usando a DPAPI e as armazena localmente. A sincronização de credenciais é transparente para o desenvolvedor de dados e é tratada pelo IR auto-hospedado.    
4. O serviço Data Factory se comunica com o tempo de execução de integração auto-hospedado para agendamento e gerenciamento de trabalhos por meio de um *canal de controle* que usa um compartilhado [retransmissão do barramento de serviço do Azure](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it#wcf-relay). Quando um trabalho de atividade precisa ser executado, o Data Factory enfileira a solicitação juntamente com quaisquer informações de credenciais (no caso de as credenciais ainda não estarem armazenadas no Integration Runtime auto-hospedado). O tempo de execução da integração auto-hospedada inicia o trabalho depois da sondagem da fila.
5. O tempo de execução da integração auto-hospedada copia dados de um repositório local para um armazenamento na nuvem, ou vice-versa, dependendo de como a atividade de cópia estiver configurada no pipeline de dados. Para esta etapa, o tempo de execução da integração auto-hospedada se comunica diretamente com os serviços de armazenamento baseado em nuvem, como Armazenamento de Blobs do Azure por um canal seguro (HTTPS).

## <a name="considerations-for-using-a-self-hosted-ir"></a>Considerações para o uso de um IR auto-hospedado

- Um único Integration Runtime auto-hospedado pode ser usado para várias fontes de dados locais. Um único tempo de execução da integração auto-hospedada pode ser compartilhado com outro data factory no mesmo locatário do Azure Active Directory. Para obter mais informações, confira [Compartilhando um tempo de execução da integração auto-hospedada](#sharing-the-self-hosted-integration-runtime-with-multiple-data-factories).
- Você pode ter apenas uma instância do tempo de execução da integração auto-hospedada instalada em um único computador. Se você tiver dois data factories que precisam acessar fontes de dados local, ou use o [recurso de compartilhamento do IR auto-hospedado](#sharing-the-self-hosted-integration-runtime-with-multiple-data-factories) compartilham o tempo de execução de integração auto-hospedado ou instalar o integration runtime auto-hospedado em dois computadores locais, um para cada data factory.  
- O tempo de execução da integração auto-hospedada não precisa estar no mesmo computador que a fonte de dados. No entanto, ter o tempo de execução da integração auto-hospedada mais perto da fonte de dados reduz o tempo para o tempo de execução da integração auto-hospedada se conectar à fonte de dados. É recomendável instalar o Integration Runtime auto-hospedado em um computador que seja diferente daquele que hospeda a fonte de dados local. Quando a fonte de dados e o tempo de execução da integração auto-hospedada estão em computadores diferentes, o tempo de execução da integração auto-hospedada não compete por recursos com a fonte de dados.
- Você pode ter vários tempos de execução da integração auto-hospedada em diferentes computadores conectados à mesma fonte de dados local. Por exemplo, pode ter dois tempos de execução da integração auto-hospedada servindo dois data factories, mas a mesma fonte de dados local é registrada com ambos os data factories.
- Se você já tiver um gateway instalado no computador para atender um cenário do Power BI, instale um tempo de execução da integração auto-hospedada separado para o Azure Data Factory em outro computador.
- O tempo de execução da integração auto-hospedada deve ser usado para dar suporte à integração de dados na rede virtual do Azure.
- Trate a fonte de dados como uma fonte de dados local protegida por um firewall mesmo quando você usar o Microsoft Azure ExpressRoute. Use o Integration Runtime auto-hospedado para estabelecer a conectividade entre o serviço e a fonte de dados.
- Você deverá usar o tempo de execução da integração auto-hospedada mesmo se o armazenamento de dados estiver na nuvem em uma máquina virtual de IaaS do Azure.
- As tarefas podem falhar em um tempo de execução da integração auto-hospedada instalado em um Windows Server no qual a criptografia em conformidade com FIPS está habilitada. Para contornar esse problema, desabilite a criptografia em conformidade com FIPS no servidor. Para desabilitar a criptografia compatível com FIPS, altere o valor de registro de 1 (habilitado) a seguir para 0 (desabilitado): `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`.

## <a name="prerequisites"></a>Pré-requisitos

- As versões de sistema operacional com suporte são Windows 7 Service Pack 1, Windows 8.1, Windows 10, Windows Server 2008 R2 SP1, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016 e Windows Server 2019. A instalação do tempo de execução da integração auto-hospedada em um controlador de domínio não tem suporte.
- É necessário o .NET Framework 4.6.1 ou posterior. Se você estiver instalando o tempo de execução da integração auto-hospedada em um computador com Windows 7, instale o .NET Framework 4.6.1 ou posterior. Confira [Requisitos de sistema do .NET Framework](/dotnet/framework/get-started/system-requirements) para obter detalhes.
- A configuração recomendada para o computador do tempo de execução da integração auto-hospedada é de, no mínimo, 2 GHz, quatro núcleos, 8 GB de RAM e um disco de 80 GB.
- Se o computador host hibernar, o Integration Runtime auto-hospedado não responderá às solicitações de dados. Configure um plano de energia adequado no computador antes de instalar o tempo de execução da integração auto-hospedada. Se o computador estiver configurado para hibernar, a instalação do Integration Runtime auto-hospedado exibirá uma mensagem.
- Você deve ser um administrador no computador local para instalar e configurar com êxito o Integration Runtime auto-hospedado.
- Execuções de atividade de cópia ocorrem em uma frequência específica. O uso de recursos (CPU, memória) no computador segue o mesmo padrão em horários ociosos e de pico. A utilização de recursos também depende muito da quantidade de dados sendo movida. Quando vários trabalhos de cópia estiverem em andamento, você verá o uso do recurso aumentar durante horários de pico.

## <a name="installation-best-practices"></a>Melhores práticas de instalação
É possível instalar o tempo de execução da integração auto-hospedada baixando um pacote de instalação MSI no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Veja o artigo [Mover dados entre locais e a nuvem](tutorial-hybrid-copy-powershell.md) para obter instruções passo a passo.

- Configure um plano de energia no computador host para o tempo de execução da integração auto-hospedada para que o computador não hiberne. Se o computador host hibernar, o tempo de execução da integração auto-hospedada ficará offline.
- Faça backup das credenciais associadas ao Integration Runtime auto-hospedado regularmente.
- Para automatizar o IR auto-hospedado operações de configuração, consulte [abaixo da seção](#automation-support-for-self-hosted-ir-function).  

## <a name="install-and-register-self-hosted-ir-from-the-download-center"></a>Instalar e registrar o IR auto-hospedado no Centro de Download

1. Acesse a [página de downloads do Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717).
2. Selecione **Baixar**, selecione a versão de 64 bits (a de 32 bits não é compatível) e selecione **Avançar**.
3. Execute o arquivo MSI diretamente ou salve-o em seu disco rígido e execute-o.
4. Na página de **Boas-vindas**, selecione um idioma e selecione **Avançar**.
5. Aceite os Termos de Licença para Software Microsoft e selecione **Avançar**.
6. Selecione **pasta** para instalar o tempo de execução da integração auto-hospedada e selecione **Avançar**.
7. Na página **Pronto para instalar**, selecione **Instalar**.
8. Clique em **Concluir** para finalizar a instalação.
9. Obtenha a chave de autenticação usando o Azure PowerShell. Aqui há um exemplo do PowerShell para recuperar a chave de autenticação:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. Na página **Registrar Integration Runtime (auto-hospedado)** do Configuration Manager do Microsoft Integration Runtime em seu computador, realize as seguintes etapas:

    a. Cole a chave de autenticação na área de texto.

    b. Opcionalmente, selecione **Mostrar chave de autenticação** para ver o texto da chave.

    c. Selecione **Registrar**.

## <a name="automation-support-for-self-hosted-ir-function"></a>Função IR auto-hospedado do suporte de automação para


> [!NOTE]
> Se você estiver planejando configurar o IR auto-hospedado em uma máquina Virtual do Azure e deseja automatizar a instalação usando modelos do Azure Resource Manager, consulte [seção](#setting-up-a-self-hosted-ir-on-an-azure-vm-by-using-an-azure-resource-manager-template).

Você pode usar a linha de comando para configurar ou gerenciar um ir auto-hospedado existente. Isso pode ser usado principalmente para automatizar a instalação, registro de nós de IR auto-hospedado. 

**Dmgcmd.exe** está incluído na instalação do auto-hospedado, geralmente localizada: C:\Program Files\Microsoft Integration Runtime\3.0\Shared\ folder. Isso dá suporte a vários parâmetros e pode ser invocado por meio do prompt de comando usando scripts em lotes para a automação. 

*Uso:* 

```powershell
dmgcmd [ -RegisterNewNode "<AuthenticationKey>" -EnableRemoteAccess "<port>" ["<thumbprint>"] -EnableRemoteAccessInContainer "<port>" ["<thumbprint>"] -DisableRemoteAccess -Key "<AuthenticationKey>" -GenerateBackupFile "<filePath>" "<password>" -ImportBackupFile "<filePath>" "<password>" -Restart -Start -Stop -StartUpgradeService -StopUpgradeService -TurnOnAutoUpdate -TurnOffAutoUpdate -SwitchServiceAccount "<domain\user>" ["password"] -Loglevel <logLevel> ] 
```

 *Detalhes (parâmetros / propriedade):* 

| Propriedade                                                    | DESCRIÇÃO                                                  | Obrigatório |
| ----------------------------------------------------------- | ------------------------------------------------------------ | -------- |
| RegisterNewNode "`<AuthenticationKey>`"                     | Registrar o nó do Integration Runtime (auto-hospedado) com a chave de autenticação especificado | Não       |
| EnableRemoteAccess "`<port>`" ["`<thumbprint>`"]            | Habilitar o acesso remoto no nó atual para configurar um Cluster de alta disponibilidade e/ou habilitando a configuração de credenciais diretamente contra o IV (sem passar pelo serviço do AAD) auto-hospedado usando  **Novo AzDataFactoryV2LinkedServiceEncryptedCredential** cmdlet de um computador remoto na mesma rede. | Não       |
| EnableRemoteAccessInContainer "`<port>`" ["`<thumbprint>`"] | Habilitar acesso remoto ao nó atual quando o nó está em execução no contêiner | Não       |
| DisableRemoteAccess                                         | Desabilite o acesso remoto ao nó atual. Acesso remoto é necessária para a instalação de vários nó. O New -**AzDataFactoryV2LinkedServiceEncryptedCredential** cmdlet do PowerShell ainda funciona até mesmo quando o acesso remoto está desabilitado enquanto ele é executado no mesmo computador que o nó do IR auto-hospedado. | Não       |
| Chave de "`<AuthenticationKey>`"                                 | Substituir / atualizar a chave de autenticação anterior. Ser cuidadoso, pois isso pode resultar em seu nó do IR auto-hospedado anterior ficar offline, se a chave é de um novo tempo de execução de integração. | Não       |
| GenerateBackupFile "`<filePath>`" "`<password>`"            | Gerar arquivo de backup para o nó atual, o arquivo de backup inclui as credenciais de repositório de chave e os dados do nó | Não       |
| ImportBackupFile "`<filePath>`" "`<password>`"              | Restaurar o nó de um arquivo de backup                          | Não       |
| Reiniciar                                                     | Reinicie o serviço do Integration Runtime (auto-hospedado) de Host   | Não       |
| Iniciar                                                       | Iniciar o serviço do Integration Runtime (auto-hospedado) de Host     | Não       |
| Stop                                                        | Parar o serviço do Integration Runtime (auto-hospedado) de atualização        | Não       |
| StartUpgradeService                                         | Iniciar o serviço do Integration Runtime (auto-hospedado) de atualização       | Não       |
| StopUpgradeService                                          | Parar o serviço do Integration Runtime (auto-hospedado) de atualização        | Não       |
| TurnOnAutoUpdate                                            | Ativar atualização do Integration Runtime (auto-hospedado) automática        | Não       |
| TurnOffAutoUpdate                                           | Desativar a atualização do Integration Runtime (auto-hospedado) automática       | Não       |
| SwitchServiceAccount "<domain\user>" ["password"]           | Defina DIAHostService para ser executado como uma nova conta. Use a senha vazia ("") para a conta do sistema ou conta virtual | Não       |
| Loglevel `<logLevel>`                                       | Definir o nível de log do ETW (desativado, erro, detalhado ou todos). Geralmente usado pelo suporte da Microsoft durante a depuração. | Não       |

   


## <a name="high-availability-and-scalability"></a>Alta disponibilidade e escalabilidade
Um tempo de execução de integração auto-hospedado pode ser associado a vários computadores locais ou máquinas virtuais no Azure. Esses computadores são chamados de nós. Você pode ter até quatro nós associados a um tempo de execução da integração auto-hospedada. Os benefícios de ter vários nós (computadores locais com um gateway instalado) para um gateway lógico são:
* Disponibilidade superior do tempo de execução da integração auto-hospedada para que ele não seja o único ponto de falha na sua solução de Big Data ou integração de dados de nuvem com o Azure Data Factory, garantindo a continuidade com até quatro nós.
* Desempenho e taxa de transferência aprimorados durante a movimentação de dados entre os armazenamentos de dados de nuvem e locais. Obtenha mais informações sobre [comparações de desempenho](copy-activity-performance.md).

Para associar vários nós, instale o software do tempo de execução da integração auto-hospedada no [Centro de Download](https://www.microsoft.com/download/details.aspx?id=39717). Então, registê-lo usando qualquer uma das chaves de autenticação obtida a **New-AzDataFactoryV2IntegrationRuntimeKey** cmdlet, conforme descrito na [tutorial](tutorial-hybrid-copy-powershell.md).

> [!NOTE]
> Você não precisa criar um novo tempo de execução da integração auto-hospedada para associar cada nó. Você pode instalar o tempo de execução da integração auto-hospedada em outro computador e registrá-lo usando a mesma chave de autenticação. 

> [!NOTE]
> Antes de adicionar outro nó para alta disponibilidade e escalabilidade, verifique se a opção **Acesso remoto à intranet** está habilitada no primeiro nó (**Configuration Manager do Microsoft Integration Runtime** > **Configurações** > **Acesso remoto à intranet**). 

### <a name="scale-considerations"></a>Considerações de escala

#### <a name="scale-out"></a>Expansão

Quando a memória disponível no IR auto-hospedado for baixa e o uso da CPI for alto, adicionar um novo nó ajuda a expandir a carga entre os computadores. Se as atividades falharem por causa de tempo limite ou porque o nó do IR auto-hospedado ficou offline, ajudará se você adicionar um nó ao gateway.

#### <a name="scale-up"></a>Escalar verticalmente

Quando a memória disponível e a CPU não são bem utilizadas, mas a execução de tarefas simultâneas está atingindo o limite, você deve aumentar o número de tarefas simultâneas que podem ser executadas em um nó. Convém também escalar verticalmente quando as atividades estiverem atingindo o tempo limite porque o IR auto-hospedado está sobrecarregado. Conforme mostrado na imagem a seguir, você pode aumentar a capacidade máxima de um nó:  

![Aumentando trabalhos simultâneos que podem ser executados em um nó](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>Requisitos de certificado TLS/SSL

Aqui estão os requisitos para o certificado TLS/SSL usado para proteger as comunicações entre os nós do tempo de execução de integração:

- O certificado deve ser um certificado X509 v3 publicamente confiável. É recomendável que você use certificados emitidos por uma AC (autoridade de certificação) pública (parceira).
- Cada nó do tempo de execução de integração deve confiar nesse certificado.
- Não recomendamos certificados SAN (nome alternativo da entidade) porque apenas o último item de SAN será usado; todos os outros serão ignorados devido às limitações atuais. Por exemplo, se você tiver um certificado SAN cujos SANs são **node1.domain.contoso.com** e **node2.domain.contoso.com**, só poderá usar esse certificado no computador cujo FQDN é **node2.domain.contoso.com**.
- O certificado dá suporte a qualquer tamanho de chave com suporte pelo Windows Server 2012 R2 para certificados SSL.
- Não há suporte a certificados que usam chaves CNG.  

> [!NOTE]
> Esse certificado é usado para criptografar as portas no nó do IR auto-hospedado, usado para **comunicação de nó para nó** (para sincronização de credenciais de sincronização de estado que inclui serviços vinculados entre nós) e while **usando o PowerShell cmdlet para o serviço vinculado de credencial configuração** de dentro da rede local. Sugerimos usar este certificado se o seu ambiente de rede privada não é seguro ou se você gostaria de proteger a comunicação entre os nós na sua rede privada também. A movimentação de dados em trânsito do IR auto-hospedado para outros armazenamentos de dados sempre ocorre por meio de um canal criptografado, independentemente desse certificado estar definido ou não. 

## <a name="sharing-the-self-hosted-integration-runtime-with-multiple-data-factories"></a>Compartilhar o tempo de execução da integração auto-hospedada com vários data factories

É possível reutilizar uma infraestrutura de tempo de execução da integração auto-hospedada existente já configurada em um data factory. Isso permite que você crie um *tempo de execução da integração auto-hospedada vinculado* em um data factory diferente, referenciando um IR auto-hospedado existente (compartilhado).

Para compartilhar um tempo de execução da integração auto-hospedada usando o PowerShell, confira [Criar um tempo de execução da integração auto-hospedada compartilhado no Azure Data Factory com o PowerShell](create-shared-self-hosted-integration-runtime-powershell.md).

Para uma introdução de doze minutos e demonstração desse recurso, assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminologia

- **IR compartilhado**: O IR auto-hospedado original executado em uma infraestrutura física.  
- **IR vinculado**: O IR o que faz referência a outro IR compartilhado. Esse é um IR lógico e usa a infraestrutura de outro IR auto-hospedado (compartilhado).

### <a name="high-level-steps-for-creating-a-linked-self-hosted-ir"></a>Etapas de alto nível para criar um IR auto-hospedado vinculado

1. No IR auto-hospedado que será compartilhado, conceda permissão para o data factory no qual deseja criar o IR vinculado. 

   ![Botão para conceder permissão na guia Compartilhamento](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)

   ![Seleções para atribuição de permissões](media/create-self-hosted-integration-runtime/3_rbac_permissions.png)

2. Observe a ID do recurso do IR auto-hospedado a ser compartilhado.

   ![Localização da ID do recurso](media/create-self-hosted-integration-runtime/4_ResourceID_self-hostedIR.png)

3. No data factory para o qual as permissões foram concedidas, crie um novo IR auto-hospedado (vinculado) e insira a ID do recurso.

   ![Botão para criar um tempo de execução da integração auto-hospedada vinculado](media/create-self-hosted-integration-runtime/6_create-linkedIR_2.png)

   ![Caixas de nome e ID do recurso](media/create-self-hosted-integration-runtime/6_create-linkedIR_3.png)

### <a name="monitoring"></a>Monitoramento 

- **IR compartilhado**

  ![Seleções para encontrar um tempo de execução de integração compartilhado](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

  ![Guia para monitoramento](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

- **IR vinculado**

  ![Seleções para encontrar um tempo de execução de integração vinculado](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

  ![Guia para monitoramento](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Limitações conhecidas do compartilhamento de IR auto-hospedado

* O data factory em que um tempo de execução de integração vinculado será criado precisa ter um [MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Por padrão, os data factories criados no portal do Azure ou nos cmdlets do PowerShell têm uma MSI criada implicitamente. No entanto, quando um data factory é criado por meio de um modelo ou SDK do Azure Resource Manager, a propriedade **Identidade** deve ser definida explicitamente para garantir que o Azure Resource Manager crie um data factory que contenha uma MSI. 

* O SDK do .NET do Azure Data Factory que dá suporte a esse recurso é a versão 1.1.0 ou posterior.

* Para conceder permissão, o usuário precisa da função Proprietário ou da função Proprietário herdada na Data Factory em que o tempo de execução de integração compartilhado existe.

* O recurso de compartilhamento só funciona para Fábricas de dados no mesmo locatário do Microsoft Azure Active Directory.

* Para [usuários convidados](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews) do Active Directory, a funcionalidade de pesquisa (que lista todos os data factories usando uma palavra-chave de pesquisa) na interface do usuário [não funciona](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits). No entanto, desde que o usuário convidado seja o Proprietário do data factory, pode compartilhar o tempo de execução de integração sem a funcionalidade de pesquisa, digitando diretamente a MSI do data factory com que o tempo de execução de integração precisa ser compartilhado na caixa de texto **Atribuir permissão** e selecionando **Adicionar** na interface do usuário do Azure Data Factory. 

  > [!NOTE]
  > Esse recurso está disponível apenas no Azure Data Factory V2. 

## <a name="notification-area-icons-and-notifications"></a>Ícones e notificações da área de notificação

Se você mover o cursor sobre o ícone ou mensagem na área de notificação, poderá encontrar detalhes sobre o estado do tempo de execução da integração auto-hospedada.

![Notificações na área de notificação](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewall"></a>Portas e firewall
Há dois firewalls a considerar: o *firewall corporativo* em execução no roteador central da organização e o *Firewall do Windows* configurado como um daemon no computador local em que o tempo de execução da integração auto-hospedada está instalado.

![Firewall](media/create-self-hosted-integration-runtime/firewall.png)

No nível do *firewall corporativo*, é necessário configurar os seguintes domínios e portas de saída:

Nomes de domínio | Portas | DESCRIÇÃO
------------ | ----- | ------------
*.servicebus.windows.net | 443 | Usado para comunicação com o serviço de movimentação de dados de back-end
*.core.windows.net | 443 | Usado para cópia em etapas por meio do Armazenamento de Blobs do Azure (se estiver configurado)
*.frontend.clouddatahub.net | 443 | Usado para comunicação com o serviço de movimentação de dados de back-end
download.microsoft.com | 443 | Usado para baixar as atualizações

No nível do *Firewall do Windows* (nível do computador), essas portas de saída normalmente estão habilitadas. Caso contrário, você poderá configurar as portas e os domínios adequadamente em um computador com tempo de execução da integração auto-hospedada.

> [!NOTE]
> Com base em sua origem e coletores, talvez você precise incluir domínios adicionais na lista de permissões e portas de saída em seu firewall corporativo ou Firewall do Windows.
>
> Para alguns bancos de dados na nuvem (por exemplo, Banco de Dados SQL do Azure, Azure Data Lake etc.), talvez seja necessário incluir os endereços IP do computador do tempo de execução da integração auto-hospedada na lista de permissões na configuração do firewall deles.

### <a name="copy-data-from-a-source-to-a-sink"></a>Copiar dados de uma origem para um coletor
Verifique se as regras de firewall estão habilitadas corretamente no firewall corporativo, no Firewall do Windows no computador do tempo de execução da integração auto-hospedada e no próprio armazenamento de dados. Habilitar essas regras permite ao Integration Runtime auto-hospedado se conectar com êxito à origem e ao coletor. Habilite as regras para cada repositório de dados que esteja envolvido na operação de cópia.

Por exemplo, para copiar de um armazenamento de dados local para um coletor do Banco de Dados SQL do Azure ou um coletor do SQL Data Warehouse do Azure, siga as etapas abaixo:

1. Permitir a comunicação TCP de saída na porta 1433 para o Firewall do Windows e o firewall corporativo.
2. Configurar as definições de firewall do Banco de Dados SQL do Azure para adicionar o endereço IP do computador do tempo de execução da integração auto-hospedada à lista de endereços IP permitidos.

> [!NOTE]
> Se o firewall não permitir a porta de saída 1433, o tempo de execução da integração auto-hospedada não poderá acessar diretamente o Banco de Dados SQL do Azure. Nesse caso, você pode usar uma [cópia em etapas](copy-activity-performance.md) para o Banco de Dados SQL do Azure e o SQL Data Warehouse do Azure. Neste cenário, você precisaria apenas de HTTPS (porta 443) para a movimentação de dados.


## <a name="proxy-server-considerations"></a>Considerações do servidor proxy
Se o ambiente de rede corporativo usar um servidor proxy para acessar a Internet, configure o tempo de execução da integração auto-hospedada para usar as definições de proxy apropriadas. Você pode definir o proxy durante a fase de registro inicial.

![Especificar o proxy](media/create-self-hosted-integration-runtime/specify-proxy.png)

Quando configurado, o tempo de execução de integração auto-hospedado usa o servidor proxy para se conectar ao serviço de nuvem, de origem / destino (aqueles que usam HTTP / protocolo HTTPS). Isso é selecionar **alterar link** durante a instalação inicial. Você verá a caixa de diálogo de configuração de proxy.

![Configurar proxy](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Há três opções de configuração:

- **Não usar a política**: O Integration Runtime auto-hospedado não usa explicitamente qualquer proxy para se conectar aos serviços de nuvem.
- **Usar proxy do sistema**: O Integration Runtime auto-hospedado usa a configuração de proxy que é definida em diahost.exe.config e em diawp.exe.config. Se nenhum proxy estiver configurado em diahost.exe.config e em diawp.exe.config, o tempo de execução da integração auto-hospedada vai se conectar ao serviço de nuvem diretamente sem passar por um proxy.
- **Usar proxy personalizado**: Configure a definição do proxy HTTP a ser usado pelo tempo de execução da integração auto-hospedada, em vez de usar as configurações em diahost.exe.config e diawp.exe.config. **Endereço** e **Porta** são necessários. O **Nome de Usuário** e a **Senha** são opcionais, dependendo da configuração de autenticação do proxy. Todas as configurações são criptografadas com a DPAPI do Windows no Integration Runtime auto-hospedado e armazenados localmente no computador.

O Serviço de Host do Integration Runtime é reiniciado automaticamente depois que você salva as configurações de proxy atualizadas.

Depois que o tempo de execução da integração auto-hospedada tiver sido registrado com êxito, se você quiser exibir ou atualizar as configurações de proxy, use o Configuration Manager do Integration Runtime.

1. Abra o **Configuration Manager do Microsoft Integration Runtime**.
2. Alterne para a guia **Configurações** .
3. Selecione o link **Alterar** na seção **Proxy HTTP** para abrir a caixa de diálogo **Configurar proxy HTTP**.
4. Selecione **Avançar**. Você verá um aviso solicitando sua permissão para salvar a configuração de proxy e reiniciar o serviço de host de tempo de execução de integração.

É possível exibir e atualizar o proxy HTTP usando a ferramenta Configuration Manager.

![Exibir proxy](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> Se você configurar um servidor proxy com autenticação NTLM, o serviço de host de tempo de execução de integração será executado sob a conta de domínio. Se você alterar a senha da conta de domínio posteriormente, lembre-se de atualizar as definições de configuração para o serviço e reiniciá-lo adequadamente. Por conta desse requisito, sugerimos o uso de uma conta de domínio dedicada para acessar o servidor proxy que não exija a atualização da senha com frequência.

### <a name="configure-proxy-server-settings"></a>Definir configurações do servidor proxy

Se você escolher a configuração **Usar proxy do sistema** para o proxy HTTP, o tempo de execução da integração auto-hospedada usará a configuração de proxy em diahost.exe.config e diawp.exe.config. Se nenhum proxy estiver especificado em diahost.exe.config e em diawp.exe.config, o tempo de execução da integração auto-hospedada vai se conectar ao serviço de nuvem diretamente sem passar pelo proxy. O procedimento a seguir fornece instruções para atualizar o arquivo diahost.exe.config:

1. No Explorador de Arquivos, faça uma cópia de segurança de C:\Arquivos de Programas\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config para fazer backup do arquivo original.
2. Abra o Notepad.exe executando como administrador e abra o arquivo de texto C:\Arquivos de Programas\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config. Localize a tag padrão para system.net, conforme mostrado no código abaixo:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Em seguida, você pode adicionar detalhes do servidor proxy, conforme mostrado no exemplo a seguir:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Propriedades adicionais são permitidas dentro da tag de proxy para especificar as configurações necessárias, como `scriptLocation`. Confira [Elemento proxy (Configurações de Rede)](https://msdn.microsoft.com/library/sa91de1e.aspx) para encontrar a sintaxe.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Salve o arquivo de configuração no local original. Depois, reinicie o serviço de host de tempo de execução da integração auto-hospedada, que assimila as alterações. 

   Para reiniciar o serviço, use o applet de serviços no painel de controle. Ou, no Configuration Manager do Integration Runtime, selecione o botão **Interromper serviço** e, depois, selecione **Iniciar serviço**. 
   
   Se o serviço não inicia, é provável que uma sintaxe de marca XML incorreta tenha sido adicionada ao arquivo de configuração de aplicativo que foi editado.

> [!IMPORTANT]
> Não se esqueça de atualizar diahost.exe.config e diawp.exe.config.

Você também precisa verificar se o Microsoft Azure está na lista de permissões da empresa. É possível baixar a lista de endereços IP válidos do Microsoft Azure no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Possíveis sintomas de problemas relacionados ao firewall e ao servidor proxy
Se você encontrar erros similares aos descritos a seguir, eles provavelmente serão devidos à configuração incorreta do servidor proxy ou firewall, que impedirá o tempo de execução da integração auto-hospedada de se conectar ao Data Factory para se autenticar. Para garantir que seu firewall e servidor proxy estejam configurados corretamente, confira a seção anterior.

* Quando você tenta registrar o Integration Runtime auto-hospedado, recebe o seguinte erro: “Falha ao registrar esse nó do Integration Runtime! Confirme se a chave de autenticação é válida e o serviço de host de serviço de integração está em execução neste computador".
* Ao abrir o Configuration Manager do Integration Runtime, você vê o status **Desconectado** ou **Conectando**. Ao exibir os logs de eventos do Windows, em **Visualizador de Eventos** > **Logs de aplicativos e serviços** > **Microsoft Integration Runtime**, você vê mensagens de erro como esta:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enabling-remote-access-from-an-intranet"></a>Habilitação de acesso remoto por uma intranet  
Se você usar o PowerShell para criptografar as credenciais de outro computador (na rede) que não sejam, em que o tempo de execução de integração auto-hospedado está instalado, você pode habilitar o **acesso remoto pela Intranet** opção. Se você executar o PowerShell para criptografar credenciais no mesmo computador em que o tempo de execução de integração auto-hospedado está instalado, será possível habilitar **acesso remoto pela Intranet**.

Você deve habilitar a opção **Acesso remoto pela intranet** antes de adicionar outro nó para alta disponibilidade e escalabilidade.  

Durante a configuração do tempo de execução da integração auto-hospedada (da versão 3.3.xxxx.x em diante), a instalação do tempo de execução da integração auto-hospedada desabilita, por padrão, a opção **Acesso remoto pela intranet** no computador do tempo de execução da integração auto-hospedada.

Se estiver usando um firewall de terceiros, poderá abrir manualmente a porta 8060 (ou a porta configurada pelo usuário). Se tiver um problema de firewall durante a configuração do tempo de execução da integração auto-hospedada, tente usar o comando a seguir para instalar o tempo de execução da integração auto-hospedada sem configurar o firewall:

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

Se optar por não abrir a porta 8060 no computador do tempo de execução da integração auto-hospedada, use mecanismos diferentes do aplicativo Definindo Credenciais para configurar as credenciais do armazenamento de dados. Por exemplo, você pode usar o **New-AzDataFactoryV2LinkedServiceEncryptCredential** cmdlet do PowerShell.


## <a name="next-steps"></a>Próximas etapas
Consulte o tutorial a seguir para obter instruções passo a passo: [Tutorial: Copiar dados locais na nuvem](tutorial-hybrid-copy-powershell.md).
