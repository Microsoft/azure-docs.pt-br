---
title: 'Tutorial: Integrar o SSO (logon único) do Azure Active Directory ao Maverics Identity Orchestrator SAML Connector | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Maverics Identity Orchestrator SAML Connector.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 19f6b0601afe9ad84f02c93d7f6e1ae3a71a06a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585087"
---
# <a name="integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>Integrar o logon único do Azure AD ao Maverics Identity Orchestrator SAML Connector

O Maverics Identity Orchestrator do Strata fornece uma forma simples de integrar aplicativos locais ao Azure AD (Azure Active Directory) para autenticação e controle de acesso. O Maverics Orchestrator é capaz de modernizar a autenticação e a autorização para aplicativos que atualmente dependem de cabeçalhos, cookies e outros métodos de autenticação proprietários. As instâncias do Maverics Orchestrator podem ser implantadas localmente ou na nuvem. 

Este tutorial de acesso híbrido demonstra como migrar um aplicativo Web local atualmente protegido por um produto de gerenciamento de acesso à Web herdado para usar o Azure AD para autenticação e controle de acesso. Veja a seguir as etapas básicas:

1. Configurar o Maverics Orchestrator
1. Efetuar proxy de um aplicativo
1. Registrar um aplicativo empresarial no Azure AD
1. Autenticar por meio do Azure e autorizar o acesso ao aplicativo
1. Adicionar cabeçalhos para acesso fácil ao aplicativo
1. Trabalhar com vários aplicativos

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Maverics Identity Orchestrator SAML Connector habilitada para SSO. Para obter o software do Maverics, entre em contato com o [departamento de vendas do Strata](mailto:sales@strata.io).
* Pelo menos um aplicativo que usa autenticação baseada em cabeçalho. Os exemplos funcionam em um aplicativo chamado Sonar, que é hospedado em https://app.sonarsystems.com, e um aplicativo chamado Connectulum, hospedado em https://app.connectulum.com.
* Um computador Linux para hospedar o Maverics Orchestrator
  * Sistema operacional: RHEL 7.7 ou superior, CentOS 7+
  * Disco: no mínimo 10 GB
  * Memória: no mínimo 4 GB
  * Portas: 22 (SSH/SCP), 443, 7474
  * Acesso à raiz para tarefas administrativas/de instalação
  * Saída de rede do servidor que hospeda o Maverics Identity Orchestrator para o seu aplicativo protegido

## <a name="step-1-set-up-the-maverics-orchestrator"></a>Etapa 1: configurar o Maverics Orchestrator

### <a name="install-maverics"></a>Instalar o Maverics

1. Obtenha o pacote RPM mais recente do Maverics. Copie o pacote para o sistema no qual você deseja instalar o software do Maverics.

1. Instale o pacote do Maverics substituindo o nome do arquivo no lugar de `maverics.rpm`.

   `sudo rpm -Uvf maverics.rpm`

   Após a instalação do Maverics, ele será executado como um serviço em `systemd`. Para confirmar se o serviço está em execução, execute o seguinte comando:

   `sudo systemctl status maverics`

1. Para reiniciar o Orchestrator e seguir os logs, você pode executar o seguinte comando:

   `sudo service maverics restart; sudo journalctl --identifier=maverics -f`

Depois de instalar o Maverics, o arquivo `maverics.yaml` padrão é criado no diretório `/etc/maverics`. Antes de você editar a configuração para incluir `appgateways` e `connectors`, o arquivo de configuração terá esta aparência:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```

### <a name="configure-dns"></a>Configurar DNS

O DNS será útil para que você não precise se lembrar do IP do servidor do Orchestrator.

Edite o arquivo de hosts do computador do navegador (seu laptop), usando um IP do Orchestrator hipotético de 12.34.56.78. Em sistemas operacionais baseados em Linux, esse arquivo está localizado em `/etc/hosts`. No Windows, ele está localizado em `C:\windows\system32\drivers\etc`.

```
12.34.56.78 sonar.maverics.com
12.34.56.78 connectulum.maverics.com
```

Para confirmar se o DNS está configurado conforme o esperado, você pode fazer uma solicitação para o ponto de extremidade de status do Orchestrator. No seu navegador, solicite http://sonar.maverics.com:7474/status.

### <a name="configure-tls"></a>Configurar TLS

A comunicação por canais seguros para se comunicar com o Orchestrator é crítico para a manutenção da segurança. Você pode adicionar um par de certificados/chaves em sua seção `tls` para conseguir isso.

Para gerar um certificado autoassinado e uma chave para o servidor do Orchestrator, execute o seguinte comando de dentro do diretório `/etc/maverics`:

`openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out maverics.crt -keyout maverics.key`

> [!NOTE]
> Para ambientes de produção, você provavelmente desejará usar um certificado assinado por uma autoridade de certificação conhecida para evitar avisos no navegador. A [Let's Encrypt](https://letsencrypt.org/) é uma opção boa e gratuita se você está procurando uma autoridade de certificação confiável.

Agora, use o certificado e a chave gerados recentemente para o Orchestrator. O arquivo de configuração agora deve conter este código:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key
```

