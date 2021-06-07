---
title: Perguntas frequentes sobre Enterprise State Roaming-Azure Active Directory
description: Perguntas frequentes sobre o ESR
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb889298a09c30a629c69442ebf31bc735af31d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96173117"
---
# <a name="settings-and-data-roaming-faq"></a>Configurações e perguntas frequentes sobre o roaming de dados

Este artigo responde a algumas dúvidas que os administradores de TI podem ter sobre as configurações e a sincronização de dados do aplicativo.

## <a name="what-data-roams"></a>Quais dados são movidos?

**Configurações do Windows**: as configurações do PC criadas no sistema operacional Windows. Em geral, essas são as configurações que personalizam seu PC e incluem as seguintes grandes categorias:

* *Tema*, que inclui recursos como as configurações de tema da área de trabalho e da barra de tarefas.
* *Configurações do Internet Explorer*, incluindo guias e favoritos abertos recentemente.
* *Configurações do navegador Microsoft Edge*, como favoritos e lista de leitura.
* *Senhas*, incluindo senhas da Internet, perfis de Wi-Fi e outros.
* *Preferências de idioma*, que incluem configurações para layouts de teclado, idioma do sistema, data e hora e muito mais.
* *Recursos de facilidade de acesso*, como tema de alto contraste, Narrador e Lupa.
* *Outras configurações do Windows*, como configurações do mouse.

> [!NOTE]
> Este artigo se aplica ao navegador baseado em HTML herdado do Microsoft Edge, iniciado com o Windows 10 em julho de 2015. O artigo não se aplica ao novo navegador Microsoft Edge Chromium lançado em 15 de janeiro de 2020. Para obter mais informações sobre o comportamento de sincronização para o novo Microsoft Edge, consulte o artigo [sincronização do Microsoft Edge](/deployedge/microsoft-edge-enterprise-sync).

