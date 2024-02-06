---
title: 'Kubernetes Meetup Tokyo #24 Kubernetes上で動かすアプリケーション開発のデバッグとテスト 参加メモ'
tags:
  - kubernetes
  - CloudNative
  - ftrace
  - TelePresence
private: false
updated_at: '2019-11-07T21:54:21+09:00'
id: 3ecbb33dff393abfa7db
organization_url_name: qiitadon
slide: false
ignorePublish: false
---
## はじめに

[Kubernetes Meetup Tokyo #24](https://k8sjp.connpass.com/event/149872/)に参加してきました。
今回のテーマは「Kubernetes上で動かすアプリケーション開発のデバッグとテスト」です

## セッション

### 1. minne の開発環境の変革とこれから

登壇者：後藤 利博 ([@_shiro16](https://twitter.com/_shiro16)), GMOペパボ株式会社

- 開発環境の数が足りない問題をkuberntesによって解決した。
- Telepresenceを使って開発するサービスだけをDocker上で動かすことが出来るようになった。

### 2. Debug application inside Kubernetes using Linux Kernel tools

資料：https://speakerdeck.com/kentatada/debug-application-inside-kubernetes-using-linux-kernel-tools
登壇者：KENTA TADA ([@KentaTada](https://github.com/KentaTada)), ソニー株式会社

oci-ftrace-syscall-analyzerというftraceベースのシステムコールアナライザをkubernetes上で動かす話。

#### どうやってコンテナにアタッチするか

- prestart時にコンテナで動かす予定のPIDをftraceに設定する
- stdinでjson形式でコンテナの状態が書いてあり、そこにPIDが書いてある
- ただし、実際のexecまでのログまで残ってしまう
- コンテナ起動後にkubectl execで実行したプロセスのシステムコールは取れない

#### Kuberentesで使う方法
CRI-Oのprestart時にruncのprestartをフックする

#### eBPFベースのoci-seccomp-bpf-hookと比べると

- 特権が不要
- eBPFの設定がカーネルのバージョンに依存するので、その心配がない

### 3. Kubernetes 1.16 アルファ機能を先取り！エフェメラルコンテナ

資料：https://speakerdeck.com/superbrothers/kubernetes-1-dot-16-ephemeral-containers-alpha-bf6ff69c-410b-4f89-a7e9-e856db91fb7f
登壇者：Kazuki Suda ([@superbrothers](https://twitter.com/superbrothers)), ゼットラボ株式会社

#### 現状のkubectl execでの課題

- コンテナ内に存在するコマンドしか実行できない
- 軽量イメージにはツールが少ない
- 最低限のツールを含めたコンテナをサイドカーにする？
  - すべてのpodに入れるのはリソースの無駄

#### エフェメラルコンテナ

- 実行中のpodに一時的に追加出来るコンテナ
- トラブルシュートやデバッグ時に任意のツールを使うことができる
- 通常のpod操作と権限を分けることが出来る
- あくまでも一時的なコンテナなので機能が制限されている
- まだアルファで、本番で使用できるようになるにはまだまだ掛かりそう


