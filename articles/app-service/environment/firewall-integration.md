---
title: Bloquear o tráfego de saída
description: Saiba como fazer a integração ao Firewall do Azure para proteger o tráfego de saída em um Ambiente do Serviço de Aplicativo.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 09/24/2020
ms.author: ccompy
ms.custom: seodec18, references_regions
ms.openlocfilehash: ec506546b52a2d137d448f07f4b7a6827c01b4d2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594125"
---
# <a name="locking-down-an-app-service-environment"></a>Bloqueando um Ambiente do Serviço de Aplicativo

O ASE (Ambiente de Serviço de Aplicativo) tem diversas dependências externas às quais ele requer acesso para funcionar corretamente. O ASE reside na VNet (rede virtual) do Azure do cliente. Os clientes devem permitir o tráfego de dependência do ASE, que é um problema para os clientes que desejam bloquear toda a saída da VNet.

Há vários pontos de extremidade de entrada que são usados para gerenciar um ASE. O tráfego de gerenciamento de entrada não pode ser enviado por meio de um dispositivo de firewall. Os endereços de origem para esse tráfego são conhecidos e publicados no documento [Endereços de gerenciamento do Ambiente do Serviço de Aplicativo](./management-addresses.md). Também há uma Marca de Serviço chamada AppServiceManagement que pode ser usada com NSGs (grupos de segurança de rede) para proteger o tráfego de entrada.

As dependências de saída do ASE são quase que totalmente definidas com FQDNs, que não têm endereços estáticos por trás delas. A falta de endereços estáticos significa que os grupos de segurança de rede não podem ser usados para bloquear o tráfego de saída por meio de um ASE. Os endereços mudam com frequência suficiente para que não seja possível configurar regras com base na resolução atual e usá-las para criar NSGs. 

A solução para proteger os endereços de saída está em usar um dispositivo de firewall que possa controlar o tráfego de saída com base em nomes de domínio. Firewall do Azure pode restringir o tráfego de HTTP e HTTPS de saída com base no FQDN de destino.  

## <a name="system-architecture"></a>Arquitetura do sistema

A implantação de um ASE com o tráfego de saída passando por um dispositivo de firewall exige a alteração das rotas na sub-rede do ASE. As rotas operam em um nível de IP. Se você não tiver cuidado ao definir as rotas, poderá forçar o tráfego de resposta TCP para a origem de outro endereço. Quando o endereço de resposta for diferente do endereço do qual o tráfego foi enviado, o problema será chamado de roteamento assimétrico e interromperá o TCP.

É preciso haver rotas definidas, de modo que o tráfego de entrada para o ASE possa responder da mesma maneira que o tráfego foi recebido. As rotas precisam ser definidas para solicitações de gerenciamento de entrada e solicitações de aplicativo de entrada.

O tráfego bidirecional em um ASE precisa obedecer às convenções a seguir

* Não há suporte para o tráfego para o SQL, o Armazenamento e o Hub de Eventos do Azure com o uso de um dispositivo de firewall. Esse tráfego precisa ser enviado diretamente para esses serviços. A maneira de fazer isso acontecer é configurar pontos de extremidade de serviço para esses três serviços. 
* As regras da tabela de rotas precisam ser definidas para enviar o tráfego de gerenciamento de entrada novamente do local de origem.
* As regras da tabela de rotas precisam ser definidas para enviar o tráfego do aplicativo de entrada novamente do local de origem. 
* Todo o outro tráfego que sai do ASE pode ser enviado ao dispositivo de firewall com uma regra de tabela de rotas.

![ASE com o fluxo de conexão do Firewall do Azure][5]

## <a name="locking-down-inbound-management-traffic"></a>Como bloquear o tráfego de gerenciamento de entrada

Se a sub-rede do ASE ainda não tiver um NSG atribuído a ela, crie um. No NSG, defina a primeira regra para permitir o tráfego da Marca de Serviço chamada AppServiceManagement nas portas 454 e 455. A regra para permitir o acesso da marca AppServiceManagement é a única coisa que é necessária para que os IPs públicos gerenciem o ASE. Os endereços que estão atrás dessa Marca de Serviço são usados apenas para administrar o Serviço de Aplicativo do Azure. O tráfego de gerenciamento que flui por essas conexões é criptografado e protegido com certificados de autenticação. O tráfego típico nesse canal inclui itens como os comandos iniciados pelo cliente e as investigações de integridade. 

