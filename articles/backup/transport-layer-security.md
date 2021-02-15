---
title: Segurança da camada de transporte no backup do Azure
description: Saiba como habilitar o backup do Azure para usar o protocolo TLS para manter os dados seguros ao serem transferidos por uma rede.
ms.topic: conceptual
ms.date: 11/01/2020
ms.openlocfilehash: ba9c9d91f562f54695a0739908c8a409d14d5852
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327110"
---
# <a name="transport-layer-security-in-azure-backup"></a>Segurança da camada de transporte no backup do Azure

TLS é um protocolo de criptografia que mantém os dados seguros ao serem transferidos por uma rede. O backup do Azure usa a segurança da camada de transporte para proteger a privacidade dos dados de backup que estão sendo transferidos. Este artigo descreve as etapas para habilitar o protocolo TLS 1,2, que fornece segurança aprimorada em relação às versões anteriores.

## <a name="earlier-versions-of-windows"></a>Versões anteriores do Windows

Se o computador estiver executando versões anteriores do Windows, as atualizações correspondentes indicadas abaixo deverão ser instaladas e as alterações de registro documentadas nos artigos da base de conhecimento deverão ser aplicadas.

|Sistema operacional  |artigos da base de dados |
|---------|---------|
|Windows Server 2008 SP2   |   <https://support.microsoft.com/help/4019276>      |
|Windows Server 2008 R2, Windows 7, Windows Server 2012   | <https://support.microsoft.com/help/3140245>         |

>[!NOTE]
>A atualização instalará os componentes de protocolo necessários. Após a instalação, você deve fazer as alterações da chave do registro mencionadas nos artigos da base de conhecimento acima para habilitar corretamente os protocolos necessários.

## <a name="verify-windows-registry"></a>Verificar registro do Windows

### <a name="configuring-schannel-protocols"></a>Configurando protocolos SChannel

As seguintes chaves do registro garantem que o protocolo TLS 1,2 esteja habilitado no nível do componente SChannel:

```reg
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "Enabled"=dword:00000001

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "DisabledByDefault"=dword:00000000
```

>[!NOTE]
>Os valores mostrados são definidos por padrão no Windows Server 2012 R2 e em versões mais recentes. Para essas versões do Windows, se as chaves do registro estiverem ausentes, elas não precisarão ser criadas.

### <a name="configuring-net-framework"></a>Configurando .NET Framework

As chaves de registro a seguir configuram .NET Framework para dar suporte à criptografia forte. Você pode ler mais sobre como [configurar .NET Framework aqui](/dotnet/framework/network-programming/tls#configuring-schannel-protocols-in-the-windows-registry).

```reg
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001
```

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="why-enable-tls-12"></a>Por que habilitar o TLS 1,2?

O TLS 1,2 é mais seguro do que os protocolos de criptografia anteriores, como SSL 2,0, SSL 3,0, TLS 1,0 e TLS 1,1. Os serviços de backup do Azure já dão suporte total a TLS 1,2.

### <a name="what-determines-the-encryption-protocol-used"></a>O que determina o protocolo de criptografia usado?

A versão de protocolo mais alta com suporte do cliente e do servidor é negociada para estabelecer a conversa criptografada. Para obter mais informações sobre o protocolo de handshake de TLS, consulte [estabelecendo uma sessão segura usando TLS](/windows/win32/secauthn/tls-handshake-protocol#establishing-a-secure-session-by-using-tls).

### <a name="what-is-the-impact-of-not-enabling-tls-12"></a>Qual é o impacto de não habilitar o TLS 1,2?

Para melhorar a segurança dos ataques de downgrade de protocolo, o backup do Azure está começando a desabilitar as versões de TLS mais antigas do que 1,2 de uma maneira em fases. Isso faz parte de uma mudança de longo prazo entre serviços para não permitir conexões de protocolo herdado e do pacote de criptografia. Os componentes e serviços de backup do Azure dão suporte total a TLS 1,2. No entanto, as versões do Windows sem atualizações necessárias ou determinadas configurações personalizadas ainda podem impedir a oferta de protocolos TLS 1,2. Isso pode causar falhas, incluindo, mas não se limitando a um ou mais dos seguintes:

- As operações de backup e restauração podem falhar.
- Os componentes de backup falham em conexões com o erro 10054 (uma conexão existente foi fechada de modo forçado pelo host remoto).
- Os serviços relacionados ao backup do Azure não serão interrompidos ou iniciados normalmente.

## <a name="additional-resources"></a>Recursos adicionais

- [Protocolo de segurança da camada de transporte](/windows/win32/secauthn/transport-layer-security-protocol)
- [Garantindo o suporte para o TLS 1,2 entre sistemas operacionais implantados](/security/engineering/solving-tls1-problem#ensuring-support-for-tls-12-across-deployed-operating-systems)
- [Práticas recomendadas do protocolo TLS com o .NET Framework](/dotnet/framework/network-programming/tls)