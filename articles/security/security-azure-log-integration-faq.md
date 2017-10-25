---
title: "Perguntas frequentes sobre a Integração de Logs do Azure | Microsoft Docs"
description: "Este artigo de perguntas frequentes responde às perguntas sobre a Integração de Logs do Azure."
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: d06d1ac5-5c3b-49de-800e-4d54b3064c64
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload8: na
ms.date: 08/07/2017
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: bfdc7154160bb6bb7dc9c46eb2352ce74310c4de
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-log-integration-faq"></a>Perguntas frequentes sobre a Integração de Logs do Azure
Este artigo responde as perguntas frequentes (FAQ) sobre a Integração de Logs do Azure. 

A Integração de Logs do Azure é um serviço do sistema operacional Windows que permite integrar logs brutos de recursos do Azure a seus sistemas locais de SIEM (Gerenciamento de Eventos e Informações de Segurança). Essa integração oferece um painel unificado para todos os seus ativos, locais ou na nuvem. Você pode então agregar, correlacionar, analisar e alertar sobre eventos de segurança associados a seus aplicativos.

## <a name="is-the-azure-log-integration-software-free"></a>O software Integração de Log do Azure é gratuito?
Sim. Não há nenhuma cobrança pelo software Integração de Log do Azure.

## <a name="where-is-azure-log-integration-available"></a>Onde a Integração de log do Azure está disponível?

Atualmente, ela está disponível atualmente no Azure Comercial e o Azure Governamental e não está disponível na China nem na Alemanha.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs"></a>Como posso ver as contas de armazenamento nas quais a Integração de Logs do Azure está efetuando pull para extrair os logs da VM do Azure?
Execute o comando **azlog source list**.

## <a name="how-can-i-tell-which-subscription-the-azure-log-integration-logs-are-from"></a>Como saber de qual assinatura os logs de Integração de Logs do Azure são provenientes?

No caso de logs de auditoria que são colocados nos diretórios **AzureResourcemanagerJson**, a ID da assinatura está no nome do arquivo de log. Isso também é verdadeiro para logs na pasta **AzureSecurityCenterJson**. Por exemplo:

20170407T070805_2768037.0000000023.**1111e5ee-1111-111b-a11e-1e111e1111dc**.json

Logs de auditoria do Azure Active Directory incluem a ID do locatário como parte do nome.

Os logs de diagnóstico lidos de um hub de eventos não incluem a ID da assinatura como parte do nome. Em vez disso, eles incluem o nome amigável especificado como parte da criação da origem do hub de eventos. 

## <a name="how-can-i-update-the-proxy-configuration"></a>Como atualizo a configuração de proxy?
Se a configuração de proxy não permitir acesso ao armazenamento do Azure diretamente, abra o arquivo **AZLOG.EXE.CONFIG** em **c:\Arquivos de Programas\Integração de Log do Microsoft Azure**. Atualize o arquivo para incluir a seção **defaultProxy** com o endereço do proxy da sua organização. Depois que a atualização for concluída, pare e inicie o serviço usando os comandos **net stop azlog** e **net start azlog**.

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress=http://127.0.0.1:8888
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>Como posso ver as informações de assinatura nos eventos do Windows?
Acrescente a ID da assinatura ao nome amigável ao adicionar a origem:

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  
O evento XML tem os seguintes metadados, incluindo a ID da assinatura:

![Evento XML][1]

## <a name="error-messages"></a>Mensagens de erro
### <a name="when-i-run-the-command-azlog-createazureid-why-do-i-get-the-following-error"></a>Ao executar o comando **azlog createazureid**, por que obtenho o erro a seguir?
Erro:

  *Falha ao criar aplicativo AAD - Locatário 72f988bf-86f1-41af-91ab-2d7cd011db37 - Motivo = 'Proibido' - Mensagem = 'Privilégios insuficientes para concluir a operação.'*

O comando **azlog createazureid** tenta criar uma entidade de serviço em todos os locatários do Azure AD para as assinaturas nas quais o logon do Azure tem acesso. Se o logon do Azure for apenas um usuário Convidado nesse locatário do Azure AD, o comando falhará com “Privilégios insuficientes para concluir a operação”. Solicite ao administrador do locatário para adicionar sua conta como um usuário no locatário.

### <a name="when-i-run-the-command-azlog-authorize-why-do-i-get-the-following-error"></a>Ao executar o comando **azlog authorize**, por que obtenho o erro a seguir?
Erro:

  *Aviso ao criar Atribuição de Função – AuthorizationFailed: O cliente janedo@microsoft.com' com a id de objeto 'fe9e03e4-4dad-4328-910f-fd24a9660bd2' não tem autorização para executar a ação 'Microsoft.Authorization/roleAssignments/write' além do escopo '/subscriptions/70d95299-d689-4c97-b971-0d8ff0000000'.*

O comando **azlog authorize** tenta criar a função de leitor para a entidade de serviço do Azure AD (criada com **azlog createazureid**) para as assinaturas fornecidas. Se o logon do Azure não for um coadministrador nem um proprietário da assinatura, ele falhará com a mensagem de erro “Falha na Autorização”. O RBAC (Controle de Acesso Baseado em Função) do coadministrador ou proprietário é necessário para concluir essa ação.

## <a name="where-can-i-find-the-definition-of-the-properties-in-the-audit-log"></a>Onde posso encontrar a definição das propriedades no log de auditoria?
Consulte:

* [Operações de auditoria com o Azure Resource Manager](../azure-resource-manager/resource-group-audit.md)
* [Listar os eventos de gerenciamento em uma assinatura na API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Onde posso encontrar detalhes sobre alertas da Central de Segurança do Azure?
Confira [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](../security-center/security-center-managing-and-responding-alerts.md).

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>Como posso modificar o que é coletado com o diagnóstico da VM?
Para obter detalhes sobre como obter, modificar e definir a configuração do Diagnóstico do Azure, veja [Usar o PowerShell para habilitar o Diagnóstico do Azure em uma máquina virtual que executa o Windows](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

O exemplo a seguir obtém a configuração do Diagnóstico do Azure:

    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

O exemplo a seguir modifica a configuração do Diagnóstico do Azure. Nessa configuração, somente a ID de evento 4624 e a ID de evento 4625 são coletadas do log de eventos de segurança. Os eventos do Microsoft Antimalware para Azure são coletados no log de eventos do sistema. Para obter detalhes sobre o uso de expressões XPath, veja [Consumo de eventos](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85)).

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

O exemplo a seguir define a configuração do Diagnóstico do Azure:

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

Depois de fazer alterações, verifique a conta de armazenamento para garantir que os eventos corretos sejam coletados.

Se você tiver problemas durante a instalação e configuração, abra uma [solicitação de suporte](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Selecione **Integração de Log** como o serviço para o qual você está solicitando suporte.

## <a name="can-i-use-azure-log-integration-to-integrate-network-watcher-logs-into-my-siem"></a>Posso usar a Integração de Logs do Azure para integrar os logs do Observador de Rede em meu SIEM?

O Observador de Rede do Azure gera grandes quantidades de informações de registro em log. Esses logs não devem ser enviadas para um SIEM. O único destino com suporte para logs do Observador de Rede é uma conta de armazenamento. A Integração de Logs do Azure não dá suporte à leitura desses logs e disponibilização deles para um SIEM.

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png
