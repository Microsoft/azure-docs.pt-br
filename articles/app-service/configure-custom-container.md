---
title: Configurar um contêiner personalizado
description: Saiba como configurar um contêiner personalizado no serviço Azure App. Este artigo mostra as tarefas de configuração mais comuns.
ms.topic: article
ms.date: 02/23/2021
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 8083c3c0c88d904ccb3ec75ae69a699867bd0f25
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704864"
---
# <a name="configure-a-custom-container-for-azure-app-service"></a>Configurar um contêiner personalizado para o Serviço de Aplicativo do Azure

Este artigo mostra como configurar um contêiner personalizado para ser executado no serviço Azure App.

::: zone pivot="container-windows"

Este guia fornece os principais conceitos e instruções para a Containerização de aplicativos do Windows no serviço de aplicativo. Se você nunca usou Azure App serviço, siga primeiro o [início rápido](quickstart-custom-container.md) e o [tutorial](tutorial-custom-container.md) do contêiner personalizado.

::: zone-end

::: zone pivot="container-linux"

Este guia fornece os principais conceitos e instruções para a Containerização de aplicativos do Linux no serviço de aplicativo. Se você nunca usou Azure App serviço, siga primeiro o [início rápido](quickstart-custom-container.md) e o [tutorial](tutorial-custom-container.md) do contêiner personalizado. Há também um início rápido e [tutorial](tutorial-multi-container-app.md)de [aplicativo de vários contêineres](quickstart-multi-container.md) .

::: zone-end

::: zone pivot="container-windows"

## <a name="supported-parent-images"></a>Imagens pai com suporte

