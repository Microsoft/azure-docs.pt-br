---
title: Configurar grupos de disponibilidade para SQL Server em máquinas virtuais RHEL no Azure – Máquinas Virtuais do Linux | Microsoft Docs
description: Aprenda a configurar a alta disponibilidade em um ambiente de cluster RHEL e configure o STONITH
ms.service: virtual-machines-sql
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 06/25/2020
ms.openlocfilehash: 889df16191104ca5b335d067abe0f3d1311c4603
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450099"
---
# <a name="tutorial-configure-availability-groups-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Tutorial: Configurar grupos de disponibilidade para o SQL Server em máquinas virtuais RHEL no Azure 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!NOTE]
> Usamos o SQL Server 2017 com RHEL 7.6 neste tutorial, mas é possível usar o SQL Server 2019 no RHEL 7 ou RHEL 8 para configurar a alta disponibilidade. Os comandos para configurar os recursos do grupo de disponibilidade e cluster do pacemake foram alterados no RHEL 8 e será conveniente examinar o artigo [Criar recurso do grupo de disponibilidade](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) e recursos do RHEL 8 para obter mais informações sobre os comandos corretos.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> - Criar um grupo de recursos, conjunto de disponibilidade e VMs (máquinas virtuais) do Linux do Azure
> - Habilitar HA (alta disponibilidade)
> - Criar um cluster do Pacemaker
> - Configurar um agente de isolamento criando um dispositivo STONITH
> - Instalar o SQL Server e mssql-tools no RHEL
> - Configurar o grupo de disponibilidade Always On do SQL Server
> - Configurar os recursos do AG (grupo de disponibilidade) no cluster do Pacemaker
> - Testar um failover e o agente de isolamento

Este tutorial usará a CLI do Azure para implantar recursos no Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.0.30 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Se você tiver mais de uma assinatura, [defina a assinatura](/cli/azure/manage-azure-subscriptions-azure-cli) na qual você deseja implantar esses recursos.

Use o comando a seguir para criar um grupo de recursos `<resourceGroupName>` em uma região. Substitua `<resourceGroupName>` por um nome de sua escolha. Estamos usando `East US 2` para este tutorial. Para obter mais informações, confira o [Início Rápido](../../../application-gateway/quick-create-cli.md) a seguir.

```azurecli-interactive
az group create --name <resourceGroupName> --location eastus2
```

## <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade

A próxima etapa é criar um conjunto de disponibilidade. Execute o comando a seguir no Azure Cloud Shell e substitua `<resourceGroupName>` pelo nome do grupo de recursos. Escolha um nome para `<availabilitySetName>`.

```azurecli-interactive
az vm availability-set create \
    --resource-group <resourceGroupName> \
    --name <availabilitySetName> \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Você deverá obter os seguintes resultados depois que o comando for concluído:

```output
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/availabilitySets/<availabilitySetName>",
  "location": "eastus2",
  "name": "<availabilitySetName>",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 2,
  "proximityPlacementGroup": null,
  "resourceGroup": "<resourceGroupName>",
  "sku": {
    "capacity": null,
    "name": "Aligned",
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```

## <a name="create-rhel-vms-inside-the-availability-set"></a>Criar VMs do RHEL dentro do conjunto de disponibilidade

> [!WARNING]
> Se você escolher uma imagem PAYG (paga conforme o uso) do RHEL e configurar a HA (alta disponibilidade), talvez seja necessário registrar sua assinatura. Isso pode fazer você pagar duas vezes pela assinatura, pois você será cobrado pela assinatura do RHEL do Microsoft Azure para a VM e por uma assinatura do Red Hat. Para obter mais informações, consulte https://access.redhat.com/solutions/2458541.
>
> Para evitar ser “cobrado duas vezes”, use uma imagem de HA do rHEL ao criar a VM do Azure. Imagens oferecidas como imagens RHEL-HA também são imagens PAYG com o repositório de HA pré-habilitado.

1. Obtenha uma lista de imagens de VM (máquina virtual) que oferecem o RHEL com HA:

    ```azurecli-interactive
    az vm image list --all --offer "RHEL-HA"
    ```

    Você deve ver o seguintes resultados:

    ```output
    [
      {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "7.4",
    "urn": "RedHat:RHEL-HA:7.4:7.4.2019062021",
    "version": "7.4.2019062021"
       },
       {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "7.5",
    "urn": "RedHat:RHEL-HA:7.5:7.5.2019062021",
    "version": "7.5.2019062021"
        },
        {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "7.6",
    "urn": "RedHat:RHEL-HA:7.6:7.6.2019062019",
    "version": "7.6.2019062019"
         },
         {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "8.0",
    "urn": "RedHat:RHEL-HA:8.0:8.0.2020021914",
    "version": "8.0.2020021914"
         },
         {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "8.1",
    "urn": "RedHat:RHEL-HA:8.1:8.1.2020021914",
    "version": "8.1.2020021914"
          },
          {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "80-gen2",
    "urn": "RedHat:RHEL-HA:80-gen2:8.0.2020021915",
    "version": "8.0.2020021915"
           },
           {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "81_gen2",
    "urn": "RedHat:RHEL-HA:81_gen2:8.1.2020021915",
    "version": "8.1.2020021915"
           }
    ]
    ```

    Para este tutorial, vamos escolher a imagem `RedHat:RHEL-HA:7.6:7.6.2019062019` para o exemplo do RHEL 7 e escolher `RedHat:RHEL-HA:8.1:8.1.2020021914` para o exemplo do RHEL 8.
    
    Você também pode escolher o SQL Server 2019 pré-instalado em imagens RHEL8-HA. Para obter a lista dessas imagens, execute o seguinte comando:  
    
    ```azurecli-interactive
    az vm image list --all --offer "sql2019-rhel8"
    ```

    Você deve ver o seguintes resultados:

    ```output
    [
      {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "enterprise",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:enterprise:15.0.200317",
    "version": "15.0.200317"
       },
       }
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "enterprise",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:enterprise:15.0.200512",
    "version": "15.0.200512"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "sqldev",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:sqldev:15.0.200317",
    "version": "15.0.200317"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "sqldev",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:sqldev:15.0.200512",
    "version": "15.0.200512"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "standard",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:standard:15.0.200317",
    "version": "15.0.200317"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "standard",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:standard:15.0.200512",
    "version": "15.0.200512"
       }
    ]
    ```

    Se você usar uma das imagens acima para criar as máquinas virtuais, ela terá SQL Server 2019 pré-instalado. Ignore a seção [Instalar o SQL Server e mssql-tools](#install-sql-server-and-mssql-tools), conforme descrito neste artigo.
    
    
    > [!IMPORTANT]
    > Os nomes de computador devem ter menos de 15 caracteres para configurar o grupo de disponibilidade. O nome de usuário não pode conter caracteres maiúsculos e as senhas devem ter mais de 12 caracteres.

1. Queremos criar três VMs no conjunto de disponibilidade. Substitua o seguinte no comando abaixo:

    - `<resourceGroupName>`
    - `<VM-basename>`
    - `<availabilitySetName>`
    - `<VM-Size>` – Um exemplo seria "Standard_D16_v3"
    - `<username>`
    - `<adminPassword>`

    ```azurecli-interactive
    for i in `seq 1 3`; do
           az vm create \
             --resource-group <resourceGroupName> \
             --name <VM-basename>$i \
             --availability-set <availabilitySetName> \
             --size "<VM-Size>"  \
             --image "RedHat:RHEL-HA:7.6:7.6.2019062019" \
             --admin-username "<username>" \
             --admin-password "<adminPassword>" \
             --authentication-type all \
             --generate-ssh-keys
    done
    ```

O comando acima cria as VMs e uma VNet padrão para essas VMs. Para obter mais informações sobre as diferentes configurações, confira o artigo [az vm create](/cli/azure/vm).

Depois que o comando for concluído para cada VM, você deverá obter resultados semelhantes aos seguintes:

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<VM1>",
  "location": "eastus2",
  "macAddress": "<Some MAC address>",
  "powerState": "VM running",
  "privateIpAddress": "<IP1>",
  "publicIpAddress": "",
  "resourceGroup": "<resourceGroupName>",
  "zones": ""
}
```

