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

`preg_replace()`関数があんまりよくないみたい。  
調べてみたら、PHP7から廃止になったらしいですね。  
Xserverのメンテナンス情報に、PHPアップデートが完了したと記載が。  
安全性のために、自動でマイナーアップデートがされていたらしい。

Xserverは良かれと思ってやってくれたことですから、こちらが合わせるしかないですね。

## 結論
`zeromail.php`の一番下のこの記述を見つけてください。

### 修正前
```
/*-----------------------------------------------------
  ver4用デコード
------------------------------------------------------*/
function unhtmlentities($string)
{
    // 数値エンティティの置換
    $string = preg_replace('~&#x([0-9a-f]+);~ei', 'chr(hexdec("\\1"))', $string);
    $string = preg_replace('~&#([0-9]+);~e', 'chr("\\1")', $string);

    // 文字エンティティの置換
    $trans_tbl = get_html_translation_table(HTML_ENTITIES);
    $trans_tbl = array_flip($trans_tbl);
    return strtr($string, $trans_tbl);
}
```

### 修正後
```
/*-----------------------------------------------------
  ver4用デコード
------------------------------------------------------*/
function unhtmlentities($string)
{
    // 数値エンティティの置換
    $string = preg_replace_callback('~&#x([0-9a-f]+);~i', function ($m) {return chr(hexdec($m[1]));}, $string);
    $string = preg_replace_callback('~&#([0-9]+);~', function ($m) {return chr($m[1]);}, $string);

    // 文字エンティティの置換
    $trans_tbl = get_html_translation_table(HTML_ENTITIES);
    $trans_tbl = array_flip($trans_tbl);
    return strtr($string, $trans_tbl);
}
```

//数値エンティティの置換の下2行を書き換えればOKです。
