# app-certificates

tcdhry が配布する Windows アプリ（MSIX）の署名に使っている、自己署名の証明書（公開鍵）です。

この証明書で署名されたアプリは、どれも同じ証明書を使っています。登録は PC ごとに1回だけで、一度登録すれば、どのアプリもインストールできます。

## 証明書

| 項目 | 値 |
| --- | --- |
| ファイル | [tcdhry-localhost.cer](https://github.com/tcdhry-public/app-certificates/releases/latest/download/tcdhry-localhost.cer)（[Releases](https://github.com/tcdhry-public/app-certificates/releases) で配布） |
| サブジェクト | `CN=tcdhry-localhost` |
| 拇印 | `D6D98D9996A9FD5E8584A99547FADAB95E296A11` |
| 有効期間 | 2026-10-11 〜 2031-10-11 |

この証明書を使っているアプリ:

- [Notify Terminal](https://github.com/tcdhry-public/notify-terminal)

## 登録

アプリは自己署名の証明書で署名されているため、インストールの前に、証明書を信頼済みとして登録する必要があります。登録しないと、インストーラーに「発行元の証明書を確認できませんでした」と表示され、インストールできません。

[tcdhry-localhost.cer](https://github.com/tcdhry-public/app-certificates/releases/latest/download/tcdhry-localhost.cer) をダウンロードし、次のどちらかの方法で登録します（管理者の権限が必要です）。

- PowerShell で登録する: 管理者として PowerShell を開き、次を実行します。

  ```powershell
  Import-Certificate -FilePath "<ダウンロードした場所>\tcdhry-localhost.cer" -CertStoreLocation "Cert:\LocalMachine\TrustedPeople"
  ```

- 画面から登録する
  1. `tcdhry-localhost.cer` をダブルクリックし、「証明書のインストール」を押します。
  2. 保存場所は「ローカル コンピューター」を選びます（「現在のユーザー」ではインストールできません）。
  3. 「証明書をすべて次のストアに配置する」を選び、「参照」から「信頼されたユーザー」を選んで、完了まで進めます。

  「証明書の種類に基づいて、自動的に証明書ストアを選択する」を選ぶと、別のストア（「中間証明機関」など）に入ってしまい、インストールできません。必ず「信頼されたユーザー」を手動で選んでください。

登録されているかどうかは、PowerShell で次を実行して確かめられます（ `True` なら登録済み）。

```powershell
Test-Path "Cert:\LocalMachine\TrustedPeople\D6D98D9996A9FD5E8584A99547FADAB95E296A11"
```

## 有効期限について

- 証明書は、アプリのインストールと更新のときに確かめられます。期限が切れても、インストール済みのアプリはそのまま動きます。
- 期限が切れる前に新しい証明書に切り替え、新しいリリースとして公開します。上のリンクは、常に最新のリリースの証明書を指します。切り替えた後は、新しい証明書を登録し直してください。

## 削除

この証明書を使っているアプリをすべてアンインストールした後、登録した証明書を削除する場合は、管理者の PowerShell で次を実行します。

```powershell
Get-ChildItem Cert:\LocalMachine\TrustedPeople | Where-Object Subject -eq "CN=tcdhry-localhost" | Remove-Item
```

削除すると、この証明書で署名されたアプリは、どれも新しくインストールしたり更新したりできなくなります。
