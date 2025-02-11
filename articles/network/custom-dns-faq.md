---
title: カスタム DNS サーバー よくあるお問い合わせ - FAQ
date: 2021-05-21 11:00:00 
tags:
  - Network
  - DNS
---

こんにちは。Azure テクニカル サポート チームの石原です。

Azure では、仮想ネットワークや仮想マシンに関連付いているネットワーク インターフェイス毎に、名前解決時の接続先となる DNS サーバーを複数指定することができます。

仮想ネットワークやネットワーク インターフェースで設定可能なカスタム DNS サーバーの概要については、下記の弊社公開技術情報にも記載がございますので、ご参考になれば幸いです。

* [DNS サーバーの指定](https://docs.microsoft.com/ja-jp/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#specify-dns-servers)

このブログでは、仮想ネットワークやネットワーク インターフェースで設定可能なカスタム DNS サーバーに関して、度々お問合せ頂く内容をご紹介させて頂きます。

<!-- more -->

---

## Q & A

#### Q. 仮想マシンに関連付いているネットワーク インターフェースに設定している DNS サーバーを変更したところ、接続している仮想マシンが自動で再起動する旨のメッセージが表示されましたが、数分待っても再起動されることはありませんでした。手動で再起動する必要があるのでしょうか。

A. ネットワーク インターフェースの DNS サーバー設定を変更した後、仮想マシンの再起動を手動で実施いただくことで、お客様のタイミングで DNS サーバーの IP アドレスをご変更頂けます。

カスタム DNS サーバーの設定を変更すると、仮想マシンの OS に設定を反映させるために、Azure 基盤による任意のタイミングで仮想マシンを再起動することが想定されています。

但し、Azure 基盤による任意のタイミングで仮想マシンを再起動するため、直ぐに仮想マシンが再起動される可能性はゼロではございませんが、基本的には設定変更後暫く時間が経過してから仮想マシンが再起動されることになります。

そのため、意図しないタイミングで再起動が発生しないように、お客様のタイミングで Azure ポータルより手動で仮想マシンの再起動を実施頂くことをご検討いただければと存じます (OS の再起動では、カスタム DNS サーバーの設定は反映されません)。

なお、Azure 基盤側で再起動を実施するより前にお客様側で仮想マシンを再起動していただければ、Azure 基盤による仮想マシンの再起動は行われません。

#### Q. 仮想ネットワークの DNS サーバー設定を変更しましたが、仮想マシンは自動で再起動されるのでしょうか。
A. ネットワーク インターフェースとは異なり、Azure 基盤による任意のタイミングで仮想マシンが再起動されることはございません。

仮想マシンの OS 上において "ipconfig /renew" を実行して頂くことで、仮想マシンの再起動をせずに DNS サーバーの設定変更を反映させることが可能です。

#### Q. 可用性セット内の仮想マシンに関連付いているネットワーク インターフェースのカスタム DNS サーバーを変更したいのですが、可用性セットの仮想マシンは 1 台ずつ自動で再起動されるのでしょうか。

A. 仮想ネットワークもしくはネットワークインターフェイスの DNS サーバー設定を変更する場合、可用性セットであることを考慮して、可用性セットの仮想マシンを順番に再起動するとは限りません。

Azure 基盤による任意のタイミングで仮想マシンを再起動するため、直ぐに仮想マシンが再起動される可能性はゼロではございませんが、基本的には DNS サーバー設定変更後、暫く時間が経過してから仮想マシンが再起動されることになります。

そのため、可用性セットを構成されている仮想マシンであれば、DNS サーバー設定を変更した後、 Azure ポータルより手動で 1 台ずつ順番に再起動を実施いただくことで、お客様のタイミングで DNS サーバーの IP アドレスの変更が反映されますので、ご検討いただければと存じます。

なお、Azure 基盤側で再起動を実施するより前にお客様側で仮想マシンを再起動していただければ、Azure 基盤による仮想マシンの再起動は行われません。


#### Q. 仮想ネットワークやネットワーク インターフェースにカスタム DNS サーバーとして複数の IP アドレスを登録しているのですが、名前解決時は各 DNS サーバーへ順次接続するのか、それとも同時に接続するのでしょうか。

A. 複数のカスタム DNS サーバーの IP アドレスを指定された場合、どの DNS サーバーを参照するかは、仮想マシン上に構成された OS の動作に依存致します。

Azure の仮想ネットワークやネットワーク インターフェースで設定可能なカスタム DNS サーバーの動作として、設定されたカスタム DNS サーバーの設定値を仮想マシンの OS に構成情報として展開致しますが、Azure の設定値としてカスタム DNS サーバーの優先度を設定する機能は持ち合わせておりません。

上記の動作より、カスタム DNS サーバーとして指定される DNS サーバーはいずれにおいても、Azure 上の仮想マシンが意図せず通信できない事象などを回避するために、パブリック インターネットに名前解決ができる構成としていただくことを推奨しております。

お客様の通信要件において、参照する DNS サーバーに優先度をつけて名前解決する場合は、Azure の機能では設定できないため、OS 上の設定において構成頂ければと存じます。


#### Q. Azure PowerShell のコマンドを用いて、仮想マシン毎にカスタム DNS サーバーの IP アドレスの一覧を出力する方法はありますでしょうか。

A. 指定したリソース グループ内において、仮想マシンが関連付いている全てのネットワーク インターフェースの DNS 設定を出力するサンプル コマンドは下記のようになります。

■ サンプル コマンド

```
Get-AzNetworkInterface -ResourceGroupName 【リソース グループ名】 | ForEach-Object {
    if ($_.VirtualMachine -ne $null) {
        $myObject = [PSCustomObject]@{
            NIC = "NIC: " + $_.Name
            VM = " VM: " + $_.VirtualMachine.Id.Substring($_.VirtualMachine.Id.LastIndexOf("/") + 1)
            DNS = "DNS: " + $_.DnsSettings.DnsServers
        }
        $myObject.NIC
        $myObject.VM
        $myObject.DNS
        echo ""
    }
}
```

【リソース グループ名】は、検索対象のリソース グループの名称に置き換えて実行して下さい。

■ 例

```
Get-AzNetworkInterface -ResourceGroupName "rg-myresources" | ForEach-Object {
    if ($_.VirtualMachine -ne $null) {
        $myObject = [PSCustomObject]@{
            NIC = "NIC: " + $_.Name
            VM = " VM: " + $_.VirtualMachine.Id.Substring($_.VirtualMachine.Id.LastIndexOf("/") + 1)
            DNS = "DNS: " + $_.DnsSettings.DnsServers
        }
        $myObject.NIC
        $myObject.VM
        $myObject.DNS
        echo ""
    }
}
```

■　結果出力例

```
NIC: kaishiha930
 VM: kaishiha
DNS: 8.8.8.8 1.1.1.1 2.2.2.2 3.3.3.3 4.4.4.4 5.5.5.5

NIC: server0774
 VM: Server0
DNS: 168.63.129.16
```

上から順番に、ネットワーク インターフェースの名称、そのネットワーク インターフェースに関連付いている Azure VM の名称、そのネットワーク インターフェースの DNS 設定の結果になります。

なお、Get-AzNetworkInterface コマンドの詳細情報に関しては、下記の弊社公開技術情報のドキュメントにございます。ご参考になれば幸いです。

* [Get-AzNetworkInterface](https://docs.microsoft.com/en-us/powershell/module/az.network/get-aznetworkinterface?view=azps-4.3.0)