Os ASEs que são criados por meio do portal com uma nova sub-rede são criados com um NSG que contém a regra de permissão para a marca AppServiceManagement.  

O ASE também precisa permitir solicitações de entrada da marca do Load Balancer na porta 16001. As solicitações do Load Balancer na porta 16001 são verificações keep alive entre o Load Balancer e os front-ends do ASE. Se a porta 16001 estiver bloqueada, o ASE entrará no estado não íntegro.

## <a name="configuring-azure-firewall-with-your-ase"></a>Como configurar o Firewall do Azure com seu ASE 

As etapas para bloquear a saída do ASE existente com o Firewall do Azure são:

1. Habilitar pontos de extremidade de serviço para o SQL, o Armazenamento e o Hub de Eventos na sub-rede do ASE. Para habilitar os pontos de extremidade de serviço, acesse o portal de rede > sub-redes e selecione Microsoft.EventHub, Microsoft.SQL e Microsoft.Storage no menu suspenso Pontos de extremidade de serviço. Quando você tiver pontos de extremidade de serviço habilitados para o SQL Azure, todas as dependências do SQL do Azure contidas nos aplicativos precisarão ser configuradas com pontos de extremidade de serviço também. 

   ![selecionar pontos de extremidade de serviço][2]
  
1. Crie uma sub-rede chamada AzureFirewallSubnet na VNET em que o ASE está localizado. Siga as instruções da [documentação do Firewall do Azure](../../firewall/index.yml) para criar seu Firewall do Azure.

1. Na interface do usuário do Firewall do Azure > Regras > Coleção de regras de aplicativo, selecione Adicionar coleção de regras de aplicativo. Forneça um nome e uma prioridade e defina Permitir. Na seção Marcas de FQDN, forneça um nome, defina os endereços de origem como * e selecione a marca FQDN do Ambiente do Serviço de Aplicativo e o Windows Update. 
   
   ![Adicionar regra de aplicativo][1]
   
1. Na interface do usuário do Firewall do Azure > Regras > Coleção de regras de rede, selecione Adicionar coleção de regras de rede. Forneça um nome e uma prioridade e defina Permitir. Na seção regras, em endereços IP, forneça um nome, selecione um protocolo de **qualquer**, defina * para endereços de origem e de destino e defina as portas como 123. Essa regra permite que o sistema execute a sincronização de relógio usando o NTP. Crie outra regra da mesma forma para a porta 12000 para ajudar na triagem dos problemas do sistema. 

   ![Adicionar regra de rede NTP][3]
   
1. Na interface do usuário do Firewall do Azure > Regras > Coleção de regras de rede, selecione Adicionar coleção de regras de rede. Forneça um nome e uma prioridade e defina Permitir. Na seção Regras em Marcas de Serviço, forneça um nome, selecione um protocolo **Qualquer um**, defina * como Endereços de origem, selecione uma marca de serviço igual a AzureMonitor e defina as portas como 80 e 443. Essa regra permite que o sistema forneça ao Azure Monitor informações de integridade e métricas.

   ![Adicionar uma regra de rede da marca de serviço NTP][6]
   
1. Criar uma tabela de rotas com os endereços de gerenciamento dos [Endereços de gerenciamento do Ambiente do Serviço de Aplicativo]( ./management-addresses.md) com um próximo salto da Internet. As entradas da tabela de rota são necessárias para evitar problemas de roteamento assimétrico. Adicione as rotas para as dependências de endereço IP indicadas abaixo nas dependências de endereço IP com um próximo salto de Internet. Adicione uma rota da Solução de Virtualização à sua tabela de rotas para 0.0.0.0/0 com o próximo nó sendo seu endereço IP privado do Firewall do Azure. 

   ![Criando uma tabela de rotas][4]
   
1. Atribua a tabela de rotas que você criou à sua sub-rede ASE.

#### <a name="deploying-your-ase-behind-a-firewall"></a>Implantando o ASE atrás de um firewall

As etapas para implantar o ASE atrás de um firewall são as mesmas da configuração do ASE existente com um Firewall do Azure, exceto que você precisará criar a sub-rede do ASE e, em seguida, seguir as etapas anteriores. Para criar o ASE em uma sub-rede pré-existente, você precisa usar um modelo do Resource Manager, conforme descrito no documento sobre como [Criar o ASE com um modelo do Resource Manager](./create-from-template.md).

