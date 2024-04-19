# DefenderForServersMappingToMDETag
> 概要

このレポジトリ ``DefenderForServersMappingToMDETag`` では Microsoft Defender for Servers によってデプロイされた MDE (Microsoft Defender for Endpoint) に対して、Defender XDR 側で登録されるデバイスに Azure サブスクリプション名を自動付与するサンプルを提供しています。<p>

このテンプレートでは、定期的に MDE タグをロジックアプリを用いて付与させています

 - タグ名 ``DefenderForServers``
 - タグ名 ``<Your Azure Subscription Name>`` <-- RESTAPI を用いて、Azure Resource Graph クエリーから取得します

![image](https://github.com/hisashin0728/DefenderForServersMappingToMDETag/assets/55295601/788710e3-d3b3-45f7-b98a-118a9359204f)

# Deploy To Azure
> デプロイはこちらから

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhisashin0728%2FDefenderForServersMappingToMDETag%2Fmain%2FDefenderForServersMappingToMDETag.json)

# ロジックアプリの設定について
> Deploy to Azure 後、以下の設定が必要です

- **Reccurence** (再帰) 設定について、ユーザー側で任意の値に修正して下さい
  - 初期値では ``1`` ヵ月の再帰設定となっています。Defender for Servers のデプロイ状況を検討の上、任意の値に修正して下さい
- ロジックアプリのマネージド ID に対して、全てのサブスクリプションの ``Reader`` ロールを付与して下さい
  - このロジックアプリはサブスクリプション名を抽出するため、RESTAPI を用いてマネージド ID 権限を用いて Azure Resource Graph にクエリーを実行します
  - もし、複数のサブスクリプションを有しており、Defender for Servers の展開が複数のサブスクリプションに渡る場合は、個々のサブスクリプション ``reader`` ロールをマネージド ID に付与して下さい

![image](https://github.com/hisashin0728/DefenderForServersMappingToMDETag/assets/55295601/cf976a86-33be-45cf-b647-62b05d907ccb)
![image](https://github.com/hisashin0728/DefenderForServersMappingToMDETag/assets/55295601/aa29bd6a-2683-4217-8786-6c694c15de7e)

# 動作確認について
> 手動でロジックアプリを起動してチェックすることができます。成功すると、個々のデバイスに 2 つのタグが付与されます。

設定が無事完了しましたら、ロジックアプリを手動で実行して下さい。設定が正しければ、2つのタグ ``DefenderForServers`` と ``<Azure Subscription Name>`` が個々のデバイスに付与されます

<img width="800" alt="image" src="https://github.com/hisashin0728/DefenderForServersMappingToMDETag/assets/55295601/82c0f813-e5e6-4112-9308-a6968958c527">

# 参考
> 現在分かっていること

- ロジックアプリは初回時に全てのDefender XDR デバイスに対してクエリーを行います。条件は ODATP フィルターを用いて行っています
  - onboardingStatus is ``Onboarded``
  - healthStatus is ``Active``
  - **NOT** machineTags equal ``DefenderForServers`` <-- Initially, Tag will be embedded, but secondary process ignored 
  - **NOT** osPlatform eq ``Windows10`` or osPlatform eq ``Windows11`` 

<img alt="hogehoge" src="https://github.com/hisashin0728/DefenderForServersMappingToMDETag/assets/55295601/3433ea73-29a5-4f0c-bcb9-b7546d393c70" width="800"><BR>

- 本ロジックアプリはリソース ID 情報からサブスクリプション ID を抽出する設計になっています
<img width="800" alt="image" src="https://github.com/hisashin0728/DefenderForServersMappingToMDETag/assets/55295601/b84f570a-f0cb-405b-86db-3f9210b03ef3">

- 幾つかのデバイスにて、リソース ID が付与されていても サブスクリプション ID が付与されない事象を確認しています。
    - このため、リソース ID 情報から関数を用いて split を行い、サブスクリプション情報を抽出しています
<img width="800" alt="image" src="https://github.com/hisashin0728/DefenderForServersMappingToMDETag/assets/55295601/5ed72e1b-7268-46d5-9bec-0b30ad98283d">
