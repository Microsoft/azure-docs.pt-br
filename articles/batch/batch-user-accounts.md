---
title: Executar tarefas em contas de usuário - Lote do Azure | Microsoft Docs
description: Configurar contas de usuário para executar tarefas no Lote do Azure
services: batch
author: laurenhughes
manager: gwallace
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 93b3004303dd1587340b467c2fd52cb7233c95fd
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322713"
---
> [!NOTE] 
> As contas de usuário discutidas neste artigo são diferentes das contas de usuários usadas para protocolo RDP (RDP) ou Secure Shell (SSH), por motivos de segurança. 
>
> Para se conectar a um nó em execução na configuração da máquina virtual Linux via SSH, confira [Usar Área de Trabalho Remota para uma VM Linux no Azure](../virtual-machines/virtual-machines-linux-use-remote-desktop.md). Para se conectar a nós em execução no Windows via RDP, confira [Conectar-se a uma VM do Windows Server](../virtual-machines/windows/connect-logon.md).<br /><br />
> Para se conectar a um nó em execução na configuração do serviço de nuvem via RDP, confira [Habilitar a Conexão de Área de Trabalho Remota para uma função nos Serviços de Nuvem do Azure](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md).
>
>


# <a name="run-tasks-under-user-accounts-in-batch"></a>Executar tarefas em contas de usuário no Lote

Uma tarefa no Lote do Azure sempre é executada em uma conta de usuário. Por padrão, as tarefas são executadas em contas de usuário padrão, sem permissões de administrador. Essas configurações da conta de usuário padrão normalmente são suficientes. No entanto, para determinados cenários, é útil ser capaz de configurar a conta de usuário sob a qual você deseja executar uma tarefa. Este artigo descreve os tipos de conta de usuário e como configurá-los para seu cenário.

## <a name="types-of-user-accounts"></a>Tipos de conta de usuário

O Lote do Azure fornece dois tipos de conta de usuário para execução de tarefas:

- **Contas de usuário automático** As contas de usuário automático são contas de usuário internas criadas automaticamente pelo serviço de Lote. Por padrão, as tarefas são executadas em uma conta de usuário automático. Você pode configurar a especificação de usuário automático para uma tarefa a fim de indicar sob qual conta de usuário automático ela deve ser executada. A especificação de usuário automático permite que você especifique o nível de elevação e o escopo da conta de usuário automático que executará a tarefa. 

- **Uma conta de usuário nomeado** Você pode especificar uma ou mais contas de usuário nomeado para um pool ao criar o pool. Cada conta de usuário é criada em cada nó do pool. Além do nome de conta, você especifica a senha, o nível de elevação e, para pools do Linux, a chave privada SSH da conta do usuário. Ao adicionar uma tarefa, você pode especificar a conta de usuário nomeado na qual essa tarefa deve ser executada.