> [!IMPORTANT]
> A imagem padrão criada com o comando acima cria um disco do sistema operacional de 32 GB por padrão. Possivelmente, você poderia ficar sem espaço com essa instalação padrão. Você pode usar o seguinte parâmetro adicionado ao comando `az vm create` acima para criar um disco do sistema operacional com 128 GB como um exemplo: `--os-disk-size-gb 128`.
>
> Em seguida, você poderá [configurar o LVM (Gerenciador de Volume Lógico)](/previous-versions/azure/virtual-machines/linux/configure-lvm) se precisar expandir os volumes de pastas apropriados para acomodar a instalação.

### <a name="test-connection-to-the-created-vms"></a>Testar a conexão com as VMs criadas

Conecte-se à VM1 ou às outras VMs usando o comando a seguir no Azure Cloud Shell. Se não for possível localizar seus IPs de VM, siga este [Início Rápido no Azure Cloud Shell](../../../cloud-shell/quickstart.md#ssh-into-your-linux-vm).

```azurecli-interactive
ssh <username>@publicipaddress
```

Se a conexão for bem-sucedida, você deverá ver a seguinte saída representando o terminal do Linux:

```output
[<username>@<VM1> ~]$
```

Digite `exit` para sair da sessão SSH.

## <a name="enable-high-availability"></a>Habilitar alta disponibilidade

> [!IMPORTANT]
> Para concluir essa parte do tutorial, você deverá ter uma assinatura do RHEL e o Complemento de Alta Disponibilidade. Se você está usando uma imagem recomendada na seção anterior, não é necessário registrar outra assinatura.
 
Conecte-se a cada nó da VM e siga a guia abaixo para habilitar a HA. Para obter mais informações, confira [habilitar a assinatura de alta disponibilidade para RHEL](/sql/linux/sql-server-linux-availability-group-cluster-rhel#enable-the-high-availability-subscription-for-rhel).

> [!TIP]
> Será mais fácil se você abrir uma sessão SSH para cada uma das VMs simultaneamente, pois os mesmos comandos precisarão ser executados em cada VM em todo o artigo.
>
> Se você estiver copiando e colando vários comandos do `sudo` e for solicitado a fornecer uma senha, os comandos adicionais não serão executados. Execute cada comando separadamente.


1. Execute os seguintes comandos em cada VM para abrir as portas do firewall do Pacemaker:

    ```bash
    sudo firewall-cmd --permanent --add-service=high-availability
    sudo firewall-cmd --reload
    ```

1. Atualize e instale os pacotes do Pacemaker em todos os nós usando os seguintes comandos:

    > [!NOTE]
    > **nmap** está instalado como parte desse bloco de comandos como uma ferramenta para localizar endereços IP disponíveis em sua rede. Você não precisa instalar **nmap**, mas ele será útil posteriormente neste tutorial.

    ```bash
    sudo yum update -y
    sudo yum install -y pacemaker pcs fence-agents-all resource-agents fence-agents-azure-arm nmap
    sudo reboot
    ```

1. Defina a senha do usuário padrão criado ao instalar pacotes do Pacemaker. Use a mesma senha em todos os nós.

    ```bash
    sudo passwd hacluster
    ```

1. Use o comando a seguir para abrir o arquivo de hosts e configurar a resolução de nomes de host. Para obter mais informações, confira [Configurar o AG](/sql/linux/sql-server-linux-availability-group-configure-ha#prerequisites) sobre como configurar o arquivo de hosts.

    ```
    sudo vi /etc/hosts
    ```

    No editor **vi**, digite `i` para inserir texto e, em uma linha em branco, adicione o **IP Privado** da VM correspondente. Em seguida, adicione o nome da VM após um espaço ao lado do IP. Cada linha deve ter uma entrada separada.

    ```output
    <IP1> <VM1>
    <IP2> <VM2>
    <IP3> <VM3>
    ```

    > [!IMPORTANT]
    > Recomendamos que você use seu endereço **IP Privado** acima. Usar o endereço IP Público nessa configuração fará a instalação falhar e não recomendamos expor sua VM a redes externas.

    Para sair do editor **vi**, primeiro pressione a tecla **Esc** e, em seguida, insira o comando `:wq` para gravar o arquivo e encerrar.

## <a name="create-the-pacemaker-cluster"></a>Criar o cluster do Pacemaker

Nesta seção, habilitaremos e iniciaremos o serviço pcsd e, em seguida, configuraremos o cluster. Para o SQL Server em Linux, os recursos de cluster não são criados automaticamente. Precisaremos habilitar e criar os recursos de pacemaker manualmente. Para obter mais informações, confira o artigo sobre como [configurar uma instância de cluster de failover para RHEL](/sql/linux/sql-server-linux-shared-disk-cluster-red-hat-7-configure#install-and-configure-pacemaker-on-each-cluster-node)

### <a name="enable-and-start-pcsd-service-and-pacemaker"></a>Habilite e inicie o serviço pcsd e o Pacemaker

1. Execute os comandos em todos os nós. Esses comandos permitem que os nós ingressem novamente no cluster após a reinicialização.

    ```bash
    sudo systemctl enable pcsd
    sudo systemctl start pcsd
    sudo systemctl enable pacemaker
    ``` 

1. Remova todas as configurações de cluster existentes de todos os nós. Execute o comando a seguir:

    ```bash
    sudo pcs cluster destroy 
    sudo systemctl enable pacemaker 
    ```

1. No nó primário, execute os comandos a seguir para configurar o cluster.

    - Ao executar o comando `pcs cluster auth` para autenticar os nós de cluster, você deverá fornecer uma senha. Insira a senha do usuário **hacluster** usuário criado anteriormente.

    **RHEL7**

    ```bash
    sudo pcs cluster auth <VM1> <VM2> <VM3> -u hacluster
    sudo pcs cluster setup --name az-hacluster <VM1> <VM2> <VM3> --token 30000
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

    **RHEL8**

    Para o RHEL 8, você precisará autenticar os nós separadamente. Insira manualmente o nome de usuário e a senha do **hacluster** quando solicitado.

    ```bash
    sudo pcs host auth <node1> <node2> <node3>
    sudo pcs cluster setup <clusterName> <node1> <node2> <node3>
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

1. Execute o comando a seguir para verificar se todos os nós estão online.

    ```bash
    sudo pcs status
    ```

   **RHEL 7** 
   
    Se todos os nós estiverem online, você verá uma saída semelhante à seguinte:

    ```output
    Cluster name: az-hacluster
     
    WARNINGS:
    No stonith devices and stonith-enabled is not false
     
    Stack: corosync
    Current DC: <VM2> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
    Last updated: Fri Aug 23 18:27:57 2019
    Last change: Fri Aug 23 18:27:56 2019 by hacluster via crmd on <VM2>
     
    3 nodes configured
    0 resources configured
     
    Online: [ <VM1> <VM2> <VM3> ]
     
    No resources
     
     
    Daemon Status:
          corosync: active/enabled
          pacemaker: active/enabled
          pcsd: active/enabled
    ```
   
   **RHEL 8** 
   
    ```output
    Cluster name: az-hacluster
     
    WARNINGS:
    No stonith devices and stonith-enabled is not false
     
    Cluster Summary:
    * Stack: corosync
    * Current DC: <VM2> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
    * Last updated: Fri Aug 23 18:27:57 2019
    * Last change: Fri Aug 23 18:27:56 2019 by hacluster via crmd on <VM2>
    * 3 nodes configured
    * 0 resource instances configured
     
   Node List:
    * Online: [ <VM1> <VM2> <VM3> ]
   
   Full List of Resources:
   * No resources
     
   Daemon Status:
          corosync: active/enabled
          pacemaker: active/enabled
          pcsd: active/enabled
    
    ```
    
1. Defina os votos esperados no cluster ativo como 3. Esse comando afeta apenas o cluster ativo e não altera os arquivos de configuração.

    Em todos os nós, defina os votos esperados com o seguinte comando:

    ```bash
    sudo pcs quorum expected-votes 3
    ```

## <a name="configure-the-fencing-agent"></a>Configurar um agente de isolamento

Um dispositivo STONITH fornece um agente de isolamento. As instruções abaixo são modificadas para este tutorial. Para obter mais informações, confira [criar uma conta STONITH](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#create-stonith-device).
 
[Confira a versão do agente do Azure Fence verificar que ele está atualizado](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#cluster-installation). Use o seguinte comando:

```bash
sudo yum info fence-agents-azure-arm
```

Você deverá ver uma saída semelhante ao exemplo abaixo.

```output
Loaded plugins: langpacks, product-id, search-disabled-repos, subscription-manager
Installed Packages
Name        : fence-agents-azure-arm
Arch        : x86_64
Version     : 4.2.1
Release     : 11.el7_6.8
Size        : 28 k
Repo        : installed
From repo   : rhel-ha-for-rhel-7-server-eus-rhui-rpms
Summary     : Fence agent for Azure Resource Manager
URL         : https://github.com/ClusterLabs/fence-agents
License     : GPLv2+ and LGPLv2+
Description : The fence-agents-azure-arm package contains a fence agent for Azure instances.
```

### <a name="register-a-new-application-in-azure-active-directory"></a>Registrar um novo aplicativo no Azure Active Directory
 
 1. Acesse https://portal.azure.com
 2. Abra a [folha do Azure Active Directory.](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties) Vá para Propriedades e anote a ID do Diretório. Este é o `tenant ID`
 3. Clique em [**Registros de aplicativo**](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
 4. Clique em **Novo registro**
 5. Insira um **Nome** como `<resourceGroupName>-app`, selecione **Contas apenas neste diretório de organização**
 6. Selecione um Tipo de Aplicativo **Web**, insira uma URL de logon (por exemplo http://localhost) e clique em Adicionar. A URL de logon não é usada e pode ser qualquer URL válida. Quando terminar, clique em **Registrar**
 7. Selecione **Certificados e segredos** para seu novo Registro de aplicativo e clique em **Novo segredo do cliente**
 8. Insira uma descrição para uma nova chave (segredo do cliente), selecione **Nunca expira** e clique em **Adicionar**
 9. Anote o valor do segredo. Ele é usado como senha da Entidade de Serviço
10. Selecione **Visão geral**. Anote a ID do Aplicativo. Ela é usada como nome de usuário (ID de logon nas etapas abaixo) da Entidade de Serviço
 
### <a name="create-a-custom-role-for-the-fence-agent"></a>Criar uma função personalizada para o agente de limite

Siga o tutorial para [Criar uma função personalizada do Azure usando a CLI do Azure](../../../role-based-access-control/tutorial-custom-role-cli.md#create-a-custom-role).

Seu arquivo JSON deve ser semelhante ao seguinte:

- Substitua `<username>` por um nome de sua escolha. Isso ocorre para evitar qualquer duplicação ao criar essa definição de função.
- Substitua `<subscriptionId>` por sua ID da Assinatura do Azure.

```json
{
  "Name": "Linux Fence Agent Role-<username>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to power-off and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/powerOff/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscriptionId>"
  ]
}
```

Para adicionar a função, execute o seguinte comando:

- Substitua `<filename>` pelo nome do arquivo.
- Se você estiver executando o comando de um caminho, que não seja a pasta na qual o arquivo foi salvo, inclua o caminho de pasta do arquivo no comando.

```bash
az role definition create --role-definition "<filename>.json"
```

Você deve ver o seguinte resultado:

```output
{
  "assignableScopes": [
    "/subscriptions/<subscriptionId>"
  ],
  "description": "Allows to power-off and start virtual machines",
  "id": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<roleNameId>",
  "name": "<roleNameId>",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/virtualMachines/powerOff/action",
        "Microsoft.Compute/virtualMachines/start/action"
      ],
      "dataActions": [],
      "notActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Linux Fence Agent Role-<username>",
  "roleType": "CustomRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="assign-the-custom-role-to-the-service-principal"></a>Atribuir a função personalizada à Entidade de Serviço

Atribua a função personalizada `Linux Fence Agent Role-<username>` que foi criada na última etapa para a Entidade de Serviço. Não mais use a função de proprietário!
 
1. Acesse https://portal.azure.com
2. Abra a [folha Todos os recursos](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAll)
3. Selecione a máquina virtual do primeiro nó do cluster
4. Clique em **Controle de acesso (IAM)**
5. Clique em **Adicionar uma atribuição de função**
6. Selecione a função `Linux Fence Agent Role-<username>` na lista **Função**
7. Na lista **Selecionar**, insira o nome do aplicativo criado acima, `<resourceGroupName>-app`
8. Clique em **Salvar**
9. Repita as etapas acima para todos os nós do cluster.

### <a name="create-the-stonith-devices"></a>Criar os dispositivos STONITH

Execute os seguintes comandos no nó 1:

- Substitua o `<ApplicationID>` pelo valor da ID do seu registro de aplicativo.
- Substitua o `<servicePrincipalPassword>` pelo valor do segredo do cliente.
- Substitua o `<resourceGroupName>` pelo grupo de recursos da sua assinatura usada para este tutorial.
- Substitua `<tenantID>` e o `<subscriptionId>` de sua assinatura do Azure.

```bash
sudo pcs property set stonith-timeout=900
sudo pcs stonith create rsc_st_azure fence_azure_arm login="<ApplicationID>" passwd="<servicePrincipalPassword>" resourceGroup="<resourceGroupName>" tenantId="<tenantID>" subscriptionId="<subscriptionId>" power_timeout=240 pcmk_reboot_timeout=900
```

Como já adicionamos uma regra ao nosso firewall para permitir o serviço de HA (`--add-service=high-availability`), não é necessário abrir as seguintes portas de firewall em todos os nós: 2224, 3121, 21064, 5405. No entanto, se você tiver qualquer tipo de problemas de conexão com a HA, use o comando a seguir para abrir essas portas associadas à HA.

> [!TIP]
> Ou você pode adicionar todas as portas neste tutorial de uma vez para economizar tempo. As portas que precisam ser abertas são explicadas em suas seções relativas abaixo. Se quiser adicionar todas as portas agora, adicione as portas adicionais: 1433 e 5022.

```bash
sudo firewall-cmd --zone=public --add-port=2224/tcp --add-port=3121/tcp --add-port=21064/tcp --add-port=5405/tcp --permanent
sudo firewall-cmd --reload
```

## <a name="install-sql-server-and-mssql-tools"></a>Instalar o SQL Server e mssql-tools

> [!NOTE]
> Se você tiver criado as VMs com o SQL Server 2019 pré-instalado em RHEL8-HA, poderá ignorar as etapas a seguir para instalar o SQL Server e mssql-tools e iniciar a seção **Configurar um grupo de disponibilidade** depois de configurar a senha SA em todas as VMs executando o comando `sudo /opt/mssql/bin/mssql-conf set-sa-password` em todas as VMs.

Use a seção abaixo para instalar o SQL Server e o mssql-tools nas VMs. Você pode escolher um dos exemplos a seguir para instalar o SQL Server 2017 no RHEL 7 ou o SQL Server 2019 no RHEL 8. Execute cada uma dessas ações em todos os nós. Para obter mais informações, confira [Instalar o SQL Server em uma VM do Red Hat](/sql/linux/quickstart-install-connect-red-hat).


### <a name="installing-sql-server-on-the-vms"></a>Instalar o SQL Server nas VMs

Os seguintes comandos são usados para instalar o SQL Server:

**RHEL 7 com SQL Server 2017** 

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/7/mssql-server-2017.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```

**RHEL 8 com SQL Server 2019** 

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/8/mssql-server-2019.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```
### <a name="open-firewall-port-1433-for-remote-connections"></a>Abrir a porta 1433 do firewall para conexões remotas

Você precisará abrir a porta 1433 na VM para se conectar remotamente. Use os seguintes comandos para abrir a porta 1433 no firewall de cada VM:

```bash
sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
sudo firewall-cmd --reload
```

### <a name="installing-sql-server-command-line-tools"></a>Instalar as ferramentas de linha de comando do SQL Server

Os comandos a seguir são usados para instalar as ferramentas de linha de comando do SQL Server. Para obter mais informações, confira [instalar as ferramentas de linha de comando do SQL Server](/sql/linux/quickstart-install-connect-red-hat#tools).

**RHEL 7** 

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/7/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```

**RHEL 8** 

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/8/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```
 
> [!NOTE] 
> Para sua conveniência, adicione /opt/mssql-tools/bin/ à sua variável de ambiente PATH. Isso permite que você execute as ferramentas sem especificar o caminho completo. Execute os seguintes comandos para modificar o PATH para sessões de logon e sessões interativas/que não são de logon:</br></br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile`</br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc`</br>
`source ~/.bashrc`


### <a name="check-the-status-of-the-sql-server"></a>Verificar o status do SQL Server

Depois que você concluir a configuração, você poderá verificar o status do SQL Server e se ele está em execução:

```bash
systemctl status mssql-server --no-pager
```

Você deve ver o seguinte resultado:

```output
● mssql-server.service - Microsoft SQL Server Database Engine
   Loaded: loaded (/usr/lib/systemd/system/mssql-server.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2019-12-05 17:30:55 UTC; 20min ago
     Docs: https://docs.microsoft.com/en-us/sql/linux
 Main PID: 11612 (sqlservr)
   CGroup: /system.slice/mssql-server.service
           ├─11612 /opt/mssql/bin/sqlservr
           └─11640 /opt/mssql/bin/sqlservr
```

## <a name="configure-an-availability-group"></a>Configurar um conjunto de disponibilidade

Use as etapas a seguir para configurar um grupo de disponibilidade Always On do SQL Server para suas VMs. Para obter mais informações, confira [Configurar grupos de disponibilidade Always On do SQL Server para alta disponibilidade no Linux](/sql/linux/sql-server-linux-availability-group-configure-ha)

### <a name="enable-always-on-availability-groups-and-restart-mssql-server"></a>Habilitar grupos de disponibilidade Always On e reiniciar o mssql-server

Habilite grupos de disponibilidade Always On em cada nó que hospeda uma instância do SQL Server. Em seguida, reinicie o mssql-server. Execute o seguinte script:

```
sudo /opt/mssql/bin/mssql-conf set hadr.hadrenabled 1
sudo systemctl restart mssql-server
```

### <a name="create-a-certificate"></a>Criar um certificado

No momento, não damos suporte à autenticação do AD para o ponto de extremidade do grupo de disponibilidade. Portanto, devemos usar um certificado para a criptografia de ponto de extremidade do grupo de disponibilidade.

1. Conecte-se a **todos os nós** usando o SSMS (SQL Server Management Studio) ou o SQL CMD. Execute os seguintes comandos para habilitar uma sessão AlwaysOn_health e criar uma chave mestra:

    > [!IMPORTANT]
    > Se você estiver se conectando remotamente à sua instância do SQL Server, será necessário que a porta 1433 esteja aberta em seu firewall. Você também precisará permitir conexões de entrada para a porta 1433 em seu NSG para cada VM. Para obter mais informações, confira [Criar uma regra de segurança](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) para criar uma regra de segurança de entrada.

    - Substitua o `<Master_Key_Password>` por sua senha.


    ```sql
    ALTER EVENT SESSION  AlwaysOn_health ON SERVER WITH (STARTUP_STATE=ON);
    GO
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Master_Key_Password>';
    ```

 
1. Conecte-se à réplica primária usando o SSMS ou o SQL CMD. Os comandos abaixo criarão um certificado em `/var/opt/mssql/data/dbm_certificate.cer` e uma chave privada em `var/opt/mssql/data/dbm_certificate.pvk` em sua réplica primária do SQL Server:

    - Substitua o `<Private_Key_Password>` por sua senha.
    
    ```sql
    CREATE CERTIFICATE dbm_certificate WITH SUBJECT = 'dbm';
    GO
    
    BACKUP CERTIFICATE dbm_certificate
       TO FILE = '/var/opt/mssql/data/dbm_certificate.cer'
       WITH PRIVATE KEY (
               FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
               ENCRYPTION BY PASSWORD = '<Private_Key_Password>'
           );
    GO
    ```

Saia da sessão do SQL CMD executando o comando `exit` e volte para sua sessão SSH.
 
### <a name="copy-the-certificate-to-the-secondary-replicas-and-create-the-certificates-on-the-server"></a>Copiar o certificado para as réplicas secundárias e criar os certificados no servidor

1. Copie esses dois arquivos que foram criados na mesma localização em todos os servidores que hospedarão as réplicas de disponibilidade.
 
    No servidor primário, execute o seguinte comando `scp` para copiar o certificado para os servidores de destino:

    - Substitua `<username>` e `<VM2>` pelo nome de usuário e pelo nome da VM de destino que você está usando.
    - Execute este comando para todas as réplicas secundárias.

    > [!NOTE]
    > Você não precisa executar o `sudo -i`, que fornece o ambiente raiz. Você poderia simplesmente executar o comando `sudo` na frente de cada comando, como fizemos anteriormente neste tutorial.

    ```bash
    # The below command allows you to run commands in the root environment
    sudo -i
    ```

    ```bash
    scp /var/opt/mssql/data/dbm_certificate.* <username>@<VM2>:/home/<username>
    ```

1. No servidor de destino, execute o seguinte comando:

    - Substitua `<username>` por seu nome de usuário.
    - O comando `mv` move os arquivos ou o diretório de um local para outro.
    - O comando `chown` é usado para alterar o proprietário e o grupo de arquivos, diretórios ou links.
    - Execute estes comandos para todas as réplicas secundárias.

    ```bash
    sudo -i
    mv /home/<username>/dbm_certificate.* /var/opt/mssql/data/
    cd /var/opt/mssql/data
    chown mssql:mssql dbm_certificate.*
    ```

1. O script Transact-SQL a seguir cria um certificado com base no backup que você criou na réplica primária do SQL Server. Atualize o script com senhas fortes. A senha de descriptografia é a mesma senha que você usou para criar o arquivo .pvk na etapa anterior. Para criar o certificado, execute o seguinte script usando o SQL CMD ou o SSMS em todos os servidores secundários:

    ```sql
    CREATE CERTIFICATE dbm_certificate
        FROM FILE = '/var/opt/mssql/data/dbm_certificate.cer'
        WITH PRIVATE KEY (
        FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
        DECRYPTION BY PASSWORD = '<Private_Key_Password>'
                );
    GO
    ```

### <a name="create-the-database-mirroring-endpoints-on-all-replicas"></a>Criar os pontos de extremidade de espelhamento de banco de dados em todas as réplicas

Execute o seguinte script em todas as instâncias do SQL Server usando o SQL CMD ou o SSMS:

```sql
CREATE ENDPOINT [Hadr_endpoint]
    AS TCP (LISTENER_PORT = 5022)
    FOR DATABASE_MIRRORING (
    ROLE = ALL,
    AUTHENTICATION = CERTIFICATE dbm_certificate,
ENCRYPTION = REQUIRED ALGORITHM AES
);
GO

ALTER ENDPOINT [Hadr_endpoint] STATE = STARTED;
GO
```

### <a name="create-the-availability-group"></a>Crie o grupo de disponibilidade

Conecte-se à instância do SQL Server que hospeda a réplica primária usando o SQL CMD ou o SSMS. Execute o seguinte comando para criar o grupo de disponibilidade:

- Substitua `ag1` pelo nome do Grupo de Disponibilidade desejado.
- Substitua os valores `<VM1>`, `<VM2>` e `<VM3>` pelos nomes das instâncias do SQL Server que hospedam as réplicas.

```sql
CREATE AVAILABILITY GROUP [ag1]
     WITH (DB_FAILOVER = ON, CLUSTER_TYPE = EXTERNAL)
     FOR REPLICA ON
         N'<VM1>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM1>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM2>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM2>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM3>'
         WITH(
            ENDPOINT_URL = N'tcp://<VM3>:5022',
            AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
            FAILOVER_MODE = EXTERNAL,
            SEEDING_MODE = AUTOMATIC
            );
GO

ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
GO
```

### <a name="create-a-sql-server-login-for-pacemaker"></a>Criar um logon do SQL Server para o Pacemaker

Em todas as instâncias do SQL Server, crie um logon de SQL Server para o Pacemaker. O Transact-SQL a seguir cria um logon.

- Substitua `<password>` por sua senha complexa.

```sql
USE [master]
GO

CREATE LOGIN [pacemakerLogin] with PASSWORD= N'<password>';
GO

ALTER SERVER ROLE [sysadmin] ADD MEMBER [pacemakerLogin];
GO
```

Em todas as instâncias do SQL Server, salve as credenciais usadas para o logon do SQL Server. 

1. Crie o arquivo:

    ```bash
    sudo vi /var/opt/mssql/secrets/passwd
    ```

1. Adicione as seguintes duas linhas ao arquivo:

    ```bash
    pacemakerLogin
    <password>
    ```

    Para sair do editor **vi**, primeiro pressione a tecla **Esc** e, em seguida, insira o comando `:wq` para gravar o arquivo e encerrar.

1. Torne o arquivo apenas legível pela raiz:

    ```bash
    sudo chown root:root /var/opt/mssql/secrets/passwd
    sudo chmod 400 /var/opt/mssql/secrets/passwd
    ```

### <a name="join-secondary-replicas-to-the-availability-group"></a>Ingressar réplicas secundárias ao grupo de disponibilidade

1. Para ingressar as réplicas secundárias ao grupo de disponibilidade, você precisará abrir a porta 5022 no firewall para todos os servidores. Execute o seguinte comando em sua sessão SSH:

    ```bash
    sudo firewall-cmd --zone=public --add-port=5022/tcp --permanent
    sudo firewall-cmd --reload
    ```

1. Em suas réplicas secundárias, execute os seguintes comandos para ingressá-los ao grupo de disponibilidade:

    ```sql
    ALTER AVAILABILITY GROUP [ag1] JOIN WITH (CLUSTER_TYPE = EXTERNAL);
    GO

    ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
    GO
    ```

1. Execute o seguinte script Transact-SQL na réplica primária e em cada réplica secundária:

    ```sql
    GRANT ALTER, CONTROL, VIEW DEFINITION ON AVAILABILITY GROUP::ag1 TO pacemakerLogin;
    GO
    
    GRANT VIEW SERVER STATE TO pacemakerLogin;
    GO
    ```

1. Depois que as réplicas secundárias forem ingressadas, você poderá vê-las no Pesquisador de Objetos do SSMS expandindo o nó **Alta Disponibilidade Always On**:

    ![A captura de tela mostra as réplicas de disponibilidade primárias e secundárias.](./media/rhel-high-availability-stonith-tutorial/availability-group-joined.png)

### <a name="add-a-database-to-the-availability-group"></a>Adicionar um banco de dados ao grupo de disponibilidade

Seguiremos o artigo [configurar o grupo de disponibilidade sobre como adicionar um banco de dados](/sql/linux/sql-server-linux-availability-group-configure-ha#add-a-database-to-the-availability-group).

Os comandos Transact-SQL a seguir são usados nesta etapa. Execute estes comandos na réplica primária:

```sql
CREATE DATABASE [db1]; -- creates a database named db1
GO

ALTER DATABASE [db1] SET RECOVERY FULL; -- set the database in full recovery mode
GO

BACKUP DATABASE [db1] -- backs up the database to disk
   TO DISK = N'/var/opt/mssql/data/db1.bak';
GO

ALTER AVAILABILITY GROUP [ag1] ADD DATABASE [db1]; -- adds the database db1 to the AG
GO
```

### <a name="verify-that-the-database-is-created-on-the-secondary-servers"></a>Verificar se o banco de dados foi criado nos servidores secundários

Em cada réplica secundária do SQL Server, execute a seguinte consulta para ver se o banco de dados db1 foi criado e está em um estado SINCRONIZADO:

```
SELECT * FROM sys.databases WHERE name = 'db1';
GO
SELECT DB_NAME(database_id) AS 'database', synchronization_state_desc FROM sys.dm_hadr_database_replica_states;
```

Se o `synchronization_state_desc` listar SINCRONIZADO para `db1`, isso significará que as réplicas estão sincronizadas. As secundárias estão mostrando `db1` na réplica primária.

## <a name="create-availability-group-resources-in-the-pacemaker-cluster"></a>Criar recursos do grupo de disponibilidade no cluster do Pacemaker

Seguiremos o guia para [criar os recursos do grupo de disponibilidade no cluster do Pacemaker](/sql/linux/sql-server-linux-create-availability-group#create-the-availability-group-resources-in-the-pacemaker-cluster-external-only).

> [!NOTE]
> Este artigo contém referências ao termo "servidor subordinado", um termo que a Microsoft não usa mais. Quando o termo for removido do software, também o removeremos deste artigo.

### <a name="create-the-ag-cluster-resource"></a>Criar o recurso de cluster do grupo de disponibilidade

1. Use um dos comandos a seguir com base no ambiente escolhido antes para criar o recurso `ag_cluster` no grupo de disponibilidade `ag1`.

    **RHEL 7**
  
    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s master notify=true
    ```

    **RHEL 8**
  
    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s promotable notify=true
    ```

2. Confira seu recurso e verifique se eles estão online antes de continuar usando o seguinte comando:

    ```bash
    sudo pcs resource
    ```

    Você deve ver o seguinte resultado:
    
    **RHEL 7** 
    
    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
    Masters: [ <VM1> ]
    Slaves: [ <VM2> <VM3> ]
    ```
    
    **RHEL 8** 
    
    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    * Clone Set: ag_cluster-clone [ag_cluster] (promotable):
    * ag_cluster             (ocf::mssql:ag) :            Slave VMrhel3 (Monitoring) 
    * ag_cluster             (ocf::mssql:ag) :            Master VMrhel1 (Monitoring)
    * ag_cluster             (ocf::mssql:ag) :            Slave VMrhel2 (Monitoring)
    ```


### <a name="create-a-virtual-ip-resource"></a>Criar um recurso de IP virtual

1. Use um endereço IP estático disponível de sua rede para criar um recurso de IP virtual. Você pode encontrar um usando a ferramenta de comando `nmap`.

    ```bash
    nmap -sP <IPRange>
    # For example: nmap -sP 10.0.0.*
    # The above will scan for all IP addresses that are already occupied in the 10.0.0.x space.
    ```

2. Defina a propriedade **stonith-enabled** como false

    ```bash
    sudo pcs property set stonith-enabled=false
    ```

3. Crie o recurso de IP virtual usando o seguinte comando:

    - Substitua o valor `<availableIP>` abaixo por um endereço IP não utilizado.

    ```bash
    sudo pcs resource create virtualip ocf:heartbeat:IPaddr2 ip=<availableIP>
    ```

### <a name="add-constraints"></a>Adicionar restrições

1. Para garantir que o endereço IP e o recurso do AG estejam em execução no mesmo nó, uma restrição de colocalização deve ser configurada. Execute o comando a seguir:

   **RHEL 7**
  
    ```bash
    sudo pcs constraint colocation add virtualip ag_cluster-master INFINITY with-rsc-role=Master
    ```

   **RHEL 8**
   
    ```bash
     sudo pcs constraint colocation add virtualip with master ag_cluster-clone INFINITY with-rsc-role=Master
    ```
  
2. Crie uma restrição de ordenação para garantir que o recurso AG esteja em funcionamento antes do endereço IP. Enquanto a restrição de colocalização implica uma restrição de ordenação, isso a impõe.

   **RHEL 7**
   
    ```bash
    sudo pcs constraint order promote ag_cluster-master then start virtualip
    ```

   **RHEL 8**
   
    ```bash
    sudo pcs constraint order promote ag_cluster-clone then start virtualip
    ```
  
3. Para verificar as restrições, execute o seguinte comando:

    ```bash
    sudo pcs constraint list --full
    ```

    Você deve ver o seguinte resultado:
    
    **RHEL 7**

    ```
    Location Constraints:
    Ordering Constraints:
          promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
          virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```
    
    **RHEL 8**
    
    ```output
    Location Constraints:
    Ordering Constraints:
            promote ag_cluster-clone then start virtualip (kind:Mandatory) (id:order-ag_cluster-clone-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-clone (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-clone-INFINITY)
    Ticket Constraints:
    ```

### <a name="re-enable-stonith"></a>Reabilitar stonith

Estamos prontos para o teste. Reabilite o stonith no cluster executando o seguinte comando no Nó 1:

```bash
sudo pcs property set stonith-enabled=true
```

### <a name="check-cluster-status"></a>Verificar o status do cluster

Você pode verificar o status dos recursos de cluster usando o seguinte comando:

```output
[<username>@VM1 ~]$ sudo pcs status
Cluster name: az-hacluster
Stack: corosync
Current DC: <VM3> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
Last updated: Sat Dec  7 00:18:38 2019
Last change: Sat Dec  7 00:18:02 2019 by root via cibadmin on VM1

3 nodes configured
5 resources configured

Online: [ <VM1> <VM2> <VM3> ]

Full list of resources:

 Master/Slave Set: ag_cluster-master [ag_cluster]
     Masters: [ <VM2> ]
     Slaves: [ <VM1> <VM3> ]
 virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
 rsc_st_azure   (stonith:fence_azure_arm):      Started <VM1>

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
```

## <a name="test-failover"></a>Failover de Teste

Para verificar se a configuração foi bem-sucedida até agora, testaremos um failover. Para obter mais informações, confira [Failover do grupos de disponibilidade Always On no Linux](/sql/linux/sql-server-linux-availability-group-failover-ha).

1. Execute o comando a seguir para fazer failover manual da réplica primária para `<VM2>`. Substitua `<VM2>` pelo valor do nome do servidor.

   **RHEL 7**
   
    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

   **RHEL 8**
   
    ```bash
    sudo pcs resource move ag_cluster-clone <VM2> --master
    ```

   Você também pode especificar uma opção adicional para que a restrição temporária criada para mover o recurso para um nó desejado seja desabilitada automaticamente e não seja necessário executar as etapas 2 e 3 abaixo.

   **RHEL 7**

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master lifetime=30S
    ```

   **RHEL 8**

    ```bash
    sudo pcs resource move ag_cluster-clone <VM2> --master lifetime=30S
    ```

   Outra alternativa para automatizar as etapas 2 e 3 abaixo que desmarcam a restrição temporária no próprio comando de movimentação de recursos é combinar vários comandos em uma linha. 

   **RHEL 7**

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master && sleep 30 && pcs resource clear ag_cluster-master
    ```

   **RHEL 8**

    ```bash
    sudo pcs resource move ag_cluster-clone <VM2> --master && sleep 30 && pcs resource clear ag_cluster-clone
    ```
    
2. Se você verificar suas restrições novamente, verá que outra restrição foi adicionada devido ao failover manual:
    
    **RHEL 7**
    
    ```output
    [<username>@VM1 ~]$ sudo pcs constraint list --full
    Location Constraints:
          Resource: ag_cluster-master
            Enabled on: VM2 (score:INFINITY) (role: Master) (id:cli-prefer-ag_cluster-master)
    Ordering Constraints:
            promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

    **RHEL 8**
    
    ```output
    [<username>@VM1 ~]$ sudo pcs constraint list --full
    Location Constraints:
          Resource: ag_cluster-master
            Enabled on: VM2 (score:INFINITY) (role: Master) (id:cli-prefer-ag_cluster-clone)
    Ordering Constraints:
            promote ag_cluster-clone then start virtualip (kind:Mandatory) (id:order-ag_cluster-clone-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-clone (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-clone-INFINITY)
    Ticket Constraints:
    ```
    
3. Remova a restrição com a ID `cli-prefer-ag_cluster-master` usando o seguinte comando:

    **RHEL 7**
    
    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

    **RHEL 8**
    
    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-clone
    ```

1. Verifique os recursos de cluster usando o comando `sudo pcs resource`; você verá que a instância primária agora é `<VM2>`.

    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         ag_cluster (ocf::mssql:ag):        FAILED <VM1> (Monitoring)
         Masters: [ <VM2> ]
         Slaves: [ <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         Masters: [ <VM2> ]
         Slaves: [ <VM1> <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

## <a name="test-fencing"></a>Isolamento de teste

Você pode testar o STONITH executando o comando a seguir. Tente executar o comando abaixo de `<VM1>` para `<VM3>`.

```bash
sudo pcs stonith fence <VM3> --debug
```

> [!NOTE]
> Por padrão, a ação de limite desativa e ativa o nó. Se você quiser deixar o nó offline, use a opção `--off` no comando.

Você deverá obter o seguinte resultado:

```output
[<username>@<VM1> ~]$ sudo pcs stonith fence <VM3> --debug
Running: stonith_admin -B <VM3>
Return Value: 0
--Debug Output Start--
--Debug Output End--
 
Node: <VM3> fenced
```
Para obter mais informações sobre como testar um dispositivo de limite, confira o artigo [Red Hat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/s1-stonithtest-haar) a seguir.

## <a name="next-steps"></a>Próximas etapas

Para utilizar um Ouvinte do grupo de disponibilidade para suas instâncias do SQL Server, você precisará criar e configurar um balanceador de carga.

> [!div class="nextstepaction"]
> [Tutorial: Configurar um ouvinte do grupo de disponibilidade para o SQL Server em máquinas virtuais do RHEL no Azure](rhel-high-availability-listener-tutorial.md)
