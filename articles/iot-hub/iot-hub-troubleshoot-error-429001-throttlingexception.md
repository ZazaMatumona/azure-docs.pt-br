---
title: Solução de problemas do Hub IoT do Azure erro 429001 Limitaçãoexception
description: Entenda como corrigir o erro 429001 Limitaçãoexception
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3095e398d7e5cfe59085144d5bb4e8dc33618064
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "76960692"
---
# <a name="429001-throttlingexception"></a>429001 ExceçãoLimitação

Este artigo descreve as causas e soluções para erros **429001 de limitaçãoexception** .

## <a name="symptoms"></a>Sintomas

Suas solicitações ao Hub IoT falham com o erro **429001 regulaexception**.

## <a name="cause"></a>Causa

[Os limites de limitação](./iot-hub-devguide-quotas-throttling.md) do Hub IOT foram excedidos para a operação solicitada.

## <a name="solution"></a>Solução

Verifique se você está atingindo o limite de limitação comparando sua métrica de *tentativas de envio de mensagem de telemetria* em relação aos limites especificados acima. Você também pode verificar a métrica *número de erros de limitação* . Para obter mais informações sobre essas e outras métricas disponíveis para o Hub IoT, consulte [métricas do Hub IOT e como usá-las](./iot-hub-metrics.md#iot-hub-metrics-and-how-to-use-them).

O Hub IoT retorna 429 Throttlingexception somente depois que o limite tiver sido violado por um período muito longo. Isso é feito para que suas mensagens não sejam descartadas se o Hub IoT obtiver tráfego intermitente. Enquanto isso, o Hub IoT processa as mensagens na taxa de limitação da operação, o que pode ser lento se houver muito tráfego na lista de pendências. Para saber mais, consulte [modelagem de tráfego do Hub IoT](./iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="next-steps"></a>Próximas etapas

Considere [escalar verticalmente o Hub IOT](./iot-hub-scaling.md) se você estiver executando limites de cota ou limitação.