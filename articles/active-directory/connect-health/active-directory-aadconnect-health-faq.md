---
title: Perguntas frequentes sobre o Azure Active Directory Connect Health - Azure | Microsoft Docs
description: "Encontre respostas para perguntas frequentes sobre o Azure AD Connect Health. Estas perguntas frequentes abordam dúvidas sobre como usar o serviço, incluindo o modelo de cobrança, recursos, limitações e suporte."
services: active-directory
documentationcenter: 
author: billmath
manager: samueld
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 902e5bdfbbf04ab70989be8c41e16eb69e475908
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Perguntas frequentes do Azure AD Connect Health
Este artigo inclui respostas para FAQs (perguntas frequentes) sobre o Azure AD (Azure Active Directory) Connect Health. Essas perguntas frequentes abordam perguntas sobre como usar o serviço, o que inclui o modelo de cobrança, os recursos, as limitações e o suporte.

## <a name="general-questions"></a>Perguntas gerais
**P: Eu gerencio vários diretórios do Azure AD. Como mudar para aquele que tem o Azure Active Directory Premium?**

Para mudar entre diferentes locatários do Azure AD, selecione o **Nome de Usuário** conectado atualmente no canto superior direito e escolhendo a conta apropriada. Se a conta não estiver listada aqui, selecione **Sair** e use as credenciais de administrador global do Diretório que tiver o Azure Active Directory Premium habilitado para entrar.

**P: Qual versão de funções de identidade tem suporte do Azure AD Connect Health?**

A tabela a seguir lista as funções e as versões de sistema operacional com suporte.

