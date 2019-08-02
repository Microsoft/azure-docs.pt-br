---
title: Def. dos Serviços de Nuvem do Azure Esquema WebRole | Microsoft Docs
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 85368e4e-a0db-4c02-8dbc-8e2928fa6091
caps.latest.revision: 60
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: 0bb0946ea48a4c206d6bfe683da0835aca9b198b
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "60613237"
---
# <a name="azure-cloud-services-definition-webrole-schema"></a>Esquema de definição de WebRole dos Serviços de Nuvem do Azure
A função web do Azure é uma função personalizada para programação de aplicativo Web conforme suporte do IIS 7, como ASP.NET, PHP, Windows Communication Foundation e FastCGI.

A extensão padrão do arquivo de definição de serviço é .csdef.

## <a name="basic-service-definition-schema-for-a-web-role"></a>Esquema básico de definição de serviço para uma função web  
O formato básico de um arquivo de definição de serviço que contém uma função web é o seguinte.

```xml
<ServiceDefinition …>  
  <WebRole name="<web-role-name>" vmsize="<web-role-size>" enableNativeCodeExecution="[true|false]">  
    <Certificates>  
      <Certificate name="<certificate-name>" storeLocation="<certificate-store>" storeName="<store-name>" />  
    </Certificates>      
    <ConfigurationSettings>  
      <Setting name="<setting-name>" />  
    </ConfigurationSettings>  
    <Imports>  
      <Import moduleName="<import-module>"/>  
    </Imports>  
    <Endpoints>  
      <InputEndpoint certificate="<certificate-name>" ignoreRoleInstanceStatus="[true|false]" name="<input-endpoint-name>" protocol="[http|https|tcp|udp]" localPort="<port-number>" port="<port-number>" loadBalancerProbe="<load-balancer-probe-name>" />  
      <InternalEndpoint name="<internal-endpoint-name>" protocol="[http|tcp|udp|any]" port="<port-number>">  
         <FixedPort port="<port-number>"/>  
         <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>  
      </InternalEndpoint>  
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">  
         <AllocatePublicPortFrom>  
            <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>  
         </AllocatePublicPortFrom>  
      </InstanceInputEndpoint>  
    </Endpoints>  
    <LocalResources>  
      <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />  
    </LocalResources>  
    <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />  
    <Runtime executionContext="[limited|elevated]">  
      <Environment>  
         <Variable name="<variable-name>" value="<variable-value>">  
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>  
          </Variable>            
      </Environment>  
      <EntryPoint>  
         <NetFxEntryPoint assemblyName="<name-of-assembly-containing-entrypoint>" targetFrameworkVersion="<.net-framework-version>"/>  
      </EntryPoint>  
    </Runtime>  
    <Sites>  
      <Site name="<web-site-name>">  
        <VirtualApplication name="<application-name>" physicalDirectory="<directory-path>"/>  
        <VirtualDirectory name="<directory-path>" physicalDirectory="<directory-path>"/>  
        <Bindings>  
          <Binding name="<binding-name>" endpointName="<endpoint-name-bound-to>" hostHeader="<url-of-the-site>"/>  
        </Bindings>  
      </Site>  
    </Sites>  
    <Startup priority="<for-internal-use-only>">  
      <Task commandLine="<command-to=execute>" executionContext="[limited|elevated]" taskType="[simple|foreground|background]">  
        <Environment>  
         <Variable name="<variable-name>" value="<variable-value>">  
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>  
          </Variable>            
        </Environment>  
      </Task>  
    </Startup>  
    <Contents>  
      <Content destination="<destination-folder-name>" >  
        <SourceDirectory path="<local-source-directory>" />  
      </Content>  
    </Contents>  
  </WebRole>  
</ServiceDefinition>  
```  

## <a name="schema-elements"></a>Elementos de esquema  
O arquivo de definição de serviço inclui estes elementos, descritos em detalhes nas seções subsequentes neste tópico:  

