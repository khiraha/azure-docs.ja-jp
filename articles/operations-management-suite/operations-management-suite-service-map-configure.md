---
title: "Operations Management Suite (OMS) のサービス マップの構成 | Microsoft Docs"
description: "サービス マップは、Windows および Linux システムのアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップする Operations Management Suite (OMS) のソリューションです。  この記事では、サービス マップを環境に展開して、さまざまなシナリオで使用する場合の詳細について説明します。"
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: d3d66b45-9874-4aad-9c00-124734944b2e
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/18/2016
ms.author: daseidma;bwren;dairwin
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: e0c74ef9fa2fa2e0b69a3730d7af30969d60e3f8
ms.lasthandoff: 04/20/2017


---
# <a name="configuring-service-map-solution-in-operations-management-suite-oms"></a>Operations Management Suite (OMS) のサービス マップ ソリューションの構成
サービス マップは、Windows および Linux システムのアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップします。 これを使用すると、サーバーを重要なサービスを提供する相互接続されたシステムとして表示することができます。  サービス マップは、TCP 接続アーキテクチャ全体のサーバー、プロセス、ポート間の接続を表示します。エージェントのインストール以外の構成は必要ありません。

この記事では、サービス マップの構成とエージェントのオンボードの詳細について説明します。  サービス マップの使用方法については、「[Operations Management Suite (OMS) のサービス マップ ソリューションの使用](operations-management-suite-service-map.md)」をご覧ください。

## <a name="dependency-agent-downloads"></a>Dependency Agent のダウンロード
| ファイル | OS | バージョン | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.0.5 | 73B3F6A2A76A08D58F72A550947FF839B588591C48E6EDDD6DDF73AA3FD82B43 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.0.5 | A1BAD0B36EBF79F2B69113A07FCF48C68D90BD169C722689F9C83C69FC032371 |


## <a name="connected-sources"></a>接続先ソース
サービス マップは、Microsoft Dependency Agent からデータを取得します。  Dependency Agent は、OMS への接続に関して OMS エージェントに依存しています。  つまり、サーバーには OMS エージェントを先にインストールして構成する必要があり、Dependency Agent はその操作の後でインストールできます。  次の表は、サービス マップソリューションの接続先としてサポートされているソースとその説明です。

