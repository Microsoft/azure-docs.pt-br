---
title: incluir arquivo
description: incluir arquivo
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/03/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 3a8a7be6f437687a4de31ce8e0ac62588f64e2eb
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "96016889"
---
**Requisitos do servidor de Configuração/Processo para replicação de servidor físico**

**Componente** | **Requisito** 
--- | ---
**CONFIGURAÇÕES DE HARDWARE** | 
Núcleos de CPU | 8 
RAM | 16 GB
Número de discos | Três, incluindo o disco de OS, disco de cache do servidor de processo e unidade de retenção para failback 
Espaço livre em disco (cache do servidor de processo) | 600 GB
Espaço livre em disco (disco de retenção) | 600 GB
 | 
**CONFIGURAÇÕES DE SOFTWARE** | 
Sistema operacional | Windows Server 2012 R2 <br> Windows Server 2016
Localidade do sistema operacional | Inglês (en-us)
Funções do Windows Server | Não habilite essas funções: <br> - Active Directory Domain Services <br>- Serviços de Informações da Internet <br> - Hyper-V 
Políticas de grupo | Não habilite estas políticas de grupo: <br> - Impedir o acesso ao prompt de comando. <br> - Impedir o acesso às ferramentas de edição do registro. <br> - Lógica de confiança para anexos de arquivo. <br> - Ativar a execução do script. <br> [Saiba mais](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | - Nenhum site da Web padrão já existente <br> - Nenhum aplicativo/site da Web pré-existente escutando na porta 443 <br>- Habilitar [autenticação anônima](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> -Habilitar configuração de [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) .
Tipo de endereço IP | Estático 
| 
**CONFIGURAÇÕES DE ACESSO** | 
MySQL | O MySQL deve ser instalado no servidor de configuração. Você pode instalar manualmente, ou o Azure Site Recovery pode instalá-lo durante a implantação. Para o Azure Site Recovery realizar a instalação, verifique se o computador pode acessar http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi.
URLs | O servidor de configuração precisa ter acesso a estas URLs (diretamente ou via proxy):<br/><br/> Microsoft Azure AD: `login.microsoftonline.com`; `login.microsoftonline.us`; `*.accesscontrol.windows.net`<br/><br/> Transferência de dados de replicação: `*.backup.windowsazure.com`; `*.backup.windowsazure.us`<br/><br/> Gerenciamento de replicação: `*.hypervrecoverymanager.windowsazure.com`; `*.hypervrecoverymanager.windowsazure.us`; `https://management.azure.com`; `*.services.visualstudio.com`<br/><br/> Acesso de armazenamento: `*.blob.core.windows.net`; `*.blob.core.usgovcloudapi.net`<br/><br/> Sincronização de horário: `time.nist.gov` ; `time.windows.com`<br/><br/> Telemetria (opcional): `dc.services.visualstudio.com`
Firewall | As regras de firewall baseadas em endereço IP devem permitir a comunicação com as URLs do Azure. Para simplificar e limitar os intervalos de IP, recomendamos usar a filtragem de URL.<br/><br/>**Para IPs comerciais:**<br/><br/>- Permita os [Intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).<br/><br/> -Permita os intervalos de endereços IP para o Oeste dos EUA (usados para Controle de Acesso e Gerenciamento de Identidade).<br/><br/> - Permita que os intervalos de endereços IP para a região do Azure de sua assinatura deem suporte ao Azure Active Directory, ao backup, à replicação e ao armazenamento.<br/><br/> **Para IPs de governo:**<br/><br/> - Permita os Intervalos de IP do Datacenter do Azure Governamental a porta HTTPS (443).<br/><br/> - Permita os intervalos de endereços IP para todas as Regiões do Gov (US) (Virgínia, Texas, Arizona e Iowa) para dar suporte ao Azure Active Directory, ao backup, à replicação e ao armazenamento.
Portas | Permitir 443 (orquestração do canal de controle)<br/><br/> Permitir 9443 (transporte de dados) 


**Requisitos de dimensionamento do servidor de processo/configuração**

**CPU** | **Memória** | **Cache de disco** | **Taxa de alteração de dados** | **Computadores replicados**
--- | --- | --- | --- | ---
8 vCPUs<br/><br/> 2 soquetes * 4 núcleos \@ 2,5 GHz | 16 GB | 300 GB | 500 GB ou menos | < 100 computadores
12 vCPUs<br/><br/> 2 soquetes  * 6 núcleos \@ 2,5 GHz | 18 GB | 600 GB | 500 GB -1 TB | 100 a 150 computadores
16 vCPUs<br/><br/> 2 soquetes  * 8 núcleos \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 a 200 computadores