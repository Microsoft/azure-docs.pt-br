---
title: Recomendações de segurança para as imagens do Azure Marketplace | Microsoft Docs
description: Este artigo fornece recomendações para as imagens incluídas no Marketplace
services: security
documentationcenter: na
author: terrylanfear
manager: barbkess
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: terrylan
ms.openlocfilehash: 7c317a0b4fea0c981b227bace00c1b8924fd582c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89536375"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Recomendações de segurança para as imagens do Azure Marketplace

Sua imagem deve atender a essas recomendações de configuração de segurança. Isso ajuda a manter um alto nível de segurança para as imagens de solução de parceiro no Azure Marketplace.

Sempre execute uma detecção de vulnerabilidade de segurança em sua imagem antes de enviá-la. Se você detectar uma vulnerabilidade de segurança em sua própria imagem publicada, deverá informar aos clientes em tempo hábil, tanto da vulnerabilidade quanto de como corrigi-la.

## <a name="open-source-based-images"></a>Abrir imagens com base na origem

| Categoria | Verificação |
| -------- | ----- |
| Segurança                                                     | Instale todos os patches de segurança mais recentes para a distribuição do Linux.                                                                                                                                                                                                              |
| Segurança                                                     | Siga as diretrizes do setor para proteger a imagem da VM para a distribuição específica do Linux.                                                                                                                                                                                     |
| Segurança                                                     | Limite a superfície de ataque mantendo somente o volume mínimo necessário das funções, recursos, serviços e portas de rede do Windows Server.                                                                                                                                               |
| Segurança                                                     | Faça a verificação do código-fonte e da imagem da VM em relação à presença de malware.                                                                                                                                                                                                                                   |
| Segurança                                                     | A imagem VHD inclui apenas as contas bloqueadas necessárias que não têm senhas padrão que permitiriam logon interativo; sem back doors.                                                                                                                                           |
| Segurança                                                     | Desabilite as regras de firewall, a menos que o aplicativo dependa delas, como um dispositivo de firewall.                                                                                                                                                                             |
| Segurança                                                     | Remova todas as informações confidenciais da imagem do VHD, como chaves SSH de teste, arquivo de hosts conhecidos, arquivos de log e certificados desnecessários.                                                                                                                                       |
| Segurança                                                     | Evite usar o LVM.                                                                                                                                                                                                                                            |
| Segurança                                                     | Inclua as versões mais recentes das bibliotecas necessárias: </br> - OpenSSL v1.0 ou superior </br> - Python 2.5 ou superior (Python 2.6+ é altamente recomendável) </br> - Pacote pyasn1 do Python, se já não estiver instalado </br> - d.OpenSSL v 1.0 ou superior                                                                |
| Segurança                                                     | Limpar entradas de histórico de bash/shell.                                                                                                                                                                                                                                             |
| Rede                                                   | Inclua o servidor SSH por padrão. Defina SSH Keep Alive para sshd config com a seguinte opção: ClientAliveInterval 180.                                                                                                                                                        |
| Rede                                                   | Remova qualquer configuração de rede personalizada da imagem. Exclua o botão de resolução. conf: `rm /etc/resolv.conf` .                                                                                                                                                                                |
| Implantação                                                   | Instale o agente Linux do Azure mais recente.</br> -Instale usando o pacote RPM ou Deb.  </br> - Você também pode usar o processo de instalação manual, mas os pacotes do instalador são recomendados e preferenciais. </br> - Se estiver instalando o agente manualmente por meio do repositório GitHub, primeiro copie o arquivo `waagent` para `/usr/sbin` e execute (como raiz): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>O arquivo de configuração do agente é colocado em `/etc/waagent.conf`. |
| Implantação                                                   | Verifique se o suporte do Azure pode fornecer aos nossos parceiros a saída do console serial quando necessário e fornecer tempo limite adequado para a montagem de disco do sistema operacional do armazenamento em nuvem. Adicione os seguintes parâmetros à linha de inicialização do kernel de imagem: `console=ttyS0 earlyprintk=ttyS0 rootdelay=300` . |
| Implantação                                                   | Não troque a partição no disco do sistema operacional. A troca pode ser solicitada para a criação no disco de recurso local pelo Agente do Linux.         |
| Implantação                                                   | Crie uma única partição raiz para o disco do sistema operacional.      |
| Implantação                                                   | Somente sistema operacional de 64 bits.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Imagens baseadas no Windows Server

| Categoria | Verificação |
|--------- | ----- |
| Segurança                                                         | Use uma imagem de base segura do sistema operacional. O VHD usado para a origem de qualquer imagem baseada no Windows Server deve estar entre as imagens do sistema operacional do Windows Server fornecidas pelo Microsoft Azure. |
| Segurança                                                         | Instale todas as últimas atualizações de segurança.                                                                                                                                     |
| Segurança                                                         | Os aplicativos não devem depender de nomes de usuário restritos como administrador, raiz ou administrador.                                                                |
| Segurança                                                         | Habilite Criptografia de Unidade de Disco BitLocker para discos rígidos de so e discos rígidos de dados.                                                             |
| Segurança                                                         | Limite a superfície de ataque mantendo somente o volume mínimo necessário das funções, recursos, serviços e portas de rede do Windows Server habilitado.                         |
| Segurança                                                         | Faça a verificação do código-fonte e da imagem da VM em relação à presença de malware.                                                                                                                     |
| Segurança                                                         | Configure a atualização de segurança de imagens do Windows Server para atualização automática.                                                                                                                |
| Segurança                                                         | A imagem VHD inclui apenas as contas bloqueadas necessárias que não têm senhas padrão que permitiriam logon interativo; sem back doors.                             |
| Segurança                                                         | Desabilite as regras de firewall, a menos que o aplicativo dependa delas, como um dispositivo de firewall.                                                               |
| Segurança                                                         | Remova todas as informações confidenciais da imagem do VHD, incluindo arquivos de HOSTs, arquivos de log e certificados desnecessários.                                              |
| Implantação                                                       | Somente sistema operacional de 64 bits.                            |

Mesmo que sua organização não tenha imagens no Azure Marketplace, considere verificar as configurações de imagem do Windows e do Linux em relação a essas recomendações.

