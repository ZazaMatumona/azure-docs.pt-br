---
title: Conectar dados de gerenciamento de vulnerabilidade do Qualys ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados de gerenciamento de vulnerabilidade do Qualys ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 34f2cfa06cbdbb75b8fd610cd1f76fb33dde4cde
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87059854"
---
# <a name="connect-your-qualys-vm-to-azure-sentinel-with-azure-function"></a>Conectar sua VM Qualys ao Azure Sentinel com o Azure function

> [!IMPORTANT]
> O conector de dados de VM Qualys no Azure Sentinel está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O conector de Qualys Vulnerability Management (VM) permite que você conecte facilmente todos os seus logs de solução de segurança de [VM Qualys](https://www.qualys.com/apps/vulnerability-management/) com o Azure sentinela, para exibir painéis, criar alertas personalizados e melhorar a investigação. A integração entre a VM Qualys e o Azure Sentinel usa Azure Functions para efetuar pull de dados de log usando a API REST.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="configure-and-connect-qualys-vm"></a>Configurar e conectar a VM Qualys

Azure Functions pode integrar e efetuar pull de eventos e logs diretamente da VM Qualys e encaminhá-los para o Azure Sentinel.

1. No portal do Azure Sentinel, clique em **conectores de dados** e selecione conector de **Gerenciamento de vulnerabilidade Qualys** .

1. Selecione a **página abrir conector**.

1. Siga as instruções na página de **Gerenciamento de vulnerabilidades do Qualys** .

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados são exibidos em Log Analytics na tabela **QualysHostDetection_CL** .

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar a VM Qualys ao Azure Sentinel usando os aplicativos de funções do Azure. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
