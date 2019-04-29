---
title: Configurar os contêineres de cliente - serviço de aplicativo do Azure | Microsoft Docs
description: Saiba como configurar aplicativos do Node. js funcione no serviço de aplicativo do Azure
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: 1e5faa8d356b891d825586414c0a1a1b9fa47090
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60853313"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Configurar um contêiner do Linux personalizado para o serviço de aplicativo do Azure

Este artigo mostra como configurar um contêiner do Linux personalizado para ser executado no serviço de aplicativo do Azure.

Este guia fornece os principais conceitos e as instruções para uso de contêineres de aplicativos do Linux no serviço de aplicativo. Se você nunca usou o serviço de aplicativo do Azure, siga as [início rápido de contêiner personalizado](quickstart-docker-go.md) e [tutorial](tutorial-custom-docker-image.md) primeiro. Há também uma [início rápido do aplicativo de multicontêiner](quickstart-multi-container.md) e [tutorial](tutorial-multi-container-app.md).

## <a name="configure-port-number"></a>Configurar o número da porta

O servidor web em sua imagem personalizada pode usar uma porta diferente de 80. Informe o Azure sobre a porta usada pelo seu personalizado usando o `WEBSITES_PORT` configuração de aplicativo. A página do GitHub que contém a [amostra do Python neste tutorial](https://github.com/Azure-Samples/docker-django-webapp-linux) mostra que você precisa definir `WEBSITES_PORT` para _8000_. Você pode defini-lo executando [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) comando no Cloud Shell. Por exemplo: 

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Configurar variáveis de ambiente

Seu contêiner personalizado pode usar variáveis de ambiente que precisam ser fornecidos externamente. Você pode passá-los em executando [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) comando no Cloud Shell. Por exemplo: 

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Esse método funciona para aplicativos de contêiner único ou vários contêineres, em que as variáveis de ambiente são especificadas na *docker-Compose. yml* arquivo.

## <a name="use-persistent-shared-storage"></a>Usar o armazenamento compartilhado persistente

Você pode usar o */home* diretório no sistema de arquivos do seu aplicativo persistir arquivos entre as reinicializações e compartilhá-los entre instâncias. O `/home` no seu aplicativo é fornecido para permitir que seu aplicativo de contêiner acessar o armazenamento persistente.

Quando o armazenamento persistente está desabilitado, em seguida, grava o `/home` diretório não são persistidos entre as reinicializações de aplicativo ou em várias instâncias. A única exceção é o `/home/LogFiles` diretório, que é usado para armazenar os logs de Docker e o contêiner. Quando o armazenamento persistente está habilitado, todas as gravações para a `/home` diretório são persistentes e podem ser acessados por todas as instâncias de um aplicativo de expansão.

Por padrão, é o armazenamento persistente *desabilitada*. Para habilitar ou desabilitá-lo, defina as `WEBSITES_ENABLE_APP_SERVICE_STORAGE` configuração de aplicativo, executando [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) comando no Cloud Shell. Por exemplo: 

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

> [!NOTE]
> Você também pode [configurar o armazenamento persistente](how-to-serve-content-from-azure-storage.md).

## <a name="enable-ssh"></a>Habilitar SSH

O SSH permite a comunicação segura entre um contêiner e um cliente. Em ordem para um contêiner personalizado dar suporte a SSH, você deve adicioná-lo para o Dockerfile próprio.

> [!TIP]
> Todos os contêineres internos do Linux adicionou as instruções de SSH em seus repositórios de imagem. Você pode percorrer as instruções a seguir com o [repositório do Node. js 10.14](https://github.com/Azure-App-Service/node/blob/master/10.14) para ver como ele é ativado lá.

- Use o [executados](https://docs.docker.com/engine/reference/builder/#run) instruções para instalar o servidor SSH e defina a senha da conta raiz como `"Docker!"`. Por exemplo, para uma imagem baseada em [Alpine Linux](https://hub.docker.com/_/alpine), você precisa os comandos a seguir:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Essa configuração não permite conexões externas para o contêiner. SSH está disponível somente por meio de `https://<app-name>.scm.azurewebsites.net` e autenticado com as credenciais de publicação.

- Adicione [nesse arquivo sshd_config](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) para o repositório de imagens e use o [cópia](https://docs.docker.com/engine/reference/builder/#copy) instruções para copiar o arquivo para o */etc/ssh/* directory. Para obter mais informações sobre *sshd_config* arquivos, consulte [documentação OpenBSD](https://man.openbsd.org/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > O arquivo *sshd_config* deve incluir os seguintes itens:
    > - `Ciphers` deve incluir pelo menos um item nesta lista: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs` deve incluir pelo menos um item nesta lista: `hmac-sha1,hmac-sha1-96`.

- Use o [EXPOR](https://docs.docker.com/engine/reference/builder/#expose) instruções para abrir a porta 2222 no contêiner. Embora a senha raiz, a porta 2222 está inacessível da internet. Ele é acessível somente por contêineres dentro da rede ponte de uma rede virtual privada.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- No script de inicialização para seu contêiner, inicie o servidor SSH.

    ```bash
    /usr/sbin/sshd
    ```

    Por exemplo, veja como o padrão [contêiner do Node. js 10.14](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) inicia o servidor SSH.

## <a name="access-diagnostic-logs"></a>Acessar logs de diagnóstico

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>Configurar aplicativos de vários contêineres

- [Usar o armazenamento persistente no Docker Compose](#use-persistent-storage-in-docker-compose)
- [Limitações de visualização](#preview-limitations)
- [Opções de composição do docker](#docker-compose-options)
- [Opções de configuração do Kubernetes](#kubernetes-configuration-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Usar o armazenamento persistente no Docker Compose

Aplicativos de vários contêineres, como o WordPress precisam de armazenamento persistente para funcionar corretamente. Para habilitá-lo, a configuração do Docker Compose deve apontar para um local de armazenamento *fora* seu contêiner. Locais de armazenamento dentro de seu contêiner não são mantidas as alterações, além de reinicialização do aplicativo.

Habilitar o armazenamento persistente, definindo o `WEBSITES_ENABLE_APP_SERVICE_STORAGE` aplicativo de configuração, usando o [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) comando no Cloud Shell.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

No seu *docker-Compose. yml* do arquivo, mapeie o `volumes` opção `${WEBAPP_STORAGE_HOME}`. 

`WEBAPP_STORAGE_HOME` é uma variável de ambiente no Serviço de Aplicativo mapeada para armazenamento persistente para o aplicativo. Por exemplo: 

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="use-custom-storage-in-docker-compose"></a>Usar armazenamento personalizado no Docker Compose

O armazenamento do Azure (arquivos do Azure ou BLOBs do Azure) pode ser montado com aplicativos de vários contêineres usando a id personalizada. Para exibir o nome do id personalizada, execute [ `az webapp config storage-account list --name <app_name> --resource-group <resource_group>` ](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list).

No seu *docker-Compose. yml* do arquivo, mapeie o `volumes` opção `custom-id`. Por exemplo: 

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

### <a name="preview-limitations"></a>Limitações de visualização

Vários contêineres está atualmente em visualização. Não há suporte para os seguintes recursos de plataforma do serviço de aplicativo:

- Autenticação/Autorização
- Identidades gerenciadas

### <a name="docker-compose-options"></a>Opções de composição do docker

As listas a seguir mostram compatíveis e sem suportadas opções de configuração Docker Compose:

#### <a name="supported-options"></a>Opções com suporte

- command
- entrypoint
- environment
- image
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>Opções sem suporte

- build (não permitido)
- depends_on (ignorado)
- networks (ignorada)
- segredos (ignorados)
- portas diferentes de 80 e 8080 (ignoradas)

> [!NOTE]
> As outras opções não explicitamente chamadas são ignoradas em visualização pública.

### <a name="kubernetes-configuration-options"></a>Opções de configuração do Kubernetes

As opções de configuração a seguir têm suporte para Kubernetes:

- args
- command
- containers
- image
- name
- ports
- spec

> [!NOTE]
> Não há suporte para quaisquer outras opções não explicitamente indicadas na visualização pública.
>

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Implantação por meio de um repositório de contêiner particular](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo WordPress cm vários contêineres](tutorial-multi-container-app.md)
