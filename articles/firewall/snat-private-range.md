---
title: Intervalos de endereços IP privados de SNAT do firewall do Azure
description: Você pode configurar intervalos de endereços IP para SNAT.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: be2bf0f9590a23f9def44a1800338c80f69a782c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610516"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Intervalos de endereços IP privados de SNAT do firewall do Azure

O Firewall do Azure fornece SNAT automático para todo o tráfego de saída para endereços IP públicos. Por padrão, o Firewall do Azure não SNAT com regras de rede quando o endereço IP de destino está em um intervalo de endereços IP privado por [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). As regras de aplicativo sempre são aplicadas usando um [proxy transparente](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) , independentemente do endereço IP de destino.

Essa lógica funciona bem quando você roteia o tráfego diretamente para a Internet. No entanto, se você tiver habilitado o [túnel forçado](forced-tunneling.md), o tráfego de entrada na Internet será no modo SNAT a um dos endereços IP privados do firewall no AzureFirewallSubnet, ocultando a origem do seu firewall local.

Se a sua organização usa um intervalo de endereços IP públicos para redes privadas, o Firewall do Azure realiza SNAT do tráfego para um dos endereços IP privados do firewall no AzureFirewallSubnet. No entanto, você pode configurar o Firewall do Azure para **não** SNAT de seu intervalo de endereços IP públicos.

Para configurar o Firewall do Azure para nunca SNAT, independentemente do endereço IP de destino, use **0.0.0.0/0** como seu intervalo de endereços IP privados. Com essa configuração, o Firewall do Azure nunca pode rotear o tráfego diretamente para a Internet. Para configurar o firewall para sempre SNAT, independentemente do endereço de destino, use **255.255.255.255/32** como seu intervalo de endereços IP privado.

## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>Configurar intervalos de endereços IP privados SNAT-Azure PowerShell

Você pode usar Azure PowerShell para especificar intervalos de endereços IP privados para o firewall.

### <a name="new-firewall"></a>Novo firewall

Para um novo firewall, o comando Azure PowerShell é:

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> O IANAPrivateRanges é expandido para os padrões atuais no firewall do Azure, enquanto os outros intervalos são adicionados a ele. Para manter o padrão IANAPrivateRanges em sua especificação de intervalo particular, ele deve permanecer em sua `PrivateRange` especificação, conforme mostrado nos exemplos a seguir.

Para obter mais informações, consulte [New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0).

### <a name="existing-firewall"></a>Firewall existente

Para configurar um firewall existente, use os seguintes comandos de Azure PowerShell:

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Modelos

Você pode adicionar o seguinte à `additionalProperties` seção:

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>Configurar intervalos de endereços IP privados SNAT-portal do Azure

Você pode usar o portal do Azure para especificar intervalos de endereços IP privados para o firewall.

1. Selecione seu grupo de recursos e, em seguida, selecione o firewall.
2. Na página **visão geral** , **intervalos de IP privado**, selecione o valor padrão **IANA RFC 1918**.

   A página **Editar prefixos de IP privado** é aberta:

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="Editar prefixos de IP privado":::

1. Por padrão, o **IANAPrivateRanges** está configurado.
2. Edite os intervalos de endereços IP privados para seu ambiente e, em seguida, selecione **salvar**.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [túnel forçado do firewall do Azure](forced-tunneling.md).