[WebRole](#WebRole)

[ConfigurationSettings](#ConfigurationSettings)

[Configuração](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[Pontos de extremidade](#Endpoints)

[InternalEndpoint](#InternalEndpoint)

[InstanceInputEndpoint](#InstanceInputEndpoint)

[AllocatePublicPortFrom](#AllocatePublicPortFrom)

[FixedPort](#FixedPort)

[FixedPortRange](#FixedPortRange)

[Certificados](#Certificates)

[Certificate](#Certificate)

[Imports](#Imports)

[Importarar](#Import)

[Tempo de execução](#Runtime)

[Ambiente](#Environment)

[Variable](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[NetFxEntryPoint](#NetFxEntryPoint)

[Sites](#Sites)

[Site](#Site)

[VirtualApplication](#VirtualApplication)

[VirtualApplication](#VirtualApplication)

[Bindings](#Bindings)

[Binding](#Binding)

[Inicialização](#Startup)

[Tarefa](#Task)

[Conteúdo](#Contents)

[Conteúdo](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="WebRole"></a> WebRole  
O elemento `WebRole` descreve uma função que é personalizada para programação de aplicativo Web, conforme o suporte do IIS 7 e ASP.NET. Um serviço pode conter zero ou mais funções web.

A tabela a seguir descreve os atributos do elemento `WebRole`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|name|cadeia de caracteres|Obrigatória. O nome para a função web. O nome da função deve ser exclusivo.|  
|enableNativeCodeExecution|boolean|Opcional. O valor padrão é `true`; a execução de código nativo e a confiança total são habilitadas por padrão. Defina este atributo como `false` para desabilitar a execução de código nativo para a função web e usar a confiança parcial do Azure em vez da confiança total.|  
|vmsize|cadeia de caracteres|Opcional. Defina esse valor para alterar o tamanho da máquina virtual que está alocada para a função. O valor padrão é `Small`. Para obter mais informações, consulte [Tamanhos de máquina virtual para os Serviços de Nuvem](cloud-services-sizes-specs.md).|  

##  <a name="ConfigurationSettings"></a> ConfigurationSettings  
O elemento `ConfigurationSettings` descreve a coleção de definições de configuração para uma função web. Esse elemento é o pai do elemento `Setting`.

##  <a name="Setting"></a> Setting  
O elemento `Setting` descreve um par de nome e valor que especifica uma definição de configuração para uma instância de uma função.

A tabela a seguir descreve os atributos do elemento `Setting`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|name|cadeia de caracteres|Obrigatória. Um nome exclusivo para a definição de configuração.|  

As definições de configuração para uma função são pares de nome e valor declarados no arquivo de definição de serviço e definidos no arquivo de configuração de serviço.

##  <a name="LocalResources"></a> LocalResources  
O elemento `LocalResources` descreve a coleção de recursos de armazenamento local para uma função web. Esse elemento é o pai do elemento `LocalStorage`.

##  <a name="LocalStorage"></a> LocalStorage  
O elemento `LocalStorage` identifica um recurso de armazenamento local que fornece espaço do sistema de arquivos para o serviço em tempo de execução. Uma função pode definir zero ou mais recursos de armazenamento local.

> [!NOTE]
>  O elemento `LocalStorage` pode ser exibido como um filho do elemento `WebRole` para dar suporte à compatibilidade com versões anteriores do SDK do Azure.

A tabela a seguir descreve os atributos do elemento `LocalStorage`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|name|cadeia de caracteres|Obrigatória. Um nome exclusivo para o repositório local.|  
|cleanOnRoleRecycle|boolean|Opcional. Indica se o repositório local deve ser limpo quando a função é reiniciada. O valor padrão é `true`.|  
|sizeInMb|int|Opcional. A quantidade desejada de espaço de armazenamento a ser alocado para o repositório local, em MB. Se não estiver especificada, o espaço de armazenamento padrão alocado será 100 MB. A quantidade mínima de espaço de armazenamento que pode ser alocada é 1 MB.<br /><br /> O tamanho máximo dos recursos locais depende do tamanho da máquina virtual. Para obter mais informações, consulte [Tamanhos de máquina virtual para os Serviços de Nuvem](cloud-services-sizes-specs.md).|  
  
O nome do diretório alocado ao recurso de armazenamento local corresponde ao valor fornecido para o atributo de nome.

##  <a name="Endpoints"></a> Endpoints  
O elemento `Endpoints` descreve a coleção de pontos de extremidade de entrada (externo), interno e de entrada da instância para uma função. Esse elemento é o pai dos elementos `InputEndpoint`, `InternalEndpoint` e `InstanceInputEndpoint`.

Os pontos de extremidade de entrada e internos são alocados separadamente. Um serviço pode ter, no total, 25 pontos de extremidade de entrada, internos e de entrada de instância que podem ser alocados entre as 25 funções permitidas em um serviço. Por exemplo, se houver 5 funções, será possível alocar 5 pontos de extremidade de entrada por função ou 25 pontos de extremidade de entrada a uma única função ou 1 ponto de extremidade de entrada, cada um, a 25 funções.

> [!NOTE]
>  Cada função implantada requer uma instância por função. O provisionamento padrão para uma assinatura é limitado a 20 núcleos e, portanto, é limitado a 20 instâncias de uma função. Se seu aplicativo exigir mais instâncias do que é fornecido pelo provisionamento padrão, consulte [Billing, Subscription Management and Quota Support](https://azure.microsoft.com/support/options/) (Cobrança, Gerenciamento de assinatura e Suporte de cota) para obter mais informações sobre como aumentar a cota.

##  <a name="InputEndpoint"></a> InputEndpoint  
O elemento `InputEndpoint` descreve um ponto de extremidade externo para uma função web.

É possível definir vários pontos de extremidade que são uma combinação de pontos de extremidade HTTP, HTTPS, UDP e TCP. É possível especificar qualquer número da porta escolhido para um ponto de extremidade de entrada, mas os números da porta especificados para cada função no serviço devem ser exclusivos. Por exemplo, se você especificar que uma função web usa a porta 80 para HTTP e a porta 443 para HTTPS, você poderá, então, especificar que uma segunda função web usa a porta 8080 para HTTP e a porta 8043 para HTTPS.

A tabela a seguir descreve os atributos do elemento `InputEndpoint`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|name|cadeia de caracteres|Obrigatória. Um nome exclusivo para o ponto de extremidade externo.|  
|protocolo|cadeia de caracteres|Obrigatória. O protocolo de transporte para o ponto de extremidade externo. Para uma função web, os valores possíveis são `HTTP`, `HTTPS`, `UDP` ou `TCP`.|  
|port|int|Obrigatória. A porta do ponto de extremidade externo. É possível especificar qualquer número da porta escolhido, mas os números da porta especificados para cada função no serviço devem ser exclusivos.<br /><br /> Os valores possíveis variam entre 1 e 65535, inclusive (SDK do Azure versão 1.7 ou superior).|  
|certificado|cadeia de caracteres|Obrigatório para um ponto de extremidade HTTPS. O nome de um certificado definido por um elemento `Certificate`.|  
|localPort|int|Opcional. Especifica uma porta usada para conexões internas no ponto de extremidade. O atributo `localPort` mapeia a porta externa no ponto de extremidade para uma porta interna em uma função. Isso é útil em cenários em que uma função deve se comunicar com um componente interno em uma porta diferente da que está exposta externamente.<br /><br /> Se não estiver especificado, o valor de `localPort` será o mesmo que o do atributo `port`. Defina o valor de `localPort` como "*" para atribuir automaticamente uma porta não alocada que pode ser descoberta usando a API de tempo de execução.<br /><br /> Os valores possíveis variam entre 1 e 65535, inclusive (SDK do Azure versão 1.7 ou superior).<br /><br /> O atributo `localPort` só está disponível usando o SDK do Azure versão 1.3 ou superior.|  
|ignoreRoleInstanceStatus|boolean|Opcional. Quando o valor deste atributo é definido como `true`, o status de um serviço é ignorado e o ponto de extremidade não será removido pelo balanceador de carga. Definir esse valor como `true` é útil para depurar instâncias ocupadas de um serviço. O valor padrão é `false`. **Observação:**  Um ponto de extremidade ainda poderá receber tráfego mesmo quando a função não estiver em um estado Pronto.|  
|loadBalancerProbe|cadeia de caracteres|Opcional. O nome da sonda do balanceador de carga associada ao ponto de extremidade de entrada. Para obter mais informações, consulte o [LoadBalancerProbe Schema](schema-csdef-loadbalancerprobe.md) (Esquema LoadBalancerProbe).|  

##  <a name="InternalEndpoint"></a> InternalEndpoint  
O elemento `InternalEndpoint` descreve um ponto de extremidade interno para uma função web. Um ponto de extremidade interno está disponível somente para outras instâncias de função em execução dentro do serviço; ele não está disponível para clientes fora do serviço. Funções web que não incluem o elemento `Sites` podem ter apenas um único ponto de extremidade interno HTTP, UDP ou TCP.

A tabela a seguir descreve os atributos do elemento `InternalEndpoint`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|name|cadeia de caracteres|Obrigatória. Um nome exclusivo para o ponto de extremidade interno.|  
|protocolo|cadeia de caracteres|Obrigatória. O protocolo de transporte para o ponto de extremidade interno. Os valores possíveis são `HTTP`, `TCP`, `UDP` ou `ANY`.<br /><br /> Um valor de `ANY` especifica que qualquer protocolo, qualquer porta é permitida.|  
|port|int|Opcional. A porta usada para conexões com balanceamento de carga interno no ponto de extremidade. Um ponto de extremidade com balanceamento de carga usa duas portas. A porta usada para o endereço IP público e a porta usada no endereço IP privado. Normalmente, elas são definidas como a mesma coisa, mas você pode optar por usar portas diferentes.<br /><br /> Os valores possíveis variam entre 1 e 65535, inclusive (SDK do Azure versão 1.7 ou superior).<br /><br /> O atributo `Port` só está disponível usando o SDK do Azure versão 1.3 ou superior.|  

##  <a name="InstanceInputEndpoint"></a> InstanceInputEndpoint  
O elemento `InstanceInputEndpoint` descreve um ponto de extremidade de entrada de instância para uma função web. Um ponto de extremidade de entrada de instância está associado a uma instância de função específica usando o encaminhamento de porta no balanceador de carga. Cada ponto de extremidade de entrada de instância é mapeado para uma porta específica de um intervalo de portas possíveis. Esse elemento é o pai do elemento `AllocatePublicPortFrom`.

O elemento `InstanceInputEndpoint` só está disponível usando o SDK do Azure versão 1.7 ou superior.

A tabela a seguir descreve os atributos do elemento `InstanceInputEndpoint`.
  
| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|name|cadeia de caracteres|Obrigatória. Um nome exclusivo para o ponto de extremidade.|  
|localPort|int|Obrigatória. Especifica a porta interna que todas as instâncias de função escutarão a fim de receber tráfego de entrada encaminhado do balanceador de carga. Os valores possíveis variam entre 1 e 65535, inclusive.|  
|protocolo|cadeia de caracteres|Obrigatória. O protocolo de transporte para o ponto de extremidade interno. Os valores possíveis são `udp` ou `tcp`. Use `tcp` para tráfego baseado em http/https.|  
  
##  <a name="AllocatePublicPortFrom"></a> AllocatePublicPortFrom  
O elemento `AllocatePublicPortFrom` descreve o intervalo de porta pública que pode ser usado por clientes externos para acessar cada ponto de extremidade de entrada de instância. O número da porta pública (VIP) é alocado desse intervalo e atribuído a cada ponto de extremidade de instância de função individual durante a implantação e a atualização do locatário. Esse elemento é o pai do elemento `FixedPortRange`.

O elemento `AllocatePublicPortFrom` só está disponível usando o SDK do Azure versão 1.7 ou superior.

##  <a name="FixedPort"></a> FixedPort  
O elemento `FixedPort` especifica a porta para o ponto de extremidade interno, que permite conexões com balanceamento de carga no ponto de extremidade.

O elemento `FixedPort` só está disponível usando o SDK do Azure versão 1.3 ou superior.

A tabela a seguir descreve os atributos do elemento `FixedPort`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|port|int|Obrigatória. A porta do ponto de extremidade interno. Isso tem o mesmo efeito que definir o `FixedPortRange` mín. e máx. para a mesma porta.<br /><br /> Os valores possíveis variam entre 1 e 65535, inclusive (SDK do Azure versão 1.7 ou superior).|  

##  <a name="FixedPortRange"></a> FixedPortRange  
O elemento `FixedPortRange` especifica o intervalo de portas atribuído ao ponto de extremidade interno ou de entrada de instância e define a porta usada para conexões com balanceamento de carga no ponto de extremidade.

> [!NOTE]
>  O elemento `FixedPortRange` funciona de maneira diferente dependendo do elemento no qual ele reside. Quando o elemento `FixedPortRange` está no elemento `InternalEndpoint`, ele abre todas as portas no balanceador de carga dentro do intervalo dos atributos mín. e máx. para todas as máquinas virtuais em que a função é executada. Quando o elemento `FixedPortRange` está no elemento `InstanceInputEndpoint`, ele abre apenas uma porta dentro do intervalo dos atributos mín. e máx. em cada máquina virtual que executa a função.

O elemento `FixedPortRange` só está disponível usando o SDK do Azure versão 1.3 ou superior.

A tabela a seguir descreve os atributos do elemento `FixedPortRange`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|min|int|Obrigatória. A porta mínima no intervalo. Os valores possíveis variam entre 1 e 65535, inclusive (SDK do Azure versão 1.7 ou superior).|  
|máx.|cadeia de caracteres|Obrigatória. A porta máxima no intervalo. Os valores possíveis variam entre 1 e 65535, inclusive (SDK do Azure versão 1.7 ou superior).|  

##  <a name="Certificates"></a> Certificates  
O elemento `Certificates` descreve a coleção de certificados para uma função web. Esse elemento é o pai do elemento `Certificate`. Uma função pode ter qualquer número de certificados associados. Para obter mais informações sobre o uso do elemento certificates, consulte [Modify the Service Definition file with a certificate](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files) (Modificar o arquivo de definição de serviço com um certificado).

##  <a name="Certificate"></a> Certificate  
O elemento `Certificate` descreve um certificado associado a uma função web.

A tabela a seguir descreve os atributos do elemento `Certificate`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|name|cadeia de caracteres|Obrigatória. Um nome para esse certificado, usado para se referir a ele quando ele é associado a um elemento `InputEndpoint` de HTTPS.|  
|storeLocation|cadeia de caracteres|Obrigatória. O local do repositório de certificados em que esse certificado pode ser encontrado no computador local. Os valores possíveis são `CurrentUser` e `LocalMachine`.|  
|storeName|cadeia de caracteres|Obrigatória. O nome do repositório de certificados em que esse certificado reside no computador local. Os valores possíveis incluem os nomes de repositório interno `My`, `Root`, `CA`, `Trust`, `Disallowed`, `TrustedPeople`, `TrustedPublisher`, `AuthRoot`, `AddressBook` ou qualquer nome de repositório personalizado. Se um nome de repositório personalizado for especificado, ele será criado automaticamente.|  
|permissionLevel|cadeia de caracteres|Opcional. Especifica as permissões de acesso fornecidas aos processos de função. Se você desejar que apenas processos com privilégios elevados possam acessar a chave privada, especifique a permissão `elevated`. A permissão `limitedOrElevated` permite que todos os processos de função acessem a chave privada. Os valores possíveis são `limitedOrElevated` ou `elevated`. O valor padrão é `limitedOrElevated`.|  

##  <a name="Imports"></a> Imports  
O elemento `Imports` descreve uma coleção de módulos de importação para uma função web que adicionam componentes ao sistema operacional convidado. Esse elemento é o pai do elemento `Import`. Esse elemento é opcional e uma função pode ter apenas um bloco de importações. 

O elemento `Imports` só está disponível usando o SDK do Azure versão 1.3 ou superior.

##  <a name="Import"></a> Import  
O elemento `Import` especifica um módulo a ser adicionado ao sistema operacional convidado.

O elemento `Import` só está disponível usando o SDK do Azure versão 1.3 ou superior.

A tabela a seguir descreve os atributos do elemento `Import`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|moduleName|cadeia de caracteres|Obrigatória. O nome do módulo a ser importado. Os módulos de importação válidos são:<br /><br /> -   RemoteAccess<br />-   RemoteForwarder<br />-   Diagnostics<br /><br /> Os módulos RemoteAccess e RemoteForwarder permitem que você configure sua instância de função para conexões de área de trabalho remota. Para obter mais informações, consulte [Enable Remote Desktop Connection](cloud-services-role-enable-remote-desktop-new-portal.md) (Habilitar Conexão de Área de Trabalho Remota).<br /><br /> O módulo Diagnostics permite coletar dados de diagnóstico para uma instância de função.|  

##  <a name="Runtime"></a> Runtime  
O elemento `Runtime` descreve uma coleção de configurações de variável de ambiente para uma função web que controlam o ambiente de tempo de execução do processo de host do Azure. Esse elemento é o pai do elemento `Environment`. Esse elemento é opcional e uma função pode ter apenas um bloco de tempo de execução.

O elemento `Runtime` só está disponível usando o SDK do Azure versão 1.3 ou superior.

A tabela a seguir descreve os atributos do elemento `Runtime`:  

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|executionContext|cadeia de caracteres|Opcional. Especifica o contexto no qual o Processo de função é iniciado. O contexto padrão é `limited`.<br /><br /> -   `limited` – O processo é iniciado sem privilégios de administrador.<br />-   `elevated` – O processo é iniciado com privilégios de administrador.|  

##  <a name="Environment"></a> Environment  
O elemento `Environment` descreve uma coleção de configurações de variável de ambiente para uma função web. Esse elemento é o pai do elemento `Variable`. Uma função pode ter qualquer número de conjunto de variáveis de ambiente.

##  <a name="Variable"></a> Variable  
O elemento `Variable` especifica uma variável de ambiente a ser definida no sistema operacional convidado.

O elemento `Variable` só está disponível usando o SDK do Azure versão 1.3 ou superior.

A tabela a seguir descreve os atributos do elemento `Variable`:  

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|name|cadeia de caracteres|Obrigatória. O nome da variável de ambiente a ser definida.|  
|value|cadeia de caracteres|Opcional. O valor a ser definido para a variável de ambiente. É necessário incluir um atributo de valor ou um elemento `RoleInstanceValue`.|  

##  <a name="RoleInstanceValue"></a> RoleInstanceValue  
O elemento `RoleInstanceValue` especifica o xPath do qual recuperar o valor da variável.

A tabela a seguir descreve os atributos do elemento `RoleInstanceValue`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|xpath|cadeia de caracteres|Opcional. Caminho do local de configurações de implantação para a instância. Para obter mais informações, consulte [Variáveis de configuração com o XPath](cloud-services-role-config-xpath.md).<br /><br /> É necessário incluir um atributo de valor ou um elemento `RoleInstanceValue`.|  

##  <a name="EntryPoint"></a> EntryPoint  
O elemento `EntryPoint` especifica o ponto de entrada para uma função. Esse elemento é o pai dos elementos `NetFxEntryPoint`. Esses elementos permitem que você especifique um aplicativo diferente do WaWorkerHost.exe padrão para atuar como o ponto de entrada da função.

O elemento `EntryPoint` só está disponível usando o SDK do Azure versão 1.5 ou superior.

##  <a name="NetFxEntryPoint"></a> NetFxEntryPoint  
O elemento `NetFxEntryPoint` especifica o programa a ser executado para uma função.

> [!NOTE]
>  O elemento `NetFxEntryPoint` só está disponível usando o SDK do Azure versão 1.5 ou superior.

A tabela a seguir descreve os atributos do elemento `NetFxEntryPoint`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|assemblyName|cadeia de caracteres|Obrigatória. O caminho e o nome do arquivo do assembly que contém o ponto de entrada. O caminho é relativo à pasta **\\%ROLEROOT%\Approot** (não especifique **\\%ROLEROOT%\Approot** no `commandLine`, pois já é presumido). **%ROLEROOT%** é uma variável de ambiente mantida pelo Azure e ela representa o local da pasta raiz da sua função. A pasta **\\%ROLEROOT%\Approot** representa a pasta do aplicativo para sua função.<br /><br /> Para funções HWC o caminho sempre é relativo à pasta **\\%ROLEROOT%\Approot\bin**.<br /><br /> Para funções web completas IIS e IIS Express, se o assembly não puder ser encontrado em relação à pasta **\\%ROLEROOT%\Approot**, a pasta **\\%ROLEROOT%\Approot\bin** será pesquisada.<br /><br /> Esse comportamento de fallback para IIS completo não é uma melhor prática recomendada e poderá ser removido em futuras versões.|  
|targetFrameworkVersion|cadeia de caracteres|Obrigatória. A versão do .NET Framework na qual esse assembly foi criado. Por exemplo, `targetFrameworkVersion="v4.0"`.|  

##  <a name="Sites"></a> Sites  
O elemento `Sites` descreve uma coleção de sites e aplicativos Web hospedados em uma função web. Esse elemento é o pai do elemento `Site`. Se você não especificar um elemento `Sites`, sua função web será hospedada como função web herdada e você só poderá ter um site hospedado em sua função web. Esse elemento é opcional e uma função pode ter apenas um bloco de sites.

O elemento `Sites` só está disponível usando o SDK do Azure versão 1.3 ou superior.

##  <a name="Site"></a> Site  
O elemento `Site` especifica um site ou aplicativo Web que faz parte da função web.

O elemento `Site` só está disponível usando o SDK do Azure versão 1.3 ou superior.

A tabela a seguir descreve os atributos do elemento `Site`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|name|cadeia de caracteres|Obrigatória. Nome do site ou aplicativo.|  
|physicalDirectory|cadeia de caracteres|O local do diretório de conteúdo para a raiz do site. O local pode ser especificado como um caminho absoluto ou relativo ao local do .csdef.|  

##  <a name="VirtualApplication"></a> VirtualApplication  
O elemento `VirtualApplication` define que um aplicativo em IIS (Serviços de Informações da Internet) 7 é um agrupamento de arquivos que entrega conteúdo ou fornece serviços através de protocolos, como HTTP. Quando você cria um aplicativo em IIS 7, o caminho do aplicativo se torna parte da URL do site.

O elemento `VirtualApplication` só está disponível usando o SDK do Azure versão 1.3 ou superior.

A tabela a seguir descreve os atributos do elemento `VirtualApplication`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|name|cadeia de caracteres|Obrigatória. Especifica um nome para identificar o aplicativo virtual.|  
|physicalDirectory|cadeia de caracteres|Obrigatória. Especifica o caminho no computador de desenvolvimento que contém o aplicativo virtual. No emulador de computação, o IIS é configurado para recuperar conteúdo deste local. Ao implantar no Azure, o conteúdo do diretório físico é empacotado junto com o restante do serviço. Quando o pacote do serviço é implantado no Azure, o IIS é configurado com o local do conteúdo descompactado.|  

##  <a name="VirtualDirectory"></a> VirtualDirectory  
O elemento `VirtualDirectory` especifica um nome de diretório (também chamado de caminho) que você especifica no IIS e mapeia para um diretório físico em um servidor local ou remoto.

O elemento `VirtualDirectory` só está disponível usando o SDK do Azure versão 1.3 ou superior.

A tabela a seguir descreve os atributos do elemento `VirtualDirectory`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|name|cadeia de caracteres|Obrigatória. Especifica um nome para identificar o diretório virtual.|  
|value|physicalDirectory|Obrigatória. Especifica o caminho no computador de desenvolvimento que contém o site ou o conteúdo do diretório virtual. No emulador de computação, o IIS é configurado para recuperar conteúdo deste local. Ao implantar no Azure, o conteúdo do diretório físico é empacotado junto com o restante do serviço. Quando o pacote do serviço é implantado no Azure, o IIS é configurado com o local do conteúdo descompactado.|  

##  <a name="Bindings"></a> Bindings  
O elemento `Bindings` descreve uma coleção de associações para um site. Ele é o elemento pai do elemento `Binding`. O elemento é necessário para cada elemento `Site`. Para obter mais informações sobre como configurar pontos de extremidade, consulte [Habilitar comunicação para instâncias de função](cloud-services-enable-communication-role-instances.md).

O elemento `Bindings` só está disponível usando o SDK do Azure versão 1.3 ou superior.

##  <a name="Binding"></a> Binding  
O elemento `Binding` especifica informações de configuração necessárias para solicitações de comunicação com um site ou aplicativo Web.

O elemento `Binding` só está disponível usando o SDK do Azure versão 1.3 ou superior.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|name|cadeia de caracteres|Obrigatória. Especifica um nome para identificar a associação.|  
|endpointName|cadeia de caracteres|Obrigatória. Especifica o nome do ponto de extremidade ao qual se associar.|  
|hostHeader|cadeia de caracteres|Opcional. Especifica um nome de host que permite hospedar vários sites, com nomes de host diferentes, em uma única combinação de Endereço IP/Número da porta.|  

##  <a name="Startup"></a> Startup  
O elemento `Startup` descreve uma coleção de tarefas que são executadas quando a função é iniciada. Esse elemento pode ser o pai do elemento `Variable`. Para obter mais informações sobre como usar as tarefas de inicialização de função, consulte [How to configure startup tasks](cloud-services-startup-tasks.md) (Como configurar tarefas de inicialização). Esse elemento é opcional e uma função pode ter apenas um bloco de inicialização.

A tabela a seguir descreve o atributo do elemento `Startup`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|prioridade|int|Apenas para uso interno.|  

##  <a name="Task"></a> Task  
O elemento `Task` especifica a tarefa de inicialização que ocorre quando a função é iniciada. As tarefas de inicialização podem ser usadas para executar tarefas que preparam a função a ser executada, como instalar componentes de software ou executar outros aplicativos. As tarefas são executadas na ordem em que aparecem dentro do bloco de elemento `Startup`.

O elemento `Task` só está disponível usando o SDK do Azure versão 1.3 ou superior.

A tabela a seguir descreve os atributos do elemento `Task`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|commandLine|cadeia de caracteres|Obrigatória. Um script, como um arquivo CMD, que contém os comandos a serem executados. O comando de inicialização e os arquivos de lote devem ser salvos no formato ANSI. Os formatos de arquivo que definem um marcador de ordem de byte no início do arquivo não serão processados corretamente.|  
|executionContext|cadeia de caracteres|Especifica o contexto no qual o script é executado.<br /><br /> -   `limited` [Padrão] – Executa com os mesmos privilégios que a função que hospeda o processo.<br />-   `elevated` – Executa com privilégios de administrador.|  
|taskType|cadeia de caracteres|Especifica o comportamento de execução do comando.<br /><br /> -   `simple` [Padrão] – O sistema aguarda a tarefa ser encerrada antes de iniciar outras tarefas.<br />-   `background` – O sistema não aguarda a tarefa ser encerrada.<br />-   `foreground` – Semelhante à tela de fundo, a função de exceção não é reiniciada até que todas as tarefas em primeiro plano sejam encerradas.|  

##  <a name="Contents"></a> Contents  
O elemento `Contents` descreve a coleção de conteúdo para uma função web. Esse elemento é o pai do elemento `Content`.

O elemento `Contents` só está disponível usando o SDK do Azure versão 1.5 ou superior.

##  <a name="Content"></a> Content  
O elemento `Content` define o local de origem do conteúdo a ser copiado para a máquina virtual do Azure e o caminho de destino para o qual ele é copiado.

O elemento `Content` só está disponível usando o SDK do Azure versão 1.5 ou superior.

A tabela a seguir descreve os atributos do elemento `Content`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|destination|cadeia de caracteres|Obrigatória. Local na máquina virtual do Azure no qual o conteúdo é colocado. Esse local é relativo à pasta **%ROLEROOT%\Approot**.|  

Esse elemento é o pai do elemento `SourceDirectory`.

##  <a name="SourceDirectory"></a> SourceDirectory  
O elemento `SourceDirectory` define o diretório local do qual o conteúdo é copiado. Use esse elemento para especificar o conteúdo local a ser copiado para a máquina virtual do Azure.

O elemento `SourceDirectory` só está disponível usando o SDK do Azure versão 1.5 ou superior.

A tabela a seguir descreve os atributos do elemento `SourceDirectory`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|path|cadeia de caracteres|Obrigatória. Caminho relativo ou absoluto de um diretório local cujo conteúdo será copiado para a máquina virtual do Azure. Há suporte para a expansão de variáveis de ambiente no caminho de diretório.|  
  
## <a name="see-also"></a>Consulte também
[Esquema de definição do Serviço de Nuvem (clássico)](schema-csdef-file.md)
