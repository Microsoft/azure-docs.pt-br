---
title: "Suporte de SSH para o Aplicativo Web do Serviço de Aplicativo do Azure para Contêineres | Microsoft Docs"
description: "Saiba como usar SSH com o Web para Contêineres do Azure."
keywords: "serviço de aplicativo do azure, aplicativo web, linux, oss"
services: app-service
documentationcenter: 
author: wesmc7777
manager: erikre
editor: 
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: wesmc
ms.openlocfilehash: 7ce9b23e8925d4c79827c7c4e8bec63067ce33e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="ssh-support-for-azure-web-app-for-containers"></a>Suporte de SSH para o Aplicativo Web para Contêineres do Azure

O [SSH (Secure Shell)](https://en.wikipedia.org/wiki/Secure_Shell) é um protocolo de rede criptográfico para usar serviços de rede de forma segura. Ele é usado com maior frequência para fazer logon em um sistema remotamente com segurança em uma linha de comando, bem como para executar comandos administrativos remotamente.

O Aplicativo Web para Contêineres oferece suporte de SSH no contêiner de aplicativo com cada uma das imagens internas do Docker usadas para a Pilha em Tempo de Execução de novos aplicativos Web. 

![Pilhas de tempo de execução](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

Você também pode usar SSH com suas imagens personalizadas do Docker incluindo o servidor SSH como parte da imagem e configurando-o conforme descrito neste tópico.

## <a name="making-a-client-connection"></a>Estabelecendo uma conexão de cliente

Para estabelecer uma conexão de cliente SSH, o site principal deve ser iniciado.

Cole o ponto de extremidade de SCM (gerenciamento de controle do código-fonte) de seu aplicativo Web no navegador usando o seguinte formato:

```bash
https://<your sitename>.scm.azurewebsites.net/webssh/host
```

Se ainda não estiver autenticado, você precisará se autenticar usando sua assinatura do Azure para se conectar.

![Conexão SSH](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)


## <a name="ssh-support-with-custom-docker-images"></a>Suporte de SSH com imagens personalizadas do Docker

Para que uma imagem personalizada do Docker dê suporte à comunicação SSH entre o contêiner e o cliente no portal do Azure, execute as seguintes etapas para a imagem do Docker.

Essas etapas são mostradas no repositório do Serviço de Aplicativo do Azure como exemplo [aqui](https://github.com/Azure-App-Service/node/blob/master/6.9.3/).

1. Inclua a instalação `openssh-server` na [instrução `RUN`](https://docs.docker.com/engine/reference/builder/#run) no Dockerfile para sua imagem e defina a senha da conta raiz como `"Docker!"`. 

    > [!NOTE]
    > Essa configuração não permite conexões externas com o contêiner. O SSH só pode ser acessado por meio do site do Kudu/SCM, que é autenticado usando as credenciais de publicação.
    
    ```docker
    # ------------------------
    # SSH Server support
    # ------------------------
    RUN apt-get update \
        && apt-get install -y --no-install-recommends openssh-server \
        && echo "root:Docker!" | chpasswd
    ```

1. Adicione uma [instrução `COPY`](https://docs.docker.com/engine/reference/builder/#copy) ao Dockerfile para copiar um arquivo [sshd_config](http://man.openbsd.org/sshd_config) para o diretório */etc/ssh/*. Seu arquivo de configuração deve ser baseado em nosso arquivo sshd_config no repositório Azure-App-Service do GitHub [aqui](https://github.com/Azure-App-Service/node/blob/master/6.11.0/sshd_config).

    > [!NOTE]
    > O arquivo *sshd_config* deve incluir o seguinte ou a conexão falhará: 
    > * `Ciphers` deve incluir pelo menos um dos itens a seguir: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs` deve incluir pelo menos um dos itens a seguir: `hmac-sha1,hmac-sha1-96`.
    
    ```docker
    COPY sshd_config /etc/ssh/
    ```

1. Inclua a porta 2222 na [instrução `EXPOSE`](https://docs.docker.com/engine/reference/builder/#expose) para o Dockerfile. Embora a senha raiz seja conhecida, a porta 2222 não pode ser acessada da Internet. Ela é uma porta interna que pode ser acessada somente por contêineres dentro da rede ponte de uma rede virtual privada.

    ```docker
    EXPOSE 2222 80
    ```

1. Certifique-se de iniciar o serviço SSH. Este exemplo [aqui](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh) usa um script shell no diretório */bin*.

    ```bash
    #!/bin/bash
    service ssh start
    ```

O Dockerfile usa a [instrução `CMD`](https://docs.docker.com/engine/reference/builder/#cmd) para executar o script.

    ```docker
    COPY init_container.sh /bin/
    ...
    RUN chmod 755 /bin/init_container.sh
    ...
    CMD ["/bin/init_container.sh"]
    ```

## <a name="next-steps"></a>Próximas etapas

Consulte os links a seguir para obter mais informações sobre o Aplicativo Web para Contêineres. Você pode postar perguntas e problemas no [nosso fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Como usar uma imagem personalizada do Docker para o Aplicativo Web para Contêineres do Azure](quickstart-custom-docker-image.md)
* [Usando o .NET Core no aplicativo Web para Contêineres do Azure](quickstart-dotnetcore.md)
* [Usando o Ruby no aplicativo Web para Contêineres do Azure](quickstart-ruby.md)
* [Perguntas frequentes sobre o Aplicativo Web para Contêineres do Serviço de Aplicativo do Azure](app-service-linux-faq.md)