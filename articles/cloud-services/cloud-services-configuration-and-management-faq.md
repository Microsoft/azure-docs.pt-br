---
title: Problemas de configuração e gerenciamento para perguntas frequentes sobre Serviços de Nuvem do Microsoft Azure | Microsoft Docs
description: Este artigo lista as perguntas frequentes sobre a configuração e o gerenciamento de Serviços de Nuvem do Microsoft Azure.
services: cloud-services
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: genli
ms.openlocfilehash: 925461ba6c74fe0bcf286692c1a19bb4e7c02dfa
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68254413"
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Configuração e problemas de gerenciamento para Serviços de Nuvem do Azure: Perguntas frequentes (FAQs)

Este artigo inclui perguntas frequentes sobre a configuração e o gerenciamento de [Serviços de Nuvem do Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Você também pode consultar a [página de tamanho de VM de Serviços de Nuvem](cloud-services-sizes-specs.md) para obter informações de tamanho.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**Certificados**

- [Por que a cadeia de certificados do meu certificado SSL de serviço de nuvem está incompleta?](#why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete)
- [Qual é a finalidade do "Certificado de Criptografia das Ferramentas do Microsoft Azure para Extensões"?](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- [Como posso gerar uma CSR (Solicitação de Assinatura de Certificado) sem fazer "RDP" para a instância?](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [O certificado de gerenciamento de serviço de nuvem está vencendo. Como renová-lo?](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [Como automatizar a instalação do certificado SSL principal (.pfx) e do certificado intermediário (.p7b)?](#how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b)
- [Qual é a finalidade do certificado de “Gerenciamento de Serviço do Microsoft Azure para MachineKey”?](#what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate)

**Monitoramento e registro em log**

- [Quais são os recursos futuros do serviço de nuvem no portal do Azure que podem ajudar a gerenciar e monitorar aplicativos?](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [Por que o IIS para de gravar no diretório de log?](#why-does-iis-stop-writing-to-the-log-directory)
- [Como habilitar o registro em log do WAD para serviços de nuvem?](#how-do-i-enable-wad-logging-for-cloud-services)

**Configuração de rede**

- [Como fazer para configurar o tempo limite para o Azure Load Balancer?](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [Como fazer para associar um endereço IP estático ao meu serviço de nuvem?](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [Quais são os recursos e as funcionalidades que o DDOS e o IPS/IDS básicos do Azure oferecem?](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [Como habilitar o HTTP/2 na VM dos Serviços de Nuvem?](#how-to-enable-http2-on-cloud-services-vm)

**Permissões**

- [Os engenheiros internos da Microsoft podem tornar o desktop remoto para instâncias de serviço de nuvem sem permissão?](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [Não consigo tornar o desktop remoto para VM de serviço de nuvem usando o arquivo RDP. Recebo o seguinte erro: Ocorreu um erro de autenticação (Código: 0x80004005)](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**Dimensionamento**

- [Não consigo dimensionar além de X instâncias](#i-cannot-scale-beyond-x-instances)
- [Como posso configurar o dimensionamento automático com base nas métricas de memória?](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**Genérico**

- [Como fazer para adicionar "nosniff" ao meu site?](#how-do-i-add-nosniff-to-my-website)
- [Como fazer para personalizar o IIS para uma função web?](#how-do-i-customize-iis-for-a-web-role)
- [O que é o limite de cota para meu serviço de nuvem?](#what-is-the-quota-limit-for-my-cloud-service)
- [Por que a unidade na VM do meu serviço de nuvem mostra muito pouco espaço livre em disco?](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [Como adicionar uma extensão antimalware para os Serviços de Nuvem de maneira automatizada?](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [Como habilitar a SNI (Indicação de Nome de Servidor) para Serviços de Nuvem?](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [Como adicionar marcas ao meu Serviço de Nuvem do Azure?](#how-can-i-add-tags-to-my-azure-cloud-service)
- [O portal do Azure não exibe a versão do SDK do meu serviço de nuvem. Como posso mudar isso?](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [Desejo interromper o serviço de nuvem por vários meses. Como reduzir o custo de cobrança do serviço de nuvem sem perder o endereço IP?](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## <a name="certificates"></a>Certificados

### <a name="why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete"></a>Por que a cadeia de certificados do meu certificado SSL de serviço de nuvem está incompleta?
    
É recomendável que os clientes instalem uma cadeia de certificados completa (certificado de folha, certificados intermediários e certificado raiz), em vez de apenas o certificado de folha. Ao instalar apenas o certificado de folha, você confia no Windows para criar a cadeia de certificados percorrendo a CTL. Se ocorrerem problemas intermitentes de rede ou DNS no Azure ou no Windows Update quando o Windows estiver tentando validar o certificado, o certificado poderá ser considerado inválido. Ao instalar a cadeia de certificados completa, esse problema pode ser evitado. O blog em [Como instalar um certificado SSL encadeado](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) mostra como fazer isso.

### <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>Qual é a finalidade do "Certificado de Criptografia das Ferramentas do Microsoft Azure para Extensões"?

Esses certificados são criados automaticamente sempre que uma extensão é adicionada ao serviço de nuvem. Normalmente, essa é a extensão WAD ou a extensão RDP, mas pode ser pessoas, como a extensão Antimalware ou Coletor de Log. Esses certificados são usados apenas para criptografar e descriptografar a configuração privada da extensão. A data de validade nunca é verificada, assim, não importa se o certificado expirou. 

Você pode ignorar esses certificados. Se você quiser limpar os certificados, poderá tentar excluir todos eles. O Azure emitirá um erro se você tentar excluir um certificado que esteja em uso.

### <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Como posso gerar uma CSR (Solicitação de Assinatura de Certificado) sem fazer "RDP" para a instância?

Consulte o documento de diretrizes a seguir:

[Como obter um certificado para uso com o WAWS (Sites do Microsoft Azure)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

O CSR é apenas um arquivo de texto. Ele não precisa ser criado no computador em que o certificado, por fim, será usado. Embora este documento tenha sido escrito para um Serviço de Aplicativo, a criação de CSR é genérica e também se aplica a Serviços de Nuvem.

### <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>Meu certificado de gerenciamento de serviço de nuvem está vencendo. Como renová-lo?

Você pode usar comandos do PowerShell a seguir para renovar seus certificados de gerenciamento:

    Add-AzureAccount
    Select-AzureSubscription -Current -SubscriptionName <your subscription name>
    Get-AzurePublishSettingsFile

O **Get-AzurePublishSettingsFile** criará um novo certificado de gerenciamento em **assinatura** > **certificados de gerenciamento** no portal do Azure. O nome do novo certificado se parece com "YourSubscriptionNam]-[CurrentDate]-credenciais".

### <a name="how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b"></a>Como automatizar a instalação do certificado SSL principal (.pfx) e do certificado intermediário (.p7b)?

Você pode automatizar essa tarefa usando um script de inicialização (lote/cmd/PowerShell) e registrar esse script de inicialização no arquivo de definição de serviço. Adicione o script de inicialização e o certificado (arquivo. p7b) na pasta do projeto do mesmo diretório do script de inicialização.

### <a name="what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate"></a>Qual é a finalidade do certificado de “Gerenciamento de Serviço do Microsoft Azure para MachineKey”?

Este certificado é usado para encriptar chaves de máquina em Funções de Web do Azure. Para saber mais, confira [este comunicado](https://docs.microsoft.com/security-updates/securityadvisories/2018/4092731).

Para obter mais informações, confira os seguintes artigos:
- [Como configurar e executar tarefas de inicialização para um serviço de nuvem](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks)
- [Tarefas de inicialização comuns do Serviço de Nuvem](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks-common)

## <a name="monitoring-and-logging"></a>Monitoramento e registro em log

### <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Quais são os recursos futuros do serviço de nuvem no portal do Azure que podem ajudar a gerenciar e monitorar aplicativos?

Capacidade de gerar um novo certificado De Protocolo De Área De Trabalho Remota (RDP) estará disponível em breve. Como alternativa, você pode executar o seguinte script:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
Capacidade de escolher o blob ou local para o seu csdef e de carregar localização de cscfg em breve. Usando [New-AzureDeployment](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-4.0.0), você pode definir o valor de cada local.

Capacidade de monitorar as métricas no nível de instância. Recursos de monitoramentos adicionais estão disponíveis em [como monitorar Serviços De Nuvem](cloud-services-how-to-monitor.md).

### <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Por que o IIS para de gravar no diretório de log?
Você esgotou a cota de armazenamento local para gravar no diretório de log. Para corrigir isso, você pode executar uma destas três ações:
* Habilitar o diagnóstico para o IIS e mover o diagnóstico periodicamente para o armazenamento de Blobs.
* Remover manualmente os arquivos de log do diretório de log.
* Aumentar os limite de cota para recursos locais.

Para obter mais informações, consulte um dos seguintes documentos:
* [Armazenar e exibir dados de diagnóstico no Armazenamento do Azure](cloud-services-dotnet-diagnostics-storage.md)
* [Logs do IIS param de gravar no serviço de nuvem](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

### <a name="how-do-i-enable-wad-logging-for-cloud-services"></a>Como habilitar o registro em log do WAD para serviços de nuvem?
Você pode habilitar o log do Windows Azure Diagnostics (WAD) através das seguintes opções:
1. [Ativar a partir do Visual Studio](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)
2. [Habilitar por meio de código .NET](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics)
3. [Ativar através do Powershell](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell)

Para obter as configurações atuais do WAD do seu Serviço em Nuvem, você pode usar [Get-AzureServiceDiagnosticsExtensions](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell#get-current-diagnostics-extension-configuration) pscmd ou pode visualizá-lo no portal a partir da folha "Serviços em Nuvem -> Extensões".


## <a name="network-configuration"></a>Configuração de rede

### <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Como fazer para configurar o tempo limite para o Azure Load Balancer?
Você pode especificar o tempo limite no arquivo de definição de serviço (csdef) assim:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="mgVS2015Worker" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10100"   idleTimeoutInMinutes="30" />
    </Endpoints>
  </WorkerRole>
```
Veja [Novo: tempo limite de ociosidade configurável para o Azure Load Balancer](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) para obter mais informações.

### <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Como fazer para associar um endereço IP estático ao meu serviço de nuvem?
Para configurar um endereço IP estático, você precisa criar um IP reservado. Esse IP reservado pode ser associado a um novo serviço de nuvem ou a uma implantação existente. Consulte os documentos a seguir para obter detalhes:
* [Como criar um endereço IP reservado](../virtual-network/virtual-networks-reserved-public-ip.md#manage-reserved-vips)
* [Reservar o endereço IP de um serviço de nuvem existente](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
* [Associar um IP reservado a um novo serviço de nuvem](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-new-cloud-service)
* [Associar um IP reservado a uma implantação em execução](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-running-deployment)
* [Associar um IP reservado a um serviço de nuvem usando um arquivo de configuração de serviço](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Quais são os recursos e as funcionalidades que o DDOS e o IPS/IDS básicos do Azure oferecem?
O Azure tem IPS/IDS nos servidores físicos do datacenter para proteger-se contra ameaças. Além disso, os clientes podem implantar soluções de segurança de terceiros, como firewalls de aplicativo Web, firewalls de rede, antimalware, IDS/IPS (sistema de detecção de intrusão/sistema de prevenção de intrusão) e muito mais. Para obter mais informações, consulte [Proteger seus dados e ativos e cumprir padrões de segurança globais](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity).

A Microsoft monitora continuamente servidores, redes e aplicativos para detectar ameaças. A abordagem de gerenciamento de ameaças em várias frentes do Azure, usa detecção de intrusão, prevenção de ataque de DDoS (negação de serviço distribuída), teste de penetração, análise de comportamento, detecção de anomalias e machine learning para reforçar constantemente sua defesa e reduzir os riscos. O Microsoft Antimalware para Azure protege os serviços de nuvem e as máquinas virtuais do Azure. Você tem a opção de implantar soluções de segurança de terceiros adicionalmente, como firewalls de aplicativo Web, firewalls de rede, antimalware, IDS/IPS (sistema de detecção de intrusão/sistema de prevenção de intrusão) e muito mais.

### <a name="how-to-enable-http2-on-cloud-services-vm"></a>Como habilitar o HTTP/2 na VM dos Serviços de Nuvem?

O Windows 10 e Windows Server 2016 vêm com suporte para HTTP/2 no lado do cliente e do servidor. Se seu cliente (navegador) estiver se conectando ao servidor do IIS sobre TLS que negocia HTTP/2 por meio de extensões do TLS, então não será necessário fazer nenhuma alteração no lado do servidor. Isso ocorre porque, por TLS, o cabeçalho h2-14 que especifica o uso de HTTP/2 é enviado por padrão. Se, por outro lado, o cliente estiver enviando um cabeçalho de atualização a ser atualizado para HTTP/2, será necessário fazer a alteração abaixo no lado do servidor para garantir que a atualização funcione e que você termine com uma conexão HTTP/2. 

1. Execute regedit.exe.
2. Navegue até a chave do Registro: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Crie um novo valor DWORD chamado **DuoEnabled**.
4. Defina seu valor como 1.
5. Reinicie seu servidor.
6. Vá para o **Site da Web padrão** e, em **Associações**, crie uma nova associação TLS com o certificado autoassinado recém-criado. 

Para obter mais informações, consulte:

- [HTTP/2 no IIS](https://blogs.iis.net/davidso/http2)
- [Vídeo: HTTP 2 no Windows 10: Navegador, Aplicativos e Servidor Web](https://channel9.msdn.com/Events/Build/2015/3-88)
         

Essas etapas poderiam ser automatizadas por meio de uma tarefa de inicialização. Assim, sempre que uma nova instância PaaS for criada, ela poder á fazer as alterações acima no Registro do sistema. Para obter mais informações, consulte [Como configurar e executar tarefas de inicialização para um serviço de nuvem](cloud-services-startup-tasks.md).

 
Quando isso tiver sido feito, será possível verificar se o HTTP/2 foi habilitado ou não usando um dos métodos a seguir:

- Habilite a versão do protocolo nos logs do IIS e examine os logs do IIS. Ela mostrará HTTP/2 nos logs. 
- Habilite a Ferramenta do Desenvolvedor F12 no Internet Explorer/Microsoft Edge e mude para a guia Rede para verificar o protocolo. 

Para obter mais informações, consulte [HTTP/2 on IIS](https://blogs.iis.net/davidso/http2) (HTTP/2 no IIS).

## <a name="permissions"></a>Permissões

### <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Como implementar acesso baseado em função para Serviços de Nuvem?
Serviços de Nuvem não dão suporte ao modelo RBAC (controle de acesso baseado em função), uma vez que ele não é um serviço do baseado no Azure Resource Manager.

Consulte [Entender as diferentes funções no Azure](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remote-desktop"></a>Área de trabalho remota

### <a name="can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission"></a>Os engenheiros internos da Microsoft podem tornar o desktop remoto para instâncias de serviço de nuvem sem permissão?
A Microsoft segue um processo estrito que não permite que os engenheiros internos tornem o desktop remoto em seu serviço de nuvem sem permissão por escrito (email ou outra comunicação por escrito) do proprietário ou seu representante.

### <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>Não consigo tornar o desktop remoto para VM de serviço de nuvem usando o arquivo RDP. Recebo o seguinte erro: Ocorreu um erro de autenticação (Código: 0x80004005)

Esse erro pode ocorrer se você usar o arquivo RDP a partir de um computador associado ao Microsoft Azure Active Directory. Para resolver esse problema, siga estas etapas:

1. Clique com o botão direito no arquivo RDP que você fez download e, em seguida, selecione **Editar**.
2. Adicione "&#92;" como prefixo antes do nome de usuário. Por exemplo, use **.\nome de usuário** em vez de **nome de usuário**.

## <a name="scaling"></a>Dimensionamento

### <a name="i-cannot-scale-beyond-x-instances"></a>Não consigo dimensionar além de X instâncias
Sua Assinatura do Azure tem um limite no número de núcleos que você pode usar. O dimensionamento não funcionará se você tiver usado todos os núcleos disponíveis. Por exemplo, se você tiver um limite de 100 núcleos, isso significa você poderia ter 100 instâncias de máquina virtual A1 dimensionadas para seu serviço de nuvem ou 50 instâncias de máquina virtual A2.

### <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>Como posso configurar o dimensionamento automático com base nas métricas de memória?

Atualmente, não há suporte para dimensionamento automático com base nas métricas de memória para os Serviços de Nuvem. 

Para contornar esse problema, você pode usar o Application Insights. O dimensionamento automático oferece suporte ao Application Insights como uma fonte de métricas e pode dimensionar a contagem de instâncias de função com base na métrica de convidado como "Memória".  Você precisa configurar o Application Insights no seu arquivo de pacote do projeto de serviço de nuvem (*.cspkg) e habilitar a extensão de Diagnóstico do Microsoft Azure no serviço para implementar essa tarefa.

Para obter mais detalhes sobre como utilizar uma métrica personalizada por meio do Application Insights para configurar o dimensionamento automático em serviços de nuvem, consulte [Introdução ao dimensionamento automático por uma métrica personalizada no Azure](../azure-monitor/platform/autoscale-custom-metric.md)

Para obter mais informações sobre como integrar o Diagnóstico do Microsoft Azure com o Application Insights para serviços de nuvem, consulte [Enviar serviço de nuvem, máquina virtual ou dados de diagnóstico do Service Fabric para o Application Insights](../azure-monitor/platform/diagnostics-extension-to-application-insights.md)

Para obter mais informações sobre como habilitar o Application Insights para serviços de nuvem, consulte [Application Insights para serviços de nuvem do Azure](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices)

Para obter mais informações sobre como habilitar o log de Diagnóstico do Microsoft Azure para serviços de nuvem, consulte [Configurar diagnósticos para serviços de nuvem do Azure e máquinas virtuais](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)

## <a name="generic"></a>Genérico

### <a name="how-do-i-add-nosniff-to-my-website"></a>Como fazer para adicionar "nosniff" ao meu site?
Para evitar que clientes detectem os tipos MIME, adicione uma configuração ao arquivo *web.config*.

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

Ela também pode ser adicionada como uma configuração no IIS. Use o comando abaixo com o artigo [tarefas comuns de inicialização](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe).

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="how-do-i-customize-iis-for-a-web-role"></a>Como fazer para personalizar o IIS para uma função web?
Use o script de inicialização do IIS do artigo [tarefas comuns de inicialização](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe).

### <a name="what-is-the-quota-limit-for-my-cloud-service"></a>O que é o limite de cota para meu serviço de nuvem?
Consulte [Limites específicos do serviço](../azure-subscription-service-limits.md#subscription-limits).

### <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Por que a unidade na VM do meu serviço de nuvem mostra muito pouco espaço livre em disco?
Esse comportamento é esperado e não deve causar nenhum problema ao seu aplicativo. Registro em log está ativado para a unidade %approot% em VMs virtuais de PaaS do Azure, que consome basicamente o dobro da quantidade de espaço que os arquivos normalmente ocupam. No entanto, há vários elementos a serem considerados que essencialmente transformam isso em um não problema.

O tamanho da unidade% approot% é calculado \<como tamanho de. cspkg + tamanho máximo do diário + uma margem de espaço livre > ou 1,5 GB, o que for maior. O tamanho da sua VM não tem influência sobre esse cálculo. (O tamanho da VM só afeta o tamanho da unidade C: temporária.) 

Não tem suporte para gravar na unidade %approot%. Se você estiver gravando na VM do Azure, deverá fazer isso em um recurso LocalStorage temporário (ou outra opção, como o armazenamento de Blobs, Arquivos do Azure etc.). Portanto, a quantidade de espaço livre na pasta %approot% não é significativa. Se você não tiver certeza de que seu aplicativo está gravando na unidade %approot%, poderá sempre permitir que o serviço seja executado por alguns dias e, em seguida, comparar os tamanhos "antes" e "depois". 

O Azure não gravará nada na unidade %approot%. Quando o VHD tiver sido criado do seu .cspkg e montado na VM do Azure, você poderá gravar apenas seu aplicativo nessa unidade. 

As definições do diário são não configuráveis, assim, não é possível desligá-las.

### <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Como adicionar uma extensão antimalware para os Serviços de Nuvem de maneira automatizada?

É possível habilitar a extensão Antimalware usando o script do PowerShell na Tarefa de Inicialização. Siga as etapas nestes artigos para implementá-la: 
 
- [Criar uma tarefa de inicialização do PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 )

Para obter mais informações sobre cenários de implantação do Antimalware e como habilitá-lo no portal, consulte [Antimalware Deployment Scenarios](../security/azure-security-antimalware.md#antimalware-deployment-scenarios) (Cenários de implantação de Antimalware).

### <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Como habilitar a SNI (Indicação de Nome de Servidor) para Serviços de Nuvem?

É possível habilitar a SNI nos Serviços de Nuvem usando um dos seguintes métodos:

**Método 1: usar o PowerShell**

A associação de SNI pode ser configurada usando o cmdlet do PowerShell **New-WebBinding** em uma tarefa de inicialização para uma instância de função do serviço de nuvem conforme descrito abaixo:
    
    New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags 
    
Conforme descrito [aqui](https://technet.microsoft.com/library/ee790567.aspx), o $sslFlags poderia ser um dos valores como o seguinte:

|Valor|Significado|
------|------
|0|Nenhuma SNI|
|1|SNI habilitada |
|2 |Nenhuma associação de SNI que usa o Repositório de certificados central|
|3|Associação de SNI que usa o Repositório de certificados central |
 
**Método 2: usar código**

A associação de SNI também pode ser configurada por meio de código na inicialização da função conforme descrito nesta [postagem no blog](https://blogs.msdn.microsoft.com/jianwu/2014/12/17/expose-ssl-service-to-multi-domains-from-the-same-cloud-service/):

    
    //<code snip> 
                    var serverManager = new ServerManager(); 
                    var site = serverManager.Sites[0]; 
                    var binding = site.Bindings.Add(“:443:www.test1.com”, newCert.GetCertHash(), “My”); 
                    binding.SetAttributeValue(“sslFlags”, 1); //enables the SNI 
                    serverManager.CommitChanges(); 
    //</code snip> 
    
Usando qualquer uma das abordagens acima, os respectivos certificados (*.pfx) para os nomes do host específicos precisam ser instaladas primeiro nas instâncias de função usando uma tarefa de inicialização ou por meio de código para que a associação de SNI entre em vigor.

### <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Como adicionar marcas ao meu Serviço de Nuvem do Azure? 

O Serviço de Nuvem é um recurso clássico. Somente os recursos criados por meio do Azure Resource Manager oferecem suporte a marcas. Não é possível aplicar marcas a recursos clássicos como o Serviço de Nuvem. 

### <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>O portal do Azure não exibe a versão do SDK do meu serviço de nuvem. Como posso mudar isso?

Estamos trabalhando para colocar este recurso no portal do Azure. Enquanto isso, você pode usar comandos do PowerShell a seguir para obter a versão do SDK:

    Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot

### <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>Desejo interromper o serviço de nuvem por vários meses. Como reduzir o custo de cobrança do serviço de nuvem sem perder o endereço IP?

Um serviço de nuvem já implantado é cobrado pela computação e armazenamento que ele usa. Portanto, mesmo que você desligue a VM do Azure, você ainda será cobrado pelo armazenamento. 

Encontre a seguir o que você pode fazer para reduzir sua cobrança sem perder o endereço IP para seu serviço:

1. [Reserve o endereço IP](../virtual-network/virtual-networks-reserved-public-ip.md) antes de excluir as implantações.  Você será cobrado somente para esse endereço IP. Para obter mais informações sobre cobrança de endereço IP, consulte [preços de endereços IP](https://azure.microsoft.com/pricing/details/ip-addresses/).
2. Exclua as implantações. Não exclua xxx.cloudapp.net, para que você possa usá-lo no futuro.
3. Se você desejar reimplantar o serviço de nuvem usando a mesma reserva de IP de sua assinatura, consulte [endereços IP reservados para serviços de nuvem e máquinas virtuais](https://azure.microsoft.com/blog/reserved-ip-addresses/).

