---
title: Identidade do dispositivo e virtualização de área de trabalho-Azure Active Directory
description: Saiba como as identidades do dispositivo VDI e do Azure AD podem ser usadas juntas
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: c30ad26f079e6353dc4763b9ae968c33882d8ab6
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029340"
---
# <a name="device-identity-and-desktop-virtualization"></a>Identidade do dispositivo e virtualização de área de trabalho

Os administradores normalmente implantam plataformas de VDI (Virtual Desktop Infrastructure) que hospedam sistemas operacionais Windows em suas organizações. Os administradores implantam o VDI em:

- Simplifique o gerenciamento.
- Reduza os custos por meio de consolidação e centralização de recursos.
- Entregue a mobilidade dos usuários finais e a liberdade de acessar áreas de trabalho virtuais a qualquer momento, de qualquer lugar, em qualquer dispositivo.

Há dois tipos principais de áreas de trabalho virtuais:

- Persistente
- Não-Persistente

As versões persistentes usam uma imagem de área de trabalho exclusiva para cada usuário ou um pool de usuários. Essas áreas de trabalho exclusivas podem ser personalizadas e salvas para uso futuro. 

Versões não persistentes usam uma coleção de áreas de trabalho que os usuários podem acessar de acordo com a necessidade. Esses desktops não persistentes são revertidos para seu estado original, no caso do Windows atual<sup>1</sup> isso acontece quando uma máquina virtual passa por um processo de desligamento/reinicialização/redefinição de so e, no caso do Windows, no nível<sup>2</sup> , isso acontece quando um usuário se desconecta.

Houve um aumento nas implantações de VDI não persistentes, pois o trabalho remoto continua sendo a nova norma. À medida que os clientes implantam o VDI não persistente, é importante garantir que você gerencie a rotatividade de dispositivos que podem ser causados devido ao registro frequente do dispositivo sem ter uma estratégia adequada para o gerenciamento do ciclo de vida do dispositivo.

> [!IMPORTANT]
> Falha ao gerenciar a rotatividade de dispositivos, pode levar ao aumento de pressão no consumo de uso de cota do locatário e no risco potencial de interrupção do serviço, se você ficar sem cota de locatário. Você deve seguir as diretrizes documentadas abaixo ao implantar ambientes de VDI não persistentes para evitar essa situação.

Este artigo abordará as diretrizes da Microsoft para os administradores sobre suporte para a identidade do dispositivo e VDI. Para obter mais informações sobre a identidade do dispositivo, consulte o artigo [o que é uma identidade de dispositivo](overview.md).

## <a name="supported-scenarios"></a>Cenários com suporte

Antes de configurar as identidades de dispositivo no Azure AD para seu ambiente de VDI, familiarize-se com os cenários com suporte. A tabela a seguir ilustra quais cenários de provisionamento têm suporte. O provisionamento neste contexto implica que um administrador pode configurar identidades de dispositivo em escala sem a necessidade de qualquer interação do usuário final.

| Tipo de identidade do dispositivo | Infraestrutura da identidade | Dispositivos Windows | Versão da plataforma VDI | Com suporte |
| --- | --- | --- | --- | --- |
| Adicionado ao Azure AD híbrido | Federado<sup>3</sup> | Windows atual e Windows de baixo nível | Persistente | Yes |
|   |   | Atual do Windows | Não persistente | Sim<sup>5</sup> |
|   |   | Nível inferior do Windows | Não persistente | Sim<sup>6</sup> |
|   | Gerenciado<sup>4</sup> | Windows atual e Windows de baixo nível | Persistente | Yes |
|   |   | Atual do Windows | Não persistente | No |
|   |   | Nível inferior do Windows | Não persistente | Sim<sup>6</sup> |
| Adicionado ao Azure AD | Federado | Atual do Windows | Persistente | No |
|   |   |   | Não persistente | No |
|   | Gerenciado | Atual do Windows | Persistente | No |
|   |   |   | Não persistente | No |
| Azure AD registrado | Federado/gerenciado | Nível inferior do Windows/atual | Persistente/não persistente | Não Aplicável |