Para confirmar se o TLS está configurado conforme o esperado, reinicie o serviço Maverics e faça uma solicitação para o ponto de extremidade de status. No seu navegador, solicite https://sonar.maverics.com/status.

## <a name="step-2-proxy-an-application"></a>Etapa 2: efetuar proxy de um aplicativo

Em seguida, configure o proxy básico no Orchestrator usando `appgateways`. Esta etapa ajuda a validar que o Orchestrator tem a conectividade necessária com o aplicativo protegido.

O arquivo de configuração agora deve conter este código:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com
```

Para confirmar que o proxy está funcionando conforme o esperado, reinicie o serviço Maverics e faça uma solicitação para o aplicativo por meio do proxy do Maverics. No seu navegador, solicite https://sonar.maverics.com. Opcionalmente, você pode fazer uma solicitação para recursos de aplicativo específicos, por exemplo, `https://sonar.maverics.com/RESOURCE`, em que `RESOURCE` é um recurso de aplicativo válido do aplicativo upstream protegido.

## <a name="step-3-register-an-enterprise-application-in-azure-ad"></a>Etapa 3: registrar um aplicativo empresarial no Azure AD

Agora, crie um aplicativo empresarial no Azure AD que será usado para autenticar usuários finais.

> [!NOTE]
> Quando você usa recursos do Azure AD como acesso condicional, é importante criar um aplicativo empresarial por aplicativo local. Isso permite acesso condicional por aplicativo, avaliação de risco por aplicativo, permissões atribuídas por aplicativo e assim por diante. Em geral, um aplicativo empresarial no Azure AD é mapeado para um conector do Azure no Maverics.

Para registrar um aplicativo empresarial no Azure AD:

1. No locatário do Azure AD, acesse **Aplicativos empresariais** e, depois, **Novos aplicativos**. Na galeria do Azure AD, pesquise por **Maverics Identity Orchestrator SAML Connector** e selecione-o.

1. No painel **Propriedades** do Maverics Identity Orchestrator SAML Connector, defina **Atribuição de usuário obrigatória?** como **Não** para permitir que o aplicativo funcione para usuários recém-migrados.

1. No painel **Visão geral** do Maverics Identity Orchestrator SAML Connector, selecione **Configurar logon único** e escolha **SAML**.

1. No painel **Logon baseado em SAML** do Maverics Identity Orchestrator SAML Connector, edite a **Configuração Básica do SAML** selecionando o botão **Editar** (ícone de lápis).

   ![Captura de tela do botão Editar de "Configuração Básica do SAML".](common/edit-urls.png)

1. Insira uma **ID da Entidade** de `https://sonar.maverics.com`. A ID da entidade precisa ser exclusiva entre os aplicativos no locatário e pode ser um valor arbitrário. Você usará esse valor ao definir o campo `samlEntityID` para o seu conector do Azure na próxima seção.

1. Insira uma **URL de Resposta** de `https://sonar.maverics.com/acs`. Você usará esse valor ao definir o campo `samlConsumerServiceURL` para o seu conector do Azure na próxima seção.

1. Insira uma **URL de entrada** de `https://sonar.maverics.com/`. Esse campo não será usado pelo Maverics, mas é necessário no Azure AD para permitir que os usuários obtenham acesso ao aplicativo por meio do portal Meus Aplicativos do Azure AD.

1. Clique em **Salvar**.

1. Na seção **Certificado de Autenticação SAML**, selecione o botão **Copiar** para copiar o valor da **URL de Metadados de Federação do Aplicativo** e salve-o no seu computador.

   ![Captura de tela do botão Copiar do "Certificado de Autenticação SAML".](common/copy-metadataurl.png)

## <a name="step-4-authenticate-via-azure-and-authorize-access-to-the-application"></a>Etapa 4: autenticar por meio do Azure e autorizar o acesso ao aplicativo

Em seguida, coloque o aplicativo empresarial que você acabou de criar em uso configurando o conector do Azure no Maverics. Essa configuração `connectors`, emparelhada com o bloco `idps`, permite que o orquestrador autentique usuários.