**Dados de aplicativo**: os aplicativos Universais do Windows podem gravar dados de configurações em uma pasta de roaming e todos os dados gravados nessa pasta serão automaticamente sincronizados. Cabe ao desenvolvedor de aplicativos individuais criar um aplicativo para tirar proveito desse recurso. Para obter mais informações sobre como desenvolver um aplicativo universal do Windows que usa roaming, consulte a [API de armazenamento de AppData](/windows/uwp/design/app-settings/store-and-retrieve-app-data) e o blog do desenvolvedor de [roaming do Windows 8 AppData](https://blogs.windows.com/windowsdeveloper/2016/05/04/roaming-app-data-and-the-user-experience/).

## <a name="what-account-is-used-for-settings-sync"></a>Qual conta é usada para a sincronização de configurações?

No Windows 8.1, a sincronização de configurações sempre usou as contas de consumidor da Microsoft. Os usuários empresariais tinham a capacidade de conectar um conta Microsoft à sua conta de domínio Active Directory para obter acesso à sincronização de configurações. No Windows 10, essa funcionalidade de conta Microsoft conectada está sendo substituída por uma estrutura de conta primária/secundária.

A conta principal é definida como a conta usada para entrar no Windows. Ela pode ser uma conta da Microsoft, uma conta do Azure AD (Azure Active Directory), uma conta do Active Directory local ou uma conta local. Além da conta principal, os usuários do Windows 10 podem adicionar uma ou mais contas de nuvem secundária ao dispositivo. Uma conta secundária geralmente é uma conta da Microsoft, uma conta do Azure AD ou outra conta, como do Gmail ou do Facebook. Essas contas secundárias fornecem acesso a serviços adicionais, como o logon único e a Windows Store, mas elas não são capazes de alimentar a sincronização de configurações.

No Windows 10, apenas a conta principal do dispositivo pode ser usada para a sincronização de configurações (confira [Como atualizar da sincronização de configurações da conta da Microsoft no Windows 8 para a sincronização de configurações do Azure AD no Windows 10?](enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10)).

Os dados nunca são misturados entre as diferentes contas de usuário no dispositivo. Há duas regras para a sincronização de configurações:

* As configurações do Windows sempre serão movidas com a conta principal.
* Os dados do aplicativo serão marcados com a conta usada para adquirir o aplicativo. Somente os aplicativos marcados com a conta primária serão sincronizados. A marcação de Propriedade do aplicativo é determinada quando um aplicativo é carregado por meio da Windows Store ou do MDM (gerenciamento de dispositivo móvel).

Se o proprietário de um aplicativo não puder ser identificado, o aplicativo será movido com a conta principal. Se um dispositivo for atualizado do Windows 8 ou do Windows 8.1 para o Windows 10, todos os aplicativos serão marcados como adquiridos por conta da Microsoft. Isso ocorre porque a maioria dos usuários adquire aplicativos por meio da Windows Store, e não havia nenhum suporte da Windows Store para contas do Azure AD antes do Windows 10. Se um aplicativo for instalado por meio de uma licença offline, o aplicativo será marcado usando a conta principal no dispositivo.

> [!NOTE]
> Os dispositivos do Windows 10 que pertencem à empresa e estão conectados ao Azure AD não podem mais conectar suas Contas da Microsoft a uma conta de domínio. A capacidade de conectar uma conta da Microsoft a uma conta de domínio e de sincronizar todos os dados do usuário para a conta da Microsoft (ou seja, o roaming da conta da Microsoft por meio da funcionalidade de conta da Microsoft conectada e Active Directory) foi removida dos dispositivos Windows 10 ingressados em um ambiente do Active Directory ou Azure AD conectado.

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Como eu atualizo a partir da sincronização das configurações da conta da Microsoft no Windows 8 para a sincronização das configurações do AD do Azure no Windows 10?

Se você tiver ingressado no domínio do Active Directory executando o Windows 8.1 com uma conta Microsoft conectada, você sincronizará as configurações por meio da conta Microsoft. Após a atualização para o Windows 10, você continuará a sincronizar as configurações de usuário por meio da conta da Microsoft, desde que você seja um usuário ingressado em domínio e o domínio do Active Directory não se conecte ao Azure AD.

Se o domínio do Active Directory local se conectar ao Azure AD, seu dispositivo tentará sincronizar as configurações usando a conta conectada do Azure AD. Se o administrador do AD do Azure não habilitar o Enterprise State Roaming, sua conta do Azure AD conectada interromperá a sincronização de configurações. Se você for um usuário do Windows 10 e entrar com uma identidade do Azure AD, você começará a sincronizar as configurações do Windows assim que o administrador permitir a sincronização de configurações por meio do Azure AD.

Se você tiver armazenado dados pessoais em seu dispositivo corporativo, você deverá estar ciente de que o sistema operacional Windows e os dados do aplicativo começarão a ser sincronizados para o Azure AD. Porém, há as seguintes implicações:

* Suas configurações da conta da Microsoft pessoal serão separadas em suas contas de trabalho ou escola do Azure AD. Isso ocorre porque a sincronização da conta da Microsoft e do Azure AD agora estão usando contas separadas.
* Dados pessoais, como senhas de Wi-Fi, credenciais da Web e favoritos do Internet Explorer que eram sincronizados anteriormente por meio de uma conta da Microsoft conectada serão sincronizados por meio do Azure AD.

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Como funciona a conta da Microsoft e a interoperabilidade do Enterprise State Roaming do Azure AD?

Nas versões de novembro de 2015 ou posteriores do Windows 10, o Enterprise State Roaming só tem suporte para uma única conta de cada vez. Se você entrar no Windows usando uma conta corporativa ou de estudante do Azure AD, todos os dados serão sincronizados por meio do Azure AD. Se você entrar no Windows usando uma conta pessoal da Microsoft, todos os dados serão sincronizados por meio da conta da Microsoft. Os dados de aplicativos universais farão roaming usando somente a conta de entrada principal no dispositivo e farão roaming somente se a licença do aplicativo for de propriedade da conta principal. Os dados de aplicativos universais para os aplicativos pertencentes a quaisquer contas secundárias não serão sincronizados.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>As configurações das contas do AD do Azure de vários locatários são sincronizadas?

Quando houver várias contas do Microsoft Azure Active Directory de diferentes locatários do Microsoft Azure Active Directory no mesmo dispositivo, você deverá atualizar o registro do dispositivo para se comunicar com o Azure Rights Management para cada locatário do Microsoft Azure Active Directory.  

1. Encontre o GUID de cada locatário do Azure AD. Abra o Portal do Azure e selecione um locatário do Azure AD. O GUID do locatário está na página de Propriedades do locatário selecionado (https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties), rotulado **ID de diretório**. 
2. Depois de ter o GUID, será necessário adicionar a chave do registro **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<tenant ID GUID>**.
   Por meio da chave do **GUID da ID do locatário**, crie um novo valor de cadeia de caracteres múltipla (REG-MULTI-SZ) denominado **AllowedRMSServerUrls**. Para seus dados, especifique as URLs de ponto de distribuição licenciamento de outros locatários do Azure que o dispositivo acessa.
3. Você pode encontrar as URLs do ponto de distribuição de licenciamento executando o cmdlet **Get-AadrmConfiguration** do módulo AADRM. Se os valores de **LicensingIntranetDistributionPointUrl** e de **LicensingExtranetDistributionPointUrl** forem diferentes, especifique ambos os valores. Se os valores forem iguais, especifique o valor apenas uma vez.

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Quais são as opções de configurações de roaming para aplicativos da área de trabalho do Windows existentes?

O roaming só funciona para aplicativos Universais do Windows. Há duas opções para habilitar o roaming em um aplicativo da área de trabalho existente do Windows:

* A [Ponte da Área de Trabalho](/windows/msix/desktop/source-code-overview) ajuda você a levar seus aplicativos da área de trabalho existentes do Windows para a Plataforma Universal do Windows. A partir daqui, alterações mínimas de código deverão poder aproveitar o roaming de dados de aplicativo do Azure AD. A Ponte da Área de Trabalho fornece aos aplicativos uma identidade de aplicativo, que é necessária para habilitar o roaming de dados de aplicativo para aplicativos da área de trabalho existentes.
* [UE-V (User Experience Virtualization)](/previous-versions//dn458947(v=vs.85)) ajuda você a criar um modelo de configurações personalizado para aplicativos da área de trabalho existentes do Windows e habilitar o roaming para aplicativos do Win32. Essa opção não exige que o desenvolvedor altere o código do aplicativo. O UE-V é limitado ao roaming do Active Directory local para clientes que adquiriram o Microsoft Desktop Optimization Pack.

Administradores podem configurar o UE-V para fazer roaming de dados de aplicativo da área de trabalho do Windows alterando o roaming de configurações do sistema operacional do Windows e dados de aplicativos universais por meio de [políticas de grupo do UE-V](/microsoft-desktop-optimization-pack/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2), incluindo:

* Política de grupo Roaming de configurações do Windows
* Política de grupo Não sincronizar aplicativos do Windows
* Roaming do Internet Explorer na seção de aplicativos

No futuro, a Microsoft poderá investigar maneiras de tornar a UE-V ainda mais integrada ao Windows e de estender a UE-V para mover as configurações por meio da nuvem do AD do Azure.

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>Posso armazenar configurações e dados sincronizados no local?

O Enterprise State Roaming armazena todos os dados sincronizados no Microsoft Cloud. O UE-V oferece uma solução de roaming local.

## <a name="who-owns-the-data-thats-being-roamed"></a>A quem pertencem os dados que estão sendo movidos?

As empresas possuem os dados movidos por meio de Enterprise State Roaming. Os dados são armazenados em um datacenter do Azure. Todos os dados de usuário são criptografados em trânsito e em repouso na nuvem usando o serviço do Microsoft Azure Rights Management da Proteção de Informações do Microsoft Azure. Isso é uma melhoria em comparação à sincronização de configurações baseada em conta da Microsoft, que criptografa apenas determinados dados confidenciais, como credenciais de usuários, antes de saírem do dispositivo.

A Microsoft está comprometida em proteger os dados do cliente. Os dados de configurações do usuário de uma empresa são automaticamente criptografados pelo Azure Rights Management antes de deixar um dispositivo Windows 10, para que outro usuário não possa lê-los. Se sua organização tiver uma assinatura paga do Azure Rights Management, você poderá usar outros recursos de proteção, como rastrear e revogar documentos automaticamente, proteger emails com informações confidenciais e gerenciar suas próprias chaves (a solução "traga sua própria chave", também conhecida como BYOK). Para saber mais informações sobre esses recursos e como esse serviço de proteção funciona, consulte [O que é o Azure Rights Management](/azure/information-protection/what-is-information-protection).

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>Posso gerenciar a sincronização para um aplicativo ou uma configuração específica?

No Windows 10, não há configurações do MDM ou da Política de Grupo para desabilitar o roaming de um aplicativo individual. Os administradores de locatários podem desabilitar a sincronização de dados de aplicativos para todos os aplicativos em um dispositivo gerenciado, mas não há controles mais refinados por aplicativo ou ao nível do aplicativo.

## <a name="how-can-i-enable-or-disable-roaming"></a>Como habilitar ou desabilitar o roaming?

No aplicativo **Configurações**, vá para **Contas** > **Sincronizar configurações**. Nesta página, você pode ver qual conta está sendo usada para mover configurações e pode habilitar ou desabilitar grupos individuais de configurações a serem movidos.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>Qual é a recomendação da Microsoft para habilitar o roaming no Windows 10?

A Microsoft tem algumas soluções de roaming de configurações diferentes disponíveis, incluindo Perfis de Usuário de Roaming, UE-V e Enterprise State Roaming.  A Microsoft está comprometida em fazer um investimento em Enterprise State Roaming em versões futuras do Windows. Caso sua organização não esteja pronta ou confortável para movimentar dados para a nuvem, recomendamos que você use a UE-V como sua principal tecnologia de roaming. Se sua organização requer suporte de roaming para aplicativos da área de trabalho do Windows, mas está ansiosa para mudar para a nuvem, recomendamos que você use o Enterprise State Roaming e o UE-V. Embora a UE-V e o Roaming de Estado da Empresa sejam tecnologias muito semelhantes, eles não são mutuamente exclusivos. Eles se complementam para ajudar a garantir que sua organização forneça os serviços móveis que precisam de seus usuários.  

Ao se usar o Enterprise State Roaming e o UE-V, as seguintes regras são aplicáveis:

* O Enterprise State Roaming é o principal agente de roaming no dispositivo. A UE-V está sendo usada para suplementar a "lacuna do Win32".
* O roaming da UE-V para as configurações do Windows e os dados de aplicativo UWP modernos devem ser desabilitados ao usar as políticas de grupo do UE-V. Eles já estão cobertos pelo Roaming de Estado de Empresa.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>Como o Roaming de Estado de Empresa dá suporte ao VDI (Virtual Desktop Infrastructure)?

O Roaming de Estado de Empresa tem suporte em SKUs de cliente do Windows 10, mas não em SKUs de servidor. Se uma VM cliente for hospedada em um computador de hipervisor e você se conectar remotamente à máquina virtual, seus dados farão roaming. Se vários usuários compartilharem o mesmo sistema operacional e os usuários se conectarem remotamente a um servidor para obter uma experiência de área de trabalho completa, o roaming poderá não funcionar. O cenário com base na segunda sessão não tem suporte oficial.

## <a name="what-happens-when-my-organization-purchases-a-subscription-that-includes-azure-rights-management-after-using-roaming"></a>O que acontece quando a minha organização adquire uma assinatura que inclui o Azure Rights Management após o uso de roaming?

Se sua organização já estiver usando o roaming no Windows 10 com a assinatura gratuita de uso limitado do Azure Rights Management, a compra de uma assinatura paga do [Azure Rights Management](https://azure.microsoft.com/pricing/details/information-protection/) que inclui o serviço de proteção do Azure Rights Management que não terá qualquer impacto na funcionalidade do recurso de roaming e nenhuma alteração de configuração será exibida pelo administrador de TI.

## <a name="known-issues"></a>Problemas conhecidos

Consulte a documentação na seção de [solução de problemas](enterprise-state-roaming-troubleshooting.md) para obter uma lista de problemas conhecidos. 

## <a name="next-steps"></a>Próximas etapas 

Para obter uma visão geral, consulte [visão geral de roaming de estado empresarial](enterprise-state-roaming-overview.md)