<sup>1</sup> os dispositivos **atuais do Windows** representam o Windows 10, o Windows Server 2016 v1803 ou superior e o Windows Server 2019.
<sup>2</sup> dispositivos **de nível inferior do Windows** representam o windows 7, o Windows 8.1, o Windows Server 2008 R2, o Windows Server 2012 e o Windows Server 2012 R2. Para obter informações de suporte sobre o Windows 7, consulte o [suporte para o Windows 7 está terminando](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support). Para obter informações de suporte sobre o Windows Server 2008 R2, consulte [preparar para o fim do suporte do Windows server 2008](https://www.microsoft.com/cloud-platform/windows-server-2008).

<sup>3</sup> um ambiente de infraestrutura de identidade **federada** representa um ambiente com um provedor de identidade, como AD FS ou outros IDP de terceiros.

<sup>4</sup> um ambiente de infraestrutura de identidade **gerenciada** representa um ambiente com o Azure ad como o provedor de identidade implantado com o [PHS (sincronização de hash de senha)](../hybrid/whatis-phs.md) ou [PTA (autenticação de passagem)](../hybrid/how-to-connect-pta.md) com [logon único contínuo](../hybrid/how-to-connect-sso.md).

<sup>5</sup> o **suporte de não persistência para o Windows atual** requer considerações adicionais, conforme documentado abaixo na seção de diretrizes. Este cenário requer o Windows 10 1803, o Windows Server 2019 ou o Windows Server (canal semestral) iniciando a versão 1803

<sup>6</sup> o **suporte de não persistência para o Windows de nível inferior** requer considerações adicionais, conforme documentado abaixo na seção de diretrizes.


## <a name="microsofts-guidance"></a>Diretrizes da Microsoft

Os administradores devem referenciar os artigos a seguir, com base em sua infraestrutura de identidade, para saber como configurar o ingresso no Azure AD híbrido.

- [Configurar a junção de Azure Active Directory híbrida para o ambiente federado](hybrid-azuread-join-federated-domains.md)
- [Configurar a junção de Azure Active Directory híbrida para o ambiente gerenciado](hybrid-azuread-join-managed-domains.md)

Ao implantar o VDI não persistente, a Microsoft recomenda que os administradores de ti implementem as diretrizes abaixo. Se você não fizer isso, o diretório terá muitos dispositivos ingressados no Azure AD híbridos que foram registrados a partir de sua plataforma VDI não persistente, resultando em maior pressão na cota do locatário e no risco de interrupção do serviço devido à falta de cota de locatário.

- Se você estiver contando com a ferramenta de preparação do sistema (sysprep.exe) e se estiver usando uma imagem anterior ao Windows 10 1809 para instalação, verifique se a imagem não é de um dispositivo que já está registrado com o Azure AD como ingressado no Azure AD híbrido.
- Se você estiver contando com um instantâneo de VM (máquina virtual) para criar VMs adicionais, verifique se o instantâneo não é de uma VM que já está registrada com o Azure AD como uma junção híbrida do Azure AD.
- Crie e use um prefixo para o nome de exibição (por exemplo, NPVDI-) do computador que indica a área de trabalho como baseada em VDI não persistente.
- Para o Windows de nível inferior:
   - Implemente o comando **autoworkplacejoin/Leave** como parte do script de logoff. Esse comando deve ser disparado no contexto do usuário e deve ser executado antes que o usuário seja completamente desconectado e enquanto ainda há conectividade de rede.
- Para Windows atual em um ambiente federado (por exemplo, AD FS):
   - Implemente **dsregcmd/Join** como parte da sequência de inicialização da VM.
   - **Não** execute dsregcmd/Leave como parte do processo de desligamento/reinicialização da VM.
- Defina e implemente o processo de [Gerenciamento de dispositivos obsoletos](manage-stale-devices.md).
   - Depois que você tiver uma estratégia para identificar seus dispositivos ingressados no Azure AD híbridos não persistentes (por exemplo, usando o prefixo do nome de exibição do computador), você deve ser mais agressivo na limpeza desses dispositivos para garantir que seu diretório não seja consumido com muitos dispositivos obsoletos.
   - Para implantações não persistentes do VDI no nível atual e inferior do Windows, você deve excluir dispositivos com **ApproximateLastLogonTimestamp** de mais de 15 dias.
 
## <a name="next-steps"></a>Próximas etapas

[Configurando a junção de Azure Active Directory híbrida para o ambiente federado](hybrid-azuread-join-federated-domains.md)