> [!IMPORTANT] 
> A versão 2017-01-01.4.0 do serviço de Lote introduz uma alteração significativa que exige a atualização do seu código para chamar essa versão. Se você estiver migrando o código de uma versão antiga do Lote, observe que a propriedade **runElevated** não tem mais suporte na API REST ou nas bibliotecas de cliente do Lote. Use a nova propriedade **userIdentity** de uma tarefa para especificar o nível de elevação. Veja a seção [Atualizar seu código para a biblioteca de cliente mais recente do Lote](#update-your-code-to-the-latest-batch-client-library) para obter diretrizes rápidas a fim de atualizar o código do Lote se estiver usando uma das bibliotecas de cliente.
>
>

## <a name="user-account-access-to-files-and-directories"></a>Acesso de conta de usuário a arquivos e diretórios

Uma conta de usuário automático e uma conta de usuário nomeado têm acesso de leitura/gravação ao diretório de trabalho da tarefa, diretório compartilhado e diretório de tarefas de várias instâncias. Ambos os tipos de conta têm acesso de leitura nos diretórios de preparação de inicialização e de trabalho.

Se uma tarefa for executada na mesma conta que foi usada para executar uma tarefa de inicialização, a tarefa terá acesso de leitura/gravação ao diretório da tarefa de inicialização. Da mesma forma, se uma tarefa for executada na mesma conta que foi usada para executar uma tarefa de preparação de trabalho, a tarefa terá acesso de leitura/gravação ao diretório de tarefa de preparação de trabalho. Se uma tarefa for executada em uma conta diferente da tarefa de inicialização ou da tarefa de preparação de trabalho, a tarefa terá acesso somente leitura ao respectivo diretório.

Para saber mais sobre como acessar arquivos e diretórios de uma tarefa, confira [Desenvolver soluções de computação paralela em larga escala com o Lote](batch-api-basics.md#files-and-directories).

## <a name="elevated-access-for-tasks"></a>Acesso elevado para tarefas 

O nível de elevação da conta de usuário indica se uma tarefa é executada com acesso elevado. Tanto uma conta de usuário automático, quanto uma conta de usuário nomeado podem ser executadas com acesso elevado. As duas opções de nível de elevação são:

- **Não administrador:** a tarefa é executada como um usuário padrão sem acesso elevado. O nível de elevação padrão de uma conta de usuário do Lote é sempre **Não Administrador**.
- **Administrador:** a tarefa é executada como um usuário com acesso elevado e opera com permissões completas de Administrador. 

## <a name="auto-user-accounts"></a>Contas de usuário automático

Por padrão, as tarefas são executadas no Lote em uma conta de usuário automático, como um usuário padrão sem acesso elevado e com escopo de tarefa. Quando a especificação de usuário automático é configurada para o escopo de tarefa, o serviço de Lote cria uma conta de usuário automático apenas para essa tarefa.

A alternativa ao escopo de tarefa é o escopo de pool. Quando a especificação de usuário automático para uma tarefa é configurada para o escopo de pool, a tarefa é executada em uma conta de usuário automático que está disponível para qualquer tarefa no pool. Para saber mais sobre o escopo de pool, confira a seção Executar uma tarefa como o usuário automático com escopo de pool.   

O escopo padrão é diferente em nós do Windows e Linux:

- Em nós do Windows, as tarefas são executadas no escopo de tarefa por padrão.
- Nós do Linux sempre são executados no escopo de pool.

Há quatro configurações possíveis para a especificação de usuário automático, e cada uma delas corresponde a uma conta de usuário automático exclusiva:

- Acesso de não administrador com escopo de tarefa (a especificação padrão de usuário automático)
- Acesso de administrador (elevado) com escopo de tarefa
- Acesso de não administrador com escopo de pool
- Acesso de administrador com escopo de pool

> [!IMPORTANT] 
> As tarefas em execução no escopo de tarefa não têm de fato acesso a outras tarefas em um nó. No entanto, um usuário mal-intencionado com acesso à conta pode contornar essa restrição enviando uma tarefa que é executada com privilégios de administrador e acessa outros diretórios de tarefa. Um usuário mal-intencionado também pode usar um RDP ou SSH para se conectar a um nó. É importante proteger o acesso às chaves da sua conta do Lote para evitar uma situação como essa. Se você suspeitar de que sua conta pode ter sido comprometida, certifique-se de regenerar as chaves.
>
>

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Executar uma tarefa como um usuário automático com acesso elevado

Você pode configurar a especificação de usuário automático para privilégios de administrador quando precisar executar uma tarefa com acesso elevado. Por exemplo, uma tarefa de inicialização pode precisar de acesso elevado para instalar software no nó.

> [!NOTE] 
> De modo geral, é melhor usar acesso elevado somente quando necessário. A prática recomendada é conceder o privilégio mínimo necessário para obter o resultado desejado. Por exemplo, se uma tarefa de inicialização instala o software para o usuário atual, e não para todos os usuários, você poderá evitar conceder acesso elevado para tarefas. É possível configurar a especificação de usuário automático para escopo de pool e acesso de não administrador para todas as tarefas que precisam ser executadas na mesma conta, incluindo a tarefa de inicialização. 
>
>

Os snippets de código a seguir mostram como configurar a especificação de usuário automático. Os exemplos definem o nível de elevação para `Admin` e o escopo para `Task`. O escopo de tarefa é a configuração padrão, mas está incluído aqui em virtude do exemplo.

#### <a name="batch-net"></a>.NET no Lote

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin, scope: AutoUserScope.Task));
```
#### <a name="batch-java"></a>Java no Lote

```java
taskToAdd.withId(taskId)
        .withUserIdentity(new UserIdentity()
            .withAutoUser(new AutoUserSpecification()
                .withElevationLevel(ElevationLevel.ADMIN))
                .withScope(AutoUserScope.TASK));
        .withCommandLine("cmd /c echo hello");                        
```

#### <a name="batch-python"></a>Python no Lote

```python
user = batchmodels.UserIdentity(
    auto_user=batchmodels.AutoUserSpecification(
        elevation_level=batchmodels.ElevationLevel.admin,
        scope=batchmodels.AutoUserScope.task))
task = batchmodels.TaskAddParameter(
    id='task_1',
    command_line='cmd /c "echo hello world"',
    user_identity=user)
