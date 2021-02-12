---
title: Perguntas frequentes de execução de contêineres internos
description: Encontre respostas para as perguntas frequentes sobre os contêineres internos do Linux no serviço Azure App.
keywords: serviço de aplicativo do Azure, aplicativo Web, perguntas frequentes, Linux, OSS, aplicativo Web para contêineres, vários contêineres
author: msangapu-msft
ms.topic: article
ms.date: 10/30/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: e2ab39f090124aaf590798795f446bda5b0fe48a
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008547"
---
# <a name="azure-app-service-on-linux-faq"></a>Perguntas frequentes sobre o Serviço de Aplicativo do Azure no Linux

Com o lançamento do Serviço de Aplicativo no Linux, estamos trabalhando para adicionar recursos e fazer melhorias em nossa plataforma. Este artigo fornece as respostas para as perguntas que nossos clientes têm feito recentemente.

Se você tiver qualquer dúvida, comente este artigo.

## <a name="built-in-images"></a>Imagens internas

**Quero bifurcar os contêineres internos do Docker que a plataforma fornece. Onde posso encontrar esses arquivos?**

É possível encontrar todos os arquivos do Docker no [GitHub](https://github.com/azure-app-service). É possível encontrar todos os contêineres do Docker no [Hub do Docker](https://hub.docker.com/u/appsvc/).

<a id="#startup-file"></a>

**Quais são os valores esperados para a seção Arquivo de Inicialização quando configuro a pilha de runtime?**

| Pilha           | Valor Esperado                                                                         |
|-----------------|----------------------------------------------------------------------------------------|
| Java SE         | o comando para iniciar seu aplicativo JAR (por exemplo, `java -jar /home/site/wwwroot/app.jar --server.port=80` ) |
| Tomcat          | o local de um script para executar as configurações necessárias (por exemplo, `/home/site/deployments/tools/startup_script.sh` )          |
| Node.js         | o arquivo de configuração PM2 ou o arquivo de script                                |
| .NET Core       | o nome da DLL compilada como `dotnet <myapp>.dll`                                 |
| Ruby            | o script Ruby com o qual você deseja inicializar seu aplicativo                     |

Esses comandos ou scripts são executados depois que o contêiner interno do Docker é iniciado, mas antes do código do aplicativo ser iniciado.

## <a name="management"></a>Gerenciamento

**O que acontece quando eu pressiono o botão de reinicialização no portal do Azure?**

Esta ação é igual a um reinício do Docker.

**Posso usar o SSH (Secure Shell) para me conectar à VM (máquina virtual) do contêiner de aplicativo?**

Sim, você pode fazer isso por meio do site de gerenciamento do controle de origem (SCM) .

> [!NOTE]
> Você também pode se conectar ao contêiner de aplicativo diretamente do seu computador de desenvolvimento local usando SSH, SFTP ou Visual Studio Code (para aplicativos do Node.js de depuração ao vivo). Para obter mais informações, consulte [Depuração remota e SSH no Serviço de Aplicativo no Linux](https://azure.github.io/AppService/2018/05/07/New-SSH-Experience-and-Remote-Debugging-for-Linux-Web-Apps.html).
>

**Como criar um plano de Serviço de Aplicativo Linux por meio de um SDK ou um modelo do Azure Resource Manager?**

Defina o campo **reservado** do serviço de aplicativo como *verdadeiro*.

## <a name="continuous-integration-and-deployment"></a>Integração contínua e implantação

**Meu aplicativo Web ainda usa uma imagem de contêiner do Docker antiga depois de atualizar a imagem no Hub do Docker. Você dá suporte à integração e à implantação contínuas de contêineres personalizados?**

Sim, para configurar integração/implantação contínua para o Registro de Contêiner do Azure ou DockerHub, seguindo a [Implantação contínua com o Aplicativo Web para Contêineres](./deploy-ci-cd-custom-container.md). Para registros privados, é possível atualizar o contêiner parando e, em seguida, iniciando o Aplicativo Web. Se preferir, é possível alterar ou adicionar uma configuração de aplicativo fictício para forçar uma atualização do contêiner.

**Há suporte para ambientes de preparo?**

Sim.

**Posso usar *WebDeploy/MSDeploy* para implantar meu aplicativo Web?**

Sim, você precisa definir uma configuração de aplicativo chamada `WEBSITE_WEBDEPLOY_USE_SCM` como *false*.

**A implantação do git do meu aplicativo falha ao usar o aplicativo Web do Linux. Como posso contornar o problema?**

Se a implantação do Git falhar no aplicativo Web do Linux, escolha uma das opções a seguir para implantar o código do aplicativo:

- Usar o recurso entrega contínua (versão prévia): você pode armazenar o código-fonte do aplicativo em um repositório Git do Azure DevOps ou repositório GitHub para usar a entrega contínua do Azure. Para obter mais informações, consulte [Como configurar a Entrega Contínua para aplicativos Web do Linux](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/).

- Usar a [API de implantação via arquivo ZIP](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file): para usar essa API, [adicione o SSH ao seu aplicativo Web](configure-linux-open-ssh-session.md) e vá para a pasta onde você deseja implantar seu código. Execute o código a seguir:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Se você receber uma mensagem de erro informando que o comando `curl` não foi encontrado, instale a ondulação usando `apt-get install curl` antes de executar o comando `curl` anterior.

## <a name="language-support"></a>Suporte ao idioma

**Eu quero usar websockets no aplicativo Node.js, há definições ou configurações especiais a serem definidas?**

Sim, desabilite `perMessageDeflate` no código Node.js do servidor. Por exemplo, se estiver usando o socket.io, use o código a seguir:

```nodejs
const io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Há suporte para aplicativos .NET Core não compilados?**

Sim.

**Há suporte para o Criador como um gerenciador de dependências para aplicativos PHP?**

Sim, durante uma implantação do Git, o Kudu deve detectar que você está implantando um aplicativo PHP (graças à presença de um arquivo composer.lock) e o Kudu, então, disparará uma instalação de criador.

## <a name="custom-containers"></a>Contêineres personalizados

**Estou usando meu próprio contêiner personalizado. Quero que a plataforma monte um compartilhamento SMB no `/home/` diretório.**

Se `WEBSITES_ENABLE_APP_SERVICE_STORAGE` a configuração não for **especificada** ou definida como *true*, o `/home/` diretório **será compartilhado** entre as instâncias de escala e os arquivos gravados **persistirão** entre as reinicializações. Definir explicitamente `WEBSITES_ENABLE_APP_SERVICE_STORAGE` como *false* desabilitará a montagem.

**Meu contêiner personalizado demora para iniciar e a plataforma o reinicia antes que ele termine a inicialização.**

Você pode configurar o tempo que a plataforma aguardará antes de reiniciar o contêiner. Para fazer isso, defina a configuração de aplicativo `WEBSITES_CONTAINER_START_TIME_LIMIT` como o valor desejado. O valor padrão é 230 segundos e o valor máximo permitido é 1800 segundos.

**Qual é o formato da URL do servidor do Registro privado?**

Forneça a URL completa do registro, incluindo `http://` ou `https://`.

**Qual é o formato do nome da imagem na opção de Registro privado?**

Adicione o nome de imagem completa, incluindo a URL de registro particular (por exemplo, myacr.azurecr.io/dotnet:latest). Os nomes de imagem que usam a porta personalizada [não podem ser inseridos por meio do portal](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Para definir `docker-custom-image-name` , use a [ `az` ferramenta de linha de comando](/cli/azure/webapp/config/container#az-webapp-config-container-set).

**Posso expor mais de uma porta em minha imagem de contêiner personalizados?**

Não há suporte para expor mais de uma porta.

**Posso colocar meu próprio armazenamento?**

Sim, [Traga seu próprio armazenamento](./configure-connect-to-azure-storage.md) está em versão prévia.

**Por que não é possível navegar os processos do meu contêiner personalizado em execução ou de sistema de arquivos do site do SCM?**

O site do SCM é executado em um contêiner separado. Não é possível verificar o sistema de arquivos ou os processos em execução do contêiner de aplicativo.

**Meu contêiner personalizado escuta uma porta diferente da porta 80. Como posso configurar meu aplicativo para rotear solicitações para essa porta?**

Temos a detecção automática de porta. Também é possível especificar uma configuração de aplicativo chamada *WEBSITES_PORT* e fornecer a ela o valor do número da porta esperada. Anteriormente, a plataforma usava a configuração de aplicativo *PORTA*. Nós estamos planejando substituir essa configuração de aplicativo e usar *WEBSITES_PORT* exclusivamente.

**É necessário implementar o HTTPS no meu contêiner personalizado?**

Não, a plataforma manipula a terminação HTTPS nos front-ends compartilhados.

## <a name="multi-container-with-docker-compose"></a>Vários contêineres com Docker Compose

**Como fazer para configurar o ACR (Registro de Contêiner do Azure) para usá-lo com vários contêineres?**

Para usar o ACR com vários contêineres, **todas as imagens de contêiner** precisam estar hospedadas no mesmo servidor de registro do ACR. Quando estiverem no mesmo servidor do registro, você precisará criar configurações do aplicativo e, em seguida, atualizar o arquivo de configuração Docker Compose para incluir o nome da imagem ACR.

Crie as seguintes configurações de aplicativo:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (URL completa, ex: `https://<server-name>.azurecr.io` )
- DOCKER_REGISTRY_SERVER_PASSWORD (habilite o acesso de administrador nas configurações do ACR)

No arquivo de configuração, referencie a imagem do ACR como o seguinte exemplo:

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**Como saber qual contêiner é acessível pela Internet?**

- Apenas um contêiner pode ser aberto para acesso
- Somente as portas 80 e 8080 são acessíveis (portas expostas)

Estas são as regras para determinar qual contêiner está acessível – na ordem de precedência:

- Configuração de aplicativo `WEBSITES_WEB_CONTAINER_NAME` definida como o nome do contêiner
- O primeiro contêiner a definir a porta 80 ou 8080
- Se nenhuma das opções acima for verdadeira, o primeiro contêiner definido no arquivo estará acessível (exposto)


## <a name="web-sockets"></a>Soquetes Web

Os Web Sockets têm suporte em aplicativos do Linux.

> [!IMPORTANT]
> Atualmente, os Web Sockets não têm suporte para aplicativos do Linux em planos de Serviço de Aplicativo Gratuito. Estamos trabalhando para remover essa limitação e planejar o suporte de até 5 conexões de soquete da Web em planos de Serviço de Aplicativo Gratuito.

## <a name="pricing-and-sla"></a>Preço e SLA

**Qual é o preço agora que o serviço está disponível?**

Os preços variam de acordo com a SKU e a região, mas você pode ver mais detalhes em nossa página de preços: [preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/linux/).

## <a name="other-questions"></a>Outras perguntas

**O que significa "o recurso solicitado não está disponível no grupo de recursos"?**

Você poderá ver esta mensagem ao criar o aplicativo Web usando o Azure Resource Manager (ARM). Com base em uma limitação atual, para o mesmo grupo de recursos, você não pode misturar aplicativos Windows e Linux na mesma região.

**Quais são os caracteres com suporte em nomes de configurações do aplicativo?**

Você pode usar apenas letras (A-Z, a-z), números (0-9) e o caractere de sublinhado (_) para as configurações do aplicativo.

**Onde posso solicitar os novos recursos?**

É possível enviar sua ideia para o [fórum de comentários dos Aplicativos Web](https://aka.ms/webapps-uservoice). Adicione “[Linux]” ao título de sua ideia.

## <a name="next-steps"></a>Próximas etapas

- [O que é o Serviço de Aplicativo do Azure no Linux?](overview.md#app-service-on-linux)
- [Configurar ambientes de preparo no Serviço de Aplicativo do Azure](deploy-staging-slots.md)
- [Implantação contínua com Aplicativo Web para Contêineres](./deploy-ci-cd-custom-container.md)