O arquivo de configuração agora deve conter o código a seguir. Você precisa substituir `METADATA_URL` pelo valor da URL de Metadados de Federação do aplicativo da etapa anterior.

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com
```

Para confirmar que a autenticação está funcionando conforme o esperado, reinicie o serviço Maverics e faça uma solicitação para um recurso de aplicativo por meio do proxy do Maverics. Você deverá ser redirecionado para o Azure para autenticação antes de acessar o recurso.

## <a name="step-5-add-headers-for-seamless-application-access"></a>Etapa 5: adicionar cabeçalhos para acesso fácil ao aplicativo

Você ainda não está enviando cabeçalhos para o aplicativo upstream. Vamos adicionar `headers` à solicitação à medida que ela passa pelo proxy do Maverics para permitir que o aplicativo upstream identifique o usuário.

O arquivo de configuração agora deve conter este código:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

    headers:
      email: azureSonarApp.name
      firstname: azureSonarApp.givenname
      lastname: azureSonarApp.surname

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com
```

Para confirmar que a autenticação está funcionando conforme o esperado, faça uma solicitação para um recurso de aplicativo por meio do proxy do Maverics. O aplicativo protegido agora deve estar recebendo cabeçalhos na solicitação. 

Fique à vontade para editar as chaves de cabeçalho se o seu aplicativo espera cabeçalhos diferentes. Todas as declarações que retornam do Azure AD como parte do fluxo SAML estão disponíveis para uso em cabeçalhos. Por exemplo, você pode incluir outro cabeçalho de `secondary_email: azureSonarApp.email`, em que `azureSonarApp` é o nome do conector e `email` é uma declaração retornada pelo Azure AD. 

## <a name="step-6-work-with-multiple-applications"></a>Etapa 6: trabalhar com vários aplicativos

Agora, vamos dar uma olhada no que é necessário para o proxy para vários aplicativos que estão em hosts diferentes. Para realizar essa etapa, configure outro Gateway de Aplicativo, outro aplicativo empresarial no Azure AD e outro conector.

O arquivo de configuração agora deve conter este código:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp
  - name: azureConnectulumApp

appgateways:
  - name: sonar
    host: sonar.maverics.com
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

    headers:
      email: azureSonarApp.name
      firstname: azureSonarApp.givenname
      lastname: azureSonarApp.surname

  - name: connectulum
    host: connectulum.maverics.com
    location: /
    # Replace https://app.connectulum.com with the address of your protected application
    upstream: https://app.connectulum.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureConnectulumApp.authenticated}}", "true"]

    headers:
      email: azureConnectulumApp.name
      firstname: azureConnectulumApp.givenname
      lastname: azureConnectulumApp.surname

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com

  - name: azureConnectulumApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://connectulum.maverics.com/acs
    samlEntityID: https://connectulum.maverics.com
```

Talvez você tenha notado que o código adiciona um campo `host` às suas definições de Gateway de Aplicativo. O campo `host` permite que o Maverics Orchestrator diferencie para qual host upstream efetuar proxy do tráfego.

Para confirmar que o Gateway de Aplicativo recém-adicionado está funcionando conforme o esperado, faça uma solicitação para https://connectulum.maverics.com.

## <a name="advanced-scenarios"></a>Cenários avançados

### <a name="identity-migration"></a>Migração de identidade

Não quer mais continuar com a sua ferramenta de gerenciamento de acesso à Web cujo ciclo de vida chegou ao fim, mas não tem uma forma de migrar seus usuários sem redefinições de senha em massa? O Maverics Orchestrator dá suporte à migração de identidade usando `migrationgateways`.

### <a name="web-server-gateways"></a>Gateways do servidor Web

Não quer refazer a sua rede e efetuar proxy do tráfego por meio do Maverics Orchestrator? Tudo bem. O Maverics Orchestrator pode ser emparelhado com os módulos (gateways de servidor Web) para oferecer as mesmas soluções sem proxy.

## <a name="wrap-up"></a>Conclusão

Neste ponto, você instalou o Maverics Orchestrator, criou e configurou um aplicativo empresarial no Azure AD e configurou o Orchestrator para fazer proxy para um aplicativo protegido, exigindo autenticação e impondo uma política. Para saber mais sobre como o Maverics Orchestrator pode ser usado para casos de uso do gerenciamento de identidades distribuídas, [entre em contato com a Strata](mailto:sales@strata.io).

## <a name="next-steps"></a>Próximas etapas

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)
