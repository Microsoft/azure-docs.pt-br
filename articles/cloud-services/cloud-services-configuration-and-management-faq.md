---
title: "Problemas de configuração e gerenciamento para perguntas frequentes sobre Serviços de Nuvem do Microsoft Azure | Microsoft Docs"
description: "Este artigo lista as perguntas frequentes sobre a configuração e o gerenciamento de Serviços de Nuvem do Microsoft Azure."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/20/2017
ms.author: genli
ms.openlocfilehash: 2ce497146abf664b0084cd96963523812f166e3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemas de configuração e gerenciamento para Serviços de Nuvem do Azure: perguntas frequentes

Este artigo inclui perguntas frequentes sobre a configuração e o gerenciamento de [Serviços de Nuvem do Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Você também pode consultar a [página de tamanho de VM de Serviços de Nuvem](cloud-services-sizes-specs.md) para obter informações de tamanho.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-add-nosniff-to-my-website"></a>Como fazer para adicionar "nosniff" ao meu site?
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

## <a name="how-do-i-customize-iis-for-a-web-role"></a>Como fazer para personalizar o IIS para uma função web?
Use o script de inicialização do IIS do artigo [tarefas comuns de inicialização](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe).

## <a name="i-cannot-scale-beyond-x-instances"></a>Não consigo dimensionar além de X instâncias
Sua Assinatura do Azure tem um limite no número de núcleos que você pode usar. O dimensionamento não funcionará se você tiver usado todos os núcleos disponíveis. Por exemplo, se você tiver um limite de 100 núcleos, isso significa você poderia ter 100 instâncias de máquina virtual A1 dimensionadas para seu serviço de nuvem ou 50 instâncias de máquina virtual A2.

## <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Como implementar Acesso Baseado em Função para Serviços de Nuvem?
Serviços de Nuvem não dão suporte ao modelo RBAC (Controle de Acesso Baseado em Função), uma vez que ele não é um serviço do baseado no Azure Resource Manager.

Consulte [RBAC do Azure versus administradores de assinatura clássica](../active-directory/role-based-access-control-what-is.md#azure-rbac-vs-classic-subscription-administrators).

## <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Como fazer para configurar o tempo limite para o Azure Load Balancer?
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
Consulte [Novo: tempo limite de ociosidade configurável para o Azure Load Balancer](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) para obter mais informações.

## <a name="can-microsoft-internal-engineers-rdp-to-cloud-service-instances-without-permission"></a>Os engenheiros internos da Microsoft podem fazer RDP para instâncias de serviço de nuvem sem permissão?
A Microsoft segue um processo estrito que não permite que os engenheiros internos façam RDP em seu serviço de nuvem sem permissão por escrito (email ou outra comunicação por escrito) do proprietário ou seu representante.

## <a name="why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete"></a>Por que a cadeia de certificados do meu certificado SSL de serviço de nuvem está incompleta?
É recomendável que os clientes instalem uma cadeia de certificados completa (certificado de folha, certificados intermediários e certificado raiz), em vez de apenas o certificado de folha. Ao instalar apenas o certificado de folha, você confia no Windows para criar a cadeia de certificados percorrendo a CTL. Se ocorrerem problemas intermitentes de rede ou DNS no Azure ou no Windows Update quando o Windows estiver tentando validar o certificado, o certificado poderá ser considerado inválido. Ao instalar a cadeia de certificados completa, esse problema pode ser evitado. O blog em [Como instalar um certificado SSL encadeado](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) mostra como fazer isso.

## <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Como fazer para associar um endereço IP estático ao meu serviço de nuvem?
Para configurar um endereço IP estático, você precisa criar um IP reservado. Esse IP reservado pode ser associado a um novo serviço de nuvem ou a uma implantação existente. Consulte os documentos a seguir para obter detalhes:
* [Como criar um endereço IP reservado](../virtual-network/virtual-networks-reserved-public-ip.md#manage-reserved-vips)
* [Reservar o endereço IP de um serviço de nuvem existente](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
* [Associar um IP reservado a um novo serviço de nuvem](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-new-cloud-service)
* [Associar um IP reservado a uma implantação em execução](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-running-deployment)
* [Associar um IP reservado a um serviço de nuvem usando um arquivo de configuração de serviço](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

## <a name="what-is-the-quota-limit-for-my-cloud-service"></a>O que é o limite de cota para meu serviço de nuvem?
Consulte [Limites específicos do serviço](../azure-subscription-service-limits.md#subscription-limits).

## <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Por que a unidade na VM do meu serviço de nuvem mostra muito pouco espaço livre em disco?
Esse comportamento é esperado e não deve causar nenhum problema ao seu aplicativo. Registro em log está ativado para a unidade %uproot% em VMs virtuais de PaaS do Azure, que consome basicamente o dobro da quantidade de espaço que os arquivos normalmente ocupam. No entanto, há vários elementos a serem considerados que essencialmente transformam isso em um não problema.

O tamanho da unidade %approot% é calculado como <tamanho do .cspkg + tamanho máximo do diário + margem de espaço livre> ou 1,5 GB, o que for maior. O tamanho da sua VM não tem influência sobre esse cálculo. (O tamanho da VM só afeta o tamanho da unidade C: temporária.) 

Não tem suporte para gravar na unidade %approot%. Se você estiver gravando na VM do Azure, deverá fazer isso em um recurso LocalStorage temporário (ou outra opção, como o armazenamento de Blobs, Arquivos do Azure etc.). Portanto, a quantidade de espaço livre na pasta %approot% não é significativa. Se você não tiver certeza de que seu aplicativo está gravando na unidade %approot%, poderá sempre permitir que o serviço seja executado por alguns dias e, em seguida, comparar os tamanhos "antes" e "depois". 

O Azure não gravará nada na unidade %approot%. Quando o VHD tiver sido criado do seu .cspkg e montado na VM do Azure, você poderá gravar apenas seu aplicativo nessa unidade. 

As definições do diário são não configuráveis, assim, não é possível desligá-las.

## <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Quais são os recursos e as funcionalidades que o DDOS e o IPS/IDS básicos do Azure oferecem?
O Azure tem IPS/IDS nos servidores físicos do datacenter para proteger-se contra ameaças. Além disso, os clientes podem implantar soluções de segurança de terceiros, como firewalls de aplicativo Web, firewalls de rede, antimalware, IDS/IPS (sistema de detecção de intrusão/sistema de prevenção de intrusão) e muito mais. Para obter mais informações, consulte [Proteger seus dados e ativos e cumprir padrões de segurança globais](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity).

A Microsoft monitora continuamente servidores, redes e aplicativos para detectar ameaças. A abordagem de gerenciamento de ameaças em várias frentes do Azure, usa detecção de intrusão, prevenção de ataque de DDoS (negação de serviço distribuída), teste de penetração, análise de comportamento, detecção de anomalias e machine learning para reforçar constantemente sua defesa e reduzir os riscos. O Microsoft Antimalware para Azure protege os serviços de nuvem e as máquinas virtuais do Azure. Você tem a opção de implantar soluções de segurança de terceiros adicionalmente, como firewalls de aplicativo Web, firewalls de rede, antimalware, IDS/IPS (sistema de detecção de intrusão/sistema de prevenção de intrusão) e muito mais.

## <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Por que o IIS para de gravar no diretório de log?
Você esgotou a cota de armazenamento local para gravar no diretório de log. Para corrigir isso, você pode executar uma destas três ações:
* Habilitar o diagnóstico para o IIS e mover o diagnóstico periodicamente para o armazenamento de Blobs.
* Remover manualmente os arquivos de log do diretório de log.
* Aumentar os limite de cota para recursos locais.

Para obter mais informações, consulte um dos seguintes documentos:
* [Armazenar e exibir dados de diagnóstico no Armazenamento do Azure](cloud-services-dotnet-diagnostics-storage.md)
* [Logs do IIS param de gravar no serviço de nuvem](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

## <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>Qual é a finalidade do "Certificado de Criptografia das Ferramentas do Microsoft Azure para Extensões"?
Esses certificados são criados automaticamente sempre que uma extensão é adicionada ao serviço de nuvem. Normalmente, essa é a extensão WAD ou a extensão RDP, mas pode ser pessoas, como a extensão Antimalware ou Coletor de Log. Esses certificados são usados apenas para criptografar e descriptografar a configuração privada da extensão. A data de validade nunca é verificada, assim, não importa se o certificado expirou. 

Você pode ignorar esses certificados. Se você quiser limpar os certificados, poderá tentar excluir todos eles. O Azure emitirá um erro se você tentar excluir um certificado que esteja em uso.

## <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Como posso gerar uma CSR (Solicitação de Assinatura de Certificado) sem fazer "RDP" para a instância?
Consulte o documento de diretrizes a seguir:

>[Como obter um certificado para uso com o WAWS (Sites do Microsoft Azure)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

Observe que uma CSR é apenas um arquivo de texto. Ela NÃO precisa ser criada no computador em que o certificado, por fim, será usado. Embora este documento tenha sido escrito para um Serviço de Aplicativo, a criação de CSR é genérica e também se aplica a Serviços de Nuvem.

## <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Como adicionar uma extensão antimalware para os Serviços de Nuvem de maneira automatizada?

É possível habilitar a extensão Antimalware usando o script do PowerShell na Tarefa de Inicialização. Siga as etapas nestes artigos para implementá-la: 
 
- [Criar uma tarefa de inicialização do PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](https://docs.microsoft.com/powershell/module/Azure/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 )

Para obter mais informações sobre cenários de implantação do Antimalware e como habilitá-lo no portal, consulte [Antimalware Deployment Scenarios](../security/azure-security-antimalware.md#antimalware-deployment-scenarios) (Cenários de implantação de Antimalware).

## <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Como habilitar a SNI (Indicação de Nome de Servidor) para Serviços de Nuvem?

É possível habilitar a SNI nos Serviços de Nuvem usando um dos seguintes métodos:

### <a name="method-1-use-powershell"></a>Método 1: usar o PowerShell

A associação de SNI pode ser configurada usando o cmdlet do PowerShell **New-WebBinding** em uma tarefa de inicialização para uma instância de função do serviço de nuvem conforme descrito abaixo:
    
    New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags 
    
Conforme descrito [aqui](https://technet.microsoft.com/library/ee790567.aspx), o $sslFlags poderia ser um dos valores como o seguinte:

|Valor|Significado|
------|------
|0|Nenhuma SNI|
|1|SNI habilitada |
|2 |Nenhuma associação de SNI que usa o Repositório de certificados central|
|3|Associação de SNI que usa o Repositório de certificados central |
 
### <a name="method-2-use-code"></a>Método 2: código de uso

A associação de SNI também pode ser configurada por meio de código na inicialização da função conforme descrito nesta [postagem no blog](https://blogs.msdn.microsoft.com/jianwu/2014/12/17/expose-ssl-service-to-multi-domains-from-the-same-cloud-service/):

    
    //<code snip> 
                    var serverManager = new ServerManager(); 
                    var site = serverManager.Sites[0]; 
                    var binding = site.Bindings.Add(“:443:www.test1.com”, newCert.GetCertHash(), “My”); 
                    binding.SetAttributeValue(“sslFlags”, 1); //enables the SNI 
                    serverManager.CommitChanges(); 
    //</code snip> 
    
Usando qualquer uma das abordagens acima, os respectivos certificados (*.pfx) para os nomes do host específicos precisam ser instaladas primeiro nas instâncias de função usando uma tarefa de inicialização ou por meio de código para que a associação de SNI entre em vigor.

## <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Como adicionar marcas ao meu Serviço de Nuvem do Azure? 

O Serviço de Nuvem é um recurso clássico. Somente os recursos criados por meio do Azure Resource Manager oferecem suporte a marcas. Não é possível aplicar marcas a recursos clássicos como o Serviço de Nuvem. 

## <a name="how-to-enable-http2-on-cloud-services-vm"></a>Como habilitar o HTTP/2 na VM dos Serviços de Nuvem?

O Windows 10 e Windows Server 2016 vêm com suporte para HTTP/2 no lado do cliente e do servidor. Se seu cliente (navegador) estiver se conectando ao servidor do IIS sobre TLS que negocia HTTP/2 por meio de extensões do TLS, então não será necessário fazer nenhuma alteração no lado do servidor. Isso ocorre porque, por TLS, o cabeçalho h2-14 que especifica o uso de HTTP/2 é enviado por padrão. Se, por outro lado, o cliente estiver enviando um cabeçalho de atualização a ser atualizado para HTTP/2, será necessário fazer a alteração abaixo no lado do servidor para garantir que a atualização funcione e que você termine com uma conexão HTTP/2. 

1. Execute regedit.exe.
2. Navegue até a chave do Registro: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Crie um novo valor DWORD chamado **DuoEnabled**.
4. Defina seu valor como 1.
5. Reinicie seu servidor.
6. Vá para o **Site da Web padrão** e, em **Associações**, crie uma nova associação TLS com o certificado autoassinado recém-criado. 

Para obter mais informações, consulte:

- [HTTP/2 no IIS](https://blogs.iis.net/davidso/http2)
- [Vídeo: HTTP/2 no Windows 10: navegador, aplicativos e servidor Web](https://channel9.msdn.com/Events/Build/2015/3-88)
         

Observe que as etapas acima poderiam ser automatizadas por meio de uma tarefa de inicialização. Assim, sempre que uma nova instância PaaS for criada, ela poder á fazer as alterações acima no Registro do sistema. Para obter mais informações, consulte [Como configurar e executar tarefas de inicialização para um serviço de nuvem](cloud-services-startup-tasks.md).

 
Quando isso tiver sido feito, será possível verificar se o HTTP/2 foi habilitado ou não usando um dos métodos a seguir:

- Habilite a versão do protocolo nos logs do IIS e examine os logs do IIS. Ela mostrará HTTP/2 nos logs. 
- Habilite a Ferramenta do desenvolvedor F12 no Internet Explorer/Edge e mude para a guia Rede para verificar o protocolo. 

Para obter mais informações, consulte [HTTP/2 on IIS](https://blogs.iis.net/davidso/http2) (HTTP/2 no IIS).