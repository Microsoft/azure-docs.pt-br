---
title: Configurar a instância do Config Server no Azure Spring Cloud
description: Saiba como configurar uma instância do Config Server do Spring Cloud para seu Azure Spring Cloud no portal do Azure
ms.service: spring-cloud
ms.topic: how-to
ms.author: brendm
author: bmitchell287
ms.date: 10/18/2019
ms.custom: devx-track-java
ms.openlocfilehash: de113e3c005e11bd2bcd13ec6c1554664ba8fbaf
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877714"
---
# <a name="set-up-a-spring-cloud-config-server-instance-for-your-service"></a>Configurar uma instância do Servidor de Configuração do Spring Cloud para seu serviço

**Este artigo aplica-se a:** ✔️ Java ✔️ C#

Este artigo mostra como conectar uma instância do Servidor de Configuração do Spring Cloud ao seu serviço do Azure Spring Cloud.

A configuração do Spring Cloud oferece suporte no lado do servidor e do cliente para configuração externalizada em um sistema distribuído. Com a instância do Servidor de Configuração, você tem um local central para gerenciar propriedades externas para aplicativos em todos os ambientes. Para obter mais informações, confira a [Referência do Servidor de Configuração do Spring Cloud](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Pré-requisitos
* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 
* Um serviço do Azure Spring Cloud já provisionado e em execução. Para configurar e iniciar um serviço do Azure Spring Cloud, confira [Início Rápido: iniciar um aplicativo Java Spring usando a CLI do Azure](spring-cloud-quickstart.md).

## <a name="restriction"></a>Restrição

Há algumas restrições quando você usa o Servidor de Configuração com um back-end do Git. Algumas propriedades são automaticamente injetadas em seu ambiente de aplicativo para acessar o Servidor de Configuração e a Descoberta de Serviço. Se você também configurar essas propriedades em seus arquivos do Servidor de Configuração, poderá enfrentar conflitos e um comportamento inesperado. Essas propriedades incluem: 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
spring.jmx.enabled
```

> [!CAUTION]
> É altamente recomendável _não_ colocar as propriedades acima em seus arquivos de aplicativo do Servidor de Configuração.

## <a name="create-your-config-server-files"></a>Criar seus arquivos do Servidor de Configuração

O Azure Spring Cloud dá suporte ao Azure DevOps, GitHub, GitLab e Bitbucket para armazenar seus arquivos do Servidor de Configuração. Quando seu repositório estiver pronto, crie os arquivos de configuração com as instruções a seguir e armazene-os lá.

Além disso, algumas propriedades configuráveis estão disponíveis apenas para determinados tipos. As subseções a seguir listam as propriedades de cada tipo de repositório.

### <a name="public-repository"></a>Repositório público

Ao usar um repositório público, suas propriedades configuráveis serão mais limitadas.

Todas as propriedades configuráveis usadas para configurar o repositório público Git estão listadas na tabela a seguir:

> [!NOTE]
> Usar um hífen ("-") para separar palavras é a única convenção de nomenclatura compatível no momento. Por exemplo, você pode usar *default-label*, mas não *defaultLabel*.

| Propriedade        | Obrigatório | Recurso                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Sim    | O URI do repositório Git que é usado como o back-end do Servidor de Configuração começa com *http://* , *https://* , *git@* ou *ssh://* . |
| `default-label` | Não     | O rótulo padrão do repositório Git deve ser o *nome do branch*, o *nome da tag* ou a *ID de commit* do repositório. |
| `search-paths`  | Não     | Uma matriz de cadeias de caracteres usada para pesquisar subdiretórios do repositório Git. |

------

### <a name="private-repository-with-ssh-authentication"></a>Repositório privado com autenticação SSH

Todas as propriedades configuráveis usadas para configurar o repositório Git privado com SSH estão listadas na tabela a seguir:

> [!NOTE]
> Usar um hífen ("-") para separar palavras é a única convenção de nomenclatura compatível no momento. Por exemplo, você pode usar *default-label*, mas não *defaultLabel*.

| Propriedade                   | Obrigatório | Recurso                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | Sim    | O URI do repositório Git usado como back-end do Servidor de Configuração. Deve começar com *http://* , *https://* , *git@* ou *ssh://* . |
| `default-label`            | Não     | O rótulo padrão do repositório Git deve ser o *nome do branch*, o *nome da tag* ou a *ID de commit* do repositório. |
| `search-paths`             | Não     | Uma matriz de cadeias de caracteres usada para pesquisar subdiretórios do repositório Git. |
| `private-key`              | Não     | A chave privada SSH para acessar o repositório Git, _necessária_ quando o URI começa com *git@* ou *ssh://* . |
| `host-key`                 | Não     | A chave de host do servidor do repositório Git. Não deve incluir o prefixo de algoritmo, conforme abordado por `host-key-algorithm`. |
| `host-key-algorithm`       | Não     | O algoritmo da chave de host, que deve ser *ssh-dss*, *ssh-rsa*, *ecdsa-sha2-nistp256*, *ecdsa-sha2-nistp384* ou *ecdsa-sha2-nistp521*. *Necessário* somente se `host-key` existir. |
| `strict-host-key-checking` | Não     | Indica se a inicialização da instância do Servidor de Configuração falhará ao fazer uso do `host-key` privado. Deve ser *true* (valor padrão) ou *false*. |

> [!NOTE]
> O servidor de configuração usa `master` (OM git em si) como rótulo padrão, se não for especificado. Mas o GitHub alterou a ramificação padrão de `master` para `main` recentemente. Para evitar a falha do servidor de configuração do Azure Spring Cloud, preste atenção ao rótulo padrão ao configurar o servidor de configuração com o GitHub, especialmente para novos repositórios criados.

-----

### <a name="private-repository-with-basic-authentication"></a>Repositório privado com autenticação Básica

Todas as propriedades configuráveis usadas para configurar o repositório Git privado com autenticação Básica estão listadas abaixo.

> [!NOTE]
> Usar um hífen ("-") para separar palavras é a única convenção de nomenclatura compatível no momento. Por exemplo, use *default-label*, não *defaultLabel*.

| Propriedade        | Obrigatório | Recurso                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Sim    | O URI do repositório Git que é usado como o back-end do Servidor de Configuração deve ser iniciado com *http://* , *https://* , *git@* ou *ssh://* . |
| `default-label` | Não     | O rótulo padrão do repositório Git deve ser o *nome do branch*, o *nome da tag* ou a *ID de commit* do repositório. |
| `search-paths`  | Não     | Uma matriz de cadeias de caracteres usada para pesquisar subdiretórios do repositório Git. |
| `username`      | Não     | O nome de usuário usado para acessar o servidor do repositório Git, _necessário_ quando o servidor do repositório Git é compatível com `Http Basic Authentication`. |
| `password`      | Não     | A senha usada para acessar o servidor do repositório Git, _necessária_ quando o servidor do repositório Git é compatível com `Http Basic Authentication`. |

> [!NOTE]
> Muitos servidores de repositório `Git` são compatíveis com o uso de tokens em vez de senhas para Autenticação Básica HTTP. Alguns repositórios, como o GitHub, permitem que os tokens persistam indefinidamente. No entanto, alguns servidores de repositório Git, incluindo o Azure DevOps, forçam tokens a expirar em algumas horas. Os repositórios que fazem com que os tokens expirem não devem usar a autenticação baseada em token com o Azure Spring Cloud.

### <a name="git-repositories-with-pattern"></a>Repositórios Git com padrão

Todas as propriedades configuráveis usadas para configurar os repositórios Git com padrão estão listadas abaixo.

> [!NOTE]
> Usar um hífen ("-") para separar palavras é a única convenção de nomenclatura compatível no momento. Por exemplo, use *default-label*, não *defaultLabel*.

| Propriedade                           | Obrigatório         | Recurso                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | Não             | Um mapa que consiste nas configurações de um repositório Git com um determinado nome. |
| `repos."uri"`                      | Sim em `repos` | O URI do repositório Git que é usado como o back-end do Servidor de Configuração deve ser iniciado com *http://* , *https://* , *git@* ou *ssh://* . |
| `repos."name"`                     | Sim em `repos` | Um nome para identificação de um repositório Git, _necessário_ somente se `repos` existir. Por exemplo, *team-A*, *team-B*. |
| `repos."pattern"`                  | Não             | Uma matriz de cadeias de caracteres usada para corresponder a um nome de aplicativo. Para cada padrão, use o formato `{application}/{profile}` com caracteres curinga. |
| `repos."default-label"`            | Não             | O rótulo padrão do repositório Git deve ser o *nome do branch*, o *nome da tag* ou a *ID de commit* do repositório. |
| `repos."search-paths`"             | Não             | Uma matriz de cadeias de caracteres usada para pesquisar subdiretórios do repositório Git. |
| `repos."username"`                 | Não             | O nome de usuário usado para acessar o servidor do repositório Git, _necessário_ quando o servidor do repositório Git é compatível com `Http Basic Authentication`. |
| `repos."password"`                 | Não             | A senha usada para acessar o servidor do repositório Git, _necessária_ quando o servidor do repositório Git é compatível com `Http Basic Authentication`. |
| `repos."private-key"`              | Não             | A chave privada SSH para acessar o repositório Git, _necessária_ quando o URI começa com *git@* ou *ssh://* . |
| `repos."host-key"`                 | Não             | A chave de host do servidor do repositório Git. Não deve incluir o prefixo de algoritmo, conforme abordado por `host-key-algorithm`. |
| `repos."host-key-algorithm"`       | Não             | O algoritmo da chave de host, que deve ser *ssh-dss*, *ssh-rsa*, *ecdsa-sha2-nistp256*, *ecdsa-sha2-nistp384* ou *ecdsa-sha2-nistp521*. *Necessário* somente se `host-key` existir. |
| `repos."strict-host-key-checking"` | Não             | Indica se a inicialização da instância do Servidor de Configuração falhará ao fazer uso do `host-key` privado. Deve ser *true* (valor padrão) ou *false*. |

## <a name="attach-your-config-server-repository-to-azure-spring-cloud"></a>Anexar o repositório do Servidor de Configuração ao Azure Spring Cloud

Agora que você salvou seus arquivos de configuração em um repositório, é necessário conectar o Azure Spring Cloud a ele.

1. Entre no [portal do Azure](https://portal.azure.com).

2. Vá até a página **Visão geral** do Azure Spring Cloud.

3. Selecione **servidor de configuração** no painel de navegação esquerdo.

4. Na seção **Repositório padrão**, defina **URI** como "https://github.com/Azure-Samples/piggymetrics-config".

5. Clique em **Validar**.

    ![Navegar até o servidor de configuração](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

6. Quando a validação for concluída, clique em **Aplicar** para salvar as alterações.

    ![Validar o servidor de configuração](media/spring-cloud-quickstart-launch-app-portal/validate-complete.png)

7. A atualização da configuração pode levar alguns minutos.
 
    ![Atualizar o servidor de configuração](media/spring-cloud-quickstart-launch-app-portal/updating-config.png) 

8. Você deverá receber uma notificação quando a configuração for concluída.

### <a name="enter-repository-information-directly-to-the-azure-portal"></a>Insira as informações do repositório diretamente para o portal do Azure

#### <a name="default-repository"></a>Repositório padrão

* **Repositório público**: Na seção **Repositório padrão**, cole o URI do repositório na seção **URI**.  Defina o **Rótulo** como **config**. Verifique se a configuração **Autenticação** está marcada como **Pública** e, em seguida, selecione **Aplicar** para concluir. 

* **Repositório privado**: O Azure Spring Cloud é compatível com SSH e autenticação básica baseada em token/senha.

    * **Autenticação Básica**: Na seção **Repositório padrão**, na caixa **URI**, cole o URI do repositório e, em seguida, selecione o botão **Autenticação** (ícone de lápis). No painel **Editar Autenticação**, na lista suspensa **Tipo de autenticação**, selecione **HTTP básico** e, em seguida, insira seu nome de usuário e senha/token para permitir acesso ao Azure Spring Cloud. Selecione **OK** e **Aplicar** para concluir a configuração de sua instância do Servidor de Configuração.

    ![A autenticação básica do painel Editar Autenticação](media/spring-cloud-tutorial-config-server/basic-auth.png)
    
    > [!CAUTION]
    > Alguns servidores de repositório Git, como o GitHub, usam um *token pessoal* ou um *token de acesso*, como uma senha, para **Autenticação Básica**. Você pode usar esse tipo de token como uma senha no Azure Spring Cloud, pois ele nunca expirará. Mas para outros servidores de repositório Git, como o BitBucket e o Azure DevOps, o *token de acesso* expirará em uma ou duas horas. Isso significa que essa opção não é viável ao usar esses servidores de repositório com o Azure Spring Cloud.

    * **SSH**: Na seção **Repositório padrão**, na caixa **URI**, cole o URI do repositório e, em seguida, selecione o botão **Autenticação** (ícone de lápis). No painel **Editar Autenticação**, na lista suspensa **Tipo de autenticação**, selecione **SSH** e, em seguida, insira sua **Chave privada**. Opcionalmente, você pode especificar sua **Chave de host** e seu **Algoritmo de chave de host**. Não deixe de incluir sua chave pública no repositório do Servidor de Configuração. Selecione **OK** e **Aplicar** para concluir a configuração de sua instância do Servidor de Configuração.

    ![O painel Editar Autenticação do ssh auth](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>Repositório de padrões

Se você quiser usar um **Repositório de padrões** opcional para configurar seu serviço, especifique o **URI** e a **Autenticação** da mesma maneira que o **Repositório padrão**. Não deixe de incluir um **Nome** para seu padrão e, em seguida, selecione **Aplicar** para anexá-lo à sua instância. 

### <a name="enter-repository-information-into-a-yaml-file"></a>Inserir informações do repositório em um arquivo YAML

Se você tiver escrito um arquivo YAML com as configurações do repositório, poderá importar o arquivo diretamente do computador local para o Azure Spring Cloud. Um arquivo YAML simples para um repositório privado com autenticação Básica ficaria assim:

```yaml
spring:
    cloud:
        config:
            server:
                git:
                    uri: https://github.com/azure-spring-cloud-samples/config-server-repository.git
                    username: <username>
                    password: <password/token>

```

Selecione o botão **Importar configurações** e, em seguida, selecione o arquivo YAML do diretório do projeto. Selecione **Importar** e, em seguida, uma operação `async` de suas **Notificações** será exibida. Após um a dois minutos, ela deve relatar êxito.

![O painel Notificações do Servidor de Configuração](media/spring-cloud-tutorial-config-server/local-yml-success.png)


As informações do arquivo YAML devem ser exibidas no portal do Azure. Selecione **Aplicar** para concluir. 

## <a name="using-azure-repos-for-azure-spring-cloud-configuration"></a>Usando Azure Repos para a configuração do Azure Spring Cloud

O Azure Spring Cloud poderá acessar repositórios Git públicos, protegidos por SSH ou com uma autenticação Básica HTTP. Usaremos essa última opção, pois é mais fácil criar e gerenciar com Azure Repos.

### <a name="get-repo-url-and-credentials"></a>Obter URL e credenciais do repositório
1. No portal de Azure Repos para seu projeto, clique no botão "clonar":

    ![Botão clonar](media/spring-cloud-tutorial-config-server/clone-button.png)

1. Copie a URL de clone da caixa de texto. Essa URL normalmente estará no formato:

    ```Text
    https://<organization name>@dev.azure.com/<organization name>/<project name>/_git/<repository name>
    ```

    Remova tudo após `https://` e antes `dev.azure.com` , incluindo o `@` . A URL resultante deve estar no formato:

    ```Text
    https://dev.azure.com/<organization name>/<project name>/_git/<repository name>
    ```

    Salve esta URL para uso na próxima seção.

1. Clique em "gerar credenciais do git". Um nome de usuário e uma senha serão exibidos. Salve-os para uso na próxima seção.


### <a name="configure-azure-spring-cloud-to-access-the-git-repository"></a>Configurar o Azure Spring Cloud para acessar o repositório Git

1. Entre no [portal do Azure](https://portal.azure.com).

1. Vá até a página **Visão geral** do Azure Spring Cloud.

1. Escolha o serviço a ser configurado.

1. No painel esquerdo da página serviço, em **configurações**, selecione a guia **servidor de configuração** . Configure o repositório que criamos anteriormente:
   - Adicione a URL do repositório que você salvou da seção anterior
   - Clique em ativar `Authentication` e selecione `HTTP Basic`
   - O __nome de usuário__ é o nome de usuário salvo na seção anterior
   - A __senha__ é a senha salva na seção anterior
   - Clique em "aplicar" e aguarde a operação ter sucesso

   ![Servidor do Spring Cloud Config](media/spring-cloud-tutorial-config-server/config-server-azure-repos.png)

## <a name="delete-your-app-configuration"></a>Excluir a configuração de aplicativos

Após salvar um arquivo de configuração, o botão **Excluir configuração de aplicativos** será exibido na guia **Configuração**. Selecionar esse botão apagará suas configurações existentes completamente. Você deverá selecioná-lo se desejar conectar sua instância do Servidor de Configuração a outra fonte, como migrar do GitHub para o Azure DevOps.



## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como habilitar e configurar sua instância do Config Server do Spring Cloud. Para saber mais como gerenciar seu aplicativo, confira [Dimensionar um aplicativo no Azure Spring Cloud](spring-cloud-howto-scale-manual.md).
