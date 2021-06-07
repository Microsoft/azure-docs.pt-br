---
title: Configurar o monitoramento de MIB do SNMP
description: Você pode executar o monitoramento de integridade do sensor usando SNMP. O sensor responde a consultas SNMP enviadas de um servidor de monitoramento autorizado.
ms.date: 12/14/2020
ms.topic: how-to
ms.openlocfilehash: 1ba52236f65c6c5daba68c67677cdc6adfb699b4
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781663"
---
# <a name="set-up-snmp-mib-monitoring"></a>Configurar o monitoramento de MIB do SNMP

Você pode executar o monitoramento de integridade do sensor usando Simple Network Management Protocol (SNMP). O sensor responde a consultas SNMP enviadas de um servidor de monitoramento autorizado. O monitor SNMP sonda os OIDs do sensor periodicamente (até 50 vezes por segundo).

As versões com suporte SNMP são SNMP v2 ou SNMP v3. O SNMP usa UDP como seu protocolo de transporte com a porta 161 (SNMP).

Antes de começar a configurar o monitoramento SNMP, você precisa abrir a porta UDP 161 no firewall.

## <a name="oids"></a>OID

| Console de gerenciamento e sensor | OID | Formatar | Descrição |
|--|--|--|--|
| Nome do dispositivo | 1.3.6.1.2.1.1.5.0 | DISPLAYSTRING | Nome do dispositivo para o console de gerenciamento local |
| Fornecedor | 1.3.6.1.2.1.1.4.0 | DISPLAYSTRING | Suporte da Microsoft (support.microsoft.com) |
| Plataforma | 1.3.6.1.2.1.1.1.0 | DISPLAYSTRING | Sensor ou console de gerenciamento local |
| Número de série | 1.3.6.1.4.1.9.9.53313.1 | DISPLAYSTRING | Cadeia de caracteres usada pela licença |
| Versão do software | 1.3.6.1.4.1.9.9.53313.2 | DISPLAYSTRING | Cadeia de caracteres de versão completa do Xsense e cadeia de versão completa de gerenciamento |
| Uso da CPU | 1.3.6.1.4.1.9.9.53313.3.1 | GAUGE32 | Indicação de zero a 100 |
| Temperatura da CPU | 1.3.6.1.4.1.9.9.53313.3.2 | DISPLAYSTRING | Indicação de Celsius para zero a 100 com base na entrada do Linux |
| Uso de memória | 1.3.6.1.4.1.9.9.53313.3.3 | GAUGE32 | Indicação de zero a 100 |
| Uso de disco | 1.3.6.1.4.1.9.9.53313.3.4 | GAUGE32 | Indicação de zero a 100 |
| Status do Serviço | 1.3.6.1.4.1.9.9.53313.5 | DISPLAYSTRING | Online ou offline se um dos quatro componentes cruciais estiver inativo |
| Largura de banda | Fora do escopo para 2,4 |  | A largura de banda recebida em cada interface do monitor em Xsense |

   - Chaves não existentes respondem com NULL, HTTP 200, com base em [Stack Overflow](https://stackoverflow.com/questions/51419026/querying-for-non-existing-record-returns-null-with-http-200).
    
   - MIBs relacionados ao hardware (uso da CPU, temperatura da CPU, uso de memória, uso do disco) devem ser testados em todas as arquiteturas e sensores físicos. Espera-se que a temperatura da CPU nas máquinas virtuais não seja aplicável.

Você pode baixar o log que contém todas as consultas SNMP que o sensor recebe, incluindo os dados de conexão e os dados brutos do pacote.

Para definir o monitoramento de integridade SNMP v2:

1. No menu lateral, selecione **configurações do sistema**.

2. No painel **descoberta ativa** , selecione **monitoramento de MIB SNMP** :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false"::: .

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="Edite sua janela SNMP.":::

3. Na seção **hosts permitidos** , selecione **Adicionar host** e insira o endereço IP do servidor que executa o monitoramento de integridade do sistema.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="Insira o endereço IP para os hosts permitidos.":::

4. Na seção **autenticação** , na caixa **cadeia de caracteres da comunidade SNMP v2** , digite a cadeia de caracteres. A cadeia de caracteres da comunidade SNMP pode conter até 32 caracteres e incluir qualquer combinação de caracteres alfanuméricos (letras maiúsculas, letras minúsculas e números). Espaços não são permitidos.

5. Selecione **Salvar**.

Para definir o monitoramento de integridade SNMP V3:

1. No menu lateral, selecione **configurações do sistema**.

2. No painel **descoberta ativa** , selecione **monitoramento de MIB SNMP** :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false"::: .

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="Edite sua janela SNMP.":::

3. Na seção **hosts permitidos** , selecione **Adicionar host** e insira o endereço IP do servidor que executa o monitoramento de integridade do sistema.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="Insira o endereço IP para os hosts permitidos.":::

4. Na seção **autenticação** , defina os seguintes parâmetros:

    | Parâmetro | Descrição |
    |--|--|
    | **Nome de usuário** | O nome de usuário SNMP pode conter até 32 caracteres e incluir qualquer combinação de caracteres alfanuméricos (letras maiúsculas, letras minúsculas e números). Espaços não são permitidos. <br /> <br />O nome de usuário para a autenticação SNMP v3 deve ser configurado no sistema e no servidor SNMP. |
    | **Senha** | Insira uma senha de autenticação que diferencia maiúsculas de minúsculas. A senha de autenticação pode conter de 8 a 12 caracteres e incluir qualquer combinação de caracteres alfanuméricos (letras maiúsculas, letras minúsculas e números). <br /> <br/>O nome de usuário para a autenticação SNMP v3 deve ser configurado no sistema e no servidor SNMP. |
    | **Tipo de autenticação** | Selecione MD5 ou SHA. |
    | **Criptografia** | Selecione DES ou AES. |
    | **Chave Secreta** | A chave deve conter exatamente oito caracteres e incluir qualquer combinação de caracteres alfanuméricos (letras maiúsculas, letras minúsculas e números). |

5. Selecione **Salvar**.

## <a name="see-also"></a>Veja também

[Exportar logs de solução de problemas](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