batch_client.task.add(job_id=jobid, task=task)
```

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Executar uma tarefa como um usuário automático com escopo de pool

Quando um nó é provisionado, duas contas de usuário automático em todo o pool são criadas em cada nó do pool, uma com acesso elevado e outra sem. Configurar o escopo do usuário automático para o escopo de pool em uma determinada tarefa executa a tarefa em uma dessas duas contas de usuário automático em todo o pool. 

Quando você especifica o escopo de pool para o usuário automático, todas as tarefas que são executadas com acesso de administrador o são na mesma conta de usuário automático em todo o pool. Da mesma forma, as tarefas executadas sem permissões de administrador também o são em uma única conta de usuário automático em todo o pool. 

> [!NOTE] 
> As duas contas de usuário automático em todo o pool são distintas. As tarefas executadas na conta administrativa em todo o pool não podem compartilhar dados com tarefas em execução na conta padrão, e vice-versa. 
>
>

A vantagem da execução na mesma conta de usuário automático é que as tarefas podem compartilhar dados com outras tarefas em execução no mesmo nó.

O compartilhamento de segredos entre tarefas é um cenário em que a execução de tarefas em um das duas contas de usuário automático em todo o pool é útil. Por exemplo, suponha que a tarefa de inicialização precise provisionar um segredo no nó que outras tarefas podem usar. Você pode usar o DPAPI (API da Proteção de Dados) do Windows, mas ele exige privilégios administrativos. Em vez disso, é possível proteger o segredo no nível de usuário. As tarefas em execução na mesma conta de usuário podem acessar o segredo sem acesso elevado.

Outro cenário em que talvez seja conveniente executar tarefas em uma conta de usuário automático com escopo de pool é em um compartilhamento de arquivos MPI (Interface de Transmissão de Mensagens). Um compartilhamento de arquivos MPI é útil quando os nós da tarefa MPI precisam trabalhar nos mesmos dados de arquivo. O nó de cabeçalho cria um compartilhamento de arquivos que os nós filho podem acessar se estiverem sendo executados na mesma conta de usuário automático. 

O snippet de código a seguir define o escopo do usuário automático para escopo de pool em uma tarefa do .NET no Lote. O nível de elevação é omitido, de modo que a tarefa é executada na conta de usuário automático em todo o pool padrão.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Contas de usuário nomeado

Você pode definir contas de usuário nomeado ao criar um pool. Uma conta de usuário nomeado tem um nome e uma senha fornecidos por você. É possível especificar o nível de elevação para uma conta de usuário nomeado. Para nós Linux, você também pode fornecer uma chave privada SSH.

Uma conta de usuário nomeado existe em todos os nós no pool e está disponível para todas as tarefas em execução nesses nós. É possível definir qualquer número de usuários nomeados para um pool. Ao adicionar uma tarefa ou um conjunto de tarefas, você pode especificar que a tarefa será executada em uma das contas de usuário nomeado definidas no pool.

Uma conta de usuário nomeado é útil quando você deseja executar todas as tarefas em um trabalho na mesma conta de usuário, mas isolá-las de tarefas em execução em outros trabalhos ao mesmo tempo. Por exemplo, é possível criar um usuário nomeado para cada trabalho e executar as tarefas de cada trabalho nessa conta de usuário nomeado. Cada trabalho pode compartilhar um segredo com suas próprias tarefas, mas não com tarefas em execução em outros trabalhos.

Você também pode usar uma conta de usuário nomeado para executar uma tarefa que defina permissões em recursos externos, como compartilhamentos de arquivos. Com uma conta de usuário nomeado, você controla a identidade do usuário e pode usar a identidade do usuário para definir permissões.  

As contas de usuário nomeado permitem o SSH sem senha entre os nós Linux. É possível usar uma conta de usuário nomeado com nós Linux que precisam executar tarefas de várias instâncias. Cada nó no pool pode executar tarefas em uma conta de usuário definida no pool inteiro. Para saber mais sobre tarefas de várias instâncias, confira [Usar tarefas de várias instâncias para executar aplicativos de MPI](batch-mpi.md).

### <a name="create-named-user-accounts"></a>Criar contas de usuário nomeado

Para criar contas de usuário nomeado no Lote, adicione um conjunto de contas de usuário no pool. Os snippets de código a seguir mostram como criar contas de usuário nomeado em .NET, Java e Python. Esses snippets de código mostram como criar contas nomeadas de administrador e não administrador em um pool. Os exemplos criam pools usando a configuração de serviço de nuvem, mas você usa a mesma abordagem na criação de um pool do Windows ou Linux usando a configuração da máquina virtual.

#### <a name="batch-net-example-windows"></a>Exemplo de .NET do Lote (Windows)

```csharp
CloudPool pool = null;
Console.WriteLine("Creating pool [{0}]...", poolId);

