---
title: Firewall local de Sincronização de Arquivos do Azure e configurações de proxy | Microsoft Docs
description: Configuração da rede local de Sincronização de Arquivos do Azure
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 11/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d9b7296a116ebd06542a53087afbd083dbd3a7eb
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766868"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Configurações de proxy e firewall da Sincronização de arquivos do Azure
A Sincronização de arquivos do Azure se conecta seus servidores locais para arquivos do Azure, permitindo camadas de recursos de nuvem e sincronização de vários locais. Como tal, um servidor local deve estar conectado à internet. Um administrador de TI precisa decidir o melhor caminho para o servidor acessar os serviços de nuvem do Azure.

Este artigo fornecerá informações sobre requisitos específicos e as opções disponíveis para conectar com êxito e segurança o servidor de Sincronização de Arquivos do Azure.

> [!Important]
> A Sincronização de Arquivos do Azure ainda não suporta firewalls e redes virtuais para uma conta de armazenamento.

## <a name="overview"></a>Visão geral
A Sincronização de Arquivos do Azure atua como um serviço de coordenação entre o Windows Server, seu compartilhamento de arquivos do Azure e vários outros serviços do Azure para sincronizar os dados conforme descrito em seu grupo de sincronização. Para que a Sincronização de Arquivos do Azure funcione corretamente, você precisará configurar seus servidores para se comunicar com os seguintes serviços do Azure:

- Armazenamento do Azure
- Sincronização de Arquivos do Azure
- Azure Resource Manager
- Serviços de Autenticação

> [!Note]  
> O agente de Sincronização de Arquivos do Azure no Windows Server inicia todas as solicitações para serviços que resultam em apenas ter que considerar o tráfego de saída de uma perspectiva de firewall de nuvem. <br /> Nenhum serviço do Azure inicia uma conexão com o agente de sincronização de arquivos do Azure.

## <a name="ports"></a>Portas
A Sincronização de Arquivos do Azure move metadados e dados de arquivos exclusivamente via HTTPS e exige que a porta 443 tenha a saída aberta.
Como resultado, todo o tráfego é criptografado.

## <a name="networks-and-special-connections-to-azure"></a>Redes e conexões especiais para o Azure
O agente de Sincronização de Arquivos do Azure não tem requisitos sobre canais especiais como [ExpressRoute](../../expressroute/expressroute-introduction.md), etc. para o Azure.

