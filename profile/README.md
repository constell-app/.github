# Constell: Your second brain, auto-organized by AI

AIが知識の「点」を「線」で結び、あなただけの知識の星座を形作るAndroidアプリケーションです。

## 概要

Constellは、散らばった知識（Web記事、メモ、アイデアなど）をAIが分析し、意味的な関連性に基づいて自動的に繋ぎ合わせる「第2の脳」を目指すツールです。単なる情報の蓄積ではなく、知識同士がどのように結びついているかを視覚的に探索することで、新しい発見を促します。

## 主な機能

- **知識の星座（Constellation View）**: 保存した記事やメモが、関連性に基づいたノードとエッジとして星座空間に配置されます。
- **AIによる文脈解析**: 保存された情報の内容をAIが理解し、手動でタグ付けすることなく関連する知識を自動的にリンクします。
- **シームレスな探索**: ある知識から関連する別の知識へと、宇宙を旅するように直感的に遷移できます。
- **ウォークスルー体験**: 初めてのユーザーでも理解しやすいように、機能のコンセプトを伝えるアニメーション付きの導入画面を用意しています。
- **セキュアな認証**: Supabase Authを利用したメール/パスワード認証および各種ソーシャルログインに対応。

## アーキテクチャ

```plantuml
@startuml Architecture

!include  https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml

title Constell Architecture

Person(user, "ユーザー", "サービスを利用するユーザー")

System_Boundary(constell, "Constell") {
    Container(androidApp, "Androidアプリ", "Jetpack Compose", "ユーザーが利用するAndroidアプリ")

    Boundary(supabase, "Supabase") {
        Container(auth, "Auth", "Supabase", "認証・認可基盤")
        Container(edgeFunctions, "Edge Functions", "Deno / TypeScript", "記事内容の要約・ベクトル化を行い保存する関数")
        Container(postgrest, "PostgRESTサーバー", "PostgREST", "HTTPリクエストをSQLに変換し、DBとやりとりするサーバー")
        ContainerDb(db, "DB", "PostgreSQL, pgvector", "データの保存、RLS、記事同士の類似度を計算・保存するトリガー関数")
    }
}

System_Ext(geminiApi, "Gemini API", "記事の要約とベクトル化を行う外部API")

Rel(user, androidApp, "利用する")
Rel(androidApp, auth, "サインイン・サインアップ処理、セッション管理、パスワードリセット")
Rel(androidApp, postgrest, "1. 記事の登録", "HTTP, REST API")
Rel(postgrest, db, "2. 記事登録処理のSQLクエリ")
Rel(db, edgeFunctions, "3. 記事の登録をトリガーに処理開始", "Database Webhooks")
Rel(edgeFunctions, geminiApi, "4. 記事の要約とベクトル化", "HTTP, REST API")
Rel(edgeFunctions, postgrest, "5. 要約・ベクトル化した記事を登録", "HTTP, REST API")
Rel(postgrest, db, "6. 記事を登録するSQLクエリ")

@enduml
```

---

© 2026 Shohei Yamagiwa
