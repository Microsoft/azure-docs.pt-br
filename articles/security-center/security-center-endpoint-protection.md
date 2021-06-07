---
title: Recomendações do Endpoint Protection nos centros de segurança do Azure
description: Como as soluções do Endpoint Protection são descobertas e identificadas como íntegras.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: memildin
ms.openlocfilehash: 1ce20deed8b26dc5f5bebf4656dd3f1c370d766f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561221"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Avaliação e recomendações do Endpoint Protection na central de segurança do Azure

A central de segurança do Azure fornece avaliações de integridade de versões [com suporte](security-center-services.md#endpoint-supported) das soluções do Endpoint Protection. Este artigo explica os cenários que levam à central de segurança para gerar as duas recomendações a seguir:

* **Instalar soluções do Endpoint Protection em sua máquina virtual**
* **Resolver problemas de integridade da proteção do endpoint em suas máquinas**

## <a name="windows-defender"></a>Windows Defender

* A central de segurança recomenda que você **"Instale soluções do Endpoint Protection na máquina virtual"** quando [Get-MpComputerStatus](/powershell/module/defender/get-mpcomputerstatus) for executado e o resultado for **AMServiceEnabled: false**

* A central de segurança recomenda que você **"resolva problemas de integridade do Endpoint Protection em suas máquinas" quando o** [Get-MpComputerStatus](/powershell/module/defender/get-mpcomputerstatus) for executado e qualquer uma das seguintes situações ocorrer:

  * Qualquer uma das seguintes propriedades é falsa:

    - **AMServiceEnabled**
    - **AntispywareEnabled**
    - **RealTimeProtectionEnabled**
    - **BehaviorMonitorEnabled**
    - **IoavProtectionEnabled**
    - **OnAccessProtectionEnabled**

  * Se uma ou ambas as propriedades a seguir forem 7 ou mais:

    - **AntispywareSignatureAge**
    - **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Proteção de ponto de extremidade do Microsoft System Center

* A central de segurança recomenda que você **"Instale soluções de proteção de ponto de extremidade na máquina virtual"** ao importar **SCEPMpModule ("$env:P Rogramfiles \ Microsoft Security Client\MpProvider\MpProvider.psd1")** e executar resultados **Get-MProtComputerStatus** em **AMServiceEnabled = false**.

* A central de segurança recomenda que você **"resolva problemas de integridade do Endpoint Protection em suas máquinas" quando o** **Get-MprotComputerStatus** for executado e qualquer uma das seguintes situações ocorrer:

  * Pelo menos uma das seguintes propriedades é false:

    - **AMServiceEnabled**
    - **AntispywareEnabled**
    - **RealTimeProtectionEnabled**
    - **BehaviorMonitorEnabled**
    - **IoavProtectionEnabled**
    - **OnAccessProtectionEnabled**

  * Se uma ou ambas as atualizações de assinatura a seguir forem maiores ou iguais a 7:

    * **AntispywareSignatureAge**
    * **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* A central de segurança recomenda que você **"Instale soluções do Endpoint Protection na máquina virtual"** quando qualquer uma das seguintes verificações não for atendida:
    - **HKLM: \ SOFTWARE\TrendMicro\Deep Security Agent** existe
    - **HKLM: \ SOFTWARE\TrendMicro\Deep Security Agent\InstallationFolder** existe
    - O arquivo **dsa_query. cmd** é encontrado na pasta de instalação
    - Executando os resultados de **dsa_query. cmd** com o **componente. am. Mode: o agente de segurança em Trend Micro Deep Security detectado**

## <a name="symantec-endpoint-protection"></a>Symantec Endpoint Protection
A central de segurança recomenda que você **"Instale soluções do Endpoint Protection na máquina virtual"** quando qualquer uma das seguintes verificações não for atendida:

- **HKLM: \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**
- **HKLM: \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Ou

- **HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**
- **HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

A central de segurança recomenda que você **"resolva problemas de integridade do Endpoint Protection em suas máquinas"** quando qualquer uma das seguintes verificações não for atendida:

- Verifique a versão do Symantec >= 12: local do registro: **HKLM: \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion "-value" PRODUCTVERSION "**
- Verifique Real-Time status de proteção: **HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff = = 1**
- Verificar status de atualização de assinatura: **HKLM\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate <= 7 dias**
- Verifique o status da verificação completa: **HKLM: \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 dias**
- Localize o caminho do número de versão da assinatura para a versão da assinatura do Symantec 12: **caminhos do registro + "CurrentVersion\SharedDefs" – valor "SRTSP"** 
- Caminho para a versão da assinatura do Symantec 14: **caminhos do registro + "CurrentVersion\SharedDefs\SDSDefs" – valor "SRTSP"**

Caminhos do registro:
- **"HKLM: \ Software\Symantec\Symantec Endpoint Protection" + $Path;**
- **"HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>McAfee Endpoint Protection para Windows

A central de segurança recomenda que você **"Instale soluções do Endpoint Protection na máquina virtual"** quando qualquer uma das seguintes verificações não for atendida:

- **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion** existe
- **HKLM: \ SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

A central de segurança recomenda que você **"resolva problemas de integridade do Endpoint Protection em suas máquinas"** quando qualquer uma das seguintes verificações não for atendida:

- Versão da McAfee: **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**
- Localizar versão da assinatura: **HKLM: \ Software\McAfee\AVSolution\DS\DS-Value "dwContentMajorVersion"**
- Localizar a data da assinatura: **HKLM: \ Software\McAfee\AVSolution\DS\DS-Value "szContentCreationDate" >= 7 dias**
- Localizar data de verificação: **HKLM: \ Software\McAfee\Endpoint\AV\ODS-Value "LastFullScanOdsRunTime" >= 7 dias**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>Prevenção contra ameaças do McAfee Endpoint Security para Linux 

A central de segurança recomenda que você **"Instale soluções do Endpoint Protection na máquina virtual"** quando qualquer uma das seguintes verificações não for atendida:

- O arquivo **/opt/ISEC/ENS/threatprevention/bin/isecav** existe
- **a saída "/opt/ISEC/ENS/threatprevention/bin/isecav-Version"** é: **nome da McAfee = McAfee Endpoint Security para Linux Threat Prevention e McAfee versão >= 10**

A central de segurança recomenda que você **"resolva problemas de integridade do Endpoint Protection em suas máquinas"** quando qualquer uma das seguintes verificações não for atendida:

- **"/opt/ISEC/ENS/threatprevention/bin/isecav--listtask"** retorna **verificação rápida, verificação completa** e ambas as verificações <= 7 dias
- **"/opt/ISEC/ENS/threatprevention/bin/isecav--listtask"** retorna o **dat e o tempo de atualização do mecanismo** e ambos <= 7 dias
- **"/opt/ISEC/ENS/threatprevention/bin/isecav--getoasconfig--Summary"** retorna **no status da verificação de acesso**

## <a name="sophos-antivirus-for-linux"></a>Sophos Antivirus para Linux 

A central de segurança recomenda que você **"Instale soluções do Endpoint Protection na máquina virtual"** quando qualquer uma das seguintes verificações não for atendida:
- O arquivo **/opt/Sophos-AV/bin/savdstatus** sai ou pesquisa o local personalizado **"readlink $ (que SAVScan)"**
- **"/opt/Sophos-AV/bin/savdstatus--Version"** retorna o Sophos name = **Sophos Anti-Virus e o Sophos versão >= 9**

A central de segurança recomenda que você **"resolva problemas de integridade do Endpoint Protection em suas máquinas"** quando qualquer uma das seguintes verificações não for atendida:
- **"/opt/Sophos-AV/bin/savlog--MaxAge = 7 | grep-i "verificação agendada. \* concluído "| Tail-1 "**, retorna um valor
- **"/opt/Sophos-AV/bin/savlog--MaxAge = 7 | grep "Verificação concluída"** | Tail-1 ", retorna um valor
- **"/opt/Sophos-AV/bin/savdstatus--LastUpdate"** retorna LastUpdate, que deve ser <= 7 dias 
- **"/opt/Sophos-AV/bin/savdstatus-v"** é igual a **"a verificação no acesso está em execução"** 
- **"/opt/Sophos-AV/bin/savconfig Get LiveProtection"** retorna habilitado

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solucionar problemas

Os logs de extensão antimalware da Microsoft estão disponíveis em: **%systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware (ou PaaSAntimalware) \1.5.5.x (Version #) \CommandExecution.log**

### <a name="support"></a>Suporte

Para obter mais ajuda, entre em contato com os especialistas do Azure nos [fóruns do Azure e Stack Overflow do MSDN](https://azure.microsoft.com/support/forums/). Ou arquivo de um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).