A Sincronização de Arquivos do Azure funcionará por meios disponíveis que permitem o alcance no Azure, automaticamente se adaptando a várias características de rede com largura de banda, latência, bem como oferecendo controle de administração para ajustar. Alguns recursos não estão disponíveis neste momento. Se você quiser configurar o comportamento específico, fale por meio do [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy
A Sincronização de Arquivos do Azure oferece suporte a configurações de proxy específicas do aplicativo e de todo o computador.

As **configurações de proxy específicas do aplicativo** permitirão a configuração de um proxy especificamente para o tráfego da Sincronização de Arquivos do Azure. As configurações de proxy específicas do aplicativo são suportadas no agente versão 4.0.1.0 ou mais recente e podem ser configuradas durante a instalação do agente ou usando o cmdlet do PowerShell Set-StorageSyncProxyConfiguration.

Comandos do PowerShell para definir as configurações de proxy específicas do aplicativo:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
As **configurações de proxy amplas do computador** são transparentes para o agente de Sincronização de Arquivos do Azure, pois o tráfego inteiro do servidor é roteado através do proxy.

Para definir as configurações de proxy amplas do computador, siga as etapas abaixo: 

1. Definir configurações de proxy para aplicativos .NET 

   - Edite esses dois arquivos:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Adicione a seção <system.net> nos arquivos machine.config (abaixo da seção <system.serviceModel>).  Altere 127.0.01: 8888 para o endereço IP e porta para o servidor proxy. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. Definir as configurações de proxy do WinHTTP 

   - Execute o comando a seguir em um prompt de comandos com privilégios elevados ou no PowerShell para ver a configuração de proxy existente:   

     netsh winhttp show proxy

   - Execute o comando a seguir em um prompt de comandos com privilégios elevados ou no PowerShell para definir a configuração de proxy (altere 127.0.01: 8888 para o endereço IP e porta para o servidor proxy):  

     netsh winhttp set proxy 127.0.0.1:8888

3. Reinicie o serviço do Agente de Sincronização de Armazenamento executando o comando a seguir em um prompt de comandos com privilégios elevados ou no PowerShell: 

      net stop filesyncsvc

      Observação: o serviço do Agente de Sincronização de Armazenamento (filesyncsvc) será iniciado automaticamente quando interrompido.

## <a name="firewall"></a>Firewall
Conforme mencionado em uma seção anterior, a porta 443 precisa estar com a saída aberta. Com base em políticas em seu banco de dados, a ramificação ou a região, restringir ainda mais o tráfego por essa porta a domínios específicos pode ser desejado ou necessário.

A tabela a seguir descreve os domínios necessários para a comunicação:

| Serviço | Ponto de extremidade de nuvem pública | Ponto de extremidade do Azure Governamental | Uso |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | https://management.usgovcloudapi.net | Qualquer chamada de usuário (como o PowerShell) passa por essa URL, incluindo a chamada de registro inicial do servidor. |
| **Azure Active Directory** | https://login.windows.net | https://login.microsoftonline.us | As chamadas do Azure Resource Manager devem ser feitas por um usuário autenticado. Para ter êxito, essa URL é usada para autenticação do usuário. |
| **Azure Active Directory** | https://graph.windows.net/ | https://graph.windows.net/ | Como parte da implantação de Sincronização de Arquivos do Azure, será criado um objeto de serviço do Azure Active Directory da assinatura. Essa URL é usada para fazer isso. Essa entidade de segurança é usada para a delegação de um conjunto mínimo de direitos para o Serviço de Sincronização de Arquivos do Azure. O usuário que estiver executando a configuração inicial de Sincronização de Arquivos do Azure deve ser um usuário autenticado com privilégios de proprietário da assinatura. |
| **Armazenamento do Azure** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | Quando o servidor baixa um arquivo, o servidor executa essa movimentação de dados com mais eficiência quando se comunicando diretamente com o compartilhamento de arquivos do Azure na conta de armazenamento. O servidor tem uma chave SAS que só permite o acesso de compartilhamento do arquivo de destino. |
| **Sincronização de Arquivos do Azure** | &ast;. one.microsoft.com | &ast;.afs.azure.us | Após o registro do servidor inicial, o servidor recebe uma URL regional para a instância do serviço de Sincronização de Arquivos do Azure nessa região. O servidor pode usar a URL para se comunicar de forma direta e eficiente com a instância de tratando sua sincronização. |
| **Microsoft PKI** | `https://www.microsoft.com/pki/mscorp`<br /><http://ocsp.msocsp.com> | `https://www.microsoft.com/pki/mscorp`<br /><http://ocsp.msocsp.com> | Depois de instalar o agente da Sincronização de Arquivos do Azure, a URL do PKI é usada para baixar os certificados intermediários necessários para se comunicar com o serviço de Sincronização de Arquivos do Azure e do compartilhamento de arquivos do Azure. A URL do OCSP é usada para verificar o status de um certificado. |

> [!Important]
> Ao permitir o tráfego para o &ast;. one.microsoft.com, o tráfego para mais do que apenas o serviço de sincronização é possível a partir do servidor. Há muitos mais serviços da Microsoft nos subdomínios.

Se o &ast;. one.microsoft.com for muito amplo, você poderá limitar a comunicação do servidor permitindo a comunicação apenas com instâncias regionais explícitas do serviço Sincronização de Arquivos do Azure. Qual instância escolher depende da região do serviço de sincronização de armazenamento em que o servidor está implantado e registrado. Essa região é chamada de "URL do ponto de extremidade primário" na tabela abaixo.

Por motivos de BCDR (continuidade dos negócios e recuperação de desastres), você pode ter especificado os compartilhamentos de arquivos do Azure em uma conta de GRS (armazenamento com redundância global). Se esse for o caso, os compartilhamentos de arquivos do Azure farão failover na região emparelhada se ocorrer uma interrupção regional duradoura. A Sincronização de Arquivos do Azure usa os mesmos emparelhamentos regionais do armazenamento. Portanto, se você usar contas de armazenamento de GRS, precisará habilitar as URLs adicionais para permitir que o servidor comunique-se com a região emparelhada para Sincronização de Arquivos do Azure. A tabela abaixo identifica isso como "Região emparelhada". Adicionalmente, há uma URL do perfil do gerenciador de tráfego que também precisa ser habilitada. Isso garantirá que o tráfego possa ser roteado novamente diretamente para a região emparelhada no caso de um failover e é chamado de "URL de Descoberta" na tabela abaixo.

| Nuvem  | Região | URL do ponto de extremidade primário | Região emparelhada | URL de descoberta |
|--------|--------|----------------------|---------------|---------------|
| Público |Leste da Austrália | https://kailani-aue.one.microsoft.com | Sudeste da Austrália | https://kailani-aue.one.microsoft.com |
| Público |Sudeste da Austrália | https://kailani-aus.one.microsoft.com | Leste da Austrália | https://tm-kailani-aus.one.microsoft.com |
| Público | Canadá Central | https://kailani-cac.one.microsoft.com | Leste do Canadá | https://tm-kailani-cac.one.microsoft.com |
| Público | Leste do Canadá | https://kailani-cae.one.microsoft.com | Canadá Central | https://tm-kailani.cae.one.microsoft.com |
| Público | Centro dos EUA | https://kailani-cus.one.microsoft.com | Leste dos EUA 2 | https://tm-kailani-cus.one.microsoft.com |
| Público | Ásia Oriental | https://kailani11.one.microsoft.com | Sudeste Asiático | https://tm-kailani11.one.microsoft.com |
| Público | Leste dos EUA | https://kailani1.one.microsoft.com | Oeste dos EUA | https://tm-kailani1.one.microsoft.com |
| Público | Leste dos EUA 2 | https://kailani-ess.one.microsoft.com | Centro dos EUA | https://tm-kailani-ess.one.microsoft.com |
| Público | Norte da Europa | https://kailani7.one.microsoft.com | Europa Ocidental | https://tm-kailani7.one.microsoft.com |
| Público | Sudeste Asiático | https://kailani10.one.microsoft.com | Ásia Oriental | https://tm-kailani10.one.microsoft.com |
| Público | Sul do Reino Unido | https://kailani-uks.one.microsoft.com | Oeste do Reino Unido | https://tm-kailani-uks.one.microsoft.com |
| Público | Oeste do Reino Unido | https://kailani-ukw.one.microsoft.com | Sul do Reino Unido | https://tm-kailani-ukw.one.microsoft.com |
| Público | Europa Ocidental | https://kailani6.one.microsoft.com | Norte da Europa | https://tm-kailani6.one.microsoft.com |
| Público | Oeste dos EUA | https://kailani.one.microsoft.com | Leste dos EUA | https://tm-kailani.one.microsoft.com |
| Governamental | Governo dos EUA do Arizona | https://usgovarizona01.afs.azure.us | Governo dos EUA do Texas | https://tm-usgovarizona01.afs.azure.us |
| Governamental | Governo dos EUA do Texas | https://usgovtexas01.afs.azure.us | Governo dos EUA do Arizona | https://tm-usgovtexas01.afs.azure.us |

- Se estiver utilizando contas de LRS (armazenamento com redundância local) ou ZRS (com redundância de zona), será necessário somente habilitar a URL listada em "URL do ponto de extremidade primário".

- Se estiver utilizando contas de GRS (armazenamento com redundância global), habilite três URLs.

**Exemplo:** você implanta um serviço de sincronização de armazenamento em `"West US"` e registra o servidor com ele. As URLs para permitir que o servidor comunique-se para esse caso são:

> - https://kailani.one.microsoft.com (ponto de extremidade primário: Oeste dos EUA)
> - https://kailani1.one.microsoft.com (região de failover emparelhada: Leste dos EUA)
> - https://tm-kailani.one.microsoft.com (URL de descoberta da região primária)

## <a name="summary-and-risk-limitation"></a>Resumo e limitação de risco
As listas no início deste documento contém as URLs de Sincronização de Arquivos do Azure com que está se comunicando. Os firewalls devem permitir o tráfego de saída para esses domínios. A Microsoft se esforça para manter essa lista atualizada.

A configuração das regras de firewall de restrição de domínio pode ser uma medida para melhorar a segurança. Se essas configurações de firewall são utilizadas, é necessário ter em mente que URLs serão adicionadas e poderão até mesmo ser alteradas ao longo do tempo. Consulte este artigo periodicamente.

## <a name="next-steps"></a>Próximas etapas
- [Planejando uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
- [Implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md)
- [Monitorar a Sincronização de Arquivos do Azure](storage-sync-files-monitoring.md)