// Create a pool using the cloud service configuration.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));   

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount("adminUser", "xyz123", ElevationLevel.Admin),
    new UserAccount("nonAdminUser", "123xyz", ElevationLevel.NonAdmin),
};

// Commit the pool.
await pool.CommitAsync();
```

#### <a name="batch-net-example-linux"></a>Exemplo de .NET do Lote (Linux)

```csharp
CloudPool pool = null;

// Obtain a collection of all available node agent SKUs.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. 
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the virtual machine configuration to use to create the pool.
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

Console.WriteLine("Creating pool [{0}]...", poolId);

// Create the unbound pool.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,                                             
    virtualMachineSize: "Standard_A1",                                      
    virtualMachineConfiguration: virtualMachineConfiguration);                  

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount(
        name: "adminUser",
        password: "xyz123",
        elevationLevel: ElevationLevel.Admin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 12345,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
    new UserAccount(
        name: "nonAdminUser",
        password: "123xyz",
        elevationLevel: ElevationLevel.NonAdmin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 45678,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
};

// Commit the pool.
await pool.CommitAsync();
```


#### <a name="batch-java-example"></a>Exemplo de Java no Lote

```java
List<UserAccount> userList = new ArrayList<>();
userList.add(new UserAccount().withName(adminUserAccountName).withPassword(adminPassword).withElevationLevel(ElevationLevel.ADMIN));
userList.add(new UserAccount().withName(nonAdminUserAccountName).withPassword(nonAdminPassword).withElevationLevel(ElevationLevel.NONADMIN));
PoolAddParameter addParameter = new PoolAddParameter()
        .withId(poolId)
        .withTargetDedicatedNodes(POOL_VM_COUNT)
        .withVmSize(POOL_VM_SIZE)
        .withCloudServiceConfiguration(configuration)
        .withUserAccounts(userList);
batchClient.poolOperations().createPool(addParameter);
```

#### <a name="batch-python-example"></a>Exemplo de Python no Lote

```python
users = [
    batchmodels.UserAccount(
        name='pool-admin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.admin)
    batchmodels.UserAccount(
        name='pool-nonadmin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.nonadmin)
]
pool = batchmodels.PoolAddParameter(
    id=pool_id,
    user_accounts=users,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        node_agent_sku_id=sku_to_use),
    vm_size=vm_size,
    target_dedicated=vm_count)
batch_client.pool.add(pool)
```

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Executar uma tarefa em uma conta de usuário nomeado com acesso elevado

Para executar uma tarefa como um usuário com acesso elevado, defina a propriedade **UserIdentity** da tarefa como uma conta de usuário nomeado que foi criada com sua propriedade **ElevationLevel** definida como `Admin`.

Este snippet de código especifica que a tarefa deve ser executada em uma conta de usuário nomeado. Essa conta de usuário nomeado foi definida no pool quando este foi criado. Nesse caso, a conta de usuário nomeado foi criada com permissões de administrador:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>Atualizar seu código para a biblioteca de cliente mais recente do Lote

A versão 2017-01-01.4.0 do serviço de Lote introduz uma alteração significativa, substituindo a propriedade **runElevated** disponível em versões anteriores pela propriedade **userIdentity**. As tabelas a seguir fornecem um mapeamento simples que você pode usar para atualizar o código de versões anteriores das bibliotecas de cliente.

### <a name="batch-net"></a>.NET no Lote

| Se seu código usar...                  | Atualize-o para...                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated` não especificado | Nenhuma atualização é necessária                                                                                               |

### <a name="batch-java"></a>Java no Lote

| Se seu código usar...                      | Atualize-o para...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated` não especificado | Nenhuma atualização é necessária                                                                                                                     |

### <a name="batch-python"></a>Python no Lote

| Se seu código usar...                      | Atualize-o para...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`, em que <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin))`                |
| `run_elevated=False`                      | `user_identity=user`, em que <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.nonadmin))`             |
| `run_elevated` não especificado | Nenhuma atualização é necessária                                                                                                                                  |


## <a name="next-steps"></a>Próximas etapas

* Para uma visão geral detalhada do Lote, confira [Desenvolver soluções de computação paralela em grande escala com o Lote](batch-api-basics.md).