|Função| Sistema Operacional/Versão|
|--|--|
|Suporte para os Serviços de Federação do Active Directory (AD FS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | Versão 1.0.9125 ou superior|
|Active Directory Domain Services (AD DS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

Observe que os recursos fornecidos pelo serviço mudam de acordo com a função e o sistema operacional. Em outras palavras, talvez nem todos os recursos estejam disponíveis para todas as versões de sistema operacional. Consulte as descrições de recurso para obter detalhes.

**P: quantas licenças é necessário monitorar minha infra-estrutura?**

* O primeiro o Agente do Connect Health requer pelo menos uma licença Azure AD Premium.
* Cada agente registrado adicional requer 25 licenças Azure AD Premium adicionais.
* A contagem de agentes é equivalente ao número total de agentes registrados em todas as funções monitoradas (AD FS, Azure AD Connect e/ou AD DS).

As informações de licenciamento também podem ser encontradas na [página Preços do Azure AD](https://aka.ms/aadpricing).

Exemplo:

| Agentes registrados | Licenças necessárias | Configuração de monitoramento de exemplo |
| ------ | --------------- | --- |
| 1 | 1 | Um servidor de conexão do Azure AD |
| 2 | 26| Um servidor do Azure AD Connect e um controlador de domínio |
| 3 | 51 | Um servidor dos Serviços de Federação do Active Directory (AD FS), um proxy do AD FS e um controlador de domínio |
| 4 | 76 | Um servidor do AD FS, um proxy do AD FS e dois controladores de domínio |
| 5 | 101 | Um servidor do Azure AD Connect, um servidor do AD FS, um proxy AD FS e dois controladores de domínio |


## <a name="installation-questions"></a>Perguntas sobre a instalação

**P: Qual é o impacto da instalação do agente do Azure AD Connect Health sobre servidores individuais?**

O impacto da instalação do AD FS do Agente do Microsoft Azure AD Connect Health, servidores proxy de Aplicativo Web, servidores do Azure AD Connect (sincronização) e de controladores de domínio é mínimo em relação ao consumo de CPU, de memória, à largura de banda de rede e ao armazenamento.

Os números a seguir são uma aproximação:

* Consumo de CPU: aumento de aproximadamente 1 a 5%.
* Consumo de memória: até 10% da memória total do sistema.

> [!NOTE]
> Se o agente não pode se comunicar com o Azure, o agente armazena os dados localmente para um limite máximo definido. O agente substitui os dados em "cache" de forma “atendido menos recentemente”.
>
>

* Armazenamento em buffer local para Agentes do Azure AD Connect Health: aproximadamente 20 MB.
* Para servidores do AD FS, é recomendável que você provisione um espaço em disco de 1.024 MB (1 GB) para o Canal de Auditoria do AD FS, de modo que Agentes do Azure AD Connect Health processem todos os dados de auditoria antes que tais dados sejam substituídos.

**P: Será necessário reinicializar meus servidores durante a instalação dos agentes do Azure AD Connect Health?**

Não. A instalação dos agentes não exigirá a reinicialização do servidor. No entanto, a instalação de algumas das etapas de pré-requisito pode exigir uma reinicialização do servidor.

Por exemplo, no Windows Server 2008 R2, a instalação do .NET Framework 4.5 requer a reinicialização do servidor.

**P: Os Serviços do Azure AD Connect Health funcionam por meio de um proxy HTTP de passagem?**

Sim. Para operações em andamento, você pode configurar o Agente de integridade para usar um proxy HTTP para encaminhar solicitações HTTP de saída.
Leia mais sobre [configurar HTTP Proxy para agentes de integridade](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).

Se precisar configurar um proxy durante o registro do agente, talvez seja necessário modificar as configurações de proxy do Internet Explorer com antecedência.

1. Abra Internet Explorer -> **Configurações** > **Opções de Internet** > **Conexões** > **Configurações da LAN**.
2. Selecione **Usar um Servidor Proxy para a LAN**.
3. Selecione **Avançado** se você tiver portas de proxy diferentes para HTTP e HTTPS/Seguro.

**P: O Azure AD Connect Health dá suporte à autenticação básica ao se conectar a proxies HTTP?**

Não. Um mecanismo para especificar o nome de usuário/senha arbitrariamente para fins de autenticação básica não tem suporte.

**P: Quais portas de firewall eu preciso abrir para o agente do Azure AD Connect Health funcione?**

Veja a [seção de requisitos](active-directory-aadconnect-health-agent-install.md#requirements) para a lista de portas de firewall e outros requisitos de conectividade.

**P: por que vejo dois servidores com o mesmo nome no portal do Azure AD Connect Health?**

Quando você remove um agente de um servidor, o servidor não é automaticamente removido do portal do Azure AD Connect Health. Se você remover um agente manualmente de um servidor ou remover o próprio servidor, precisará excluir manualmente a entrada do servidor no portal do Azure AD Connect Health.

Você pode refazer a imagem de um servidor ou criar um novo servidor com os mesmo detalhes (como o nome do computador). Se você não tiver removido o servidor já registrado do portal do Azure AD Connect Health e instalado o agente no novo servidor, você poderá ver duas entradas com o mesmo nome.

Nesse caso, exclua manualmente a entrada que pertence ao servidor mais antigo. Os dados para esse servidor devem estar desatualizados.

## <a name="health-agent-registration-and-data-freshness"></a>Registro de Agente de Integridade e atualização de dados

**P: Quais são os motivos comuns para as falhas de registro do Agente de Integridade e como solucionar problemas?**

O agente de integridade não conseguirá registrar devido a motivos possíveis:

* O agente não pode se comunicar com os pontos de extremidade necessários porque um firewall está bloqueando o tráfego. Isso é especialmente comum em servidores proxy de aplicativo Web. Cerifique de ter permitido a comunicação de saída para as portas e os pontos de extremidade necessários. Confira a [seção de requisitos](active-directory-aadconnect-health-agent-install.md#requirements) para obter detalhes.
* A comunicação de saída está sujeita a uma inspeção SSL feita pela camada de rede. Isso faz com que o certificado usado pelo agente seja substituído por entidade/servidor de inspeção e ocorre falha nas etapas necessárias para concluir o registro do agente.
* O usuário não tem acesso para executar o registro do agente. Por padrão, os administradores globais têm acesso. Você pode usar [controle de acesso com base em função](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) para delegar acesso a outros usuários.

**P: Estou recebendo o alerta "Os dados do Serviço de Integridade não estão atualizados". Como solucionar esse problema?**

O Azure AD Connect Health gera o alerta quando não recebe todos os pontos de dados do servidor nas últimas duas horas. Pode haver várias razões para esse alerta ser acionado.

* O agente não pode se comunicar com os pontos de extremidade necessários porque um firewall está bloqueando o tráfego. Isso é especialmente comum em servidores proxy de aplicativo Web. Verifique se você permitiu a comunicação de saída para as portas e os pontos de extremidade necessários. Confira a [seção de requisitos](active-directory-aadconnect-health-agent-install.md#requirements) para obter detalhes.
* A comunicação de saída está sujeita a uma inspeção SSL feita pela camada de rede. Isso faz com que o certificado usado pelo agente seja substituído pela entidade/servidor inspeção e o processo falha ao carregar dados para o serviço Azure AD Connect Health.
* Você pode usar o comando de conectividade criado no agente. [Leia mais](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service).
* Os agentes também dão suporte a conectividade de saída por meio de um HTTP Proxy não autenticado. [Leia mais](active-directory-aadconnect-health-agent-install.md##configure-azure-ad-connect-health-agents-to-use-http-proxy).

## <a name="operations-questions"></a>Perguntas sobre operações
**P: Preciso habilitar a auditoria em servidores proxy de aplicativo Web?**

Não, a auditoria não precisa estar habilitada nos servidores proxy de aplicativo Web.

**P: Como Alertas do Azure AD Connect Health são resolvidos?**

Alertas do Azure AD Connect Health são resolvidos em uma condição de êxito. Os agentes do Azure AD Connect Health detectam e relatam as condições de sucesso para o serviço periodicamente. Para alguns alertas, a supressão é periódica. Em outras palavras, se a mesma condição de erro não for observada no período de 72 horas a partir da geração do alerta, este será resolvido automaticamente.

**P: eu estou sendo alertado de que "A Solicitação de Autenticação de Teste (Transação Sintética) falhou ao obter um token." Como solucionar esse problema?**

O Azure AD Connect Health para AD FS gera este alerta quando o Agente de Integridade instalado em um servidor do AD FS falha ao obter um token como parte de uma transação sintética iniciada pelo Agente de Integridade. O agente de Integridade usa o contexto do sistema local e tenta obter um token para uma terceira parte confiável própria. Este é um teste de catch-all para garantir que o AD FS esteja em um estado de emissão de tokens.

Geralmente esse teste falha porque o Agente de Integridade não consegue resolver o nome do farm do AD FS. Isso poderá acontecer se os servidores do AD FS estiverem atrás de um balanceador de carga de rede e a solicitação for iniciada de um nó que esteja atrás do balanceador de carga (ao contrário de um cliente regular que está na frente do balanceador de carga). Isso pode ser corrigido através da atualização do arquivo "hosts" localizado em "C:\Windows\System32\drivers\etc" para incluir o endereço IP do servidor do AD FS ou um endereço IP de loopback (127.0.0.1) para o nome do farm do AD FS (por exemplo, sts.contoso.com). A adição do arquivo de host causará curto-circuito na chamada de rede, permitindo assim que o Agente de Integridade obtenha o token.

**P: recebi um email indicando que meus computadores NÃO têm patches para os ataques de ransomeware recentes. Por que eu recebi esse email?**

O serviço Azure AD Connect Health verificou todos os computadores que ele monitora para garantir que os patches necessários fossem instalados. O email foi enviado aos administradores de locatário se um ou mais computadores não tinham os patches críticos. A seguinte lógica foi usada para fazer essa determinação.
1. Localizar todos os hotfixes instalados no computador.
2. Verificar se pelo menos um dos HotFixes da lista definida está presente.
3. Se Sim, o computador está protegido. Se Não, o computador está correndo risco de ataque.

Você pode usar o seguinte script do PowerShell para executar essa verificação manualmente. Ele implementa a lógica acima.

```
Function CheckForMS17-010 ()
{
    $hotfixes = "KB3205409", "KB3210720", "KB3210721", "KB3212646", "KB3213986", "KB4012212", "KB4012213", "KB4012214", "KB4012215", "KB4012216", "KB4012217", "KB4012218", "KB4012220", "KB4012598", "KB4012606", "KB4013198", "KB4013389", "KB4013429", "KB4015217", "KB4015438", "KB4015546", "KB4015547", "KB4015548", "KB4015549", "KB4015550", "KB4015551", "KB4015552", "KB4015553", "KB4015554", "KB4016635", "KB4019213", "KB4019214", "KB4019215", "KB4019216", "KB4019263", "KB4019264", "KB4019472", "KB4015221", "KB4019474", "KB4015219", "KB4019473"

    #checks the computer it's run on if any of the listed hotfixes are present
    $hotfix = Get-HotFix -ComputerName $env:computername | Where-Object {$hotfixes -contains $_.HotfixID} | Select-Object -property "HotFixID"

    #confirms whether hotfix is found or not
    if (Get-HotFix | Where-Object {$hotfixes -contains $_.HotfixID})
    {
        "Found HotFix: " + $hotfix.HotFixID
    } else {
        "Didn't Find HotFix"
    }
}

CheckForMS17-010

```



## <a name="related-links"></a>Links relacionados
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalação do Agente do Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operações de Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Usando o Azure AD Connect Health com o AD FS](active-directory-aadconnect-health-adfs.md)
* [Usando o Azure AD Connect Health para sincronização](active-directory-aadconnect-health-sync.md)
* [Usar o Azure AD Connect Health com o AD DS](active-directory-aadconnect-health-adds.md)
* [Histórico de versão do Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)