Para sua imagem personalizada do Windows, você deve escolher a [imagem pai correta (imagem base)](https://docs.docker.com/develop/develop-images/baseimages/) para a estrutura que você deseja:

- Para implantar .NET Framework aplicativos, use uma imagem pai com base na versão do [LTSC (canal de manutenção em longo prazo)](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) do Windows Server Core. 
- Para implantar aplicativos .NET Core, use uma imagem pai com base na versão do [SAC (canal de manutenção semestral)](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) do nano do Windows Server. 

Leva algum tempo para baixar uma imagem pai durante a inicialização do aplicativo. No entanto, você pode reduzir o tempo de inicialização usando uma das seguintes imagens pai já armazenadas em cache no Serviço de Aplicativo do Azure:

- [MCR.Microsoft.com/Windows/servercore](https://hub.docker.com/_/microsoft-windows-servercore): 2004
- [MCR.Microsoft.com/Windows/servercore](https://hub.docker.com/_/microsoft-windows-servercore): ltsc2019
- [MCR.Microsoft.com/DOTNET/Framework/ASPNET](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/): 4,8-windowsservercore-2004
- [MCR.Microsoft.com/DOTNET/Framework/ASPNET](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/): 4,8-windowsservercore-ltsc2019
- [MCR.Microsoft.com/dotnet/Core/Runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-coserver-2004
- [MCR.Microsoft.com/dotnet/Core/Runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-coserver-1909
- [MCR.Microsoft.com/dotnet/Core/Runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-coserver-1903
- [MCR.Microsoft.com/dotnet/Core/Runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-coserver-1809
- [MCR.Microsoft.com/dotnet/Core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-coserver-2004
- [MCR.Microsoft.com/dotnet/Core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-coserver-1909
- [MCR.Microsoft.com/dotnet/Core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-coserver-1903
- [MCR.Microsoft.com/dotnet/Core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-coserver-1809

::: zone-end

## <a name="change-the-docker-image-of-a-custom-container"></a>Alterar a imagem do Docker de um contêiner personalizado

Para alterar um aplicativo de contêiner personalizado existente da imagem do Docker atual para uma nova imagem, use o seguinte comando:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <docker-hub-repo>/<image>
```

## <a name="use-an-image-from-a-private-registry"></a>Usar uma imagem de um registro particular

Para usar uma imagem de um registro privado, como o registro de contêiner do Azure, execute o seguinte comando:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

Para o *\<username>* e *\<password>* o, forneça as credenciais de logon para sua conta de registro particular.

## <a name="i-dont-see-the-updated-container"></a>Não vejo o contêiner atualizado

Se você alterar as configurações de contêiner do Docker para apontar para um novo contêiner, pode levar alguns minutos para que o aplicativo sirva solicitações HTTP do novo contêiner. Enquanto o novo contêiner está sendo puxado e iniciado, o serviço de aplicativo continua a atender solicitações do contêiner antigo. Somente quando o novo contêiner é iniciado e está pronto para receber solicitações, o serviço de aplicativo começa a enviar solicitações para ele.

## <a name="how-container-images-are-stored"></a>Como as imagens de contêiner são armazenadas

Na primeira vez que você executa uma imagem personalizada do Docker no serviço de aplicativo, o serviço de aplicativo faz um `docker pull` e efetua pull de todas as camadas de imagem. Essas camadas são armazenadas em disco, como se você estivesse usando o Docker local. Cada vez que o aplicativo é reiniciado, o serviço de aplicativo faz um `docker pull` , mas apenas efetua pull das camadas que foram alteradas. Se não houver nenhuma alteração, o serviço de aplicativo usará camadas existentes no disco local.

Se o aplicativo alterar as instâncias de computação por qualquer motivo, como escalar ou reduzir verticalmente os tipos de preço, o serviço de aplicativo deverá retirar todas as camadas novamente. O mesmo será verdadeiro se você expandir para adicionar instâncias adicionais. Também há casos raros em que as instâncias do aplicativo podem ser alteradas sem uma operação de dimensionamento.

## <a name="configure-port-number"></a>Configurar número da porta

Por padrão, o serviço de aplicativo assume que seu contêiner personalizado está escutando na porta 80. Se o seu contêiner escutar uma porta diferente, defina a `WEBSITES_PORT` configuração do aplicativo em seu aplicativo do serviço de aplicativo. Você pode defini-lo por meio do [Cloud Shell](https://shell.azure.com). No bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

No PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_PORT"="8000"}
```

Atualmente, o serviço de aplicativo permite que o contêiner exponha apenas uma porta para solicitações HTTP. 

## <a name="configure-environment-variables"></a>Configurar variáveis de ambiente

Seu contêiner personalizado pode usar variáveis de ambiente que precisam ser fornecidas externamente. Você pode passá-los por meio do [Cloud Shell](https://shell.azure.com). No bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings DB_HOST="myownserver.mysql.database.azure.com"
```

No PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"DB_HOST"="myownserver.mysql.database.azure.com"}
```

Quando seu aplicativo é executado, as configurações do aplicativo do serviço de aplicativo são injetadas no processo como variáveis de ambiente automaticamente. Você pode verificar as variáveis de ambiente do contêiner com a URL `https://<app-name>.scm.azurewebsites.net/Env)` .

::: zone pivot="container-windows"
Para os contêineres baseados no IIS ou .NET Framework (4,0 ou superior), eles são injetados `System.ConfigurationManager` como configurações de aplicativo .net e cadeias de conexão automaticamente pelo serviço de aplicativo. Para todas as outras linguagens ou estruturas, elas são fornecidas como variáveis de ambiente para o processo, com um dos seguintes prefixos correspondentes:

- `APPSETTING_`
- `SQLCONTR_`
- `MYSQLCONTR_`
- `SQLAZURECOSTR_`
- `POSTGRESQLCONTR_`
- `CUSTOMCONNSTR_`

::: zone-end

::: zone pivot="container-linux"

Esse método funciona tanto para aplicativos de contêiner único quanto para aplicativos de vários contêineres, onde as variáveis de ambiente são especificadas no arquivo *Docker-Compose. yml* .

::: zone-end

## <a name="use-persistent-shared-storage"></a>Usar armazenamento compartilhado persistente

::: zone pivot="container-windows"

Você pode usar o diretório *C:\home* no sistema de arquivos do seu aplicativo para manter os arquivos entre as reinicializações e compartilhá-los entre instâncias. O `C:\home` em seu aplicativo é fornecido para permitir que seu aplicativo de contêiner acesse o armazenamento persistente.

Quando o armazenamento persistente é desabilitado, as gravações no `C:\home` diretório não são mantidas. Os [logs de host do Docker e os logs de contêiner](#access-diagnostic-logs) são salvos em um armazenamento compartilhado persistente padrão que não está anexado ao contêiner. Quando o armazenamento persistente está habilitado, todas as gravações no `C:\home` diretório são persistidas e podem ser acessadas por todas as instâncias de um aplicativo expandido e o log pode ser acessado em `C:\home\LogFiles` .

::: zone-end

::: zone pivot="container-linux"

Você pode usar o diretório */Home* no sistema de arquivos do seu aplicativo para manter os arquivos entre as reinicializações e compartilhá-los entre instâncias. O `/home` em seu aplicativo é fornecido para permitir que seu aplicativo de contêiner acesse o armazenamento persistente.

Quando o armazenamento persistente é desabilitado, as gravações no `/home` diretório não são mantidas entre as reinicializações do aplicativo ou entre várias instâncias. A única exceção é o `/home/LogFiles` diretório, que é usado para armazenar os logs do Docker e do contêiner. Quando o armazenamento persistente está habilitado, todas as gravações no `/home` diretório são persistidas e podem ser acessadas por todas as instâncias de um aplicativo expandido.

::: zone-end

Por padrão, o armazenamento persistente está desabilitado e a configuração não é exposta nas configurações do aplicativo. Para habilitá-lo, defina a `WEBSITES_ENABLE_APP_SERVICE_STORAGE` configuração do aplicativo por meio do [Cloud Shell](https://shell.azure.com). No bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

No PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_ENABLE_APP_SERVICE_STORAGE"=true}
```

> [!NOTE]
> Você também pode [configurar seu próprio armazenamento persistente](configure-connect-to-azure-storage.md).

## <a name="detect-https-session"></a>Detectar sessão HTTPS

O serviço de aplicativo encerra TLS/SSL em front-ends. Isso significa que as solicitações TLS/SSL nunca chegarão ao seu aplicativo. Você não precisa e não deve implementar nenhum suporte para TLS/SSL em seu aplicativo. 

Os front-ends estão localizados dentro dos data centers do Azure. Se você usar o TLS/SSL com seu aplicativo, o tráfego na Internet sempre será criptografado com segurança.

::: zone pivot="container-windows"

## <a name="customize-aspnet-machine-key-injection"></a>Personalizar a injeção de chave do computador ASP.NET

 Durante o início do contêiner, as chaves geradas automaticamente são injetadas no contêiner como as chaves de computador para rotinas de criptografia ASP.NET. Você pode [encontrar essas chaves em seu contêiner](#connect-to-the-container) procurando pelas seguintes variáveis de ambiente: `MACHINEKEY_Decryption` , `MACHINEKEY_DecryptionKey` , `MACHINEKEY_ValidationKey` , `MACHINEKEY_Validation` . 

As novas chaves em cada reinicialização podem redefinir a autenticação de formulários ASP.NET e o estado de exibição, caso seu aplicativo dependa delas. Para evitar a regeneração automática de chaves, [defina-as manualmente como configurações de aplicativo do serviço de aplicativo](#configure-environment-variables). 

## <a name="connect-to-the-container"></a>Conectar-se ao contêiner

Você pode se conectar ao seu contêiner do Windows diretamente para tarefas de diagnóstico navegando até `https://<app-name>.scm.azurewebsites.net/DebugConsole` . Veja como funciona:

- O console de depuração permite que você execute comandos interativos, como iniciar sessões do PowerShell, inspecionar chaves do registro e navegar pelo sistema de arquivos do contêiner inteiro.
- Ele funciona separadamente do navegador gráfico acima dele, que mostra apenas os arquivos em seu [armazenamento compartilhado](#use-persistent-shared-storage).
- Em um aplicativo expandido, o console de depuração está conectado a uma das instâncias de contêiner. Você pode selecionar uma instância diferente na lista suspensa **instância** no menu superior.
- Qualquer alteração feita no contêiner de dentro do *console não persiste quando* seu aplicativo é reiniciado (exceto por alterações no armazenamento compartilhado), porque ele não faz parte da imagem do Docker. Para manter suas alterações, como configurações do registro e instalação de software, torne-as parte do Dockerfile.

## <a name="access-diagnostic-logs"></a>Acessar logs de diagnóstico

O serviço de aplicativo registra ações pelo host do Docker, bem como atividades de dentro do contêiner. Os logs do host do Docker (logs de plataforma) são fornecidos por padrão, mas os logs de aplicativo ou de servidor Web de dentro do contêiner precisam ser habilitados manualmente. Para obter mais informações, consulte [habilitar o log do aplicativo](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) e [habilitar o log do servidor Web](troubleshoot-diagnostic-logs.md#enable-web-server-logging). 

Há várias maneiras de acessar os logs do Docker:

- [No Portal do Azure](#in-azure-portal)
- [No console do kudu](#from-the-kudu-console)
- [Com a API do kudu](#with-the-kudu-api)
- [Enviar logs ao Azure Monitor](troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview)

### <a name="in-azure-portal"></a>No Portal do Azure

Os logs do Docker são exibidos no portal, na página **configurações de contêiner** do seu aplicativo. Os logs são truncados, mas você pode baixar todos os logs clicando em **baixar**. 

### <a name="from-the-kudu-console"></a>No console do kudu

Navegue até `https://<app-name>.scm.azurewebsites.net/DebugConsole` e clique na pasta **LogFiles** para ver os arquivos de log individuais. Para baixar todo o diretório de arquivos de **log** , clique no ícone de **Download** à esquerda do nome do diretório. Você também pode acessar essa pasta usando um cliente FTP.

No terminal do console, você não pode acessar a `C:\home\LogFiles` pasta por padrão porque o armazenamento compartilhado persistente não está habilitado. Para habilitar esse comportamento no terminal do console, [habilite o armazenamento compartilhado persistente](#use-persistent-shared-storage).

Se você tentar baixar o log do Docker que está em uso no momento usando um cliente FTP, você poderá receber um erro devido a um bloqueio de arquivo.

### <a name="with-the-kudu-api"></a>Com a API do kudu

Navegue diretamente para `https://<app-name>.scm.azurewebsites.net/api/logs/docker` para ver os metadados dos logs do Docker. Você pode ver mais de um arquivo de log listado e a `href` propriedade permite baixar o arquivo de log diretamente. 

Para baixar todos os logs juntos em um arquivo ZIP, acesse `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip` .

## <a name="customize-container-memory"></a>Personalizar a memória do contêiner

Por padrão, todos os contêineres do Windows implantados no serviço Azure App são limitados a 1 GB de RAM. Você pode alterar esse valor fornecendo a `WEBSITE_MEMORY_LIMIT_MB` configuração do aplicativo por meio do [Cloud Shell](https://shell.azure.com). No bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_MEMORY_LIMIT_MB=2000
```

No PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_MEMORY_LIMIT_MB"=2000}
```

O valor é definido em MB e deve ser menor e igual à memória física total do host. Por exemplo, em um plano do serviço de aplicativo com 8 GB de RAM, o total cumulativo de `WEBSITE_MEMORY_LIMIT_MB` para todos os aplicativos não deve exceder 8 GB. Informações sobre a quantidade de memória disponível para cada tipo de preço podem ser encontradas em [preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/windows/), na seção plano do **contêiner Premium (Windows)** .

## <a name="customize-the-number-of-compute-cores"></a>Personalizar o número de núcleos de computação

Por padrão, um contêiner do Windows é executado com todos os núcleos disponíveis para o tipo de preço escolhido. Você pode desejar reduzir o número de núcleos que seu slot de preparo usa, por exemplo. Para reduzir o número de núcleos usados por um contêiner, defina a `WEBSITE_CPU_CORES_LIMIT` configuração do aplicativo como o número preferencial de núcleos. Você pode defini-lo por meio do [Cloud Shell](https://shell.azure.com). No bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --slot staging --settings WEBSITE_CPU_CORES_LIMIT=1
```

No PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_CPU_CORES_LIMIT"=1}
```

> [!NOTE]
> A atualização da configuração do aplicativo dispara a reinicialização automática, causando tempo de inatividade mínimo. Para um aplicativo de produção, considere trocá-lo em um slot de preparo, altere a configuração do aplicativo no slot de preparo e, em seguida, troque-o novamente em produção.

Verifique seu número ajustado acessando o console do kudu ( `https://<app-name>.scm.azurewebsites.net` ) e digitando os comandos a seguir usando o PowerShell. Cada comando gera um número.

```PowerShell
Get-ComputerInfo | ft CsNumberOfLogicalProcessors # Total number of enabled logical processors. Disabled processors are excluded.
Get-ComputerInfo | ft CsNumberOfProcessors # Number of physical processors.
```

Os processadores podem ser de vários núcleos ou processadores hyperthreading. Informações sobre quantos núcleos estão disponíveis para cada tipo de preço podem ser encontradas em [preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/windows/), na seção **plano do contêiner Premium (Windows)** .

## <a name="customize-health-ping-behavior"></a>Personalizar comportamento de ping de integridade

O serviço de aplicativo considera que um contêiner seja iniciado com êxito quando o contêiner é iniciado e responde a um ping HTTP. A solicitação de ping de integridade contém o cabeçalho `User-Agent= "App Service Hyper-V Container Availability Check"` . Se o contêiner for iniciado, mas não responder a um ping após determinado período de tempo, o serviço de aplicativo registrará um evento no log do Docker, dizendo que o contêiner não foi iniciado. 

Se seu aplicativo estiver com uso intensivo de recursos, o contêiner poderá não responder ao ping de HTTP no tempo. Para controlar as ações quando ocorre falha em pings HTTP, defina a `CONTAINER_AVAILABILITY_CHECK_MODE` configuração do aplicativo. Você pode defini-lo por meio do [Cloud Shell](https://shell.azure.com). No bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings CONTAINER_AVAILABILITY_CHECK_MODE="ReportOnly"
```

No PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"CONTAINER_AVAILABILITY_CHECK_MODE"="ReportOnly"}
```

A tabela a seguir mostra os valores possíveis:

| Valor | Descrições |
| - | - |
| **Corrige** | Reiniciar o contêiner após três verificações de disponibilidade consecutivas |
| **ReportOnly** | O valor padrão. Não reinicie o contêiner, mas o relatório nos logs do Docker para o contêiner após três verificações de disponibilidade consecutivas. |
| **Desativado** | Não Verifique a disponibilidade. |

## <a name="support-for-group-managed-service-accounts"></a>Suporte para Contas de Serviço Gerenciado de Grupo

No momento, não há suporte para contas de serviço gerenciado de grupo (gMSAs) em contêineres do Windows no serviço de aplicativo.

::: zone-end

::: zone pivot="container-linux"

## <a name="enable-ssh"></a>Habilitar SSH

O SSH permite a comunicação segura entre um contêiner e um cliente. Para que um contêiner personalizado dê suporte ao SSH, você deve adicioná-lo à própria imagem do Docker.

> [!TIP]
> Todos os contêineres internos do Linux no serviço de aplicativo adicionaram as instruções de SSH em seus repositórios de imagem. Você pode percorrer as instruções a seguir com o [ repositórioNode.js 10,14](https://github.com/Azure-App-Service/node/blob/master/10.14) para ver como ele está habilitado lá. A configuração no Node.js imagem interna é ligeiramente diferente, mas a mesma em princípio.

- Adicione [um arquivo de sshd_config](https://man.openbsd.org/sshd_config) ao repositório, como no exemplo a seguir.

    ```
    Port            2222
    ListenAddress       0.0.0.0
    LoginGraceTime      180
    X11Forwarding       yes
    Ciphers aes128-cbc,3des-cbc,aes256-cbc,aes128-ctr,aes192-ctr,aes256-ctr
    MACs hmac-sha1,hmac-sha1-96
    StrictModes         yes
    SyslogFacility      DAEMON
    PasswordAuthentication  yes
    PermitEmptyPasswords    no
    PermitRootLogin     yes
    Subsystem sftp internal-sftp
    ```

    > [!NOTE]
    > Este arquivo configura o OpenSSH e deve incluir os seguintes itens:
    > - `Port` deve ser definido como 2222.
    > - `Ciphers` deve incluir pelo menos um item nesta lista: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs` deve incluir pelo menos um item nesta lista: `hmac-sha1,hmac-sha1-96`.

- Em seu Dockerfile, adicione os seguintes comandos:

    ```Dockerfile
    # Install OpenSSH and set the password for root to "Docker!". In this example, "apk add" is the install instruction for an Alpine Linux-based image.
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 

    # Copy the sshd_config file to the /etc/ssh/ directory
    COPY sshd_config /etc/ssh/

    # Open port 2222 for SSH access
    EXPOSE 80 2222
    ```

    Essa configuração não permite conexões externas com o contêiner. A porta 2222 do contêiner é acessível somente dentro da rede de ponte de uma rede virtual privada e não pode ser acessada por um invasor na Internet.

- No script de inicialização do seu contêiner, inicie o servidor SSH.

    ```bash
    /usr/sbin/sshd
    ```

## <a name="access-diagnostic-logs"></a>Acessar logs de diagnóstico

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="configure-multi-container-apps"></a>Configurar aplicativos de vários contêineres

- [Usar armazenamento persistente no Docker Compose](#use-persistent-storage-in-docker-compose)
- [Limitações de visualização](#preview-limitations)
- [Opções de Docker Compose](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Usar armazenamento persistente no Docker Compose

Aplicativos de vários contêineres, como o WordPress, precisam de armazenamento persistente para funcionar corretamente. Para habilitá-lo, sua configuração de Docker Compose deve apontar para um local de armazenamento *fora* do contêiner. Os locais de armazenamento dentro de seu contêiner não mantêm as alterações além da reinicialização do aplicativo.

Habilite o armazenamento persistente definindo a `WEBSITES_ENABLE_APP_SERVICE_STORAGE` configuração do aplicativo, usando o comando [AZ webapp config appSettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) no [Cloud Shell](https://shell.azure.com).

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

No arquivo *Docker-Compose. yml* , mapeie a `volumes` opção para `${WEBAPP_STORAGE_HOME}` . 

`WEBAPP_STORAGE_HOME` é uma variável de ambiente no Serviço de Aplicativo mapeada para armazenamento persistente para o aplicativo. Por exemplo: 

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>Limitações de visualização

No momento, vários contêineres estão em versão prévia. Os seguintes recursos da plataforma do serviço de aplicativo não têm suporte:

- Autenticação/Autorização
- Identidades gerenciadas
- CORS

### <a name="docker-compose-options"></a>Opções de Docker Compose

As listas a seguir mostram opções de configuração com e sem suporte Docker Compose:

#### <a name="supported-options"></a>Opções com suporte

- .
- entrypoint
- ambiente
- image
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>Opções sem suporte

- build (não permitido)
- depends_on (ignorado)
- networks (ignorada)
- secrets (ignorados)
- portas diferentes de 80 e 8080 (ignoradas)

> [!NOTE]
> Quaisquer outras opções não explicitamente chamadas são ignoradas na visualização pública.

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: migrar o software personalizado para Azure App serviço usando um contêiner personalizado](tutorial-custom-container.md)

::: zone pivot="container-linux"

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo WordPress cm vários contêineres](tutorial-multi-container-app.md)

::: zone-end

Ou então, consulte recursos adicionais:

[Carregar certificado em contêineres do Windows/Linux](configure-ssl-certificate-in-code.md#load-certificate-in-linuxwindows-containers)
