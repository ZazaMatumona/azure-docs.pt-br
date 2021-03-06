---
title: Ações do mecanismo de regras do Azure Front Door
description: Este artigo fornece uma lista das várias ações que você pode executar com o mecanismo de regras do Azure Front Door.
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/30/2020
ms.author: mebeatty
ms.openlocfilehash: 74c0a2617a01e8c24cd93a015b667081250657ad
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86521489"
---
# <a name="azure-front-door-rules-engine-actions"></a>Ações do mecanismo de regras do Azure Front Door

No [Mecanismo de regras do AFD](front-door-rules-engine.md) uma regra consiste em zero ou mais condições de correspondência e ações. Este artigo fornece descrições detalhadas das ações que você pode usar no mecanismo de regras do AFD.

Uma ação define o comportamento que é aplicado ao tipo de solicitação que uma condição de correspondência ou conjunto de condições de correspondência identifica. No mecanismo de regras do AFD, uma regra pode conter até cinco ações, apenas uma das quais pode ser uma ação de substituição de configuração de rota (encaminhar ou redirecionar).

As ações a seguir estão disponíveis para uso no mecanismo de regras do Azure Front Door.  

## <a name="modify-request-header"></a>Modificar o cabeçalho de solicitação

Use esta ação para modificar os cabeçalhos que estão presentes nas solicitações enviadas para sua origem.

### <a name="required-fields"></a>Campos obrigatórios

Ação | Nome do cabeçalho HTTP | Valor
-------|------------------|------
Acrescentar | Quando essa opção é selecionada e a regra corresponde, o cabeçalho especificado em **Nome do cabeçalho** é adicionado à solicitação com o valor especificado. Se o cabeçalho já estiver presente, o valor será anexado ao valor existente. | String
Overwrite | Quando essa opção é selecionada e a regra corresponde, o cabeçalho especificado em **Nome do cabeçalho** é adicionado à solicitação com o valor especificado. Se o cabeçalho já estiver presente, o valor especificado substituirá o valor existente. | String
Excluir | Quando essa opção é selecionada, a regra corresponde e o cabeçalho especificado na regra está presente, o cabeçalho é excluído da solicitação. | String

## <a name="modify-response-header"></a>Modificar o cabeçalho de resposta

Use essa ação para modificar os cabeçalhos que estão presentes nas respostas retornadas aos clientes.

### <a name="required-fields"></a>Campos obrigatórios

Ação | Nome do cabeçalho HTTP | Valor
-------|------------------|------
Acrescentar | Quando essa opção é selecionada e a regra corresponde, o cabeçalho especificado em **Nome do cabeçalho** é adicionado à resposta usando o **Valor** especificado. Se o cabeçalho já estiver presente, **Valor** será anexado ao valor existente. | String
Overwrite | Quando essa opção é selecionada e a regra corresponde, o cabeçalho especificado em **Nome do cabeçalho** é adicionado à resposta usando o **Valor** especificado. Se o cabeçalho já estiver presente, **Valor** substituirá o valor existente. | String
Excluir | Quando essa opção é selecionada, a regra corresponde e o cabeçalho especificado na regra está presente, o cabeçalho é excluído da resposta. | String

## <a name="route-configuration-overrides"></a>Substituições de configuração de rota 

### <a name="route-type-redirect"></a>Tipo de Rota: Redirecionar

Use esta ação para redirecionar clientes para uma nova URL. 

#### <a name="required-fields"></a>Campos obrigatórios

Campo | Descrição 
------|------------
Tipo de redirecionamento | Selecione o tipo de resposta para retornar ao solicitante: Encontrado (302), Movido (301), Redirecionamento temporário (307) e Redirecionamento permanente (308).
Protocolo de redirecionamento | Solicitação de correspondência, HTTP, HTTPS.
Host de destino | Selecione o nome do host para o qual você deseja que a solicitação seja redirecionada. Deixe em branco para preservar o host de entrada.
Caminho de destino | Defina o caminho a ser usado no redirecionamento. Deixe em branco para preservar o caminho de entrada.  
Cadeia de consulta | Defina a cadeia de consulta usada no redirecionamento. Deixe em branco para preservar a cadeia de consulta de entrada. 
Fragmento de destino | Defina o fragmento a ser usado no redirecionamento. Deixe em branco para preservar o fragmento de entrada. 


### <a name="route-type-forward"></a>Tipo de Rota: Avançar

Use esta ação para encaminhar clientes para uma nova URL. Essa ação também contém subações para regravações de URL e cache. 

Campo | Descrição 
------|------------
Pool de back-end | Selecione o pool de back-end do qual substituir e atender as solicitações. Isso mostrará todos os pools de back-end pré-configurados atualmente em seu perfil do Front Door. 
Protocolo de encaminhamento | Solicitação de correspondência, HTTP, HTTPS.
Reconfiguração de URL | Use essa ação para reescrever o caminho de uma solicitação que é roteada para sua origem. Se habilitada, confira abaixo os campos adicionais necessários
Cache | Habilitado, Desabilitado. Confira abaixo os campos adicionais necessários, se habilitada. 

#### <a name="url-rewrite"></a>Reconfiguração de URL

Use essa configuração para configurar um **Caminho de Encaminhamento Personalizado** opcional para usar ao construir a solicitação para encaminhar para o back-end.

Campo | Descrição 
------|------------
Caminho de encaminhamento personalizado | Defina o caminho para o qual encaminhar as solicitações. 

#### <a name="caching"></a>Cache

Use essas configurações para controlar como os arquivos são armazenados em cache para solicitações que contêm cadeias de consulta e se deseja armazenar em cache o conteúdo com base em todos os parâmetros ou nos parâmetros selecionados. Você pode usar configurações adicionais para substituir o valor TTL (vida útil) para controlar por quanto tempo o conteúdo permanece no cache para solicitações que as regras correspondem às condições especificar. Para forçar o armazenamento em cache como uma ação, defina o campo de cache como "Habilitado". Quando você fizer isso, as seguintes opções serão exibidas: 

Comportamento do cache |  Descrição              
---------------|----------------
Ignorar as cadeias de caracteres de consulta | Depois que o ativo é armazenado em cache, todas as solicitações subsequentes ignoram as cadeias de consulta até que o ativo em cache expire.
Armazenar em cache todas as URLs exclusivas | Cada solicitação com um URL exclusiva, incluindo a cadeia de caracteres de consulta, é tratada como um ativo exclusivo com um cache próprio.
Ignorar as cadeias de caracteres de consulta especificadas | As cadeias de consulta da URL de solicitação listadas na configuração "parâmetros de consulta" são ignoradas para cache.
Incluir as cadeias de caracteres de consulta especificadas | As cadeias de consulta da URL de solicitação listadas na configuração "parâmetros de consulta" são usadas para cache.

Campos adicionais |  Descrição 
------------------|---------------
Compactação dinâmica | O Front Door pode compactar dinamicamente conteúdo na borda, resultando em uma resposta menor e mais rápida.
Parâmetros de consulta | Uma lista separada por vírgula de parâmetros permitidos (ou não permitidos) a serem usados como base para cache.
Duração do cache | Duração do término do cache em dias, horas, minutos, segundos. Todos os valores devem ser Int. 

## <a name="next-steps"></a>Próximas etapas

- Saiba como definir sua primeira [Configuração do mecanismo de regras](front-door-tutorial-rules-engine.md). 
- Saiba mais sobre as [condições de correspondência do Mecanismo de regras](front-door-rules-engine-match-conditions.md)
- Saiba mais sobre o [Mecanismo de regras do Azure Front Door](front-door-rules-engine.md)
