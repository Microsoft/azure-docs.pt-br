---
title: Gerenciar e monitorar o aplicativo com o Azure Spring Boot Actuator
description: Saiba como gerenciar e monitorar o aplicativo com o acionador do Spring boot.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 93fd286aa76a0409a515abbf8c9dabd88a9a65c4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877725"
---
# <a name="manage-and-monitor-app-with-azure-spring-boot-actuator"></a>Gerenciar e monitorar o aplicativo com o Azure Spring Boot Actuator

**Este artigo aplica-se a:** ✔️ Java

Depois de implantar um novo binário em seu aplicativo, talvez você queira verificar a funcionalidade e ver informações sobre o aplicativo em execução. Este artigo explica como acessar a API de um ponto de extremidade de teste fornecido pelo Azure Spring Cloud e expor os recursos prontos para produção para seu aplicativo.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você tenha um aplicativo Spring boot 2. x que pode ser implantado com êxito e inicializado no serviço de nuvem Spring do Azure.  Consulte [início rápido: iniciar um aplicativo de nuvem Spring do Azure existente usando o portal do Azure](spring-cloud-quickstart.md)

## <a name="verify-app-through-test-endpoint"></a>Verificar aplicativo por meio de ponto de extremidade de teste
1. Vá para o **painel do aplicativo** e clique em seu aplicativo para entrar na página Visão geral do aplicativo.

1. No painel **visão geral** , você deve ver **ponto de extremidade de teste**.  Acesse esse ponto de extremidade da linha de comando ou navegador e observe a resposta da API.

1. Observe o URI do **ponto de extremidade de teste** que será usado na próxima seção.

>[!TIP]
> * Se o aplicativo retornar uma página de front-end e fizer referência a outros arquivos por meio do caminho relativo, confirme se o ponto de extremidade de teste termina com uma barra (/). Isso garantirá que o arquivo CSS seja carregado corretamente.
> * Se você exibir sua API de um Brower e seu navegador exigir que você insira as credenciais de logon para exibir a página, use a [decodificação de URL](https://www.urldecoder.org/) para decodificar o ponto de extremidade de teste. A decodificação de URL retorna uma URL no formato "https:// \<username> : \<password> @ \<cluster-name> . Test.azureapps.Io/ \<app-name> / \<deployment-name> ".  Use este formulário para acessar seu ponto de extremidade.

## <a name="add-actuator-dependency"></a>Adicionar dependência do atuador

Para adicionar o atuador a um projeto baseado em Maven, adicione a dependência "iniciador":

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependencies>
```

Compile o novo binário e implante-o em seu aplicativo.

## <a name="enable-production-ready-features"></a>Habilitar recursos prontos para produção
Os pontos de extremidade do atuador permitem que você monitore e interaja com seu aplicativo. Por padrão, o aplicativo Spring boot expõe `health` e `info` pontos de extremidade para mostrar informações arbitrárias sobre o aplicativo e a integridade.

Para observar a configuração e o ambiente configurável, precisamos habilitar `env` e `configgrops` pontos de extremidade também.

1. Vá para o painel **visão geral** do aplicativo, clique em **configuração** no menu configuração, vá para a página configuração de **variáveis de ambiente** .
1. Adicione as seguintes propriedades como no formulário "chave: valor". Esse ambiente abrirá o ponto de extremidade do acionador de mola "env", "Health", "info".

   ```
   management.endpoints.web.exposure.include: env,health,info
   ```
1. Clique no botão **salvar** , seu aplicativo será reiniciado automaticamente e carregará as novas variáveis de ambiente.

Agora você pode voltar para o painel Visão geral do aplicativo e aguardar até que o status de provisionamento mude para "êxito".  Haverá mais de uma instância em execução.

> [!Note] 
> Depois de expor o aplicativo para público, esses pontos de extremidade do atuador também são expostos ao público. Você pode ocultar todos os pontos de extremidade excluindo as variáveis de ambiente `management.endpoints.web.exposure.include` e definir `management.endpoints.web.exposure.exclude=*`

## <a name="view-the-actuator-endpoint-to-view-application-information"></a>Exibir o ponto de extremidade do atuador para exibir informações do aplicativo
1. Agora você pode acessar a URL `"<test-endpoint>/actuator/"` para ver todos os pontos de extremidade expostos pelo acionador do Spring boot.
1. URL `"<test-endpoint>/actuator/env"` de acesso, você pode ver os perfis ativos usados pelo aplicativo e todas as variáveis de ambiente carregadas.
1. Se você quiser pesquisar um ambiente específico, poderá acessar  `"<test-endpoint>/actuator/env/{toMatch}"` a URL para exibi-lo.

Para exibir todos os pontos de extremidade internos, consulte [expondo pontos de extremidade](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html#production-ready-endpoints-exposing-endpoints)

## <a name="next-steps"></a>Próximas etapas

* [Entender as métricas para o Azure Spring Cloud](spring-cloud-concept-metrics.md)
* [Noções básicas sobre o status do aplicativo no Azure Spring Cloud](spring-cloud-concept-app-status.md)