| 接続先ソース | サポートの有無 | 説明 |
|:--|:--|:--|
| Windows エージェント | はい | サービス マップは、Windows エージェント コンピューターからのデータを分析して収集します。  <br><br>[OMS エージェント](../log-analytics/log-analytics-windows-agents.md)に加えて、Windows エージェントには Microsoft Dependency Agent が必要です。  サポートされる OS バージョンの一覧については、「[サポートされているオペレーティング システム](#supported-operating-systems)」をご覧ください。 |
| Linux エージェント | はい | サービス マップは、Linux エージェント コンピューターからのデータを分析して収集します。  <br><br>[OMS エージェント](../log-analytics/log-analytics-linux-agents.md)に加えて、Linux エージェントには Microsoft Dependency Agent が必要です。  サポートされる OS バージョンの一覧については、「[サポートされているオペレーティング システム](#supported-operating-systems)」をご覧ください。 |
| SCOM 管理グループ | はい | サービス マップは、接続された [System Center Operations Manager (SCOM) 管理グループ](../log-analytics/log-analytics-om-agents.md)内の Windows エージェントと Linux エージェントからのデータを分析して収集します。 <br><br>SCOM エージェント コンピューターを OMS に直接接続する必要はありません。 データは管理グループから OMS リポジトリに直接送信されます。|
| Azure ストレージ アカウント | なし | サービス マップはエージェント コンピューターからデータを収集するため、Azure Storage から収集するデータはありません。 |

サービス マップは 64 ビット プラットフォームのみをサポートします。

Windows では、SCOM と OMS の両方で Microsoft Monitoring Agent (MMA) が使用され、監視データを収集して送信します。  (このエージェントは、コンテキストに応じて、SCOM エージェント、OMS エージェント、MMA、または直接エージェントと呼ばれます)。SCOM と OMS では、すぐに使用できるさまざまなバージョンの MMA を提供していますが、これらのバージョンは SCOM、OMS、または両方にレポートすることができます。  Linux では、OMS Agent for Linux が監視データを収集して OMS に送信します。  サービス マップは、OMS ダイレクト エージェントがインストールされているサーバーまたは SCOM 管理グループ経由で OMS にアタッチされているサーバーで使用できます。  このドキュメントでは、すべてのエージェント (Linux または Windows で SCOM 管理グループに接続しているか、OMS に直接接続しているかに関係なく) を「OMS エージェント」と呼びます。ただし、エージェントの特定のデプロイ名がコンテキストで必要な場合を除きます。

サービス マップ エージェントがデータを送信することはないため、ファイアウォールやポートを変更する必要はありません。  サービス マップのデータは、常に OMS エージェントによって、直接または OMS ゲートウェイ経由で OMS に送信されます。

![サービス マップエージェント](media/oms-service-map/agents.png)

OMS に接続している管理グループの SCOM ユーザーの場合。

- SCOM エージェントがインターネット経由で OMS にアクセスできる場合は、追加の構成は必要ありません。  
- SCOM エージェントがインターネット経由で OMS にアクセスできない場合は、SCOM で動作するように OMS ゲートウェイを構成する必要があります。
  
OMS ダイレクト エージェントを使用している場合は、OMS エージェント自体を OMS または OMS ゲートウェイに接続するように構成する必要があります。  OMS ゲートウェイは [https://www.microsoft.com/download/details.aspx?id=52666](https://www.microsoft.com/download/details.aspx?id=52666) からダウンロードできます。


### <a name="avoiding-duplicate-data"></a>重複データの回避

SCOM ユーザーの場合、SCOM エージェントを OMS と直接通信するように構成しないでください。データが 2 回報告される可能性があります。  この結果、サービス マップではコンピューター一覧にコンピューターが 2 回表示されます。

OMS の構成は、次の場所のいずれか一方で行ってください。

- 管理対象コンピューターの SCOM コンソールの Operations Management Suite パネル
- MMA プロパティの Azure Operational Insights 構成

*同じ*ワークスペースで両方の構成を使用すると、データの重複が発生します。 *別の*ワークスペースで両方の構成を使用すると、構成の競合 (一方はサービス マップソリューションが有効、他方は無効) が発生し、データがサービス マップに完全に流れなくなる可能性があります。  

コンピューター自体が SCOM コンソールの OMS 構成で指定されていない場合でも、「Windows Server インスタンス グループ」などのインスタンス グループがアクティブになっていれば、SCOM 経由で OMS 構成を受信できる場合があります。



## <a name="management-packs"></a>管理パック
サービス マップが OMS ワークスペースでアクティブになると、300 KB の管理パックがそのワークスペース内のすべての Microsoft Monitoring Agents に送信されます。  SCOM エージェントを[接続された管理グループ](../log-analytics/log-analytics-om-agents.md)で使用している場合は、サービス マップ管理パックが SCOM からデプロイされます。  エージェントが直接接続されている場合は、OMS によって管理パックが配布されます。

この管理パックの名前は、Microsoft.IntelligencePacks.ApplicationDependencyMonitor* で、  *%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\* に書き込まれます。  管理パックで使用されるデータ ソースは、*%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll* です。



## <a name="configuration"></a>構成
Windows コンピューターと Linux コンピューターにエージェントをインストールした上で OMS に接続する場合は、Dependency Agent インストーラーをサービス マップソリューションからダウンロードし、root または Admin として各管理対象サーバーにインストールする必要があります。  サービス マップ エージェントが OMS にレポートするサーバーにインストールされると、サービス マップの依存関係マップが 10 分以内に表示されます。


### <a name="migrating-from-bluestripe-factfinder"></a>BlueStripe FactFinder からの移行
サービス マップは、BlueStripe テクノロジを段階的に OMS に移行します。 FactFinder は、既存の顧客に対してまだサポートされていますが、個別に購入できなくなります。  この Dependency Agent のプレビュー バージョンのみ、OMS と通信できます。  現在 FactFinder ユーザーである場合は、FactFinder の管理対象外のサービス マップのテスト サーバー セットを指定してください。 

### <a name="download-the-dependency-agent"></a>Dependency Agent のダウンロード
コンピューターと OMS 間の接続を提供する Microsoft Management Agent (MMA) と OMS Linux Agent に加えて、サービス マップによって分析されるすべてのコンピューターに Dependency Agent をインストールする必要があります。  Linux では、Dependency Agent の前に OMS Agent for Linux をインストールする必要があります。 

![[サービス マップ] タイル](media/oms-service-map/additional_configuration.png)

Dependency Agent をダウンロードするには、**[サービス マップ]** タイルの **[ソリューションの構成]** をクリックして、**[Dependency Agent]** ブレードを開きます。  Dependency Agent ブレードには、Windows と Linux エージェントのリンクがあります。 エージェントを異なるシステムにインストールする方法の詳細については、次のセクションをご覧ください。

### <a name="install-the-dependency-agent"></a>Dependency Agent のインストール

#### <a name="microsoft-windows"></a>Microsoft Windows
エージェントをインストールまたはアンインストールするには、管理者特権が必要です。

Dependency Agent は、InstallDependencyAgent-Windows.exe によって Windows コンピューターにインストールされます。 オプションを指定せずにこの実行可能ファイルを実行すると、ウィザードが起動します。指示に従って対話形式でインストールを実行できます。  

各 Windows コンピューターに Dependency Agent をインストールするには、次の手順を使用します。

1.    OMS エージェントが Connect コンピューターで手順に従って OMS に直接インストールされていることを確認します。
2.    Windows エージェントをダウンロードし、次のコマンドを使用して実行します。 <br>*InstallDependencyAgent-Windows.exe*
3.    ウィザードに従ってエージェントをインストールします。
4.    Dependency Agent が起動しない場合は、詳細なエラー情報のログを確認します。 Windows エージェントのログ ディレクトリは、*C:\Program Files\Microsoft Dependency Agent\logs* にあります。 

Dependency Agent for Windows は、管理者によってコントロール パネルからアンインストールできます。


#### <a name="linux"></a>Linux
エージェントをインストールまたは構成するには、ルート アクセスが必要です。

Dependency Agent は、InstallDependencyAgent-Linux64.bin (自己解凍バイナリ ファイルを含むシェル スクリプト) によって Linux コンピューターにインストールされます。 sh を使用してファイルを実行するか、実行アクセス許可をファイルに追加します。
 
各 Linux コンピューターに Dependency Agent をインストールするには、次の手順を使用します。

1.    OMS エージェントが [Linux コンピューターからのデータを分析して収集する手順に従ってインストールされていることを確認します。OMS エージェントは、Linux Dependency Agent の前にインストールされている必要があります](https://technet.microsoft.com/library/mt622052.aspx)。
2.    次のコマンドを使用して、Linux Dependency Agent をルートとしてインストールします。<br>*sh InstallDependencyAgent-Linux64.bin*。
3.    Dependency Agent が起動しない場合は、詳細なエラー情報のログを確認します。 Linux エージェントのログ ディレクトリは、*/var/opt/microsoft/dependency-agent/log* にあります。

### <a name="uninstalling-the-dependency-agent-on-linux"></a>Linux の Dependency Agent をアンインストールする
Linux から Dependency Agent を完全にアンインストールするには、エージェント自体と、自動的にエージェントにインストールされたコネクタを削除する必要があります。  次の 1 つのコマンドで、両方ともアンインストールできます。

    rpm -e dependency-agent dependency-agent-connector


### <a name="installing-from-a-command-line"></a>コマンド ラインからインストールする
前のセクションでは、規定のオプションを使用して Dependency Monitor Agent をインストールする方法について説明しました。  次のセクションでは、カスタム オプションを使用してコマンド ラインからエージェントをインストールする方法について説明します。

#### <a name="windows"></a>Windows
次の表のオプションを使用して、コマンドラインからインストールします。 インストール フラグの一覧を表示するには、 次のように /? フラグを使ってインストーラーを実行します。

    InstallDependencyAgent-Windows.exe /?

| フラグ | 説明 |
|:--|:--|
| /S | ユーザー プロンプトを表示せずにサイレント インストールを実行します。 |

Windows Dependency Agent のファイルは、既定で *C:\Program Files\Microsoft Dependency Agent* に配置されます。


#### <a name="linux"></a>Linux
次の表のオプションを使用してインストールします。 インストール フラグの一覧を表示するには、次のように -help フラグを使ってインストール プログラムを実行します。

    InstallDependencyAgent-Linux64.bin -help

| フラグ | Description |
|:--|:--|
| -s | ユーザー プロンプトを表示せずにサイレント インストールを実行します。 |
| --check | アクセス許可とオペレーティング システムを確認しますが、エージェントはインストールされません。 |

Dependency Agent のファイルは、次のディレクトリに保存されます。

| ファイル | 場所 |
|:--|:--|
| コア ファイル | /opt/microsoft/dependency-agent |
| ログ ファイル | /var/opt/microsoft/dependency-agent/log |
| 構成ファイル | /etc/opt/microsoft/dependency-agent/config |
| サービス実行可能ファイル | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| バイナリ ストレージ ファイル | /var/opt/microsoft/dependency-agent/storage |



## <a name="troubleshooting"></a>トラブルシューティング
このセクションでは、サービス マップのインストール時または実行時に問題が生じた場合に、起動し実行する方法を説明します。  この方法でも問題が解決しない場合は、Microsoft サポートにお問い合わせください。

### <a name="dependency-agent-installation-issues"></a>Dependency Agent のインストールに関する問題
#### <a name="installer-asks-for-a-reboot"></a>インストーラーが再起動を要求する
*通常*、Dependency Agent がインストールまたはアンインストール時に再起動を要求することはありません。  しかし、まれに、インストールを続行するために Windows Server が再起動を要求することがあります。  これは、依存関係 (通常は Microsoft VC++ 再頒布可能ファイル) のあるファイルがロックされているため、再起動が要求される場合に生じます。

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--codenumber"></a>メッセージ「Dependency Agent をインストールできません: Visual Studio ランタイム ライブラリが (code = [code_number]) をインストールできませんでした。」

Microsoft Dependency Agent は、Microsoft Visual Studio ランタイム ライブラリ上にビルドされます。 ライブラリをインストールする際に問題が発生しました。 ランタイム ライブラリのインストーラーは、%LOCALAPPDATA%\temp フォルダー内にログを作成します。 このログ ファイルは作成される際に、dd_vcredist_arch_yyyymmddhhmmss.log というファイル名になり、arch のところが「x86」または「amd64」に、yyyymmddhhmmss のところが日付と時刻 (24 時間形式) になります。 このログで、インストールをブロックしている問題の詳細を確認できます。

最初に[最新のランタイム ライブラリ](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)をインストールしておくことをお勧めします。

コード番号と推奨される解決策を次に示します。

| コード | 説明 | 解決策 |
|:--|:--|:--|
| 0x17 | ライブラリのインストーラーは、まだインストールされていない Windows Update を要求しています。 | 最新のライブラリ インストーラー ログを確認してください (上記参照)。<br><br>「Windows8.1-KB2999226-x64.msu」の次に「エラー 0x80240017: MSU パッケージを実行できませんでした。」というメッセージが続いている場合は、KB2999226 のインストールに必要な前提条件がインストールされていません。  https://support.microsoft.com/kb/2999226 の前提条件のセクションにある手順に従ってください。  必要な前提条件をインストールするためには、Windows Update の実行と複数回の再起動が必要になることがあります。<br><br>Microsoft Dependency Agent インストーラーをもう一度実行します。 |

### <a name="post-installation-issues"></a>インストール後の問題
#### <a name="server-doesnt-show-in-service-map"></a>サーバーがサービス マップに表示されない
Dependency Agent のインストールに成功しても、サービス マップ ソリューションにはサーバーが表示されません。
1. Dependency Agent は正しくインストールされているのでしょうか?   これについては、サービスがインストールされているか、実行中かを確認することで検証できます。<br><br>
**Windows**: 「Microsoft Dependency Agent」という名前のサービスを探してください。<br>
**Linux**: 「microsoft-dependency-agent」という名前の実行中のプロセスを探してください。

2. 現在ご利用されているのは、[無料の価格レベルの OMS または Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers) ですか?   無料のプランでは、一意のサービス マップ サーバーを最大 5 台まで使用できます。  以前使用していた 5 台がデータを送信していない場合でも、6 台目以降のサーバーはサービス マップに表示されません。

3. サーバーはログとパフォーマンス データを OMS に送信していますか?   ログ検索に移動し、お使いのコンピューターに対して次のクエリを実行します。 

        * Computer="<your computer name here>" | measure count() by Type
        
結果としてさまざまなイベントを取得しましたか?   そのデータは最近のものですか?   そうであれば、OMS エージェントは正しく動作しており、OMS サービスと通信しています。 そうでなければ、お使いのサーバーの OMS エージェントを確認してください: [Windows 用 OMS エージェントのトラブルシューティング](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues)。  [Linux 用 OMS エージェントのトラブルシューティング](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md)。

#### <a name="server-shows-in-service-map-but-has-no-processes"></a>サービス マップにサーバーは表示されるがプロセスが表示されない
サービス マップにサーバーは表示されるがプロセスまたは接続データは表示されないという場合は、Dependency Agent がインストールされて実行されているがカーネル ドライバーがロードされなかったということを意味しています。  ドライバーがロードされなかった原因を探るために、wrapper.log file (Windows の場合) または service.log file (Linux の場合) を確認します。  ファイルの最後の行に、カーネルがロードされなかった原因が記載されています (例: カーネルがサポートされていないなど。Linux でカーネルを更新した場合に発生することがあります)。

Windows: C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log

Linux: /var/opt/microsoft/dependency-agent/log/service.log

## <a name="data-collection"></a>データ収集
システムの依存関係の複雑さに応じて、各エージェントは 1 日あたり約 25 MB を送信すると予想されます。  サービス マップの依存関係のデータは、15 秒ごとに各エージェントによって送信されます。  

Dependency Agent は、通常、システム メモリの 0.1% とシステム CPU の 0.1% を消費します。


## <a name="supported-operating-systems"></a>サポートされているオペレーティング システム
次のセクションでは、Dependency Agent でサポートされているオペレーティング システムを一覧します。   32 ビット アーキテクチャは、どのオペレーティング システムでもサポートされていません。

### <a name="windows-server"></a>Windows Server
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Windows デスクトップ
- WIndows 10
- Windows 8.1
- Windows 8
- Windows 7

### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux、CentOS Linux 、および Oracle Linux (RHEL カーネル搭載)
- 既定と SMP Linux のカーネル リリースのみがサポートされています。
- PAE、Xen などの非標準のカーネル リリースは、どの Linux ディストリビューションでもサポートされていません。 たとえば、リリースの文字列が「2.6.16.21-0.8-xen」であるシステムはサポートされていません。
- カスタム カーネル (標準カーネルの再コンパイルを含む) は、サポートされていません。
- Centos Plus カーネルは、サポートされていません。
- Oracle Unbreakable Kernel (UEK) については、以下の別のセクションで説明します。


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7
| OS バージョン | カーネル バージョン |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6
| OS バージョン | カーネル バージョン |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4. | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5
| OS バージョン | カーネル バージョン |
|:--|:--|
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398<br>2.6.18-400<br>2.6.18-402<br>2.6.18-404<br>2.6.18-406<br>2.6.18-407<br>2.6.18-408<br>2.6.18-409<br>2.6.18-410<br>2.6.18-411<br>2.6.18-412<br>2.6.18-416<br>2.6.18-417<br>2.6.18-419 |

#### <a name="oracle-enterprise-linux-w-unbreakable-kernel-uek"></a>Unbreakable Kernel (UEK) を搭載した Oracle Enterprise Linux

#### <a name="oracle-linux-6"></a>Oracle Linux 6
| OS バージョン | カーネル バージョン
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4. | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| OS バージョン | カーネル バージョン
|:--|:--|
| 5.8 | Oracle 2.6.32-300 (UEK R1) |
| 5.9 | Oracle 2.6.39-300 (UEK R2) |
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11
| OS バージョン | カーネル バージョン
|:--|:--|
| 11 | 2.6.27 |
| 11 SP1 | 2.6.32 |
| 11 SP2 | 3.0.13 |
| 11 SP3 | 3.0.76 |
| 11 SP4 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10
| OS バージョン | カーネル バージョン
|:--|:--|
| 10 SP4 | 2.6.16.60 |

## <a name="diagnostic-and-usage-data"></a>診断と使用状況データ
マイクロソフトは、お客様によるサービス マップ サービスの使用を通して、使用状況とパフォーマンス データを自動的に収集します。 Microsoft はこのデータを使用して、提供するサービス マップサービスの品質、セキュリティ、および整合性の向上に努めています。 データには、オペレーティング システムとバージョンのような、ソフトウェアの構成に関する情報が含まれています。また、正確で効果的なトラブルシューティングの機能を提供するために、IP アドレス、DNS 名、およびワークステーション名も含まれています。 名前や住所などの連絡先情報は収集されません。

データの収集と使用状況の詳細については、[Microsoft オンライン サービスのプライバシーに関する声明](https://go.microsoft.com/fwlink/?LinkId=512132)を参照してください。



## <a name="next-steps"></a>次のステップ
- サービス マップをデプロイして構成したら、[サービス マップを使用する](operations-management-suite-service-map.md)方法を確認します。

