# ZeroMail　メールに本文が表示されない場合
ZeroMailはオープンソースのメールフォームスクリプトです。  
先日、ZeroMailからのメールに、本文が表示されなくなりました。  
その時の対処方法を忘備録として保存しておきます。

## 原因はPHPのバージョンが上がったこと
フォーム関連の作業はしていなかったので、サーバー（Xserver）のエラーログをみたところ、下記のような記述がありました。  
```
PHP message: PHP Warning:
 preg_replace(): The /e modifier is no longer supported,
 use preg_replace_callback instead in ****/zeromail.php on line 807
```