## <a name="application-traffic"></a>Tráfego de aplicativo 

As etapas acima permitirão que seu ASE opere sem problemas. Você ainda precisará configurar tudo para acomodar as necessidades do seu aplicativo. Há dois problemas para aplicativos em um ASE configurado com o Firewall do Azure.  

- As dependências de aplicativo precisam ser adicionadas ao Firewall do Azure ou à tabela de rotas. 
- As rotas precisam ser criadas para o tráfego de aplicativo a fim de evitar problemas de roteamento assimétrico

Se seus aplicativos tiverem dependências, precisarão ser adicionados ao Firewall do Azure. Crie regras de aplicativo para permitir o tráfego HTTP/HTTPS e regras de Rede para todo o resto. 

Se você souber o intervalo de endereços do qual seu tráfego de solicitação de aplicativo virá, poderá adicioná-lo à tabela de rotas atribuída à sua sub-rede do ASE. Se o intervalo de endereços for grande ou não estiver especificado, você poderá usar um dispositivo de rede, como o Gateway de Aplicativo para fornecer um endereço para adicionar à sua tabela de rotas. Para obter detalhes sobre como configurar um Gateway de Aplicativo com seu ILB ASE, leia [Como integrar seu ILB ASE a um Gateway de Aplicativo](./integrate-with-application-gateway.md)

Esse uso do Gateway de Aplicativo é apenas um exemplo de como configurar o sistema. Se você seguiu esse caminho, você precisa adicionar uma rota à tabela de rotas da sub-rede do ASE, de modo que o tráfego de resposta enviado ao Gateway de Aplicativo acesse-o diretamente. 

## <a name="logging"></a>Registro em log 

O Firewall do Azure pode enviar logs para o Armazenamento do Azure, o Hub de Eventos ou os Logs do Azure Monitor. Para integrar seu aplicativo com qualquer destino compatível, acesse o portal do Firewall do Azure > Logs de Diagnóstico e habilite os logs para o destino desejado. Se você fizer a integração com os logs do Azure Monitor, poderá ver os logs de qualquer tráfego enviado para o Firewall do Azure. Para ver o tráfego que está sendo negado, abra o portal de espaços de trabalho do Log Analytics existentes &gt; Logs e insira uma consulta como 

```kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

A integração do Firewall do Azure aos logs do Azure Monitor é útil ao tornar o aplicativo funcional pela primeira vez, quando você não está ciente de todas as dependências de aplicativo. Saiba mais sobre os logs do Azure Monitor em [Analisar dados de log no Azure Monitor](../../azure-monitor/logs/log-query-overview.md).
 
## <a name="dependencies"></a>Dependências

As informações a seguir só são necessárias se você deseja configurar um dispositivo de firewall diferente do Firewall do Azure. 

- Os serviços compatíveis com o Ponto de Extremidade de Serviço devem ser configurados com pontos de extremidade de serviço.
- As dependências de Endereço IP são para o tráfego não HTTP/S (tráfego TCP e UDP)
- Pontos de extremidade HTTP/HTTPS do FQDN podem ser colocados em seu dispositivo de firewall.
- Pontos de extremidade HTTP/HTTPS curinga são dependências que podem variar de acordo com seu ASE com base em vários qualificadores. 
- As dependências do Linux serão uma preocupação apenas se você estiver implantando aplicativos Linux em seu ASE. Se você não estiver implantando aplicativos Linux em seu ASE, esses endereços não precisarão ser adicionados ao firewall. 

#### <a name="service-endpoint-capable-dependencies"></a>Dependências com capacidade de Ponto de Extremidade de Serviço 

| Ponto de extremidade |
|----------|
| SQL do Azure |
| Armazenamento do Azure |
| Hub de Eventos do Azure |

#### <a name="ip-address-dependencies"></a>Dependências de endereço IP

| Ponto de extremidade | Detalhes |
|----------| ----- |
| \*:123 | Verificação do relógio do NTP. O tráfego é verificado em vários pontos de extremidade na porta 123 |
| \*:12000 | Essa porta é usada para alguns tipos de monitoramento do sistema. Se ela estiver bloqueada, alguns problemas serão mais difíceis de serem passados pela triagem, mas o ASE continuará funcionando |
| 40.77.24.27:80 | Necessário para monitorar e alertar sobre problemas do ASE |
| 40.77.24.27:443 | Necessário para monitorar e alertar sobre problemas do ASE |
| 13.90.249.229:80 | Necessário para monitorar e alertar sobre problemas do ASE |
| 13.90.249.229:443 | Necessário para monitorar e alertar sobre problemas do ASE |
| 104.45.230.69:80 | Necessário para monitorar e alertar sobre problemas do ASE |
| 104.45.230.69:443 | Necessário para monitorar e alertar sobre problemas do ASE |
| 13.82.184.151:80 | Necessário para monitorar e alertar sobre problemas do ASE |
| 13.82.184.151:443 | Necessário para monitorar e alertar sobre problemas do ASE |

Com um Firewall do Azure, você obtém automaticamente tudo abaixo configurado com as marcas FQDN. 

#### <a name="fqdn-httphttps-dependencies"></a>Dependências de HTTP/HTTPS do FQDN 

| Ponto de extremidade |
|----------|
|graph.microsoft.com:443 |
|login.live.com:443 |
|login.windows.com:443 |
|login.windows.net:443 |
|login.microsoftonline.com:443 |
|client.wns.windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|ocsp.msocsp.com:443 |
|ocsp.msocsp.com:80 |
|oneocsp.microsoft.com:80 |
|oneocsp.microsoft.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|crl.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.thawte.com:443 |
|crl3.digicert.com:80 |
|ocsp.digicert.com:80 |
|ocsp.digicert.com:443 |
|csc3-2009-2.crl.verisign.com:80 |
|crl.verisign.com:80 |
|ocsp.verisign.com:80 |
|cacerts.digicert.com:80 |
|azperfcounters1.blob.core.windows.net:443 |
|azurewatsonanalysis-prod.core.windows.net:443 |
|global.metrics.nsatc.net:80 |
|global.metrics.nsatc.net:443 |
|az-prod.metrics.nsatc.net:443 |
|antares.metrics.nsatc.net:443 |
|azglobal-black.azglobal.metrics.nsatc.net:443 |
|azglobal-red.azglobal.metrics.nsatc.net:443 |
|antares-black.antares.metrics.nsatc.net:443 |
|antares-red.antares.metrics.nsatc.net:443 |
|prod.microsoftmetrics.com:443 |
|maupdateaccount.blob.core.windows.net:443 |
|clientconfig.passport.net:443 |
|packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|management.core.windows.net:443 |
|management.core.windows.net:80 |
|management.azure.com:443 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge.net:443 |
|validation-v2.sls.microsoft.com:443 |
|flighting.cp.wd.microsoft.com:443 |
|dmd.metaservices.microsoft.com:80 |
|admin.core.windows.net:443 |
|prod.warmpath.msftcloudes.com:443 |
|prod.warmpath.msftcloudes.com:80 |
|gcs.prod.monitoring.core.windows.net:80|
|gcs.prod.monitoring.core.windows.net:443|
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 | 
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|settings-win.data.microsoft.com:443 |
|maupdateaccount2.blob.core.windows.net:443 |
|maupdateaccount3.blob.core.windows.net:443 |
|dc.services.visualstudio.com:443 |
|gmstorageprodsn1.blob.core.windows.net:443 |
|gmstorageprodsn1.file.core.windows.net:443 |
|gmstorageprodsn1.queue.core.windows.net:443 |
|gmstorageprodsn1.table.core.windows.net:443 |
|rteventservice.trafficmanager.net:443 |
|ctldl.windowsupdate.com:80 |
|ctldl.windowsupdate.com:443 |
|global-dsms.dsms.core.windows.net:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Dependências de HTTP/HTTPS de curinga 

| Ponto de extremidade |
|----------|
|gr-Prod-\*.cloudapp.net:443 |
| \*.management.azure.com:443 |
| \*.update.microsoft.com:443 |
| \*.windowsupdate.microsoft.com:443 |
| \*.identity.azure.net:443 |
| \*.ctldl.windowsupdate.com:80 |
| \*.ctldl.windowsupdate.com:443 |

#### <a name="linux-dependencies"></a>Dependências do Linux 

| Ponto de extremidade |
|----------|
|wawsinfraprodbay063.blob.core.windows.net:443 |
|registry-1.docker.io:443 |
|auth.docker.io:443 |
|production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|us.archive.ubuntu.com:80 |
|download.mono-project.com:80 |
|packages.treasuredata.com:80|
|security.ubuntu.com:80 |
|oryx-cdn.microsoft.io:443 |
| \*.cdn.mscr.io:443 |
| \*. data.mcr.microsoft.com:443 |
|mcr.microsoft.com:443 |
|\*. data.mcr.microsoft.com:443 |
|packages.fluentbit.io:80 |
|packages.fluentbit.io:443 |
|apt-mo.trafficmanager.net:80 |
|apt-mo.trafficmanager.net:443 |
|azure.archive.ubuntu.com:80 |
|azure.archive.ubuntu.com:443 |
|changelogs.ubuntu.com:80 |
|13.74.252.37:11371 |
|13.75.127.55:11371 |
|13.76.190.189:11371 |
|13.80.10.205:11371 |
|13.91.48.226:11371 |
|40.76.35.62:11371 |
|104.215.95.108:11371 |

## <a name="us-gov-dependencies"></a>Dependências de US Gov

Para os ASEs nas regiões US Gov, siga as instruções da seção [Como configurar o Firewall do Azure com o seu ASE](#configuring-azure-firewall-with-your-ase) deste documento para configurar um Firewall do Azure com o ASE.

Caso deseje usar um dispositivo que não seja o Firewall do Azure em US Gov 

* Os serviços compatíveis com o Ponto de Extremidade de Serviço devem ser configurados com pontos de extremidade de serviço.
* Pontos de extremidade HTTP/HTTPS do FQDN podem ser colocados em seu dispositivo de firewall.
* Pontos de extremidade HTTP/HTTPS curinga são dependências que podem variar de acordo com seu ASE com base em vários qualificadores.

O Linux não está disponível nas regiões US Gov e, portanto, não está listado como uma configuração opcional.

#### <a name="service-endpoint-capable-dependencies"></a>Dependências com capacidade de Ponto de Extremidade de Serviço ####

| Ponto de extremidade |
|----------|
| SQL do Azure |
| Armazenamento do Azure |
| Hub de Eventos do Azure |

#### <a name="ip-address-dependencies"></a>Dependências de endereço IP

| Ponto de extremidade | Detalhes |
|----------| ----- |
| \*:123 | Verificação do relógio do NTP. O tráfego é verificado em vários pontos de extremidade na porta 123 |
| \*:12000 | Essa porta é usada para alguns tipos de monitoramento do sistema. Se ela estiver bloqueada, alguns problemas serão mais difíceis de serem passados pela triagem, mas o ASE continuará funcionando |
| 40.77.24.27:80 | Necessário para monitorar e alertar sobre problemas do ASE |
| 40.77.24.27:443 | Necessário para monitorar e alertar sobre problemas do ASE |
| 13.90.249.229:80 | Necessário para monitorar e alertar sobre problemas do ASE |
| 13.90.249.229:443 | Necessário para monitorar e alertar sobre problemas do ASE |
| 104.45.230.69:80 | Necessário para monitorar e alertar sobre problemas do ASE |
| 104.45.230.69:443 | Necessário para monitorar e alertar sobre problemas do ASE |
| 13.82.184.151:80 | Necessário para monitorar e alertar sobre problemas do ASE |
| 13.82.184.151:443 | Necessário para monitorar e alertar sobre problemas do ASE |

#### <a name="dependencies"></a>Dependências ####

| Ponto de extremidade |
|----------|
| \*.ctldl.windowsupdate.com:80 |
| \*.management.usgovcloudapi.net:80 |
| \*.update.microsoft.com:80 |
|admin.core.usgovcloudapi.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azprofileruploads1.blob.core.windows.net:80 |
|azprofileruploads10.blob.core.windows.net:80 |
|azprofileruploads2.blob.core.windows.net:80 |
|azprofileruploads3.blob.core.windows.net:80 |
|azprofileruploads4.blob.core.windows.net:80 |
|azprofileruploads5.blob.core.windows.net:80 |
|azprofileruploads6.blob.core.windows.net:80 |
|azprofileruploads7.blob.core.windows.net:80 |
|azprofileruploads8.blob.core.windows.net:80 |
|azprofileruploads9.blob.core.windows.net:80 |
|azureprofilerfrontdoor.cloudapp.net:80 |
|azurewatsonanalysis.usgovcloudapp.net:80 |
|cacerts.digicert.com:80 |
|client.wns.windows.com:80 |
|crl.microsoft.com:80 |
|crl.verisign.com:80 |
|crl3.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|ctldl.windowsupdate.com:80 |
|definitionupdates.microsoft.com:80 |
|download.windowsupdate.com:80 |
|fairfax.warmpath.usgovcloudapi.net:80 |
|flighting.cp.wd.microsoft.com:80 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:80 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:80 |
|global.metrics.nsatc.net:80 |
|go.microsoft.com:80 |
|gr-gcws-prod-bd3.usgovcloudapp.net:80 |
|gr-gcws-prod-bn1.usgovcloudapp.net:80 |
|gr-gcws-prod-dd3.usgovcloudapp.net:80 |
|gr-gcws-prod-dm2.usgovcloudapp.net:80 |
|gr-gcws-prod-phx20.usgovcloudapp.net:80 |
|gr-gcws-prod-sn5.usgovcloudapp.net:80 |
|login.live.com:80 |
|login.microsoftonline.us:80 |
|management.core.usgovcloudapi.net:80 |
|management.usgovcloudapi.net:80 |
|maupdateaccountff.blob.core.usgovcloudapi.net:80 |
|mscrl.microsoft.com:80
|ocsp.digicert.com:80 |
|ocsp.verisign.com:80 |
|rteventse.trafficmanager.net:80 |
|settings-n.data.microsoft.com:80 |
|shavamafestcdnprod1.azureedge.net:80 |
|shavanifestcdnprod1.azureedge.net:80 |
|v10ortex-win.data.microsoft.com:80 |
|wp.microsoft.com:80 |
|dcpalt.microsoft.com:80 |
|www.microsoft.com:80 |
|www.msftconnecttest.com:80 |
|www.thawte.com:80 |
|\*ctldl.windowsupdate.com:443 |
|\*.management.usgovcloudapi.net:443 |
|\*.update.microsoft.com:443 |
|admin.core.usgovcloudapi.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads5.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 |
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|azurewatsonanalysis.usgovcloudapp.net:443 |
|cacerts.digicert.com:443 |
|client.wns.windows.com:443 |
|crl.microsoft.com:443 |
|crl.verisign.com:443 |
|crl3.digicert.com:443 |
|csc3-2009-2.crl.verisign.com:443 |
|ctldl.windowsupdate.com:443 |
|definitionupdates.microsoft.com:443 |
|download.windowsupdate.com:443 |
|fairfax.warmpath.usgovcloudapi.net:443 |
|gcs.monitoring.core.usgovcloudapi.net:443 |
|flighting.cp.wd.microsoft.com:443 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:443 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:443 |
|global.metrics.nsatc.net:443 |
|go.microsoft.com:443 |
|gr-gcws-prod-bd3.usgovcloudapp.net:443 |
|gr-gcws-prod-bn1.usgovcloudapp.net:443 |
|gr-gcws-prod-dd3.usgovcloudapp.net:443 |
|gr-gcws-prod-dm2.usgovcloudapp.net:443 |
|gr-gcws-prod-phx20.usgovcloudapp.net:443 |
|gr-gcws-prod-sn5.usgovcloudapp.net:443 |
|login.live.com:443 |
|login.microsoftonline.us:443 |
|management.core.usgovcloudapi.net:443 |
|management.usgovcloudapi.net:443 |
|maupdateaccountff.blob.core.usgovcloudapi.net:443 |
|mscrl.microsoft.com:443 |
|ocsp.digicert.com:443 |
|ocsp.msocsp.com:443 |
|ocsp.msocsp.com:80 |
|oneocsp.microsoft.com:80 |
|oneocsp.microsoft.com:443 |
|ocsp.verisign.com:443 |
|rteventservice.trafficmanager.net:443 |
|settings-win.data.microsoft.com:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|v10.vortex-win.data.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|www.microsoft.com:443 |
|www.msftconnecttest.com:443 |
|www.thawte.com:443 |
|global-dsms.dsms.core.usgovcloudapi.net:443 |

<!--Image references-->
[1]: ./media/firewall-integration/firewall-apprule.png
[2]: ./media/firewall-integration/firewall-serviceendpoints.png
[3]: ./media/firewall-integration/firewall-ntprule.png
[4]: ./media/firewall-integration/firewall-routetable.png
[5]: ./media/firewall-integration/firewall-topology.png
[6]: ./media/firewall-integration/firewall-ntprule-monitor.png
